- [【Apollo 6.0项目实战】Localization模块_Travis.X的博客-CSDN博客](https://blog.csdn.net/Travis_X/article/details/121768756)

# 前言

环境：

- Ubuntu 20.04
- Apollo 6.0
- LGSVL仿真器

## [Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) 6.0软件框架

![在这里插入图片描述](https://img-blog.csdnimg.cn/66d527b5163d444abd699023053db962.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

- Perception——感知模块识别自动驾驶汽车周围的环境。感知模块内部包含两个重要的子模块：障碍物检测和交通灯检测。
- Prediction——预测模块用来预测与感知障碍物未来的运动轨迹。
- Routing——路由模块告诉自动驾驶汽车通过全局路径到达目的地。
- Planning——规划模块规划自动驾驶汽车要采取的时空轨迹。
- Control——控制模块通过产生油门、刹车和转向等控制命令来执行计划的时空轨迹。
- CanBus —— CanBus 是将控制命令传递给车辆硬件的接口。它还将机箱信息传递给软件系统。
- HD-Map——该模块类似于库。它不是发布和订阅消息，而是经常用作查询引擎支持，以提供关于道路的特定结构化信息。
- **Localization——定位模块利用各种信息源（如 GPS、LiDAR 和 IMU）来估计自动驾驶汽车的位置。**
- HMI——Apollo 中的人机界面或 DreamView 是用于查看车辆状态、测试其他模块和实时控制车辆功能的模块。
- Monitor——车辆中所有模块的监控系统，包括硬件。
- Guardian——新的安全模块，用于干预监控检测到的失败和action center相应的功能。 执行操作中心功能并进行干预的新安全模块应监控检测故障。
- Storytelling——隔离和管理复杂场景的新模块，创建可触发多个模块操作的Story。所有其他模块都可以订阅此特定模块。

本文讲解的是 Apollo 中的 Localization 模块。当前， Apollo 提供的定位方案有三种，分别是 RTK（Real Time Kinematic）定位模块、MSF（Multi-[Sensor](https://so.csdn.net/so/search?q=Sensor&spm=1001.2101.3001.7020) Fusion）定位模块以及 NDT（Normal Distribution Transform）定位模块。本文中重点讲解的是MSF（Multi-Sensor Fusion）定位模块。

------

# 一、MSF 定位模块简介

高精度、高鲁棒性的定位系统是自动驾驶系统不可或缺的基础模块。定位模块的作用是为 planning 模块提供车辆的位置信息，以及为 control 模块提供车辆的姿态，速度信息。

MSF 定位模块结合 GPS + IMU + Lidar 实现的多传感器融合全局定位，利用多传感器优缺点的互补，实现高精度、高鲁棒性的定位能力。对于 GPS 失效或者 Lidar 地图环境变更场景具备一定的冗余处理能力。本模块可提供城市道路、高速、部分隧道等场景下的定位能力。

## 1.1 MSF 定位模块原理框架

![在这里插入图片描述](https://img-blog.csdnimg.cn/fc620b8fb0c74980a8243b588be2b6da.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
MSF 定位系统以多种传感器数据和离线制作的高精度 Lidar 定位地图为输入，其中 GNSS Localization 模块以车端 GPS 信号和基站数据为输入，输出高精度 RTK 定位结果。LiDAR Localization 模块以在线 lidar 扫描数据和高精度 Lidar 定位地图为输入，提供高精度 lidar 定位结果。SINS 模块利用IMU数据进行惯性导航。后端采用 Error-state Kalman filter 融合多种传感器量测信息。最后输出高精度的车辆位置和姿态。

## 1.2 MSF 定位模块的输入输出

![在这里插入图片描述](https://img-blog.csdnimg.cn/04fab16f0f5c4b748069d9ee5d0f6cb6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

| channel名称                                    | 输入输出 | 说明             |
| ---------------------------------------------- | -------- | ---------------- |
| /apollo/sensor/hesai40/compensator/PointCloud2 | 输入     | 融合后的点云数据 |
| /apollo/sensor/gnss/imu                        | 输入     | IMU 消息         |
| /apollo/sensor/gnss/odometry                   | 输入     | 里程计消息       |
| /apollo/sensor/gnss/best_pose                  | 输入     | GPS 位置         |
| /apollo/sensor/gnss/heading                    | 输入     | GPS 航向角       |
| /apollo/localization/msf_gnss                  | 输出     | GNSS 定位结果    |
| /apollo/localization/msf_lidar                 | 输出     | Lidar 定位结果   |
| /apollo/localization/pose                      | 输出     | 融合定位结果     |
| /apollo/localization/msf_status                | 输出     | 融合定位状态     |

------

# 二、多传感器融合定位实践

本节利用数据集进行 msf 定位实践。

## 2.1 下载数据

终端上执行以下指令下载完整的定位数据集

```cpp
# 下载数据集
wget https://apollo-system.cdn.bcebos.com/dataset/localization/demo-localization-data-3.5.tar.gz

# 解压数据集
tar -xvf demo-localization-data-apollo-3.5.tar.gz
```

解压数据集后会产生四个文件夹，分别为local_map（MSF Localization 定位地图）、ndt_map（NDT Localization 定位地图）、params（车辆参数）和 records（bag 数据）。

------

## 2.2 参数配置

为了使定位模块正确运行，需要对传感器外参和地图参数进行配置。

### 2.2.1 配置传感器外参

解压下载的数据集 params 文件夹下里包含两个文件夹 **gnss_params** 和 **velodyne_params**。

- gnss_params
  - ant_imu_leverarm.yaml: # 杆臂值参数，GNSS 天线相对 Imu 的距离
- velodyne_params
  - velodyne128_novatel_extrinsics.yaml: # Lidar 相对 Imu 的外参
  - velodyne128_height.yaml: # LiDAR 相对于地面的高度

运行以下命令，拷贝车辆参数到定位模块目录下：

```cpp
cp -r DATA_PATH/params/* /apollo/modules/localization/msf/params/
```

**注意**：DATA_PATH 代表定位数据集的路径。

### 2.2.2 配置地图参数

打开 /apollo/modules/localization/conf/localization.conf 文件并修改 --map_dir 字段：

```cpp
# 使用 vim 打开 localization.conf
vim /apollo/modules/localization/conf/localization.conf

# 移动到第五行，修改 --map_dir 字段
--map_dir=DATA_PATH
```

## 2.3 启动 Apollo docker

```cpp
cd apollo/

./docker/scripts/dev_start.sh

./docker/scripts/dev_into.sh

./scripts/bootstrap.sh
```

打开Dreamview http://localhost:8888/，在上方选择对应的模式、车型以及地图（根据自己的仿真环境选择相应的地图）。

```cpp
cd DATA_PATH/records
cyber_recorder play -f record.*
```

注意：DATA_PATH 下载定位 demo 数据的路径。
正常显示如下
![在这里插入图片描述](https://img-blog.csdnimg.cn/3bdc5670e9274bcbaffadcce21c8797c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/1da4b516c0694494b3075548a7e4c37e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
黑白的背景是地图，颜色表示环境中反射值：颜色越亮表示反射值越高，颜色越暗表示反射值越低，纯黑色表示没有被激光雷达扫描到的区域。

白色车辆模型表示经过 MSF 融合算法得到的位置坐标，蓝色方框表示 GPS 位置，可以在视频中看到蓝色方框在不停跳动，但车辆位置比较稳定，说明融合定位算法提供了更好的平滑性。

------

# 三、 msf_visualizer 可视化工具

按照[官方教程](https://apollo.auto/document_cn.html?target=/Apollo-Homepage-Document/Apollo_Doc_CN_6_0/)对定位结果进行可视化，但是没有正常显示出来，弹出的窗口是黑屏和终端报错，目前尚未找到解决的方法，先将问题记录下来。

为 docker 配置 X-Server：

```cpp
sudo apt install x11-xserver-utils
```

执行以下命令启动可视化工具：

```cpp
cyber_launch start /apollo/modules/localization/launch/msf_visualizer.launch
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/50cb9252488c48098284ed0ecbe4bb3d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/0dea860c237c4aaea4cf12a0a61699e7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
而正常应该显示这样的结果
![在这里插入图片描述](https://img-blog.csdnimg.cn/73039fe0837544018fb5b467efad64cf.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
灰色背景表示地图，绿色细线表示原始点云数据，红色粗线圆圈表示 LiDAR 定位的置信度，绿色粗线圆圈表示 MSF 定位的置信度。

------

# 参考

【1】[Apollo定位能力介绍](https://apollo.auto/document_cn.html?target=/Apollo-Homepage-Document/Apollo_Doc_CN_6_0/)