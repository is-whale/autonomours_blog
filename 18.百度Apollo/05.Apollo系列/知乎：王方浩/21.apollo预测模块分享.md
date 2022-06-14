- [apollo预测模块分享（二十一） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/367557601)

本文主要是对周末分享的整理，主要介绍了Apollo预测模块的整体流程，以及预测模块是如何 工作的，最后预测模块的调试技巧。

## 预测模块主流程

学习Apollo预测模块之前，首先要了解的就是预测模块的整体流程。预测模块包括以下3大主流程：**消息主流程、数据主流程和代码主流程**。搞清楚了上述3大流程也就清楚了预测模块的整体结构，理解起来也就事半功倍。

**数据主流程**

首先介绍的是消息主流程，也就是说预测模块接收什么消息，输出什么消息，从下图可以看出，预测模块接收感知到的障碍物消息，然后结合自身的位置和规划信息，输出的结果是**障碍物未来一小段时间的预测轨迹**。

![img](https://pic2.zhimg.com/80/v2-be0f8ab5317f752d05c03d76ffc80849_720w.jpg)

**数据主流程**

接着介绍数据主流程，数据主流程的作用主要是为了帮助训练，例如在线无人车预测的数据，可以保存下来给离线使用。同时在调试的时候也可以通过跑离线任务来定位问题。

![img](https://pic2.zhimg.com/80/v2-f57b7c2bb5f954187bce380c48e72f55_720w.jpg)

**代码主流程**

最后介绍的是代码主流程，预测模块的代码相对比较简单，主流程在"[http://message_process.cc](https://link.zhihu.com/?target=http%3A//message_process.cc)"中，然后通过评估器和预测器来输出最后的预测结果。其中不同的障碍物类型对应不同的评估器和预测器。

![img](https://pic3.zhimg.com/80/v2-81a635e3e017d8d358c5bc27137e18da_720w.jpg)



## 3个容器类型

介绍完3大流程，接着我们开始介绍主要的数据结构，实际上搞清楚了预测模块的3个容器，可以很大帮助我们理解预测模块。这3个容器分别是：

- **pose_container （自动驾驶车辆当前的位置信息）**
- **adc_trajectory_container （自动驾驶车辆规划的轨迹信息）**
- **obstacles_container （感知到的障碍物信息）**



**pose_container**

首先介绍pose_container，pose_container相对比较简单，主要是保持自动驾驶车自身的位置和速度信息。也就是说后面预测的时候也会把障碍物和自身车辆同时进行考虑。

![img](https://pic3.zhimg.com/80/v2-3cdb2c3e6109e25dde0a6c00c5db99ea_720w.jpg)



**ADCTrajectoryContainer**

接着介绍ADCTrajectoryContainer，ADCTrajectoryContainer主要保存自动驾驶车规划好的轨迹，也就是说自动驾驶汽车自身的驾驶意图。和pose_container一样是用来考虑障碍物和自动驾驶汽车本身的交互。

![img](https://pic2.zhimg.com/80/v2-737ae2e1e88702c5fd19466bc4b611ad_720w.jpg)



**ObstaclesContainer**

最后介绍ObstaclesContainer，ObstaclesContainer主要用来保存障碍物信息，采用了一个LRUcache来保持障碍物，并且定期淘汰过期的障碍物。同时还会对障碍物进行聚类，找出同一条车道上的所有障碍物，组成障碍物簇。

![img](https://pic4.zhimg.com/80/v2-2b87071f65614d02fe7c16acb7c82dc3_720w.jpg)



**RoadGraph**

这里补充一点**路网（RoadGraph）**的信息，因为在添加障碍物的时候，会计算出当前障碍物可能的行驶路径，也就是说未来障碍物有几种可能的驾驶路径选择，这通过路网（RoadGraph）来实现。下面是一个简单的例子，障碍物有2种路径选择，一种是直行，对应的车道分别是：l20,l98,l95；一种是右拐，对应的车道分别是：l20,l31,l29。

![img](https://pic3.zhimg.com/80/v2-09b37e75f6580534937ac4bb9c008c66_720w.jpg)



## 评估器和预测器

接下来我们就开始介绍正题了，预测模块根据不同的障碍物类型选择不同的评估器和预测器。同时还根据障碍物在不在车道上，也会选择不同的评估器件和预测器，下面是整体的框图。

![img](https://pic3.zhimg.com/80/v2-5b671d353dcc46b0fb25e55d8a35deb2_720w.jpg)



我们首先介绍评估器，一共有以下几种评估器，大部分都是深度学习模型，有少量的基于规则的模型。

- **cyclist_keep_lane_evaluator**
- **pedestrian_interaction_evaluator**
- **mlp_evaluator**
- **cruise_mlp_evaluator**
- **junction_mlp_evaluator**
- **junction_map_evaluator**

首先介绍的是单车在路上的评估器，该评估器是基于规则的模型，只要当前的自行车在之前生成好的车道序列（自行车可能的行驶路径）上，那么就认为单车会保持当前车道。下图可以看出单车有2种选择，一种是左拐，一种是直行，那么这2个路径的概率都是1,之后会把结果交给预测器去选择。

> 在车道选择比较多的情况下，生成好的车道序列最多限制大小为4条，本车道最多选择2条，相邻车道最多选择2条。

![img](https://pic3.zhimg.com/80/v2-6ce436793a92110b747328d8f94d1b5a_720w.jpg)

接下来介绍行人，行人的预测采用的是LSTM的模型，最多预测2s的轨迹，行人没有区分在不在路上，默认为行人是一个自由移动的模型。

![img](https://pic4.zhimg.com/80/v2-be6ec2590af672f760b0da3177ae48cb_720w.jpg)

此外还有在车道上的UNKNOW类型，用的是mlp_evaluator模型。它是一个神经网络，输入的特征主要是障碍物的轨迹和车道的误差（位置、夹角、速度等）来判断车辆拐弯的概率和直行的概率。

![img](https://pic4.zhimg.com/80/v2-6fa324186e36453244087d8df2b41d47_720w.jpg)

然后是在路上的车辆模型，在路上的车辆模型也是神经网络，和mlp_evaluator的区别不是很大，主要是输入特征的选取有细微差别。

![img](https://pic1.zhimg.com/80/v2-892e20635207f078659062669dc46e1c_720w.jpg)

最后介绍路口的评估器，路口的情况比较复杂，这里把路口分为12等分，然后预测障碍物可能的出口，同时还要考虑车辆和本车之间路径是否有冲突，最后得出预测的结果。

![img](https://pic3.zhimg.com/80/v2-6d65d5f91a6259563666a66fa4f8daca_720w.jpg)

介绍完评估器接下来我们开始介绍预测器。

一共有以下几种预测器，预测器最后的输出是一系列的轨迹点。

- **move_sequence_predictor**
- **interaction_predictor**
- **lane_sequence_predictor**
- **free_move_predictor**



**free_move_predictor**

首先介绍free_move_predictor，free_move_predictor是自由移动评估器，主要是预测不在路上的所有类型的障碍物以及行人，之前行人通过pedestrian_interaction_evaluator评估之后，只有2s的预测轨迹，后面会通过free_move_predictor补充6s的轨迹，一共预测8s。free_move_predictor是通过当前的速度和朝向拟合的8s轨迹，因此变化非常大，表现为预测线会甩来甩去的。

![img](https://pic4.zhimg.com/80/v2-7b5fddf0bb57ffba68cbd49ad3b0494f_720w.jpg)

**lane_sequence_predictor**

lane_sequence_predictor会根据评估的车道，过滤一些不太可能的车道，然后再选择一条曲率最低的路径。

![img](https://pic3.zhimg.com/80/v2-45258fa9223cf0f3dcac46ffa40b1a26_720w.jpg)

**move_sequence_predictor**

move_sequence_predictor和lane_sequence_predictor有轻微不同，主要不同在生成轨迹的部分(DrawMoveSequenceTrajectoryPoints)，进行了多项式拟合EvaluateQuarticPolynomial，不知道是不是刚体的原因？

> 路口的预测器这里暂时遗留没有分析



## 调试技巧

Apollo的预测模块有2种调试方式，一种是离线模式，另外一种是跑测试用例。

![img](https://pic3.zhimg.com/80/v2-684c5fce149c6f610c368086a300ee9a_720w.jpg)

离线模式的打开方式如下

![img](https://pic1.zhimg.com/80/v2-f5f7507fa3b669a17cceec2e3633b54c_720w.jpg)

离线模型的结果是一些特征和预测的信息，可以帮助我们分析。

![img](https://pic2.zhimg.com/80/v2-bc2a272ab7ad377f5e81e65bbbea3009_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-532036e930d3f52a3d84e2dfaa8eb648_720w.jpg)

**测试用例**

预测用到的地图是kml_map.bin，下面是可视化的结果。

![img](https://pic2.zhimg.com/80/v2-4ab25c60d35305825468839d0c1ee1a1_720w.jpg)

然后就可以结合地图和每个评估器、预测器的测试用例来熟悉和测试它们的功能。

![img](https://pic1.zhimg.com/80/v2-1f15863a48509496541432027505867c_720w.jpg)



## 最后

以上就是预测模块的介绍和分享，还遗留的主要问题是路口的预测器没有进行深入分析，同时对神经网络的部分也没有进行训练和重构，后面有时间会做进一步的学习。希望对大家的学习有帮助！