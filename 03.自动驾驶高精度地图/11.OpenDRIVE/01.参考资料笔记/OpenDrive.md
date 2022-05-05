- [OpenDrive | 码农家园 (codenong.com)](https://www.codenong.com/cs105975308/)

**VIRES**
VTD是一个完整的驱动仿真应用工具链。VTD是我们的工具箱，用于在基于道路和铁路的模拟范围内创建、配置、演示和评估虚拟环境。它用于ADAS和自动驾驶系统的开发，以及训练模拟器的核心。它涵盖了从3D内容的生成到复杂交通场景的模拟，最后到简化或物理驱动传感器的模拟。它用于SIL、DIL、VIL和HIL应用程序中，也可以作为包括第三方或自定义软件包在内的联合仿真进行操作。通过开放和模块化设计，它可以很容易地进行接口和集成。

**VTD PROCESS IN 4 STEPS:**
**1. 虚拟世界的创造**
我们的交互式道路网络编辑器（ROD）允许以无限数量的车道、复杂的交叉口、综合性标志和信号来详细设计道路和铁路网络。它从一个源链接和导出逻辑和图形数据。
虚拟世界可以从零开始设计，也可以从现有的数据库块进行编译。各种导入和导出格式以及大型3D模型库和特定于国家的标志/信号加速了创建过程。
所有逻辑数据都按照OpenDrive?格式导出。OpenCRG?数据可以链接到数据库。图形数据的导出可以自定义。

**2.虚拟世界配置**

动态内容由我们的交互式场景编辑确定。它可视化了未打开>数据库，并允许用户作为个别对象和主要实体周围的自主swarms具体贩运。同时，支持左手和右手驱动环境。我们的汽车、脚踏车和驾驶性能的大型图书馆可以很容易地定制。用一个小鼠点击，用户可以为个体实体定义路径，配置信号控制程序，位置对象和从一大组动作添加事件。

**3.虚拟世界模拟**

无论如何，你的环境在（XIL）中运行，你的时间基础看起来（实时或非实时）或附加成分参与（联合模拟）——VTD将是封闭的适配。在任何时候，用户都可以通过整个界面范围（网络、共享存储器等）对模拟的执行进行充分控制，特别是变为步骤和消耗品、图像和传感器数据。任何外部计算机实体都可以注入，多个设施可以在并行或相互连接中运行。

4.虚拟世界的定制

VTD是开箱即用的解决方案，但不会坚持使用此解决方案。用户可以自定义不同级别的VTD。我们为传感器模拟（基于对象列表和基于物理）、动力学模拟和图像生成提供了SDK以及现成的模板。我们针对运行时数据和模拟控制的开放式接口使得在任何环境中集成VTD都很容易。VTD运行在Linux系统上，通过广泛使用网络接口，它不仅是模块化的，而且具有极高的可扩展性。

**OPEN SOLUTIONS**

VIRES是驾驶模拟环境中各种社区项目的主要贡献者。我们的愿景是通过将最好的不同解决方案结合在一起，使世界变得更加兼容和高效。

**1.OpenDrive**
OpenDrive?是在驾驶模拟中描述道路网络的事实标准。它由来自模拟、大地测量和导航行业的专业人员组成的团队进行管理，描述了道路网络的几何和逻辑特性。驾驶动力学、交通或传感器模拟等应用程序依赖于对这些数据的可靠、精确和快速解释。该格式正在不断发展，以满足社区日益增长的需求。

**2.OpenCRG**
OpenCRG?通过一个弯曲的规则网格详细描述了路面的特性。网格上的信息通常由Z偏移、摩擦系数等数据组成，用于轮胎、振动或传感器模拟。OpenCRG是一个开源项目，提供规范、软件和示例数据。它可以链接到OpenDrive，以更精确地描述道路。

**3.OpenScenario**
OpenScenario?将成为描述驾驶模拟中动态内容的未来标准。对于OpenDrive?和OpenCRG?静态内容进行了描述，但越来越需要协调动态流量实体的时间和行为。因此，我们正在一个欧洲研究项目中对本标准的定义进行研究，并将通过软件和无缝集成到我们的VTD套件中来支持本标准。

**Open Drive**
**背景**
OpenDrive?项目始于几年前，当时Vires开始为各种驾驶模拟器构建可视化数据库。为了将数据库与车辆动力学或自主交通进行接口，必须在每个可视化数据库中提供道路网络逻辑的附加描述。
为不同的客户工作有助于理解，尽管使用了不同的格式，但每个模拟所需的道路信息在内容上几乎是相同的。
考虑到这一点，Vires和柏林戴姆勒驾驶模拟器的人员（同时迁移到Sindelfingen）开始开发一种标准化逻辑道路描述的方法，以便于不同模拟器之间的数据交换。
从这一点上说，该项目开始了，并从那时起获得了广泛的用户社区。它被认为是模拟工业中的事实标准。

**团队**
自2006年以来，OpenDrive标准已由驾驶模拟专家核心团队审查和发布。该团队决定于2018年9月7日将管理层移交给ASAM
E.V.，从而将OpenDrive?提升到更高的专业水平。有关此过程的新闻和详细信息，请访问www.asam.net。

**特点**
OpenDrive?文件格式提供以下功能：
· XML格式
· 层次结构
· 道路几何分析定义（平面要素、横向/垂直剖面、车道宽度等）
· 各种车道
· 接头和接头组
· 车道的逻辑连接
· 符号和信号，包括相关性
· 信号控制器（例如用于接头）
· 路面特性（另见OpenCRG）
· 道路和路边物体
· 用户可定义的数据珠等。
OpenDrive?文件旨在描述与道路环境相关的所有数据的整个道路网络。它们不描述作用于道路或与道路相互作用的实体。

**应用**
下图显示了OpenDrive?文件与模拟工具链的典型结合：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507162617332.png)
OpenDrive?数据可能来自道路扫描、导航数据、道路网络设计软件或其他来源。它可以由使用OpenCRG格式的详细表面信息补充。由此产生的道路信息将通过一层评估例行程序提供给车辆动力学、交通模拟和传感器模拟。典型应用要求在强时间约束下实时评估道路信息。

用户可能希望编写自己的例程来评估OpenDrive?文件。或者，基于最新版本的OpenDrive?格式的商业工具可用于高效访问道路数据（http://www.vires.com/docs/OdrManager201307.pdf）有关OpenDrive?的免费工具，请参见下载部分。

**OpenDrive格式规范 1.5**
**1前言**
**1.1 Scope**
OpenDrive?格式为使用可扩展标记语言（XML）语法描述基于轨道的道路网络提供了通用基础。存储在OpenDrive?文件中的数据以分析的方式描述了道路的几何结构以及影响逻辑（如车道、标志、信号）的道路沿线特征。
格式组织在节点中，这些节点可以使用用户定义的数据进行扩展。因此，在保持不同应用程序之间数据交换所需的通用性的同时，对单个应用程序进行高度专门化是可行的。







**1.2 XML Syntax**
OpenDrive?遵循XML定义1.0（请参阅http://www.w3.org/tr/2008/rec-xml20081126/）。使用OpenDrive?1.4，引入了一个单独的命名空间，允许用户在单个上下文中组合各种XML命名空间的内容。

