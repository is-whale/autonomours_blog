- [【Apollo 6.0项目实战】Routing模块_Travis.X的博客-CSDN博客_apollo routing](https://blog.csdn.net/Travis_X/article/details/121674874)

# 前言

环境：

- Ubuntu 20.04
- Apollo 6.0
- LGSVL仿真器

## [Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) 6.0软件框架

![在这里插入图片描述](https://img-blog.csdnimg.cn/66d527b5163d444abd699023053db962.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

- Perception——感知模块识别自动驾驶汽车周围的环境。感知模块内部包含两个重要的子模块：障碍物检测和交通灯检测。
- Prediction——预测模块用来预测与感知障碍物未来的运动轨迹。
- **Routing——路由模块告诉自动驾驶汽车通过全局路径到达目的地。**
- Planning——规划模块规划自动驾驶汽车要采取的时空轨迹。
- Control——控制模块通过产生油门、刹车和转向等控制命令来执行计划的时空轨迹。
- CanBus —— CanBus 是将控制命令传递给车辆硬件的接口。它还将机箱信息传递给软件系统。
- HD-Map——该模块类似于库。它不是发布和订阅消息，而是经常用作查询引擎支持，以提供关于道路的特定结构化信息。
- Localization——定位模块利用各种信息源（如 GPS、LiDAR 和 IMU）来估计自动驾驶汽车的位置。
- HMI——Apollo 中的人机界面或 DreamView 是用于查看车辆状态、测试其他模块和实时控制车辆功能的模块。
- Monitor——车辆中所有模块的监控系统，包括硬件。
- Guardian——新的安全模块，用于干预监控检测到的失败和action center相应的功能。 执行操作中心功能并进行干预的新安全模块应监控检测故障。
- Storytelling——隔离和管理复杂场景的新模块，创建可触发多个模块操作的Story。所有其他模块都可以订阅此特定模块。

本文讲解的是在 LGSVL [仿真器](https://so.csdn.net/so/search?q=仿真器&spm=1001.2101.3001.7020)环境中生成路由信息。

------

# 一、 Routing 模块简介

Routing 模块的作用是从拓扑地图中规划出一条从起点到终点的全局路径。

- 模块输入：
  - 地图数据
  - 请求，包括：开始和结束位置
- 模块输出：
  - 路由导航信息

Routing 模块是依赖于路由拓扑文件，通常称为 Apollo 中的 routing_map.*。Routing 地图可以通过命令来生成。

```cpp
bash scripts/generate_routing_topo_graph.sh
```

**注意**：关于 Routing 地图的制作可以参考该节 [【Apollo 6.0项目实战】HD-Map模块](https://blog.csdn.net/Travis_X/article/details/121486163)。

Routing 模块的实现结构如下图所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/66e0c3efbcd243b3891253f51e280d81.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

------

# 二、LGSVL 与 Apollo 6.0 联合仿真

## 1. Apollo启动

```cpp
cd apollo/

./docker/scripts/dev_start.sh

./docker/scripts/dev_into.sh

./scripts/bootstrap_lgsvl.sh

./scripts/bridge.sh
```

打开Dreamview http://localhost:8888/，在上方选择对应的模式、车型以及地图（根据自己的仿真环境选择相应的地图）。

## 2. 启动 LGSVL仿真器

启动 LGSVL 仿真器后，打开 Dreamview 的左侧栏 Module Controller，启动 Localization、Routing 和 Transform 模块。

正常情况下，可以看到车辆和地图在 Dreamview 中的显示，点击左侧栏 Route Editing 选择目标点，最后目标点发送请求，即可观察到可视化结果，红线即是 Routing 模块规划出全局路径。

![在这里插入图片描述](https://img-blog.csdnimg.cn/5604956be5734423964aa7acb72463c5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/89f1d376f08540fcbb683640b58efdc6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 三、演示视频

<iframe id="Z3W1oHGy-1638797009334" src="https://player.bilibili.com/player.html?aid=977167682" allowfullscreen="true" data-mediaembed="bilibili" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; font-weight: normal; overflow-wrap: break-word; display: block; width: 660px; height: 330px;"></iframe>

【自动驾驶】Apollo 6.0 与 LGSVL 联合仿真（3）

------

# 参考

【1】[Apollo3.5:基于拓扑地图TopoGraph的Routing模块A*算法路径导航](https://blog.csdn.net/weixin_44809980/article/details/107929643?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-1.opensearchhbase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-1.opensearchhbase)