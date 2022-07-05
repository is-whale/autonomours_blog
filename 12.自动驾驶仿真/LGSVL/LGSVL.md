- [SVL-Simulation自动驾驶仿真器_一銤阳光的博客-CSDN博客_自动驾驶模拟器](https://blog.csdn.net/CSDNhuaong/article/details/122653711)
- [自动驾驶模拟器LGSVL简介及安装（一） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/489991710)
- [简析LGSVL自动驾驶仿真系统_SuperWiwi的博客-CSDN博客_lgsvl simulator](https://blog.csdn.net/qq_36622009/article/details/123233720)
- 纯官网：[LGSVL Simulatorwww.lgsvlsimulator.com](https://link.zhihu.com/?target=https%3A//www.lgsvlsimulator.com/)
- 官网github：[lgsvl/simulatorgithub.com/lgsvl/simulator](https://link.zhihu.com/?target=https%3A//github.com/lgsvl/simulator)

## 1 什么是 SVL Simulation

> - [SVL Simulator ： An Autonomous Vehicle Simulator](https://www.svlsimulator.com/)
> - 是有LG电子美国研发中心推出的一款基于Unity的用于自动驾驶开发的多机器人仿真器。
> - SVL 的目标：提供了一个开箱即用仿真解决方案，可以满足开发人员专注于测试自动驾驶汽车算法的需求。
> - SVL的特点：容易上手，直接提供了与Apollo、Autoware 等开源自动驾驶系统集成仿真的解决方案。

LGSVL可用于如下应用开发：

- L4/L5自动驾驶车辆系统
- L2/L3 ADAS/AD系统
- 仓库机器人
- 户外移动机器人
- 未来移动服务
- 自动赛车
- 传感器/传感器系统开发和营销
- 和自动系统安全
- 合成数据生成
- 汽车用实时嵌入式系统



LGSVL Simulator[1]是一个基于Unity游戏引擎开发的开源自动驾驶仿真系统。LGSVL Simulator提供端到端的仿真，它通过一个自定义的通信Bridge与多种自动驾驶系统（autonomous driving (AD) stacks）进行消息传递。该通信Bridge支持ROS、ROS2和Cyber RT消息，使LGSVL Simulator可以与两个最流行的开源自动驾驶引擎Autoware(基于ROS)和百度Apollo(3.0以下版本基于ROS，3.5版本以上基于Cyber RT)进行通信。

LGSVL Simulator配备了核心模拟引擎，允许用户轻松定制传感器，创建新型可控对象，替换核心模拟器中的一些模块，并创建特定环境的Digital Twin。此外，LGSVL Simulator还提供了一个地图工具，可以导入和导出Lanelet2、OpenDRIVE、Apollo HD Map等格式的自动驾驶的高精地图。

## 2 如何安装使用 SVL-Sim

### 2.1 硬件基础

[System requirements](https://www.svlsimulator.com/docs/installation-guide/system-requirements/#system-requirements)：

> 跑[自动驾驶](https://so.csdn.net/so/search?q=自动驾驶&spm=1001.2101.3001.7020)这种大系统，对CPU和GPU还是有些要求的， 而且基于Unity和Unreal这些3D引擎的仿真器，渲染也需要很多GPU资源
>
> - at least **4 GHz Quad core CPU**
> - NVIDIA **GTX 1080 (8GB memory)** or higher **显存最好大于10GB**
> - Windows 10 (64-bit), **Ubuntu 18.04 (64-bit)**, or Ubuntu 20.04 (64-bit)
> - 如果 SVL仿真器和Apollo 这种自动驾驶系统在同一台机器上跑，对系统资源的要求会更高一些
>    
>   Tips:
> - 开发SVL模拟器在Windows下： SVL模拟器的完整功能在开发模式(在Unity编辑器)仅支持Windows
> - 用SVL跑自动驾驶算法在Linux下: 用PythonAPI运行时SVL模板或Visual Scenario运行时的端到端自动驾驶仿真仅支持Linux

### 2.2 SVL-Sim安装

LGSVL有2种安装方式：

第一种：下载官网编译好的安装包直接安装。

第二种：下载开源代码，编译开源代码生成可执行文件。

- 一种便捷的方式是直接使用Release的打包文件
- 另一种方式是源码编译安装， 可以了解其实现方式，定制化改造

## 3 Lgsvl windows安装

SVL官网注册账号。填写完姓名邮箱后，邮箱会接到一封确认邮件。但是我没有点那封确认邮件，我也能登陆官网，为了防止后面出现不能使用服务情况，我最后还是点了那封确认邮件。

![img](https://pic2.zhimg.com/80/v2-cde4a4c72b525410ad27b135aa09e22d_720w.jpg)

下载windows安装包。直接点download就可以。

![img](https://pic4.zhimg.com/80/v2-b82b2d16f53d85acfb4c5c9a81b6bc7f_720w.jpg)

解压安装包，找到simulator.exe，双击simulator.exe，首次运行，出现如下界面，点“LINK TO CLOUD”。非首次运行就不是这个界面了。

![img](https://pic3.zhimg.com/80/v2-4da443922359f16e48946e43b754ef3e_720w.jpg)

新建cluster

![img](https://pic2.zhimg.com/80/v2-b731f87b89611c68e4dbe172e418884d_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-74323d670a9f74d7591976472bcae34d_720w.jpg)

在Simulations中选Available from others

![img](https://pic3.zhimg.com/80/v2-4208c55fd13e1fb2c676d5894977571a_720w.jpg)

在search中输入“Manual”，找到“local-Random：Cubetown（Manual Drive）”，点击右下角红色添加按钮。

![img](https://pic3.zhimg.com/80/v2-b5ba277b6c4283e1c348ad5582a4415e_720w.jpg)

选择本地建好的cluster

![img](https://pic1.zhimg.com/80/v2-6ad0cbdf9b2aa8dc3181ca96b66208a0_720w.jpg)

选keyboardcontrol

![img](https://pic3.zhimg.com/80/v2-a8eb30705a74a95b1c7854d2d0b4b762_720w.jpg)

选next

![img](https://pic1.zhimg.com/80/v2-6b7b0dedaaf80ea800c67e4687963474_720w.jpg)

选publish

![img](https://pic3.zhimg.com/80/v2-70c4f757c017e1f383baf93ac98df9ee_720w.jpg)

选Run Simulation

![img](https://pic4.zhimg.com/80/v2-0c5ff89c3a3c2c7d1ff958ea2807d03b_720w.jpg)

点击play按钮

![img](https://pic1.zhimg.com/80/v2-42a83e5b573878f59bfef2d6f3cebac0_720w.jpg)

先上段视频体验一下LGSVL模拟器。

## 4 LGSVL学习资料

svl官网

[https://www.svlsimulator.com/](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/)

svl官方文档

[https://www.svlsimulator.com/docs/](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/docs/)

svl 开源代码

[https://github.com/lgsvl/simula](https://link.zhihu.com/?target=https%3A//github.com/lgsvl/simulator)