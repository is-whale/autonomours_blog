- [OpenV2X 标准整理 (qq.com)](https://mp.weixin.qq.com/s/zDyG0MwH51B4yNYQvMUxmQ)

## 1 前言

中国车联网主要标准组织和联盟包括CCSA(中国通信标准化协会）、C-ITS（中国智能交通产业联盟）、SAE-China（中国汽车工程学会）、NTCAS(全国汽车标准化委员会）、TIAA（车载信息服务产业应用联盟）、TC/ITS（全国智能运输系统标准化技术委员会）、全国道路交通管理标准化技术委员会、IMT-2020(5G)推进组C-V2X工作组、CAICV(中国智能网联汽车产业创新联盟）等。

依据《国家车联网产业标准体系建设指南》系列文件指导，2018年开始，中国通信标准化协会(CCSA）、CSAE 、C-ITS 及 CAICV 相继制定发布了30多项团体标准，其中包含多项“⼈、⻋、路、云” 核心指标。

OpenV2X（https://openv2x.org）基于 V2X 标准接口，运用 5G、AI、MEC 等技术，整合产学研各方贡献，构建源码开放、生态兼容、性能优异的基础架构和开放平台。

## 2 OpenV2X 参考标准

OpenV2X 车路协同开源平台项目架构设计与功能实现参照了其中相关标准进行，主要包括：

- lT/CSAE53-2020 合作式智能运输系统车用通信系统应用层及应用数据-交互标准第一阶段
- lT/CSAE157-2020 合作式智能运输系统车用通信系统应用层及应用数据交互标准第二阶段
- lT/ITS 0117-2020 合作式智能运输系统 RSU 与中心子系统间数据接口规范
- l基于 LTE 的车联网无无线通信技术消息层技术要求

《合作式智能运输系统 RSU 与中心子系统间数据接口规范》规定了 RSU 与中心子系统的接口规范，包括业务数据接口和运维管理接口的要求。对于RSU 信息上报、业务配置下发、MAP 数据上报、RSI、RSM、SPAT 数据上报及数据下发等数据进行了规范，决定了消息发送频率等。

《基于 LTE 的车联网无无线通信技术消息层技术要求》中的通信方式对于消息交互都采用了广播类的通信方式，也就是说，消息的发送没有特定的对象，在通信可以达到的的范围都能接收到对应的信息。基于 LTE 的车联网无线通信技术，可以实现智能运输系统中不同子系统之间的信息交互，从而实现道路安全、通行效率、信息服务等不同的应用。协议表示，消息层数据集用ASN.1 标准进行定义，遵循“消息帧-消息体-数据帧-数据元素”层层嵌套的逻辑进行制定。消息层数据集主要由一个消息帧格式和五个最基本的消息体以及相对应的数据帧和数据元素组成。该协议给 RSU、RSI、RSM、MAP、SPAT 的消息体提供了结构参考，对于汽车的状态、信号灯属性及当前状态、不同类型的车道属性等数据帧进行了解释说明，便于后续区分人、车、路道状态。

OpenV2X 主要分为设备管理、运维管理、事件管理、云控大屏显示四个模块，RSU 设备管理、信息上报、业务配置下发、RSI、RSM、SPAT 数据上报及数据下发等功能实现均参照了上述标准，通过标准化的数据格式，有利于软硬件解耦、厂商互通、降低 V2X 产业入门门槛，有效助力 V2X 产业发展。通过各类标准接口，可以将各路路侧设备接入到边缘节点进行统一的管理、运维、监控，增强可维护性。

《T/CSAE53-2020合作式智能运输系统车用通信系统应用层及应用数据交互标准第一阶段》及《T/CSAE157-2020合作式智能运输系统车用通信系统应用层及应用数据交互标准第二阶段》指出了29种典型的应用场景，说明了29种应用场景的适用范围和工作原理，并将这29种场景分为了安全类、效率类和信息服务类。

安全类场景：

| 前向碰撞预警           | 交叉路口碰撞预警 | 左转辅助     | 盲区预警/变道预警      |
| ---------------------- | ---------------- | ------------ | ---------------------- |
| 逆向超车预警           | 紧急制动预警     | 异常车辆提醒 | 车辆失控预警           |
| 道路危险状况提示       | 限速预警         | 闯红灯预警   | 弱势交通参与者碰撞预警 |
| 弱势交通参与者安全通行 |                  |              |                        |

效率类场景：

| 绿波车速引导 | 车内标牌           | 前方拥堵提醒   | 紧急车辆提醒     |
| ------------ | ------------------ | -------------- | ---------------- |
| 感知数据共享 | 协作式变道         | 协作式车辆汇入 | 协作式交叉口通行 |
| 动态车辆管理 | 协作式优先车辆通行 |                |                  |

信息服务类场景：

| 汽车近场支付       | 差分数据服务 | 场站路径引导服务 | 浮动车数据采集 |
| ------------------ | ------------ | ---------------- | -------------- |
| 协作式车辆编队管理 | 道路收费服务 |                  |                |

OpenV2X 目前已经实现了路侧设备消息（RSM）、路侧设备信息（RSI）的下发和逆行超车预警场景，下个版本（beihai 版本）将增加4个应用场景，包括弱势交通参与者碰撞预警、感知数据分享、协作式变道，地图接入后，云控大屏会根据车辆的行驶轨迹对危险事件进行告警提示。以下为各应用场景工作原理。

### 2.1 逆向超车预警

逆向超车预警指的是两车行驶于同一车道时，其中的某辆车因为借用逆向车道超车，与逆向车道上的逆向行驶车辆存在碰撞危险。如图所示， A 车与 B 车位于同一车道行驶，C 车从相邻车道逆向驶来，若此时 B 车占用逆向车道进行超车，由于 B 车视线可能被 A 车遮挡，B 车和 C 车存在碰撞可能，逆行超车预警系统（DNPW）会对 B 车驾驶员进行预警。

![图片](https://mmbiz.qpic.cn/mmbiz_png/OzEcg7CtMiaJuNBj0IlZwmUsSmfiaAgwx9qVufeVXyaDtziczaFH0XLniaaqnDPD9hAFLYwvWDVwo05ua1wLO4efqg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

逆行超车预警系统（DNPW）工作原理为：

1. 分析接收到的车辆信息，筛选与 B 车左/右前方相邻逆向车道逆向行驶的车辆
2. 进一步筛选两车/多车之间的距离范围是否安全，是否存在潜在威胁车辆
3. 计算每一个潜在威胁车辆到达碰撞点的的碰撞时间和碰撞距离，筛选出与 B 车存在碰撞威胁的车辆
4. 若存在多个威胁车辆，则筛选出最紧急的威胁车辆
5. 若发现 B 车主动进行变道超车动作与逆向车道上的车辆碰撞条件成立，系统通过人机交互界面（HMI）对 B 车进行预警

### 2.2 交叉路口碰撞预警

交叉路口碰撞指的是在交叉路口，A 车驶向交叉路口时与侧向行驶的 B 车发生碰撞。如图所示。A 车行驶在路口，B 车从左侧或右侧驶向路口，A 车视线可能被 C 车遮挡，从而跟B车产生碰撞，交叉路口碰撞预警系统（ICW）会对 A 车驾驶员进行预警。



![图片](https://mmbiz.qpic.cn/mmbiz_png/OzEcg7CtMiaJuNBj0IlZwmUsSmfiaAgwx9BsibaNjHBxJgy0IshvDws8t8ARTDic18WCoM7YhnlBR4RJqnmowgXgag/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



交叉路口碰撞预警系统（ICW）工作原理为：

1. 分析接收到的车辆信息，筛选出位于交叉路口左右两侧的车辆信息
2. 进一步筛选两车/多车之间的距离范围是否安全，是否存在潜在威胁车辆
3. 计算每一个潜在威胁车辆到达路口的时间和距离，筛选出与 A 车存在碰撞威胁的车辆
4. 若存在多个威胁车辆，则筛选出最紧急的威胁车辆
5. 通过人机交互界面（HMI）对 A 车进行预警。

### 2.3 弱势交通参与者碰撞预警

弱势交通参与者碰撞预警是指，A 车在行驶中，与周边行人、电动车等弱势交通参与者存在碰撞危险时，VRUCW 应用将对车辆驾驶员进行预警，也可对行人进行预警。

![图片](https://mmbiz.qpic.cn/mmbiz_png/OzEcg7CtMiaJuNBj0IlZwmUsSmfiaAgwx963FwdfCJ7Elrm2fAib9ffQzS6bC5D6NNar91cVhn40iaR45GRGIK7cmA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



弱势交通参与者碰撞预警系统（VRUCW）工作原理为：

1. 分析接收到的弱势交通参与者消息，筛选出与车辆行驶方向可能发现冲突的弱势交通参与者
2. 进一步筛选处于一定距离或时间范围内的弱势交通参与者是否存在潜在威胁
3. 计算与每个弱势交通参与者的碰撞时间，筛选出存在碰撞威胁的弱势交通参与者
4. 若存在多个威胁，则筛选出最紧急的威胁弱势交通参与者
5. 系统对驾驶员进行相应的碰撞预警

### 2.4 感知数据共享

车辆以及路侧设备RSU通过自身搭载的感知设备（摄像头、雷达等传感器）探测到周围其他交通参与者或道路异常状况信息、车辆异常行为、道路障碍物等信息，并将探测到的目标信息处理后，通过 V2X 发给周围其他车辆，收到此信息的其他车辆可提前感知到不在自身视野范围内的交通参与者或道路异常状况，感知数据共享可以分为车车感知数据共享和车路感知数据共享。

![图片](https://mmbiz.qpic.cn/mmbiz_png/OzEcg7CtMiaJuNBj0IlZwmUsSmfiaAgwx9fSYo2TarneszsFzaBRRqo2AqMHwNXht3ITqX3dUvePQWVmibgBPYyVg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

车车间感知数据共享原理：

1. 两车之间具备无线通信能力，车辆 A、B 同向行驶在同一车道上，B 车视野可能被 A 车挡住
2. A 车通过车载传感器发现前方车道有障碍物或者行人
3. A 车根据 B 车的共享请求或自身算法，决定是否发送共享信息，若决定发送，则通过无线通信向 B 车发送感知信息
4. B 车接收到 A 车发送的感知信息，根据自身驾驶决定是否采取相应措施

![图片](https://mmbiz.qpic.cn/mmbiz_png/OzEcg7CtMiaJuNBj0IlZwmUsSmfiaAgwx9wG1oOH0ibXpPtYceIeTK3qIYV6ic7XQRIVjFqesGzv05oCHM8j6Zh0Uw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

车路间感知数据共享原理：

1. 车车之间或车人之间没有 V2X 通信能力，车辆 A、B 同向行驶在同一车道上，B 车视野可能被 A 车挡住，B 车与 RSU 具备无线通信能力
2. RSU 通过传感器发现道路前方有行人、车辆异常行为或障碍物，并生成道路感知信息
3. RSU 根据前方障碍物的影响房屋和道路地图信息、周围车辆，分析判断受到影响的车辆，主动向车辆 B 进行感知数据共享或由车辆 B 主动请求感知数据共享
4. 车辆 B 接收到 RSU 发送的感知信息，根据自身驾驶决定是否采取相应措施

### 2.5 协作式变道

车辆 A 在行驶过程中需要变道，并将变道意图发送给相关车道（本车道和目标车道）的其他相关车辆或路侧设备RSU，相关车辆收到变道意图信息或路侧设备的调度信息，根据自身情况调整驾驶行为，使得车辆A 能够安全完成变道或延迟变道。

协作式变道可以分为车车协作式变道和车路协作式变道。

![图片](https://mmbiz.qpic.cn/mmbiz_png/OzEcg7CtMiaJuNBj0IlZwmUsSmfiaAgwx9AslxUmDL156uVC04RXeTKfO41HouEQFngicgQoiastCAqcAoTM5R39ww/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

车车协作式变道原理：

1. 两车之间具备无线通信能力，A、B 两车在相邻车道行驶
2. A 车行驶过程中需要进行变道，并将变道意图发送给 B 车
3. B 车接收到 A 车的变道信息后，根据自身驾驶信息及周围车辆、行人等环境决定是否进行加速或减速让道等驾驶调整，并将自身驾驶行为调整发送给 A 车。

![图片](https://mmbiz.qpic.cn/mmbiz_png/OzEcg7CtMiaJuNBj0IlZwmUsSmfiaAgwx9Aez5WibAVZTUYHeuiao9cseNNiakicIM9d1jogeTs1iaWSBUuENvK027dHA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

车路协作式变道原理：

1. 车车之间不具备无线通信能力，A 车与 RSU 具备无线通信能力
2. A 车行驶过程中需要进行变道，并将变道意图发送给 RSU
3. RSU 根据 A 车到变道信息和当前相关车道的车辆信息及路况信息等作出判断，给 A 车下发变道引导信息，如让其加速或延后变道，A 车收到引导信息后，决定自身驾驶行为并将自身驾驶行为发送给 RSU 及周边其它车辆。

## 3 OpenV2X 应用场景展望

一、城市道路方面，当前城市道路路口复杂、红绿灯密集、公交出行需求量大等问题，智慧城市公交可以提升交通效率、提高乘客体验需求、促进绿色节能减排。

二、高速公路方面，目前城际交流密切、交通繁忙，高速公路极易拥堵，且雨雪雾等恶劣天气导致高速公路关闭，通行很是不便，安全事故频频发生，通行效率低下。智慧高速公路可以对高速公路进行路段监测，及时发现道路异常情况，对过往车辆进行提示，减少事故发生，降低拥堵，提升交通效率。

三、自动驾驶方面，由于城市道路和高速公路环境的复杂性，雷达、摄像头等本地的传感系统受到视距、环境等各种因素影响，暂时无法实现百分百的安全性，V2X 车辆协同网联技术可以弥补本地传感器欠缺的能力。

V2X 的核心目标是为了赋能自动驾驶和智慧公路，通过借助于人、车、路、云平台之间的全方位连接和高效信息交互，V2X 可以分为以下四类场景：

- 信息服务类场景

信息服务是提高车主驾车体验的重要应用场景，典型的信息服务应用包括紧急呼叫等，紧急呼叫业务指的是车辆能自动或手动通过网络发起紧急救助，并对外提供基础的数据信息，如车辆类型、事故地址等。服务提供方可以说政府紧急救助中心、运营商紧急救助中心和第三方救助中心。

- 交通效率类场景

典型的交通效率应用场景主要包括车速引导、绿波通行等，车速引导指的是路侧单元（RSU）收集信号灯等设备等信息，并将这些信息（信号灯当前状态及当前状态剩余时间）广播给其它车辆，车辆收到信息后可以结合当前车速和位置信息，计算出更优的行驶速度并对车主进行提示，从而提供车辆不停车通过交叉路口等可能性。

- 交通安全类场景

根据29个典型应用场景可以看到，交通安全类场景是目前关注度较高的场景类型，提前对车主进行危险预警可以避免交通事故，保障行车安全。例如在交叉路口时，车辆探测到与侧向行驶到车辆有碰撞可能，可以提供音频或者图像提醒车主以避免碰撞。

- 自动驾驶类场景

V2X 不容易受到天气等影响，可以更好的获取其它车辆、行人运动状态的信息。可助力于智慧公交、无人驾驶清洁车、无人驾驶货运卡车等场景。⽬前，典型的⾃动驾驶应⽤场景包括⻋辆编队⾏驶、远程遥控驾驶等。⻋辆编队⾏驶是指头⻋为有⼈驾驶⻋辆或⾃主式⾃动驾驶⻋辆，后⻋通过 V2X  通信与头⻋保持实时信息交互，在⼀定的速度下实现⼀定⻋间距的多⻋稳定跟⻋，具备⻋道保持与跟踪、协作式⾃适应巡航、协作式紧急制动、协作式换道提醒、出⼊编队等多种应⽤功能。

## 4 结语

城市交通的智能化治理是解决交通安全、提升交通效率的方案之一，目前，各个厂商已经中各个城市进行了智慧公路的试点，从这些试点中不断挖掘 V2X 在城市道路交通中更多的场景应用，给城市道路的智能化治理建设提供参考。OpenV2X（https://openv2x.org） 车路协同开源项目是由上海开源信息技术协会与九州云、东南大学、联通智网科技股份有限公司、中电信数字城市科技有限公司等单位联合发起，上海白玉兰开源开放研究院、天翼智联、一汽富晟、南开大学、东揽智能、谷梵智能共同参与的车路协同 V2X 开源技术项目，同时也是上海开源信息技术协会旗下首例边缘计算开源技术项目。OpenV2X车路协同开源项目基于开放源码方式，解决当前车路协同系统 CVIS 中路侧系统遇到的“平台不开放”、“生态不多样”、“算法不解耦”、“5G 未联动”的问题，整合 5G、C-V2X、AI、MEC 等技术组合，支持海量路侧信息收集、融合、智能运算和云边协同能力，遵循国际 V2X 和“新四跨”协议的标准接口定义，具备厂商兼容、接口透明、生态开放的能力，构建未来 5G/6G 网络下路侧开放基础架构（Road Side Open Infrastructure，RSOI）的事实标准，更好地服务智能车辆和行人。