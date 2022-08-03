- [自动驾驶（七十七）---------Apollo平台介绍_一实相印的博客-CSDN博客](https://blog.csdn.net/zhouyy858/article/details/111159518)

目前[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020)已经更新到6.0，整体架构和功能都已经很成熟了，Apollo开源无疑极大的提高了自动驾驶行业的整体水平，所以对于研究自动驾驶的人来说，apollo可以说是入场券，最近应该是我比较轻松的时间，准备系统的学习一下Apollo的代码和思想，过完年去新公司应该会比较忙了。

**1. Apollo与ROS**

​    Apollo项目基于ROS，但是对其进行了改造，主要包括下面三个方面：

1. 通信性能优化：将通过共享内存来减少数据拷贝，以提升通信性能。
2. 去中心化网络拓扑：Apollo使用RTPS服务发现协议实现完全的P2P网络拓扑。
3. 数据兼容性扩展：ROS通过msg定义接口，但是接口升级之后不同的版本的模块难以兼容，因此Apollo选择了Google的[Protocol Buffers](https://developers.google.com/protocol-buffers/)格式数据来定义接口。

**2. 软件架构图**

​         ![img](https://img-blog.csdnimg.cn/img_convert/0d7bbf604af69072fcff535b94273d2f.png)

**3. 模块介绍**

​    apollo目前现有的模块如图： ![img](https://img-blog.csdnimg.cn/20201214124048950.png)![img](https://img-blog.csdnimg.cn/2020121412410173.png)

​    下面每个模块做一个简单介绍，重要的模块单独写一篇文章来分析。

1. audio：音频模块，用来检测能否激活车辆紧急状态时的警报器声音，该模块主要输出警报器的开/关状态、移动状态和警报器的相对位置。
2. bridge：桥接模块，该模块为其他阿波罗模块提供了一种通过socket与Apollo外部进程交互的方法，包括发送方和接收方组件。
3. calibration：标定模块，包含车身、摄像头、激光雷达、定位、感知、雷达等等一系列的参数，注意这里只有参数的结果，没有标定的工具和方法。
4. common：公共模块，包含通讯、配置、滤波器、记录模块、数学、log系统、车辆状态等子模块。
5. contrib：apollo的有益贡献，包含Google的Protobuf定义消息、tcp桥接模块、自定位系统(GPS和百度高精地图融合)。
6. control：控制模块，根据规划和车辆状态，来控制车辆驾驶。
7. data：数据记录模块，特别的，数据较小的模块都记录：定位、车速等，数据很大的只在特定场景记录：点云、摄像头等。
8. dreamview：画图模块，提供web端的可视化模块。
9. driver：驱动模块，包含摄像头、can总线、gps、雷达、手机、等驱动文件。
10. guardian：系统检测保护。
11. localization：定位模块，可以是直接基于RTK的信号，也可以是gps+imu+激光雷达等多传感器融合定位。
12. map：地图模块，可以是直接用百度的高精地图，也可以是基于传感器的实时地图。
13. monitor：监视器模块，功能包含：各模块的运行状态、数据完整性、监测数据频率、CPU、内存、磁盘使用情况、生成端到端的延迟统计报告等。
14. perception：检测模块，主要作用是检测和分类基于摄像头、雷达（前后）、LiDAR的障碍物，6.0提供基于PointPillars的训练模型。
15. planning：规划模块，6.0不同场景采用不同的参数集，例如：路口、公共道路、靠边停车、紧急情况等。规划包含轨迹和速度两块的规划。
16. prediction：预测模块，预测感知到的所有对象的行为，包含位置、方向、速度、加速度、轨迹等。
17. routing：导航模块，根据地图和目的地，生成导航信息。
18. storytelling：场景管理模块，不同的场景需要调用不同的模块相互工作，storytelling就是做这个协调和管理工作。
19. task_manager：任务管理器。
20. third_party_perception：第三方的检测模块，之前apollo自己的检测效果不好，借用了第三方的检测：Mobileye的视觉、 Conti/Delphi 的激光雷达等。
21. tools：基于python的一些工具，不会作为一个节点独立运行。
22. transform：坐标转换模块，相当于ROS中的tf2。
23. v2x：车路协同，目前还不成熟。