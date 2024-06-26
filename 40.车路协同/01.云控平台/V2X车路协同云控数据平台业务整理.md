- [V2X车路协同云控数据平台业务整理](https://blog.csdn.net/a1290123825/article/details/116524479)

V2X即Vehicle-to-Everything，是智能汽车和智能交通的支撑技术之一；首先我们要明确梳理该篇文章能够达到哪些目标？清楚【V2X车路协同云控数据平台】是做什么的？为什么需要？如何去做?（wwh的问题what\why\how)肯定是必选项；

其次我们还要清楚需要解决的核心问题有哪些:

- 车端和路侧与云端控制平台数据的上下行整体通路是怎样的？（智能路侧&自动驾驶车感知设备<—>v2x边缘云计算<—>v2x中心云数据平台—>高精地图&百度地图&车载设备）
- 车路协同相关的数据对象有哪些？（静态数据：sensor|rscu等设备信息；动态准实时数据：融合感知交通参与对象&交通事件&设备在线数据）
- 如何支撑车端和路侧各类sensor感知检测数据的高频率、准实时接入到云端平台，并进一步进行融合服务各类业务？（上行通路）
- 云端平台如何稳定输出数据到可视化的高精地图、智能网联（车载设备）、公众出行（百度地图）等服务?（数据输出通路）
- mqtt\flink\tsdb\bos\kafka等技术组件再中间承担的重要作用以及核心技术方案有什么？（通信|业务计算|数据存储&转发）

# v2x车路协同云控平台的WWH问题

# 1、关于v2x的简单理解&思考

v2x主要包含vehicle-to-vehicle (V2V)， vehicle-to-infrastructure (V2I)， vehicle-to-network (V2N)以及vehicle-to-pedestrian (V2P)。其希望实现车辆与一切可能影响车辆的实体实现信息交互，所以包含了车辆与车辆、与路侧设备、与网络、与人的交互感知；我们可以理解为一种规定的车与车、车与人、车与路之间要实现通信的标准；如果我们实现了这种标准的话，L5级的自动驾驶实现起来会很容易，现有自动驾驶车辆的传感器就可以大大减少<font color='red'>（目前只能达到L2~L4级别自动驾驶）；</font>

- 车车通信能够计算车辆之间的距离，不需要使用雷达等；
- 车路通信能够实现车辆在交叉路口与路侧设备以及信号灯的感知，那么车辆的摄像头可以减少一些；
- 车人通信能够让车辆知道行人在哪里，是否有可能产生碰撞等，同样可以减少雷达和摄像头的使用； 

要全面实现V2X是及其困难的一件事

- 首先拿车车通信来说吧。V2X是基于5G标准的一个车联网标准，这就要求市面所有车辆全部具备5G通信能力，这意味着目前中国道路上跑的汽车全部不符合要求，那么把现有汽车全部换为具备5G通信能力的汽车，你可以想想需要多久？
- 再拿车路通信，那这要求所有的道路两旁以及交叉路口的信号灯也要具备5G通信能力，这就是国家一直所说的新基建，你可以评估这需要多久？<font color='red'>(项目重点推进)</font>

V2X的实现能够大大降低自动驾驶的难度，但V2X实现本身就是一件非常困难的事情。所以现有的自动驾驶方案都是通过大量的传感器来实现。我认为V2X是实现自动驾驶的必由之路，但需要等上几年。<font color='red'>（理想与现实之间有一条很漫长的路在等待着！）</font>

# 2、关于车路协同理解&思考

自动驾驶界已经逐渐达成的共识：

- 单从车端来看，车身搭载的传感器存在巨大短板，其视距较短，往往只有150-200米，FOV角也很有限，无法大范围感知道路环境，也难以识别重要路标，尤其是文字。如果要对于整个画面做计算的话，对于算力的要求会很高。另外，遮挡、恶劣天气等对于单车传感器的感知能力影响明显。鲁棒性、场景适应性、数据的准确性都会受到影响。<font color='red'>（总结单车感知高成本、高技术壁垒）</font>

- 如果路侧的同构感知设备等基础设施完成布局时，车端则可以摆脱部分昂贵的传感器，用后视镜、摄像头等相对简单的传感器保障基本安全，配合交叉路口的信号灯和路侧通信计算设备（RSCU|RSU）边缘计算分担计算压力，共同实现高级别的自动驾驶<font color='red'>（总结车路协同）</font>

- **车路协同主要涉及车端、路端和云端三个端口。路端会部署摄像头、毫米波雷达、激光雷达等多种传感器设备，这些设备与车端传感器是同构的，因此可以更方便地实现数据的传输与交互；**

# 3、关于云控理解&思考

云端借助感应设备采集来的大数据进行数据处理分析、构建业务场景；万辆出租车一天就会上传数亿条 GPS 数据，加上车牌、监控等数据，交通有关的数据量级已经从 TB 等级跃升到了 PB 等级。

