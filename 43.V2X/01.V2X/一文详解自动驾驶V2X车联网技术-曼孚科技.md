- [一文详解自动驾驶V2X车联网技术丨曼孚科技](https://www.cnblogs.com/manfukeji/p/14001415.html)

自动驾驶系统是一个高度复杂的大系统产业集成，车辆在多个子系统技术的加持下，实现不同程度的自动驾驶。

中国汽车工业协会对自动驾驶汽车的定义为：

自动驾驶汽车是搭载先进车载传感器、控制器、执行器等装置，融合现代通信与网络技术，实现车与X(人、车、路、后台等)智能信息的交换共享，并具备复杂环境感知、智能决策、协同控制和执行等功能，并最终可替代人来操作的新一代汽车。

在自动驾驶整套系统中，以V2X技术为基础的汽车网联化和道路智能化是实现自动驾驶的重要支撑。

借助车-车，车与路测基础设施、车与路人之间的无线通信，可以实现实时感知车辆周边状况并进行及时预警，是自动驾驶系统中的重要组成部分。

本文将详解自动驾驶V2X技术的各层面内容。

## 一、V2X概述

### 1.V2X技术的发展历史

V2X的英文全称为Vehicle to Everything，即车用无线通信技术。

其中V代表车辆，X代表任何与车交互信息的对象，X主要包含车、人、交通路侧基础设施和网络。简单来说，V2X是将车辆与一切事物相连接的新一代信息通信技术的统称。

V2X技术最早于2006年应用于凯迪拉克一辆展示的汽车上。从此以后，其他汽车制造商和汽车配套产品供应商纷纷开始研究此项技术。但此阶段仅限于企业内部研究。

V2X真正被提上日程，得到官方重视是起源于美国的两起交通事故。

21世纪初，美国新泽西州和弗罗里达州先后发生两起特大交通事故，导致被撞校车内的全部学生死亡。

事故发生之后，美国国家运输安全委员会对事故进行了调查并提交了一份报告给美国公路交通安全管理局。

该份报告中详细描述了事故发生经过，并认为如果事故发生时车辆上有能与其他汽车进行通信的系统，那么这两起事故就能够被避免，这直接催生了车联网相关标准的制定，V2X技术的相关研究进入快车道。

2010年，美国颁布了以IEEE 802.11P作为底层通信协议和IEEE 1609系列规范作为高层通信协议的V2X网联通信标准。

在国外加大对V2X研究力度的同时，国内相关机构也在同步跟进中。

2015年我国开始相关的研究工作，2016年国家无线电委员会确定了我国的V2X专用频谱。

2016年6月，V2X技术测试成为第一家“国家智能网联汽车试点示范区”及封闭测试区的重点布置场景之一。

2017年9月，《合作式智能交通系统车用通信系统应用层及应用数据交互标准》正式发布。

### 2.V2X的主要作用

与自动驾驶技术中常用的摄像头或激光雷达相比，V2X技术具备突破视觉死角和跨越遮挡物获取信息的能力，同时也可以和其他车辆及设施共享实时驾驶状态信息，还可以通过研判算法产生预测信息。

另外，V2X是唯一不受天气状况影响的车用传感技术，无论雨、雾或强光照射都不会影响其正常工作。因此V2X技术广泛应用于交通运输尤其是自动驾驶领域。

## 二、V2X四类关键技术

V2X车联网集成了V2N、V2V、V2I和V2P共四类关键技术。

![img](https://p6-tt-ipv6.byteimg.com/origin/pgc-image/72d526e2773a4cbd9f67cd08aa648794)

### 1.V2N(Vehicle to Network，车-互联网)

V2N是指车载设备通过接入网/核心网与云平台连接，云平台与车辆之间进行数据交互，并对获取的数据进行存储和处理，提供车辆所需要的各类应用服务。

V2N通信主要应用于车辆导航、车辆远程监控、紧急救援、信息娱乐服务等。

### 2.V2V(Vehicle to Vehicle，车-车)

V2V是指通过车载终端进行车辆间的通信。车载终端可以实时获取周围车辆的车速、位置、行车情况等信息，车辆间也可以构成一个互动的平台，实时交换文字、图片和视频等信息。

V2V通信主要应用于避免或减少交通事故、车辆监督管理等。

### 3.V2I(Vehicle to Infrastructure，车-基础设施)

V2I是指车载设备与路侧基础设施(如红绿灯、交通摄像头、路侧单元等)进行通信，路侧基础设施也可以获取附近区域车辆的信息并发布各种实时信息。

V2I通信主要应用于实时信息服务、车辆监控管理、不停车收费等。

V2P(Vehicle to Pedestrian，车-行人)

V2P是指弱势交通群体(包括行人、骑行者等)使用用户设备(如手机、笔记本电脑等)与车载设备进行通信。

V2P通信主要应用于避免或减少交通事故、信息服务等。

通过将“人、车、路、云”等要素有机联系在一起，V2X技术不仅可以支撑车辆获得比单车感知更多的信息，促进自动驾驶技术创新与应用，同时还有利于构建一个更加智慧的交通体系，促进汽车和交通服务的新模式新业态发展。

这对提高交通效率、节省资源、减少污染、降低事故发生率、改善交通管理具有重要意义。

## 三、V2X两种无线通信技术

从全球来看，V2X车联网主要包括两种无线通信技术：美国主推的以IEEE802.11为基础的802.11p通信技术和我国主推的以移动蜂窝通信技术为基础的C-V2X通信技术。

### 1. 802.11p通信技术

IEEE 802.11p(又称WAVE，Wireless Access in the Vehicular Environment)是一个由IEEE 802.11标准扩充的通讯协定。这个通讯协定主要用在车用电子的无线通讯上。

它设定上是从IEEE 802.11来扩充延伸，来符合智能运输系统(Intelligent Transportation Systems，ITS)的相关应用。

IEEE 802.11p 对传统的无线短距离网络技术加以扩展，可以实现对汽车非常有用的功能，包括：更先进的切换机制(handoff  scheme)、移动操作、增强安全、识别(identification)、对等网络(peer-to-peer)认证，以及最重要的在车载规定频率上进行通信。

### 2. C-V2X通信技术

C-V2X(Cellular-V2X)是基于3GPP全球统一标准的通信技术，包含LTE-V2X、5G-V2X及后续演进。

C-V2X技术基于蜂窝网络，提供Uu接口(蜂窝通信接口)和PC5接口(直连通信接口)，可复用蜂窝网的基础设施，部署成本更低、网络覆盖更广，在更密集的环境中，C-V2X 支持更远的通信距离、更佳的非视距通信性能、增强的可靠性(更低的误包率)、更高的容量和更佳的拥塞控制。

此外，C-V2X拥有清晰的、具有前向兼容性的5G演进路线，利用5G技术的低延时、高可靠性、高速率、大容量等特点，不仅可以帮助车辆之间进行位置、速度、驾驶方向和驾驶意图的交流，而且可以用在道路环境感知、远程驾驶、编队驾驶等方面。

C-V2X技术旨在将“人-车-路-云”等交通参与要素有机地联系在一起，不仅可以为交通安全和效率类应用提供通信基础，还可以将车辆与其他车辆、行人、路侧设施等交通元素有机结合，弥补了单车智能的不足，推动了协同式应用服务的发展。

## 四、车辆网联化等级分级

基于C-V2X为车辆提供交互信息、参与协同控制程度的不同，车辆网联化大致可以划分为三个层级：网联辅助信息交互，网联协同感知以及网联协同决策与控制。

![img](https://p6-tt-ipv6.byteimg.com/origin/pgc-image/f705285597764638ba61a1804570ec2b)

### 1.网联辅助信息交互

基于V2I、V2N通信，实现导航、道路状态、交通信号灯等辅助信息的获取以及车辆行驶与驾驶人操作等数据的上传。

### 2.网联协同感知

基于V2V、V2I、V2P、VIN通信，实时获取车辆周边交通环境信息，与车载传感器的感知信息融合，作为自主决策与控制系统的输入。

### 3.网联协同决策与控制

基于V2V、V2I、V2P、VIN通信，实时并可靠获取车辆周边交通环境信息及车辆决策信息，车-车、车-路等交通参与者之间信息进行交互融合，形成车-车、车-路等交通参与者之间的协同决策与控制。

## 五、V2X发展现状

### 1.国外发展情况

作为802.11p通信技术的主要发祥地，美国在2015年推出了主题为“改变社会前进方式”的ITS五年规划。

这份规划的主要技术目标是“实现网联汽车应用”和“加快自动驾驶”。规划内容中定义了六个项目大类，包括：加速部署、网联汽车、自动驾驶、新兴能力、互操作和企业数据。

其中，顶端的加速部署代表了所有项目的最终目标，网联汽车、自动驾驶和新兴能力是技术发展的三条路径，而互操作和企业数据则是ITS发展的基石。

为了推动车车通信技术的进一步发展，并反推美国后续的立法决策，美国交通部在密歇根州安娜堡东北部主导了基于车车、车路通信技术的“安全试点示范部署”项目。

在“安全试点示范部署”项目测试验证的基础上，2014年美国国家公路交通安全管理局公布了车车通信预立法草案，并于2016年启动了NPRM过程，即：Federal Motor Vehicle Safety Standard(FMVSS)， No. 150，用来强制轻型汽车V2V通信，主要内容包括：

1)提出强制基于IEEE 802.11p的V2V通信;

2)指定BSM消息内容;

3)指定V2V通信性能要求;

