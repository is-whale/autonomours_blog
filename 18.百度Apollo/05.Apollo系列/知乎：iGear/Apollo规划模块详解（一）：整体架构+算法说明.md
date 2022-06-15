- [Apollo规划模块详解（一）：整体架构+算法说明 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/428441268)

## 1.概述

轨迹规划主要考虑实际临时或者移动障碍物，考虑速度，动力学约束的情况下，尽量按照规划路径进行轨迹规划。轨迹规划的核心就是解决车辆该怎么走的问题。

在Apollo的平台上，规划分为三种模式：

**OnLanePlanning（车道规划，可用于城区及高速公路各种复杂道路）**

**NaviPlanning（导航规划，主要用于高速公路）**

**OpenSpacePlanning （自主泊车和狭窄路段的掉头）**

包含四种具体规划算法：

**PublicRoadPlanner（默认规划器）**

**LatticePlanner、NaviPlanner（主要用于高速公路场景）**

**RTKPlanner（循迹算法，一般不用）**

## **2.整体架构**

规划模块的上游是Localization, Prediction, Routing模块，而下游是Control模块，主要作用就是根据给定的这些信息计算出可供control模块执行的一条局部的，安全且舒适的行驶路径。其内部结构及其与其他模块的交互示意图如下。

![img](https://pic4.zhimg.com/80/v2-4ab253b9da7e1ba8d500007d0e00b08f_720w.jpg)

通过创建PlanningComponen和Public Road Planner类对象及调用相应的初始化函数完成规划器的选择和场景的注册，具体实现下面会详细介绍。

Deciders&Optimizers：决策任务和各种优化。优化器特别优化车辆的轨迹和速度，决策者是基于规则的分类决策者。

## 3.算法说明

### 3.1 输入输出

输入参数为:

预测的障碍物信息(prediction_obstacles)

此类信息源自于perception模块，其输出prediction_obstacles作为prediction模块的输入，经过后续处理，丰富障碍物的预测信息，然后输出至planning模块。

车辆底盘(chassis)信息(车辆的速度，加速度，航向角等信息)

车辆当前位置(localization_estimate)

输出信息：

ADCTrajectory，不仅包含了行驶路线，还包含了每个时刻车辆的速度，加速度，方向转向等信息

### 3.2 算法流程图

Trajectory Planning 的内部算法实现：

![img](https://pic3.zhimg.com/80/v2-e5d02787e02d3b432dddb218e9e0737a_720w.jpg)

### 3.3 代码解析

Planning模块的主入口为：/apollo/cyber/mainboard/[http://mainboard.cc](https://link.zhihu.com/?target=http%3A//mainboard.cc)，之后创建apollo::planning::PlanningComponent类对象，本文planning的介绍从此处开始。在apollo::planning::PlanningComponent中有两个重要的成员函数，PlanningComponent::Init()和PlanningComponent::Proc。

**3.3.1 数据初始化**

在创建apollo::planning::PlanningComponent类对象时会进行初始化，初始化的内容包括：

1. **根据配置选择planning实现方式**

![img](https://pic1.zhimg.com/80/v2-67fab184c1be7a048f96d65ffcbc3f28_720w.jpg)

Planning_base_是PlanningBase的实例化对象，用来描述planning的执行过程，其中比较重要的两个函数是RunOnce和Plan，OnLanePlanning和NaviPlanning都是PlanningBase的继承类，在初始化的时候根据配置默认的规划模式是OnLanePlanning，因此下面所有的介绍都是基于OnLanePlanning

![img](https://pic3.zhimg.com/80/v2-ed59fbebc7306578bb99735432a38a9e_720w.png)

另外OnLanePlanning::Init()函数内部调用DispatchPlanner()创建Public Road Planner，

![img](https://pic3.zhimg.com/80/v2-e1b402c1731b6b10f00d3388ba9ce4aa_720w.png)

进而调用PublicRoadPlanner::Init()，在PublicRoadPlanner::Init()函数内部读取配置文件，获取所有支持的场景，然后调用scenario_manager_.Init(config)对配置文件中的场景初始化，当前的默认场景为：LANE_FOLLOW

![img](https://pic1.zhimg.com/80/v2-caefbfac7efc9f4baf8d984f8214053c_720w.jpg)

**2.实现具体消息的发布和订阅**

//读取routing模块信息、红绿灯信息、pad信息、自车相对于道路特征的位置信息（车辆到路口的距离，到信号灯的距离，到交通标识牌的距离，在规划模块中有助于scenario判断或者某个scenario中stage之间的切换），使用导航模式时读取地图信息

![img](https://pic3.zhimg.com/80/v2-6e52c117aa82500208d07c81e94d9a32_720w.jpg)

//发布规划好的轨迹、重新规划请求、规划学习数据

![img](https://pic3.zhimg.com/80/v2-557094e435d2fe80c54187b4216799d2_720w.jpg)





之前的文章我们将Apollo中感知、规划、控制分模块都进行了入门介绍，接下来的文章我们会给大家带来不同模块的详细解析。这篇文章作为引讲了整体架构及算法说明部分，接下来会推出一系列文章阐述算法实现部分，先放出目录剧透一下~~

- 3.3.2 算法实现
- 3.3.2.1 lane change decider
- 3.3.2.2 path_lane_borrow_decider:
- 3.3.2.3 path_bounds_decider：
- 3.3.2.4 piecewise_jerk_path_optimizer:
- 3.3.2.5 path assessment decider
- 3.3.2.6 path decider

![img](https://pic3.zhimg.com/80/v2-958ca7ec75a7205ceb2d8c478964bb4e_720w.jpg)

后续更新：

[iGear：Apollo规划模块详解：算法实现-9类交通规则（上）](https://zhuanlan.zhihu.com/p/433428958)?

[iGear：Apollo规划模块详解：算法实现-9类交通规则（下）](https://zhuanlan.zhihu.com/p/436139193)?

[iGear：Apollo规划模块详解（五）：算法实现-lane change decider](https://zhuanlan.zhihu.com/p/439010910)?

[iGear：Apollo规划模块详解（六）：算法实现-path lane borrow decider](https://zhuanlan.zhihu.com/p/441721310)

[iGear：Apollo规划模块详解（七）：算法实现-path bounds decider 上篇](https://zhuanlan.zhihu.com/p/444687011)