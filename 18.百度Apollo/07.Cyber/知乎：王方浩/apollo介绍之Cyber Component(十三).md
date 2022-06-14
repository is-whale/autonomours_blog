- [apollo介绍之Cyber Component(十三) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/116782645)

## Component介绍

我们首先需要清楚一点，**component实际上是cyber为了帮助我们特意实现的对象，component加载的时候会自动帮我们创建一个node，通过node来订阅和发布对应的消息**，每个component有且只能对应一个node。

**component对用户提供2个接口"Init()"和"Proc()"，用户在Init中进行初始化，在"Proc"中接收Topic执行具体的算法**。对用户隐藏的部分包括component的"Initialize()"初始化，以及"Process()"调用执行。

component还可以动态的加载和卸载，这也可以对应到在dreamviewer上动态的打开关系模块。下面我们先大致介绍下component的工作流程，然后再具体介绍各个模块。



## component工作流程

component的工作流程大致如下：

1. 通过继承"cyber::Component"，用户自定义一个模块，并且实现"Init()"和"Proc()"函数。编译生成".so"文件。
2. 通过classloader加载component模块到内存，创建component对象，调用"Initialize()"初始化。（Initialize中会调用Init）
3. 创建协程任务，并且注册"Process()"回调，当数据到来的时候，唤醒对象的协程任务执行"Process()"处理数据。（Process会调用Proc）

综上所述，component帮助用户把初始化和数据收发的流程进行了封装，减少了用户的工作量，component封装了整个数据的收发流程，component本身并不是单独的一个线程执行，模块的初始化都在主线程中执行，而具体的任务则是在协程池中执行。



## cyber入口

cyber的入口在"cyber/mainboard/[http://mainboard.cc](https://link.zhihu.com/?target=http%3A//mainboard.cc)"中，主函数中先进行cyber的初始化，然后启动cyber模块，然后运行，一直等到系统结束。

```cpp
int main(int argc, char** argv) {
  // 1. 解析参数
  ModuleArgument module_args;
  module_args.ParseArgument(argc, argv);

  // 2. 初始化cyber
  apollo::cyber::Init(argv[0]);

  // 3. 启动cyber模块
  ModuleController controller(module_args);
  if (!controller.Init()) {
    controller.Clear();
    AERROR << "module start error.";
    return -1;
  }
  
  // 4. 等待直到程序退出
  apollo::cyber::WaitForShutdown();
  controller.Clear();
  return 0;
}
```



## component动态加载

cyber主函数在"ModuleController::Init()"进行模块的加载，具体的加载过程在"ModuleController::LoadModule"中。

```cpp
bool ModuleController::LoadModule(const DagConfig& dag_config) {
  const std::string work_root = common::WorkRoot();

  for (auto module_config : dag_config.module_config()) {
    // 1. 加载动态库
    class_loader_manager_.LoadLibrary(load_path);
    
    // 2. 加载消息触发模块
    for (auto& component : module_config.components()) {
      const std::string& class_name = component.class_name();
      // 3. 创建对象
      std::shared_ptr<ComponentBase> base =
          class_loader_manager_.CreateClassObj<ComponentBase>(class_name);
      // 4. 调用对象的Initialize方法
      if (base == nullptr || !base->Initialize(component.config())) {
        return false;
      }
      component_list_.emplace_back(std::move(base));
    }
    
    // 5. 加载定时触发模块
    for (auto& component : module_config.timer_components()) {
      // 6. 创建对象
      const std::string& class_name = component.class_name();
      std::shared_ptr<ComponentBase> base =
          class_loader_manager_.CreateClassObj<ComponentBase>(class_name);
      // 7. 调用对象的Initialize方法
      if (base == nullptr || !base->Initialize(component.config())) {
        return false;
      }
      component_list_.emplace_back(std::move(base));
    }
  }
  return true;
}
```

模块首先通过classloader加载到内存，然后创建对象，并且调用模块的初始化方法。component中每个模块都设计为可以动态加载和卸载，可以实时在线的开启和关闭模块，实现的方式是通过classloader来进行动态的加载动态库。



## component初始化

component一共有4个模板类，分别对应接收0-3个消息，（这里有疑问为什么没有4个消息的模板类，是漏掉了吗？）我们这里主要分析2个消息的情况，其它的可以类推。

```cpp
template <typename M0, typename M1>
bool Component<M0, M1, NullType, NullType>::Initialize(
    const ComponentConfig& config) {
  // 1. 创建Node
  node_.reset(new Node(config.name()));
  LoadConfigFiles(config);

  // 2. 调用用户自定义初始化Init()
  if (!Init()) {
    AERROR << "Component Init() failed.";
    return false;
  }

  bool is_reality_mode = GlobalData::Instance()->IsRealityMode();

  ReaderConfig reader_cfg;
  reader_cfg.channel_name = config.readers(1).channel();
  reader_cfg.qos_profile.CopyFrom(config.readers(1).qos_profile());
  reader_cfg.pending_queue_size = config.readers(1).pending_queue_size();
  
  // 3. 创建reader1
  auto reader1 = node_->template CreateReader<M1>(reader_cfg);
  ...
  // 4. 创建reader0
  if (cyber_likely(is_reality_mode)) {
    reader0 = node_->template CreateReader<M0>(reader_cfg);
  } else {
    ...
  }
  
  readers_.push_back(std::move(reader0));
  readers_.push_back(std::move(reader1));


  auto sched = scheduler::Instance();
  // 5. 创建回调，回调执行Proc()
  std::weak_ptr<Component<M0, M1>> self =
      std::dynamic_pointer_cast<Component<M0, M1>>(shared_from_this());
  auto func = [self](const std::shared_ptr<M0>& msg0,
                     const std::shared_ptr<M1>& msg1) {
    auto ptr = self.lock();
    if (ptr) {
      ptr->Process(msg0, msg1);
    } else {
      AERROR << "Component object has been destroyed.";
    }
  };

  std::vector<data::VisitorConfig> config_list;
  for (auto& reader : readers_) {
    config_list.emplace_back(reader->ChannelId(), reader->PendingQueueSize());
  }
  // 6. 创建数据访问器
  auto dv = std::make_shared<data::DataVisitor<M0, M1>>(config_list);
  // 7. 创建协程，协程绑定回调func（执行proc）。数据访问器dv在收到订阅数据之后，唤醒绑定的协程执行任务，任务执行完成之后继续休眠。
  croutine::RoutineFactory factory =
      croutine::CreateRoutineFactory<M0, M1>(func, dv);
  return sched->CreateTask(factory, node_->Name());
}
```

