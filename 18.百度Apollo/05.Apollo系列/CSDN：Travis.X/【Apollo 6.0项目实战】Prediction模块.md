- [【Apollo 6.0项目实战】Prediction模块_Travis.X的博客-CSDN博客_apollo预测模块](https://blog.csdn.net/Travis_X/article/details/121658184)

# 前言

环境：

- Ubuntu 20.04
- Apollo 6.0
- LGSVL仿真器

## [Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) 6.0软件框架

![在这里插入图片描述](https://img-blog.csdnimg.cn/66d527b5163d444abd699023053db962.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

- Perception——感知模块识别自动驾驶汽车周围的环境。感知模块内部包含两个重要的子模块：障碍物检测和交通灯检测。
- **Prediction——预测模块用来预测与感知障碍物未来的运动轨迹。**
- Routing——路由模块告诉自动驾驶汽车通过全局路径到达目的地。
- Planning——规划模块规划自动驾驶汽车要采取的时空轨迹。
- Control——控制模块通过产生油门、刹车和转向等控制命令来执行计划的时空轨迹。
- CanBus —— CanBus 是将控制命令传递给车辆硬件的接口。它还将机箱信息传递给软件系统。
- HD-Map——该模块类似于库。它不是发布和订阅消息，而是经常用作查询引擎支持，以提供关于道路的特定结构化信息。
- Localization——定位模块利用各种信息源（如 GPS、LiDAR 和 IMU）来估计自动驾驶汽车的位置。
- HMI ——Apollo 中的人机界面或 DreamView 是用于查看车辆状态、测试其他模块和实时控制车辆功能的模块。
- Monitor——车辆中所有模块的监控系统，包括硬件。
- Guardian——新的安全模块，用于干预监控检测到的失败和action center相应的功能。 执行操作中心功能并进行干预的新安全模块应监控检测故障。
- Storytelling——隔离和管理复杂场景的新模块，创建可触发多个模块操作的Story。所有其他模块都可以订阅此特定模块。

本文讲解的是在 LGSVL [仿真器](https://so.csdn.net/so/search?q=仿真器&spm=1001.2101.3001.7020)中测试自动驾驶车辆的预测能力。

------

# 一、 Prediction模块简介

预测模块通过障碍物的历史状态信息，来预测障碍物的未来轨迹。感知模块作为预测模块的上游，提供障碍物的位置、朝向、速度、加速度等信息，预测模块根据这些信息，给出障碍物未来的预测轨迹，供下游规划模块进行自车轨迹的规划。

## 1.1 预测模块的原理

预测模块主要有四个子模块组成，分别是：**信息容器**（container）、**场景选择**（scenario）、**评估器**（evaluator）和**预测器**（predictor）。

- **信息容器**：储存上游信息，为之后的轨迹预测提供输入，当期储存的主要有：感知信息、定位信息以及自车轨迹规划信息。
- **场景选择**：预测模块针对不同的场景采用不同的预测方法（如巡航、路口等场景），便于后续扩展，提高算法的泛化能力。
- **评估器**：评估器基于障碍物的状态信息，结合预测模型，给出障碍物预测轨迹的概率或短预测时域的轨迹信息。
- **预测器**：预测器直接或结合评估器的结果给出障碍物的完整预测时域的预测轨迹。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/8408f7eebeca4866acafe78d8a8ee9ac.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 1.2 预测模块的输入输出

![在这里插入图片描述](https://img-blog.csdnimg.cn/5540a485b2c04ff2836c993342dd3720.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

------

# 二、LGSVL 与 Apollo 6.0 联合仿真

根据上图中预测模块的输入输出配置相关的传感器，例如GPS、IMU、3D Ground Truth Sensor、Signal Sensor等等。其中 3D Ground Truth Sensor 和 Signal Sensor 传感器可以完全绕过 Apollo 的感知模块，获取到红绿灯检测的信息和障碍物信息。 因此，没必要给车辆添加激光雷达、相机和毫米波雷达等传感器。可以参考关于感知模块的另外一篇文章[【Apollo 6.0项目实战】Perception模块](https://blog.csdn.net/Travis_X/article/details/121518854)中的第三节。

## 1. Apollo启动

```cpp
cd apollo/

./docker/scripts/dev_start.sh

./docker/scripts/dev_into.sh

./scripts/bootstrap_lgsvl.sh

./scripts/bridge.sh
```

打开Dreamview http://localhost:8888/，在上方选择对应的模式、车型以及地图（根据自己的仿真环境选择相应的地图）。

## 2. 启动 LGSVL 仿真器

Dreamview 打开prediction 按钮，即可观察到可视化结果。图中的绿色线代表预测障碍物未来的运动轨迹。
![在这里插入图片描述](https://img-blog.csdnimg.cn/9b2eacf9b5b64a2fac30edbbec3a9945.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 参考

【1】[apollo预测模块分享（二十一）](https://zhuanlan.zhihu.com/p/367557601)
【2】[Apollo预测能力介绍](https://apollo.auto/document_cn.html?target=/Apollo-Homepage-Document/Apollo_Doc_CN_6_0/)