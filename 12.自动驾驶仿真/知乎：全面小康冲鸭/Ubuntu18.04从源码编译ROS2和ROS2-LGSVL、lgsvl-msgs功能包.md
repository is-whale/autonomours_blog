- [Ubuntu18.04从源码编译ROS2和ROS2-LGSVL、lgsvl-msgs功能包 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/350182465)

主要根据LGSVL自动驾驶仿真器官方文档，完成了仿真器的源码构建。如果你按照上一篇文章的步骤，那么你应当能够在Unity的simulator项目中通过点击“Play”按钮运行LGSVL仿真器。

------

**本篇文章主要参考“LGSVL simulator doc => Advanced => Setting up ROS 2 bridge”，介绍ROS2和LGSVL官方提供的ros2-lgsvl-bridge的安装。**

[Setting up ROS 2 bridgewww.lgsvlsimulator.com/docs/ros2-bridge/](https://link.zhihu.com/?target=https%3A//www.lgsvlsimulator.com/docs/ros2-bridge/)

首先介绍几个概念：

- ROS/ROS2：机器人操作系统。
- ROS2功能包：用于从仿真器订阅/发布消息。
- ROS2 Bridge：连接ROS2和仿真器的桥，主要通过IP地址和端口号连接。

虽然官方也给出了pythonAPI来运行仿真，请移步：NULL

但官方也说明了想要实时、连续的控制自身车辆，ROS2桥接是一个很好的选择，像Autoware，早期版本的Apollo都是使用ROS系统来实现的。所以，你如果要开发的话，请使用ROS桥。

**暂时无需过多了解与ROS有关的知识。记住整体的一个仿真流程是：ROS节点通过ROS桥连接到仿真器，从仿真器中订阅车辆周围环境信息、交通信息等，然后发布车辆控制指令给仿真器。**也即：

- 启动ROS2 Bridge
- 在Unity中点击“Play”
- 在WebUI界面配置车辆的JSON文件（后面会讲）
- 加载场景，开始运行仿真环境，此时应当已经通过ROS桥连接到ROS系统上
- 运行ROS2节点，开始仿真，本地的测试代码也即是AD stack部署在ROS节点上

------

**1、我们首先安装ROS2，依然是参考ROS2官方的教程，我们从源码编译。**

[Building ROS 2 on Linuxindex.ros.org/doc/ros2/Installation/Dashing/Linux-Development-Setup/](https://link.zhihu.com/?target=https%3A//index.ros.org/doc/ros2/Installation/Dashing/Linux-Development-Setup/)

LGSVL官方教程提到，Ubuntu18.04支持安装ROS2 dashing或者eloguent版本，Ubuntu20.04仅支持安装ROS2 foxy版本，foxy和dashing都是一个长期支持的版本，所以我们在Ubuntu18.04系统上从源码编译ROS2 dashing。

1> 首先设置locale支持Utf-8。

```text
locale  # check for UTF-8  
sudo apt update && sudo apt install locales 
sudo locale-gen en_US en_US.UTF-8 
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 exportLANG=en_US.UTF-8  
locale  # verify settings
```

2> 添加ROS2 apt安装库。

首先执行sudo gedit /etc/hosts命令，然后编辑打开的hosts文件，将这句话添加到文件后面，然后保存。这一步的操作主要是为了防止网络错误带来的问题。

> **199.232.28.133 [http://raw.githubusercontent.com](https://link.zhihu.com/?target=http%3A//raw.githubusercontent.com)**

然后执行

```text
sudo apt update && sudo apt install curl gnupg2 lsb-release 
curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
sudo sh -c 'echo "deb [arch=$(dpkg --print-architecture)] http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" > /etc/apt/sources.list.d/ros2-latest.list'
```

3> 然后安装一堆的开发工具和ROS工具，就是一堆的依赖包。

```text
sudo apt update && sudo apt install -y \
  build-essential \
  cmake \
  git \
  python3-colcon-common-extensions \
  python3-pip \
  python-rosdep \
  python3-vcstool \
  wget
# install some pip packages needed for testing
python3 -m pip install -U \
  argcomplete \
  flake8 \
  flake8-blind-except \
  flake8-builtins \
  flake8-class-newline \
  flake8-comprehensions \
  flake8-deprecated \
  flake8-docstrings \
  flake8-import-order \
  flake8-quotes \
  pytest-repeat \
  pytest-rerunfailures \
  pytest \
  pytest-cov \
  pytest-runner \
  setuptools
# install Fast-RTPS dependencies
sudo apt install --no-install-recommends -y \
  libasio-dev \
  libtinyxml2-dev
# install Cyclone DDS dependencies
sudo apt install --no-install-recommends -y \
  libcunit1-dev
```

4> 下面获得ROS2代码

```text
mkdir -p ~/ros2_dashing/src
cd ~/ros2_dashing
wget https://raw.githubusercontent.com/ros2/ros2/dashing/ros2.repos
vcs import src < ros2.repos
```

5> 又是安装一堆的依赖

```text
sudo rosdep init
rosdep update
rosdep install --from-paths src --ignore-src --rosdistro dashing -y --skip-keys "console_bridge fastcdr fastrtps libopensplice67 libopensplice69 rti-connext-dds-5.3.1 urdfdom_headers"
```

6> 构建ROS2功能包

```text
cd ~/ros2_dashing/
colcon build --symlink-install
```

通过该命令让所有以ROS2开头的命令生效，可以把该命令写到.bashrc脚本里面，让每次启动终端的时候自动加载该命令。

```text
. ~/ros2_dashing/install/setup.bash
```

至此，ROS2已经安装成功。下面用官方给出的用例测试一下。

在一个终端中运行如下命令，开启一个C++ 的talker。

```text
. ~/ros2_dashing/install/local_setup.bash
ros2 run demo_nodes_cpp talker
```

在另一个终端中运行如下命令，开启一个python的listener。

```text
. ~/ros2_dashing/install/local_setup.bash
ros2 run demo_nodes_py listener
```

如果ROS2安装成功，你将能够看到C++的talker在发布消息，python的listener在接收消息。

![img](https://pic4.zhimg.com/80/v2-36daa11739c2b5f65f099ae713a3b8bb_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-1193b6f3cd167159833bd142f13392dc_720w.jpg)

**2、安装Boost，可能在上一步安装ROS2的时候，已经装好了这个依赖，再执行一次命令确认一下有没有安装成功。**

```text
sudo apt install libboost-all-dev
```

**3、我们安装ROS2 LGSVL Bridge，该Bridge是LGSVL官方提供的ROS2 Bridge，用于连接ROS2和仿真器。注意：该桥接只适合“release-2020.06”分支的LGSVL simulator仓库。**

1> 进入到工作目录

```text
cd ~/ros2_dashing/src
```

2> clone对应的仓库

```text
git clone https://github.com/lgsvl/ros2-lgsvl-bridge.git
```

3> 让所有以ROS2开头的命令生效

```text
source ~/ros2_dashing/install/setup.bash
```

4> 切换到对应的dashing版本分支

```text
cd ros2-lgsvl-bridge
git checkout dashing-devel
```

5> 构建

```text
colcon build --cmake-args '-DCMAKE_BUILD_TYPE=Release'
```

6> 启动ROS2-lgsvl-bridge

```text
source ~/ros2_dashing/install/setup.bash
lgsvl_bridge
```

这样，在我们的终端里会监听9090端口。如图所示：

![img](https://pic1.zhimg.com/80/v2-f978fe3ede294e172d7fcd7d87b1a8d8_720w.png)

**4、最后安装一个ROS2功能包，叫做lgsvl-msgs，也是LGSVL官方提供的功能包，包内的消息定义如下：**

> \- Detection3DArray.msg # A list of 3D detections
> \- Detection3D.msg # 3D detection including id, label, score, and 3D bounding box
> \- BoundingBox3D.msg # A 3D bounding box definition
> \- Detection2DArray.msg # A list of 2D detections
> \- Detection2D.msg # 2D detection including id, label, score, and 2D bounding box
> \- BoundingBox2D.msg # A 2D bounding box definition
> \- SignalArray.msg # A list of traffic light detections
> \- Signal.msg # 3D detection of a traffic light including id, label, score, and 3D bounding box
> \- CanBusData.msg # Can bus data for an ego vehicle published by the simulator
> \- VehicleControlData.msg # Defines vehicle control data the simulator subscribes to
> \- VehicleStateData.msg # Data published by the simulator describing the full state of an ego vehicle

与上面的ROS2-lgsvl-bridge的安装方式相似，clone功能包，构建，生效：

```text
cd ~/ros2_bashing/src
git clone https://github.com/lgsvl/lgsvl_msgs.git
cd lgsvl_msgs
colcon build
source install/setup.bash
```

------

但并不是安装了ROS2就可以直接订阅/发布消息，仍需要车辆的传感器配置JSON文件，记住大多数“发布/订阅的消息”是和“传感器”对应的。

[全面小康冲鸭：Ubuntu18.04从源码编译自动驾驶仿真器LGSVL simulator10 赞同 · 10 评论文章![img](https://pic2.zhimg.com/v2-7ced3f2ef7ac3d3b2047286ffe2d7c0d_180x120.jpg)](https://zhuanlan.zhihu.com/p/350031359)

至此，结合上一篇文章，我们基本全部安装搭建好自动驾驶仿真环境（LGSVL simulator + Unity + ROS2-lgsvl-bridge + lgsvl-msgs）。