- [Apollo学习笔记7-感知融合_hello1268的博客-CSDN博客_感知融合](https://blog.csdn.net/hello1268/article/details/116779235)

# 一、Apollo感知模块概述

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518153006280.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

## Input

**The perception module inputs are:**

- 128 channel LiDAR data (cyber channel /apollo/sensor/velodyne128)
- 16 channel LiDAR data (cyber channel /apollo/sensor/lidar_front,
  lidar_rear_left, lidar_rear_right)
- Radar data (cyber channel /apollo/sensor/radar_front, radar_rear)
- Image data (cyber channel /apollo/sensor/camera/front_6mm,
  front_12mm)
- Extrinsic parameters of radar sensor calibration (from YAML files)
- Extrinsic and Intrinsic parameters of front camera calibration (from
  YAML files)
- Velocity and Angular Velocity of host vehicle (cyber channel
  /apollo/localization/pose)

## Output

**The perception module outputs are:**

- The 3D obstacle tracks with the heading, velocity and classification
  information (cyber channel /apollo/perception/obstacles)
- The output of traffic light detection and recognition (cyber channel
  /apollo/perception/traffic_light)

**主要用到的传感器类型包括相机、激光雷达和毫米波雷达，相机和激光雷达的目标检测部分都是利用深度学习网络完成，然后都进行了目标跟踪，最后设计了一个融合模块，用来融合三种传感器跟踪后的目标序列，获得更加稳定可靠的感知结果**。

感知模块大体上包括：
3D障碍物感知： 主要有是三个主要部分提供3D障碍物感知：`lidar`、`radar`和`fusion`模块。
Radar感知： 毫米波雷达检测与跟踪
`Obstacle Results Fusion`：
用来融合lidar和radar的检测结果。
融合中有publish-[sensor](https://so.csdn.net/so/search?q=sensor&spm=1001.2101.3001.7020)的概念，apollo中`publish-sensor`是lidar，即对`radar`结果进行缓存，`lidar`结果用来触发融合动作，因此融合输出频率等于publish-sensor的频率。Apollo保留所有传感器结果，每个融合目标丢失存活时间根据传感器不同，一个目标必须至少有一个传感器结果存活。Apollo在近距离里提供`lidar`和`radar`的融合结果，在远距离只有radar结果。

传感器结果到融合序列关联： ==传感器目标与融合目标进行匹配时，首先匹配相同传感器相同的跟踪ID，然后建立二分图，对没有匹配上的那些结果进行匈牙利匹配，距离损失矩阵是通过计算anchor点的欧氏距离完成。

`Motion Fusion`： 使用基于自适应卡尔曼滤波的CA模型做运动估计，运动状态量包括`anchor point`位置、速度和加速度。在激光跟踪和radar检测中提供位置和速度的不确定性，将所有的这些状态和不确定量输入给自适应卡尔曼滤波，来得到融合结果。滤波过程使用击穿阈值来抵消更新增益的过估计。

# 二、文件结构分析

有必要新增一下关于感知融合模块的代码结构说明。因为有时候不知道哪里是入口，不知道从哪里看起。

```
Apollo/modules/perception
perception
├── base    // 一些基础类
├── BUILD
├── camera  //相机检测
├── common  // 公用的类
├── data    // 一些相机的内外参
├── fusion  // 障碍物融合
├── inference //一些接口
├── lib
├── lidar   // 激光雷达检测
├── map     // 高精度地图
├── onboard // 组件文件结构和组件类的实现
├── Perception_README_3_5.md
├── production  // 组件配置文件和启动文件
├── proto       // 一些protobuf的消息结构
├── radar       // 毫米波雷达
├── README.md
└── tool        // 一些工具方法
```

`base`——整个感知模块公用的一些基础类型定义，比如表示感知目标的基础类类型；
`camera`——相机处理子模块；
`common`——整个感知模块公用的一些基础操作定义，如点云预处理、图像预处理等；
`data`——感知模块用到的数据，目前只有相机的标定参数yaml文件；
`fusion`——融合处理子模块；
`interface`——整个感知模块的一些公用接口定义，目前里面好像都是视觉感知与显卡的一些的接口；
`lib`——整个感知模块公用的一些算法定义，如config_manager参数配置操作；
`datlidara`——激光雷达处理子模块；
`map`——高精度地图的一些操作，主要在感知模块中用于提取感兴趣区域；
`onboard`——上车运行程序，其中component文件夹可以认为是感知模块的入口；
`production`——感知模块所有配置参数定义；
`proto`——感知模块protobuf文件定义；
`radar`——毫米波雷达处理子模块

**1. production**

```
Apollo/modules/perception/production
```

Launch文件定义了模块的启动，dag定义了模块的依赖关系。launch文件中包含了dag文件，每个dag文件中又包含了很多个子组件（component）、相应的动态库和配置文件，每个子组件对应的是相应的类实现。
如 `launch/perception_all.launch`包含了如下：

```
- dag_streaming_perception.dag
- DetectionComponent //dag文件中包含的组件，对应onboard文件夹中的component文件夹
- RecognitionComponent
- RadarDetectionComponent
- FusionComponent
- V2XFusionComponent
- dag_streaming_perception_camera.dag
 - ...
- dag_streaming_perception_trafficlights.dag
- ...
- dag_motion_service.dag
- ...
```

**2. onboard**

文件夹结构如下：

```
Apollo/modules/perception/onboard
├── onboard
│   ├── common_flags
│   ├── component  //感知融合的组件实现
│   ├── inner_component_messages
│   ├── msg_buffer
│   ├── msg_serializer
│   ├── proto
│   └── transform_wrapper
```

主要是component文件夹：

```
Apollo/modules/perception/onboard/component/
├── BUILD //定义了 perception 中所有的 component 如 camera,radar,lidar 等的  信息
├── camera_perception_viz_message.cc
├── camera_perception_viz_message.h
├── detection_component.cc // 激光雷达检测
├── detection_component.h
├── fusion_camera_detection_component.cc
├── fusion_camera_detection_component.h
├── fusion_component.cc //融合
├── fusion_component.h
├── lane_detection_component.cc
├── lane_detection_component.h
├── lidar_inner_component_messages.h
├── lidar_output_component.cc
├── lidar_output_component.h
├── radar_detection_component.cc
├── radar_detection_component.h
├── recognition_component.cc
├── recognition_component.h
├── segmentation_component.cc
├── segmentation_component.h
├── trafficlights_perception_component.cc
└── trafficlights_perception_component.h
```

其中对应了每个组件的执行实现，包括初始化配置参数和处理函数，当然这些组件只是个入口，具体实现还是要看每个组件里面对应的算法类的process函数，后面便会进入每个组件的文件夹，如fusion_component.cc对应的具体实现就在fusion文件夹中的app：

```
Apollo/modules/perception/fusion
fusion
├── app 入口
├── base 定义的数据类(需清晰了解)
├── common 证据推理，IF，KF等方法
└── lib
```

反正是一层套一层，封装的比较好.

# 三、融合入口类

## 1.fusion下基础类含义说明

在利用不同传感器感知结果来进行目标级融合时，会涉及到对很多单个目标、数据帧所有目标以及多帧目标等数据的存储问题，因此会定义很多数据结构来完成这个工作，这给阅读源码带来了很多困惑，因此此处首先对这些基础自定义数据结构进行一个简单总结，方便后面的解释。

这些基础类主要定义在`fusion/base`文件夹下：
`SensorObject`类——某单一传感器的一个目标，其实就是包含了某个传感器的相关属性定义和一个base::Object成员变量；
`SensorFrame`类——某传感器的一帧所有目标数据，定义了`std::vector<SensorObject>`成员变量；
`Sensor`类——某传感器历史多帧信息，定义有`std::deque<SensorFramePtr>`类型成员变量，并提供获取最新数据帧的一些接口函数；
`SensorDataManager`类——最终在`ProbabilisticFusion`类中作为成员变量用来存储`<sensor_id, SensorPtr>`键值对，即存储多个传感器的多帧历史信息的类，定义有`std::unorderd_map<std::string, SensorPtr> sensors_`成员变量，作为`ProbabilisticFusion`类的数据输入缓存；
`Track`类——单个跟踪目标，在该类中完成大部分跟踪目标状态更新工作；
`Scene`类——前景和背景跟踪目标的管理，定义有`std::vector<TrackPtr> forground_tracks_`和`std::vector<TrackPtr> background_tracks_； PbfTracker`类——单个跟踪目标，也是主要的融合算法发生过程所在，定义了不同的融合算法类。

## 2.融合程序入口逻辑

上面提到了感知模块所有子模块的入口类都定义在 `modules/perception/onboard/component` 文件夹下对应源文件中，如融合模块的入口类就是定义在 `modules/perception/onboard/component/fusion_component.h` 中的FusionComponent类。
输入： `SensorFrameMessage` 类型消息；
输出： `PerceptionObstacles` 类型消息；
`FusionComponent<` 类的参数配置protobuf文件为：`modules/perception/onboard/proto/fusion_component_config.proto` ，具体参数实现定义在文件 `modules/perception/production/conf/perception/fusion/fusion_component_conf.pb.txt` 中：

**配置参数**

```cpp
fusion_method: "ProbabilisticFusion"
fusion_main_sensor: "velodyne16"
object_in_roi_check: true
radius_for_roi_object_check: 120
output_obstacles_channel_name: "/apollo/perception/obstacles"
output_viz_fused_content_channel_name: "/perception/inner/visualization/FusedObjects"
```

从配置参数可以看出，融合的主传感器设置为`velodyne128`，融合方法使用的是`ProbabilisticFusion`，发布融合后目标障碍物消息的话题名为`/apollo/perception/obstacles`。

`FusionComponent`类主要负责接收融合所需要的来自不同传感器的目标序列消息，然后利用`ProbabilisticFusion`融合类完成实际融合工作，得到融合后感知结果，最后把融合结果在`FusionComponent`类中发布，供其他模块使用。

**入口**
注意处理类的初始化`init`函数，主要是注意配置参数

`fusion_component`里的`fusion_`成员对象是`ObstacleMultiSensorFusion`类定义的，有`fusion_`相应的`init`函数，会传入上述的配置参数

`frame`是一帧传感器数据，fused_objects是最后融合后的结果

```cpp
modules/perception/onboard/component/fusion_component.cc

fusion_->Process(frame, &fused_objects);
```

配置项
`ProbabilisticFusion`类里面`init()`函数的主要读取内容：

```cpp
modules/perception/production/data/perception/fusion/probabilistic_fusion.pt
use_lidar: true
use_radar: true
use_camera: true
tracker_method: "PbfTracker"
data_association_method: "HMAssociation"
gate_keeper_method: "PbfGatekeeper"
prohibition_sensors: "radar_front"

max_lidar_invisible_period: 0.25
max_radar_invisible_period: 0.50
max_camera_invisible_period: 0.75

max_cached_frame_num: 50
```

核心函数：`Fuse`函数

```cpp
modules/perception/fusion/app/obstacle_multi_sensor_fusion.cc

bool ObstacleMultiSensorFusion::Process(const base::FrameConstPtr& frame,
                                        std::vector<base::ObjectPtr>* objects) {
  FusionOptions options;
  return fusion_->Fuse(options, frame, objects); //ProbabilisticFusion
}
```

**执行融合**

```cpp
  // 3. perform fusion on related frames
  for (const auto& frame : frames) {
    FuseFrame(frame);
  }
```

**融合逻辑**

```cpp
void ProbabilisticFusion::FuseFrame(const SensorFramePtr& frame) {
  AINFO << "Fusing frame: " << frame->GetSensorId()
        << ", foreground_object_number: "
        << frame->GetForegroundObjects().size()
        << ", background_object_number: "
        << frame->GetBackgroundObjects().size()
        << ", timestamp: " << GLOG_TIMESTAMP(frame->GetTimestamp());
  this->FuseForegroundTrack(frame);
  this->FusebackgroundTrack(frame);
  this->RemoveLostTrack();
}
```

因为主要是看感知融合的算法实现，也就是 `camera, lidar, radar, fusion` 文件夹，另外 `onboard, production` 两个文件夹就是入口，从这里出发看代码，流程会比较清楚一点。
顺序应该是：`production/*.launch -> production/*.dag -> onboard/component/*componet.cc -> fusion/app`等

**主要的流程图如下：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518154104563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)
**一个比较全的流程图**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210518153042227.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

# 四、重要结构体解读

## 1、部分重要结构体解读

第一个输入参数对frame，结构体Frame为[perception/base/frame.h](https://blog.csdn.net/hello1268/article/details/perception/base/frame.h).

```cpp
struct alignas(16) Frame {
  EIGEN_MAKE_ALIGNED_OPERATOR_NEW

  Frame() { sensor2world_pose.setIdentity(); }

  void Reset() {
    timestamp = 0.0;
    objects.clear();
    sensor2world_pose.setIdentity();
    sensor_info.Reset();
    lidar_frame_supplement.Reset();
    radar_frame_supplement.Reset();
    camera_frame_supplement.Reset();
  }
  // @brief sensor information
  SensorInfo sensor_info; //传感器信息

  double timestamp = 0.0;  //帧时间戳
  std::vector<std::shared_ptr<Object>> objects;  //障碍物
  Eigen::Affine3d sensor2world_pose;  //时变的传感器到世界的旋转变换？（目前不清楚什么意思，可能与动态标定相关）

  // sensor-specific frame supplements
  LidarFrameSupplement lidar_frame_supplement; //结构体定义如下所示，主要包括是否开启和原始数据的指针
  RadarFrameSupplement radar_frame_supplement;
  CameraFrameSupplement camera_frame_supplement;
  UltrasonicFrameSupplement ultrasonic_frame_supplement;
};
```

SensorInfo的结构体定义在[/perception/base/sensor_meta.h](https://gitee.com/ApolloAuto/apollo/blob/r6.0.0/modules/perception/base/sensor_meta.h).

```cpp
struct SensorInfo {
  std::string name = "UNKNONW_SENSOR";  //传感器名称
  SensorType type = SensorType::UNKNOWN_SENSOR_TYPE;  //传感器类型
  SensorOrientation orientation = SensorOrientation::FRONT;  //传感器安装位置
  std::string frame_id = "UNKNOWN_FRAME_ID";  //?? 帧id字符串（可能与传感器数据帧的结构有关）
  void Reset() {
    name = "UNKNONW_SENSOR";
    type = SensorType::UNKNOWN_SENSOR_TYPE;
    orientation = SensorOrientation::FRONT;
    frame_id = "UNKNOWN_FRAME_ID";
  }
};
```

四个FrameSupplement的结构体定义在
[/perception/base/frame_supplement](https://gitee.com/ApolloAuto/apollo/blob/r6.0.0/modules/perception/base/frame_supplement.h).

```cpp
struct alignas(16) LidarFrameSupplement {
  // @brief valid only when on_use = true
  bool on_use = false;

  // @brief only reference of the original cloud in lidar coordinate system
  std::shared_ptr<AttributePointCloud<PointF>> cloud_ptr;

  void Reset() {
    on_use = false;
    cloud_ptr = nullptr;
  }
};

struct alignas(16) RadarFrameSupplement {
  // @brief valid only when on_use = true
  bool on_use = false;
  void Reset() { on_use = false; }
};

struct alignas(16) CameraFrameSupplement {
  // @brief valid only when on_use = true
  bool on_use = false;

  // @brief only reference of the image data
  Image8UPtr image_ptr = nullptr;

  // TODO(guiyilin): modify interfaces of visualizer, use Image8U
  std::shared_ptr<Blob<uint8_t>> image_blob = nullptr; //??图像块？这是什么？

  void Reset() {
    on_use = false;
    image_ptr = nullptr;
    image_blob = nullptr;
  }
};

struct alignas(16) UltrasonicFrameSupplement {
  // @brief valid only when on_use = true
  bool on_use = false;

  // @brief only reference of the image data
  std::shared_ptr<ImpendingCollisionEdges> impending_collision_edges_ptr;

  void Reset() {
    on_use = false;
    impending_collision_edges_ptr = nullptr;
  }
};
```

第二个输入参数对objects，结构体Object[（perception/base/object.h）](https://gitee.com/ApolloAuto/apollo/blob/r6.0.0/modules/perception/base/object.h)

```cpp
struct alignas(16) Object {
  EIGEN_MAKE_ALIGNED_OPERATOR_NEW

  Object();
  std::string ToString() const;
  void Reset();

  // @brief object id per frame, required
  int id = -1;

  // @brief convex hull of the object, required
  PointCloud<PointD> polygon;

  // oriented boundingbox information
  // @brief main direction of the object, required
  Eigen::Vector3f direction = Eigen::Vector3f(1, 0, 0);
  /*@brief the yaw angle, theta = 0.0 <=> direction(1, 0, 0),
    currently roll and pitch are not considered,
    make sure direction and theta are consistent, required
  */
  float theta = 0.0f;
  // @brief theta variance, required
  float theta_variance = 0.0f;
  // @brief center of the boundingbox (cx, cy, cz), required
  Eigen::Vector3d center = Eigen::Vector3d(0, 0, 0);
  // @brief covariance matrix of the center uncertainty, required
  Eigen::Matrix3f center_uncertainty;
  /* @brief size = [length, width, height] of boundingbox
     length is the size of the main direction, required
  */
  Eigen::Vector3f size = Eigen::Vector3f(0, 0, 0);
  // @brief size variance, required
  Eigen::Vector3f size_variance = Eigen::Vector3f(0, 0, 0);
  // @brief anchor point, required
  Eigen::Vector3d anchor_point = Eigen::Vector3d(0, 0, 0);

  // @brief object type, required
  ObjectType type = ObjectType::UNKNOWN;
  // @brief probability for each type, required
  std::vector<float> type_probs;

  // @brief object sub-type, optional
  ObjectSubType sub_type = ObjectSubType::UNKNOWN;
  // @brief probability for each sub-type, optional
  std::vector<float> sub_type_probs;

  // @brief existence confidence, required
  float confidence = 1.0f;

  // tracking information
  // @brief track id, required
  int track_id = -1;
  // @brief velocity of the object, required
  Eigen::Vector3f velocity = Eigen::Vector3f(0, 0, 0);
  // @brief covariance matrix of the velocity uncertainty, required
  Eigen::Matrix3f velocity_uncertainty;
  // @brief if the velocity estimation is converged, true by default
  bool velocity_converged = true;
  // @brief velocity confidence, required
  float velocity_confidence = 1.0f;
  // @brief acceleration of the object, required
  Eigen::Vector3f acceleration = Eigen::Vector3f(0, 0, 0);
  // @brief covariance matrix of the acceleration uncertainty, required
  Eigen::Matrix3f acceleration_uncertainty;

  // @brief age of the tracked object, required
  double tracking_time = 0.0;
  // @brief timestamp of latest measurement, required
  double latest_tracked_time = 0.0;

  // @brief motion state of the tracked object, required
  MotionState motion_state = MotionState::UNKNOWN;
  // // Tailgating (trajectory of objects)
  std::array<Eigen::Vector3d, 100> drops;
  std::size_t drop_num = 0;
  // // CIPV
  bool b_cipv = false;
  // @brief brake light, left-turn light and right-turn light score, optional
  CarLight car_light;
  // @brief sensor-specific object supplements, optional
  LidarObjectSupplement lidar_supplement;
  RadarObjectSupplement radar_supplement;
  CameraObjectSupplement camera_supplement;
  FusionObjectSupplement fusion_supplement;

  // @debug feature to be used for semantic mapping
//  std::shared_ptr<apollo::prediction::Feature> feature;
};
```

## 2. 部分重要结构体表格

综上，`Process`的两个输入，第一个描述传感器帧的固有属性：

| 名称                     | 类型           | 含义                 |
| ------------------------ | -------------- | -------------------- |
| sensor_info              | sensor_info    | 传感器的固有信息     |
| timestamp                | timestamp      | 该帧的时间戳         |
| objects                  | Object指针向量 | 障碍物信息           |
| sensor2world_pose        |                | ？可能与动态标定相关 |
| 可选的四种传感器支持信息 |                | 四个传感器的支持信息 |

**第二个描述障碍物的属性：**

| 名称                                        | 类型               | 含义                   |
| ------------------------------------------- | ------------------ | ---------------------- |
| id                                          | int                | id号                   |
| polygon                                     | 多点               | 凸包                   |
| direction                                   | 3维向量            | 朝向                   |
| theta                                       | float              | yaw角                  |
| center_uncertainty                          | 3*3矩阵            | 中心点协方差矩阵       |
| size                                        | 3维向量            | 长宽高                 |
| size_variance                               | 3维向量            | 长宽高的不确定度       |
| type                                        | 枚举               | 类型                   |
| type_probs                                  | float向量          | 每种类型的概率         |
| sub_type                                    | 枚举               | 子类型                 |
| sub_type_probs                              | float向量          | 子类型概率             |
| confidence                                  | float              | 存在概率               |
| track_id                                    | int                | 轨迹id号               |
| velocity                                    | 3维向量            | 速度                   |
| velocity_uncerntainty                       | 3*3矩阵            | 速度协方差矩阵         |
| acceleration                                | 3维向量            | 加速度                 |
| acceleration_uncerntainty                   | 3*3矩阵            | 加速度协方差矩阵       |
| tracking_time                               | double             | 轨迹的累计时间         |
| latest_tracked_time                         | double             | 最后一次追踪到的时间   |
| motion_status                               | 枚举               | 运动状态               |
| drops                                       | 长度100的3维点数组 | 轨迹                   |
| drop_num                                    | size_t             | 轨迹长度               |
| b_cipv bool cipv标志位                      | 长bool             | cipv                   |
| car_light CarLight 车灯（包括可见性）       | CarLight           | 轨车灯（包括可见性     |
| 可选的四种传感器支持信息 车灯（包括可见性） | -                  | 传感器ObjectSupplement |

# 五、融合代码解析

## 1.保存数据

先保存传感器进来的一帧数据，保存最新数据到一个map结构中，map为每个sensor对应的数据队列。

注意每次进来数据后判断是不是主传感器velodyne16，有主传感器数据进来之后（started_ = true;）才开始保存数据；之后不是主传感器的数据都直接return掉，只有主传感器的数据进来才触发后续的处理。

也就是说处理周期以`velodyne128`的周期为准，期间`sensor_data_manager`会暂存几帧其他传感器的数据，直到`velodyne128`进来再进行后续处理。
具体传感器的频率是多少还没有找到

```cpp
  // 1. save frame data
  {
    std::lock_guard<std::mutex> data_lock(data_mutex_);
    // 三个if全是false
    if (!params_.use_lidar && sensor_data_manager->IsLidar(sensor_frame)) {
      return true;
    }
    if (!params_.use_radar && sensor_data_manager->IsRadar(sensor_frame)) {
      return true;
    }
    if (!params_.use_camera && sensor_data_manager->IsCamera(sensor_frame)) {
      return true;
    }

    // velodyne128
    bool is_publish_sensor = this->IsPublishSensor(sensor_frame);
    if (is_publish_sensor) {
      started_ = true;
    }

    // 有velodyne128进来才开始save？
    if (started_) {
      AINFO << "add sensor measurement: " << sensor_frame->sensor_info.name
            << ", obj_cnt : " << sensor_frame->objects.size() << ", "
            << FORMAT_TIMESTAMP(sensor_frame->timestamp);
      // 传感器数据管理，保存最新数据到一个map结构中，map为每个sensor对应的数据队列
      sensor_data_manager->AddSensorMeasurements(sensor_frame);
    }

    // 不是主传感器就return
    if (!is_publish_sensor) {
      return true;
    }
  }
```

## 2.查询

查询所有传感器的最新一帧数据，插入到`frames`中，按时间顺序排序好。

类`Sensor`里有类`SensorFrame`的一个队列成员，`sensor`类有该传感器的最新10帧数据，`SensorFrame`是其中的一帧数据

```cpp
  // 2. query related sensor_frames for fusion
  // 查询所有传感器的最新一帧数据，然后按时间排序好
  std::lock_guard<std::mutex> fuse_lock(fuse_mutex_);
  double fusion_time = sensor_frame->timestamp;
  std::vector<SensorFramePtr> frames;
  sensor_data_manager->GetLatestFrames(fusion_time, &frames);
```

## 3.融合最新一帧

```cpp
  // 3. perform fusion on related frames
  // 融合最新一帧
  for (const auto& frame : frames) {
    FuseFrame(frame);
  }
```

每一帧融合代码执行结构如下：

**FuseFrame()函数**

```cpp
void ProbabilisticFusion::FuseFrame(const SensorFramePtr& frame) {
  // 融合前景
  this->FuseForegroundTrack(frame);
  // 融合背景，主要是来自激光雷达的背景数据
  this->FusebackgroundTrack(frame);
  // 删除未更新的航迹
  this->RemoveLostTrack();
}
```

- FuseFrame
  - FuseForegroundTrack
    - matcher_->Associate
    - UpdateAssignedTracks
    - UpdateUnassignedTracks
    - CreateNewTracks
  - FusebackgroundTrack
  - RemoveLostTrack

### 3.1 FuseForegroundTrack函数

其中四个关键函数

```cpp
  // 关联匹配--HMTrackersObjectsAssociation
  AssociationOptions options;
  AssociationResult association_result;
  matcher_->Associate(options, frame, scenes_, &association_result);

  // 更新匹配的航迹
  const std::vector<TrackMeasurmentPair>& assignments =
      association_result.assignments;
  this->UpdateAssignedTracks(frame, assignments);

  // 更新未匹配的航迹
  const std::vector<size_t>& unassigned_track_inds =
      association_result.unassigned_tracks;
  this->UpdateUnassignedTracks(frame, unassigned_track_inds);

  // 未匹配上的量测新建航迹
  const std::vector<size_t>& unassigned_obj_inds =
      association_result.unassigned_measurements;
  this->CreateNewTracks(frame, unassigned_obj_inds);
```

#### 3.1.1 HMTrackersObjectsAssociation::Associate函数

关联匹配的代码剖析放在另一篇文章 [Apollo perception源码分析–fusion–Associate关联匹配](https://blog.csdn.net/weixin_43152152/article/details/115175675).

#### 3.1.2 UpdateAssignedTracks

匹配上的结果做更新，使用观测更新tracker，tracker类型是pbf_tracker，

```cpp
void ProbabilisticFusion::UpdateAssignedTracks(
    const SensorFramePtr& frame,
    const std::vector<TrackMeasurmentPair>& assignments) {
  TrackerOptions options;
  options.match_distance = 0;
  for (size_t i = 0; i < assignments.size(); ++i) {
    size_t track_ind = assignments[i].first;
    size_t obj_ind = assignments[i].second;
        
    //pbf_tracker,观测更新tracker
    trackers_[track_ind]->UpdateWithMeasurement(
        options, frame->GetForegroundObjects()[obj_ind], frame->GetTimestamp());
  }
}
```

tracker更新的函数中会更新四个部分，`existence`、`motion`、`shape`、`type`和`tracker`的信息，前四个`fusion`的配置参数在`modules/perception/proto/pbf_tracker_config.proto`，就是init中的默认值。

```cpp
// 观测更新tracker
void PbfTracker::UpdateWithMeasurement(const TrackerOptions& options,
                                       const SensorObjectPtr measurement,
                                       double target_timestamp) {
  std::string sensor_id = measurement->GetSensorId();
  ADEBUG << "fusion_updating..." << track_->GetTrackId() << " with "
         << sensor_id << "..." << measurement->GetBaseObject()->track_id << "@"
         << FORMAT_TIMESTAMP(measurement->GetTimestamp());
  
  // options.match_distance = 0
  // DstExistenceFusion
  // 证据推理（DS theory）更新tracker的存在性
  existence_fusion_->UpdateWithMeasurement(measurement, target_timestamp,
                                           options.match_distance);
  // KalmanMotionFusion
  // 鲁棒卡尔曼滤波更新tracker的运动属性                                     
  motion_fusion_->UpdateWithMeasurement(measurement, target_timestamp);

  // PbfShapeFusion
  // 更新tracker的形状
  shape_fusion_->UpdateWithMeasurement(measurement, target_timestamp);

  // DstTypeFusion
  // 证据推理（DS theory）更新tracker的属性
  type_fusion_->UpdateWithMeasurement(measurement, target_timestamp);

  track_->UpdateWithSensorObject(measurement);
}
```

主要是`DS theory`和`Kalman`更新`tracker`的属性，再往里写文章就太长了，具体的细节后续更新文章TODO

#### 3.1.3 UpdateUnassignedTracks

同上`UpdateAssignedTracks`一样，对没有匹配到观测的`tracker`，更新同样的参数

```cpp
// 没有观测时更新tracker
void PbfTracker::UpdateWithoutMeasurement(const TrackerOptions& options,
                                          const std::string& sensor_id,
                                          double measurement_timestamp,
                                          double target_timestamp) {
  existence_fusion_->UpdateWithoutMeasurement(sensor_id, measurement_timestamp,
                                              target_timestamp,
                                              options.match_distance);
  motion_fusion_->UpdateWithoutMeasurement(sensor_id, measurement_timestamp,
                                           target_timestamp);
  shape_fusion_->UpdateWithoutMeasurement(sensor_id, measurement_timestamp,
                                          target_timestamp);
  type_fusion_->UpdateWithoutMeasurement(sensor_id, measurement_timestamp,
                                         target_timestamp,
                                         options.match_distance);
  track_->UpdateWithoutSensorObject(sensor_id, measurement_timestamp);
}
```

#### 3.1.4 CreateNewTracks

对没有匹配到`tracker`的观测`object`，新建航迹`tracker`，主要是最后的两个Init函数。可以详细看下`Track`和`BaseTracker`两个类
`track<tracker<trackers`

```cpp
void ProbabilisticFusion::CreateNewTracks(
    const SensorFramePtr& frame,
    const std::vector<size_t>& unassigned_obj_inds) {
  for (size_t i = 0; i < unassigned_obj_inds.size(); ++i) {
    size_t obj_ind = unassigned_obj_inds[i];

    bool prohibition_sensor_flag = false;
    // 泛型，radar_front不新建航迹
    std::for_each(params_.prohibition_sensors.begin(),
                  params_.prohibition_sensors.end(),
                  [&](std::string sensor_name) {
                    if (sensor_name == frame->GetSensorId())
                      prohibition_sensor_flag = true;
                  });
    if (prohibition_sensor_flag) {
      continue;
    }
    // 新建track，并初始化,添加到scenes_中
    TrackPtr track = TrackPool::Instance().Get();
    track->Initialize(frame->GetForegroundObjects()[obj_ind]);
    scenes_->AddForegroundTrack(track);

    // PbfTracker：新建tracker，track初始化tracker，tracker插入到航迹集合trackers_中
    if (params_.tracker_method == "PbfTracker") {
      std::shared_ptr<BaseTracker> tracker;
      tracker.reset(new PbfTracker());
      tracker->Init(track, frame->GetForegroundObjects()[obj_ind]);
      trackers_.emplace_back(tracker);
    }
  }
}
```

### 3.2 FusebackgroundTrack函数

和FuseForegroundTrack函数的过程类似，处理函数也是，同样的四个步骤，关联->更新匹配的航迹->更新未匹配的航迹->新建航迹

***添加内容***

### 3.3 RemoveLostTrack函数

前景航迹和背景航迹，当该航迹所有匹配的传感器都没有更新过，移除掉该航迹

```cpp
void ProbabilisticFusion::RemoveLostTrack() {
  // need to remove tracker at the same time
  size_t foreground_track_count = 0;
  std::vector<TrackPtr>& foreground_tracks = scenes_->GetForegroundTracks();
  for (size_t i = 0; i < foreground_tracks.size(); ++i) {
    // track里面所有匹配过的传感器是否存在
    // 不存在就删掉，不能直接erase？
    if (foreground_tracks[i]->IsAlive()) {
      if (i != foreground_track_count) {
        foreground_tracks[foreground_track_count] = foreground_tracks[i];
        trackers_[foreground_track_count] = trackers_[i];
      }
      foreground_track_count++;
    }
  }
  foreground_tracks.resize(foreground_track_count);
  trackers_.resize(foreground_track_count);

  // only need to remove frame track
  size_t background_track_count = 0;
  std::vector<TrackPtr>& background_tracks = scenes_->GetBackgroundTracks();
  for (size_t i = 0; i < background_tracks.size(); ++i) {
    if (background_tracks[i]->IsAlive()) {
      if (i != background_track_count) {
        background_tracks[background_track_count] = background_tracks[i];
      }
      background_track_count++;
    }
  }
  background_tracks.resize(background_track_count);
}
```

4.融合结果后处理

```cpp
  // 4. collect fused objects
  // 规则门限过滤，最新匹配更新过track的obj放入到fused_objects中，并publish
  CollectFusedObjects(fusion_time, fused_objects);
```

# 六、参考文章

## fusion模块

***主要是融合的框架代码，代码注释量比较大，分成多篇文章来写了***
[自动驾驶 Apollo 源码分析系列，感知篇(八)：感知融合代码的基本流程](https://frank909.blog.csdn.net/article/details/118101596?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-4.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-4.pc_relevant_default&utm_relevant_index=8).

关联匹配：
1、[Apollo perception fusion感知融合源码分析–Associate关联匹配](https://blog.csdn.net/weixin_43152152/article/details/115175675)
2、[Apollo 5.0源码学习笔记（三）| 感知模块 | 融合模块 | 数据关联](https://blog.csdn.net/zhanghm1995/article/details/104910923/?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-4&spm=1001.2101.3001.4242)
3、[自动驾驶 Apollo 源码分析系列，感知篇(九)：感知融合中的数据关联细节](https://frank909.blog.csdn.net/article/details/118250139?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~default-1-118250139-blog-118101596.pc_relevant_scanpaymentv1&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2~default~CTRLIST~default-1-118250139-blog-118101596.pc_relevant_scanpaymentv1&utm_relevant_index=1)

匈牙利匹配算法: [Apollo perception fusion感知融合源码分析–匈牙利匹配](https://blog.csdn.net/weixin_43152152/article/details/114235820).

卡尔曼滤波:[Apollo perception fusion感知融合源码分析–卡尔曼滤波](https://blog.csdn.net/weixin_43152152/article/details/115308664)

证据推理： [Apollo perception fusion感知融合源码分析–证据推理](https://blog.csdn.net/weixin_43152152/article/details/115648285)

## 部分参考文章

链接: [自动驾驶 Apollo 源码分析系列，感知篇(二)：Perception 如何启动？](https://frank909.blog.csdn.net/article/details/111598755).
关于Apollo启动模块的一些内容，更好的了解函数从哪里开始执行的

[Apollo的感知融合模块解析](https://blog.csdn.net/u012423865/article/details/80386444?spm=1001.2014.3001.5501).
这是一篇2.5版本的解析，算法结构是差不多的，流程图比较详尽，没有过多的代码细节，文章比较短，容易理解整个过程。

[Apollo 5.0源码学习笔记（二）| 感知模块 | 融合模块](https://blog.csdn.net/zhanghm1995/article/details/103539976).

[Apollo perception源码阅读 | fusion](https://blog.csdn.net/weixin_43152152/article/details/114836882).