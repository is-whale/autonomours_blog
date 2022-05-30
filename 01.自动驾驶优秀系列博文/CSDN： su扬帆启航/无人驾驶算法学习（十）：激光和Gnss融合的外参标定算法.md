- [无人驾驶算法学习（十）：激光和Gnss融合的外参标定算法_su扬帆启航的博客-CSDN博客_激光外参标定](https://blog.csdn.net/orange_littlegirl/article/details/93890552)

# 1.lidar_align功能包

一种寻找三维激光雷达与六自由度姿态传感器外部[标定](https://so.csdn.net/so/search?q=标定&spm=1001.2101.3001.7020)的简单方法，[项目原链接下载](https://github.com/ethz-asl/lidar_align)。

> 该工具来自瑞士苏黎世联邦理工大学的自动驾驶实验室。其实验室对imu与其他传感器融合传感定位建图的理论及项目研究处于世界领先水平，其代表作还包括摄像头与imu融合传感的OKVIS、ROVIO。此次lidar_align主要针对激光雷达与imu（或任意6自由度位姿传感器）外参坐标系标定问题。

**注意:准确的结果需要高度非平面运动，这使得该技术不适用于校准安装在汽车上的传感器。**

该方法利用了来自lidars的点云在校准正确时看起来更“清晰”的特性。它是这样做的:
1)设置激光雷达与位姿传感器之间的转换。
2)将pose与上述变换相结合，将所有激光雷达点融合成一个点云。
3)求出每一点与其最近邻点云之间的距离之和。
这个过程在一个优化过程中重复，这个优化过程试图找到最小化这个距离的转换。

## 1.1安装

1.安装 [ROS Indigo](http://wiki.ros.org/indigo/Installation/Ubuntu), [ROS Kinetic](http://wiki.ros.org/kinetic/Installation/Ubuntu) or [ROS Melodic](http://wiki.ros.org/melodic/Installation/Ubuntu).

2.安装非线性优化依赖

```
sudo apt-get install libnlopt-dev
```

## 1.2输入里程记信息

imu或者gnss（也就是里程记）输入的是类似geometry_msgs::TransformStamped的消息类型，也就是我们关注的是积分后的平移量和旋转量。
最终的标定质量与转换源的质量和观测到的运动范围密切相关。为了确保准确的校准，数据集应该包含大量的旋转和平移。近似规划器的运动(例如，一辆汽车沿着街道行驶)不会在垂直于平面的方向上提供关于系统的任何信息，这将导致优化器在这个方向上给出不正确的估计。

## 1.3外参估计程序（Estimation proceedure）

对于大多数系统，可以在不调优参数的情况下运行节点。默认情况下执行两种优化，粗略的角度只进行全局优化，然后进行局部6自由度优化。

- 1.节点将从给定的ROS包中加载所有类型为“sensor_msgs/PointCloud2”的消息，用作激光雷达扫描处理。
- 2.姿态可以在与“geometry_msgs/TransformStamped”消息相同的包文件中给出,或者改成相应的话题处理也行,也可以在单独的CSV文件中给出，格式为Maplab

## 1.4 可视化结果

节点将在运行时输出其当前估计的转换。要查看此启动文件，必须在“”部分中设置“output=“screen”。有关示例，请参见给定的launch文件。

一旦优化完成，转换参数将被打印到控制台。示例输出如下:

```
Active Transformation Vector (x,y,z,rx,ry,rz) from the Pose Sensor Frame to  the Lidar Frame:
[-0.0608575, -0.0758112, 0.27089, 0.00371254, 0.00872398, 1.60227]

Active Transformation Matrix from the Pose Sensor Frame to  the Lidar Frame:
-0.0314953  -0.999473  0.0078319 -0.0608575
  0.999499 -0.0314702 0.00330021 -0.0758112
 -0.003052 0.00793192   0.999964    0.27089
         0          0          0          1

Active Translation Vector (x,y,z) from the Pose Sensor Frame to  the Lidar Frame:
[-0.0608575, -0.0758112, 0.27089]

Active Hamiltonen Quaternion (w,x,y,z) the Pose Sensor Frame to  the Lidar Frame:
[0.69588, 0.00166397, 0.00391012, 0.718145]

Time offset that must be added to lidar timestamps in seconds:
0.00594481

ROS Static TF Publisher: <node pkg="tf" type="static_transform_publisher" name="pose_lidar_broadcaster" args="-0.0608575 -0.0758112 0.27089 0.00166397 0.00391012 0.718145 0.69588 POSE_FRAME LIDAR_FRAME 100" />
```

如果设置了路径，结果也将保存到文本文件中。作为评估对齐质量的一种方法，如果设置了所需的路径，用于对齐的所有点都将被投影到单个点云中，并保存为一层。

# 2.标定原理

> 首先，激光雷达扫描的每一个点，记录的数据都是根据激光发射和接收的时间差、激光强度差、激光发射的偏航角、俯仰角计算而得，其默认的坐标系是此时刻以激光雷达为原点的坐标系，但激光雷达在整个扫描过程中是运动的，所以如果以运动出发点为世界坐标系原点，获得各时刻激光雷达扫描到的点在世界坐标系下的绝对坐标的方法有2个：
> 1.( R T ) (RT)(*R**T*)表示从imu到lidar的坐标变换，世界坐标系为lidar的初始时刻坐标，( R T ) − 1 (RT)^{−1}(*R**T*)−1是imu初始坐标系
> imu做积分可获得任一时刻相对初始位姿的位姿**变换矩阵C**（里程记消息），则 ( R T ) − 1 C ( R T ) (RT)^{−1}C(RT)(*R**T*)−1*C*(*R**T*)表示此时刻激光雷达原点位姿在世界坐标系中的表示，假设某一点在激光雷达坐标系下的表示为S，则其在世界坐标系下的绝对坐标为( R T ) − 1 C ( R T ) S (RT)^{−1}C(RT)S(*R**T*)−1*C*(*R**T*)*S*。
> 2.点云扫描前后2帧有很大一部分点云重合，利用点云配准算法，可以估计前后2帧之间的移动转动，从而将每2帧之间的变换C i​Ci​*C**i*​估计出来，∏ i = 0 n − 1 C i S ∏^{n−1}_{i=0}CiS∏*i*=0*n*−1​*C**i**S*表示任一点在世界绝对坐标系下的坐标。
> 我们使用ICP最近邻迭代算法将2种表示方法描述的同一片点云区域配准，构建并优化最近邻误差，这里不直接对获得的laser坐标系构建优化目标，而对坐标系内的点云构建优化目标，运用了统计误差平均效应，直至收敛，获得RT变换矩阵的估计值，从而获得激光雷达与imu的外参变换矩阵。
> 原理示意图：
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190627211342205.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

# 2.标定显示

## 2.1外参显示

利用本程序标定lidar和gnss的外参，只需要更改loader.cpp中的话题类型函数，标定成功后得到如下外参文件：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190627210235453.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

## 2.2标定结果

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190627210318737.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)