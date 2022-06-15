- [Apollo控制模块记录 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/423071273)

## 1.概述

Apollo中的control模块是一个相对比较简单的模块，其主要功能是根据读取的PadMessage、Chassis信息、Planning模块输出的轨迹ADCTrajectory以及位置信息LocalizationEstimate，产生相应的控制命令ControlCommand并发送出去。

![img](https://pic3.zhimg.com/80/v2-1eb306cc5e9f0e67513f6b245b7c1fb6_720w.jpg)

## 2.信息收集

control模块的入口为ControlComponent类，首先注意到这个类的基类为apollo::cyber::TimerComponent，这意味着ControlComponent::Proc()会被周期性的调用：

![img](https://pic2.zhimg.com/80/v2-37d9a5802f847a98904f0f06210f1dd1_720w.jpg)

49行创建了一个Timer，每隔config.interval()毫秒调用一次48行定义的func函数，也就是调用self->Proc()。config.interval的数值在control模块的dag文件modules/control/dag/control.dag中指定：

![img](https://pic1.zhimg.com/80/v2-af2a73cca03a10354c216eb399f7a4d0_720w.jpg)

第9行指定interval为10ms。

下面来看看ControlComponent::Proc()函数，由于代码比较长，这里仅选取关键的代码片段：

![img](https://pic2.zhimg.com/80/v2-4587c996397a639f2b145d98fde583ed_720w.jpg)


282 - 289行读取Chassis，291 - 297行读取轨迹规划ADCTrajectory，299 - 305读取位置信息LocalizationEstimate，307 - 311行读取PadMessage。这4种消息的读取类似，我们以读取Chassis为例：283行从Chassis的发布渠道读取最新的消息，然后调用OnChassis(chassis_msg)将读取到的消息拷贝到latest_chassis_成员：

![img](https://pic4.zhimg.com/80/v2-500b9513c61f9b442c4feb50a7eeb617_720w.png)

接着Proc()将这4种消息合并到local_view_成员：

![img](https://pic1.zhimg.com/80/v2-73c0a7226848d9ec0c6e423e6f892298_720w.jpg)

## 3.控制命令生成

信息收集完毕，下一步就是对信息进行处理，生成控制命令。这部分代码也在ControlComponent::Proc()中：

![img](https://pic1.zhimg.com/80/v2-973539d7d356b94b93c589c5a8d9ae24_720w.jpg)

367行调用ProduceControlCommand()生成控制命令ControlCommand，后面的371 - 389行是填充时间戳等补充信息。ProduceControlCommand()中有这样一段关键代码：

![img](https://pic3.zhimg.com/80/v2-1f75d09769e2068a89c8e099357b31ce_720w.jpg)

244 - 246行调用ControllerAgent::ComputeControlCommand()，基于传入的LocalizationEstimate、Chassis和ADCTrajectory参数来填充ControlCommand结构：

![img](https://pic2.zhimg.com/80/v2-14d57f5aaf5680d36142ff9bf8766249_720w.jpg)

这个函数对controller_list_中的每一个controller，调用其ComputeControlCommand()方法。controller_list_在ControllerAgent::InitializeConf()中被初始化的：

![img](https://pic1.zhimg.com/80/v2-3ec7e74525149b04af1161b66e1a0044_720w.jpg)

对于control_conf_中的active_controllers，在70行将其加入controller_list_列表。control conf文件路径为modules/control/conf/control_conf.pb.txt：

![img](https://pic1.zhimg.com/80/v2-d2599b00022216cfb538189f5f68535c_720w.jpg)

25、26两行指定启用的controller为LAT_CONTROLLER和LON_CONTROLLER。

### 3.1 LON_CONTROLLER

顾名思义，LON_CONTROLLER就是对车辆进行纵向的控制，包括刹车、油门等等。LonController::ComputeControlCommand()涉及纵向控制的具体算法，故而代码很长，但是归根结底，最终目的在于函数结尾处的这块代码：

![img](https://pic2.zhimg.com/80/v2-f20e4020ef4cdad844cbce43d0875b79_720w.jpg)

356 - 358行设置油门、刹车和加速度，364和366行设置车辆档位。

### 3.2 LAT_CONTROLLER

LAT_CONTROLLER对车辆进行横向控制，主要是方向盘的转角。

LatController::ComputeControlCommand()的代码同样很复杂，但是目的性很强：万语千言只为了函数结尾处605 - 608行的设置方向盘的转角和转速。

![img](https://pic3.zhimg.com/80/v2-bd957ae0867cf5f66af4011abd6e07b6_720w.jpg)

## 4.控制命令的发送

这部分代码仍然在ControlComponent::Proc()中也非常简单：在函数末尾调用Ｗriter的Write方法将填充好的ControlCommand发送出去：

![img](https://pic1.zhimg.com/80/v2-b798c13626e86539a530203f9317b910_720w.png)

ControlCommand的发布渠道为"/apollo/control"。