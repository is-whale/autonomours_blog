- [Apollo系统监控模块解析（上篇） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/447754065)

## 概述

Apollo系统中，Monitor模块是用来做系统监控的，包括对模块进程的监控、硬件传感器数据的监控、对特定数据channel的监控、对模块节点发现协议层面的监控、对系统CPU/内存/磁盘空间等资源占用的监控、针对特定模块的定制化监控等。

Monitor模块对于需要监控的模块配置信息来自于DreamView模块，具体可以参考dreamview/conf/hmi_modes文件夹中HMIMode的配置文件。

另外，Guardian模块作为系统的安全保护模块，会根据Monitor输出的监控状态和底盘传感器的数据来决定是否要紧急停车。如果需要紧急停车，则发送GuardianCommand到Canbus模块执行Brake的操作；在其他非紧急情况下，Guardian模块会包装透传ControlCommand到Canbus模块执行。也就是说，当把Guardian打开以后(需要同时打开Canbus中的FLAGS_receive_guardian选项)，所有的ControlCommand需要通过Guardian包装以后透传给Canbus模块，同时Canbus将不再直接订阅处理ControlCommand，而是订阅处理GuardianCommand。

## Monitor模块间的数据流

![img](https://pic2.zhimg.com/80/v2-c9c203fda1876b69c589770dbca14095_720w.jpg)

如上图，Monitor模块的输入数据主要包括来自DreamView模块的HMIStatus和HMIMode，还有其他monitor监控类中需要检测的数据，而底盘数据主要是拿来判断当前是否处于自动驾驶模式，这里不再展开来了。

![img](https://pic1.zhimg.com/80/v2-b1dd69d7cf53875a6c42490d867289d0_720w.jpg)

Monitor模块的输出数据主要是SystemStatus，当前主要的订阅模块就是DreamView和Guardian，其中DreamView用来更新HMI上各个模块的状态，Guardian用来处理紧急停车的事件，如下：

![img](https://pic1.zhimg.com/80/v2-35e1781061b204256a979ac0f21a2d7c_720w.jpg)

## Monitor模块的核心类

![img](https://pic2.zhimg.com/80/v2-7d6a71031107df460d15c7c114b64cf5_720w.jpg)



Monitor模块中几个主要的类如下：

1. Monitor类，Apollo模块的组件类，主要负责周期性的触发监控事件，调用每个注册的RecurrentRunner子类的RunOnce函数。
2. MonitorManager类，单例模式，用来缓存一些公共数据，并封装了一些常用的接口函数。
3. RecurrentRunner及其子类，这些是真正执行监控任务并处理相关逻辑的类，如果我们要扩展监控新的模块和数据信息，就需要添加新的RecurrentRunner子类，并在RunOnce函数中实现相关监控处理逻辑。