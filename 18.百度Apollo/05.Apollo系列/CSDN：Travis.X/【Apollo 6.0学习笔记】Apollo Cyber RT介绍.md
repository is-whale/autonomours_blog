- [【Apollo 6.0学习笔记】Apollo Cyber RT介绍_Travis.X的博客-CSDN博客_apollo cyber](https://blog.csdn.net/Travis_X/article/details/120965138)

# 前言

![在这里插入图片描述](https://img-blog.csdnimg.cn/570af3de8fe5425ea3ff892e6aee5245.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) Cyber RT 是一个开源、高性能的运行时框架，专为自动驾驶场景而设计。针对自动驾驶的高并发、低延迟、高吞吐量进行了大幅优化。

使用 Apollo Cyber RT 的主要好处：

- 加速开发
  - 具有数据融合功能的定义明确的任务接口
  - 一系列开发工具
  - 大量传感器驱动程序
- 简化部署
  - 高效自适应的消息通信
  - 具有资源意识的可配置用户级调度程序
  - 可移植，依赖更少
- 为您的自动驾驶汽车赋能
  - 默认的开源运行时框架
  - 为自动驾驶搭建专用模块

------

# 一、基本概念

| Cyber          | ROS               | 注释                                                         |
| -------------- | ----------------- | ------------------------------------------------------------ |
| Component      | 无                | 不同Component之间通过Channel进行通信。                       |
| Channel        | Topic             | channel 用于管理数据通信，用户可以通过 publish/subscribe 相同的 channel 来通信。 |
| Node           | Node              | Channel用于管理 Cyber RT 中的数据通信。用户可以发布/订阅同一个Channel，实现p2p通信。 |
| Reader/Writer  | Publish/Subscribe | 订阅者模式。往 channel 读写消息的类。 通常作为 Node 主要的消息传输接口。 |
| Service/Client | Service/Client    | 请求/响应模式，支持节点间双向通信。                          |
| Message        | Message           | Message是Cyber RT中用于模块之间数据传输的数据单元。          |
| Parameter      | Parameter         | Parameter 服务提供全局参数访问接口。该服务基于 service/client 模式。 |
| Record file    | Bag file          | Record文件用于记录从Cyber RT中的Channel发送/接收的消息。回放Record文件可以帮助重现Cyber RT之前操作的行为。 |
| Launch file    | Launch file       | Launch文件提供了一种启动模块的简单方法。通过在launch文件中定义一个或多个dag文件，可以同时启动多个模块。 |
| Task           | 无                | Task是Cyber RT中异步计算任务的抽象描述。                     |
| CRoutine       | 无                | 协程，优化线程使用与系统资源分配                             |
| Scheduler      | 无                | 为了更好地支持自动驾驶场景，Cyber RT提供了多种资源调度算法供开发者选择。 |
| Dag file       | 无                | Dag文件是模块拓扑关系的配置文件。您可以在dag文件中定义使用的Component和上游/下游通道。 |

------

# 二、开发工具

CyberRT框架同时也提供了一系列实用的工具用来辅助日常开发, 包括可视化工具cyber_visualizer以及命令行工具cyber_monitor和cyber_recorder等。

## 1. Cyber Monitor

cyber_monitor提供了终端中实时显示channel信息列表的功能。

进入Apollo Docker环境后执行如下命令运行cyber_monitor。

```cpp
cyber_monitor
```

cyber_monitor会自动从拓扑中收集所有channel的信息并分两列显示（channel名称，数据频率）。
channel信息默认显示为红色，当有数据流经channel时，对应的行就会显示成绿色，如下图所示：![在这里插入图片描述](https://img-blog.csdnimg.cn/af291eb61c494c9aa160df21070076d8.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
可以通过-h选项来获取帮助信息：

```cpp
cyber_monitor -h
```

> ESC | q key ---- 退出
> Backspace ---- 后退
> h | H ---- 显示帮助页
> PageDown | Ctrl+d ---- 上一页
> PageUp | Ctrl+u ---- 下一页
> Up, down or w, s keys ---- 上下移动当前的高亮行
> Right arrow or d key ---- 进入高亮行, 显示高亮行数据的详细信息
> Left arrow or a key ---- 从当前界面返回上一层界面
> Enter key ---- 与d键相同
> f | F ---- 显示数据帧频率
> t | T ---- 显示channel消息类型
> Space ---- 关闭|开启 channel (仅在channel有数据到达时有效; channel关闭后会变成黄色)
> i | I ---- 显示channel的Reader和Writer信息
> b | B ---- 显示channel消息内容
> n | N ---- 显示消息中RepeatedField的下一条数据
> m | M ---- 显示消息中RepeatedField的上一条数据

使用-c选项，可以让cyber_monitor只监测一个指定的channel信息：

```cpp
cyber_monitor -c ChannelName
```

------

## 2. Cyber Visualizer

在Apollo Docker环境中执行如下命令运行cyebr_visualizer：

```cpp
cyber_visualizer
```

在启动cyber_visualizer后，你会看到如下界面：
![在这里插入图片描述](https://img-blog.csdnimg.cn/236bdb812ee049b9a4491c75965656db.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
当有数据流经channel时，在ChannelNames下会像下图一样显示channel列表。
![在这里插入图片描述](https://img-blog.csdnimg.cn/820ac92751574fe8a5be969987dc1fa0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
实际车辆在道路上运行过程中的 cyber visualizer 画面。
![在这里插入图片描述](https://img-blog.csdnimg.cn/7819ac77f60d4bd68c0687c871824eba.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

------

## 3. Cyber Recorder

cyber_recorder是 Apollo Cyber RT 提供的录制/播放工具。它提供了许多有用的功能，包括录制record文件、播放record文件、拆分record文件、查看record文件信息等。

启动cyber_recorder

```cpp
cyber_recorder <command>> [<args>]
```

- 查看record文件的信息：

```cpp
$ cyber_recorder info -h
usage: cyber_recorder info [options]
	-h, --help				show help message
```

- 录制record文件

```cpp
$ cyber_recorder record -h
usage: cyber_recorder record [options]
    -o, --output <file>                                            output record file
    -a, --all                                                                  all channels
    -c, --channel <name>                                     channel name
    -i, --segment-interval <seconds>              record segmented every n second(s)
    -m, --segment-size <MB>                              record segmented every n megabyte(s)
    -h, --help                                                              show help message
```

- 要拆分record文件

```cpp
$ cyber_recorder split -h
usage: cyber_recorder split [options]
    -f, --file <file>                                                    input record file
    -o, --output <file>                                           output record file
    -a, --all                                                                all channels
    -c, --channel <name>                                   channel name
    -b, --begin <2018-07-01 00:00:00>          begin at assigned time
    -e, --end <2018-07-01 01:00:00>              end at assigned time
```

- 修复record文件

```cpp
$ cyber_recorder recover -h
usage: cyber_recorder recover [options]
    -f, --file <file>                                                  input record file
    -o, --output <file>                                         output record file
```

------

## 4. rosbag_to_record

rosbag_to_record是 Apollo Cyber RT 提供的一个可以将 rosbag 转换为record文件的工具。现在该工具支持以下channel：

```cpp
/apollo/perception/obstacles
/apollo/planning
/apollo/prediction
/apollo/canbus/chassis
/apollo/control
/apollo/guardian
/apollo/localization/pose
/apollo/perception/traffic_light
/apollo/drive_event
/apollo/sensor/gnss/odometry
/apollo/monitor/static_info
/apollo/monitor
/apollo/canbus/chassis_detail
/apollo/control/pad
/apollo/navigation
/apollo/routing_request
/apollo/routing_response
/tf
/tf_static
/apollo/sensor/conti_radar
/apollo/sensor/delphi_esr
/apollo/sensor/gnss/best_pose
/apollo/sensor/gnss/imu
/apollo/sensor/gnss/ins_stat
/apollo/sensor/gnss/rtk_eph
/apollo/sensor/gnss/rtk_obs
/apollo/sensor/velodyne64/compensator/PointCloud2
```

使用教程

> $ source /your-path-to-apollo-install-dir/cyber/setup.bash
> $ rosbag_to_record
> Usage:
> rosbag_to_record input.bag output.record

------

# 参考

[Apollo Cyber RT Developer Tools](https://cyber-rt.readthedocs.io/en/latest/CyberRT_Developer_Tools.html#rosbag-to-record)
[CyberRT介绍](https://apollo.auto/Apollo-Homepage-Document/Apollo_Doc_CN_6_0/上机使用教程/实时通信框架CyberRT的使用/CyberRT介绍)
[百度 Apollo Cyber RT简介、基本概念以及与 ROS 对照](https://blog.csdn.net/kesalin/article/details/88914029)