**1.3 Developers**
最初的OpenDrive?格式是由德国Vires SimulationStechnologie GmbH与德国Sindelfingen戴姆勒驾驶模拟器密切合作开发的。
文件格式的内容在发布前由核心团队审查。对于核心团队的当前成员，请访问OpenDrive?网站（www.opendrive.org）。本标准由用户创建，并为其用户创建。因此，如果您觉得有任何遗漏，应澄清或修改，请随时联系我们。特别感谢位于德国布伦瑞克的德国航空航天中心（DLR）的同事们，他们在规范和修订过程中再次提供了非常好和详细的反馈，从而得出当前版本1.5。

**1.4 Point of Contact**
the OpenDRIVE? website ：http://www.opendrive.org
email：opendrive@opendrive.org

**1.5 Example for a Quick Start**
以下示例将指导本规范。它不包含格式的所有可用节点，但很好地说明了关键原则。这是一条无边无际的两车道乡村公路，中间有一条四车道交叉口。此示例也可以在OpenDrive网站上免费下载。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507162902254.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
本小型道路网的OpenDrive文件结构应在以下几页的摘录中进行说明。有关完整的示例文件，请参阅OpenDrive网站上的下载部分。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507162945432.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
所有标签都包含在OpenDrive声明中：



:

标题包含有关道路网的基本信息：



每条道路在类型、平面图、立面图、横截面图等中独立描述，并分别链接到同一网络中的其他道路：
每个道路都有信号和标志：
连接有助于解决不明确的前置-后继关系：
最后，控制器提供了一种分组动态信号的方法：

**2 Conventions**

**2.1 Naming Conventions**
在本文件中，以下惯例适用：
data types：根据IEEE标准给出
track: 表示道路的基准线
**2.2 Units**
本规范中的所有数值均采用国际单位制（除非另有明确说明），例如：
position/distance in [m]
angles in [rad]
time in ▼显示
speed in [m/s]
地理位置将以空间坐标系定义的单位给出（例如，对于WGS84–EPSG 4326，为[度]）。见第2.3.5和5.2.1节。
一些数据标记为您提供了显式地说明给定值的单位的方法。如果没有给出单位，或者没有办法说明单位，则采用国际单位制（见上文）。可用于显式分配的单位有：

**2.3Co-ordinate Systems**
**2.3.1Overview**
下图概述了本规范中最常用的两个坐标系统-轨道坐标和惯性坐标（有关详细信息，请参阅以下章节）。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507163518729.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**2.3.2 Inertial System**
根据ISO
8855，惯性系统是一个右手坐标系统，投影在绘图平面上，轴指向以下方向（参见上一页的图）：
x right
y up
z coming out of drawing plane(右手坐标系，大拇指朝上)
以下公约适用于地理参考：
x east
y north
z up
在惯性系统中，定义了以下角度（正向=逆时针）：
heading around
z-axis, 0.0 = direction of x-axis / east (+π/2 = direction of y-axis /north)
pitch around y’-axis, 0.0 = level (in x’/y’ plane), (+π/2 = direction of negative z-axis)
roll around x’’-axis, 0.0 = level (parallel to x’’/y’’ plane)
下图显示了相应角度的正轴和正方向。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507163808764.png)
**2.3.3 Track System**
轨道坐标系沿道路基准线应用。它是一个右手坐标系。定义了以下自由度：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/202005071638331.png)

S:沿参考线的S位置，从轨道开始测量，在xy平面计算（即不考虑轨道的高程剖面）
T:横向位置，左正
h:上
heading：around h-axis, 0 = forward
pitch ：around t’-axis, 0 = level
roll ：around s’’-axis, 0 = level
下图显示了相应角度的正轴和正方向。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507163928870.png)
**2.3.4 Local System**
本地系统是符合ISO 8855的右手坐标系统，轴指向以下方向：
U向前
V向左
Z向上
在本地系统中，定义了以下角度：
绕Z轴方向，0.0=轨道方向（见下文备注）
绕V’轴倾斜，0.0=水平
绕U’'轴滚动，0.0=水平
下图显示了相应角度的正轴和正方向。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050716403523.png)
本地系统只能通过提供完整的轨道坐标和原点方向来定位在轨道空间中。

**2.3.5Geo-Referencing**
从OpenDrive1.4开始，可以使用格式为“proj4”字符串的投影定义对道路网络进行地理引用（请参见：https://github.com/osgeo/proj.4）
**2.3.6Curvature**
对于曲率指示，以下惯例适用：
· 正曲率左曲线（逆时针运动）
· 负曲率右曲线（顺时针运动）

**3 Road Layout**
**3.1 General**
下图描述了本规范所涵盖的道路布局原则：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507164545149.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**所有道路都由定义基本几何图形（圆弧、直线等）的参考线组成**。沿着参考线，可以定义道路的各种特性。例如，高程剖面、车道、交通标志等。道路可以直接连接（当两条给定道路之间只有一个连接时）或通过连接（当一条给定道路与其他道路之间有多个连接时）。
所有属性都可以根据本规范中规定的标准和用户定义的数据（可选）进行参数化。
该约定适用于沿单个引用行定义的同一类型的属性必须按升序列出。这意味着属性的起始坐标（参数s，请参见上文）必须等于或大于同一轨道上相同类型的前一属性的起始坐标。

**3.2 Reference Line (Track)**
参考线的几何图形被描述为各种类型的基本体序列。可用的基本体包括：
l 直线（常零曲率）
l 螺旋（曲率线性变化）
l 曲线（沿运行长度的恒定非零曲率）
l 立方聚乙烯
l 参数三次曲线
下图说明了来自上述部分元素的参考线的组成。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507193356602.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**3.3 Lanes**
**3.3.1 General**
车道由以下数字标识：
-唯一（每个车道段，见下文）
-按顺序（即无间隙）
-从参考线上的0开始
-向上向左（正T方向）
-向右下降（负T方向）
车道总数不受限制。参考线本身定义为车道零，不能有宽度条目（即其宽度必须始终为0.0）。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507193508103.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
根据这个定义，很明显相邻车道之间没有间隙。但是，可以定义车辆或其他道路使用者不得使用的车道，参见第6.5章关于车道类型。

**3.3.2 Lane Offset**
在某些情况下（例如，作为最内侧车道插入的左转车道），可能更方便地改变车道轮廓而不是改变基准线。为此，车道0可以使用立方体polynom进行偏移：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507193815257.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**3.3.3 Lane Sections**
在道路的给定横截面中出现的车道在所谓的车道截面中定义。多车道路段可沿参考线按升序定义。在定义下一个车道段之前，每个车道段都是有效的。因此，为了便于使用，每条道路必须至少配备一个从S=0.0 m开始的车道段。
下图描述了车道段的原理：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507193848272.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
每个车道段的车道数是恒定的。但是，每条车道的特性（例如宽度、路标、摩擦等）可能会发生变化。
在OpenDrive1.4之前，每个车道段必须包含道路两个方向的所有车道定义。在某些情况下（例如，仅在道路一侧增加或减少车道，或两侧车道数发生变化，但有轻微的纵向偏移），这可能导致限制或增加复杂道路布局定义的工作量。
下图说明了典型高速公路入口/出口的经典定义，其中各入口/出口车道的纵向偏移量为：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507193929153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
从OpenDrive1.4开始，车道段仅对道路的一侧（行驶方向）有效。因此，上述示例可以简化如下：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507193947758.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**3.3.4 Lane Properties**
车道特性是相对于相应车道段的起点定义的。对于车道段的起点，偏移量从0.0开始，并随着道路坐标的增加而增加。在定义相同类型的新特性或车道段结束之前，车道属性是有效的。相同类型的车道属性必须按升序定义。车道特性可以是点或范围特性，也就是说，它们分别在给定点或给定范围内有效。
如果某个属性未在给定车道段内定义或未覆盖整个路段，则应用程序可以假定默认值。

