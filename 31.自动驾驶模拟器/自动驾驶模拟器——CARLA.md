- [自动驾驶模拟器——CARLA](https://blog.csdn.net/learning_tortosie/article/details/105155523?spm=1001.2014.3001.5501)
- [强化学习：自动驾驶——Carla 模拟器简介](https://www.jianshu.com/p/f99b1d8bfc2a)
- [CARLA无人车仿真环境搭建](https://cloud.tencent.com/developer/news/317014)

# 1 CARLA模拟器

## 1.1 介绍

CARLA是用于自动驾驶研究的开源模拟器。CARLA是从零开始开发的，旨在支持自动驾驶系统的开发，训练和验证。除了开源代码和协议，CARLA还提供了为此目的而创建且可以免费使用的开放数字资产（城市布局，建筑物，车辆）。该仿真平台支持传感器套件的灵活规范，环境条件，对所有静态和动态参与者的完全控制，地图生成等等。

如果您希望在与我们的CoRL’17论文相同的条件下对模型进行基准测试，请查看[基准测试](https://github.com/carla-simulator/driving-benchmarks)。

相关资料：

- 主页：http://carla.org/
- 文档：https://carla.readthedocs.io/en/latest/
- GitHub：https://github.com/carla-simulator/carla

## 1.2 特色功能

- 通过服务器多客户端架构实现可扩展性：同一节点或不同节点中的多个客户端可以控制不同的参与者。
- 灵活的API：CARLA公开了功能强大的API，使用户可以控制与模拟相关的所有方面，包括交通生成，行人行为，天气，传感器等。
- 自动驾驶传感器套件：用户可以配置各种传感器套件，包括激光雷达，多个摄像头，深度传感器和GPS。
- 用于规划和控制的快速仿真：此模式禁用渲染以提供不需要图形的交通仿真和道路行为的快速执行。
- 地图生成：用户可以通过[RoadRunner](https://www.vectorzero.io/)之类的工具轻松遵循[OpenDrive](http://www.opendrive.org/)标准创建自己的地图。
- 交通场景模拟：我们的引擎[ScenarioRunner](https://github.com/carla-simulator/scenario_runner)允许用户基于模块化行为定义和执行不同的交通状况。
- ROS集成：CARLA通过我们的[ROS-bridge](https://github.com/carla-simulator/ros-bridge)与[ROS](http://www.ros.org/)集成
- 自主驾驶基准：我们以CARLA中可运行的代理程序（包括[AutoWare](https://github.com/carla-simulator/carla-autoware)代理和[条件模仿学习](https://github.com/felipecode/coiltraine)代理）的形式提供自主驾驶基准。

# 2 CARLA生态系统

与CARLA仿真平台相关的存储库：

- [Scenario_Runner](https://github.com/carla-simulator/scenario_runner)：在CARLA 0.9.X中执行交通场景的引擎
- [ROS-bridge](https://github.com/carla-simulator/ros-bridge)：用于将CARLA 0.9.X连接到ROS的接口
- [Driving-benchmarks](https://github.com/carla-simulator/driving-benchmarks)：用于自动驾驶任务的基准工具
- [Conditional Imitation-Learning](https://github.com/felipecode/coiltraine)：在CARLA中训练和测试条件模仿学习模型
- [AutoWare AV stack](https://github.com/carla-simulator/carla-autoware): Bridge to connect AutoWare AV stack to CARLA
- [Reinforcement-Learning](https://github.com/carla-simulator/reinforcement-learning)：在CARLA中运行条件强化学习模型的代码
- [Map Editor](https://github.com/carla-simulator/carla-map-editor)：独立的GUI应用程序可通过交通信号灯和交通标志信息来增强[RoadRunner](https://www.vectorzero.io/)地图

喜欢你看到的吗？在GitHub上为我们加注星标以支持该项目！

# 3 论文

如果您使用CARLA，请引用我们的CoRL’17论文。

CARLA: An Open Urban Driving Simulator
 Alexey Dosovitskiy, German Ros, Felipe Codevilla, Antonio Lopez, Vladlen Koltun; PMLR 78:1-16 [[PDF](http://proceedings.mlr.press/v78/dosovitskiy17a/dosovitskiy17a.pdf)] [[talk](https://www.youtube.com/watch?v=xfyK03MEZ9Q&feature=youtu.be&t=2h44m30s)]

> @inproceedings{Dosovitskiy17,
>  title = {{CARLA}: {An} Open Urban Driving Simulator},
>  author = {Alexey Dosovitskiy and German Ros and Felipe Codevilla and Antonio Lopez and Vladlen Koltun},
>  booktitle = {Proceedings of the 1st Annual Conference on Robot Learning},
>  pages = {1–16},
>  year = {2017}
>  }

# 4 安装

使用git clone或从此[页面](https://github.com/wbercode/carla)下载项目。请注意，`master`分支包含最新的修补程序和功能，最新的稳定代码可能最好切换到`stable`分支。

然后按照“[如何在Linux上构建](http://carla.readthedocs.io/en/latest/how_to_build_on_linux)或[如何在Windows上构建](http://carla.readthedocs.io/en/latest/how_to_build_on_windows)”中的说明进行操作。

不幸的是，我们还没有在Mac上构建的正式说明，请检查[问题#150](https://github.com/carla-simulator/carla/issues/150)的进度。

**注意：CARLA需要Ubuntu 16.04或更高版本。**

## 4.1 安装构建工具和依赖项

```
sudo apt-get update
sudo apt-get install wget software-properties-common
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key|sudo apt-key add -
sudo apt-add-repository "deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial-7 main"
sudo apt-get update
sudo apt-get install build-essential clang-7 lld-7 g++-7 cmake ninja-build libvulkan1 python python-pip python-dev python3-dev python3-pip libpng16-dev libtiff5-dev libjpeg-dev tzdata sed curl unzip autoconf libtool rsync
pip2 install --user setuptools
pip3 install --user setuptools
```

**提示：对于Ubuntu 18.04，将上一个示例中的`libpng16-dev`更改为`libpng-dev`。**

为了避免Unreal  Engine和CARLA依赖项之间的兼容性问题，最好的配置是使用相同的编译器版本和C++运行时库编译所有内容。我们使用clang  6.0和LLVM的libc++。我们建议更改默认的clang版本以编译Unreal Engine和CARLA依赖项

```
sudo update-alternatives --install /usr/bin/clang++ clang++ /usr/lib/llvm-7/bin/clang++ 170
sudo update-alternatives --install /usr/bin/clang clang /usr/lib/llvm-7/bin/clang 170
```

## 4.2 构建Unreal Engine

**注意：Unreal Engine存储库设置为私有。为了获得访问权限，您需要在 www.unrealengine.com 上注册时添加GitHub用户名。**

下载并编译Unreal Engine 4.22。在这里，我们假设您将其安装在`~/UnrealEngine_4.22`上，但是您可以将其安装在任何位置，只需在必要时替换路径即可。

```
git clone --depth=1 -b 4.22 https://github.com/EpicGames/UnrealEngine.git ~/UnrealEngine_4.22
cd ~/UnrealEngine_4.22
./Setup.sh && ./GenerateProjectFiles.sh && make
```

如果以上任何步骤失败，请查看Unreal的文档“[Building on Linux](https://wiki.unrealengine.com/Building_On_Linux)”。

## 4.3 构建CARLA

从我们的[GitHub存储库](https://github.com/carla-simulator/carla)克隆或下载项目

```
git clone https://github.com/carla-simulator/carla
```

现在您需要下载资产包，为此，我们提供了一个方便的脚本来下载并提取最新版本。

**注意：此包的大小为3GB以上，此步骤可能需要一些时间，具体取决于您的连接。**

**提示：（可选）您可以下载aria2（使用`sudo apt-get install aria2`），因此以下命令将利用它，并且运行速度会更快。**

```
./Update.sh
```

为了让CARLA找到您的Unreal Engine的安装文件夹，您需要设置以下环境变量：

```
export UE4_ROOT=~/UnrealEngine_4.22
```

您也可以将此变量添加到`~/.bashrc`或`~/.profile`中。

现在已经建立了环境，您可以使用make运行不同的命令并构建不同的模块：

```
make launch     # Compiles the simulator and launches Unreal Engine's Editor.
make PythonAPI  # Compiles the PythonAPI module necessary for running the Python examples.
make package    # Compiles everything and creates a packaged version able to run without UE4 editor.
make help       # Print all available commands.
```

## 4.4 Assets repository（仅限开发）

我们的3D资产，模型和地图也有一个[公开可用的git存储库](https://bitbucket.org/carla-simulator/carla-content)。我们定期将最新更新推送到此存储库。但是，仅建议开发人员使用此版本的内容，因为我们经常有正在进行的工作图和模型。

处理该存储库需要在您的计算机中安装[git-lfs](https://git-lfs.github.com/)。将此存储库克隆到`Unreal/CarlaUE4/Content/Carla`：

```
git lfs clone https://bitbucket.org/carla-simulator/carla-content Unreal/CarlaUE4/Content/Carla
```

建议使用`git lfs clone`进行克隆，因为这在旧版本的git中明显更快。

# 5 常见问题解答

如果您遇到问题，请查看我们的[常见问题解答](http://carla.readthedocs.io/en/latest/faq/)。

# 6 协议

CARLA特定代码在MIT许可下分发。

CARLA特定资产根据CC-BY许可进行分配。

由[RSS Integration build variant](https://github.com/wbercode/carla/blob/master/Docs/rss_lib_integration.md)编译和链接的ad-rss-lib库引入了仅限LGPL-2.1的许可。

请注意，UE4本身遵循其自己的许可条款。

**注：开源协议非常重要，后期如果商用一定要遵守协议才行。**