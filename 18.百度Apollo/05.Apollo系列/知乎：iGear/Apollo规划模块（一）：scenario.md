- [Apollo规划模块（一）：scenario - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/415408124)

## 1.scenario概述：

Apollo中的Planning（规划）模块的场景选择是通过scenario-stage-task并利用状态机行车对车辆状态和规划功能进行维护的。

scenario代表当前所处的场景，包括正常的路上行驶场景、停车场景、红绿灯路口场景等。

scenario功能模块中的主要功能如下：

![img](https://pic2.zhimg.com/80/v2-e3445b1f9941e269a212c9199f0f201d_720w.jpg)图1 scenario功能模块



1. 通过Scenario_manager作为scenario场景的执行和处理的入口，是由PublicRoadPlanner::Plan(）调用
2. scenario及其子类，通过定义的场景以及config配置文件具体执行后续的scenario-stage-task流程
3. tasks及其子类（这部分会在之后的tasks里进行分享）
4. 整个scenario的数据信息来源来自于ReferenceLineInfo类，其中主要通过reference_line, lanes, vehicle_state以及adc_planning_point.

## **2.场景管理 scenario_manager：**

planning模块对于scenario的切换的代码是在scenario_manager中实现的，目前apollo一共支持了11多种场景和场景的定义。

1. Lane Follow scenario：默认驾驶场景，包括本车道保持、变道、基本转弯
2. Bare intersection unprotected：无保护裸露交叉路口
3. Stop sign unprotected：无保护停止标志
4. Traffic light protected：有保护交通灯，即有明确的交通指示灯（左转、右转）
5. Traffic light unprotected left turn：无保护交通灯左转，即没有明确的左转指示灯，需要避让对向的直行来车
6. Traffic light unprotected right turn：无保护交通灯右转，需要避让交通流
7. Pull over ：靠边停车
8. Emergency pull over ：紧急靠边
9. Narrow street u turn ：窄道掉头
10. Park and go：停车和启动
11. Yield sign：让路标志

...

## 3.场景配置

在apollo的planning中，场景首先通过配置文件进行配置, 如在/planning/proto/planning_config.proto中定义一个场景的结构。

![img](https://pic2.zhimg.com/80/v2-884b4c885e37690fdfb7c51c99d0dcc9_720w.jpg)图2 scenario config 场景结构

这里是定义了一个ScenarioType, 同时在这文件中还定义stage 于type字段，这个会在stage与type部分细说。

## 4.场景注册 （Register Scenarios)

从一开始的图片，我们已经可以看出ScenarioManager负责实际的场景注册，而这个场景注会读取/modules/planning/config/scenario目录下的配置文件，具体在代码中是通过ScenarioManager::RegisterScenario这个函数对场景配置文件进行读取，这里注意的是在ScenarioManager::Init()初始化的时候，也就是刚开始执行planning场景时会定义一个默认的场景LANE_FOLLOW。

![img](https://pic2.zhimg.com/80/v2-6baaeae55454dffcd342ed6280c10f0d_720w.jpg)图3 场景注册

##  5.场景确认

ScenarioManager除了负责场景的配置与注册外也负责对场景进行具体的确认，具体实现来看主要是ScenarioManager::Update进行这部分功能代码逻辑的实现。这个函数有两个输入：common::TrajectoryPoint 与 Frame。其中TrajectoryPoint包含了车辆的基础位置，速度，加速度，方向等自车信息。Frame则代表了一次planning循环计算后所有的需要存储的结果与中间信息。

![img](https://pic2.zhimg.com/80/v2-a9bd36e16c19a3d90a377572f3f23b99_720w.jpg)图4 场景update 代码逻辑

其中，ScenarioManager::Observe函数获取当前各个模块的信息，并更新first_encountered_overlaps。

在apollo里 Overlap 是指地图上任意重合的东西，比如PNC_JUNCTION里是道路之间有相互重合，SIGNAL是信号灯与道路有重合，STOP_SIGN是停止标志与道路有重合，YIELD_SIGN是合标志与道路有重合。

![img](https://pic1.zhimg.com/80/v2-7565c9f82a8cb11939d241014d6788b4_720w.jpg)图5 ScenarioManager::Observe函数

而ScenarioManager::ScenarioDispatch函数则会对场景进行具体的处理，在apollo 6.0的planning模块中共有2种处理模式，分别是经典模式（能够有具体的场景定义的）与学习模式（通过深度学习进行场景处理的），分别会根据配置文件调用其后的ScenarioManager::ScenarioLearningDispatch与ScenarioManager::ScenarioNoLearningDispatch。

ScenarioManager::ScenarioNoLearningDispatch则是通过车辆状态frame中的scenario_type对车辆具体场景模块进行切换 ScenarioManager::Select*Scenario，并且由场景执行stage。

## 小结

至此，Apollo规划模块中的Scenario部分已介绍完毕，后续我们会对规划模块中的stage、task阶段进行详细介绍。

规划模块的主要任务是根据导航信息及车辆当前状态，在有限的时间范围内，计算出一条合适的轨迹供车辆行驶。其上游是定位、地图，导航，感知模块[Apollo 感知模块（一）：Radar Perception](https://zhuanlan.zhihu.com/p/415416588)，下游是控制模块[iGear：Apollo控制模块记录](https://zhuanlan.zhihu.com/p/423071273)。