**3.4 Superelevation and Crossfall(超高和横坡)**
在许多情况下，道路的横截面不会与下面的地形平行。相反，它将升高到一侧（例如在曲线中）或中心（用于排水）。这两个属性都由OpenDrive格式覆盖，前者称为“超高”，后者称为“横向坡度”。下图说明了这两个属性：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507194034301.png)
可以叠加超高和横坡，以便在有横坡的直线段和有超高的曲线之间提供平滑过渡。
从上图可以看出，超高是按照整个道路横截面定义的，而横坡是按照道路的每侧定义的。
单车道可以从超高和横坡特性的应用中排除。例如，人行道将始终在水平面上运行（参见下图）
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507194100910.png)
超高入口的定义不会改变车道的实际宽度，而是改变车道的投影宽度；而对于横坡定义，则相反。
随着“横向剖面”的引入（见下一章），横斜度特性可能会被视为一种特殊情况，或者完全过时。在新的数据库中，建议首选横向轮廓的定义，而不是交叉下降特性。

**3.5 Lateral Profile**
一些横向道路形状可能比单用超高和横坡来描述更复杂。描述这些复杂形状的一种方法是使用显式表面数据（见下文）。在OpenDrive 1.4的格式规范中增加了描述横向轮廓的分析方法。例如，这就允许对弯曲路面进行描述，因为它们可以在高速测试轨道上找到。请注意，“超高”属性作用于道路的基准面，而不是侧面轮廓本身。
侧面轮廓不得与“横坡”特性结合。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507194133237.png)
横向纵断面入口的定义不会改变车道的投影宽度（但是额外的超高入口，见上一章）。
**3.6 Roadb Linkage**
**3.6.1Overview**
为了在道路网络中导航，应用程序必须了解不同道路之间的链接。有两种类型的连接方式：
l 后继/前任链接方式（标准联动装置）
l 交叉点
当两条道路之间的连接清晰时，标准的连接信息就足够了。当道路的继承关系或前任关系不明确时，需要交叉口。在这里，应用程序需要选择几种可能性中的一种。
下图和表说明了不同的情况：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507194212595.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507194237633.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
说明：上表和图说明了接头内的一些可能连接。3号、4号和5号公路被称为“连接道路”。对他们来说，2号路就是所谓的“进路”。道路6、7和8是“出站道路”（对于图中未显示的其他连接，它们也可以作为“进站道路”）。
为了方便在每条车道上通过路网进行导航，可以在车道级别上提供附加的连接信息。
**3.6.2Junctions**
**3.6.2.1Basics**
基本原理非常简单：交叉口通过道路（连接道路）连接进入道路和出口道路。下图显示了一个复杂的连接场景：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507194315111.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
根据这里为所有其他道路制定的规则，连接道路也被建模为道路。它们由带有车道段等的参考线组成。通常，在进口道路中也用作出口道路，道路的实际使用情况由其车道决定。
交叉口由一个连接矩阵组成，该矩阵表示从给定的进入道路进入连接道路的所有可能性。为了便于导航，这些连接按每条车道列出。一旦进入一条连接道路，就可以从存储在每条道路上的一般继承/前任信息中检索到与相应引出道路的以下连接。在交叉口内，可以存储道路相对的优先顺序，但也可以通过评估标志/信号和几何图形来检索这些优先顺序。
作为交叉口的一部分，U形转弯必须建模为单独的连接道路，从而为连接矩阵增加一个可能性。

**3.6.2.2 Junction Groups**
在某些情况下（例如环形交叉口），交叉口必须了解彼此的存在，以便更有效地实施交通规则（通行权）。根据OpenDrive风格指南，环形交叉口应包括单独的交叉点：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507194413898.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
将所有这些交叉口放在一个组中可能有助于交通模拟软件：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507194431697.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)**3.6.3Neighbors**
道路不仅可以连接到前辈和后继者，还可以连接到邻居。当每条参考线只定义了一个行驶方向（即只有左车道或只有右车道）或每条参考线只定义了总车道数的一小部分时，可能需要这种类型的连接信息。每条道路最多可有两个邻居。
示例1（两条道路彼此相邻）：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/202005071944558.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507194522528.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
邻居条目主要是出于遗留目的而引入的。对于新道路网的设计，建议沿一条参考线确定道路的两个行驶方向，并避免使用相邻入口（该建议仅适用于交叉口外部；在交叉口内，每辆车可以并应使用一条参考线然而，G方向）。
**3.7 Surface**
OpenDrive提供了两种描述表面特性的方法：
l 标准说明：在标准情况下，可为道路车道定义<材料>记录，提供参数、表面材料代码、粗糙度或摩擦。
l 扩展描述：关于路面数据（例如测量数据）的更详细说明（不限于车道边界内材料特性的定义）可在新引入的记录中提供。该数据可应用于整个横截面或部分横截面；由于包含在详细表面信息中的数据量可能非常大，并且由于表面数据已经存在公共可用的数据格式，OpenDrive不定义其自己的表面描述XML实现，而是提供对资源的引用。而是检查数据文件；下面列出了官方支持的作为OpenDrive表面导入格式的格式。此列表可能会在OpenDrive的未来版本中扩展，这取决于附加格式所提供的用途；为了保证OpenDrive描述的可移植性，本标准建议使用OpenCRG格式作为首选的扩展路面描述。
支持的表面数据格式列表：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507194604766.png)
**3.8Alternative Layouts**
根据各个应用程序的不同，可能需要描述道路网络属性的各种可能设置，以便OpenDrive文件不仅包含这些属性的一种描述，而且提供对所有预定义设置的访问。
为此，引入了记录（见5.10）。它允许用户在其级别内定义属性的可选实例。
记录可以在任何级别上使用而不受限制，但是用户应考虑到OpenDrive道路描述的可移植性可能会受到在其他用户可能不期望的级别上定义集合的影响。将来，本文件应包含在应用程序中成功使用集合的提示，以说明鼓励使用集合的位置和应避免使用集合的位置。
**3.9Railroad Elements（铁路要素）**
免责声明：OpenDrive并未设计用于描述复杂的铁路网络，包括信号、安全系统（如欧洲铁路交通管理系统-ERTMS）等。为此，存在其他格式（如http://www.railml.org）。就在铁路和道路交汇的情况下，OpenDrive应提供一种方法，以单个文件和格式描述这两种功能。
城市地区的道路网有时必须包括铁路。有轨电车（即有轨电车）与其他车辆共用空间或在指定区域运行时尤其如此。在许多情况下，共享/独占空间的使用在网络中不断变化。
**3.9.1Railroad Tracks**
对于轨道车辆，横向运动的自由度受到很大限制（脱轨情况除外）。因此，为“有轨电车”或（更一般的）“轨道”类型的铁路提供一条车道，并以基准线为中心（这可以通过对0号车道应用偏移来实现，见图）：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507194701741.png)
注：对于具有多条平行轨道的铁路线（通常为双轨布局），每个方向必须使用一条独立轨道，而不是向现有轨道添加铁路车道。还建议为轨道车辆提供单独的参考线（每个参考线正好承载一条车道），即使是在与道路车辆共用的区域。
**3.9.2Railroad Switches**
在道路车辆的交叉路口可能变得相当复杂的地方，轨道上的开关总是根据其位置提供一条清晰的路径。因此，不需要连接矩阵，甚至可以在轨道内放置开关（即主轨道的参考线可以继续），见下图：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507194729904.png)
轨道1和3是主轨道，而轨道2连接轨道1和3。由轨道车辆驱动的路径仅取决于开关#12和#32的逻辑状态。因此，方向控制由车辆上的网络执行（与基于道路的车辆完全相反）。

