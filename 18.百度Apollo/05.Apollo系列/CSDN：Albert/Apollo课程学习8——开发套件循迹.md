- [Apollo 课程学习8——开发套件循迹_Albert的博客-CSDN博客](https://blog.csdn.net/weixin_43476492/article/details/107991783)

# 概述

**循迹自动驾驶**是指车辆按照录制好的轨迹线进行[自动驾驶](https://so.csdn.net/so/search?q=自动驾驶&spm=1001.2101.3001.7020)。循迹的过程中车辆不能进行避障，循迹是车辆进行自动驾驶时的一个最基本的能力。

![img](https://img-blog.csdnimg.cn/20200813212310683.jpg#pic_center)

# 实验所需Apollo硬件设备

![img](https://img-blog.csdnimg.cn/20200813213718303.jpg#pic_center)

- 车辆底盘
- 计算单元：使用工控机（工业控制计算机）**IPC** [（Nuvo-6108GC series）](https://www.neousys-tech.com/en/product/application/edge-ai-gpu-computing/nuvo-6108gc-gpu-computing)

![img](https://img-blog.csdnimg.cn/20200813214553944.jpg#pic_center)

- 定位系统：主要依靠卫星定位导航系统。使用双GPS天线、GPS接收机[（Newton-M2）](http://www.starneto.com/index.php?m=content&c=index&a=show&catid=100&id=183)

![img](https://img-blog.csdnimg.cn/20200813220322304.jpg#pic_center)

- CAN卡：EMUC-CAN卡

![img](https://img-blog.csdnimg.cn/20200813221049995.jpg#pic_center)

- 4G路由器、显示器、键盘、鼠标

# 硬件连接

![img](https://img-blog.csdnimg.cn/20200813221416666.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200813221628242.jpg#pic_center)

# 硬件安装

## 一、CAN 卡的安装要求

- 安装前 CAN 卡的两个黑色跳帽在指定位置安装确保两路 CAN 通讯同时工作。

![img](https://img-blog.csdnimg.cn/20200814111746584.jpg#pic_center)

- CAN 卡接线，CN1、CN2 对应 CAN0、CAN1

![img](https://img-blog.csdnimg.cn/20200814112107719.jpg#pic_center)

- 拆解工控机
- 找 mini-PCI-E，固定 CAN 卡。CAN 卡的 mini-PCI-E 接口接在工控机的 mini-PCI-E 端
- 安装工控机外壳

## 二、[工控](https://so.csdn.net/so/search?q=工控&spm=1001.2101.3001.7020)机的安装要求

- 安装固定支架
- 把工控机安装在车辆底盘，把工控机带接口的一面安装向后
- 给工控机安装电源，有 12V 和 24V 电源，正负极不接反

## 三、导航设备的安装要求

- 安装 GPS 天线，连接 GPS 天线线束
- 安装 GPS 接收器和 IMU，一般安装在车辆后轴中心附近
- 将 GPS 天线和 GPS 接收机连接，对接收器进行供电
- 把集成线束连接到 M2 上
- 车辆底盘供电线和车辆供电线连接，把车辆底盘的电源连接到保险盒的干路
- 连接 M2 电源延长线，连接 M2 至保险盒，负极接到汇流排，正极输出端

## 四、路由器显示器的安装要求

- 固定 4G 路由器在车辆底盘上
- 对 4G 路由器连接电源，电源线束一端连接电源适配，一端连接路由器
- 连接显示器的供电
- 通信接口连接

# 软件部署

## 一、软件

- Unbuntu操作系统
- Linux4.4内核
- Apollo1.5.5内核

## 二、驱动软件

- GPU显卡驱动
- ESD-CAN卡驱动或Socket CAN卡驱动

## 三、应用软件

- Docker软件
- Git软件
- Apollo源代码

## 四、操作

- BIOS设置：设置工控机一直以最优状态。
- 安装Unbuntu
- 安装Linux4.4内核
- 安装Apollo内核
- 安装GPU驱动
- 安装CAN卡驱动
- Docker 软件安装
- Git 安装
- 拉取 Apollo 源代码
- 启动并加入docker容器
- 编译apollo
- 回放数据包 demo

# 定位模块配置

![img](https://img-blog.csdnimg.cn/20200814120250683.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200814120432859.jpg#pic_center)

- 用一根串口延长线，一端连接 M2的升级口，另一端接到工控机 COM1 串口。
- 在工控机上下载 cutecom 串口助手，通过串口助手与 M2 设备进行交互，写入配置信息

![img](https://img-blog.csdnimg.cn/20200814120832335.jpg#pic_center)

- 配置 M2 的所有参数：反馈$ c m d , c o n f i g , o k ∗ f f cmd,config,ok^*ff*c**m**d*,*c**o**n**f**i**g*,*o**k*∗*f**f*即成功，反馈$ c m d , c o m m a n d , b a d ∗ f f cmd,command,bad^*ff*c**m**d*,*c**o**m**m**a**n**d*,*b**a**d*∗*f**f*即失败。
- 设置网口信息
- 配置定位基站信息
- 配置杆臂值。
- 软件配置
- 实测验证
- 检查定位状态类型
- 将车辆遥控数米，得到localization输出，检查定位输出

# 车辆动力学标定

**Apollo软件的标定**：
标定表提供一个描述不同车辆速度、油门/刹车踏板开合度、加速度量之间关系的映射表。

![img](https://img-blog.csdnimg.cn/20200814190132753.jpg#pic_center)
标定的操作方法和**基本步骤**：

- 启动docker
- 启动各模块：打开canbus、GPS、Localization、rescore，并检查
- 标定数据采集，意外时使用遥控器或车辆急停按钮
- 输入XYZ值，Z值是负值

![img](https://img-blog.csdnimg.cn/20200814191919662.jpg#pic_center)

- q命令退出采集，生成.csv文件
- 对标定结果画曲线

![img](https://img-blog.csdnimg.cn/20200814192651500.jpg#pic_center)

- 生成protobuf标定文件，拷贝数据段
- 循迹轨迹文件包含定位信息、车速反馈、车辆加速度反馈、车辆的动作信息

# 车辆循迹

![img](https://img-blog.csdnimg.cn/20200814193407329.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200814193513819.jpg#pic_center)

- 配置文件修改
- 启动docker
- 进入dreamview界面
- 启动EMUC-CAN驱动程序
- 启动canbus、启动GPS、定位模块
- 轨迹录制，标注起点位置
- 循迹