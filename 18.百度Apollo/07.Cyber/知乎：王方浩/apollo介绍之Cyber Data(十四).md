- [apollo介绍之Cyber Data(十四) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/117318368)

上一篇分析了Component模块的调用流程。为了弄清楚消息的调用过程，下面我们分析"DataDispatcher"和"DataVisitor"。

## DataVisitor和DataDispatcher

DataDispather（消息分发器）发布消息，DataDispather是一个单例，所有的数据分发都在数据分发器中进行，DataDispather会把数据放到对应的缓存中，然后Notify(通知)对应的协程（实际上这里调用的是DataVisitor中注册的Notify）去处理消息。

DataVisitor（消息访问器）是一个辅助的类，**一个数据处理过程对应一个DataVisitor，通过在DataVisitor中注册Notify（唤醒对应的协程，协程执行绑定的回调函数），并且注册对应的Buffer到DataDispather**，这样在DataDispather的时候会通知对应的DataVisitor去唤醒对应的协程。

也就是说DataDispather（消息分发器）发布对应的消息到DataVisitor，DataVisitor（消息访问器）唤醒对应的协程，协程中执行绑定的数据处理回调函数。

## DataVisitor数据访问器

DataVisitor继承至DataVisitorBase类，先看DataVisitorBase类的实现。

```cpp
class DataVisitorBase {
 public:
  // 1. 初始化的时候创建一个Notifier
  DataVisitorBase() : notifier_(new Notifier()) {}

  // 2. 设置注册回调
  void RegisterNotifyCallback(std::function<void()>&& callback) {
    notifier_->callback = callback;
  }

 protected:
  // 3. 下一次消息的下标
  uint64_t next_msg_index_ = 0;
  // 4. DataNotifier单例
  DataNotifier* data_notifier_ = DataNotifier::Instance();
  std::shared_ptr<Notifier> notifier_;
};
```

可以看到DataVisitorBase创建了一个"Notifier"类，并且提供注册回调的接口。同时还引用了"DataNotifier::Instance()"单例。



接下来看"DataVisitor"类的实现。

```cpp
template <typename M0, typename M1, typename M2>
class DataVisitor<M0, M1, M2, NullType> : public DataVisitorBase {
 public:
  explicit DataVisitor(const std::vector<VisitorConfig>& configs)
      : buffer_m0_(configs[0].channel_id,
                   new BufferType<M0>(configs[0].queue_size)),
        buffer_m1_(configs[1].channel_id,
                   new BufferType<M1>(configs[1].queue_size)),
        buffer_m2_(configs[2].channel_id,
                   new BufferType<M2>(configs[2].queue_size)) {
    // 1. 在DataDispatcher中增加ChannelBuffer
    DataDispatcher<M0>::Instance()->AddBuffer(buffer_m0_);
    DataDispatcher<M1>::Instance()->AddBuffer(buffer_m1_);
    DataDispatcher<M2>::Instance()->AddBuffer(buffer_m2_);
    // 2. 在DataNotifier::Instance()中增加创建好的Notifier
    data_notifier_->AddNotifier(buffer_m0_.channel_id(), notifier_);
    // 3. 对接收到的消息进行数据融合
    data_fusion_ =
        new fusion::AllLatest<M0, M1, M2>(buffer_m0_, buffer_m1_, buffer_m2_);
  }


  bool TryFetch(std::shared_ptr<M0>& m0, std::shared_ptr<M1>& m1,  // NOLINT
                std::shared_ptr<M2>& m2) {                         // NOLINT
    // 4. 获取融合数据
    if (data_fusion_->Fusion(&next_msg_index_, m0, m1, m2)) {
      next_msg_index_++;
      return true;
    }
    return false;
  }

 private:
  fusion::DataFusion<M0, M1, M2>* data_fusion_ = nullptr;
  ChannelBuffer<M0> buffer_m0_;
  ChannelBuffer<M1> buffer_m1_;
  ChannelBuffer<M2> buffer_m2_;
};
```

总结一下DataVisitor中实现的功能。

1. 在DataDispatcher中添加订阅的ChannelBuffer
2. 在DataNotifier中增加对应通道的Notifier
3. 通过DataVisitor获取数据并进行融合



