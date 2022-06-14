- [【Apollo 6.0项目实战】Planning模块_Travis.X的博客-CSDN博客](https://blog.csdn.net/Travis_X/article/details/121793238)

# 前言

环境：

- Ubuntu 20.04
- Apollo 6.0
- LGSVL仿真器

## [Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) 6.0软件框架

![在这里插入图片描述](https://img-blog.csdnimg.cn/66d527b5163d444abd699023053db962.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

- Perception——感知模块识别自动驾驶汽车周围的环境。感知模块内部包含两个重要的子模块：障碍物检测和交通灯检测。
- Prediction——预测模块用来预测与感知障碍物未来的运动轨迹。
- Routing——路由模块告诉自动驾驶汽车通过全局路径到达目的地。
- **Planning——规划模块规划自动驾驶汽车要采取的时空轨迹。**
- Control——控制模块通过产生油门、刹车和转向等控制命令来执行计划的时空轨迹。
- CanBus —— CanBus 是将控制命令传递给车辆硬件的接口。它还将机箱信息传递给软件系统。
- HD-Map——该模块类似于库。它不是发布和订阅消息，而是经常用作查询引擎支持，以提供关于道路的特定结构化信息。
- Localization——定位模块利用各种信息源（如 GPS、LiDAR 和 IMU）来估计自动驾驶汽车的位置。
- HMI——Apollo 中的人机界面或 DreamView 是用于查看车辆状态、测试其他模块和实时控制车辆功能的模块。
- Monitor——车辆中所有模块的监控系统，包括硬件。
- Guardian——新的安全模块，用于干预监控检测到的失败和action center相应的功能。 执行操作中心功能并进行干预的新安全模块应监控检测故障。
- Storytelling——隔离和管理复杂场景的新模块，创建可触发多个模块操作的Story。所有其他模块都可以订阅此特定模块。

本文主要简单介绍下 Apollo 的规划模块、在 LGSVL 环境中实现规划模块的调用以及如何使用 Dreamland 平台进行决策规划算法的测试。

------

# 一、规划模块简介

Apollo规划模块的主要作用是结合障碍物、地图定位以及导航信息为自动驾驶车辆规划一条运动轨迹，这条轨迹由若干轨迹点组成，每个轨迹点均包含了位置坐标、速度、加速度、加加速度、相对时间等信息，这些信息为自动驾驶车辆的运动提供依据，参照规划的轨迹，自动驾驶车辆可以高效、安全、舒适的驶向目的地。

Apollo 规划模块输入输出如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/f124d614a8c7476bbc68f5e1b718e492.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

| channel名称                      | 输入输出 | 说明                   |
| -------------------------------- | -------- | ---------------------- |
| /apollo/canbus/chassis           | 输入     | 车辆底盘反馈信息       |
| /apollo/localization/pose        | 输入     | 车辆定位信息           |
| /apollo/perception/traffic_light | 输入     | 感知红绿灯信息         |
| /apollo/prediction               | 输入     | 预测障碍物信息         |
| /apollo/relative_map             | 输入     | 局部地图信息           |
| /apollo/routing_response         | 输入     | 导航routing信息        |
| /apollo/planning                 | 输出     | 自动驾驶车辆的轨迹信息 |

------

# 二、LGSVL 与 Apollo 6.0 联合仿真

## 2.1 Apollo启动

```cpp
cd apollo/

./docker/scripts/dev_start.sh

./docker/scripts/dev_into.sh

./scripts/bootstrap_lgsvl.sh

./scripts/bridge.sh
```

打开Dreamview http://localhost:8888/，在上方选择对应的模式、车型以及地图（根据自己的仿真环境选择相应的地图）。

## 2.2 启动 LGSVL 仿真器

启动LGSVL仿真器后，Dreamview 打开 Localization 、 Control、 Perception、Prediction、Routing、Planning 以及 Transform 模块，即可观察到可视化结果。图中的蓝绿色粗线代表着车辆规划出的轨迹，可以观察轨迹的状态定性的分析规划问题。

![在这里插入图片描述](https://img-blog.csdnimg.cn/36968639718849ca8ce892cba6c6c934.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/c6acd92e616d460385d806b842d35d71.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/835869af6785454ea337744a2e9180e4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

进一步精细化分析规划问题，可以打开 PNC monitor，界面右侧会显示路径规划和速度规划的相关定量图线，供开发者做进行的分析和问题定位。

![在这里插入图片描述](https://img-blog.csdnimg.cn/60c4db3d29874d9d9df56c5d86991850.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 2.3 视频演示

<iframe id="TnTJrxYN-1638952149878" src="https://player.bilibili.com/player.html?aid=977167682" allowfullscreen="true" data-mediaembed="bilibili" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; font-weight: normal; overflow-wrap: break-word; display: block; width: 660px; height: 330px;"></iframe>

【自动驾驶】Apollo 6.0 与 LGSVL 联合仿真（3）

------

# 三、Dreamland 仿真平台的使用

Dreamland 是 Apollo 基于大量的驾驶场景数据和大规模云计算能力提供打造的仿真引擎，它目前提供了大约200个场景案例、支持同时高效运行多个场景的执行方式、自动评分系统以及三维可视化。具体介绍参考[Dreamland 仿真平台介绍](https://studio.apollo.auto/introduction)。

![在这里插入图片描述](https://img-blog.csdnimg.cn/3dbd08ee92a04a4bb7bebf7c0a404e0e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)



仿真平台服务状态申请中，使用教程后续再进行补充，可以先参考
https://studio.apollo.auto/introduction?locale=zh-cn

------

# 参考

【1】[Apollo规划能力介绍](https://apollo.auto/document_cn.html?target=/Apollo-Homepage-Document/Apollo_Doc_CN_6_0/)