**3.9.3 Railroad Stations**
铁路线可能包含车站，也可能包含站台。为了定义站点的空间范围，至少需要一个平台入口。
平台ID必须是唯一的，但多个轨道可能引用同一平台。另见下图。在上面的情况下，轨道1和3将引用同一平台，在下面的情况下，轨道1和2将引用平台2，而轨道3仅引用平台1。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507194759488.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**4 File**
**4.1 Format**
OpenDrive?数据存储在XML文件中。
**4.2 Extension**
OpenDrive?文件扩展名为“.xodr”。
压缩的OpenDrive?文件扩展名为“.xodrz”（压缩格式：gzip）
**4.3Structure**
OpenDrive?文件结构根据XML规则并参考各自的模式文件进行布局。
元素按层次排列。级别大于零（0）元素的是前一级别的子级。1级元素称为初级元素。
可以使用用户定义的数据扩展每个元素。这些数据存储在所谓的辅助元素中。
**4.4Notation**
默认情况下，所有浮点数都是“双精度”。强烈建议对浮点数使用16位科学表示法。
**4.5 Schema**
OpenDrive?格式的模式文件可从www.opendrive.org中检索。
**4.6Combining Files**
可以在适当的位置通过include标记组合多个文件。分析此标记后，OpenDrive阅读器应立即开始读取指定为标记属性的文件。用户有责任确保从include文件读取的内容与从中开始包含的上下文一致。
出现include标记的父标记必须同时出现在父文件和included文件中。
例如：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507203206878.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**4.7Overview of Beads**
下表提供了OpenDrive?文件中可能出现的所有beads的简化概述。它还指示单个父对象下给定beads类型的最大实例数。命名约定如下：
-1只需要一个实例
-0…n最多n个实例（通常n=1），可选
-0+实例数量不限，可选
-至少1个实例，必需
如果省略了各自的父级，则不能出现可选bead的子级。bead的水平由缩进和适当的数字表示。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507214820137.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507214829948.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050721484696.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
在任何水平上，可能有以下bead：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507214901119.png)
**5 File Entries(文件条目)**
本章列出了所有可能的文件条目。标记是必需的还是可选的，可以通过指示各个标记的实例数来派生。标记中的属性是必需的还是可选的，可以从“opt.”（可选）列中的指示派生。在该列中标记有“√”的每个属性都是可选属性，其他的都是强制性的。
**5.1Enclosing Tag（封闭标签）**
文件的整体封闭标记是：
delimiters

> …

instances 1

attributes xmlns=“http://www.opendrive.org”

**5.2 Header**
头记录是OpenDrive标记中的第一个记录。
Delimiters（分隔符）

…

parent
instances 1
attributes
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507214958306.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.2.1 Geo Reference**
数据库地理参考信息可以作为OpenDrive头节点的子节点提供。它将提供将OpenDrive的笛卡尔X/Y/Z坐标转换为相应地理参考系统所需的所有信息。地理投影的定义不得超过一个。
如果缺少定义，笛卡尔坐标将是数据库中唯一可用的坐标。
delimiters …
parent

instances 0…1
attributes none
根据proj4（另见https://github.com/osgeo/proj.4）的投影字符串应作为节点的cdata子节点提供，因为它可能包含干扰节点属性的XML语法的字符。
Example（http://spatialreference.org/ref/epsg/32632/proj4/):
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215057634.png)
**5.2.2 Offset**
偏移记录允许数据库的惯性重新定位和重新定向，同时将所有原始坐标保存在适当的记录中。此记录的主要用途是重新定位道路数据，这些数据是由测量得出的，或者是在更大的组合中重新定位数据库片段。
数据集首先由x、y和z转换，然后由hdg围绕新原点旋转。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215122477.png)
注意：如果给定了地理参考记录，则最好处理该记录中的所有偏移量，而不是使用本章中定义的其他<偏移量>记录。
**5.3 Road Records**
道路是数据库中信息的主要容器。有关可存储在道路中的记录的概述，请参阅第4.7章。
**5.3.1 Road Header Record**
“道路标题”记录定义单个道路的基本参数。紧接着是定义道路几何和逻辑特性的其他记录。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215156470.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.2 Road Link Record**
如果道路（与往常一样）链接到继承者、前任或邻居（请参见链接记录的子项），则此记录将跟随道路标题记录。孤立的道路可能会忽略这一记录。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215219530.png)
**5.3.2.1 Road Predecessor**
此记录提供有关道路前身的详细信息。前身可以是道路或交叉口类型。链路指示可能有两种变体。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215243591.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.2.2 Road Successor**
此记录提供有关道路继承人的详细信息。继承人可以是道路或交叉口类型。链路指示可能有两种变体。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215310104.png)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215316826.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.2.3 Road Neighbor**
此记录提供有关道路邻居的详细信息。邻居必须是“道路”类型。
注意：neighbor条目主要用于遗留目的。对于新道路网的设计，建议沿一条参考线确定道路的两个行驶方向，并避免使用相邻入口（本建议仅适用于外部交叉点；但是，在交叉点内，单个参考线可用于并应用于每个行驶方向）。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215343877.png)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215352399.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
5.3.3 Road Type Record
道路的类型可能在其整个长度上发生变化。因此，对道路某一路段的道路类型定义提供了单独的记录。道路类型入口对道路的整个横截面有效。在提供新的道路类型记录或道路结束之前，它也是有效的。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215414447.png)
**5.3.3.1 Speed Record**
此记录定义了与指定道路类型一起允许的默认最大速度。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215435797.png)
**5.3.4 Road Plan View Record**
平面图记录包含一系列几何记录，这些几何记录定义了X/Y平面（平面图）中道路参考线的布局。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215501389.png)
**5.3.4.1 Road Geometry Header Record**
道路几何记录序列定义了X/Y平面（平面图）中道路参考线的布局。几何记录必须以升序出现（即增加S位置）。几何信息被拆分为所有几何元素共用的标题和包含实际几何元素数据的后续bead（取决于几何元素的类型）。
目前，支持五种类型的几何元素：
l 直线
l 螺旋形
l 弧线
l 立方多项式
l 参数三次多项式
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215555147.png)
此记录后面紧跟一条记录，其中包含有关实际几何元素的更多信息。
**5.3.4.1.1 Geometry, Line Record**
此记录将直线描述为道路参考线的一部分。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215622509.png)
**5.3.4.1.2 Geometry, Spiral Record (Clothoids：回旋曲线)**
此记录将螺旋线描述为道路参考线的一部分。对于这种类型的螺旋，元素开始和结束之间的曲率变化是线性的。
为了在各种实施的螺旋评估之间提供一致性，强烈建议用户使用相同的算法，或至少在适用范围内（即曲率、螺旋长度等）具有合理精度的算法。
回旋曲线理论链接：
http://en.wikipedia.org/wiki/Euler_spiral
计算回旋曲线的库可以下载到：
http://www.opendrive.org/downloads/spiral.zip
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215726187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.4.1.3 Geometry, Arc Record**
此记录将圆弧描述为道路参考线的一部分。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215754658.png)
**5.3.4.1.4 Geometry, Cubic Polynomial Record**
此记录将一个三次多项式描述为道路参考线的一部分。多项式描述在起点的局部U/V坐标系中（U指向局部S方向，V指向局部T方向）。每个局部坐标的计算方法如下：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215820376.png)
通过相对于起点的简单几何变换（即一个平移和一个旋转），可以轻松地将U/V坐标转换为X/Y坐标。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215844375.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
注意：为了根据节点坚持到起点和方向，参数a和b必须为零。根据下图，为这些参数提供非零值将导致S/T坐标的移动和旋转：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215903218.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.4.1.5 Geometry, Parametric Cubic Curve Record**
此记录描述了一条参数化的三次曲线，作为局部U/V坐标系中道路参考线的一部分。该曲线由两个三阶多项式组成，用一个公共参数（P）来描述局部u-和vcoordinates。除非另有说明，参数p在范围[0；1]内。或者，可以在范围[0；弧长]内给出。必须用属性“prange”说明与默认规则的任何偏差，并且必须相应地调整多项式的参数。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050721593052.png)
如前一章（三次多项式记录）所述，将局部U/V坐标转换为惯性X/Y坐标。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507215947110.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
注：为了根据节点坚持到起点和方向，参数au、av和bv必须为零。为这些参数提供非零值将导致S/T坐标的移动和旋转（另见上一章中的图）。

