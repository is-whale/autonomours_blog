- [LGSVL官方文档理解（一）：仿真的入门、配置和启动 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/350301593)

本篇文章，先写一些关于LGSVL simulator比较基础的东西，不会有太深入的知识。主要包括：

- **WebUI界面介绍**
- **Simulator界面介绍**
- **如何从头到尾启动一个仿真。**

## 1 WebUI界面

参考：

[Maps - LGSVL Simulatorwww.lgsvlsimulator.com/docs/maps-tab/![img](https://pic2.zhimg.com/v2-f93d715990f403076e53ea96eb05dead_180x120.jpg)](https://link.zhihu.com/?target=https%3A//www.lgsvlsimulator.com/docs/maps-tab/)

我们打开LGSVL simulator可执行文件或者在Unity中点击“Play”，会出现如下界面：

![img](https://pic4.zhimg.com/80/v2-7196e4755abab60d3c4689e277d64363_720w.jpg)

点击界面右上方的“齿轮”可以设置“全屏/窗口化”，“分辨率”，“显卡性能”。

![img](https://pic2.zhimg.com/80/v2-f84d76c737a01dd962709c1782fbe3bd_720w.jpg)

点击“Open Browser”或在浏览器中输入“[http://localhost:8080/](https://link.zhihu.com/?target=http%3A//localhost%3A8080/)”就会打开LGSVL的**WebUI界面**。WebUI界面用于配置车辆、地图和仿真环境，启动/停止仿真等。

![img](https://pic2.zhimg.com/80/v2-8b9698ba3e0d6837e2109df4a2138979_720w.jpg)

1> **地图、车辆**：我们可以在每个界面的“Add New”里面添加新的资产（地图、车辆）。如果你没有从源码编译LGSVL，只是运行一个可执行文件，那么界面上所有的资产（地图、车辆）会自动联网下载，当你在单个资产的占用框里看到“Valid”，说明这个资产是可用的。

![img](https://pic1.zhimg.com/80/v2-4c79055ce95cde061d9c3edc7b01b8dc_720w.png)

如果你想要添加本地构建的资产，那么你需要点击“Add New”在URL链接里键入本地资产的位置，地图在simulator\AssetBundles\Environments这个文件夹下，车辆在simulator\AssetBundles\Vehicles这个文件夹下。然后输入一个资产的名字，点击“Submit”即可。

例如

> simulator\AssetBundles\Environments\environment_CubeTown
> simulator\AssetBundles\Vehicles\vehicle_Jaguar2015XE

![img](https://pic4.zhimg.com/80/v2-5a9ae7e528a1fe46f701df41631abe13_720w.jpg)



注意：在车辆资产上有一个小的“扳手”，点击这个扳手将会弹出以下内容框:

![img](https://pic4.zhimg.com/80/v2-32930f07ff194fb081b48da8724f9913_720w.jpg)

"Bridge Type”用于选择想要的桥接类型：No bridge、ROS、ROS2、ROS Apollo、CyberRT。

"Sensors"用于配置依附在车辆上的传感器（Json格式）(关于传感器的配置内容请参考下面的官方链接，后面也会讲到，现在先大体知道一下)

[Sensor parameters - LGSVL Simulatorwww.lgsvlsimulator.com/docs/sensor-json-options/](https://link.zhihu.com/?target=https%3A//www.lgsvlsimulator.com/docs/sensor-json-options/)

如果你不是自己导入的车辆，那么”扳手“里的配置内容应该都是已经默认填好的。

2> **集群**：用于分散传感器到多台机器上，缓解仿真的压力，额，这个我们先不用管了，也用不到。

3> **仿真**：首先点击“Add New”介绍几个比较常用的仿真配置。

“API Only”只允许通过调用Python API来控制仿真、自身车辆和行人、NPC等。

“Headless Mode”该模式不会启用渲染，在非交互的情况下使用缓解仿真压力。

“Interactive Mode”交互模式，可以在仿真的过程中暂停/开始。

“Bridge connection string”桥接字符串，一般为IP和端口，例如：localhost:9090。

“Use Predefined Seed”如果使用相同的随机种子，那么仿真环境也是完全相同的。

“Random Traffic”和“Random Pedestrians”随机交通流和行人。

“Weather”天气中可以设置时间、降雨量（0-1）、湿度、雾和云。

同样的，如果你是直接下载的可执行文件，那么有些仿真是已经配置好的。

4> **启动**：选中仿真包点击”运行“，仿真包的状态变为”running“即代表仿真开始，同时会在仿真器的界面看到加载的车辆和地图。

![img](https://pic3.zhimg.com/80/v2-8e2e93ad1bd91f5c0dbe0807e01ac42a_720w.jpg)

## 2 LGSVL simulator界面

当我们启动仿真之后，simulator界面会加载地图和车辆、甚至是NPC、行人、天气等。下面我们对simulator的界面做简单的介绍。参考：

[Menu items - LGSVL Simulatorwww.lgsvlsimulator.com/docs/simulation-menu/![img](https://pic1.zhimg.com/v2-c72db7377214bb61d24608c9bbc408e4_180x120.jpg)](https://link.zhihu.com/?target=https%3A//www.lgsvlsimulator.com/docs/simulation-menu/)

1> **底部的菜单栏**

![img](https://pic2.zhimg.com/80/v2-7d680870964e8bd6d970451b009265d5_720w.png)

从左到右分别是：展开/折叠菜单栏、开始/暂停（交互模式）、传感器可视化、仿真环境配置（时间、天气、降雨、行人、NPC等）、桥接状态、**快捷键**、仿真信息。右侧的一个是视角切换，另一个是观察车辆选择。

2 > **快捷键**

官方提供了非常多的快捷键，参考：

[Keyboard shortcuts - LGSVL Simulatorwww.lgsvlsimulator.com/docs/keyboard-shortcuts/](https://link.zhihu.com/?target=https%3A//www.lgsvlsimulator.com/docs/keyboard-shortcuts/)

但对于开发人员，我们大部分都是在使用脚本操作车辆，修改环境。键盘快捷键只是入门LGSVL的一点点猎奇。下面列出了较为常用的快捷键。

> ← ↑ ↓ → - Drive vehicle forward/brake, turn
> 1 - 0 - Agent select and follow cam
> Mouse Right-Click hold & drag - Look/Rotate
> ~ - Free Camera
> W S - Zoom
> A D - Strafe
> Q E - Up/Down

3> **传感器可视化**

当我们在车辆的“Sensors”配置中添加了传感器的JSON配置之后，可以在传感器可视化菜单中选中传感器，点击“眼睛”按钮，就可以把该传感器捕获的内容可视化出来。下面三张图是以LGSVL自带的“BorregasAve, evening (with Apollo 5.0)”仿真包为例，分别可视化出的“语义分割摄像头信息”、“二维标注”和“三维标注”。

![img](https://pic3.zhimg.com/80/v2-d6a095b8cbcb682cf99826bbdbbc3cfe_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-c67e5f51b4b735797f59260605f62c4f_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-1ad7b00c1f828dc90b2043238c9dd9c1_720w.jpg)

4> **桥接**：如果使用了桥接，那么会显示出桥接信息，包括桥接是否成功等。

![img](https://pic1.zhimg.com/80/v2-73f88038a1cf3694d38c5a38bb5de138_720w.jpg)

## 3 从头到尾启动一个自定义的仿真

下面我们从导入车辆、地图开始，一步步的运行一个自定义的仿真。

1> 导入车辆，把URL改为自己的地址。

![img](https://pic2.zhimg.com/80/v2-aa94160c2409aa248fe8c97385b55d09_720w.jpg)

2> 配置车辆，在“Sensors”中输入以下JSON格式的配置，为了简单起见，我们只加了一个用键盘控制车辆的JSON配置，等我们了解传感器之后，再更新配置。

> [{"type": "Keyboard Control", "name": "Keyboard Car Control"}]

![img](https://pic2.zhimg.com/80/v2-3a252509e5e338595a5982bf1cfcd91d_720w.jpg)

3> 添加地图，同样把URL地址改为自己的。

![img](https://pic1.zhimg.com/80/v2-db6f9be8f713d8fb99446e9b05d75828_720w.jpg)

4> 配置仿真包，选中刚才添加的车辆和地图，其他的选项按照自己的喜好配置。

![img](https://pic2.zhimg.com/80/v2-63fa8aa300b6dd349262dfe64c4f39dd_720w.jpg)

5> 选中我们刚才添加的仿真包，点击运行按钮，显示“Running”表示仿真已经开始。

![img](https://pic1.zhimg.com/80/v2-25eea837a3fcf4634f4b427c4338f024_720w.jpg)

6> 使用快捷键操作车辆或查看视图、漫游地图。

![img](https://pic2.zhimg.com/80/v2-d223feeb081650861bb1cd86d7a0c04d_720w.jpg)

如果你看到了这里，应该基本对LGSVL能明白很多，再加上自己的实际操作，那么恭喜你，入门了！！

那么后面，我将介绍较为重要的传感器分类、传感器参数和ROS桥接等。