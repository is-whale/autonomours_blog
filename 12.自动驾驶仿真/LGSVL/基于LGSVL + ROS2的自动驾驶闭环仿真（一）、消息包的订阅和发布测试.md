- [基于LGSVL + ROS2的自动驾驶闭环仿真（一）、消息包的订阅和发布测试 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/392768756)

1、新建一个文件夹作为我们的工作空间，也是ROS2 build的空间。

```text
mkdir lgsvl_simu
```

2、在该文件夹内创建一个ROS消息包，**下面都以C++ 包为例，最后给出Python包**。

```text
cd lgsvl_simu
ros2 pkg create --build-type ament_cmake autodriving  (C++包)
ros2 pkg create --build-type ament_python autodriving  (Python包)
```

3、查看都生成了哪些文件。

```text
tree

output:
.
└── autodriving
    ├── CMakeLists.txt
    ├── include
    │   └── autodriving
    ├── package.xml
    └── src

4 directories, 2 files
```

4、在src中创建一个用于订阅与发布消息的cpp文件。

```text
nano autodriving/src/msg_subpub.cpp
```

msg_subpub.cpp代码如下：

```cpp
#include <chrono>
#include <memory>
#include <boost/bind.hpp>
// 消息过滤与时间同步
#include <message_filters/subscriber.h>
#include <message_filters/synchronizer.h>
#include <message_filters/sync_policies/approximate_time.h>


// 自定义类继承该基类
#include "rclcpp/rclcpp.hpp"
#include "std_msgs/msg/string.hpp"

// 消息类型-订阅
#include "sensor_msgs/msg/compressed_image.hpp"     
#include "lgsvl_msgs/msg/detection3_d_array.hpp"
#include "lgsvl_msgs/msg/signal_array.hpp"
#include "lgsvl_msgs/msg/can_bus_data.hpp"
// 消息类型-发布
#include "lgsvl_msgs/msg/vehicle_state_data.hpp"
#include "lgsvl_msgs/msg/vehicle_control_data.hpp"


using namespace std::chrono_literals;
typedef message_filters::sync_policies::ApproximateTime<sensor_msgs::msg::CompressedImage, lgsvl_msgs::msg::Detection3DArray, 
                                                          lgsvl_msgs::msg::SignalArray, lgsvl_msgs::msg::CanBusData> MySyncPolicy;

class msgSubPub : public rclcpp::Node
{
  public:
    msgSubPub();
    ~msgSubPub();

  private:

    // 消息订阅的回调函数
    void subscriber_callback(const sensor_msgs::msg::CompressedImage::ConstSharedPtr& image_msg, const lgsvl_msgs::msg::Detection3DArray::ConstSharedPtr& groundturth_msg, 
                              const lgsvl_msgs::msg::SignalArray::ConstSharedPtr& signal_msg, const lgsvl_msgs::msg::CanBusData::ConstSharedPtr& canbus_msg);
    
    // 消息发布的回调函数
    void publisher_callback();

    // 消息订阅对象
    message_filters::Subscriber<sensor_msgs::msg::CompressedImage> image_sub;           // 摄像机传感器
    message_filters::Subscriber<lgsvl_msgs::msg::Detection3DArray> groundturth_sub;     // 3D 地面真相传感器
    message_filters::Subscriber<lgsvl_msgs::msg::SignalArray> signal_sub;               // 信号灯传感器
    message_filters::Subscriber<lgsvl_msgs::msg::CanBusData> canbus_sub;                // 车辆底盘传感器
    // 松时间同步
    message_filters::Synchronizer<MySyncPolicy> *sync = new message_filters::Synchronizer<MySyncPolicy>
                                                                (MySyncPolicy(10), image_sub, groundturth_sub, signal_sub, canbus_sub);

    // 消息发布对象
    rclcpp::Publisher<lgsvl_msgs::msg::VehicleStateData>::SharedPtr state_pub;           // 车辆状态
    rclcpp::Publisher<lgsvl_msgs::msg::VehicleControlData>::SharedPtr control_pub;      // 车辆控制
    // 发布定时
    rclcpp::TimerBase::SharedPtr timer_;
    size_t count_;
};

msgSubPub::msgSubPub() : Node("msg_publish_subscribe"), count_(0)
{
    // INFO
    RCLCPP_INFO(this->get_logger(), "Init message publish and subscribe node");

    // 订阅消息 ""中的内容必须和对应的传感器Topic相同。
    image_sub.subscribe(this, "/simulator/sensor/camera/center/image/compressed");
    groundturth_sub.subscribe(this, "/simulator/ground_truth/m3d_detections");
    signal_sub.subscribe(this, "/simulator/ground_truth/signals");
    canbus_sub.subscribe(this, "/simulator/canbus");
    // 注册回调函数
    sync -> registerCallback(boost::bind(&msgSubPub::subscriber_callback, this, _1, _2, _3, _4));

    // 发布消息
    state_pub = this->create_publisher<lgsvl_msgs::msg::VehicleStateData>("/simulator/vehicle_state", 10);
    control_pub = this->create_publisher<lgsvl_msgs::msg::VehicleControlData>("/simulator/vehicle_control", 10);

    timer_ = this->create_wall_timer(500ms, std::bind(&msgSubPub::publisher_callback, this));    // 定时执行发布的回调函数
}

msgSubPub::~msgSubPub()
{
    delete sync;
}

// 订阅者的回调函数
void msgSubPub::subscriber_callback(const sensor_msgs::msg::CompressedImage::ConstSharedPtr& image_msg, const lgsvl_msgs::msg::Detection3DArray::ConstSharedPtr& groundturth_msg, 
                          const lgsvl_msgs::msg::SignalArray::ConstSharedPtr& signal_msg, const lgsvl_msgs::msg::CanBusData::ConstSharedPtr& canbus_msg)
{
    // TEST
    RCLCPP_INFO(this->get_logger(), "Subscribed: Get 3D_ground_truth & signal & can_bus_data Message");

    RCLCPP_INFO(this->get_logger(), "scan stamp:%d - %d - %d - %d", image_msg->header.stamp.sec, groundturth_msg->header.stamp.sec, 
                                                                    signal_msg->header.stamp.sec, canbus_msg->header.stamp.sec);

    // Solve all of perception here...
    
}

// 发布者的回调函数
void msgSubPub::publisher_callback()
{
    // 车辆控制
    auto control = lgsvl_msgs::msg::VehicleControlData();
    control.target_gear = lgsvl_msgs::msg::VehicleControlData::GEAR_DRIVE; //前进档位
    control.acceleration_pct = 1;  //加速度
    // 车辆状态
    auto state = lgsvl_msgs::msg::VehicleStateData();
    state.autonomous_mode_active = true;
    state.vehicle_mode= lgsvl_msgs::msg::VehicleStateData::VEHICLE_MODE_COMPLETE_AUTO_DRIVE;   //驾驶模式

    state_pub->publish(state);
    control_pub->publish(control);

    RCLCPP_INFO(this->get_logger(), "Publishing: '%d'", count_++);
}


int main(int argc, char * argv[])
{
  rclcpp::init(argc, argv);
  rclcpp::spin(std::make_shared<msgSubPub>());
  rclcpp::shutdown();
  return 0;
}
```

