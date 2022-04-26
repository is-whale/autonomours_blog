- [一文读懂车联网V2X通信技术 (qq.com)](https://mp.weixin.qq.com/s/XT6JXiVKZ3ifgdCr4wg-ww)

## 1 车联网的组成

根据车联网产业技术创新联盟的定义，车联网是以车内网、车际网和车云网组成，进行无线电通信和信息交互的大系统网络。如图1所示，通过三网融合，实现V2X之间通信的无缝连接，提高通信效率，减少通信盲区。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny69QVCPfrnTib1AhTicRG8WckmKJyQfQrsRO0FpwhUYRiaXGzUYOZLFCKkQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图1 车内网、车际网、车云网三网融合

### 1.1 车内网络

车内网络是基于CAN、LIN、FlexRay、MOST、以太网等总线技术建立的标准化整车网络，实现车内各电器、电子单元间的状态信息和控制信号在车内网上的传输，使车辆具有状态感知、故障诊断和智能控制等功能。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6C3aXl7H0mVBBXo95Kwo4UOSUYia42KC5aVZYErCQoNXRzDGagPvM9Vg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图2 车载网络总线结构

如图2所示车载网络以高速以太网作为骨干，将动力总成、底盘控制、车身控制、娱乐、ADAS(先进驾驶辅助系统)共5个核心域连接在一起，各个域控制器在实现专用的控制功能的同时，还提供强大的网关功能。

图3所示奔驰222型号轿车网络总线拓扑图。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6fNXbleeq0Y5sNOawoRflYEVKOy1Mdeic4VAkFqO5VzHaib7nU2Fl4djA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图3 奔驰222型号轿车车载网络总线拓扑图

CAN A。车载智能信息系统 (CAN)；CAN C. 发动机控制器区域网络 (CAN)；CAN C1. 传动系统控制器区域网络 (CAN)；CAN D. 诊断控制器区域网络(CAN)；CAN HMI. 用户界面控制器区域网络 (CAN)；CAN I. 传动系传感器控制器区域网络 (CAN)；CAN L. 混合动力控制器区域网络 (CAN)；CAN PER. 外围设备控制器区域网络 (CAN)；Ethernet. 以太网；Flex E. 底盘 FlexRay；LIN E2. 座椅承载识别区域互联网 (LIN)；LIN G1. 左侧大灯局域互联网 (LIN)；G2. 右侧大灯局域互联网 (LIN)；MOST. 多媒体传输系统。

### 1.2 车际网络

车际网络 ( 也称车载自组织网络VehicularAdhoc Networks VANET)是指在交通环境中，以车辆、路侧单元以及行人为节点而构成的开放式移动自组织网络。它通过结合全球定位系统及无线通信技术，如无线局域网、蜂窝网络等，建立无线多跳连接，为处于高速移动状态的车辆提供高速率的数据接入服务，以实现V2X之间的信息交互，如图4所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6jVNS6SmaNXBvSVAVwf9naibLQmXrTcEUpbe209vtaiabg8MIVibibMjPUw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图4 车载自组织网络结构

车载自组织网络是智能交通系统未来发展的通信基础，也是智能网联汽车安全行驶的保障。

#### 1.2.1 专用短程通信(DSRC)

DSRC是基于美国电气电子工程师协会(IEEE)，在IEEE802.11 的Wi-Fi技术基础上改进制定的IEEE802.11p标准和IEEE1609标准的V2V和V2I通信协议，是比较成熟、高效的无线通信系统技术，它是智能交通系统的重要基础之一，目前已被欧洲、日本等国汽车制造企业采用并完善。我国在高速公不停车收费设备(ETC)也采用该项技术。

DSRC通信在5.9GHz附近的频段上，专门将车与车、车与道路基础设施有机连接，实现在数百米的范围内对高速行驶的车辆进行识别和双向通信，提供实时图像、语音和数据信息传输，保证通信链路的低时延和低干扰以及系统的可靠性。例如DSRC在有效通信距离范围内，本车辆通过DSRC以10Hz的频率，向路上其他车辆发送位置、车速、方向等信息；同时本车辆还能收到其他车辆所发出的信号，在必要时(例如马路转角有车辆驶出，或前方车辆突然紧急刹车，变换车道的情况发生)车内信号装置会以闪烁、语音提醒或座椅、方向盘振动等方式提醒驾驶员注意，采取必要安全措施，如图5所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6qHrBiaCHcrMHISpzOJ3XMibnTWGIJozDibZ9314UybD0icpxDa7l4rS6DA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图5 专用短程通信(DSRC)在V2X通信的应用

DSRC系统结构主要由三部分组成，如图6所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6Liae55AS5C2Mo88zQLQ56Uf7A8owgIXM60oOFqzoLPb5HQ1IibP7a2mQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图6 专用短程通信(DSRC)系统结构组成

分别是车载单元(on Board unit，OBU)、路侧单元(road-side unit，RSU)、专用通信链路。OBU安装在车辆上的嵌入式车载通信单元内，它通过专用的通信链路依照通信协议的规定与RSU进行信息交互。RSU是安装在指定地点(如车道旁边、车道上方等)固定的通信设备，与不同OBU进行实时高效的通信，并通过有线光纤的方式接入移动互联网设备，与云端智能交通(ITS)平台进行数据交互。

专用通信链路是OBU和RSU保持信息交互的通道，它由两部分组成：下行链路和上行链路。RSU到OBU的通信应用为下行链路，主要实现RSU向OBU写入信息的功能。

上行链路是从OBU到RSU的通信，主要实现RSU读取OBU的信息，完成车辆状态的自主识别功能。因此在DSRC的架构中需要部署大量的RSU才能较好地满足业务需要，建设投资较大。

#### 1.2.2 C-V2X 通信

C-V2X通信是基于3G/4G/5G等蜂窝网通信技术演进形成的车用无线通信技术，包含基于4G网络的LTE-V2X系统以及未来5G资源的5G-V2X系统，借助已存在的LTE网络设施来实现V2V、V2I、V2P、V2N的信息交互，适应于更复杂的安全应用场景，满足低时延高可靠性和带宽要求。

①LTE-V2X技术

LTE—V (Long Term Evolution—Vehicle，长期演进，V2X)是我国具有自主知识产权的V2X技术，是基于TD．LTE(Time Division—Long Term Evolution，分时长期演进)的ITS(Intelligent Transport System，智能交通系统)系统解决方案，属于LTE后续演进生态系统的重要应用分支。

②LTE-V2X协议架构与组成

LTE-V2X标准协议架构由三部分组成，包括物理层、数据链路层、应用层。物理层是LTE-V2X系统的底层协议，主要提供帧传输控制服务和信道的激活、失效服务，定时收发及同步功能。

数据链路层负责信息的可靠传输，提供差错和流量控制，对上层提供无差错的链路链接。应用层基于数据链路层提供的服务，实现通信初始化和释放程序、广播服务、远程应用等相关操作。

LTE-V2X系统设备组成包含了UE(User Equipment，用户终端)、RSU(Road Side Uni，路侧单元)、和基站三部分，具体组成如图7所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6VrjzpEOXtj0yH7pf41ia52iaVMxqtrziaONR0eRGvV52fBCV2JJ2F0p3w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图7 LTE—V2X通信系统的组成

UE包含了车载设备、个人用户便携设备等。RSU处于基站和UE之间，承担着V2I的数据通信任务。基站是承担了LTE-V2X系统的无线接入控制功能的设备，主要完成无线接入功能，包括管理空中接口、用户资源分配、接入控制、移动性控制等无线资源管理功能。GPS信号则通过卫星地面站与基站进行通信。

③LTE-V2X主要技术指标分析

V2X技术影响用户体验的主要系统指标有延时时间、可靠性、数据速率、通信覆盖范围移动性、用户密度、安全性等。其相关指标有安全类时延≤20ms，非安全类时延≤100ms，峰值速率上行500Mbps、下行1Gbps，支持车速280km/h，在后续演进5G版本中提升至500km/h，可靠性几乎为100%，覆盖范围与LTE范围相当。

④LTE-V2X通信方式

LTE-V2X系统的通信方式采用了“广域集中式蜂窝通信”(LTE-V-Cell蜂窝)和“短程分布式直通通信”(LTE-VDirect直通)两种技术方案。分别对应LTE-Uu(UTRAN-UE，接入网-用户终端)和PC5(ProSeDirectCommunication，ProSe直接通信)接口。

广域集中式蜂窝通信(Uu接口)技术是基于现有蜂窝技术的扩展，主要承载传统的车联网远程业务，满足终端与V2X应用服务器间大数据量传输要求，如图8所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6Vr1Hhje2Rb3spibFAhRNULrqClJRWEzu6wWcNX6GWLbW5LlAI8ic612Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图8 基于Uu接口的V2V和V2I通信

短程分布式直通通信(PC5接口)技术引入LTED2D(Device-to-Device，端-端)，绕过RSU进行V2V、V2I直接通信，主要承载了车辆主动安全业务，图9所示。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6IOCFDBaMu1X9vDPmiaPvg7tYujro4o7LcbJicrr9VA3KpvxFqJ5iacZXQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

图9 基于PC5接口的终端直通的V2V通信

因此LTE-V-Direct具有低时延、通信容量大和无需网络设备(基站或路边设施)即可工作的优点。上述通信方式的多样性，不仅减少了网络节点，降低了系统的复杂程度，而且还提高了系统通信的低时延性和高可靠性，也降低了网络部署和维护成本。

⑤LTE-V2X安全认证技术

涉及交通时，其安全重要性不言而喻。由于车辆是一个高速移动的物体，LTE-V2X系统需要提供安全机制来保障使用者的信息安全，预防非法及伪装终端设备进入网络。对车辆间的高速认证和安全数据传输也提出了极高的要求，包括身份认证管理、异常用户检测、个人隐私保护、安全机制的更新、信息加密等。目前在传统联网系统中经常采用集中式管理机制，具有的安全性较高，但对于庞大的车辆管理数量来说，同时也会造成时延的问题；而分布式管理机制相对较灵活，作为集中式的补充对LTEV2X系统来说是个可行的解决方法。

⑥LTE-V2X工作场景

如图10所示为LTE-V2X技术的典型工作场景。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6n7PSsbS1dgcc0pcZSz7643h6DutRvZthIBRZSEWiczTEP4uiabanLic1A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图10 LTE—V2X典型工作场景

图10(a)中，车辆通过基站或路侧单元获得与远端ITS(智能交通系统)服务器的IP地址接入；图(b)中，车辆通过不同的基站或路侧单元，进而通过云平台，获得分发的远距离车辆的信息；图(c)中，车辆间直接交互与道路安全相关的低时延安全业务信息；图(d)为非视距(notlineofsight，NLOS)场景，车辆在十字路口由于建筑物的遮挡不能直接交互低时延安全业务，此时可以通过基站或路侧设备的转发，获得车辆间的道路安全信息。在上述场景中，图(c)可采用LTE-V-direct模式进行通信，其他场景可采用LTE-V-cell模式进行通信。

图11所示是LTE-V2X技术在智能网联汽车上的应用框图。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6U5TiaApNwLZ4Biby3smiceXKclejYyBRzBqZwFibSb31ic9BR7yicyCIDc0g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图11 LTE—V2X通信技术在智能网联汽车上的应用框图

⑦5G-V2X技术

与LTE-V2X相比，5-V2X将支持更加多样化的场景，融合多种无线接入方式，并充分利用低频和高频等频谱资源。同时，5G还将满足网络灵活部署和高效运营维护的需求，大幅提升频谱效率、能源效率和成本效率，实现车载移动通信网络的可持续发展。基于5G新空口的V2X可以提供高吞吐量、宽带载波支持、超低时延和高可靠性，从而支持众多面向自动驾驶的技术需求。5-V2X业务场景具体包括：

车辆编队：车辆编队使车辆形成动态编队一起行驶。编队中的所有车辆从编队头车获取信息来管理这个编队，这些管理信息能够以比正常行驶更接近(编队车辆之间间隔仅2～5m)、更协调的方式同向行驶。

传感器扩展：扩展传感器使车辆之间、车与路侧单元之间、车与行人之间以及车与V2X服务器之间可以交互本地传感器信息和实时视频图像信息等，车辆可以获得额外的环境感知能力，更全面了解周边环境。

先进驾驶：先进驾驶用于支持半自动或全自动驾驶。每个车辆把通过自身传感器获得的感知数据以及自身的驾驶意图分享给周围车辆，从而支持多个车辆之间同步和协调其行驶轨迹。

驾驶：远程驾驶使远程司机或车联网应用服务器遥控车辆的行驶，适应于车主不能自己驾车或远程车辆处于危险环境中等特殊场景。高可靠性和低延迟通信是远程驾驶的主要要求。5GV2X业务场景对通信的需求，具体如表1所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6aXqq38a57KRSCBD9NDZKvORGs3jcoTwbccowt0IYTibEKVwlpdzv33A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

⑧C-V2X与DSRC(IEEE802.11p)的比较

作为车联网的V2X无线通信技术，虽然DSRC(IEEE802.11p)有先发优势，但是C-V2X以蜂窝技术作为基础，通过增强的接入层，应对当前和未来智能交通系统的应用。尤其C-V2X的多种通信模式，可以利用现有的蜂窝网络基础设施提供大容量的数据传输和低时延的广域通信，这样就为交通道路安全借助强大的云端处理能力和边缘计算的保驾护航途径。例如C-V2X在性能改进上的体现，通过仿真比较了汽车分别采用LTE-V2X和DSRC时的最大容许刹车反应距离/时间(即汽车感知到前方危险后司机拥有的反应距离/时间)。如图12所示结果显示，相比于DSRC，LTE-V2X技术能够让司机在更远距离的位置感知危险并开始刹车，也就是司机拥有更长的刹车反应时间。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6lY22tLAuquzRQuM02PARvVC6CQJ37iahibSSxicpUITiaH21h2zKf9IDvg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图12 C-V2X和DSRC(IEEE802.11p)传输距离比较

图中例子是汽车以140km/h速度行驶的时候，采用LTE-V2X的汽车比采用DSRC技术的汽车拥有额外的5.9s(9.2s～3.3s)来决定是否刹车。

此外，C-V2X的直接通信技术在ITS频谱(5.9GHz)下操作，以确保直接安全通信的匿名性和蜂网络覆盖区域外的直通需求。从产业化进程而言，C-V2X正在逐步缩小与DSRC(IEEE802.11p)之间的差距。尤其在网络建设和维护方面，尽管DSRC可利用现有的Wi-Fi基础进行产业布局，由于Wi-Fi接入点未达到蜂窝网络的广覆盖和高业务质量，不仅DSRC的新建路侧单元需要大量投资进行部署，而且DSRCV2X通信安全相关设备、安全机制维护需要新投入资金。而C-V2X可以利用现有LTE商用网络中的基站等安全设备进行升级扩展，支持安全证书的更新以及路侧单元的日常维护。

另外，目前国内在DSRC系列技术和产业方面缺乏核心知识产权和产业基础。而基于我国自主研发的4GTD-LTE移动通信技术标准，C-V2X技术拥有核心自主知识产权，可以打破国外产业在V2X通信技术垄断，减少在知识产权方面的限制。整个C-V2X预期发展的关键时间节点如图13所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6VjteEDdn1u7pkzdym6goHiahviaE6rYbeBUdIhKpH4DGs9jnGfMsK7OA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图13 LTE—V2X发展预期

### 1.3 车云网

车云网也称车载移动互联网。它是以车为移动终端，通过远距离无线通信(Telematics)技术构建的车与互联网之间的网络，实现车与服务信息在车载移动互联网上传输，如图14所示。车载移动互联网是先通过短距离通信技术在车内建立无线个域网或无线局域网，再通过4G或5G技术与互联网连接。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6MN8CyUAre8L2OPZ2RriaOlEflN8y6puWVtJz6ojnEM08fq4kUWYOrmQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图14车载移动互联网结构组成

## 2 车联网的应用

### 2.1 车联网应用分类

便捷、安全和环保是车联网应用的核心价值。车联网通过对多样化信息的融合，可以面向不同用户开发个性化的应用，为出行者提供更加便捷的交通服务，为车辆驾驶员提供智能化的安全服务和控制，为交通管理部门提供节能环保的交通服务和控制。表2列出了车联网基本的应用内容。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6mNPRialmiaM6XPwj1ibZx0vkamrXDPRobx2nSiaaicGWKazZza1sd9Zahhg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6iaiaTicpDgYS5yAdfT4oh8z8zMbmVBUI3zn5V0lcUhxMnrFwrrcz51Shg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

表3反映了车联网在交通运输、汽车、IT、金融保险等几个领域的主要应用，可以看出，车联网对提高行业效能，深耕服务品质，推动建立便捷、安全、环境友好型社会，有着重要意义。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6q7rffe51qPpzQEth2OLNZDdiav4vjVh3UjpgSmxpJdLgV4WhLJzn1DQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 2.2 丰田汽车车联网

丰田汽车在TNGA(丰田全球新体系架构)生产的新一代凯美瑞、卡罗拉等车型上借助现代信息和通信技术，导入了TOYOTA Connect(丰田智行互联)系统(即车载通信系统，简称车联网)，实现车内、车与车、车与路、车与人、车与服务平台的全方位网络连接。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6yr8xlN8cYOe298YJdLVibZb7Cgo1zDlgibfaFIQD7AJBpHRhdl8fMgdA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图15 丰田车载通信系统组成示意图

如图15所示是丰田车载通信系统组成示意图。通过车载通信系统，能够24小时365天提供紧急救援，车辆被盗追踪等真正“安心、安全”的服务；同时通过车联网技术，实现车况确认、远程控制、提供“快捷、便利”的服务。在CR(客户服务)活动支援方面，根据车辆使用状况向客户提供最合适的商品，确保车辆入厂保养与维修；故障发生时及时主动给客户提供远程诊断帮助。

TOYOTA Connect车联网服务项目和目的，如图16所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6eYhTZZML9dic7sSuibIHTl3wJ2j6g3edNjrmlvGrRtu6z64SUvBzwTicg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图16 丰田智行互联系统服务项目

图17所示为是车载通信系统电路图。丰田车载通信零件组成及功能说明如表4。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6DZjcDATiboZaMAK98eZPFGfGpibjC7YDbA6n12iaP3KddEDKicw9W7DQxA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图17 丰田车载通信系统电路图

丰田车载通信零件组成及功能说明如表4。

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6ia586zd7ic5icJWZ91xjpNNfqtJfBdAtyzEcm4ogoMXXWdSamF22IiaSLw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/r0dtNnQI8FKyyicbOY7ow8CTAhmPHJny6OXXNicLic6ovApZjvpZcjhwXanqN7kCRYpBu9LZ29uugf9L00rQySpbg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

车联网技术是车辆信息化的核心内容，是互联网、车际网V2X和车内网技术一体化发展的必然结果。今后，随着5G技术在V2X的运用，V2X通信技术能够实现更加安全、高效、便捷的驾驶体验，同时也是未来高度自动驾驶的基础支撑,是汽车产业融入万物互联时代的重要途径。相信车辆全面智能化、网联化已为时不远了。