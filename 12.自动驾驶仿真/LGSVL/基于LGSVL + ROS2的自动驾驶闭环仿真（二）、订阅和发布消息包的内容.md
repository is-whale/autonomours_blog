- [基于LGSVL + ROS2的自动驾驶闭环仿真（二）、订阅和发布消息包的内容 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/393079410)

首先是在终端查看ROS2消息定义相关的命令。

1、使用ros2 msg -h查看帮助。

```text
ros2 msg -h

output：
usage: ros2 msg [-h] Call `ros2 msg <command> -h` for more detailed usage. ...

Various msg related sub-commands

optional arguments:
  -h, --help            show this help message and exit

Commands:
  list      Output a list of available message types
  package   Output a list of available message types within one package
  packages  Output a list of packages which contain messages
  show      Output the message definition

  Call `ros2 msg <command> -h` for more detailed usage.
```

2、使用ros2 msg list列出所有的消息。

```text
ros2 msg list
```

3、使用ros2 msg show [消息名称]来展示出该消息的定义。

例如获取lgsvl_msgs/msg/CanBusData的消息定义，后面也会展开说一下消息的含义。

```text
ros2 msg show lgsvl_msgs/msg/CanBusData

output：
std_msgs/Header header

float32 speed_mps
float32 throttle_pct  # 0 to 1
float32 brake_pct     # 0 to 1
float32 steer_pct     # -1 to 1
bool parking_brake_active
bool high_beams_active
bool low_beams_active
bool hazard_lights_active
bool fog_lights_active
bool left_turn_signal_active
bool right_turn_signal_active
...
```



------

下面是一些查看ROS2 Topic相关的命令(**不用启动我们上一篇创建的ROS节点**)：

我们先把LGSVL仿真器跑起来。

