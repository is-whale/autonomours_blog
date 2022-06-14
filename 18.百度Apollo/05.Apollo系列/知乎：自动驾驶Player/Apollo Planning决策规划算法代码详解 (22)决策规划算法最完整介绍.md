- [Apollo Planning决策规划算法代码详解 (22):决策规划算法最完整介绍 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/516568314)

前言：

后台已经写完了Apollo Planning决策规划算法的完整解析，一路从规划模块的入口OnLanePlanning，介绍到常见的规划器PublicRoadPlanner；接着介绍了在PublicRoadPlanner中如何通过类似有限状态机的ScenarioDispatch进行场景决策。之后又介绍了在每个场景Scenario中如何配置以及判断当前所处的stage，以及对于每个stage又是如何注册tasks来执行具体的规划任务。

现在回头来看，这个系列应该是目前全网最完整的apollo规划算法planning模块的解析教程了，所以现在阶段性的，想再对apollo整个规划算法的流程做一个总结，也可以当做是整个系列的文章的概述。

在本文将会讲解下面这些内容：

1、apollo规划算法整体运行框架pipline

2、常用的LANE_FOLLOW场景如何执行具体的规划任务；

3、LANE_FOLLOW_DEFAULT_STAGE有哪些tasks，这些task分别执行什么任务；

4、完整的决策规划流程

正文如下：

一、apollo规划算法整体运行框架

在每个运行周期内，可以理解为task是最小的执行单位；按照配置不同的task构成了stage；当确定所处stage后，会执行stage下注册的所有task。