**5.3.5 Road Elevation Profile Record（道路高程剖面记录）**
高程剖面记录包含一系列高程记录，这些记录定义了道路沿参考线的高程特征。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507220011719.png)
**5.3.5.1 Road Elevation Record**
高程记录定义给定参考线位置的高程条目。必须沿参考线按递增顺序定义条目。在下一个入口开始或道路参考线结束之前，入口的参数是有效的。默认情况下，道路的高程为零。
高程存储在一个三阶多项式函数中。它看起来像：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507220040119.png)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507221240515.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507221300802.png)
5.3.6 Road Lateral Profile Record
横向剖面记录包含一系列超高和横坡记录，这些记录定义了沿基准线的路面倾斜特性。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507221326404.png)
**5.3.6.1 Road Superelevation Record(道路超高记录)**
道路超高定义为路段绕S轴的侧倾角度（超高为正，表示路面“下降”到右侧，即下图显示负超高）。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507221427543.png)
每个超高记录在给定参考线位置定义一个条目。必须沿参考线按递增顺序定义条目。在下一个入口开始或道路参考线结束之前，入口的参数是有效的。默认情况下，道路的超高为零。

超高存储在一个三阶多项式函数中。它看起来像：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507221451313.png)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050722150648.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050722151747.png)
**5.3.6.2 Crossfall Record(横向坡度记录)**
道路横坡定义为路面相对于T轴的角度。正横坡导致路面从参考线“下降”到外边界。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507221609289.png)
横坡可在道路两侧定义。它将应用于各侧的每一条车道，这些车道并未明确排除在其应用之外。
横斜度存储在一个三阶多项式函数中。它看起来像：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507221627781.png)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050722164325.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507221656558.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.6.3 Road Shape Record**
道路形状定义为相对于参考平面的路段表面（由于现有的超高，其本身可能围绕S轴倾斜）。对于给定的“S”站，此形状可以描述为一系列三阶多项式。可以在连续的“S”站沿道路定义多个形状。在这些站点的形状之间，应沿S进行线性插值。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507221746929.png)
形状多项式如下：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507222821674.png)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507222840874.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507222848131.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.7 Lanes Record**
车道记录包含一系列车道断面记录，这些记录定义了道路横断面相对于参考线沿线车道的特征。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507222915582.png)
5.3.7.1 Lane Offset Record
车道偏移记录定义了车道参考线的横向偏移（通常与道路参考线相同）。这可用于相对于道路基准线轻松实现车道（局部）横向移动。特别是城市内部布局或“2+1”越野道路布局的建模，可以通过此功能大大简化。
下图说明了沿直线参考线的附加“内部”车道的建模：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507222939216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
用三阶多项式函数计算给定点的实际偏移量。如：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507222957248.png)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507224926892.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507224935193.png)
如果车道段没有定义车道偏移记录，则车道参考线与道路参考线相同。
**5.3.7.2 Lane Section Record**
车道断面记录定义了道路沿线道路横断面的特征。它规定了车道的数量和类型以及车道的进一步特征。为了使用道路，必须至少定义一个记录。车道段记录在定义新的车道段记录之前有效。如果定义了多车道区段记录，则必须按升序列出这些记录。如果多个单侧车道段记录从相同位置开始，则其顺序是任意的。如果属性“singleside”未给定或为false，则记录对所有边都有效。
实际车道及其属性分别是车道段记录和车道记录的子项。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507225011218.png)
**5.3.7.2.1Left / Center / Right Records**
为了更方便地通过道路描述进行导航，车道部分下的车道分为左车道、中车道和右车道。必须至少存在一个条目（左、中或右）。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507225038948.png)
重要提示：根据车道对交通的意义，车道的实际区分是通过查看每条车道的ID来实现的（见下一章；左车道：正，右车道：负，中心车道：零）。分隔符仅在道路数据库中提供更好的导航。
**5.3.7.2.1.1 Lane Record**
车道记录位于左/中/右记录中。它们定义实际车道的ID（因此，它们在道路上的位置，请参见3中的约定）。为了防止混淆，车道记录应该表示从左到右的车道（即具有降序ID）。车道的所有属性都定义为车道记录的子级。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507225335106.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
对于车道段内的每条车道，需要车道记录。如果应引入新车道或现有车道将要结束，则需要新车道断面记录。车道段内的车道数是恒定的。
**5.3.7.2.1.1.1 Lane Link Record**
为了方便在每车道的基础上通过道路网络进行导航，应向车道提供前导/后继信息。只有当车道在交叉口处结束或没有物理连接时，才应忽略此记录。
对于同一物理道路上的车道之间的连接（即相同的参考线），车道前导/后继信息提供了前面或后面车道段上的车道ID。对于不同道路上车道之间的连接，即没有交叉口的直接连接道路，完整的连接信息必须由相应的道路连接记录和车道连接记录组成。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230100900.png)
**5.3.7.2.1.1.1.1 Lane Predecessor**
此记录提供了有关车道前导的详细信息。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230126935.png)
**5.3.7.2.1.1.1.2 Lane Successor**
此记录提供有关车道继承者的详细信息。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230221876.png)
实例1：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230235122.png)
实例2：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230243884.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.7.2.1.1.2 Lane Width Record**
道路横截面内的每条车道都可以设置多个宽度入口。每个车道必须至少定义一个入口，中心车道除外，按照惯例，中心车道的宽度为零（见3.2）。在定义新条目之前，每个条目都是有效的。如果为一条车道定义了多个条目，则必须按升序列出。
用三阶多项式函数计算给定点的实际宽度。它看起来像：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230310954.png)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230334826.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
下图说明了给定范围内宽度不同的车道的这种惯例：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050723035725.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
每次多项式函数改变时都需要一个新的宽度项。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230421171.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.7.2.1.1.3 Lane Border Record**
相对于通过宽度条目来描述车道，直接描述车道的外部边界反而更加方便，即独立于任何内部车道的特征（内部车道=比当前定义的车道具有更低绝对数字ID的每条车道）。尤其是在道路数据是由测量得出的情况下，这种类型的定义将提供一种更方便的方法，而无需创建大量车道段。
车道相同区域的宽度和边界记录相互排斥。如果一个车道段内的一条车道同时给出了两个入口（宽度和边界），则仅以宽度入口为准。
如果车道段中存在边界定义，则偏移记录的定义（参见“车道偏移记录”）将过时。
用三阶多项式函数计算给定点的实际（横向）边界位置。它看起来像：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230456631.png)![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230511430.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230528821.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)每次多项式函数更改时都需要新的边界项。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230556398.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)**5.3.7.2.1.1.4 Road Mark Record**
道路横截面内的每条车道都可以设置若干个道路标记入口。道路标记信息定义了车道外部边界处的线条样式。对于左车道，这是左边界，对于右车道，这是右边界。分隔左右车道的线的样式由车道0（即中心车道）的道路标记入口决定。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230621158.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
参数权重可用于对道路标记宽度的分类定义（例如，根据相应国家的设计规则，“标准”和“粗体”），而可选参数宽度可用于对单个道路标记宽度的精确定义。可能需要这样做，例如在偏离通用设计规则的情况下。
为了精确评估道路标志的边界，包括标志的宽度，公约应适用于道路标志的中心线始终位于各自车道的外部边界线上（以便道路标志的外部半部分物理上放置在下一条车道上）。
插图：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230641860.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.7.2.1.1.4.1 Road Mark – Sway Definition(摆动定义)**
摆动定义扩展了定义。它为即将到来的（显式）类型定义重新定位横向引用位置。摆动偏移相对于道路标记的名义参考位置（即车道边界）。
用三阶多项式函数计算给定点的实际（横向）参考位置。形如：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230759139.png)![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230811383.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230822445.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.7.2.1.1.4.2 Road Mark Type**
道路标记可根据其类型进一步描述。不向路标标签提供附加参数，应将类型定义定义为路标标签的子项。每个类型定义必须包含一个或多个线条定义，其中包含有关道路标记所组成线条的附加信息。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230847394.png)
**5.3.7.2.1.1.4.2.1 Road Mark Type – Line Definition**
道路标志可以由一个或多个要素组成。多个元素通常并排放置。当前，元素只能是行。每条线可以通过其线/空间设置和到道路标记参考位置的横向偏移（即两条车道之间的边界或由记录定义的位置）来定义。线条定义对给定长度（即可见线条和不可见空间的总和）有效，并将自动重复。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230914479.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230928398.png)**5.3.7.2.1.1.4.3 Explicit Road Mark Definition**
高度不规则的道路标记数据可通过每个可见行的单独显式条目进行定义。与记录相比，没有重复任何定义的固有概念；因此，只有显式定义的条目才有效。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507230954657.png)
**5.3.7.2.1.1.4.3.1 Explicit Road Mark Definition – Line Entry**
此条目在显式道路标记定义中指定单行。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050723105181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)**5.3.7.2.1.1.5 Lane Material Record**
道路横截面内的每条车道可设置若干条条目，以定义其材料。在定义新条目之前，每个条目都是有效的。如果定义了多个条目，则必须按递增顺序列出它们。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507231144516.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.7.2.1.1.6 Lane Visibility Record(车道可见度记录)**
道路横截面内的每一条车道可设置若干条入口，以确定相对于车道方向四个方向上的能见度。在定义新条目之前，每个条目都是有效的。如果定义了多个条目，则必须按递增顺序列出它们。
对于左车道（正ID），前进方向与轨道方向相反，对于右车道，前进方向与轨道方向相同。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507231211261.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)**5.3.7.2.1.1.7 Lane Speed Record**
此记录定义给定车道上允许的最大速度。在定义新条目之前，每个条目在增加坐标的方向上都是有效的。如果定义了多个条目，则必须按递增顺序列出它们。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507231306699.png)
**5.3.7.2.1.1.8 Lane Access Record(车道出入记录)**
此记录为特定类型的道路使用者定义访问限制。该记录可用于补充由标志或信号引起的限制，以控制场景中的交通流。在定义新条目之前，每个条目在增加坐标的方向上都是有效的。如果定义了多个条目，则必须按递增顺序列出；但是，条目可以从相同的偏移位置开始。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050723133894.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.7.2.1.1.9 Lane Height Record**
车道表面可与参考线定义的平面以及相应的高程和横坡入口（例如，人行道通常比路面高出几厘米）偏移。高度记录提供了一种简单的方法来描述这种偏移，方法是在车道纵断面的离散位置设置与道路水平的内部和外部偏移。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507231504914.png)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507231511226.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
5.3.7.2.1.1.10 Lane Rule Record
车道属性可以通过附加规则进一步描述，这些规则不包含在本格式规范中定义的任何其他车道属性中。例如，“禁止任何时间停车”、“禁止停车”等规则可能适用于整个车道（在美国，路缘石将显示相应的颜色），并且可能不会隐式地从颜色、标志等衍生出来。目前，规则的描述取决于用户。OpenDrive内的正式标准化将根据用户反馈进行。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507231759717.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.8 Road Objects Record**
对象记录是道路上所有对象的容器。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507231823285.png)
**5.3.8.1 Object Record**

