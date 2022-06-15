> 作者：浅笑
> 链接：https://www.zhihu.com/question/358869380/answer/2500596375
> 来源：知乎

Apollo官网：[https://apollo.auto/](https://link.zhihu.com/?target=https%3A//apollo.auto/)

GitHub：[https://github.com/ApolloAuto/apollo](https://link.zhihu.com/?target=https%3A//github.com/ApolloAuto/apollo)

## 1. Apollo各版本框架

![img](https://pic1.zhimg.com/50/v2-1518bd48d168536e9c405215695c9a8f_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-1518bd48d168536e9c405215695c9a8f_720w.jpg?source=1940ef5c)

### **Apollo 1.0: 循迹功能实现**

Apollo 1.0（也称为自动GPS航点跟踪）可在**封闭的场地（例如测试跑道或停车场）中工作**。 必须进行此安装，以确保Apollo与您的车辆完美配合。 下图列出了Apollo 1.0中的各个模块

![img](https://pic1.zhimg.com/50/v2-0750f771ff75c7046bb7dbcccd6a7388_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-0750f771ff75c7046bb7dbcccd6a7388_720w.jpg?source=1940ef5c)Apollo 1.0架构图

### **Apollo 1.5: 巡航功能**

Apollo 1.5适用于**固定车道巡航**。 通过添加**LiDAR**，具有该版本的车辆现在可以更好地感知周围环境，并且**可以更好地绘制其当前位置并规划其轨迹**，从而在车道上进行更安全的操纵。 请注意，以黄色突出显示的模块是1.5版的添加或升级。

![img](https://pica.zhimg.com/50/v2-ef83107911b1e804093950c64301e203_720w.jpg?source=1940ef5c)![img](https://pica.zhimg.com/80/v2-ef83107911b1e804093950c64301e203_720w.jpg?source=1940ef5c)Apollo 1.5架构图

### **Apollo 2.0: 城市避障换道信号灯停车**

Apollo 2.0支持在简单的城市道路上自动驾驶的车辆。 车辆能够安全地在道路上行驶，**避免与障碍物碰撞，在交通信号灯处停车以及在需要时改变车道以到达目的地**。 请注意，红色突出显示的模块是2.0版的新增或升级。

![img](https://pica.zhimg.com/50/v2-dbb898d920f56c1bd9a86615d0dce61b_720w.jpg?source=1940ef5c)![img](https://pica.zhimg.com/80/v2-dbb898d920f56c1bd9a86615d0dce61b_720w.jpg?source=1940ef5c)Apollo 2.0架构图

### **Apollo 2.5:高速车道保持**

Apollo 2.5允许车辆通过**摄像头**在**障碍物高速公路上自主行驶**，以进行障碍物检测。 车辆能够保持车道控制，行驶并避免与前方车辆发生碰撞

![img](https://pic2.zhimg.com/50/v2-cde4580d2c0dee5c082b2f99750ab5eb_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-cde4580d2c0dee5c082b2f99750ab5eb_720w.jpg?source=1940ef5c)Apollo 2.5架构图

### **Apollo 3.0: 封闭园区低速控制**

Apollo 3.0的主要重点是为开发人员提供一个在封闭场所低速环境中进行构建的平台。 车辆能够保持车道控制，行驶并避免与前方车辆发生碰撞。

![img](https://pic1.zhimg.com/50/v2-934b09a24448e3aaf47c7d2bcf882756_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-934b09a24448e3aaf47c7d2bcf882756_720w.jpg?source=1940ef5c)Apollo 3.0架构图

### **Apollo 3.5: 市区360环视**

Apollo 3.5能够**导航复杂的驾驶场景**，例如住宅区和市区。 该汽车现在具有360度可视性，并具有升级的感知算法，可以应对不断变化的城市道路状况，从而使汽车更安全，更醒目。 基于场景的计划可以在复杂的场景中导航，包括未保护的转弯和狭窄的街道，这些街道通常出现在居民区和带有停车标志的道路中。

![img](https://pica.zhimg.com/50/v2-a488fdffecd88852052f08fab30669c4_720w.jpg?source=1940ef5c)![img](https://pica.zhimg.com/80/v2-a488fdffecd88852052f08fab30669c4_720w.jpg?source=1940ef5c)Apollo 3.5架构图

### **Apollo 5.0: 360全面感知深度学习模型**

Apollo 5.0旨在支持地理围栏自动驾驶的批量生产。 该汽车现在具有360度可视性，并具有升级的感知深度学习模型，可以处理复杂路况的变化情况，从而使汽车更加安全和感知。 基于场景的计划已得到增强，以支持其他场景，例如，过马路和穿越交叉路口。

![img](https://pic1.zhimg.com/50/v2-1677c92e0803a339ed40b864d72b8693_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-1677c92e0803a339ed40b864d72b8693_720w.jpg?source=1940ef5c)Apollo 5.0架构图

### **Apollo 5.5: 点到点城市自动驾驶**

Apollo 5.5通过引入路边对道路的驾驶支持，增强了先前Apollo版本中复杂的城市道路自动驾驶能力。有了这一新功能，阿波罗现在已接近自动驾驶城市道路驾驶的飞跃。该汽车具有完整的360度可视性，以及**升级的感知深度学习模型和全新的预测模型**，可应对复杂道路和交汇处场景的变化情况，从而使汽车更安全，更醒目。

![img](https://pica.zhimg.com/50/v2-ad89cc1f2cc9e974cb3a87d36b9c84f4_720w.jpg?source=1940ef5c)![img](https://pica.zhimg.com/80/v2-ad89cc1f2cc9e974cb3a87d36b9c84f4_720w.jpg?source=1940ef5c)Apollo 5.5架构图

### **Apollo 6.0: 迈向无人化自动驾驶**

**Apollo 6.0亮点主要有5点：**

1.  Apollo 6.0在算法模块上，引入了三个新的基于深度学习的模型。
2. 在感知上，Apollo 6.0 实现了基于PointPillars的激光点云障碍物识别模型
3. 在预测上，Apollo 6.0 发布了基于语义地图的低速行人预测模型
4. 在规划上，Apollo 6.0 首次引入了基于语义地图的模仿学习
5. 集成了无人驾驶的相关内容
6. 将主要工具、依赖库都升级到了最新版本。
7. 对5.0发布的云服务（（Apollo数据流水线服务，搭配Apollo教育和开发者套件））也进行了全面升级
8. 对v2x车路协同方案做了重大升级，首发对象级别的车端感知与路侧感知融合。

更多详情：[Apollo 6.0发布！无人化自动驾驶迎来新篇章](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s/-7gY6SYbLNy-mhpR9dJtIQ)

![img](https://pic1.zhimg.com/50/v2-f613fba42d9491c02768cbf3a633cd2f_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-f613fba42d9491c02768cbf3a633cd2f_720w.jpg?source=1940ef5c)Apollo 6.0架构图

### **Apollo 7.0: 性能再次升级**

Apollo 7.0 包含 3 个全新的深度学习模型，以增强 Apollo 感知和预测模块的能力。该版本引入了Apollo Studio，结合Data Pipeline，提供一站式在线开发平台，更好地服务Apollo开发者。 Apollo 7.0 还发布了基于以往仿真服务的 PnC 强化学习模型训练和仿真评估服务。

1. 云端服务平台层面，Apollo 7.0将6.0版本中深受开发者欢迎的“数据流水线”服务正式升级为Apollo Studio，涵盖开发者从上机到上车实践的全流程云端工具链，为开发者提供一站式实践平台体验。
2. 仿真平台层面，**Apollo 7.0推出业界首个PnC强化学习模型训练与仿真评测平台**，具有数据真实、功能强大、评测标准全面、架构可扩展等多重优势，有望为强化学习研究提供统一的验证标准。
3. 开源软件平台层面，**Apollo7.0对感知和预测算法模块升级，引入MaskPillars、SMOKE、Inter-TNT三个基于深度学习的模型**，有效减少漏检、抖动等问题。

更多详情：[Apollo7.0 重磅发布，百度多款汽车机器人集体驶入元宇宙](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s/0ft9uz-2srlZU09yWpO6Jw)

![img](https://pic2.zhimg.com/50/v2-2702b3f86e72130b7fd4967d9c25b241_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-2702b3f86e72130b7fd4967d9c25b241_720w.jpg?source=1940ef5c)Apollo 7.0架构图

## 2. 软硬件框架

### **(1) 硬件框架**

![img](https://pica.zhimg.com/50/v2-3ba5e1117a541c63a3bf5f5d80d279da_720w.jpg?source=1940ef5c)![img](https://pica.zhimg.com/80/v2-3ba5e1117a541c63a3bf5f5d80d279da_720w.jpg?source=1940ef5c)

![img](https://pic2.zhimg.com/50/v2-1bb3fd5c5dbc64c21f4687adfecd1c16_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-1bb3fd5c5dbc64c21f4687adfecd1c16_720w.jpg?source=1940ef5c)

### (2) 硬件连接

![img](https://picx.zhimg.com/50/v2-26ba10a3ab51980127931f4e11c2eabf_720w.jpg?source=1940ef5c)![img](https://picx.zhimg.com/80/v2-26ba10a3ab51980127931f4e11c2eabf_720w.jpg?source=1940ef5c)

### (3) 软件框架

![img](https://pic2.zhimg.com/50/v2-4fbd37ad2bbd6c866f98a5906a5ab17a_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-4fbd37ad2bbd6c866f98a5906a5ab17a_720w.jpg?source=1940ef5c)

> Perception——感知模块识别自动驾驶汽车周围的环境。感知模块内部包含两个重要的子模块：障碍物检测和交通灯检测。
> Prediction——预测模块用来预测与感知障碍物未来的运动轨迹。
> Routing——路由模块告诉自动驾驶汽车通过全局路径到达目的地。
> Planning——规划模块规划自动驾驶汽车要采取的时空轨迹。
> Control——控制模块通过产生油门、刹车和转向等控制命令来执行计划的时空轨迹。
> CanBus —— CanBus 是将控制命令传递给车辆硬件的接口。它还将机箱信息传递给软件系统。
> HD-Map——该模块类似于库。它不是发布和订阅消息，而是经常用作查询引擎支持，以提供关于道路的特定结构化信息。
> 定位——定位模块利用各种信息源（如 GPS、LiDAR 和 IMU）来估计自动驾驶汽车的位置。
> HMI ——Apollo 中的人机界面或 DreamView 是用于查看车辆状态、测试其他模块和实时控制车辆功能的模块。
> Monitor——车辆中所有模块的监控系统，包括硬件。
> Guardian——新的安全模块，用于干预监控检测到的失败和action center相应的功能。 执行操作中心功能并进行干预的新安全模块应监控检测故障。
> Storytelling——隔离和管理复杂场景的新模块，创建可触发多个模块操作的Story。所有其他模块都可以订阅此特定模块。

## 3. 各模块架构

Apollo 无人驾驶平台是以**高精地图和定位模块**作为核心。其他的模块都是以这两个模块为基础。

![img](https://pic2.zhimg.com/50/v2-1d733f3f43deb3e291d3472ee129986b_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-1d733f3f43deb3e291d3472ee129986b_720w.jpg?source=1940ef5c)

### 定位

- - [https://github.com/ApolloAuto/apollo/blob/master/modules/localization/README_cn.md](https://link.zhihu.com/?target=https%3A//github.com/ApolloAuto/apollo/blob/master/modules/localization/README_cn.md)
  - 一种是结合GPS和IMU信息的RTK（Real Time Kinematic实时运动）方法，另一种是融合GPS、IMU和激光雷达信息的多传感器融合方法。

### 感知

- - [https://github.com/ApolloAuto/apollo/tree/master/modules/perception](https://link.zhihu.com/?target=https%3A//github.com/ApolloAuto/apollo/tree/master/modules/perception)

### 预测

- - [https://github.com/ApolloAuto/apollo/blob/master/modules/prediction/README_cn.md](https://link.zhihu.com/?target=https%3A//github.com/ApolloAuto/apollo/blob/master/modules/prediction/README_cn.md)
  - 预测模块从感知模块接收障碍物，其基本感知信息包括位置、方向、速度、加速度，并生成不同概率的预测轨迹。

### 路由

- - [https://github.com/ApolloAuto/apollo/blob/master/modules/routing/README_cn.md](https://link.zhihu.com/?target=https%3A//github.com/ApolloAuto/apollo/blob/master/modules/routing/README_cn.md)
  - 路由模块根据请求生成高级导航信息。

路由模块依赖于路由拓扑文件，通常称为Apollo中的routing_map.*。路由地图可以通过命令来生成。

### 规划

- - [https://github.com/ApolloAuto/apollo/blob/master/modules/planning/README_cn.md](https://link.zhihu.com/?target=https%3A//github.com/ApolloAuto/apollo/blob/master/modules/planning/README_cn.md)

### 控制

- - [https://github.com/ApolloAuto/apollo/blob/master/modules/control/README_cn.md](https://link.zhihu.com/?target=https%3A//github.com/ApolloAuto/apollo/blob/master/modules/control/README_cn.md)
  - 本模块基于规划和当前的汽车状态，使用不同的控制算法来生成舒适的驾驶体验。控制模块可以在正常模式和导航模式下工作。
  - 输出：**给底盘的控制指令（转向，节流，刹车）。**