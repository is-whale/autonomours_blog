- [【机器人】无人车-运动规划-Apollo6.0-planning模块代码框架梳理(一)--整体结构 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/455162288)

## Apollo6.0 planning模块代码框架梳理(一)--整体结构

## 简介

- 本文是基于Apollo6.0的planning模块代码进行的框架梳理
- planning模块的工程实现主体思想是三分策略：分场景、分阶段、分任务实现。
- 即将运动规划问题，先划分到不同场景中（泊车、左转等），再分阶段执行（减速、观察、转弯等），最后分任务执行。
- Apollo开源框架经过近几年的发展，已经开发了多种方案的规划器，此处采用基于车道线规划的LatticePlanner进行示例解读
- 时间有限，梳理过程难免有一定的不足，欢迎探讨交流

## 代码库结构介绍

### 代码库地址

- 官方网站：[https://apollo.auto/](https://link.zhihu.com/?target=https%3A//apollo.auto/)
- 国外库：[https://github.com/apolloauto](https://link.zhihu.com/?target=https%3A//github.com/apolloauto)
- 国内库：[https://gitee.com/ApolloAuto/apollo](https://link.zhihu.com/?target=https%3A//gitee.com/ApolloAuto/apollo)

### 代码库结构简图



![img](https://pic4.zhimg.com/80/v2-adc432ff8fed2e4e064d3de96f2a9a4b_720w.jpg)



### 代码库结构简单介绍

### 第一级

- 第一层级主要由cyber、modules两个目录构成
- cyber是操作系统的组成部分，实现了节点管理、资源调度、消息通讯等功能
- modules集合了各模块的实现，包括perception、location、planning等

### 第二级

- 在modules中与planning比较相关的是planning、map、common等目录
- planning内部是所有规划（planning）模块的实现代码
- map内部是地图接口相关实现，planning规划时需要调用
- common内部是一些通用代码，包括math函数、proto协议（通信协议的设定）等，属于模块间的共享代码

### 第三级

- 在planning目录内部，都是与planning模块相关的实现
- 其中launch、dag、conf都是与模块启动相关的配置参数，用于模块在cyber系统中的注册
- planning_component.h实现了planning模块的封装，以作为操作系统cyber的组件使用
- on_lane_planning.h则是继承自planning_base.h的基于车道线的planning模块实现框架逻辑
- planner内部实现了继承自planner.h的多种规划器，如lattice_planner、navi_planner等
- 同时planner内部实现了规划器分配器dispatcher，利用工厂模式的设计模式实现规划器的动态调度
- 每个planner的具体实现都在对应的目录下，比如lattice_palnner在planning/lattice目录下实现
- lattice目录下包括behavior目录负责建图相关代码实现、trajectory_generation则是负责轨迹生成、优化、组合评估的框架实现
- scenario目录下负责所有的场景实现和场景管理，用来落地分场景的设计策略
- math目录下负责一些数学计算的代码实现，包括1维曲线生成的计算curve1d等
- constraint_checker与lattice_planner相关的约束检查、碰撞检查的代码实现
- common是planning模块内部的共享代码、如trajectory生成、trajectory拼接相关的代码，主要是不同planner之间的共享
- proto内部包含了planning内部需要的一些协议定义，包括planning_conf.proto这类的模块配置的定义。

### planning模块启动

- 利用cyber系统的luanch命令启动，启动配置取决于启动文件（*.launch)
- 默认启动文件是launch/planning.launch，内部决定了模块间的通讯配置建图文件(*.dag)
- 默认建图文件是dag/planning.dag，内部实现模块节点间的通讯配置，构建DAG(有向无环图)
- dag中再设置了config中的相关配置文件，以实现planning模块更为细致的配置工作

## Planning主流程梳理

### Planning主流程示意图

![img](https://pic3.zhimg.com/80/v2-178027ec1754987845c0a32e0dbd7f2e_720w.jpg)



**说明** 图中的Group-xxxx是对实现某一类相关功能代码的聚拢归类，如Group-Planning是与planning相关代码的统称，以便于理解记忆，并无具体的实现。

### 四大类

### Cyber

- 此处的Cyber类为泛指，表示cyber系统，不是具体的类
- Cyber类负责模块管理、消息通讯、资源分配等工作。
- Cyber中的初始化功能(Init)、定时器功能(Timer)决定了整个planning的工作流水线
- Init代指初始化相关的代码实现，负责触发planning模块的初始化逻辑
- Timer代指定时器相关的代码实现，负责触发planning的消息响应逻辑
- Timer的触发逻辑，与planning模块启动时配置的工作频率有关，Cyber系统负责定时触发回调函数Proc()

### PlanningComponent

- Planning模块的封装类，构建planning组件，在cyber完成注册
- 继承自 cyber::Component 类
- 内部包含planning_base_对象，用于管理planning模块框架

### OnLanePlanning

- 具体的基于车道线的planning框架类，继承自PlanningBase类
- 内部实现了planning模块的整体框架函数
- 内部包含planner_dispatcher_用于规划器的动态分发
- 内部包含planner_用于实现具体的规划工作

### LatticePlanner

> 此处以LatticePlanner为例，此外还有其他规划器可供选择 - LatticePlanner继承自PlannerWithReferenceLine，间接继承自Planner - 横纵分离的规划器，通过ST、SL的规划实现Trajectory的生成工作。 注意：代码库中默认的是PublicRoadPlanner，要想使用LatticePlanner，需要修改启动配置参数，即将modules/planning/conf/planning_config.pb.txt中的planner_type:PUBLIC_ROAD改为planner_type:LATTICE

### 两大流程

### 初始化流程

> launch命令启动，由Cyber的Init触发，简化流程如下，具体流程参看流程图

```cpp
PlanningComponent::Init() //由Cyber系统初始化调用
OnLanePlanning::OnLanePlanning() //在PlanningComponent::Init()中调用，由use_navigation_mode标志位决定
OnLanePlanning::Init() //在PlanningComponent::Init()中调用
LatticePlanner::LatticePlanner() //在OnLanePlanning::init()中间接调用，由PlannerDispatcher::RegisterPlanners()直接调用
LatticePlanner::Init() //在OnLanePlanning::init()中调用
```

### 消息响应流程（运动规划）

> 由Cyber中的Timer触发，简化流程如下，具体流程参看流程图

```cpp
PlanningComponent::Proc() //由Cyber系统定时器调用
OnLanePlanning::RunOnce() //由PlanningComponent::Proc()调用
OnLanePlanning::Plan() //由OnLanePlanning::RunOnce()调用
LatticePlanner::Plan() //由OnLanePlanning::Plan()调用
LatticePlanner::PlanOnReferenceLine() //由LatticePlanner::Plan()调用，具体的基于参考线的规划工作
```

## 结语

planning代码框架结构相对清晰，但是具体细节代码还是比较复杂，因此本文无法做到面面俱到，因此只做了一些简单的框架梳理介绍，用于入门了解，具体代码还需自行阅读源码，后续也会计划做一些针对性的细致介绍。

大致计划如下：

- LatticePlanner规划器PlanOnReferenceLine逻辑梳理
- referenceline平滑轨迹生成逻辑梳理