**这里注意**

\1. 如果DataVisitor只访问一个消息，则不会对消息进行融合，如果DataVisitor访问2个以上的数据，那么需要进行融合，并且注册融合回调。之后CacheBuffer中会调用融合回调进行数据处理，而不会把数据放入CacheBuffer中。

```cpp
// 1. 只有一个消息的时候直接从Buffer中获取消息
  bool TryFetch(std::shared_ptr<M0>& m0) {  // NOLINT
    if (buffer_.Fetch(&next_msg_index_, m0)) {
      next_msg_index_++;
      return true;
    }
    return false;
  }

// 2. 当有2个消息的时候，从融合buffer中读取消息
  bool TryFetch(std::shared_ptr<M0>& m0, std::shared_ptr<M1>& m1) {  // NOLINT
    if (data_fusion_->Fusion(&next_msg_index_, m0, m1)) {
      next_msg_index_++;
      return true;
    }
    return false;
  }
```

\2. 实际上如果有多个消息的时候，会以第1个消息为基准，然后把其它消息的最新消息一起放入融合好的buffer_fusion_。

```cpp
  AllLatest(const ChannelBuffer<M0>& buffer_0,
            const ChannelBuffer<M1>& buffer_1)
      : buffer_m0_(buffer_0),
        buffer_m1_(buffer_1),
        buffer_fusion_(buffer_m0_.channel_id(),
                       new CacheBuffer<std::shared_ptr<FusionDataType>>(
                           buffer_0.Buffer()->Capacity() - uint64_t(1))) {
    // 1. 注意这里只注册了buffer_m0_的回调，其它的消息还是把消息放入CacheBuffer。  
    // 2. 而buffer_m0_直接调用回调函数，把消息放入融合的CacheBuffer。  
    buffer_m0_.Buffer()->SetFusionCallback(
        [this](const std::shared_ptr<M0>& m0) {
          std::shared_ptr<M1> m1;
          if (!buffer_m1_.Latest(m1)) {
            return;
          }

          auto data = std::make_shared<FusionDataType>(m0, m1);
          std::lock_guard<std::mutex> lg(buffer_fusion_.Buffer()->Mutex());
          buffer_fusion_.Buffer()->Fill(data);
        });
  }
```

\3. DataFusion类是一个虚类，定义了数据融合的接口"Fusion()"，Apollo里只提供了一种数据融合的方式，即以第一个消息的时间为基准，取其它最新的消息，当然也可以在这里实现其它的数据融合方式。



## DataDispatcher数据分发器

接下来我们看DataDispatcher的实现。

```cpp
template <typename T>
class DataDispatcher {
 public:
  using BufferVector =
      std::vector<std::weak_ptr<CacheBuffer<std::shared_ptr<T>>>>;
  ~DataDispatcher() {}
  // 1. 添加ChannelBuffer到buffers_map_
  void AddBuffer(const ChannelBuffer<T>& channel_buffer);

  // 2. 分发通道中的消息
  bool Dispatch(const uint64_t channel_id, const std::shared_ptr<T>& msg);

 private:
  // 3. DataNotifier单例
  DataNotifier* notifier_ = DataNotifier::Instance();
  std::mutex buffers_map_mutex_;
  // 4. 哈希表，key为通道id，value为订阅通道消息的CacheBuffer数组。
  AtomicHashMap<uint64_t, BufferVector> buffers_map_;
  // 5. 单例
  DECLARE_SINGLETON(DataDispatcher)
};
```

总结一下DataDispatcher的实现。

1. 添加ChannelBuffer到buffers_map_，key为通道id（topic），value为订阅通道消息的CacheBuffer数组。
2. 分发通道中的消息。根据通道id，把消息放入对应的CacheBuffer。然后通过DataNotifier::Instance()通知对应的通道。

**如果一个通道(topic)有3个CacheBuffer订阅，那么每次都会往这3个CacheBuffer中写入当前消息的指针。因为消息是共享的，消息访问的时候需要加锁。**



那么DataNotifier如何通知对应的Channel的呢？理解清楚了DataNotifier的数据结构，那么也就理解了DataNotifier的原理，DataNotifier保存了

