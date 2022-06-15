- [【机器人】无人车-运动规划-Apollo6.0-planning模块代码框架梳理(二)--PlanningComponent - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/457857048)

## Apollo6.0 planning模块代码框架梳理(二)--PlanningComponent

## 简介

- 本文主要针对Apollo6.0代码库的planning模块进行更为细致的介绍，想了解更为宽泛的介绍参考上一篇文章

[【机器人】无人车-运动规划-Apollo6.0-planning模块代码框架梳理(一)](https://zhuanlan.zhihu.com/p/455162288)

- 为了便于后续简化表述，此处做一些不太严谨的名词约束：
- **planning包**：表示apollo/modules/planning文件夹下的内容
- **planning组件**：表示apollo/modules/planning/planning_component.h文件中定义的PlanningComponent类、或者对应的对象
- **planning模块**：表示来自apollo/modules/planning/planning_base.h中的PlanningBase类、对象，以及其子类，包括OnLanePlanning、NaviPlanning类、对象等，本文针对OnLanePlanning类展开讨论，即代指OnLanePlanning模块
- 本文的重点是关于**planning组件**的逻辑梳理，planning模块的梳理在后续文章中展开

## 数据流

要想更为清晰的理解planning模块的工作逻辑，需要对整个模块工作时的数据流进行初步了解。Apollo6.0 代码库的planning模块数据流大致如下图所示。

![img](https://pic4.zhimg.com/80/v2-aebe95247c690555fb7dc0a0a65e52a3_720w.jpg)PlanningComponent 逻辑图

- 一个planning模块，作为planning的核心结构，负责规划工作
- 多个输入、输出通道(channel)，负责与其他模块进行数据通信
- 一个数据缓存区(DependencyInjector)，负责全程数据的缓存与交互
- 两个处理流，分别实现初始化(Init)，和消息响应处理(Proc)

以下逐个进行简要介绍

## 组件对象 PlanningComponent

- planning组件，即PlanningComponent类、对象，其继承自cyber::Component<M0, M1, M2, NullType>类，可在CyberRT中完成注册工作，实现消息响应机制的托管，是工程实现和算法实现之间的桥梁。
- planning组件是一个支持3个channel输入的消息回调型组件，支持的消息类型分别如下：

1. 预测消息：prediction::PredictionObstacles
2. 底盘消息：canbus::Chassis
3. 定位消息：localization::LocalizationEstimate

- CyberRT系统收到上述任何一个消息后都会调起Planning组件的Proc函数进行消息响应（与回调型组件对应的是定时器型组件，即CyberRT中的定时器会按着一定的频率调用Proc函数）。
- planning模块，是实际的planning核心框架，作为planning组件的成员变量，实现具体的规划功能，具体逻辑本文暂不展开讲解。

## 组件数据缓存 DependencyInjector

- DependencyInjector：依赖注入器，这是一个过于专业的名词，来自软件设计模式的**依赖倒置**原则的一种具体实现方式，起到模块解耦作用。
- DependencyInjector本质就是一个数据缓存中心，叫DataCacheCenter可能更贴切些。
- DependencyInjector对象内部管理者planning模块工作过程中的实时数据和几乎全部历史数据，以便于规划任务的前后帧之间的承接，以及异常处理的回溯。
- DependencyInjector是以空对象的形式引入到planning组件中，进而引入到planning模块中用来承载中间数据。 DependencyInjector的类结构如下图所示：

![img](https://pic4.zhimg.com/80/v2-63f58d25b17c0665a958b962895a071b_720w.jpg)DependencyInjector结构图

- 依赖注入结构中主要有6类成员变量，分别如下：
- **PlanningContext** planning_context_：负责planning上下文的缓存，比如是否触发重新路由的ReroutingStatus信息
- **History** history_：负责障碍物状态的缓存，包括运动状态、决策结果。该数据与routing结果绑定，routing变更后会清理掉历史数据。
- **FrameHistory** frame_history_：是一个可索引队列，负责planning的输入、输出等主要信息的缓存，以**Frame**类进行组织，内部包含LocalView结构体（负责输入数据的融合管理）。与上述的History是不同的是，该缓数据自模块启动后就开始缓存所有的Frame对象，不受routing变动的影响。
- **EgoInfo** ego_info_：提供车辆动、静信息，即车辆运动状态参数（轨迹、速度、加速度等）和车辆结构参数（长宽高等）
- **apollo::common::VehicleStateProvider** vehicle_state_：车辆状态提供器，用于获取车辆实时信息
- **LearningBasedData** learning_based_data_：基于学习的数据，用于学习建模等

## 组件工作流

planning组件主要完成两项任务：初始化工作Init，消息响应工作Proc，具体过程可参考下图的1~6步

![img](https://pic4.zhimg.com/80/v2-aebe95247c690555fb7dc0a0a65e52a3_720w.jpg)PlanningComponent逻辑图

### 初始化

- 初始化工作由CyberRT系统的launch命令触发，大致分为2步，核心代码整理截图如下

![img](https://pic3.zhimg.com/80/v2-1d1f97028915a8b09737fd2cdd1787a6_720w.jpg)Init()

- 1、planning模块的初始化，根据标志位选择具体的PlanningBase子类，并调用Init函数初始化，图中1所示
- 2、建立消息通道(channel)，包括输入通道traffic_light、routing等（图中2-1）和输出通道rerouting、planning等（图中2-2）

### 消息响应

- 消息响应由CyberRT在接收到关联消息（上文有提到3类中的任何1类）到达后触发，工作大致分为4步，核心代码整理截图如下

![img](https://pic3.zhimg.com/80/v2-8ad98516974c0b47bec2b00ea5caf706_720w.jpg)Proc()

- 3、工作前检查，包括重新进行路由的检查（图中3-1），如果需要会发送rerouting消息；输入数据融合与检查（图中3-2），数据来源包括外部传参和内部缓存数据，一并汇总到LocalView结构体中，实现融合，并最终会保存到Frame对象中
- 4、执行规划任务（图中4），此处会调用PlanningBase子类的RunOnce函数进行一次规划任务，生成规划后的轨迹
- 5、发布规划结果（图中5），利用已经创建的planning通道发送规划结果
- 6、更新历史缓存（图中6），将最终轨迹更新到history中（其他缓存在PlanningBase子类中完成的）以备不时之需

## 结语

本文只对planning组件的逻辑进行了梳理介绍，不难看出，该部分的结果还是相对比较清晰明了，planning的大部分工作是在PlanningBase子类中完成的，这部分的梳理在后续文章中展开。