[apollo](https://link.zhihu.com/?target=https%3A//so.csdn.net/so/search%3Fq%3Dapollo%26spm%3D1001.2101.3001.7020)规划算法中，task的执行流程如下，下图便是apollo规划算法的完整运行流程：



![img](https://pic3.zhimg.com/80/v2-e04973fbdd0a3890edb860ecb9d042fe_720w.jpg)

二、apollo规划算法具体执行的task

上文讲到PublicRoadPlanner 的 LaneFollowStage 是在自动驾驶过程中使用频率最高的场景与stage，LaneFollowStage的配置如下，正是下面这些task组成了在LaneFollowStage状态下完整的决策规划算法。

```text
scenario_type: LANE_FOLLOW
stage_type: LANE_FOLLOW_DEFAULT_STAGE
stage_config: {
  stage_type: LANE_FOLLOW_DEFAULT_STAGE
  enabled: true
  task_type: LANE_CHANGE_DECIDER
  task_type: PATH_REUSE_DECIDER
  task_type: PATH_LANE_BORROW_DECIDER
  task_type: PATH_BOUNDS_DECIDER
  task_type: PIECEWISE_JERK_PATH_OPTIMIZER
  task_type: PATH_ASSESSMENT_DECIDER
  task_type: PATH_DECIDER
  task_type: RULE_BASED_STOP_DECIDER
  task_type: ST_BOUNDS_DECIDER
  task_type: SPEED_BOUNDS_PRIORI_DECIDER
  task_type: SPEED_HEURISTIC_OPTIMIZER
  task_type: SPEED_DECIDER
  task_type: SPEED_BOUNDS_FINAL_DECIDER
  # task_type: PIECEWISE_JERK_SPEED_OPTIMIZER
  task_type: PIECEWISE_JERK_NONLINEAR_SPEED_OPTIMIZER
  task_type: RSS_DECIDER
}
```

三、apollo规划算法tasks最完整解析

上文提到task组成了在LaneFollowStage状态下完整的决策规划算法，本节将会完整介绍这些task设置的目的，具体执行什么样的任务：

1、LaneChangeDecider

LaneChangeDecider 是lanefollow 场景下，所调用的第一个task，它的作用主要有两点：

- 判断当前是否进行变道，以及变道的状态，并将结果存在变量lane_change_status中；
- 变道过程中将目标车道的reference line放置到首位，变道结束后将当前新车道的reference line放置到首位

流程图如下：



![img](https://pic2.zhimg.com/80/v2-2d9117b12304920f53e861568787cf4d_720w.jpg)

2、PathReuseDecider

PathReuseDecider 是lanefollow 场景下，所调用的第 2 个 task，它的作用主要是换道时：

- 根据横纵向跟踪偏差，来决策是否需要重新规划轨迹；
- 如果横纵向跟踪偏差，则根据上一时刻的轨迹生成当前周期的轨迹，以尽量保持轨迹的一致性

流程如下：



![img](https://pic1.zhimg.com/80/v2-dbff427427b6bb3d99af894859261760_720w.jpg)

3、PathLaneBorrowDecider

PathLaneBorrowDecider 是第3个task，PathLaneBorrowDecider会判断已处于借道场景下判断是否退出避让；判断未处于借道场景下判断是否具备借道能力。

需要满足下面条件才能判断是否可以借道：

- 只有一条参考线，才能借道
- 起点速度小于最大借道允许速度
- 阻塞障碍物必须远离路口
- 阻塞障碍物会一直存在
- 阻塞障碍物与终点位置满足要求
- 为可侧面通过的障碍物

![img](https://pic3.zhimg.com/80/v2-4b0e4a50fd9a2341a0d009f59a3ace9e_720w.jpg)

4、PathBoundsDecider

PathBoundsDecider 是第四个task，PathBoundsDecider根据lane borrow决策器的输出、本车道以及相邻车道的宽度、障碍物的左右边界，来计算path 的boundary，从而将path 搜索的边界缩小，将复杂问题转化为凸空间的搜索问题，方便后续使用QP算法求解。



![img](https://pic2.zhimg.com/80/v2-6cfb00703dda56cc3b44e51bb3d9e331_720w.jpg)

5、**PiecewiseJerkPathOptimizer**

PiecewiseJerkPathOptimizer 是lanefollow 场景下，所调用的第 5 个 task，属于task中的optimizer类别它的作用主要是：

- 1、根据之前decider决策的reference line和 path bound，以及横向约束，将最优路径求解问题转化为二次型规划问题；
- 2、调用osqp库求解最优路径；

![img](https://pic1.zhimg.com/80/v2-c0d7b5eb81fc89390c2cdd1e8839df18_720w.jpg)

6、PathAssessmentDecider

PathAssessmentDecider 是lanefollow 场景下，所调用的第 6 个 task，属于task中的decider 类别它的作用主要是：

- 选出之前规划的备选路径中排序最靠前的路径；
- 添加一些必要信息到路径中

![img](https://pic2.zhimg.com/80/v2-3d76ca7bb6cdd8d43a4c0ae596effba1_720w.jpg)

7、PathDecider

PathDecider 是lanefollow 场景下，所调用的第 7 个 task，属于task中的decider 类别它的作用主要是：

在上一个任务中获得了最优的路径，PathDecider的功能是根据静态障碍物做出自车的决策，对于前方的静态障碍物是忽略、stop还是nudge

![img](https://pic1.zhimg.com/80/v2-3e8d867eb4688c85cb0161bf78fe5158_720w.jpg)

8、RuleBasedStopDecider

RuleBasedStopDecider 是lanefollow 场景下，所调用的第 8 个 task，属于task中的decider 类别它的作用主要是：

- 根据一些规则来设置停止标志。



![img](https://pic1.zhimg.com/80/v2-eeea65782a9010705475f7f220785b88_720w.jpg)

9、SPEED_BOUNDS_PRIORI_DECIDER

SPEED_BOUNDS_PRIORI_DECIDER 是lanefollow 场景下，所调用的第 10 个 task，属于task中的decider 类别它的作用主要是：

（1）将规划路径上障碍物的st bounds 加载到路径对应的st 图上

（2）计算并生成路径上的限速信息

![img](https://pic4.zhimg.com/80/v2-e73f1c8c850808174ddf917f662454a7_720w.jpg)

SPEED_BOUNDS_PRIORI_DECIDER 这个task 是 SpeedBoundsDecider 这个类的对象，使用SPEED_BOUNDS_PRIORI_DECIDER 的config进行初始化，相关代码在 [http://task_factory.cc](https://link.zhihu.com/?target=http%3A//task_factory.cc) 中定义。

![img](https://pic3.zhimg.com/80/v2-a8a4db5cc06aa4e5400e4a6dffeccdd6_720w.jpg)

10、PathTimeHeuristicOptimizer

SPEED_HEURISTIC_OPTIMIZER 是lanefollow 场景下，所调用的第 11个 task，属于task中的optimizer 类别，它的作用主要是：

- apollo中使用动态规划的思路来进行速度规划，其实更类似于使用动态规划的思路进行速度决策；
- 首先将st图进行网格化，然后使用动态规划求解一条最优路径，作为后续进一步速度规划的输入，将问题的求解空间转化为凸空间

代码总流程如下：

- 1、遍历每个障碍物的boundry，判度是否有碰撞风险，如果有碰撞风险使用fallback速度规划；
- 2、初始化cost table
- 3、按照纵向采样点的s，查询各个位置处的限速
- 4、搜索可到达位置
- 5、计算可到达位置的cost
- 6、搜索最优路径

![img](https://pic1.zhimg.com/80/v2-8094082dbc1f592d625deb863d435cb8_720w.jpg)

11、SpeedDecider

SpeedDecider 是lanefollow 场景下，Apollo Planning算法所调用的第12个 task，属于task中的decider 类别它的作用主要是：

- 1、对每个目标进行遍历，分别对每个目标进行决策
- 2、或得mutable_obstacle->path_st_boundary()
- 3、根据障碍物st_boundary的时间与位置的分布，判断是否要忽略
- 4、对于虚拟目标 Virtual obstacle，如果不在referenceline的车道上，则跳过
- 5、如果是行人则决策结果置为stop
- 6、SpeedDecider::GetSTLocation() 获取障碍物在st图上与自车路径的位置关系
- 7、根据不同的STLocation，来对障碍物进行决策
- 8、如果没有纵向决策结果，则置位ignore_decision；

![img](https://pic3.zhimg.com/80/v2-4e2bd60d35f031b13565b2ad79e97b26_720w.jpg)

12、SPEED_BOUNDS_FINAL_DECIDER

SPEED_BOUNDS_FINAL_DECIDER 是lanefollow 场景下，所调用的第 13 个 task，属于task中的decider 类别它的作用主要是：

- （1）将规划路径上障碍物的st bounds 加载到路径对应的st 图上
- （2）计算并生成路径上的限速信息

![img](https://pic3.zhimg.com/80/v2-670f034f115f36dc16be62e6468fe76a_720w.jpg)

13、PiecewiseJerkSpeedOptimizer

PiecewiseJerkSpeedOptimizer 是lanefollow 场景下，所调用的第 14个 task，属于task中的decider 类别它的作用主要是：

- 1、根据之前decider决策的speed decider和 speed bound，以及纵向约束，将最优速度求解问题转化为二次型规划问题；
- 2、调用osqp库求解最优路径；

![img](https://pic4.zhimg.com/80/v2-de69da35129417e9b56969336d890a47_720w.jpg)