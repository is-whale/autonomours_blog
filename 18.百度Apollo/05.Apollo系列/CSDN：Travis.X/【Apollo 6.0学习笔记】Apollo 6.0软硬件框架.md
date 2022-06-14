- [【Apollo 6.0学习笔记】Apollo 6.0软硬件框架_Travis.X的博客-CSDN博客_apollo6.0运行硬件](https://blog.csdn.net/Travis_X/article/details/121850104)

# 前言

[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) 6.0框架

1. Apollo 6.0在算法模块上，引入了三个新的基于深度学习的模型。
2. 集成了无人驾驶的相关内容 将主要工具、依赖库都升级到了最新版本。
3. 对5.0发布的云服务（（Apollo数据流水线服务，搭配Apollo教育和开发者套件））也进行了全面升级。
4. 对v2x车路协同方案做了重大升级，首发对象级别的车端感知与路侧感知融合。

其他版本[框架](https://so.csdn.net/so/search?q=框架&spm=1001.2101.3001.7020)查询https://github.com/ApolloAuto/apollo#documents
![在这里插入图片描述](https://img-blog.csdnimg.cn/ed0e0dba97b64d12b6a07f41c143e336.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

------

# 一、硬件框架

Apollo 6.0 的硬件设置与Apollo 3.5 相同。
![在这里插入图片描述](https://img-blog.csdnimg.cn/454c9c509687402692c8ec3b51127e3f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/77d01b89983c4dabbe836770f26a6b22.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 1. 硬件与系统集成

车辆:

- 工控机 (IPC)
- 全球定位系统 (GPS)
- 惯性测量单元 (IMU)
- 控制器局域网 (CAN) 卡
- GPS天线
- GPS接收器
- 激光雷达
- 相机
- 毫米波雷达
- 阿波罗传感器单元 (ASU)
- Apollo 扩展单元 (AXU)

软件：

- Ubuntu Linux
- Apollo Linux 内核
- NVIDIA GPU 驱动程序

------

## 2. 核心硬件模块组成

- 车载电脑系统─ Neousys Nuvo-6108GC (2)
- 控制器局域网 (CAN) 卡 ─ ESD CAN-PCIe/402-B4
- 全球定位系统 (GPS) 和惯性测量单元 (IMU) ─ 您可以选择以下选项之一：
  - NovAtel SPAN-IGM-A1
  - NovAtel SPAN® ProPak6™ 和 NovAtel IMU-IGM-A1
  - NovAtel SPAN® PwrPak7™
  - Navtech NV-GI120
- 激光雷达 ─ 您可以选择以下选项之一，请注意 Apollo master 使用 VLS-128 LiDAR：
  - Velodyne VLS-128
  - Velodyne HDL-64E S3
  - Velodyne 冰球系列
  - Innovusion LiDAR
  - Hesai’s Pandora
- 相机 — 您可以选择以下选项之一：
  - Leopard Imaging LI-USB30-AR023ZWDR with USB 3.0 case
  - Argus Camera (FPD-Link)
  - Wissen Camera
- 毫米波雷达 — 您可以选择以下选项之一：
  - Continental ARS408-21
  - Racobit B01HC

------

## 3. 硬件连接

![在这里插入图片描述](https://img-blog.csdnimg.cn/2618a743d3314de58b13c48247355947.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

------

# 二、软件框架

Apollo 6.0 的软件设置与Apollo 5.5 相同。

## 1. 核心软件模块组成

![在这里插入图片描述](https://img-blog.csdnimg.cn/66d527b5163d444abd699023053db962.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

- Perception——感知模块识别自动驾驶汽车周围的环境。感知模块内部包含两个重要的子模块：障碍物检测和交通灯检测。
- Prediction——预测模块用来预测与感知障碍物未来的运动轨迹。
- Routing——路由模块告诉自动驾驶汽车通过全局路径到达目的地。
- Planning——规划模块规划自动驾驶汽车要采取的时空轨迹。
- Control——控制模块通过产生油门、刹车和转向等控制命令来执行计划的时空轨迹。
- CanBus —— CanBus 是将控制命令传递给车辆硬件的接口。它还将机箱信息传递给软件系统。
- HD-Map——该模块类似于库。它不是发布和订阅消息，而是经常用作查询引擎支持，以提供关于道路的特定结构化信息。
- 定位——定位模块利用各种信息源（如 GPS、LiDAR 和 IMU）来估计自动驾驶汽车的位置。
- HMI ——Apollo 中的人机界面或 DreamView 是用于查看车辆状态、测试其他模块和实时控制车辆功能的模块。
- Monitor——车辆中所有模块的监控系统，包括硬件。
- Guardian——新的安全模块，用于干预监控检测到的失败和action center相应的功能。 执行操作中心功能并进行干预的新安全模块应监控检测故障。
- Storytelling——隔离和管理复杂场景的新模块，创建可触发多个模块操作的Story。所有其他模块都可以订阅此特定模块。

------

## 2. 代码架构

![在这里插入图片描述](https://img-blog.csdnimg.cn/27a35b29b1e74d7f8c5797af533a39a9.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

- audio: 音频模块，用来检测能否激活车辆紧急状态时的警报器声音，该模块主要输出警报器的开关状态，移动状态和警报器的相对位置。
- bridge: 桥接模块，该模块采用socket提供了Apollo和外界模块交互的相关支持，包括sender和receiver模块。例如通过该模块可以实现Apollo和LGSVL模拟器的连接。
- calibration: 标定模块，根据不同车型的传感器方案提供包含车身、摄像头、激光雷达、GNSS、感知、雷达、初始化等参数，该文件夹只有标定好的参数，并没有标定的工具和方法。
- canbus: can总线模块，该模块接受并执行控制模块发出的指令，并收集底盘的状态作为反馈。
- common: 公共的模块，包含通信、配置、滤波器、记录模块、数学、log系统、车辆状态，proto, 可视化等子模块。
- contrib: apollo的有益贡献，包含和lgsv相关的protobuf包的信息、tcp和Cyber交互的桥接模块、自定位系统模块(elo: 利用高精地图的自定位模块。前向的摄像头会采集车道数据以实现更精确的定位，输出的位置信息包括车辆的x y z坐标，还有就是在百度高精度地图中的ID)、和端到端(e2e: 端到端深度学习，所谓e2e指的是由传感器的输入，直接决定车的行为，例如油门，刹车，方向等。也就是机器学习的算法直接学习人类司机的驾驶行为)子模块
- control: 控制模块，控制模块根据规划的轨迹和车辆的当前状态，采用不同的控制算法来生成一个舒适的驾驶体验(这里个人理解就是结合舒适性的考虑输出底盘的油门、转向角和刹车的指令等)。控制模块可以再正常模式和导航模式下工作。
- data: 数据模块，包含一些数据的proto定义以及一个smart_recorder工具，smart_recorder用来减少记录数据量的大小，可以有选择的记录所需要的数据。
- dreamview: 可视化模块，dreamview是一个web程序，可以用来可视化车辆的状态信息，以及各个模块的输出。
  driver: 驱动模块，包含摄像头、CAN总线、GNSS、激光雷达、麦克风、smarteye等模块的驱动和proto配置文件，此外还包括了一个视频子模块和一个image_decompress的工具。
- guardian: 系统检测保护模块。
- localization: 定位模块，该模块通过两种方式提供定位服务，一种是结合GPS和IMU信息的RTK（Real Time Kinematic实时运动）方法，另一种是融合GPS、IMU和激光雷达信息的多传感器融合方法。
- map: 地图模块，包括高精度地图、PNC地图(Planning and Control map)、和relative map(连接HD map/感知模块和规划模块的中间层)。此外还有一些相关的proto配置文件，数据和工具等。
- monitor: 监视器模块，该模块包含系统层次的软件用来检查硬件状态和监控系统的健康状态的代码。
- perception: 感知模块，该模块能够仅在一个detection component中检测并分类障碍物。
- planning: 轨迹规划模块，Apollo6.0采用数据驱动的方法和基于学习的模型来解决轨迹规划问题。该模块将每个驾驶用例都视为不同的驾驶场景。Apollo引入了E2E模式和混合模式两种规划模式。
- prediction: 预测模块，该模块学习并预测所有感知模块所检测到的物体的行为，预测的数据输入为障碍物的基本感知信息，包括位置、朝向、速度和加速度。输出为物体的轨迹(带有概率信息)。
- routing: 路由模块，该模块可以根据要求生成高等级的导航信息。该模块的输入是地图数据和路由指令(起点和终点位置)，输出为路由导航信息。
- storytelling: 全局的高级场景管理器，该模块用于帮助协调跨模块的操作。 为了让自动驾驶汽车在城市道路上安全运行，需要复杂的规划场景来确保安全驾驶。该模块输入为定位和高精度地图信息，输出为可以被其他模块订阅的"story"。
- task_manager: 任务管理器，主要任务为似乎是routing相关。
- third_party_perception: 第三方感知模块，该模块用来管理第三方的传感器(例如Mobileye, Conti/Delphi雷达)来进行简单融合并得到感知的输出。主要是在apollo 2.5之前使用，据说apollo刚开始检测效果不好。
- tools: 一些基于python编写并且兼容proto的模块。
- transform: 坐标转换模块，类似于ros的tf2。
- v2x: 车路协同。

------

# 参考

[Apollo 3.5 Hardware and System Installation Guide](https://github.com/ApolloAuto/apollo/blob/master/docs/quickstart/apollo_3_5_hardware_system_installation_guide.md)

[Apollo 5.5 Software Architecture](https://github.com/ApolloAuto/apollo/blob/master/docs/specs/Apollo_5.5_Software_Architecture.md)

[Apollo6.0学习004：Apollo的代码结构](https://zhuanlan.zhihu.com/p/410067026)