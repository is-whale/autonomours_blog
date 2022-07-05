- [简析LGSVL自动驾驶仿真系统_SuperWiwi的博客-CSDN博客_lgsvl simulator](https://blog.csdn.net/qq_36622009/article/details/123233720)

# 摘要

在真实的[自动驾驶](https://so.csdn.net/so/search?q=自动驾驶&spm=1001.2101.3001.7020)汽车上测试自动驾驶算法的成本非常高，自动驾驶仿真引擎可以对自动驾驶车辆进行虚拟测试，极大地降低了测试成本。LGSVL Simulator是一个基于Unity游戏引擎开发的开源自动驾驶仿真系统，该软件提供端到端的仿真，通过一个自定义的通信Bridge与多种自动驾驶算法引擎接口进行消息传递。LGSVL Simulator配备了核心仿真引擎，允许用户轻松定制传感器。本文基于LGSVL Simulator发表的论文分析了该模拟器的软件架构，并对其应用的领域进行了总结。

**关键词：** 自动驾驶仿真，LGSVL Simulator，传感器，高[精度](https://so.csdn.net/so/search?q=精度&spm=1001.2101.3001.7020)地图

# 1 引言

LGSVL Simulator[1]是一个基于Unity[游戏引擎](https://so.csdn.net/so/search?q=游戏引擎&spm=1001.2101.3001.7020)开发的开源自动驾驶仿真系统。LGSVL Simulator提供端到端的仿真，它通过一个自定义的通信Bridge与多种自动驾驶系统（autonomous driving (AD) stacks）进行消息传递。该通信Bridge支持ROS、ROS2和Cyber RT消息，使LGSVL Simulator可以与两个最流行的开源自动驾驶引擎Autoware(基于ROS)和百度Apollo(3.0以下版本基于ROS，3.5版本以上基于Cyber RT)进行通信。

LGSVL Simulator配备了核心模拟引擎，允许用户轻松定制传感器，创建新型可控对象，替换核心模拟器中的一些模块，并创建特定环境的Digital Twin。此外，LGSVL Simulator还提供了一个地图工具，可以导入和导出Lanelet2、OpenDRIVE、Apollo HD Map等格式的自动驾驶的高精地图。

本文基于LGSVL Simulator官方发表的论文[1]、官方使用手册[2]以及源代码[3]对LGSVL Simulator的软件[架构](https://so.csdn.net/so/search?q=架构&spm=1001.2101.3001.7020)、应用领域进行分析。

# 2 软件架构

由LGSVL Simulator实现的自动驾驶仿真工作流程如图所示，本章将详细说明各个组件。
![在这里插入图片描述](https://img-blog.csdnimg.cn/34b7554d2d7c46fabacb96c933aa6686.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3VwZXJXaXdp,size_16,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 2.1 自动驾驶算法引擎接口

自动驾驶算法引擎接口是用户开发、测试和仿真验证的系统。LGSVL Simulator目前提供了与开源系统Apollo、Autoware.AI、Autoware.Auto的开箱即用集成。

自动驾驶算法引擎接口通过通信桥接接口（communication bridge interface）与LGSVL Simulator连接; 通信桥接接口是基于自动驾驶算法引擎的运行时框架选择的。对于使用名为Cyber RT的自定义运行时框架的百度的Apollo平台，LGSVL Simulator提供了一种自定义的通信桥接接口。Autoware.AI、Autoware.Auto在ROS和ROS2上运行，LGSVL Simulator通过标准的开源ROS和ROS2Bridge连接到LGSVL Simulator。下图显示Autoware和Apollo与LGSVL Simulator连接后的仿真情况。

| Autoware与LGSVL Simulator联合仿真                            | Apollo与LGSVL Simulator联合仿真                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![在这里插入图片描述](https://img-blog.csdnimg.cn/4ab6f37a44d64a56b647c7e0104b1d00.png) | ![在这里插入图片描述](https://img-blog.csdnimg.cn/e420bfa46aff464caabd372d55c7c21e.png) |

如果自动驾驶算法引擎接口使用自定义运行时框架，则可以将自定义通信桥接接口作为插件进行集成。此外，LGSVL Simulator支持多个自动驾驶算法引擎接口同时连接。每个自动驾驶算法引擎接口可以通过一个专用的通信桥接接口进行连接，使不同自动驾驶算法引擎接口在统一的模拟环境中进行交互。

## 2.2 仿真引擎

LGSVL Simulator利用Unity的游戏引擎进行仿真，并利用了Unity的最新技术，如高清渲染管道(HDRP)，以模拟与现实世界相匹配的逼真的虚拟环境。仿真引擎的功能可以分为：环境仿真、传感器仿真、主车车辆动力学仿真和控制仿真。
环境仿真包括交通仿真和物理环境仿真，如天气和一天中的时间。交通仿真和物理环境仿真是测试场景仿真的重要模块。环境仿真的所有模块都可以通过Python API进行控制。下图显示了仿真引擎与自动驾驶算法引擎接口的关系。
![在这里插入图片描述](https://img-blog.csdnimg.cn/b00bdbbb6d81444a9bfe45c0e4522cd1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3VwZXJXaXdp,size_19,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 2.3 传感器和主车模型

在LGSVL Simulator中，主车传感器的布置是完全可定制的。LGSVL Simulator的web用户界面接受传感器配置为JSON格式的文本，允许轻松设置传感器的内部和外部参数。每个传感器条目描述传感器类型、位置、发布率、Topic名称和测量值的参考框架。一些传感器也可能有额外的字段来进一步定义规范;例如，每个激光雷达传感器的波束数也是可配置的。

LGSVL Simulator有一组默认的传感器可供选择，目前包括相机、激光雷达、雷达和GPS、IMU以及不同的虚拟ground truth传感器。用户也可以建立自己的定制传感器，并将它们添加到LGSVL Simulator作为传感器插件。下图说明了一些LGSVL Simulator中的传感器:左列显示了一些物理传感器，包括鱼眼相机传感器、激光雷达传感器和雷达传感器;右栏显示了一些虚拟的ground truth传感器，包括分割传感器，深度传感器，3D包围盒传感器。
![在这里插入图片描述](https://img-blog.csdnimg.cn/73ca817dfb354c5b9867bf48f687b82c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAU3VwZXJXaXdp,size_19,color_FFFFFF,t_70,g_se,x_16#pic_center)

对于分割传感器，我们将语义分割和实例分割相结合。用户可以配置哪个语义得到实例分割——具有这些语义的对象的每个实例将得到不同的分割颜色，而其他类型的对象的实例每个语义只能得到一个分割颜色。例如，如果用户只配置了“汽车”和“行人”的实例分割，那么所有的建筑将有相同的分割颜色，所有的道路将有另一种分割颜色。每辆车和每个行人将有不同的分割颜色，但所有汽车的颜色将是相似的(如所有的蓝色)，行人的颜色也是相似的(如所有的红色)。

除了默认的参考传感器，在自动驾驶算法引擎接口中使用的真实世界的传感器模型也在LGSVL Simulator中得到支持。这些传感器插件的参数与真实世界中的传感器相匹配，例如Velodyne VLP-16 LiDAR，并以与真实传感器相同的格式生成点云。此外，用户可以创建自己的传感器插件来实现新的变体，甚至是在默认情况下不支持的新型传感器。

LGSVL Simulator为主车提供了一个基本的汽车动力学模型。此外，车辆动力学系统的建立允许通过功能性模型接口(FMI)共享库，从而集成外部第三方动力学模型。因此，用户可以将LGSVL Simulator与第三方车辆动力学仿真工具结合起来，充分利用这两种系统。

## 2.4 3D环境和高精度地图

虚拟环境是自动驾驶仿真的一个重要组成部分，能够提供许多自动驾驶算法引擎接口需要的输入数据。

环境作为所有传感器的输入源，影响着自动驾驶算法引擎接口的感知、预测和跟踪模块。环境对车辆动力学的影响是车辆控制模块设计的关键因素。它还通过改变高精地图来影响定位和规划模块，这取决于实际的3D环境。最后，三维环境是环境仿真的基础，包括天气、时间、交通agent和其他动态对象。

3D环境可以通过手工创建并用于仿真，也可以通过从记录的数据(图像、点云等)创建真实场景的数字孪生来复制和模拟真实世界的位置。图中显示了LGSVL Simulator为加利福尼亚州森尼维尔市的Borregas大道创建的数字孪生模拟环境。此外，LGSVL Simulator还与北加州的AAA、内华达和犹他州合作，将GoMentum站的一部分做成数字孪生。GoMentum是一家位于加州康科德的自动驾驶算法引擎测试机构，拥有19英里的公路、48个十字路口和8个不同的测试区域，占地面积超过2100英亩。使用GoMentum数字孪生环境，LGSV LSimulator在模拟和测试设施的真实测试车辆上测试了Scenarios。

LGSVL Simulator支持创建、编辑和导出现有3D环境的高精地图。该功能允许用户在3D环境中创建和编辑自定义高精地图注释。虽然3D环境对于仿真道路、建筑、交通agent和环境条件非常有用，但地图注释可以被场景中的其他agent(车辆、行人、可控插件对象)使用。这意味着仿真中的车辆agent将能够遵守交通规则，如交通灯、停车标志、车道和转弯，行人agent可以遵循注释的步行路线等。

LGSVL Simulator提供的地图注释工具有三大功能：导入地图、导出地图、编辑地图注释。可以导入的地图种类有：Apollo5 HD Map、Lanelet2 Map、OpenDRIVE Map。导入地图模块，获取到导入地图的种类后，调用不同种类地图的导入类来分别处理地图数据。对于导入的地图，LGSVL Simulator可以显示地图数据的可视化符号。LGSVL Simulator还支持添加新的地图注释，具体的操作方法是通过新增车道点来自动生成道路。可以导出的地图种类有：Apollo3 HD Map、Apollo5 HD Map、Lanelet2 Map、OpenDRIVE Map、Autoware Vector Map。

## 2.5 场景数据管理

场景数据的存储主要分为高精度地图导出和AssetBundle打包存储两种形式。
![在这里插入图片描述](https://img-blog.csdnimg.cn/87faa1edb2ff4343963b7de003bbd4c2.png#pic_center)场景数据存储的高精度地图导出主要包含Apollo 5 HD、Apollo 3 HD、Autoware Vector、Lanelet2和OpenDRIVE五种格式。用户选择需要导出的高精度地图的格式，MapExporter模块将调用用户选择格式高精度地图的地图工具（分别为ApolloMapTool、AutowareMapTool、Lanelet2MapExporter和OpenDriveMapExporter）解析场景中对应的物体语义、属性以及关键点坐标，最后将以上信息保存为相应的文件格式。

场景数据存储的AssetBundle打包存储要求用户自定义构建场景，并将场景构建中使用的材质、纹理、模型等分类放置在Unity工程的指定文件夹下，并将整个场景保存为场景文件（.unity），再通过Unity官方推荐的AssetBundle打包方式进行打包。

在项目工程中的AssetBundle打包过程中，主要涉及两部分：manifest文件信息的读写和根据信息构建AssetBundle包。manifest文件为相应AssetBundle的信息。
对应于场景数据的存储，场景数据的加载也分为高精度地图导入生成场景和AssetBundle加载两种形式。
![在这里插入图片描述](https://img-blog.csdnimg.cn/98260e92aa8d44d9a7f0f5d799c4820e.png#pic_center)
场景数据加载的高精度地图导入生成场景主要支持Apollo 5 HD、Lanelet2和OpenDRIVE三种格式。用户选择需要导出的高精度地图的格式，并读入相应的高精度地图。MapImporter模块将调用用户选择格式高精度地图的地图导入于解析工具（分别为ApolloMapImporter、Lanelet2MapImporter和OpenDriveMapImporter）读取高精度地图文件信息场景，并根据物体语义、属性以及关键点坐标生成场景。

场景数据加载的AssetBundle加载要求用户选择本地或远程的AssetBundle进行加载。

# 3 软件应用领域

## 3.1 SIL和HIL测试

LGSVL Simulator支持自动驾驶算法引擎接口的软件在环(SIL)和硬件在环(HIL)测试。对于SIL测试，LGSVL Simulator为不同的感知传感器生成数据，例如相机传感器的图像和激光雷达传感器的点云数据，以及自动驾驶算法引擎接口的感知和定位模块使用的GPS和IMU遥测数据。这使得LGSVL Simulator可以对用户的自动驾驶算法引擎接口进行端到端测试。此外，LGSVL Simulator还为其他自动驾驶算法引擎接口模块生成输入数据，以支持单个模块(单元)测试。例如，可以生成3D包围框，模拟感知模块的输出，并作为规划模块的输入。用户可以绕过感知模块(即假设完美感知)，只测试规划模块。

LGSVL Simulator支持一组底盘命令，这样一台运行LGSVL Simulator的机器可以与另一台运行自动驾驶算法引擎接口的机器通信，自动驾驶算法引擎接口可以使用这些底盘命令控制仿真的主车。这使得HIL测试中，自动驾驶算法引擎接口无法区分输入数据是来自真实汽车还是仿真主车，因此，自动驾驶算法引擎接口会向LGSVL Simulator发送控制命令，就像它发送给真实汽车一样。

## 3.2 [机器学习](https://so.csdn.net/so/search?q=机器学习&spm=1001.2101.3001.7020)和数据生成

LGSVL Simulator提供了一个易于使用的Python API，允许收集和存储相机图像和激光雷达数据，并包含各种地面真实信息——遮挡、截断、2D边界框、3D边界框、语义和实例分割等。用户可以编写Python脚本来配置传感器的内部和外部参数，并以自己的格式生成标记数据，用于感知训练。GitHub上提供了一个示例Python脚本来生成KITTI格式的数据。

强化学习是自动驾驶汽车和机器人研究的一个活跃领域，其目标通常是训练规划和控制代理。在强化学习中，一个agent在一个基于策略的环境中采取行动，通常作为DNN来实现，并从环境中获得奖励反馈，反过来被用来修改策略。这个过程通常需要在大量的事件中重复，然后才能得到最优的解决方案。LGSVL Simulator通过Python API提供了与OpenAl Gym的开箱即用集成，使LGSVL Simulator可以作为一个环境，用于增强OpenAl Gym的学习。

## 3.3 V2X系统

除了通过装备的传感器感知世界，自动驾驶汽车还可以从V2X(车辆到一切)通信中受益，比如通过V2V(车辆对车辆)获取其他车辆的信息，通过V21(车辆对基础设施)获取更多的环境信息。在现实世界中测试V2X比测试单一的自动驾驶汽车更加困难，因为它需要联网车辆和基础设施支持。研究人员通常使用模拟器来测试和验证V2X算法。LGSVL Simulator支持创建真实或虚拟传感器插件，用户可以创建特殊的V2X传感器，以从其他车辆(V2V)、行人(V2P)或周围的基础设施(V21)获取信息。这样LGSVL Simulator既可以用来测试V2X系统，也可以用来生成synthetiç数据进行训练。

## 3.4 [智慧城市](https://so.csdn.net/so/search?q=智慧城市&spm=1001.2101.3001.7020)

现代智能城市系统利用路边的传感器来监测交通流量。研究结果可用于控制交通灯，使交通更加顺畅。这种系统需要不同的指标来评估交通状况。一个典型的例子是“停车次数”——一辆汽车通过十字路口的“停车次数”，而“停车”的定义是它的速度在一定时间内下降到低于给定的阈值。这种度量标准的基本事实很难手工收集。LGSVL Simulator也适用于这类应用。使用LGSVL Simulator的传感器插件模型，用户可以定义一种新型的传感器计算“停止”的数量为汽车，因为LGSVL Simulator有确切的速度和位置信息。LGSVL Simulator的可控插件允许用户定制交通灯和其他特殊的交通标志，并通过Python API进行控制。

# 4 小结

LGSVL Simulator是一个基于Unity游戏引擎开发的开源自动驾驶仿真系统。LGSVL Simulator的软件架构主要包含以下模块：

1. 自动驾驶算法引擎接口：多个自动驾驶算法引擎可以通过通信桥接接口与LGSVL Simulator连接；
2. 仿真引擎：使用Unity的HDRP实现了环境仿真、传感器仿真、主车车辆动力学仿真和控制仿真；
3. 传感器与主车模型：用户可以通过json文本选择默认的传感器也可以建立自己的定制传感器；用户可以选择系统的基本汽车动力学模型，也可以通过功能性模型接口集成外部第三方动力学模型；
4. 3D环境和高精度地图：系统提供了多个数字孪生模拟场景，还支持创建、编辑和导出现有3D环境的高精地图；
5. 场景数据管理：系统支持高精度地图的导入导出和基于AssetBundle技术的打包存储。

LGSVL Simulator的应用领域主要包含SIL和HIL测试，机器学习和数据生成，V2X系统以及智慧城市。综合以上分析，LGSVL Simulator是一个功能强大且完善的自动驾驶仿真系统，其开源框架是自动驾驶仿真领域非常好的参考和学习资料。

# 参考文献

[1] Rong G , Shin B H , Tabatabaee H , et al. LGSVL Simulator: A High Fidelity Simulator for Autonomous Driving[C]// 2020.
[2] L. Simulator, “LGSVL Simulator Docs,” 2020. https://www.lgsvlsimulator.com/docs/
[3] L. Simulator. [Online]. Available: https://github.com/lgsvl/simulator