介绍了对象记录，用于描述参考给定道路的常见三维对象。在将来的版本中，最常用的对象类型可能成为OpenDrive规范的一部分。当前已知和常用对象类型的定义在相应章节中给出。

对象轮廓和边界框可能变得相当复杂。为了方便定义与记录相关的边界框或边界圆柱体，用户可以指定属性
l 宽度、长度和高度（边界框）或
l 半径和高度（包围筒）
有关详细信息，请参见下表。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507232007521.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507232059759.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
圆形（或圆柱形）物体图示：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507232116989.png)
停车位示意图（与道路和偏移角度对齐）：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507232129736.png)
**5.3.8.1.1 Object Repeat Record**
为了避免同一对象的多个实例有多个定义，可以为原始对象定义重复参数。可以覆盖对象节点中的某些参数，以指定线性过渡，例如对象的横向偏移。对于对象重复记录中未指定的属性，以各自对象记录的属性为准。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507232152571.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050723220166.png)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050723225149.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507232257426.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507232306440.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.8.1.2 Outlines Record（大纲记录）**
对象记录的默认参数允许在数据库中放置具有矩形和圆形示意图的对象。但是，用户可能需要描述道路沿线的线性特征以及非矩形的多边形区域。为此，可使用大纲记录。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507232401210.png)
**5.3.8.1.2.1 Object Outline Record**
轮廓记录定义了一系列角点，包括物体范围的高度信息，包括物体坐标或相对于道路参考线的高度信息（不要使用混合定义！）.对于区域，最好按CCW顺序列出点。
仍应支持OpenDrive1.4大纲定义（不带父标记）。
大纲记录后必须至少有一个角记录。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507233026602.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.8.1.2.1.1 CornerRoad(转弯道路)**
以道路坐标定义对象轮廓上的角点。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507233130713.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.8.1.2.1.2 CornerLocal(转角局部)**
在局部U/V坐标中，定义对象轮廓上相对于对象轴心点的角点。轴点和对象的方向由条目的s/t/heading参数给出。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507233150654.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.8.1.3 Object Material Record**
对于路面内（通常与路面共面）且表示局部偏离标准道路材料的补片等对象，需要对材料特性进行描述。本说明取代了道路材料记录提供的说明，并且仅在父道路对象的轮廓内有效。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507233212256.png)
5.3.8.1.4 Lane Validity Record(车道有效性记录)
默认情况下，对象对指向对象方向的所有车道都有效。可以用对象的显式有效性信息替换此默认有效性。有效性记录是对象记录的可选子记录。每个对象可以定义多个有效性记录。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507233545436.png)
注意：对于对象的单车道有效性，请为FromLane和Tolane提供相同的值。
**5.3.8.1.5 Parking Space Record**
停车位是一种特殊类型的矩形物体，它可以承载更多的信息，而不仅仅是其空间范围。因此，可以选择在对象条目中添加细节，以定义更复杂的停车位情况。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507234540629.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.8.1.6 Markings Record**
通过标记记录，可以通过多个标记元素描述物体的外观（通常为“停车位”类型）。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050723522980.png)
**5.3.8.1.6.1 Marking Record**
指定附加到对象边界框一侧或引用轮廓点的标记。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507235541194.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.8.1.6.1.1 Corner Reference Record**
通过引用现有轮廓点指定标记或边框的角点.
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507235605269.png)
**5.3.8.1.7Borders Record**
通过引用现有轮廓点指定点。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200507235628953.png)
**5.3.8.1.7.1 Border Record**
指定沿某些轮廓点的边框。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508000926115.png)
**5.3.8.2 Object Reference Record**
由于同一对象可能来自多条道路，因此提供了相应的记录。但是，这要求要引用的对象具有唯一的ID。对象引用记录由主记录和可选的车道有效性记录组成。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001009638.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)**5.3.8.3 Tunnel Record**
隧道记录（与目标记录一样）适用于给定范围内的整个道路横截面，除非将带有进一步限制的车道有效性记录作为子记录提供。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001052971.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.8.4 Bridge Record**
桥梁记录与目标记录一样，适用于给定范围内的整个道路横截面，除非提供了带有进一步限制的车道有效性记录作为子记录。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200508001129388.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70
**5.3.9 Road Signals Record**
信号记录是道路上所有信号的容器。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050800115911.png)
**5.3.9.1 Signal Record**
信号记录用于提供有关道路标志和信号的信息。信号是可以动态改变其状态的标志（例如交通灯）。信号记录包括主记录和可选车道有效性记录。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001224625.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001232419.png)
注意：属性objectReference已被子记录引用替换。