总结以下component的流程。

1. 创建node节点（1个component只能有1个node节点，之后用户可以用node_在init中自己创建reader或writer）。
2. 调用用户自定义的初始化函数Init()（子类的Init方法）
3. 创建reader，订阅几个消息就创建几个reader。
4. 创建回调函数，实际上是执行用户定义算法Proc()函数
5. 创建数据访问器，数据访问器的用途为接收数据（融合多个通道的数据），唤醒对应的协程执行任务。
6. 创建协程任务绑定回调函数，并且绑定数据访问器到对应的协程任务，用于唤醒对应的任务。

因为之前对cyber数据的收发流程有了一个简单的介绍，这里我们会分别介绍如何创建协程、如何在scheduler注册任务并且绑定Notify。也就是说，为了方便理解，你可以认为数据通过DataDispatcher已经分发到了对应的DataVisitor中，**接下来我们只分析如何从DataVisitor中取数据，并且触发对应的协程执行回调任务**。



## 创建协程

创建协程对应上述代码

```text
  croutine::RoutineFactory factory =
      croutine::CreateRoutineFactory<M0, M1>(func, dv);
```

接下来我们查看下如何创建协程呢？协程通过工厂模式方法创建，**里面包含一个回调函数和一个dv（数据访问器）**。

```cpp
template <typename M0, typename M1, typename F>
RoutineFactory CreateRoutineFactory(
    F&& f, const std::shared_ptr<data::DataVisitor<M0, M1>>& dv) {
  RoutineFactory factory;
  // 1. 工厂中设置DataVisitor
  factory.SetDataVisitor(dv);
  factory.create_routine = [=]() {
    return [=]() {
      std::shared_ptr<M0> msg0;
      std::shared_ptr<M1> msg1;
      for (;;) {
        CRoutine::GetCurrentRoutine()->set_state(RoutineState::DATA_WAIT);
        // 2. 从DataVisitor中获取数据
        if (dv->TryFetch(msg0, msg1)) {
          // 3. 执行回调函数
          f(msg0, msg1);
          // 4. 继续休眠
          CRoutine::Yield(RoutineState::READY);
        } else {
          CRoutine::Yield();
        }
      }
    };
  };
  return factory;
}
```

上述过程总结如下：

1. 工厂中设置DataVisitor
2. 工厂中创建设置协程执行函数，回调包括3个步骤：从DataVisitor中获取数据，执行回调函数，继续休眠。



## 创建调度任务

创建调度任务是在过程"Component::Initialize"中完成。

```cpp
sched->CreateTask(factory, node_->Name());
```

我们接着分析如何在Scheduler中创建任务。

```cpp
bool Scheduler::CreateTask(std::function<void()>&& func,
                           const std::string& name,
                           std::shared_ptr<DataVisitorBase> visitor) {
  // 1. 根据名称创建任务ID
  auto task_id = GlobalData::RegisterTaskName(name);
  
  auto cr = std::make_shared<CRoutine>(func);
  cr->set_id(task_id);
  cr->set_name(name);
  AINFO << "create croutine: " << name;
  // 2. 分发协程任务
  if (!DispatchTask(cr)) {
    return false;
  }

  // 3. 注册Notify唤醒任务
  if (visitor != nullptr) {
    visitor->RegisterNotifyCallback([this, task_id]() {
      if (cyber_unlikely(stop_.load())) {
        return;
      }
      this->NotifyProcessor(task_id);
    });
  }
  return true;
}
```



## TimerComponent对象

实际上Component分为2类：一类是上面介绍的消息驱动的Component，第二类是定时调用的TimerComponent。定时调度模块没有绑定消息收发，需要用户自己创建reader来读取消息，如果需要读取多个消息，可以创建多个reader。

```cpp
bool TimerComponent::Initialize(const TimerComponentConfig& config) {
  // 1. 创建node
  node_.reset(new Node(config.name()));
  LoadConfigFiles(config);
  // 2. 调用用户自定义初始化函数
  if (!Init()) {
    return false;
  }

  std::shared_ptr<TimerComponent> self =
      std::dynamic_pointer_cast<TimerComponent>(shared_from_this());
  // 3. 创建定时器，定时调用"Proc()"函数
  auto func = [self]() { self->Proc(); };
  timer_.reset(new Timer(config.interval(), func, false));
  timer_->Start();
  return true;
}
```

总结一下TimerComponent的执行流程如下。

1. 创建Node
2. 调用用户自定义初始化函数
3. 创建定时器，定时调用"Proc()"函数



上述就是Component模块的调用流程。为了弄清楚消息的调用过程，下面我们分析"DataDispatcher"和"DataVisitor"。