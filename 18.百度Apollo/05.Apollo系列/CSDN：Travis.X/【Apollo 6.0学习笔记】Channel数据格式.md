- [【Apollo 6.0学习笔记】Channel数据格式_Travis.X的博客-CSDN博客_apollo 数据格式](https://blog.csdn.net/Travis_X/article/details/121013438)

# 前言

[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) 系统各个模块之间的通信框架如下图所示。
![在这里插入图片描述](https://img-blog.csdnimg.cn/02de19ac75e74fd2afb367648d010c4a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
橘色的实线为数据流动线，黑色实线为控制逻辑线。各个模块的功能如下：

| 模块               | 功能                                                         |
| ------------------ | ------------------------------------------------------------ |
| 感知(Perception）  | 识别交通参与者（汽车、自行车、行人等），识别交通信号灯等。   |
| 预测(Prediction）  | 对交通参与者的行为进行预测。                                 |
| 规划(Planning)     | 对主车行为进行决策，实时生成车辆规划线。                     |
| 控制(Control)      | 根据规划线目标，生成控制车辆指令（转角、速度、加速度）。     |
| 高精地图(HD Map)   | 该模块类似于库。它不是发布和订阅消息，而是经常用作查询引擎支持，以提供关于道路的特定结构化信息。高精地图还可以提供环境静态感知能力。 |
| 定位(Localization) | 定位模块利用 GPS、LiDAR 和 IMU 的各种信息源来定位自动驾驶车辆的位置。 |
| 底盘通信 CANBus    | CANBus 是将控制命令传递给车辆硬件的接口。将控制车辆指令发送至底盘，还将底盘信息传递给软件系统。 |
| 人机交互(HMI)      | Apollo 中的 HMI 和 DreamView 是一个用于查看车辆状态，测试其他模块以及实时控制车辆功能的模块。 |
| 监控(Monitor)      | 车辆中所有模块的监控系统包括硬件。                           |
| Guardian           | 安全模块，用于干预监控检测到的失败和 action center 相应的功能。执行操作中心功能并进行干预的新安全模块应监控检测故障。 |

------

# 一、Apollo 通信系统

Apollo 的各个模块是以组件的形式存在的。组件之间利用数据通道进行通信。其中，最小的数据单元是消息格式来定义的。也有其他 Node、writer\reader 等，为了避免混淆，这里只关注组件、数据通道和消息格式。

| 通信单元             | 定义                                                         |
| -------------------- | ------------------------------------------------------------ |
| 组件（Component）    | 在自动驾驶系统中，模块（如感知、定位、控制系统等）在 CyberRT 下以Component 的形式存在。不同 Component 之间通过 Channel 进行通信。Component 概念不仅解耦了模块，还为将模块拆分为多个子模块提供了灵活性。 |
| 数据通道（Channel）  | 用于管理 Cyber RT 中的数据通信。用户可以发布/订阅同一个 Channel，实现 P2P 通信。 |
| 消息格式 （Message） | CyberRT 中用于模块之间数据传输的数据单元。                   |

## 1. 组件

Apollo 的系统是由各个模块的组件组成的，每一个组件类似于一个功能。一个模块（例如：定位、感知等）可以有多个组件。 各个组件之间通信是通过 Channel 实现。Channel 通信通道的具体数据格式则由 Message 定义。

## 2. 数据通道

Channel 是传输数据的通道，管理 CyberRT 中的数据通信。用户可以发布/订阅同一个 Channel 建立通信，实现点对点（P2P）通信。

播放数据包之后，打开 CyberMonitor 工具并进入特定数据通道，可以看到每个 Channel 中都有 ChannelName、MessageType、FrameRatio、RawMessage Size 数据字段。关于播放数据包，参见 播放 Apollo 的演示包。

各个数据字段的名称和描述如下所示：

| 名称            | 描述                     | 值                                          |
| --------------- | ------------------------ | ------------------------------------------- |
| ChannelName     | 数据通道的名字。         | 例如：/apollo/perception/obstacles          |
| MessageType     | 通道内的数据的数据类型。 | 例如：apollo.perception.PerceptionObstacles |
| FrameRatio      | 数据更新频率。           | 例如：10 HZ                                 |
| RawMessage Size | 原始数据的数据大小。     | 例如：16863 字节                            |

其中，MessageType 字段展现的数据格式是本 Channel 通道里使用的最小数据单元，由消息格式（Message）定义。示例中 apollo.perception.PerceptionObstacles 是数据通道 /apollo/perception/obstacles 的核心消息格式。

## 3. 消息格式

Message 是 CyberRT 中用于模块之间数据传输的基本数据单元。Apollo 中，消息格式（Message）由 .proto 为后缀的文件定义。关于 .proto，参见 protobuf 官网。
以 perception_obstacle.proto 文件定义的 PerceptionObstacles 消息格式为例：

```cpp
message PerceptionObstacles {
  repeated PerceptionObstacle perception_obstacle = 1;  // An array of obstacles
  optional apollo.common.Header header = 2;             // Header
  optional apollo.common.ErrorCode error_code = 3 [default = OK];
  optional LaneMarkers lane_marker = 4;
  optional CIPVInfo cipv_info = 5;  // Closest In Path Vehicle (CIPV)
}
```

消息格式主要分为两大类，即：

- 通用（Common）消息格式

  各模块通用的消息格式，如定义时间的时间戳消息格式、错误代码消息格式。

- 特定模块消息格式

  各模块独有的消息格式。

## 3.1 通用消息格式

常用的通用的消息格式如下所示：

| 文件       | 定义的数据内容 | Message 定义                    |
| ---------- | -------------- | ------------------------------- |
| header     | 头信息         | 由 apollo.common.Header 定义    |
| error_code | 错误码         | 由 apollo.common.ErrorCode 定义 |

关于获取更多通用消息格式的信息，参见 [Common](https://gitee.com/ApolloAuto/apollo/tree/master/modules/common/proto)消息格式。

## 3.2 特定模块的消息格式

播放数据包并打开 CyberMonitor 后，可以看到以下数据通道和对应的主消息格式。 关于主消息格式的更多内容，参见对应的文档深入了解。

| 模块         | 数据通道                          | 主消息格式                               |
| ------------ | --------------------------------- | ---------------------------------------- |
| CANBUS 模块  | /apollo/canbus/chassis            | apollo.canbus.Chassis                    |
| 控制模块     | /apollo/control                   | apollo.control.ControlCommand            |
| Guardian模块 | /apollo/guardian                  | apollo.guardian.GuardianCommand          |
| 人机交互模块 | /apollo/hmi/status                | apollo.dreamview.HMIStatus               |
| 定位模块     | /apollo/localization/msf_gnss     | apollo.localization.LocalizationEstimate |
| 定位模块     | /apollo/localization/msf_lidar    | apollo.localization.LocalizationEstimate |
| 定位模块     | /apollo/localization/msf_status   | apollo.localization.LocalizationStatus   |
| 定位模块     | /apollo/localization/pose         | apollo.localization.LocalizationEstimate |
| 定位模块     | /apollo/sensor/gnss/corrected_imu | apollo.localization.CorrectedImu         |
| 定位模块     | /apollo/sensor/gnss/odometry      | apollo.localization.Gps                  |
| 监控模块     | /apollo/monitor                   | apollo.common.monitor.MonitorMessage     |
| 监控模块     | /apollo/monitor/system_status     | apollo.monitor.SystemStatus              |
| 感知模块     | /apollo/perception/obstacles      | apollo.perception.PerceptionObstacles    |
| 感知模块     | /apollo/perception/traffic_light  | apollo.perception.TrafficLightDetection  |
| 规划模块     | /apollo/planning                  | apollo.planning.ADCTrajectory            |
| 预测模块     | /apollo/prediction                | apollo.prediction.PredictionObstacles    |

------

# 总结

[Channel数据格式文档介绍](https://apollo.auto/document_cn.html?target=/Apollo-Homepage-Document/Apollo_Doc_CN_6_0/)