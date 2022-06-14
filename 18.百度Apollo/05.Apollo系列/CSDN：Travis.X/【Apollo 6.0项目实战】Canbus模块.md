- [【Apollo 6.0项目实战】Canbus模块_Travis.X的博客-CSDN博客_canbus](https://blog.csdn.net/Travis_X/article/details/121539973)

# 前言

环境：

- Ubuntu 20.04
- Apollo 6.0
- LGSVL仿真器

[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) 6.0安装教程参考[【Apollo 6.0项目实战】Apollo 6.0安装](https://blog.csdn.net/Travis_X/article/details/120947607)

LGSVL仿真器安装配置教程参考[【Apollo 6.项目实战】LGSVL 与 Apollo6.0联合仿真教程](https://editor.csdn.net/md/?articleId=121269630)

## Apollo 6.0软件框架

![在这里插入图片描述](https://img-blog.csdnimg.cn/66d527b5163d444abd699023053db962.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

- Perception——感知模块识别自动驾驶汽车周围的环境。感知模块内部包含两个重要的子模块：障碍物检测和交通灯检测。
- Prediction——预测模块用来预测与感知障碍物未来的运动轨迹。
- Routing——路由模块告诉自动驾驶汽车通过全局路径到达目的地。
- Planning——规划模块规划自动驾驶汽车要采取的时空轨迹。
- Control——控制模块通过产生油门、刹车和转向等控制命令来执行计划的时空轨迹。
- **CanBus —— CanBus 是将控制命令传递给车辆硬件的接口。它还将机箱信息传递给软件系统**。
- HD-Map——该模块类似于库。它不是发布和订阅消息，而是经常用作查询引擎支持，以提供关于道路的特定结构化信息。
- Localization——定位模块利用各种信息源（如 GPS、LiDAR 和 IMU）来估计自动驾驶汽车的位置。
- HMI ——Apollo 中的人机界面或 DreamView 是用于查看车辆状态、测试其他模块和实时控制车辆功能的模块。
- Monitor——车辆中所有模块的监控系统，包括硬件。
- Guardian——新的安全模块，用于干预监控检测到的失败和action center相应的功能。 执行操作中心功能并进行干预的新安全模块应监控检测故障。
- Storytelling——隔离和管理复杂场景的新模块，创建可触发多个模块操作的Story。所有其他模块都可以订阅此特定模块。

本文讲解的是在 LGSVL 仿真环境中，如何使用Apllo提供的底盘调试工具对车辆底盘进行调试。主要的目的是了解下 canbus 模块、canbus_teleop 、canbus_tester 以及 cyber_monitor工具的使用。

------

# 一、Canbus模块简介

Canbus模块，其实是Chassis底盘控制模块，其主要的作用是反馈车辆当前的状态(速度、航向、yaw_rate等信息)，并且发送控制命令到线控底盘，其底层实现基于 “drivers/canbus” 驱动模块。

Canbus模块是车和[自动驾驶](https://so.csdn.net/so/search?q=自动驾驶&spm=1001.2101.3001.7020)软件之间的桥梁，通过canbus驱动(drivers/canbus)来实现将车身信息发送给apollo上层软件，同时接收控制命令，发送给汽车线控底盘实现对汽车的控制。

关于Canbus模块的详细介绍可参考
[apollo介绍之Canbus模块(八)](https://zhuanlan.zhihu.com/p/85083829)
[Apollo之Canbus模块学习总结](https://blog.csdn.net/biaonuan6783/article/details/107357483?spm=1001.2014.3001.5501)
[Apollo CANBUS模块解析](http://kublaikhangeek.github.io/2019/04/19/Apollo-CANBUS模块解析/)

------

# 二、teleop 测试工具介绍

底盘联调测试就是通过将Apollo与车辆进行canbus通信后，测试Apollo下发控制信号（如加速/减速/转向/使能等）是否能够准确控制车辆，测试车辆的底盘反馈信号（如当前踏板百分比反馈/当前转角反馈/使能反馈/接管反馈等）是否与反馈了车辆的实际状态，验证Apollo下发的控制指令，车辆底盘能够准确执行。

apollo 里为开发者提供了一个teleop的测试工具，在apollo/modules/canbus/tools/teleop.cc，在terminal内输入

```cpp
./scripts/canbus_teleop.sh
```

显示如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/679a396da4324e18aa1e15faf76ea550.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

| 键位                    | 功能                                                         |
| ----------------------- | ------------------------------------------------------------ |
| h                       | 可以调出上图所示的帮助界面，可以**查询Teleop工具**的使用方法。 |
| Set Action: [m] + Num   | **执行Apollo对车辆的使能控制**：按m和0键组合，表示执行reset指令，车辆退出自动驾驶模式； 按m和1键组合，表示执行start指令，车辆进入自动驾驶模式。 |
| Set Gear : [G] + Num    | **设置档位**：按g和数字组合，进行相应档位设置：按g+0挂入N档（空挡）； 按g+1挂入D档（前进挡）； 按g+2挂入R档（倒车档）； 按g+3挂入P档（驻车档）。 |
| Throttle/Speed up: [w]  | **表示每次增加油门踏板量2%，车辆加速**：按 w 键增加油门踏板2%，使车辆加速。如果当前已经执行brake刹车指令，按w表示减少刹车踏板 |
| Set Throttle: [T] + Num | **设置油门踏板百分比**：按t+数字可以直接设置具体的油门踏板百分比，油门踏板可设置的百分比数为0~100。如执行t20，表示直接设置当前油门踏板量为20%，并将刹车踏板百分比置为0，这一点与实际开车逻辑一致，如果踩下油门踏板，就不能踩刹车踏板。 |
| Brake/Speed down: [S]   | **车辆减速**：按s键增加刹车踏板百分比，使车辆减速。如当前已经执行throttle加速指令，按s键表示减少油门踏板百分比。 |
| Set Brake: [B] + Num    | **设置刹车踏板百分比**：按b+数字可以直接设置具体的刹车踏板百分比，刹车踏板可设置的百分比数为0~100。如执行b20，表示直接设置当前刹车踏板量为20%，并将油门踏板百分比置为0，这一点与实际开车逻辑一致，如果踩下刹车踏板，就不能踩油门踏板。 |
| Steer LEFT: [A]         | **方向盘每次向左转2%**：按a键表示每次向左转2%的方向盘最大转角，具体转动角度应根据车辆设置的最大方向盘转角乘以2%进行换算。 该指令执行可以在车辆静止时执行，也可以在车辆启动后执行。 |
| Parking Brake: [P]      | **打开电子手刹**：按P键（注意是大写P）可以手动控制车辆电子手刹开关。这个功能根据车辆的是否提供了电子手刹的控制接口而实现。注意：执行电子手刹开启或释放时，请将车辆用teleop设置为P档状态。 |
| Emergency Stop: [E]     | **紧急停车**：按E键（注意是大写E）可以进行车辆紧急停车，默认执行50%刹车。 建议开发者在测试时尽量少用此功能，体感差，调试车辆时多注意周围情况。发生突发情况时及时用外接踩刹车踏板的方式进行手动接管车辆。 |

------

# 三、tester 测试工具介绍

tester 测试 canbus 模块用的数据，能发送指定的控制消息。

运行

```cpp
./scripts/canbus_tester.sh
```

可能会出现的错误

> F1126 11:52:40.193882 60034 utilities.cc:346] Check failed: !IsGoogleLoggingInitialized() You called InitGoogleLogging() twice!
> *** Check failure stack trace: ***
> ./scripts/canbus_tester.sh: line 24: 60034 Aborted (core dumped) ./bazel-bin/modules/canbus/tools/canbus_tester --canbus_test_file=modules/canbus/testdata/canbus_test.pb.txt

解决方法：看[这里](https://github.com/ApolloAuto/apollo/pull/13931/commits/d50a6de8cdffcfb3d39c36170eb8dda3d752c0c4)，修改modules/canbus/tools/canbus_tester.cc，注释掉 google::InitGoogleLogging(argv[0]); 这一行，再重新编译和输入上述指令即可。

------

# 四、诊断工具介绍

cyber_monitor提供了终端中实时显示channel信息列表的功能。了解了 teleop 测试工具的基本操作后，开发者根据相应的指令，对车辆执行具体的控制命令，然后通过Apollo的可视化监控工具cyber_monitor进行查看车辆当前的反馈信号，确认控制下发后车辆的执行结果是否正确。

进入Apollo Docker环境后执行如下命令运行cyber_monitor。

```cpp
cyber_monitor
```

在apollo/modules/canbus/conf/canbus.conf文件内： 修改配置–noenable_chassis_detail_pub为–enable_chassis_detail_pub，表示在打开chassis_detail底盘详细信息，即可以查看底盘反馈信号的每一帧报文原始信息。 修改配置–receive_guardian为–noreceive_guardian，即可以关闭guardian模式，进入canbus的调试模式，这样teleop时就能够控制车辆了。如下图所示是修改canbus_conf配置文件。

> –flagfile=/apollo/modules/common/data/global_flagfile.txt
> –canbus_conf_file=/apollo/modules/canbus/conf/canbus_conf.pb.txt
> –enable_chassis_detail_pub
> –receive_guardian

通过方向键选择/apollo/canbus/chassis的Channel按回车键，即可查看底盘的控制信息。
![在这里插入图片描述](https://img-blog.csdnimg.cn/8ecdb4847a364ac3bb22a6ec050efc3c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/d7779efb56784acb959d628efcaf94ef.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 五、LGSVL 与 Apollo 6.0 联合仿真

## 1. Apollo启动

```cpp
cd apollo/

./docker/scripts/dev_start.sh

./docker/scripts/dev_into.sh

./scripts/bootstrap_lgsvl.sh

./scripts/bridge.sh
```

打开Dreamview http://localhost:8888/，在上方选择对应的模式、车型以及地图（根据自己的仿真环境选择相应的地图）。

## 2. 启动LGSVL仿真器

![在这里插入图片描述](https://img-blog.csdnimg.cn/a0bc35faa01e453e8e39a71db216c80f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
启动 teleop 测试工具，打开新的终端输入

```cpp
./scripts/canbus_teleop.sh
```

在终端使用不同的按键，查看终端车辆控制信息变化、Dreamview右侧栏数值变化以及仿真车辆的行为变化。
![在这里插入图片描述](https://img-blog.csdnimg.cn/773d84c99b704c689a4ace198052e987.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/b471b45e127247e9b21963f5fcb3402e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 参考

【1】[Apollo车辆适配教程](https://github.com/ApolloAuto/apollo/blob/master/docs/technical_tutorial/apollo_vehicle_adaption_tutorial_cn.md#parking-brake-打开电子手刹)
【2】[Apollo详解之canbus模块——车辆底层协议调试](https://blog.csdn.net/weixin_49024732/article/details/118583077)
【3】[apollo介绍之Canbus模块(八)](https://zhuanlan.zhihu.com/p/85083829)
【4】[Apollo之Canbus模块学习总结](https://blog.csdn.net/biaonuan6783/article/details/107357483?spm=1001.2014.3001.5501)
【5】[Apollo CANBUS模块解析](http://kublaikhangeek.github.io/2019/04/19/Apollo-CANBUS模块解析/)