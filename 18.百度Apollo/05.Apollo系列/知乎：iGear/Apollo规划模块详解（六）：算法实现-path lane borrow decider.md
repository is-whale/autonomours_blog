- [Apollo规划模块详解（六）：算法实现-path lane borrow decider - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/441721310)

## 1.概述

借道决策器的主要功能为判断当前车辆是否具备借道能力，其实现在类PathLaneBorrowDecider的成员函数process()中。process()函数的功能一共分为三部分：检查输入、如果路径复用则跳过借道决策。

![img](https://pic4.zhimg.com/80/v2-e9bb1f0521e9f76475fa20008e1fa14f_720w.jpg)

## 2.输入、输出

### 2.1 输入信息

![img](https://pic3.zhimg.com/80/v2-4f2427a472e066b0c9885401d7e42366_720w.png)

### 2.2 输出信息

![img](https://pic4.zhimg.com/80/v2-cada02fa3b9f20917d4eee2ffdb31803_720w.png)

## 3.借道判断IsNecessaryToBorrowLane（）

借道判断主要通过核心函数IsNecessaryToBorrowLane()判断是否借道，主要涉及一些rules，包括距离信号交叉口的距离，与静态障碍物的距离，是否是单行道，是否所在车道左右车道线是虚线等规则。主要有两个功能：

1.已处于借道场景下判断是否退出避让；

2.未处于借道场景下判断是否具备借道能力。

### 3.1 已处于借道场景下

已处于避让场景下，根据数值优化求解轨迹后的信息计算是否退出借道场景（如：避让输出轨迹无解时退出借道），滤波周期为6，条件满足后is_path_lane_borrow_标志、is_in_path_lane_borrow_scenario_标志均为false。

![img](https://pic4.zhimg.com/80/v2-dcba3917e8d4c8b25d4736fcf0b3c703_720w.jpg)

### 3.2 未处于借道场景下

在车辆未借道时输出车辆当前的借道状态:左借道、右借道 、不借道。

**1）避让约束条件如下:**

![img](https://pic3.zhimg.com/80/v2-baf250593b5638db72c5194429abda9a_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-e551f24b2f0e51e2e5a659961e1ee7a8_720w.jpg)

**2）避让方向**

在无避让方向时重新计算避让方向，若左、右借道空间均不满足则不借道，is_in_path_lane_borrow_scenario_标志为false；左借道条件满足左借道、右借道条件满足右借道，is_in_path_lane_borrow_scenario_为true。

![img](https://pic2.zhimg.com/80/v2-a0040bd2e60bc71317004ac21ebdede9_720w.jpg)

## 4. 重要子函数

### 4.1 CheckLaneBorrow（）

在此函数中2m间隔一个点遍历车前100m参考线或全部参考线，如果车道线类型为黄实线、白实线则不借道。

![img](https://pic4.zhimg.com/80/v2-da2d30069cfe9a12abaa2c30668bfb77_720w.jpg)

### 4.2 IsSidePassableObstacle（）

IsSidePassableObstacle（）的实现主要在IsNonmovableObstacle（）函数中实现。

IsNonmovableObstacle（）函数中障碍物距离车辆很远不借道、前方障碍物停车借道、障碍物是被其他障碍物阻塞不借道

![img](https://pic2.zhimg.com/80/v2-02c688107157362fcd6408d27e0cacad_720w.jpg)

## 5. 总结

经过借道决策模块计算输出的借道能力仅表示为当前车辆有借道需求且满足借道条件，但是否真的借道仍需要下游的路径生成、路径选优决定。