**5.3.9.1.1 Lane Validity Record**

默认情况下，信号对指向信号方向的所有车道都有效。可以用信号的显式有效性信息替换此默认有效性。有效性记录是信号记录的可选子记录。每个信号可定义多个有效性记录。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001256563.png)
注：对于信号的单车道有效性，请为FromLane和Tolane提供相同的值。

**5.3.9.1.2 Signal Dependency Record**

信号依赖记录为信号提供控制其他信号的方法。标志可以限制各种类型车辆的其他标志，红灯时可以打开警示灯等。信号依赖记录是信号记录的可选子记录。一个信号可能有多个依赖记录。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001316941.png)
**5.3.9.1.4 Physical Signal Position Record, Road Co-ordinates**

在实际（物理）信号位置偏离其逻辑位置的情况下，应采用惯性或道路坐标来描述物理位置的参考点（因为该位置可能或可能与任何道路相关）。道路位置入口定义如下：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050800134350.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.9.1.5 Physical Signal Position Record, Inertial Co-ordinates**

在实际（物理）信号位置偏离其逻辑位置的情况下，应采用惯性或道路坐标来描述物理位置的参考点（因为该位置可能或可能与任何道路相关）。惯性位置输入定义如下。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050800140585.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.9.2 Signal Reference Record**







根据不同用途的道路（尤其是交叉路口）布置方式，可能需要参考来自多条道路的相同（即相同）信号。为了防止通过乘法定义整个信号条目的不一致性，用户只需定义一次完整的信号条目，并且可以通过信号参考记录引用该完整记录。

然而，这要求被引用的信号具有唯一的ID。信号引用记录由主记录和可选车道有效性记录组成。

注意：此记录只能用于信号！
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001427439.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.10.1 Curved Regular Grid Record**

曲线规则网格（crg）表面描述文件的接口定义为标记的参数，以及打开和关闭标记之间的包含操作。

注：如果在给定位置为相同物理属性（属性目的）提供了多个CRG条目，则OpenDrive文件中出现顺序中的最后一个条目应为相关条目。其他的都将被忽略。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001449237.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
顾名思义，CRG数据组织在一个规则的网格中，该网格沿着一条参考线（相当于OpenDrive道路的参考线）排列。在每个网格位置，它包含沿实际道路测量的绝对高程和一些附加数据，这些数据允许计算相对于参考线的三角洲高程。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001503450.png)
结合OpenDrive和CRG数据的关键是定义两条参考线之间的相关性，以及使用两种描述的高程数据的规则。

CRG数据可能会偏离OpenDrive道路的参考线（见Toffset），并且可能与道路的布局方向（见方向）相同或相反。

CRG数据可在两种模式下应用于给定的OpenDrive道路：

附加模式：考虑到Toffset和Soffset参数，将CRG数据集的参考线替换为OpenDrive路的参考线。CRG局部高程值（通过评估CRG网格并应用zoffset和zscale计算）将添加到OpenDrive道路的地面高程数据中（由高程、超高和横坡组合得出）。使用此模式，与原始CRG数据参考线相关的表面信息从任意CRG道路传输到OpenDrive道路，而无需确保道路的总体几何图形匹配。不考虑CRG道路的原始位置、航向、曲率、高程和超高。沿OpenDrive参考线而不是CRG参考线评估CRG网格。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001519399.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
模式附件0：该模式与附加模式基本相同，唯一的例外是只考虑CRG数据的高程值（即OpenDrive高程设置为零）。

真实模式：CRG数据集参考线的起点相对于OpenDrive道路参考线上由s start、soffset和toffset定义的位置进行定位。通过提供纵向（s offset）和横向（toffset）位移、航向（hoffset）和高程（zoffset）的偏移值，两个描述的参考线之间的相关性是明确的。在真实模式下，CRG数据将完全取代OpenDrive高程数据，即路面给定点的绝对高程直接根据CRG数据计算（将CRG和OpenDrive数据与OpenDrive高程、超高和横坡数据结合起来考虑）为零）。使用此方法时，当然必须确保CRG数据的几何图形与底层OpenDrive道路的几何图形匹配（在一定的公差范围内）。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001540626.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
全局模式：CRG数据集仅从给定的轨道或交叉点记录引用，但既不应用平动变换，也不应用旋转变换。CRG文件中的所有数据都保留在其本机坐标系统中。高程数据被解释为惯性数据，即原样。由于CRG数据可能只覆盖部分路面，因此必须确保在有效的CRG区域之外，仍然可以使用从OpenDrive数据中获得的高程信息。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001553901.png)
**5.3.11 Railroad Elements**

