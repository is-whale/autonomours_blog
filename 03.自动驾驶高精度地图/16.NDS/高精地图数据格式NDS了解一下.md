- [高精地图数据格式NDS了解一下 (qq.com)](https://mp.weixin.qq.com/s/KAcjDjtDTgpS2B-EJEhh7A)

# **NDS背景简介**

当今时代，说起汽车，人们自然会提到智能驾驶，而说起智能驾驶，人们则自然会提到高精地图。高精地图是离我们这么近又那么远的一个概念。说起高精地图，相信即使是行外人士，也不会完全不知道它是什么意思，毕竟我们手机上的地图APP通常都不止一个，大家用起来都十分利索。

实际上高精地图的发展与智能网联汽车密切相关。相对于以往的导航地图，高精地图是智能驾驶汽车规划道路行驶路径的重要基础，能为车辆提供定位、决策和交通动态信息等依据。另一方面，高精地图也能为智能驾驶汽车上的传感器补位，增强超视距的感知，提高系统安全性。当功能丰富、使用场景多样的高精地图需要落实到汽车上下游产业链，并适应日益提速迭代的汽车及其应用开发周期，就对制定通用的地图标准提出了需求。而今天我们要探讨的NDS就是其中最常用的导航地图数据标准。

NDS的全称是Navigation Data Standard，亦即导航数据标准，由NDS组织制定及维护。NDS格式是高精地图的常用格式，也是国际OEM的普遍选择。NDS可以用于移动应用程序、互联汽车云解决方案和自动驾驶。

![图片](https://mmbiz.qpic.cn/mmbiz_png/dgmES0HFW0vDyzB3Yr2BczUYH8mkslWtwjREQkNYGRmvu91IaRFnJmVzynCvEfncsM86sBQl1X2f953HSp6uicA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

*图1：NDS的口号 （来源：NDS组织官网）*

每每谈论到标准，我们都容易想起行业圈子，因为标准组织内有哪些玩家往往能体现标准分量。而从行业背景来看，NDS是为汽车行业服务的，由汽车行业负责。具体的协会成员直接看下图，囊括中外，我们耳熟能详的OEM、Tier1和图商都位列其中。

![图片](https://mmbiz.qpic.cn/mmbiz_png/dgmES0HFW0vDyzB3Yr2BczUYH8mkslWtbsnaFxl8FY2qlFFHxgs9ofLQOzwzHrWVicf4LXP4Kgvf4uOG5OXg9Rw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

*图2：NDS协会成员*

**NDS.Classic和NDS.Live**

这些年汽车的发展异常迅速，越来越多的车辆配备了联网功能，车云协作越来越主流，与车辆地图相关的APP应用也是层出不穷。这就对地图数据格式提出了新的要求。为了应对这种趋势，NDS自身也在蜕变和革命。在2019年NDS正式对外公布了其新一代的导航数据格式NDS.Live，并把之前的NDS命名为NDS.Classic以示区分。

![图片](https://mmbiz.qpic.cn/mmbiz_png/dgmES0HFW0vDyzB3Yr2BczUYH8mkslWtvWhmlPpnwicWpfhwJKIsj93sqoPbyeQxUmPqSKicO0Fg2ZKf2p9xz11w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

*图3：2019年NDS在公开会议上正式提出了下一代数据格式NDS.Live*

### **NDS.Classic**

目前量产车上用的NDS数据格式大部分都是NDS.Classic，它已经在30多个不同品牌的数百万辆汽车上部署，用于导航、ADAS和自动驾驶等，有着良好的记录。它本质上是为嵌入式设备使用而设计的。之前很长一段时间，导航地图数据都是存储在数据载体（DVD、USB、HDD、SD卡）上，而且对应的数据只会在车上用。相信有资历的车友都能想起以前手动升级一版导航地图是如何大费周章。基于此，NDS.Classic的存储格式实质上是数据库类型的。它作为一个嵌入式数据库，可以渐进式更新。当然随着技术发展，NDS.Classic后来也支持OTA。

举个例子，下图就是一个应用NDS导航系统的系统示意图。虚线左边是NDS规范的主体内容，通过编译器将原始的地图输入生成满足NDS规范的数据库（一般以SQL文件格式存储），然后数据库提供操作接口给不同的业务应用，例如地图显示、地位和路径规划等应用。

![图片](https://mmbiz.qpic.cn/mmbiz_png/dgmES0HFW0vDyzB3Yr2BczUYH8mkslWtXDcRyQxBkUdwjnVAibcnfq7sOFlSZ47uicYsrS8SNK1Mjj3ePNoGhNYQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

*图4：NDS格式数据库及其接口示意图*

NDS.Classic是一个基于瓦片（Tile-based）的组织结构，支持瓦片级的更新。瓦片可以理解为地球面上的一个四边形。我们在手机上看地图的时候，数据经常是一个方块一个方块地加载出来，背后也是因为数据是基于瓦片来存储和传输的。四维图新在《智能网联汽车高精地图白皮书》中提到，其向某OEM 最终交付 2.5.4 版本的 NDS 规格的中国全境高速和部分城市道路的高精地图数据，平均一个瓦片的数据大小大约在 60-80kb。

### **NDS.Live**

相比之下，NDS.Live就不再是一个数据库，而是一个分布式地图数据系统。这其中包括数据服务以及应用服务，比如路径生成、电动车里程计算和POI搜索等功能。这些服务的应用接口在NDS.Live中定义，但规范中并不包括底层通讯传输。NDS.Live的用户能够自由选择NDS数据或应用服务的部署位置，这可以是在云端、车载娱乐控制器或者手机APP等其他任何地方。NDS.Live可以说是NDS的新一代规范，更具生命力。

NDS.Live支持多种数据传输协议，包括常用的从云端到车端通讯的HTTP/REST和车载通讯SOME/IP。但无论选择哪种传输协议，接口层的互操作性都由NDS.Live来维护，因为数据在被传递到传输层之前是以互操作、跨平台、跨语言的方式进行序列化的。如下图示例，底层采用HTTP传输，然后在标准的HTTP协议之上设计应用接口作为适配参考层，同时根据NDS.Live规范可以设计统一的服务层，供不同的上层应用模块调用。如果例子中的HTTP协议换成SOME/IP协议，则只需设计对应的参考层，可以最大限度地复用上层应用和底层标准传输协议。而下图示例中的参考层虽然不属于NDS.Live的规范内容，但是NDS组织也提供了针对HTTP和SOME/IP的参考层实现样例。

![图片](https://mmbiz.qpic.cn/mmbiz_png/dgmES0HFW0vDyzB3Yr2BczUYH8mkslWtrv9mhtZMrlR5iaiaYSasPRCHNae6tT3Cx9wuhAddJ8VsgL2u66BxAhhw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

*图5：NDS.Live与不同传输层接口层次示意图*

**NDS.Live架构介绍**

在传输协议和API适配之上，则是NDS.Live规范的主体部分。如图5所示，我们可以分两个层次来进一步理解：模块（Module）和服务层（Service Layer）。

### **模块（Module）**

NDS.Live是一个基于模块的分布式地图系统，因此规范和文档也是按模块安排的。模块代表相关数据的集合，例如数据类型、定义和接口。下图是NDS.Live所定义的模块概览。

![图片](https://mmbiz.qpic.cn/mmbiz_png/dgmES0HFW0vDyzB3Yr2BczUYH8mkslWtvC4agFYuC8TwaulSVHkW5oYrtXLRBFAfiaqP4jsHPE4y7CXltNw8icYw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

*图6：NDS.Live模块概览*

**模块分为5种类型：**

- Common：通用模块，用于可以全局复用的通用数据，例如基础的数据类型等。

- Feature: 特征模块，用于定义地图特征和几何数据。

- Attribute: 属性模块，用于定义属性，可以简单理解为是对地图特征的更细致描述。例如ADAS、定位等都是在属性模块中定义。

- Reference: 参考模块，实际上是特征模块和属性模块之间的接口。通过参考模块引用数据，可以降低模块之间的耦合度。

- Service：服务模块，提供数据访问的接口。服务的终端访问定义都在该模块定义，例如常见的“请求-应答”和“发布-订阅”服务接口都在这类模块中定义。

这些不同的模块之间不同的排列组合，可以满足不同的用例。例如在ADAS应用场景下，基于车道的特征模块、ADAS属性模块、数据类型定义的通用模块和定义具体服务接口的服务模块之间就可以满足。而在不同的应用场景和不同的模块下，NDS.Live还抽象出了不同的数据层（Data Layer），用来定义具体的数据结构。这背后同样是“高内聚、低耦合”的思想。如下图所示，不同用例可以取5种数据层中的若干种。例如普通ISA（Intelligent Speed Assist）场景下，业务只需要道路基础信息和限速信息，可以只获取底下两层。更复杂的功能业务，则可以用更丰富的层次。当然这只是在功能逻辑层面的区分，实际上在数据二进制存储和通讯等实现过程中，不同层次的数据层会打包进一个数据容器（Data Container）中。

![图片](https://mmbiz.qpic.cn/mmbiz_png/dgmES0HFW0vDyzB3Yr2BczUYH8mkslWtF3dSEu0OcvEQeGu2zj4zMdfb4RNoic3LRCD0GDgqDDEsXTGsqicjYKhQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

*图7：不同用例下数据层的复用示意图*

另外，在数据压缩和安全方面，NDS.Live也提供了针对数据层的解决方案，有利于满足汽车行业的功能安全和信息安全等要求。NDS.Live中明确了支持的压缩算法。而关于数据签名，NDS.Live也提供了一个预定义的哈希和签名机制列表。签名算法可以用专有的签名算法接口进行扩展。用于签名和加密的密钥管理不在NDS.Live的范围内，但它提供了URL或密钥ID接口。结合AUTOSAR中的密钥管理模块，可以快速高效地部署数据安全系统。

### **服务层（Service Layer）**

服务层可以理解为NDS.Live中定义的通信中间件。NDS.Live规范是针对多个网络参与者的分布式系统开发的。每个参与者由一个网络节点代表，由该节点来提供或请求与地图和导航有关的信息。

NDS.Live系统内的每个通信事件都涉及两种类型的网络节点：服务端节点和客户端节点。服务层所定义的服务接口，就是用于服务端节点和客户端节点之间的信息传递。服务端和客户端之间可以通过“请求-应答”和“发布-订阅”模式进行通信。而NDS.Live中定义的服务接口分两种类型：通用服务接口和基于模块的服务接口。

两种服务接口类型的主要差别就是是否对不同的模块通用。通用服务接口如下图左侧所示，如果客户端发起一个请求，来获取不同模块所提供的不同数据内容。这时候服务接口可以把不同数据源拼接在一起，统一通过同一个服务响应来传递数据。这种汇纳多个数据源后由统一的服务响应传递数据的方式，在NDS.Live中也称为智慧层（Smart Layer）。

智慧层的设计也是基于终端使用场景出发的，例如客户端传来基于瓦片ID的请求，服务端可以把对应瓦片内不同模块不同层次的数据都放在同一个数据容器内后响应。除了支持NDS.Classic的基于瓦片的方法，NDS.Live还增加了路径和对象作为容器的额外选项。例如ADAS客户端发来基于路径几何特性的请求，服务端可以把该路径对应的数据统一响应。这样可以为基于路径的自动驾驶和ADAS用例减少数据带宽。又例如针对泊车场景，需要单独下载一个停车场的高清地图时，可以通过对象作为容器和服务接口来传输。

![图片](https://mmbiz.qpic.cn/mmbiz_png/dgmES0HFW0vDyzB3Yr2BczUYH8mkslWtj4DcaTqIpKKAEbm0TCIx7C7EwZaSWgYStgv2CsRew4P7ibS6SmLl6xg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

*图8：NDS.Live两种服务接口类型对比*

**NDS应用例子**

NDS作为导航数据标准，致力于标准化流程和接口，以支撑更多产业创新。那么它在汽车行业中是怎么应用的？接下来我们看一个NDS官网上展示的实际例子。这个例子是BMW和dSPACE两大巨头联手打造的HIL（Hardware In the Loop，硬件在环测试）方案，架构如下所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/dgmES0HFW0vDyzB3Yr2BczUYH8mkslWtkia41nicTkxTh9rHz2512D7icG6jRprM2HYwthn87JtJRk3FBmGrcGRgw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

*图9：NDS 2022大会上展示的一种应用NDS的HIL架构*

从连接的其他模拟模型可以猜测，被测ECU是ADAS的域控制器。对于毫米波雷达、激光雷达等传感器，通过仿真环境配合各个传感器的内核算法模型，可以仿真感知数据，然后提供给被测ECU。而跟NDS息息相关的主要是图9中上部分的链路，也就是NDS和OpenDrive的双向格式转换的打通。

OpenDrive是dSPACE仿真环境对于地图数据的统一接口，可以被输入到仿真环境中，以开发和验证ADAS功能。但原生OpenDrive生成的场景往往缺乏实际驾驶区域旁边的环境细节。而这些细节又往往影响到所需验证的功能。因此在打通NDS和OpenDrive双向转换之前，对于某些测试用例，可能需要手动修改三维场景。但有了上图所示的双向转换之后，当需要把采集的真实数据回灌到被测ECU时，基于NDS的丰富的导航地图数据可以经过车机后自动转换为OpenDrive格式，提供给仿真环境，然后生成相对更真实的3D环境进行测试。而当采用dSPACE搭建虚拟环境时，生成的OpenDrive地图数据也可以转换为NDS，供车机渲染酷炫的动画。

来自两大巨头将接口标准的打通，相信也会加速NDS的应用推广。方案中呈现的NDS和OpenDrive双向转换，相信也会商业化成为产品。这样一来，在推广应用NDS作为高精地图数据标准的同时，原有的硬件在环测试设备和方案也可以沿用，是一个共赢的局面。

**写在最后**

随着智能驾驶技术的日益成熟，高精地图已经成为了各大厂家的新战场。高盛对全球高精地图市场的预判是，到2025年市场规模会扩大到94亿美元。行业普遍认为，未来15年高精地图行业将进入黄金发展期。而在这个过程中，最好的选择肯定是制定标准。如果不能，那选择一个有前景的标准进行跟随和应用也是提升自身核心竞争力的有效手段。那么你觉得NDS是这个有前景的标准吗？

**参考来源：**

1.https://nds-association.org/#nds

2.https://developer.nds.live/