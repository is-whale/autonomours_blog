- [自动驾驶模拟器Carla简介及功能说明（一） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/475193556)

CARLA是一款基于Unreal Engine虚幻引擎模拟真实世界的自动驾驶模拟器.

下面简单介绍Carla中的术语和功能。

## 1.术语

**world**：一个仿真对象，包括actor生成，改变天气，取得world state等。每次仿真只有一个world，当更换地图时，当前world会被销毁，再重新生成一个world。

**actor**：仿真中的任一角色，例如：车，人，传感器，交通标志，交通灯。

**blueprint**：蓝图，生成actor的actor layout，这些actor有自己的属性，有的属性可以修改，有的属性不能修改。Blueprint library包含了所有blueprint。

**maps：**仿真使用的地图。carla有8个地图，地图采用OpenDRIVE1.4格式。8个地图如下：

Town01: T junctions

Town02: 和Town01类似，更小一些

Town03：最复杂的Town，包括junction，roundabout，tunnel等

Town04：高速和小镇

Town05：交叉路口和桥，多个lane，可以用来做车道变线测试。

Town06：带有出入口的长高速路

Town07：狭窄路

Town10：林荫道，步行大道

**sensor：**sensor可以附加到车上，从仿真环境中收集数据。sensor包括如下类型：

- Cameras (RGB, depth and semantic segmentation). RGB相机（普通摄像头），深度相机，语义分割相机
- Collision detector.碰撞检测
- Gnss sensor. 全球卫星定位
- IMU sensor. 惯性传感器
- Lidar raycast. 激光雷达
- Lane invasion detector. 压线检测
- Obstacle detector. 障碍物检测
- Radar. 毫米波雷达
- RSS. 责任敏感安全传感器

**OpenDRIVE standalone mode：** 用OpenDRIVE文件生成路网，不需要在carla中创建assets就可以加载OpenDRIVE地图。

**PTV-Vissim co-simulation**： PTV-Vissim 是一款traffic仿真软件。carla支持和 PTV-Vissim同步仿真。

**SUMO co-simulation：** SUMO 是一款traffic仿真软件。carla支持和 SUMO 同步仿真。

**Recorder：**回放 ，跟踪工具。所有数据写到一个在server端的二进制文件。

**Rendering options：**渲染选项。分三种：Graphics quality settings, off-screen rendering , a no-rendering mode

**Traffic manager**:控制车辆，仿真市区交通环境。

## 2.功能

下面我们来运行一下carla，看看都有那些功能.

在carla代码根目录下打开一个新的terminal， 输入 make launch，启动CarlaUE4。点击“Play”按钮。

![img](https://pic3.zhimg.com/80/v2-66b7a72bda49636bec121e5770655826_720w.jpg)

在carla/PythonAPI/examples下打开一个新的terminal，输入 python manual_control.py

![img](https://pic2.zhimg.com/80/v2-b4cf291f99d945a14a8ab56828522149_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-f552ad6113047c0347e634633926cf80_720w.jpg)

W : 前进。↑键也可以前进。

S : 刹车。↓ 键也可以 刹车

A/D : A左转 D右转。←也可以左转，→也可以右转。

Q : Q倒车标志。Q+W可以倒车。

Space : 空格是手刹。和S不同。

P : 开启/关闭自动驾驶模式。

M : 切换到手动模式。

,/. : 加减挡。，减档 . 加档

CTRL + W : 同时按下 CTRL + W ，在放开CTRL + W ，车会一直以60 km/h的速度前进

L : 控制车灯切换。雾灯、近光灯等切换。

SHIFT + L : 切换远光灯

Z/X : 转向灯。Z左转向，X右转向。

I : 车内照明灯。

TAB : 切换视角

` or N : 切换不同类型的camera和lidar

[1-9] : 切换不同类型的camera和lidar，和N不同，N每按下一次，sensor顺序切换。按下数字键，可直接切换到对应sensor

G : toggle radar visualization

C : 切换天气，(Shift+C ，天气有多种，切换顺序和C相反)

Backspace : 换车型

V : 选地图图层 (Shift+V ，地图有多个图层，切换顺序和V 相反)

B : 加载当前的地图图层(Shift+B 卸载当前的地图图层)

R : 时时记录车辆走行情况

CTRL + R : 切换到 R做的记录 (replacing any previous)

CTRL + P : 回放R的记录

CTRL + + : increments the start time of the replay by 1 second (+SHIFT = 10 seconds)

CTRL + - : decrements the start time of the replay by 1 second (+SHIFT = 10 seconds)

F1 : 显示/不显示页面左侧和sensor相关的一些信息，例如加速度，陀螺仪，GNSS等

H/? : H和？可以弹出帮助命令

ESC : 退出pygame