目前，低于路面的可用铁路记录集仅限于道岔的定义。目前，所有其他条目都应包含现有记录（例如，由定义的轨道、由定义的信号等）。需要再次指出的是，铁路特定元素是在有轨电车应用的背景下定义的。铁路记录是所有铁路定义的容器，应沿公路使用。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001613663.png)
**5.3.11.1 Railroad Switches**
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001634107.png)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001639152.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.3.11.1.1 Main Track**
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001700887.png)
**5.3.11.1.2 Side Track**
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001728390.png)
**5.3.11.1.3 Partner Switch**

此条目可用于指示输入后哪个开关将引出侧轨。此条目是可选的，可以用于更容易地通过铁路网络进行导航。在上面的示例中，交换机12和32将被视为合作伙伴。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001750268.png)
**5.4 Controller Record**

控制器为一组信号提供一致的状态。这可能是交叉路口内的一组信号或高速公路上的一组动态速度限制。整个记录由一个标题和一些依赖关系记录组成。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001808556.png)
5.4.1 Control Entry Record
控制条目记录提供由相应控制器控制的单个信号的信息。此记录是控制器记录的子记录。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001826834.png)
**5.5Junction Record**

当一条道路可以链接到多个后路（或前路，取决于方向）时，需要交叉路口记录。它包含有关在物理交叉口处的道路之间所有可能连接的信息。
对于交叉口，必须区分两种类型的道路：
l standard roads
l paths
standard roads进出路口道路。通常情况下，他们不需要任何特殊的对待，除非他们结束或开始在一个路口。
paths是交叉路口内的道路。对于从一条标准道路到另一条标准道路的每个连接，都定义了一条路径。一次只能连接一条车道或一组车道。道路的几何和逻辑描述符合为标准道路定义的规则（即，它们包含车道、高程入口等）。
交叉口提供了连接入口道路和道路的信息。这是连接中唯一不明确的部分。从道路的道路连接记录（见5.3.2）中可以清楚地看到道路与相应的输出道路之间的连接。
连接记录分为头记录和一系列链接记录。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001855282.png)
在OpenDrive1.5中，引入了“虚拟”连接的概念。这些交叉口不需要在实际交叉区之前分割（终止）主进出道路。它们允许定义道路内的连接，因此，提供了一种简单的定义方法，例如，即使是沿着主（干道）道路的大量私人道路连接（参见下面的示例…）。这一原理与铁路道岔使用的原理相当。

建议仅在车道上使用虚拟交叉口（例如通往停车场、住宅区等）。它们决不能取代连接多条常规道路的常规交叉口和交叉口。交通网络必须为所有道路提供常规的交叉口和交叉口，供在整个网络中导航的交通参与者用于常规路线。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001910863.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
注：99号公路的定义不是强制性的；只有转弯是足够的。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050800192655.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001932414.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001941282.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508001947119.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.5.1Connection Record**
连接记录提供有关连接内单个连接的信息。它是连接头记录的子项。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002016426.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
对于ConnectionMaster，以下情况适用：如果连接指向ConnectionMaster，则可以从此Master中检索有关路边对象、信号、轮廓等的进一步信息。这被认为是一种“舒适”功能，它减少了在交叉口内的多条连接道路上具有相同信息的要求。注意，如果给定了connectionmaster，那么connectionmaster上的所有信息对于引用它的所有连接都必须有效。对于当前版本，在直接引用中支持此功能的功能有：
l objects
l signals
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002033769.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050800203855.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
除了“虚拟”交叉口（即直通道路内的交叉口）之外，还可能存在“虚拟”连接，即仅表示可能从一条道路转向另一条道路而不实际定义路径的连接（参见下图中的红色虚线）。在这些情况下，前置任务和后续任务信息必须明确定义为连接记录的子节点（参见下面的示例）。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050800205183.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002057549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.5.1.1 Junction Predecessor Record**
此记录提供有关虚拟连接的前一条道路的详细信息。前一条道路只能是“道路”类型。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002118676.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.5.1.2 Junction Successor Record**
此记录提供有关虚拟连接的后续道路的详细信息。继承人只能是道路类型。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002141475.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.5.1.3 Junction Lane Link Record**

交叉口车道连接记录提供了有关在进口道路和连接道路之间连接的车道的信息。如果所有进入车道与连接道路上具有相同ID的车道相连，则可以省略此记录。但是，强烈建议提供此记录。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002159952.png)
**5.5.2 Junction Priority Record**

交叉口优先权记录提供连接道路优先于另一连接道路的信息。只有当优先权不能从路口或通往路口的轨道上的标志或信号中获得时，才需要优先权。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002216183.png)
**5.5.3 Junction Controller Record**
连接控制器记录列出用于管理连接的控制器。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002236363.png)
**5.5.4Surface**

路面记录可用于描述交叉口内的道路高程剖面。这样就可以描述接合处内的复杂（即不规则弯曲）表面。在交叉口记录中存在地面记录的情况下，连接道路的所有高程数据将被该记录的信息取代（即任何高程/超高等。忽略连接道路记录下面给出的数据）
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002258249.png)
5.5.4.1 Curved Regular Grid Record

曲线规则网格（crg）表面描述文件的接口定义为标记的参数，以及打开和关闭标记之间的包含操作。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002319592.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.6Junction Group Record**

在某些情况下（例如环形交叉口），将交叉口分组可能会更好，以便交通模拟软件能够更有效地执行计算。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002337251.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002343154.png)
**5.7Stations**
对于有轨电车和铁路应用，车站的定义必须是可行的。车站指的是多条轨道，因此与交叉点在同一水平上定义。站点的物理范围由站点内所有平台的组合物理范围定义。对于纯汽车环境，也可以使用相同的元素定义公共汽车站。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050800242329.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002428112.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.7.1 Platform Record**
每个车站入口必须至少包含一个站台入口。每个平台入口必须至少包含一个对有效轨道段的引用。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002449339.png)
**5.7.1.1 Segment Record**
每个站台入口在一个或多个轨道区段上有效。这些必须使用以下记录指定。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002511976.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.10.1 Layout Instance**
每个集合可以包含一个或多个封闭属性的可选设置。为了标识特定的设置，必须用实例标记将其括起来。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002534234.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.11 Data Quality Description**

从OpenDrive1.5开始，从外部来源或地理参考数据中获得的道路数据可以根据质量和来源进行分类。以下是有助于描述OpenDrive数据库的不同质量标准的条目列表。数据质量标签只是一个容器，用于进一步描述质量的各个方面。dataQuality标记可以用作任何其他标记的子标记，从而使其在应用程序中与标记一样通用。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002557460.png)
**5.11.1 Data Error Description**

包含xy和z方向的相对和绝对误差。数值以国际单位制米为单位。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/2020050800261852.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**5.11.2 Raw Data Description**

包含有关原始数据及其处理的信息。
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002633734.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**6 Constants**
**6.1 Road Type Information**
道路类型信息的已知关键字为：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002658449.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**6.2 Road Mark Type Information**
简化道路标记类型信息的已知关键字为：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002717473.png)
6.4 Road Mark Color Information
路标颜色信息的已知关键字为：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002817550.png)
**6.5 Lane Type Information**

车道类型信息的已知关键字为：
![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002853888.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)
**6.6 Object Types**

对象类型信息的已知关键字是：

![在这里插入图片描述](https://i2.wp.com/img-blog.csdnimg.cn/20200508002911846.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMjg3ODcx,size_16,color_FFFFFF,t_70)