5、同时，应当修改package.xml文件，注意对应关系，下同。

```xml
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>autodriving</name>
  <version>0.0.0</version>
  <description>TODO: Package description</description>
  <maintainer email="xxx@todo.todo">xxx</maintainer>
  <license>TODO: License declaration</license>

  <buildtool_depend>ament_cmake</buildtool_depend>

  <test_depend>ament_lint_auto</test_depend>
  <test_depend>ament_lint_common</test_depend>

  <export>
    <build_type>ament_cmake</build_type>

    <exec_depend>message_filters</exec_depend>
    <exec_depend>rclcpp</exec_depend>
    <exec_depend>std_msgs</exec_depend>
    <exec_depend>sensor_msgs</exec_depend>
    <exec_depend>lgsvl_msgs</exec_depend>

  </export>
</package>
```

6、同时，应当修改CMakeLists.txt文件。

```cmake
cmake_minimum_required(VERSION 3.5)
project(autodriving)

cmake_minimum_required(VERSION 3.5)
project(autodriving)

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)

find_package(message_filters REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(lgsvl_msgs REQUIRED)

add_executable(main_msg src/msg_subpub.cpp)
ament_target_dependencies(main_msg message_filters rclcpp std_msgs sensor_msgs lgsvl_msgs) 

install(TARGETS
  main_msg
  DESTINATION lib/${PROJECT_NAME})

ament_package()
```