**云端也可以分为中心云和边缘云**，边缘云的作用是在数据最初级、最密集的边缘端提供具有云端计算能力的服务器<font color='red'>（例如百度自研RSCU）</font>，是在最接近源头的地方将数据初步处理，同时也可以减轻中心云端的接入和运算压力，**中心云与边缘云计算的结合可以将云计算的效率和成本发挥到最佳水平**。

**除了计算能力强，云端还可以对交通流做集中控制，构建起云控平台。**同样在分析完所需要的数据后，根据云计算的结果，云平台也可以通过车路协同系统网络自动下发实施控制信号，实现全自动、全工况的动态交通系统控制。例如实时交通管理服务功能域中的交通控制子功能，当各个车辆上传的位置、速度以及方向等大数据通过云控平台的云计算系统，计算出一周中不同时段不同路段不同方向的车速及流量情况后，动态的计算出各个路口各个方向红绿灯的相位和时间，达到最优的通行速度，并将这些结果数据通过云控平台发送到各个路口的信号灯控制器，实施动态控制信号灯的绿信比，达到交通效率最优控制<font color='red'>（其实是一种交通研判）</font>。

# 4、v2x车路协同云控平台的整体实现架构

总结前面所说传统的单车自动驾驶需要单车搭载大量的成本高昂感应器（毫米波|激光雷达、摄像头），即使这样单车感应也存在视距只有150~200m、FOV视角有限，无法大范围感知道路环境，也难识别路标、文字，恶略天气影响，如果需要对整个画面做感知计算的话，对于算力的要求也很高，所以利用V2I车路协同的理念（也是政府现在大力推动的新基建），在路侧150m间隔布置与车端同构的感知设备(毫米波|激光雷达、摄像头)，车端则可以摆脱部分昂贵传感器，用后视镜、摄像头等相对简单的传感设备可保障基本安全；再配合部署路侧的RSCU|RSU具备通信、计算能力的（边缘云服务器)设备，实现传感器数据的采集和边缘云计算的能力；路侧RSCU最终将感知算法识别的数据输出到中心云（机房服务器）的各系统中做进一步的计算业务处理，边缘云和中心云结合将计算效率和成本发挥到最佳；同样中心云也会接收来自车端通信计算设备OBU一部分感知业务数据；**【v2x车路协同云控数据平台】主要承担的就是中心云的能力，最终赋能到智能网联场景(例如百度的度小镜)、公众出行场景（百度地图app）以及**<font color='red'>交通监管业务（目前主要业务，结合高精地图实现的大屏前端+B端管理后台）</font>

- 【V2X】依赖统一的路侧基础感应设施、车辆感应设备|定位通信设施、以及统一的网络模式；
- 【车路协同云控】通过部署于 **路侧的RSCU|车端的OBU 提供边缘云的能力**实现边缘感知计算、决策控制的能力；
- 【车路协同云控】中心云的负责将车端、路侧的融合感知数据、sensor设备数据进行采集、分析，最终应用到智能网联场景(例如百度的度小镜)、公众出行场景（百度地图app）以及<font color='red'>交通监管业务(目前主要业务，结合高精地图实现的大屏前端+B端管理后台)；</font>