4)指定隐私与安全要求;

5)指定设备授权系统等。

总体而言，美国是以企业为主体、政府搭平台，通过市场力量发展车联网，政府则从立法、政策、标准的方面着力营造良好发展环境。

### 2.国内发展情况

随着宽带网络、高速公路网的快速发展，以及北斗卫星导航系统的全区域覆盖，国内C-V2X技术的应用有了坚实的软硬件条件基础。

目前，国内初步形成了覆盖C-V2X标准协议栈各层次、各层面的标准体系：

在C-V2X技术商业化应用方面，2019年发布的《C-V2X 产业化路径及时间表研究》白皮书对国内车联网的发展趋势做了详细的预测：

![img](https://p6-tt-ipv6.byteimg.com/origin/pgc-image/8a0a99833a5e40ef80c0b10d7d423398)

其中，2019-2021年为 C-V2X 产业化部署导入期。在这一阶段，C-V2X通信设备、安全保障、数据平台、测试认证方面可基本满足C-V2X产业化初期部署需求。

同时，在国家车联网示范区、先导区及部分特定园区部署路侧设施，形成示范应用，车企逐步在新车前装 C-V2X设备，鼓励后装 C-V2X 设备，车、路部署相辅相成，形成良性循环，C-V2X生态环境逐步建立，探索商业化运营模式。

2022-2025年为 C-V2X产业化部署发展期。根据前期示范区、先导区建设经验，形成可推广的商业化运营模式，在全国典型城市和道路进行推广部署，并开展应用。

2025年以后为C-V2X 产业高速发展期。逐步实现C-V2X 全国覆盖，建成全国范围内的多级数据平台，跨行业数据实现互联互通，提供多元化出行服务。

## 参考资料：

1.《C-V2X 产业化路径及时间表研究》

2.IMT-2020(5G)推进组 - C-V2X白皮书