7、OK，下面编译我们创建的autodriving包（注意一定是在lgsvl_simu目录下）。

```text
colcon build --packages-select autodriving

output:
Finished <<< autodriving [5.94s]                       
Summary: 1 package finished [6.06s]
```

8、开启LGSVL的桥接。

```text
source (path\to\bridge\repository)/install/setup.bash
lgsvl_bridge

output:
[INFO] [lgsvl-bridge]: Listening on port 9090 
```

9、到现在我们ROS端的所有准备都已经完成。

------

下面是LGSVL仿真器端的准备工作，我们以[最新发布版](https://link.zhihu.com/?target=https%3A//github.com/lgsvl/simulator/releases/download/2021.2/svlsimulator-linux64-2021.2.zip)为例：

1、启动仿真器，下载完成后解压，双击simulator图标运行。

![img](https://pic4.zhimg.com/80/v2-dfe2aa42ff7b844407e9614fd045a853_720w.jpg)

2、我们使用在线仿真，点击“OPEN BROWSER”打开浏览器，首次使用最新版本需要注册一个账号，这里不再赘述。注册好之后的界面如下（这个就是新版本全新的webUI界面）：

![img](https://pic2.zhimg.com/80/v2-fc1d886c39aca2c63ce1d4acc822c1f9_720w.jpg)

3、点击左侧Vehicles，并找到如下图所示的车辆（当然，也可以是你喜欢的任意一辆车，如果点开后没有任何车辆，去左侧的Store中下载）。

![img](https://pic3.zhimg.com/80/v2-0d69d3f2006be27dc15fb63a62cdc56e_720w.jpg)

4、给车辆新加一个我们用于测试的传感器配置。

（注意：Topic要和上面msg_subpub.cpp中的相同，例如图片上的Signal Sensor的Topic（**/simulator/ground_truth/signals**）

就和signal_sub.subscribe(this,"**/simulator/ground_truth/signals**")相同）。

![img](https://pic3.zhimg.com/80/v2-a9891323d9e890ee4f7170fa8210d5ba_720w.jpg)

可以自己按照上图添加，或者直接在下图所示的Raw中粘贴上下面的传感器JSON配置。

![img](https://pic3.zhimg.com/80/v2-b1eb17bd0e46422d4b78854338963136_720w.jpg)

```json
[
  {
    "params": {
      "Topic": "/simulator/vehicle_control",
      "Frame": "AD_car_control"
    },
    "name": "AD car control",
    "parent": null,
    "pluginId": "a5696615-d3e2-45e9-afa3-9b5ae7359e02",
    "plugin": {
      "assetGuid": null,
      "isFavored": true,
      "isShared": false,
      "isOwned": false,
      "accessInfo": {
        "userAccessType": "favored",
        "owner": {
          "id": "0d888b00-fa53-47c1-882a-b68391268a11",
          "firstName": "SVL",
          "lastName": "Content"
        }
      },
      "supportedSimulatorVersions": [
        "2021.1",
        "2021.1.1",
        "2021.2"
      ],
      "id": "a5696615-d3e2-45e9-afa3-9b5ae7359e02",
      "name": "LGSVL Control Sensor",
      "type": "LGSVLControlSensor",
      "category": "sensor",
      "ownerId": "0d888b00-fa53-47c1-882a-b68391268a11",
      "accessType": "public",
      "description": "Example template of a sensor for vehicle Control information.",
      "copyright": "LG Electronics Inc.",
      "licenseName": "LG Content",
      "status": "active",
      "owner": {
        "id": "0d888b00-fa53-47c1-882a-b68391268a11",
        "firstName": "SVL",
        "lastName": "Content"
      },
      "shareRequests": []
    },
    "type": "LGSVLControlSensor"
  },
  {
    "params": {
      "Topic": "/simulator/canbus",
      "Frame": "canbus"
    },
    "name": "CAN Bus",
    "parent": null,
    "pluginId": "b0bdc474-ac09-4355-901d-d7012a8c57a8",
    "plugin": {
      "assetGuid": null,
      "isFavored": true,
      "isShared": false,
      "isOwned": false,
      "accessInfo": {
        "userAccessType": "favored",
        "owner": {
          "id": "0d888b00-fa53-47c1-882a-b68391268a11",
          "firstName": "SVL",
          "lastName": "Content"
        }
      },
      "supportedSimulatorVersions": [
        "2021.1",
        "2021.1.1",
        "2021.2"
      ],
      "id": "b0bdc474-ac09-4355-901d-d7012a8c57a8",
      "name": "CAN Bus Sensor",
      "type": "CanBusSensor",
      "category": "sensor",
      "ownerId": "0d888b00-fa53-47c1-882a-b68391268a11",
      "accessType": "public",
      "description": "This sensor sends data about the vehicle chassis. The data includes:\n\t* Speed [m/s]\n\t* Throttle [%]\n\t* Braking [%]\n\t* Steering [+/- %]\n\t* Parking Brake Status [bool]\n\t* High Beam Status [bool]\n\t* Low Beam Status [bool]\n\t* Hazard Light Status [bool]\n\t* Fog Light Status [bool]\n\t* Left Turn Signal Status [bool]\n\t* Right Turn Signal Status [bool]\n\t* Wiper Status [bool]\n\t* Reverse Gear Status [bool]\n\t* Selected Gear [Int]\n\t* Engine Status [bool]\n\t* Engine RPM [RPM]\n\t* GPS Latitude [Latitude]\n\t* GPS Longitude [Longitude]\n\t* Altitude [m]\n\t* Orientation [3D Vector of Euler angles]\n\t* Velocity [3D Vector of m/s]\nSee https://www.svlsimulator.com/docs/simulation-content/sensors-list/#can-bus for more details.",
      "copyright": "LG Electronics Inc.",
      "licenseName": "LG Content",
      "status": "active",
      "owner": {
        "id": "0d888b00-fa53-47c1-882a-b68391268a11",
        "firstName": "SVL",
        "lastName": "Content"
      },
      "shareRequests": []
    },
    "type": "CanBusSensor"
  },
  {
    "params": {
      "MaxDistance": 200,
      "Topic": "/simulator/ground_truth/m3d_detections",
      "Frame": "3D_ground_truth"
    },
    "name": "3D Ground Truth",
    "parent": null,
    "pluginId": "e3fef197-9724-48ec-b98e-c5a0892d09c4",
    "plugin": {
      "assetGuid": null,
      "isFavored": true,
      "isShared": false,
      "isOwned": false,
      "accessInfo": {
        "userAccessType": "favored",
        "owner": {
          "id": "0d888b00-fa53-47c1-882a-b68391268a11",
          "firstName": "SVL",
          "lastName": "Content"
        }
      },
      "supportedSimulatorVersions": [
        "2021.1",
        "2021.1.1",
        "2021.2"
      ],
      "id": "e3fef197-9724-48ec-b98e-c5a0892d09c4",
      "name": "3D Ground Truth Sensor",
      "type": "PerceptionSensor3D",
      "category": "sensor",
      "ownerId": "0d888b00-fa53-47c1-882a-b68391268a11",
      "accessType": "public",
      "description": "This sensor returns 3D ground truth data for training and creates bounding boxes around the detected objects. The color of the object corresponds to the object's type:\n\t[\n\t\t\"Car\": \"Green\"\n\t\t\"Pedestrian\": \"Yellow\"\n\t\t\"Bicycle\": \"Cyan\"\n\t\t\"Unknown\":  \"Magenta\"\n\t]\nSee https://www.svlsimulator.com/docs/simulation-content/sensors-list/#3d-ground-truth for more details.",
      "copyright": "LG Electronics Inc.",
      "licenseName": "LG Content",
      "status": "active",
      "owner": {
        "id": "0d888b00-fa53-47c1-882a-b68391268a11",
        "firstName": "SVL",
        "lastName": "Content"
      },
      "shareRequests": []
    },
    "type": "PerceptionSensor3D"
  },
  {
    "params": {
      "Topic": "/simulator/ground_truth/signals",
      "Frame": "signal_sensor"
    },
    "name": "Signal Sensor",
    "parent": null,
    "pluginId": "c4ba9b81-b274-4c05-b78e-636e61b4590e",
    "plugin": {
      "assetGuid": null,
      "isFavored": true,
      "isShared": false,
      "isOwned": false,
      "accessInfo": {
        "userAccessType": "favored",
        "owner": {
          "id": "0d888b00-fa53-47c1-882a-b68391268a11",
          "firstName": "SVL",
          "lastName": "Content"
        }
      },
      "supportedSimulatorVersions": [
        "2021.1",
        "2021.1.1",
        "2021.2"
      ],
      "id": "c4ba9b81-b274-4c05-b78e-636e61b4590e",
      "name": "Signal Sensor",
      "type": "SignalSensor",
      "category": "sensor",
      "ownerId": "0d888b00-fa53-47c1-882a-b68391268a11",
      "accessType": "public",
      "description": "This sensor returns ground truth data for traffic light signals connected to the current lane of ego vehicle and creates bounding boxes around the detected signals. The color of the bounding box corresponds to the signal's type:\n\t[\n\t\t\"Green\": \"Green\"\n\t\t\"Yellow\": \"Yellow\"\n\t\t\"Red\": \"Red\"\n\t]\nSee https://www.svlsimulator.com/docs/simulation-content/sensors-list/#signal-sensor for more details.",
      "copyright": "LG Electronics Inc.",
      "licenseName": "LG Content",
      "status": "active",
      "owner": {
        "id": "0d888b00-fa53-47c1-882a-b68391268a11",
        "firstName": "SVL",
        "lastName": "Content"
      },
      "shareRequests": []
    },
    "type": "SignalSensor"
  },
  {
    "params": {
      "MaxDistance": 1000,
      "Frame": "main_camera",
      "Topic": "/simulator/sensor/camera/center/image/compressed"
    },
    "transform": {
      "x": 0,
      "y": 1.5,
      "z": -0.2,
      "pitch": 0,
      "yaw": 0,
      "roll": 0
    },
    "name": "Main Camera",
    "parent": null,
    "pluginId": "3d4f1e08-4c62-4e9f-b859-b26d4910b85e",
    "plugin": {
      "assetGuid": null,
      "isFavored": true,
      "isShared": false,
      "isOwned": false,
      "accessInfo": {
        "userAccessType": "favored",
        "owner": {
          "id": "0d888b00-fa53-47c1-882a-b68391268a11",
          "firstName": "SVL",
          "lastName": "Content"
        }
      },
      "supportedSimulatorVersions": [
        "2021.1",
        "2021.1.1",
        "2021.2"
      ],
      "id": "3d4f1e08-4c62-4e9f-b859-b26d4910b85e",
      "name": "Color Camera Sensor",
      "type": "ColorCameraSensor",
      "category": "sensor",
      "ownerId": "0d888b00-fa53-47c1-882a-b68391268a11",
      "accessType": "public",
      "description": "This is the type of sensor that would be used for the Main Camera in Apollo.\nColor Camera also has multiple post processing sensor effects that can be added to the Postprocessing field in params. Effects can be combined with an array of Postprocessing fields but order is hard coded.\nSee https://www.svlsimulator.com/docs/simulation-content/sensors-list/#color-camera for more details.",
      "copyright": "LG Electronics Inc.",
      "licenseName": "LG Content",
      "status": "active",
      "owner": {
        "id": "0d888b00-fa53-47c1-882a-b68391268a11",
        "firstName": "SVL",
        "lastName": "Content"
      },
      "shareRequests": []
    },
    "type": "ColorCameraSensor"
  },
  {
    "name": "Keyboard Control Sensor",
    "parent": null,
    "pluginId": "a2ff904a-ff06-4f06-9e45-cb58217a7142",
    "plugin": {
      "assetGuid": null,
      "isFavored": true,
      "isShared": false,
      "isOwned": false,
      "accessInfo": {
        "userAccessType": "favored",
        "owner": {
          "id": "0d888b00-fa53-47c1-882a-b68391268a11",
          "firstName": "SVL",
          "lastName": "Content"
        }
      },
      "supportedSimulatorVersions": [
        "2021.1",
        "2021.1.1",
        "2021.2"
      ],
      "id": "a2ff904a-ff06-4f06-9e45-cb58217a7142",
      "name": "Keyboard Control Sensor",
      "type": "KeyboardControlSensor",
      "category": "sensor",
      "ownerId": "0d888b00-fa53-47c1-882a-b68391268a11",
      "accessType": "public",
      "description": "This sensor is required for a vehicle to accept keyboard control commands. Parameters are not required.\nSee https://www.svlsimulator.com/docs/simulation-content/sensors-list/#keyboard-control for more details.",
      "copyright": "LG Electronics Inc.",
      "licenseName": "LG Content",
      "status": "active",
      "owner": {
        "id": "0d888b00-fa53-47c1-882a-b68391268a11",
        "firstName": "SVL",
        "lastName": "Content"
      },
      "shareRequests": []
    },
    "type": "KeyboardControlSensor"
  },
  {
    "params": {
      "Topic": "/simulator/vehicle_state",
      "Frame": "AD_car_state"
    },
    "name": "AD car state",
    "parent": null,
    "pluginId": "8aff00f2-a5e4-4bd3-a778-517f9307fa99",
    "plugin": {
      "assetGuid": null,
      "isFavored": true,
      "isShared": false,
      "isOwned": false,
      "accessInfo": {
        "userAccessType": "favored",
        "owner": {
          "id": "0d888b00-fa53-47c1-882a-b68391268a11",
          "firstName": "SVL",
          "lastName": "Content"
        }
      },
      "supportedSimulatorVersions": [
        "2021.1",
        "2021.1.1",
        "2021.2"
      ],
      "id": "8aff00f2-a5e4-4bd3-a778-517f9307fa99",
      "name": "Vehicle State Sensor",
      "type": "VehicleStateSensor",
      "category": "sensor",
      "ownerId": "0d888b00-fa53-47c1-882a-b68391268a11",
      "accessType": "public",
      "description": "Example of a sensor to get state of different parts of vehicle.",
      "copyright": "LG Electronics Inc.",
      "licenseName": "LG Content",
      "status": "active",
      "owner": {
        "id": "0d888b00-fa53-47c1-882a-b68391268a11",
        "firstName": "SVL",
        "lastName": "Content"
      },
      "shareRequests": []
    },
    "type": "VehicleStateSensor"
  }
]
```

5、然后，点击左侧Simulations，并点击“Add New”添加一个新的仿真环境，配置之后的环境如下图所示（注意车辆和车辆传感器配置一定要是我们之前配置的，桥接选择Other ROS 2 Autopilot）。

![img](https://pic4.zhimg.com/80/v2-92bde56ad6d49f8c3bd9d3767ec91e37_720w.jpg)

6、好的，点击“Run Simulation”启动仿真，点击仿真器上的运行按钮开始运行（此时可用键盘方向键控制车辆）。

![img](https://pic4.zhimg.com/80/v2-ef28a51d8126dfb1765381cfdfa6bea7_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-769d54ff4bdf163835bb20c604500f14_720w.jpg)

同时，也能看到已经有了桥接后的订阅和发布的消息包信息。

![img](https://pic3.zhimg.com/80/v2-954de6e0b9507d36667df38aadfe478e_720w.jpg)

当然，在仿真器界面Bridge栏也能看到对应的消息包。

![img](https://pic3.zhimg.com/80/v2-d9b88b5d991ed56ac3f01d7d162349ca_720w.jpg)

7、至此，仿真器端的所有工作都已经完成。

------

最后，启动我们创建的ROS2 main_msg节点，仍然是在lgsvl_simu目录下。

```text
source install/setup.bash
ros2 run autodriving main_msg
```

可以看到已经成功啦～

![img](https://pic3.zhimg.com/80/v2-3ad1a8113c37d9f39b038884849bb9f6_720w.jpg)



------

可能会有对Python比较熟悉的朋友，下面给出Python消息包的配置，比较类似：

1、Python包结构

```text
cd autodriving_python
tree

output:
.
├── autodriving_python
│   └── __init__.py
├── package.xml
├── resource
│   └── autodriving_python
├── setup.cfg
├── setup.py
└── test
    ├── test_copyright.py
    ├── test_flake8.py
    └── test_pep257.py
```

2、在autodriving中创建一个用于订阅与发布消息的python文件。

```text
nano autodriving_python/msg_subpub.py
```

msg_subpub.py中的内容如下，和Cpp中的内容比较类似：

```python
import rclpy
from rclpy.node import Node
from sensor_msgs.msg import CompressedImage
from geometry_msgs.msg import TwistStamped
from lgsvl_msgs.msg import Detection3DArray, Detection2DArray, SignalArray
from lgsvl_msgs.msg import CanBusData, VehicleStateData, VehicleControlData
import message_filters
import cv2
import time
from datetime import datetime
import os
import errno

class AD_stack(Node):
    def __init__(self):
        super().__init__('Main_Node', allow_undeclared_parameters=True, automatically_declare_parameters_from_overrides=True)
        self.get_logger().info(f'[{self.get_name()}] Initializing the main node...')

        # Subscribe
        sub_3D_ground_truth_array = message_filters.Subscriber(self, Detection3DArray, '/simulator/ground_truth/m3d_detections')
        sub_signal_array = message_filters.Subscriber(self, SignalArray, '/simulator/ground_truth/signals')
        sub_can_bus_data = message_filters.Subscriber(self, CanBusData, '/simulator/canbus')

        subscribe_msgs = message_filters.ApproximateTimeSynchronizer([sub_3D_ground_truth_array, sub_signal_array, sub_can_bus_data], 10, 0.1)
        subscribe_msgs.registerCallback(self.subscribe_callback)

        # Publish
        self.control_pub = self.create_publisher(VehicleControlData, '/simulator/vehicle_control')
        self.state_pub = self.create_publisher(VehicleStateData, '/simulator/vehicle_state')
        self.timer_period = 0.2  # seconds
        self.timer = self.create_timer(self.timer_period, self.publish_callback)

        self.get_logger().info(f'[{self.get_name()}] Up and running the main node...')

    def subscribe_callback(self, sub_3D_ground_truth_array, sub_signal_array, sub_can_bus_data):
        self.get_logger().info('Subscribed: {}'.format("Get 3D_ground_truth & signal & can_bus_data Message"))
        print(sub_3D_ground_truth_array.header.stamp.sec)
    
    def publish_callback(self):
        # Vehicle Control
        control = VehicleControlData()
        control.target_gear = VehicleControlData.GEAR_DRIVE #前进档位
        control.acceleration_pct = 0.2  # 加速度
        self.control_pub.publish(control)

        # Vehicle State
        state = VehicleStateData()
        state.autonomous_mode_active = True 
        state.vehicle_mode= VehicleStateData.VEHICLE_MODE_COMPLETE_AUTO_DRIVE   # 驾驶模式
        self.state_pub.publish(state)

        self.get_logger().info('Publishing: {}'.format("Send vehicle_control & vehicle_data Message"))


def main(args=None):
    rclpy.init(args=args)
    stack = AD_stack()
    rclpy.spin(stack)


if __name__ == '__main__':
    main()
```

3、同时，应当修改package.xml文件。

```xml
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>autodriving_python</name>
  <version>0.0.0</version>
  <description>Auto driving based LGSVL and ROS2</description>
  <maintainer email="hanyh@bupt.edu.cn">hanyh</maintainer>
  <license>License declaration</license>
  
  <exec_depend>rclpy</exec_depend>
  <exec_depend>std_msgs</exec_depend>
  <exec_depend>sensor_msgs</exec_depend>
  <exec_depend>lgsvl_msgs</exec_depend>


  <test_depend>ament_copyright</test_depend>
  <test_depend>ament_flake8</test_depend>
  <test_depend>ament_pep257</test_depend>
  <test_depend>python3-pytest</test_depend>

  <export>
    <build_type>ament_python</build_type>
  </export>
</package>
```

4、同时，应当修改setup.py文件。

```python
from setuptools import setup

package_name = 'autodriving_python'

setup(
    name=package_name,
    version='0.0.0',
    packages=[package_name],
    data_files=[
        ('share/ament_index/resource_index/packages',
            ['resource/' + package_name]),
        ('share/' + package_name, ['package.xml']),
    ],
    install_requires=['setuptools'],
    zip_safe=True,
    maintainer='hanyh',
    maintainer_email='hanyh@bupt.edu.cn',
    description='auto driving based LGSVL and ROS2',
    license='License declaration',
    tests_require=['pytest'],
    entry_points={
        'console_scripts': [
            'main_node = autodriving_python.msg_subpub:main'
        ],
    },
)
```

5、其他的都没有区别了，仍然是编译运行即可。（在lgsvl_simu目录下，我这里python节点写的是main_node）

```text
colcon build --packages-select autodriving_python
source install/setup.bash
ros2 run autodriving_python main_node
```

![img](https://pic2.zhimg.com/80/v2-5b7a170dcb1faaa85ba29d4f15239239_720w.jpg)