![img](https://img-blog.csdnimg.cn/20210509203127208.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExMjkwMTIzODI1,size_16,color_FFFFFF,t_70)

**车路协同云控数据平台相关数据对象**

- **车路融合感知数据：**障碍物（行人|机动车|非机动车|特殊物品）&交通事件数据（危险行为|道路状态）
- **车端车辆相关数据&路侧sensor设备相关数据：**基础数据&车端GPS|CAN|里程统计数据&路侧各类sensor设备|RSU|RSCU通信计算设备在线状态|数据流量数据

# 5、中心云平台技术挑战-车路协同流计算引擎

- 各类路侧、车端感应器设备的在线情况、故障的准实时监控；（利用<font color='red'>mqtt遗嘱消息|心跳包+层级主题+qos</font>实现物联网数据的接入，结合kafka实现海量消息的集成 的功能）
- 各类实时<font color='red'>高频（10hz+）</font>车端、路侧感知障碍物数据、交通事件数据的融合、抽帧降频；（flink流处理引擎的窗口计算）
- 路侧设备的数据量实时统计、交通路口车流数据实时统计；
- 结合高精地图实现交通监管的大屏可视化需求，实现智能网联（度小镜）&公众出行场景（百度地图app）的数据输出对接需求；

## 5.1、基础技术选型

### a、物联网设备数据的接入离不开 Mqtt/MqttBroker

最开始就是为物联网设备的网络接入而设计的，**物联网设备大多都是性能低下，功耗较低的计算机设备，而且网络连接的质量也是不可靠的**，所以在设计协议的时候最需要考虑的几个重点是：

- 协议要足够轻量，方便嵌入式设备去快速地解析和响应。
- 具备足够的灵活性，使其足以为 IoT 设备和服务的多样化提供支持。
- 应该设计为异步消息协议而非同步协议，这么做是因为大多数 IoT 设备的网络延迟很可能非常不稳定，若使用同步消息协议，IoT 设备需要等待服务器的响应，对于为大量的 IoT 设备提供服务这一情景，显然是非常不现实的。
- 必须是双向通信，服务器和客户端应该可以互相发送消息。
- mqtt broker主要设计的侧重点就是实现即时通信。
- 公司自研物联网核心套件Iot Core，支持原生Mqtt即时消息传输协议，实现在智能设备与云端之间建立安全的双向连接（ TLS/SSL 双向认证）；
  

### b、结合Kafka实现大量设备消息采集-存储-处理

<font color='red'>mqtt broker仅仅起到消息转发的作用，即物联网设备的消息转发（主动推送）到数据处理程序。当物联网设备数量巨大，某个瞬间推来大量数据，会导致处理程序应接不暇，特别是数据库达到瓶颈。此问题最普遍的解决模式——采用消息队列，把消息可靠的保存下来，再慢慢的处理（被动拉取），而这正是kafka的功能。</font>

**Kafka 虽然也是基于发布/订阅范式的消息系统，但它同时也被称为“分布式提交日志”或者“分布式流平台”，它的最主要的作用还是实现分布式持久化保存数据的目的。**Kafka 的数据单元就是消息，可以把它当作数据库里的一行“数据”或者一条“记录”来理解，Kafka 通过主题来进行分类，Kafka 的生产者发布消息到某一特定主题上，由消费者去消费特定主题的消息，其实生产者和消费者就可以理解成发布者和订阅者，主题就好比数据库中的表，每个主题包含多个分区，分区可以分布在不同的服务器上，也就是说通过这种方式来实现分布式数据的存储和读取， Kafka 分布式的架构利于读写系统的扩展和维护（比如说通过备份服务器来实现冗灾备份，通过架构多个服务器节点来实现性能的提升），在很多有大数据分析需求的大型企业，都会用到 Kafka 去做数据流处理的平台。

### c、结合Flink提供设备数据流的有状态实时计算处理

<font color='red'>海量的设备消息数据需要进行一些高性能、高可靠、分布式的Flink流计算引擎的流计算处理，例如 车流量数据、设别数据量的实时统计，感知交通参与对象、交通事件数据的抽帧降频场景；我们需要对上述四类业务数据场景做窗口准实时计算，窗口计算需要使用到状态，同时flink提供了完备的状态管理机制；</font>

什么场景会用到状态呢？下面列举了常见的 4 种：

- 去重：比如上游的系统数据可能会有重复，落到下游系统时希望把重复的数据都去掉。去重需要先了解哪些数据来过，哪些数据还没有来，也就是把所有的主键都记录下来，当一条数据到来后，能够看到在主键当中是否存在。
- 窗口计算：比如统计每分钟 Nginx 日志 API 被访问了多少次。窗口是一分钟计算一次，在窗口触发前，如 08:00 ~ 08:01 这个窗口，前59秒的数据来了需要先放入内存，即需要把这个窗口之内的数据先保留下来，等到 8:01 时一分钟后，再将整个窗口内触发的数据输出。未触发的窗口数据也是一种状态。
- 机器学习/深度学习：如训练的模型以及当前模型的参数也是一种状态，机器学习可能每次都用有一个数据集，需要在数据集上进行学习，对模型进行一个反馈。
- 访问历史数据：比如与昨天的数据进行对比，需要访问一些历史数据。如果每次从外部去读，对资源的消耗可能比较大，所以也希望把这些历史数据也放入状态中做对比。

参考：https://blog.csdn.net/a1290123825/article/details/109095241

### d、时序数据存储&持久化消息队列&缓存

- 对于设备数据量、车流量两类数据，我们需要展示其历史趋势、周期规律，所以该类数据我们要存储到TSDB时序数据库中；
- 对于感知交通参与对象数据、交通事件数据我们需要利用持久化消息队列将其进行存储、转发；
- 对于硬件设备状态心跳检测数据我们通过缓存进行并发存储；

# 6、博文参考

- 什么是v2x：https://zhuanlan.zhihu.com/p/76438511
- v2x简单介绍：https://zhuanlan.zhihu.com/p/100217165
- 一部v2x的进化史：https://zhuanlan.zhihu.com/p/30384564
- 车路协同云控介绍：https://zhuanlan.zhihu.com/p/57087945
- 高精地图介绍：https://zhuanlan.zhihu.com/p/103752407