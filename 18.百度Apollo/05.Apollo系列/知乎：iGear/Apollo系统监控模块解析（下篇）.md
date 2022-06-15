- [Apollo系统监控模块解析（下篇） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/450627615)

## Monitor模块的处理流程

### 1.Monitor模块的初始化

这部分功能在Monitor::Init中实现，主要是初始化MonitorManager，实例化每个RecurrentRunner子类，并放入集合runners_中去。这里需要注意SummaryMonitor应该放在最后放入集合中去，因为它会汇总其他monitor类的检测结果并填入Component::summary，最后，将最终的SystemStatus发布出去。

### 2.Monitor模块的周期回调函数(如下图)

现在的回调时间间隔是 500ms，在dag文件中可以配置。该函数中，首先调用MonitorManager::Instance()->StartFrame获取当前的HMIMode和当前是否处于自动驾驶模式；然后，依次调用每个RecurrentRunner子类的Tick函数，在Tick中会调用纯虚函数RunOnce，每个子类通过RunOnce来实现不同的监控检测任务，每个子类的调用时间间隔可以在构造对象的时候自己指定；最后，调用MonitorManager::Instance()->EndFrame()发布monitor日志到/apollo/monitor主题。

![img](https://pic4.zhimg.com/80/v2-1e6a0299d9075e5d79a539fd866fc0d3_720w.jpg)

### 3.关于HMIMode配置的处理(这里要跑题了，需要介绍一下DreamView的处理流程:-) )。

a.我们先看一下MonitorManager::Instance()->StartFrame的处理流程，首先，从DreamView模块获取HMIStatus；然后，判断当前HMIMode是否有变化，如果已经改变了则清空当前SystemStatus里面需要监控的模块信息，并重新根据HMIMode加载相关的模块信息；如果当前HMIMode没有改变，则只是清除上一次每个模块的summary结果。

![img](https://pic3.zhimg.com/80/v2-cb5eeecb8981c316f126b7d8e8607aea_720w.jpg)

b.那么DreamView又是如何处理HMIStatus和HMIMode的呢？我们接下来看看DreamView中关于HMIMode的处理流程：首先，当用户在界面中更改了当前的mode配置，则会通过websocket发一个HMIAction::CHANGE_MODE到DreamView后台，如下图：

![img](https://pic1.zhimg.com/80/v2-444ae8b07a14018c6acbd80b594e61d8_720w.jpg)

而在apollo::dreamview::HMI类的RegisterMessageHandlers函数中会注册websocket的HMIAction的回调函数，如下：

![img](https://pic4.zhimg.com/80/v2-8129a141cbe6ca0ed0dc5680c2535b2f_720w.jpg)

处理HMIAction::CHANGE_MODE的请求，最终会调用HMIWorker::ChangeMode函数完成，该函数会先调用HMIWorker::ResetMode杀掉上一个Mode打开的所有模块进程；然后，根据Mode名称获取到对应的配置文件名，当前所有的Mode配置文件放在/apollo/modules/dreamview/conf/hmi_modes目录下；最后，调用HMIWorker::LoadMode加载新的Mode，并更新HMIStatus。具体代码如下：

![img](https://pic3.zhimg.com/80/v2-6e424e54824dc32323152ebd3796bb82_720w.jpg)

这里有一个地方需要注意，细心的同学可能会发现代码里面一直都是处理的modules，但是配置文件里面都是定义的cyber_modules，很少有modules的定义，那么两者有什么关系呢？原来在HMIWorker::LoadMode函数中，会帮我们把CyberModule转为Module，代码如下：

![img](https://pic3.zhimg.com/80/v2-9aa22c7e72b27cd118105412a05aaf5a_720w.jpg)

c.最后，我们看一下HMIMode配置文件，目前提供4种类型的模块配置信息：cyber_modules、modules、monitored_components和other_components，如下：

![img](https://pic4.zhimg.com/80/v2-d672018e35dfe5459684a86174433dd7_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-ea0a0085f3a1ea7ef9a5df980124857f_720w.jpg)



那么它们在Monitor模块里面处理上有什么不同呢？

- **cyber_modules**

- - 因为在LoadMode的时候会被转为modules，所以处理流程请参考modules。



- **modules**

- - 会被放入SystemStatus的hmi_modules集合中，集合每一项的Value是一个ComponentStatus；
  - 在ProcessMonitor中，会检查每个模块对应进程的状态，并将结果更新到hmi_modules对应的ComponentStatus中去；
  - 在FunctionalSafetyMonitor::CheckSafety中，会去检查hmi_modules的ComponentStatus状态，如果该模块设置了required_for_safety属性(默认为true)，则当模块状态异常时，会导致触发进入安全模式并紧急停车。



- **monitored_components**

- - 会被放入SystemStatus的components集合中，集合每一项的Value是一个Component；
  - 在为每个特殊模块实现的RecurrentRunner子类中，会先根据模块名在components里面查找对应的模块，然后会检查和更新该Component的other_status；
  - 在ResourceMonitor中，如果该component的配置中有resource定义，则检查对应的resource项(目前支持磁盘占用/CPU占用/内存占用/磁盘负载)，并更新Component的resource_status；
  - 在ChannelMonitor中，如果该component的配置中有channel定义，则检查该channel是否有数据输出、处理延时情况和发送频率，最后更新Component的channel_status；
  - 在ModuleMonitor中，通过NodeManager检查模块当前是否存在(这里涉及到fast_rtps协议的发现机制，以后有机会再展开讨论)，这里主要还是考虑到有可能模块进程还在，但是已经不响应任何rtps协议消息了，最后更新Component的module_status；
  - 在ProcessMonitor中，会检查每个模块对应进程的状态，并将结果更新到Component的process_status中去；
  - 在SummaryMonitor中，会根据以上的各个状态，综合一个最终的summary状态，并更新到Component的summary中去；
  - 在FunctionalSafetyMonitor::CheckSafety中，会去检查components的summary状态，如果该模块设置了required_for_safety属性(默认为true)，则当该模块summary状态异常，会导致触发进入安全模式并紧急停车。



- **other_components**

- - 会被放入SystemStatus的other_components集合中，集合每一项的Value是一个ComponentStatus；
  - 在ProcessMonitor中，会检查每个模块对应进程的状态，并将结果更新到other_components对应的ComponentStatus中去。

![img](https://pic1.zhimg.com/80/v2-047a823b84a1bcb54a21881a747cf5dc_720w.jpg)

**d. 特别注意，在配置中添加required_for_safety属性为true(默认值为true)，将会导致该模块异常后触发进入安全模式并紧急停车。**

### 4.已实现的RecurrentRunner子类介绍

- **EsdCanMonitor**，用来监控ESD-CAN设备的，目前主要的检测方法是打开CAN设备，通过设备提供的ioctl获取当前设备的状态；最后，会更新Component::other_status的状态。
- **GpsMonitor**，用来监控当前的GPS状态，目前主要的检测方法是获取GnssBestPose数据，并根据SolutionType判断GPS当前的状态；最后，会更新Component::other_status的状态。
- **SocketCanMonitor**，用来监控Socket CAN设备，目前主要的检测方法是通过Socket接口打开和绑定到CAN设备，而且CAN设备的ifname写死是can0；最后，会更新Component::other_status的状态。
- **ResourceMonitor**，用来监控所有SystemStatus::components(HMIMode::monitored_components)中的模块，并找出那些在配置文件中定义了resource属性的模块，会去检测它们当前的resource占用情况，如果超出了设定值则会报错，目前支持磁盘占用/CPU占用/内存占用/磁盘负载的检测；最后，会更新对应模块的Component::resource_status状态。
- **CameraMonitor**，用来监控摄像头设备的，目前主要的检测方法是通过订阅多个camera输出的主题，并通过frame id来判定当前状态(这里的frame_id是摄像头的类型，具体参考modules/drivers/camera/conf下protobuf文件的定义)，而且只允许存在一个摄像头；最后，会更新Component::other_status的状态。
- **ChannelMonitor**，用来监控所有SystemStatus::components(HMIMode::monitored_components)中的模块，并找出那些在配置文件中定义了channel属性的模块，会去订阅并检测channel是否有数据、处理延时情况、发送频率等信息；最后，会更新对应模块的Component::channel_status状态。
- **LatencyMonitor**，用来统计系统中所有模块的延时情况的，并为ChannelMonitor提供发送频率的统计数据，目前主要是通过订阅/apollo/common/latency_records主题，获取模块的处理延时记录，并计算一段时间内的发送频率。注意，如果需要跟踪某个模块的处理延时，还需要在该模块的实现中创建apollo::common::LatencyRecorder实例，并通过它记录每次Proc方法的处理延时。
- **LocalizationMonitor**，用来监控定位数据的状态，目前主要通过订阅LocalizationStatus消息来判断状态；最后，会更新Component::other_status的状态。
- **ModuleMonitor**，用来监控所有SystemStatus::components(HMIMode::monitored_components)中的模块，并找出那些在配置文件中定义了module属性的模块，使用node名称调用NodeManager检查模块当前是否存在(这里涉及到fast_rtps协议的发现机制，以后有机会再展开讨论)，这里主要还是考虑到有可能模块进程还在，但是已经不响应任何rtps协议消息了，最后更新Component::module_status状态。
- **ProcessMonitor**，用来监控所有SystemStatus::components、SystemStatus::hmi_modules和other_components中模块对应的进程是否存在，这是一种最基本的检测方式，最后更新Component:: process_status状态。
- **RecorderMonitor**，用来监控SmartRecorder的状态，当前通过订阅SmartRecorderStatus来判断recorder的状态；最后，会更新Component::other_status的状态。
- **SummaryMonitor**，主要是遍历SystemStatus::components中的每一个模块，并根据该模块的process_status、module_status、channel_status、resource_status和other_status计算一个综合状态更新到模块的summary状态中去；最后，SummaryMonitor会把更新后的SystemStatus发布出去。
- **FunctionalSafetyMonitor**，主要是根据每个模块的状态，判断是否需要触发进入安全模式和紧急停车。现在判断的依据是：首先，必须是进入自动驾驶模式；其次，遍历所有的SystemStatus::components和SystemStatus::hmi_modules中的模块，如果模块的summary状态异常(ERROR或FATAL)且该模块的required_for_safety属性为true(默认值为true)，**则会触发进入安全模式，当进入安全模式10秒后(时间可配置)没有任何改善和恢复，则会立即触发紧急停车。**

## 扩展自定义监控类的流程

1. 新建RecurrentRunner子类，并在RunOnce函数中实现相关监控处理逻辑。
2. 在RunOnce函数中，首先，根据模块名称在MonitorManager::GetStatus()::mutable_components()中，获取对应的Component实例；然后，在处理完监控逻辑后，调用SummaryMonitor::EscalateStatus函数更新Component的other_status。
3. 在Monitor::Init函数中，注册新创建的RecurrentRunner子类，一定要放在SummaryMonitor之前注册。
4. 修改HMIMode的配置文件，添加一个monitored_components节点，注意里面的key值一定要与第2步中的模块名称一致。

## 其他

这里有些需要注意的地方：

1. 在Guardian模块中，当前在GuardianComponent::TriggerSafetyMode函数中，传感器的数据是写死被忽略掉了(如下图)，所以现在只有当监控状态被设置为请求紧急停车时才生效。

![img](https://pic3.zhimg.com/80/v2-e7feecee64b8dbf4adf7b7ac3a998a2e_720w.jpg)

\2. 开启Guardian模块的同时，Canbus模块的FLAGS_receive_guardian选项必须设置为true，如下：

![img](https://pic2.zhimg.com/80/v2-e6542c4a9c5e6a60cc91b63c6af05f7d_720w.png)