![img](https://pic2.zhimg.com/80/v2-7c3f9b9306d06b44aa0bbe6c908bbc91_720w.jpg)

能够看到lgsvl-bridge已经显示了订阅与发布的消息。

![img](https://pic3.zhimg.com/80/v2-132bebfb76d6d366835925a3771a4486_720w.jpg)

1、使用ros2 topic -h查看帮助，后面主要测试Commands里面的一些命令。

```text
ros2 topic -h

output:
usage: ros2 topic [-h] [--include-hidden-topics]
                  Call `ros2 topic <command> -h` for more detailed usage. ...

Various topic related sub-commands

optional arguments:
  -h, --help            show this help message and exit
  --include-hidden-topics
                        Consider hidden topics as well

Commands:
  bw     Display bandwidth used by topic
  delay  Display delay of topic from timestamp in header
  echo   Output messages from a topic
  hz     Print the average publishing rate to screen
  info   Print information about a topic
  list   Output a list of available topics
  pub    Publish a message to a topic

  Call `ros2 topic <command> -h` for more detailed usage.
```

2、使用ros2 topic list列出所有活动的话题，能够看到我们订阅与发布的话题。

```text
ros2 topic list

output:
...
/simulator/canbus
/simulator/ground_truth/m3d_detections
/simulator/ground_truth/signals
/simulator/sensor/camera/center/image/compressed
/simulator/vehicle_control
/simulator/vehicle_state
```

3、以/simulator/canbus话题为例使用ros2 topic bw [话题名称]查看该话题使用的带宽，下同。

```text
ros2 topic bw /simulator/canbus

output:
Subscribed to [/simulator/canbus]
average: 1.36KB/s
        mean: 0.14KB min: 0.14KB max: 0.14KB window: 6
average: 1.39KB/s
        mean: 0.14KB min: 0.14KB max: 0.14KB window: 16
average: 1.39KB/s
        mean: 0.14KB min: 0.14KB max: 0.14KB window: 26
average: 1.39KB/s
        mean: 0.14KB min: 0.14KB max: 0.14KB window: 36
```

4、使用ros2 topic delay [话题名称]查看该话题的延迟。

```text
ros2 topic delay /simulator/canbus
```

5、使用ros2 topic hz [话题名称]查看该话题发布的频率。

```text
ros2 topic hz /simulator/canbus
```

6、使用ros2 topic info [话题名称]查看话题信息。

```text
ros2 topic info /simulator/canbus

output：
Topic: /simulator/canbus
Publisher count: 1  
Subscriber count: 0   发布者与订阅者计数
```

7、使用ros2 topic echo [话题名称]实时输出话题订阅的消息。

```text
ros2 topic echo /simulator/canbus

output：
---
header:
  stamp:
    sec: 1627284390
    nanosec: 579887104
  frame_id: canbus
speed_mps: 3.918106017408718e-07
throttle_pct: 0.0
brake_pct: 0.0
steer_pct: 0.0
parking_brake_active: false
high_beams_active: false
low_beams_active: false
hazard_lights_active: false
fog_lights_active: false
left_turn_signal_active: false
right_turn_signal_active: false
wipers_active: false
reverse_gear_active: false
selected_gear: 1
engine_active: true
engine_rpm: 800.0053100585938
gps_latitude: 37.35241193503326
gps_longitude: -121.95264345185514
gps_altitude: 0.34882116317749023
orientation:
  x: -2.495944215752388e-07
  y: 1.0856904708589354e-07
  z: 1.0
  w: 2.3075416577533758e-10
linear_velocities:
  x: 2.7719487150079658e-08
  y: -3.8743019104003906e-07
  z: 5.142988257489378e-08
---
```

------

下面对几个主要的发布、订阅的消息包内容做简单说明。包括:

- sensor_msgs/msg/CompressedImage
- lgsvl_msgs/msg/CanBusData
- lgsvl_msgs/msg/Detection3DArray
- lgsvl_msgs/msg/SignalArray
- lgsvl_msgs/msg/VehicleStateData
- lgsvl_msgs/msg/VehicleControlData

**1、sensor_msgs/msgs/CompressedImage**

该消息包存储的是压缩后的图像信息，从其名字就可以看出来，使用如下命令可以获得其详细内容:

```text
ros2 msg show sensor_msgs/msg/CompressedImage
```

其内容主要包括消息头，存储的图像数据格式，以及图像数据。

**2、lgsvl_msgs/msg/CanBusData**

该消息存储的是车辆底盘信息，内容较为丰富，使用如下命令可以获得其详细内容:

```text
ros2 msg show lgsvl_msgs/msg/CanBusData
```

主要包括:

- float32 speed_mps # 速度m/s
- float32 throttle_pct # 0 to 1 油门
- float32 brake_pct # 0 to 1 刹车
- float32 steer_pct # -1 to 1 转向
- bool parking_brake_active # 停车制动状态
- bool high_beams_active # 远光灯状态
- bool low_beams_active # 近光灯状态
- bool hazard_lights_active # 警示灯状态
- bool fog_lights_active # 雾灯状态
- bool left_turn_signal_active # 左转信号灯
- bool right_turn_signal_active # 右转信号灯
- bool wipers_active # 雨刷状态
- bool reverse_gear_active # 倒车档
- int8 selected_gear # 档位选择
- bool engine_active # 发动机状态
- float32 engine_rpm # 发动机转速r/min
- float64 gps_latitude # 维度
- float64 gps_longitude # 经度
- float64 gps_altitude # 海拔
- geometry_msgs/Quaternion orientation # 朝向（四元素）
- geometry_msgs/Vector3 linear_velocities # 线速度

**3、lgsvl_msgs/msg/Detection3DArray**

该消息包内包含一组检测到的车辆周围的物体，主要是其他交通参与者等，使用如下命令可以获得其详细内容:

```text
ros2 msg show lgsvl_msgs/msg/Detection3DArray
```

然后，对于每一个检测到的物体，使用如下命令查看:

```text
ros2 msg show lgsvl_msgs/msg/Detection3D
```

对于每一个Detection3D其主要包括

- uint32 id # 检测到物体的标识ID
- string label # 检测到物体的标签，主要是行人，车辆
- float32 score # 检测到物体的置信度，是0到1之间的一个值
- BoundingBox3D bbox # 该物体的3D包围框
- geometry_msgs/Twist velocity # Linear and angular velocity # 该物体的线速度和角速度

**4、lgsvl_msgs/msg/SignalArray**

该消息包内包含检测到的信号灯信息，与上面类似，仍然是一组信息数据，使用如下命令可以获得其详细内容:

```text
ros2 msg show lgsvl_msgs/msg/SignalArray
```

然后，对于每一个检测到的信号灯，使用如下命令查看:

```text
ros2 msg show lgsvl_msgs/msg/Signal
```

对于每一个Signal其主要包括

- uint32 id # 检测到信号灯的标识ID
- string label # 检测到信号灯的状态，红灯、黄灯、绿灯
- float32 score # 检测到信号灯的置信度，是0到1之间的一个值
- BoundingBox3D bbox # 该信号灯的3D包围框

上面的四个都是我们从LGSVL仿真器中订阅的消息包，借助于这四个消息包基本就可以实现自动驾驶车辆的决策、规划模块等。

下面两个是我们发布给LGSVL 仿真器的消息包，主要是车辆状态和车辆控制，这样就可以让车跑起来了，其实逻辑还是较为简单。

**5、lgsvl_msgs/msg/VehicleStateData**

该消息包内包含了常用的车辆状态，使用如下命令可以获得其详细内容:

```text
ros2 msg show lgsvl_msgs/msg/VehicleStateData
```

其主要包括:

- uint8 blinker_state # 闪光灯/警戒灯状态
- uint8 headlight_state # 大灯状态
- uint8 wiper_state # 雨刷
- uint8 current_gear # 当前档位
- uint8 vehicle_mode # 驾驶模式
- bool hand_brake_active #手刹状态
- bool horn_active # 喇叭
- bool autonomous_mode_active # 自动驾驶状态

**6、lgsvl_msgs/msg/VehicleControlData**

该消息包内包含了常用的车辆控制，使用如下命令可以获得其详细内容:

```text
ros2 msg show lgsvl_msgs/msg/VehicleControlData
```

其主要包括:

- float32 acceleration_pct # 0 to 1 加速度
- float32 braking_pct # 0 to 1 刹车制动
- float32 target_wheel_angle # radians 目标车轮角度（弧度）
- float32 target_wheel_angular_rate # radians / second 目标车轮角度比
- uint8 target_gear # 目标档位

------

最后，应当能够通过C++ 或者Python调用来获取这些消息值，我们简单的说一下，其实上一篇代码里面就有写到获取时间戳信息的方式。

**1、C++的方式**

- 假设我们现在有了一个对象指针用来存储订阅的消息。

我们以SignalArray消息为例，如下所示:

```text
const lgsvl_msgs::msg::SignalArray::ConstSharedPtr& signal_msg；
```

那么我们可以按照如下方式获得消息内容:

```text
std：:string signals_frame_id = signal_msg->header.frame_id;   // 当前帧ID
auto signals_timestamp = signal_msg->header.stamp;   // 时间戳

unsigned_int signal_id = （signal_msg->signals）[0].id;   // ID
float signal_bbox_sizeX = （signal_msg->signals）[0].bbox.size.x;  // 包围框的X方向长度
float signal_bbox_posOrentX = （signal_msg->signals）[0].bbox.position.orientation.x;  // 包围框朝向四元数X 
```

- 那么如何写一个发布的消息包呢，以VehicleControlData为例，如下所示:

```text
auto control = lgsvl_msgs::msg::VehicleControlData();
control.target_gear = lgsvl_msgs::msg::VehicleControlData::GEAR_DRIVE; //前进档位
control.acceleration_pct = 1;  //加速度
```

**2、Python的方式**

Python方式与C++较为类似，最大的区别应该是Python不用显式声明类型。

- 订阅

比如我们现在有一个对象用来存储订阅的Detection3DArray消息，。

```text
# sub_3D_ground_truth_array
_sec = sub_3D_ground_truth_array.header.stamp.sec   # 时间戳-秒
_label = sub_3D_ground_truth_array.detections[0].label  # 标签
_score = sub_3D_ground_truth_array.detections[0].score  # 置信度
_velocityX = sub_3D_ground_truth_array.detections[0].velocity.angular.y  # Y方向的角度
```

- 发布

和C++的方式更加类似，如下所示

```text
state = VehicleStateData()
state.autonomous_mode_active = True # 自动驾驶
state.vehicle_mode= VehicleStateData.VEHICLE_MODE_COMPLETE_AUTO_DRIVE   # 驾驶模式
self.state_pub.publish(state)
```