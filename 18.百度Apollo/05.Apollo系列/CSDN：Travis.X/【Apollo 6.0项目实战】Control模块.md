- [【Apollo 6.0项目实战】Control模块_Travis.X的博客-CSDN博客](https://blog.csdn.net/Travis_X/article/details/121802955)

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
- Planning——规划模块规划自动驾驶汽车要采取的时空轨迹。
- **Control——控制模块通过产生油门、刹车和转向等控制命令来执行计划的时空轨迹。**
- CanBus —— CanBus 是将控制命令传递给车辆硬件的接口。它还将机箱信息传递给软件系统。
- HD-Map——该模块类似于库。它不是发布和订阅消息，而是经常用作查询引擎支持，以提供关于道路的特定结构化信息。
- Localization——定位模块利用各种信息源（如 GPS、LiDAR 和 IMU）来估计自动驾驶汽车的位置。
- HMI——Apollo 中的人机界面或 DreamView 是用于查看车辆状态、测试其他模块和实时控制车辆功能的模块。
- Monitor——车辆中所有模块的监控系统，包括硬件。
- Guardian——新的安全模块，用于干预监控检测到的失败和action center相应的功能。 执行操作中心功能并进行干预的新安全模块应监控检测故障。
- Storytelling——隔离和管理复杂场景的新模块，创建可触发多个模块操作的Story。所有其他模块都可以订阅此特定模块。

本文讲解的是 Apollo 中的 Control 模块。主要目的是简单了解下Apollo 控制模块的基本知识、控制模块的组成和输入输出、调试工具 plot_control 和 realtime_test 的使用。

------

# 一、 Control模块简介

控制模块是整个自动驾驶软件系统中的执行环节，控制模块的目标是基于规划模块输出的目标轨迹和定位模块输出的车辆状态生成方向盘、油门、刹车控制命令，并通过 canbus 模块给车辆底层执行器。简单而言，就是告诉车辆该打多大方向盘、多大的油门开度、多大的刹车制动力。

控制模块由两个子模块组成：**横向控制模块**和**纵向控制模块**。纵向控制模块通过有门和刹车控制车的纵向加减速，横向控制模块是通过控制方向盘的转动来控制前轮的转向，从而控制汽车的行驶方向。

## 1.1 横向控制模块

**横向控制根据规划模块的轨迹生成方向盘指令。**

**横向控制器是基于LQR的最优控制器。**

横向控制模块如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/128ad652d8a94361998461842f75e6d3.png)

- 方向盘闭环：通过上游规划模块输出的目标方向盘角度和下游 canbus 模块返回的实际方向盘角度做差，作为控制器的方向盘闭环输出。
- 方向盘开环：通过上游规划模块输出的目标道路曲率经过转换，输出控制器方向盘开环输出。
- 车辆：是控制器的控制对象，通过方向盘开环、方向盘闭环生成的方向盘角度打方向盘。

## 1.2 纵向控制模块

**纵向模块根据规划模块的轨迹生成油门、刹车指令。**

**纵向控制配置为级联PID+校准表。**

纵向控制模块如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/b9854f3eba7f41328cf4c2ef965e4998.png)

- 位置闭环：通过规划模块的目标车辆位置和定位模块给出的实际车辆位置做差，经过纵向位置控制器生成对应的速度偏差。
- 速度闭环：结合位置闭环的结果，规划模块的目标速度以及 canbus 模块返回的实际车速，通过纵向速度控制器生成对应的加速度偏差。
- 标定表：速度，加速度和油门、刹车对应关系表，通过输入速度、加速度可以查出对应的油门、刹车。

控制模块的输入输出如下图所示。
![在这里插入图片描述](https://img-blog.csdnimg.cn/8192b3cbcc2c4d4985a121e65e4cae5e.png)

| channel名称               | 输入输出 | 说明                   |
| ------------------------- | -------- | ---------------------- |
| /apollo/canbus/chassis    | 输入     | 车辆底盘反馈信息       |
| /apollo/localization/pose | 输入     | 车辆定位信息           |
| /apollo/planning          | 输入     | 自动驾驶车辆的轨迹信息 |
| /apollo/control           | 输出     | 车辆底盘控制信息       |

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

启动LGSVL仿真器后，Dreamview 打开 Localization 、 Control、 Perception、Prediction、Routing、Planning 以及 Transform 模块，即可观察到可视化结果。打开 PNC monitor 可以对轨迹和速度曲线进行定量分析。
![在这里插入图片描述](https://img-blog.csdnimg.cn/a4ae56297e4c424bbbe194b2d7b89e8b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 3.3 plot_control 调试工具

plot_control 调试工具可以将控制模块输出的油门刹车转向参数以图表的形式显示出来。

```cpp
bash modules/tools/plot_control/run.sh
```

可能出现以下警告提示：

> UserWarning: Matplotlib is currently using agg, which is a non-GUI backend, so cannot show the figure.
> plt.show()

解决方法：

```cpp
sudo apt-get update
sudo apt-get install tcl-dev tk-dev python3-tk
```

再重新执行 bash modules/tools/plot_control/run.sh，开始订阅控制消息，按 CTRL + c 结束订阅，会显示这一段时间内油门刹车转向随时间的变化关系，如下图所示（只有在结束后才会弹出下面的图表）。

![在这里插入图片描述](https://img-blog.csdnimg.cn/0a53888d6553411b8f516c3f2c0c59f2.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 3.4 realtime_test 调试工具

realtime_test调试工具用来设定车辆的加速度阈值，当车辆加速度超过该阈值时会在终端会依次打印出当前的时间戳、平均车速、当前车辆加速度、回调函数序号、加速度阈值。

```cpp
./bazel-bin/modules/tools/plot_control/realtime_test --acc 1
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/ba43dfb802694b1bb6b89ff36d1a850b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 四、演示视频

<iframe id="Z3W1oHGy-1638797009334" src="https://player.bilibili.com/player.html?aid=977167682" allowfullscreen="true" data-mediaembed="bilibili" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; font-weight: normal; overflow-wrap: break-word; display: block; width: 660px; height: 330px;"></iframe>

【自动驾驶】Apollo 6.0 与 LGSVL 联合仿真（3）

------

# 参考

【1】[Apollo 控制能力介绍](https://apollo.auto/document_cn.html?target=/Apollo-Homepage-Document/Apollo_Doc_CN_6_0/)
【2】[Apollo详解之常用工具](https://blog.csdn.net/weixin_49024732/article/details/118879205?spm=1001.2101.3001.6650.10&utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~default-10.highlightwordscore&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~default-10.highlightwordscore)