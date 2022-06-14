- [【Apollo 6.0学习笔记】Apollo模块_Travis.X的博客-CSDN博客](https://blog.csdn.net/Travis_X/article/details/121872314)

## [Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) 6.0软件框架

![在这里插入图片描述](https://img-blog.csdnimg.cn/66d527b5163d444abd699023053db962.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

- Perception——感知模块识别自动驾驶汽车周围的环境。感知模块内部包含两个重要的子模块：障碍物检测和交通灯检测。
- Prediction——预测模块用来预测与感知障碍物未来的运动轨迹。
- Routing——路由模块告诉自动驾驶汽车通过全局路径到达目的地。
- Planning——规划模块规划自动驾驶汽车要采取的时空轨迹。
- Control——控制模块通过产生油门、刹车和转向等控制命令来执行计划的时空轨迹。
- CanBus —— CanBus 是将控制命令传递给车辆硬件的接口。它还将机箱信息传递给软件系统。
- HD-Map——该模块类似于库。它不是发布和订阅消息，而是经常用作查询引擎支持，以提供关于道路的特定结构化信息。
- Localization——定位模块利用各种信息源（如 GPS、LiDAR 和 IMU）来估计自动驾驶汽车的位置。
- HMI——Apollo 中的人机界面或 DreamView 是用于查看车辆状态、测试其他模块和实时控制车辆功能的模块。
- Monitor——车辆中所有模块的监控系统，包括硬件。
- Guardian——新的安全模块，用于干预监控检测到的失败和action center相应的功能.执行操作中心功能并进行干预的新安全模块应监控检测故障。
- Storytelling——隔离和管理复杂场景的新模块，创建可触发多个模块操作的Story。所有其他模块都可以订阅此特定模块。

------

# Apollo 模块接口

| Module       | Topic                            | Description                              | Protobuf Interface                                           | Fields provided by simulation                                |
| ------------ | -------------------------------- | ---------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Canbus       | /apollo/canbus/chassis           | 输出主车速度、驾驶模式等数据             | [proto file](https://github.com/ApolloAuto/apollo/blob/master/modules/canbus/proto/chassis.proto) | speed_mps                                                    |
| Localization | /apollo/localization/pose        | 输出主车位置、朝向等数据                 | [proto file](https://github.com/ApolloAuto/apollo/blob/master/modules/localization/proto/localization.proto) | position, orientation, heading, linear_velocity, linear_acceleration, angular_velocity |
| Perception   | /apollo/perception/obstacles     | 输出各障碍物位置、朝向、速度、形状等数据 | [proto file](https://github.com/ApolloAuto/apollo/blob/master/modules/perception/proto/perception_obstacle.proto) | id, position, heading, velocity, length, width, height, type, polygon points |
| Perception   | /apollo/perception/traffic_light | 输出红绿灯信号                           | [proto file](https://github.com/ApolloAuto/apollo/blob/master/modules/perception/proto/traffic_light_detection.proto) | color, id, tracking_time                                     |
| Prediction   | /apollo/prediction               | 输出各障碍物及其预测轨迹                 | [proto file](https://github.com/ApolloAuto/apollo/blob/master/modules/prediction/proto/prediction_obstacle.proto) | trajectory in PredictionObstacle                             |
| Routing      | /apollo/routing_response         | 输出导航结果                             | [proto file](https://github.com/ApolloAuto/apollo/blob/master/modules/routing/proto/routing.proto) | the entire routing response as defined by proto file         |
| Planning     | /apollo/planning                 | 输出规划出的主车未来一段时间的轨迹       | [proto file](https://github.com/ApolloAuto/apollo/blob/master/modules/planning/proto/planning.proto) | timestamp_sec in Header, v, a, relative_time in TrajectoryPoint,x, y, z, theta, kappa in PathPoint, MainDecision in DecisionResult, ObjectDecisions in DecisionResult |
| Control      | /apollo/control                  | 车辆底盘控制信息                         | [proto file](https://github.com/ApolloAuto/apollo/blob/master/modules/control/proto/control_cmd.proto) | header, throttle, brake, steering_target                     |

------

# Apollo 模块算法

| Module       | Algorithm                                                    |
| ------------ | ------------------------------------------------------------ |
| Localization | GNSS/IMU 、MSF、NDT                                          |
| Perception   | 激光雷达感知：启发式的Ncut、深度学习算法CNNSeg；视觉感知：深度学习+后处理计算 |
| Prediction   | 路口场景预测模型、语义地图预测模型、基于交互的预测模型、行人预测模型 |
| Routing      | A*                                                           |
| Planning     | RTK Planner、EM Planner、Lattice Planner、NAVI Planner、Open Space Planner |
| Control      | 横向：LQR；纵向：级联PID + 校准表                            |

------

# Apollo 模块上机实践

| Module       | Algorithm                                                    |
| ------------ | ------------------------------------------------------------ |
| HD-Map       | [【Apollo 6.0项目实战】HD-Map模块](https://blog.csdn.net/Travis_X/article/details/121486163) |
| Canbus       | [【Apollo 6.0项目实战】Canbus模块](https://blog.csdn.net/Travis_X/article/details/121539973) |
| Localization | [【Apollo 6.0项目实战】Localization模块](https://blog.csdn.net/Travis_X/article/details/121768756) |
| Perception   | [【Apollo 6.0项目实战】Perception模块](https://blog.csdn.net/Travis_X/article/details/121518854) |
| Prediction   | [【Apollo 6.0项目实战】Prediction模块](https://blog.csdn.net/Travis_X/article/details/121658184) |
| Routing      | [【Apollo 6.0项目实战】Routing模块](https://blog.csdn.net/Travis_X/article/details/121674874) |
| Planning     | [【Apollo 6.0项目实战】Planning模块](https://blog.csdn.net/Travis_X/article/details/121793238) |
| Control      | [【Apollo 6.0项目实战】Control模块](https://blog.csdn.net/Travis_X/article/details/121802955) |