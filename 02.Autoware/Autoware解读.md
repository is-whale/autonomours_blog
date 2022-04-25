- [Autoware解读 - 古月居 (guyuehome.com)](https://www.guyuehome.com/36811)

对自动驾驶感兴趣的学者及开发者，可以研究下Autoware和百度的Apollo,今天我们来介绍下开源的Autoware。

开源的自动驾驶框架：Autoware。其最早是由名古屋大学研究小组在加藤伸平教授(Prof. Shinpei Kato)的领导下于2015年8月正式发布。2015年12月下旬，加藤伸平教授创立了Tier IV，以维护Autoware并将其应用于真正的自动驾驶汽车。随着时间的流逝，Autoware已成为公认的开源项目。Autoware也是世界上第一个用于自动驾驶技术的“多合一”开源软件。目前其基于ROS 1，并在Apache 2.0许可下可用。Autoware的功能主要适合与城市，但也可以覆盖高速公路。支持以下功能：路径规划、路径跟随、加速/制动/转向控制、数据记录、汽车/行人/物体检测、3D本地化、3D映射、交通信号检测、交通灯识别、车道检测、对象跟踪、传感器校准、传感器融合、面向云的地图连接自动化、智能手机导航、软件仿真、虚拟现实

现在基于ROS2.0重新设计了的Autoware也在开发中，详情请查看官网

[Autoware.Autoautoware.auto](https://link.zhihu.com/?target=http%3A//autoware.auto)

[![img](https://pic3.zhimg.com/80/v2-32e1d460c8017102a3a28450db3c95ae_720w.jpg)](https://pic3.zhimg.com/80/v2-32e1d460c8017102a3a28450db3c95ae_720w.jpg)

Autoware开发架构

我们先从基于ROS1.0的autoware入手，来分析Autoware的模块。

主要描述了Autoware的整体框架和模块描述，主要包括感知和规划两大部分。

感知包括定位模块，检测模块，预测模块。

定位模块取决于使用3D map(高精度地图)、NDT和SLAM算法来实现，使用从CAN消息和GNSS/IMU传感器获取的里程计信息，通过Kalman滤波算法对定位结果进行补充。检测模块使用摄像头和激光雷达，结合传感器融合算法和深度学习网络进行目标检测。

检测模块使用深度学习和传感器融合算法，使用摄像头和Lidar设备结合3D高精度地图数据实现检测。

预测模块使用定位和检测的结果来预测跟踪目标。通过卡尔曼滤波算法和3D高精度地图实现。

规划模块主要是基于感知的输出结果，进行全局路径规划和局部路径规划。全局路径规划在车辆启动或重启的时候被确定，局部路径根据车辆的状态进行实时更新。例如，如果车辆在障碍物前或停止线前，车辆状态变为“stop”，那么车辆的速度就被规划为0。如果车辆遇到一个障碍物且状态为“avoid”,那么局部跟踪路径就会被重新规划绕过障碍物。规划是基于概率机器人技术和基于规则的系统，部分功能使用深度学习方法。

控制模块通过速度和角度(以及曲率)的扭曲来定义车辆的运动，经常采用PID和MPC算法

主要模块如下所示：

**Localization:定位**

- lidar_localizar 计算车辆当在全局坐标的当前位置(x,y,z,roll,pitch,yaw)，使用LIDAR的扫描数据和预先构建的地图信息。autoware推荐使用正态分布变换(NDT)算法来匹配激光雷达当前帧和3D map。
- gnss_localizer 转换GNSS接收器发来的NEMA消息到位置信息(x,y,z,roll,pitch,yaw)。结果可以被单独使用为车辆当前位置，也可以作为lidar_localizar的初始参考位置。
- dead_reckoner 主要使用IMU传感器预测车辆的下一帧位置，也可以用来对lidar_localizar和gnss_localizar的结果进行插值。

**Detection:检测**

- lidar_detector 从激光雷达单帧扫描读取点云信息，提供基于激光雷达的目标检测。主要使用欧几里德聚类算法，从地面以上的点云得到聚类结果。除此之外，可以使用基于卷积神经网路的算法进行分类，包括VoxelNet,LMNet.
- image_detector 读取来自摄像头的图片，提供基于图像的目标检测。主要的算法包括R-CNN，SSD和Yolo，可以进行多类别(汽车，行人等)实时目标检测。
- image_tracker 使用image_detector的检测结果完成目标跟踪功能。算法基于Beyond Pixels，图像上的目标跟踪结果被投影到3D空间，结合lidar_detector的检测结果输出最终的目标跟踪结果。
- fusion_detector 输入激光雷达的单帧扫描点云和摄像头的图片信息，进行在3D空间的更准确的目标检测。激光雷达的位置和摄像头的位置需要提前进行联合标定，现在主要是基于MV3D算法来实现。
- fusion_tools 将lidar_detector和image_detector的检测结果进行融合，image_detector 的识别类别被添加到lidar_detector的聚类结果上。
- object_tracter 预测检测目标的下一步位置，跟踪的结果可以被进一步用于目标行为分析和目标速度分析。跟踪算法主要是基于卡尔曼滤波器。

**Prediction:预测**

- moving_predictor 使用目标跟踪的结果来预测临近物体的未来行动轨迹，例如汽车或者行人。
- collision_predictor 使用moving_predictor的结果来进一步预测未来是否会与跟踪目标发生碰撞。输入的信息包括车辆的跟踪轨迹，车辆的速度信息和目标跟踪信息。

**Misson planning：任务规划**

- route_planner 寻找到达目标地点的全局路径，路径由道路网中的一系列十字路口组成。
- lane_planner 根据route_planner发布的一系列十字路口结果，确定全局路径由哪些lane组成，lane是由一系列waypoint点组成
- waypoint_planner 可以被用于产生到达目的地的一系列waypont点，它与lane_planner的不同之处在于它是发布单一的到达目的地的waypoint路径,而lane_planner是发布到达目的地的一系列waypoint数组。
- waypoint_maker 是一个保存和加载手动制作的waypoint文件的工具。为了保存waypoint到文件里，需要手动驾驶车辆并开启定位模块，然后记录车辆的一系列定位信息以及速度信息， 被记录的信息汇总成为一个路径文件，之后可以加载这个本地文件，并发布需要跟踪的轨迹路径信息给其他规划模块。

**Motion planning:运动规划**

- velovity_planner 更新车辆速度信息，注意到给定跟踪的waypoint里面是带有速度信息的，这个模块就是根据车辆的实际状态进一步修正速度信息，以便于实现在停止线前面停止下来或者加减速等等。
- astar_planner 实现Hybrid-State A*查找算法，生成从现在位置到指定位置的可行轨迹，这个模块可以实现避障，或者在给定waypoint下的急转弯，也包括在自由空间内的自动停车。
- adas_lattice_planner 实现了State Lattice规划算法，基于样条曲线，事先定义好的参数列表和语义地图信息，在当前位置前方产生了多条可行路径，可以被用来进行障碍物避障或车道线换道。
- waypoint_follower 这个模块实现了 Pure Pursuit算法来实现轨迹跟踪，可以产生一系列的控制指令来移动车辆，这个模块发出的控制消息可以被车辆控制模块订阅，或者被线控接口订阅，最终就可以实现车辆自动控制。

**Sensing**

- Autoware支持相机（Camera），激光雷达（LiDAR），惯导（IMU）和GPS作为基础传感器，并提供了多种传感器的驱动
- Autoware支持多个相机，但是每个相机需要被分配到独立的工作，同时不支持将多张图像融合成为一张图像（即不支持全景拼图）
- Autoware的传感器部分主要基于激光雷达，也支持毫米波雷达用于长距离的目标跟踪，但将其整合到感知工具包的工作仍未完成
- Autoware认为虽然在基于SLAM的定位工作中，无需IMU也能表现良好，但是IMU在某些场景应用中仍然适用。

## Actuation:执行模块

Autoware已安装并通过许多有线车辆进行了测试。Autoware的计算输出是一组速度、角速度、轮角和曲率。这些信息通过车辆接口作为命令发送给有线控制器。控制转向和油门需要由线控器来控制。