```cpp
class DataNotifier {
 public:
  using NotifyVector = std::vector<std::shared_ptr<Notifier>>;
  ~DataNotifier() {}

  void AddNotifier(uint64_t channel_id,
                   const std::shared_ptr<Notifier>& notifier);

  bool Notify(const uint64_t channel_id);

 private:
  std::mutex notifies_map_mutex_;
  // 1. 哈希表，key为通道id，value为Notify数组
  AtomicHashMap<uint64_t, NotifyVector> notifies_map_;

  DECLARE_SINGLETON(DataNotifier)
};
```

DataNotifier中包含一个哈希表，表的key为通道id，表的值为Notify数组，每个DataVisitorBase在初始化的时候会创建一个Notify。



接着我们看下CacheBuffer的实现，CacheBuffer实际上实现了一个缓存队列，主要关注下Fill函数。

```cpp
  void Fill(const T& value) {
    if (fusion_callback_) {
      // 1. 融合回调
      fusion_callback_(value);
    } else {
      // 2. 如果Buffer满，实现循环队列
      if (Full()) {
        buffer_[GetIndex(head_)] = value;
        ++head_;
        ++tail_;
      } else {
        buffer_[GetIndex(tail_ + 1)] = value;
        ++tail_;
      }
    }
  }
```



ChannelBuffer是CacheBuffer的封装，主要看下获取值。

```cpp
template <typename T>
bool ChannelBuffer<T>::Fetch(uint64_t* index,
                             std::shared_ptr<T>& m) {  // NOLINT
  std::lock_guard<std::mutex> lock(buffer_->Mutex());
  if (buffer_->Empty()) {
    return false;
  }

  if (*index == 0) {
    *index = buffer_->Tail();
  // 1. 为什么是判断最新的加1，而不是大于？？？
  } else if (*index == buffer_->Tail() + 1) {
    return false;
  } else if (*index < buffer_->Head()) {
    auto interval = buffer_->Tail() - *index;
    AWARN << "channel[" << GlobalData::GetChannelById(channel_id_) << "] "
          << "read buffer overflow, drop_message[" << interval << "] pre_index["
          << *index << "] current_index[" << buffer_->Tail() << "] ";
    *index = buffer_->Tail();
  }
  m = buffer_->at(*index);
  return true;
}
```

**疑问**：这里获取的id是消息的累计数量，也就是说从开始到结束发送了多少的消息。如果消息大于最新的id，而不是等于最大值+1，则会返回错误的值？？？



## data目录总结

通过上述的分析，实际上数据的访问都是通过"DataVisitor"来实现，数据的分发通过"DataDispatcher"来实现。reader中也是通过DataVisitor来访问数据，在reader中订阅对应的DataDispatcher。

也就是说如果你要订阅一个通道，首先是在reader中注册消息的topic，绑定DataDispatcher，之后对应通道的消息到来之后，触发DataDispatcher分发消息，而DataDispatcher通过DataVisitor中的Notify唤醒协程，从DataVisitor中获取消息，并执行协程中绑定的回调函数，以上就是整个消息的收发过程。

**疑问**： Reader中还拷贝了一份数据到Blocker中，实际上数据的处理过程并不需要缓存数据，参考"Planning"模块中的实现都是在回调函数中把数据拷贝到指针中。看注释是说Blocker是用来仿真的？？？后面需要确实下。以下是Planning模块中回调函数中拷贝数据的实现。

```cpp
  traffic_light_reader_ = node_->CreateReader<TrafficLightDetection>(
      FLAGS_traffic_light_detection_topic,
      [this](const std::shared_ptr<TrafficLightDetection>& traffic_light) {
        ADEBUG << "Received traffic light data: run traffic light callback.";
        std::lock_guard<std::mutex> lock(mutex_);
        // 1. 拷贝消息到指针
        traffic_light_.CopyFrom(*traffic_light);
      });
```



系统在component中自动帮我们创建了一个DataVisitor，订阅component中的消息，融合获取最新的消息之后，执行Proc回调。**需要注意component的第一个消息一定是模块的基准消息来源，也就是模块中最主要的参考消息，不能随便调换顺序**。