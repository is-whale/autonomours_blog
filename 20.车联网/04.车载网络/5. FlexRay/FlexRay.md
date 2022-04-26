- [FlexRay 介绍_aFakeProgramer的博客-CSDN博客_flexray](https://blog.csdn.net/usstmiracle/article/details/121328770)

汽车上的总线技术包括：LIN、CAN、CAN FD、FlexRay、MOST及Ethernet，我们之前已经分享了LIN，CAN、CAN FD总线。在开始阅读之前，如果你对已介绍的总线技术还不了解的话，可以先阅读以下文章快速温习一下~

[说一说LIN总线](http://mp.weixin.qq.com/s?__biz=MzIxMzI3MzUwOA==&mid=2247483721&idx=1&sn=7d3e40c2dbf10852aaa6e574ab94b593&chksm=97b81f82a0cf9694111708cc0f101d4117dde7d7cc235a4f65e342ff30eddbe05cb94499ba98&scene=21#wechat_redirect)

[CAN总线基础（一）](http://mp.weixin.qq.com/s?__biz=MzIxMzI3MzUwOA==&mid=2247483678&idx=1&sn=2f65d7eed3a40d2893360e053528710e&chksm=97b81fd5a0cf96c3ea528b39bf16e5dc366cd0880e21f81a2555ccbed07654163310773765e2&scene=21#wechat_redirect)

[CAN总线基础（下）](http://mp.weixin.qq.com/s?__biz=MzIxMzI3MzUwOA==&mid=2247483690&idx=1&sn=2fc761702b9a9cc65b936a8aebd99912&chksm=97b81fe1a0cf96f70a84657e83df7d8ff52feacdd3f4403e316e2c8e90cf14fc0ef491b5c7d0&scene=21#wechat_redirect)

[CAN FD 介绍](http://mp.weixin.qq.com/s?__biz=MzIxMzI3MzUwOA==&mid=2247485186&idx=1&sn=5a9f54026c3cf1204435a6f8b45b83f5&chksm=97b819c9a0cf90dfa691398ad8cfc09965a9bb5c584746277c176c99bede73713a6f08835781&scene=21#wechat_redirect)

![Image](https://img-blog.csdnimg.cn/img_convert/ea34af8b92b4f636b6ea0367af30f347.png)

## FlexRay背景

随着汽车电子技术的不断发展和系统的集成化，我们可不需要传统的机械传递控制信号而是通过电子手段来驾驶汽车，而这一电子手段即X-By-Wire（X代表汽车中的各个系统，By-Wire可称为电子线控），如线控转向（Steering-By-Wire），线控制动（Brake-By-Wire），线控技术主要应用在主动安全等关键系统中，这些场合都对信息的实时性和安全性有很高的要求。

![Image](https://img-blog.csdnimg.cn/img_convert/9a8478b8ec0b8634725e2aa63d70707c.png)

另一方面随着汽车电子电器架构复杂度的提升尤其当前辅助驾驶系统、无人驾驶技术的快速发展，传统的LIN、[CAN总线](https://so.csdn.net/so/search?q=CAN总线&spm=1001.2101.3001.7020)已不堪重负且无法满足未来高带宽的要求，上期讲的CAN FD只是对传统CAN总线的一种扩展和过渡，首先其不会对原有的整车网络带来大的变更，具备很好的兼容性又具有不错的传输速率（最高2Mbps），其次LIN CAN总线在汽车上已应用了这么多年，若突然向新的总线技术迁移（如本期讲的FlexRay）会带来开发迁移量、时间成本、硬件成本等方面的同步提升（所有节点必须升级为FlexRay节点），因此CAN FD在当前阶段是很好的过渡方案。但当同时考虑X-By-Wire应用场景和更高的带宽要求时，CAN FD则无法满足，而FlexRay则非常适用，但FlexRay的应用对OEM的能力要求相比CAN会提高很多。

![Image](https://img-blog.csdnimg.cn/img_convert/c929cb0a0fc52a2cfe5e6ff99cb24019.png)

![Image](https://img-blog.csdnimg.cn/img_convert/83ad803844bdf60f845583a0cb30dd66.png)

## FlexRay联盟

FlexRay的出现和发展离不开2000年由Daimler Crysler 、 BMW 、Motorola 和Philips创建的FlexRay联盟的推动。该联盟的目标是开发一种独立于OEM、确定性和容错的FlexRay通信标准，该联盟的每个成员都可以使用该标准而无需支付许可费。目前FlexRay联盟的核心成员包括：BOSCH 、BMW、Daimler AG、General Motors、Volkswagen AG、NXP Semiconductors。

FlexRay联盟在2010年发布了3.0.1版规范，开始推动作为ISO标准，并在2013年发布了ISO 17458标准规范。

第一款采用FlexRay的量产车于2006年底在BMW X5中推出，应用在电子控制减震系统中，2008年，全新BMW 7系全面采用了FlexRay。另外Audi、Mercedes-Benz以及领克等车型上也逐渐应用。

![Image](https://img-blog.csdnimg.cn/img_convert/f358e2c45a7e2166e10fd00b090fe51a.png)



## FlexRay通讯特点及拓扑

FlexRay是专为车内局域网设计的一种具备故障容错的高速可确定性车载总线系统，采用了基于时间触发的机制且具有高带宽、容错性好等特点，在实时性、可靠性及灵活性方面都有很大的优势，非常适用于安全性要求较高的线控场合及带宽要去高的场合。

1、高速率和容错性

FlexRay支持两通道，可通过一个或两个通道进行数据传输，单个通道的数据传输速率可达10Mbps，通过两通道平行传输数据时可达20Mbps。也可通过双通道传输相同的数据（真实情况大多应用的方式），当其中某个通道出现故障或信息有误时，另一通道可继续正常传输，并影响整个网络的数据通讯，通过这种冗余备份实现很好的容错性。

2、确定性

FalexRay是一种时间触发式的总线系统，符合TDMA(Time Division Multiple Access)的原则，因此在时间控制区域内，时隙会分配给确定的消息，即会将规定好的时间段分配给特定的消息，时隙是经固定周期重复，也就是说信息在总线上的时间可以被预测出来，因此保证了其确定性。这就意味着控制信号是根据预定义的时间进度传输的，无论系统外部发生什么情况，都不会产生计划外事件。在确定性算法中，始终会预先定义正确的输出结果，这些结果是基于特定输入的。

![Image](https://img-blog.csdnimg.cn/img_convert/30fa6a0e7b2477c72a77b2bab013701d.png)

![Image](https://img-blog.csdnimg.cn/img_convert/8a0c1180ee2dc6be99afe75c7cbb45d7.png)

3、灵活性

FlexRay除了支持时间触发式通讯外，还可通过事件触发来进行数据的传输，例如对于时间要求不高的信息，可配置在事件控制区域内传输，可形成以时间触发为主，兼顾事件触发的灵活特性。

![Image](https://img-blog.csdnimg.cn/img_convert/a869d440a12ae5d5964970cef1432578.png)

此外，FlexRay的拓扑是多样的，有线型、星型和混合型三大类，再结合单通道和双通道的使用（FlexRay的两个通道可相互独立实现，所以两个通道可采用不同的拓扑结构，如一个通道为主动星型拓扑，另一个为总线拓扑结构），所以最终组合的结果可形成很多种。再例如既有点对点的线性结构和多节点的线性结构，还有增加冗余性的双通道星型拓扑结构等等。

![Image](https://img-blog.csdnimg.cn/img_convert/b985de26e4b1b1c31519a2aed540c3b8.png)

![Image](https://img-blog.csdnimg.cn/img_convert/151d25bef670e22c94f7993dfb2badfa.png)

## FlexRay数据传输

FlexRay规范定义了OSI参考模型中的物理层和数据链路层，每个FlexRay节点通过一个FlexRay Controller和两个FlexRay Transceivers（用于通道冗余）与总线相连，FlexRay Controller负责Flexray协议中的数据链路层，FlexRay Transceivers则负责总线物理信号接收发送。

![Image](https://img-blog.csdnimg.cn/img_convert/9bb4bbfe66e20c49205a07068f2db0be.png)

FlexRay可采用屏蔽或不屏蔽的双绞线，每个通道有两根导线，即总线正（Bus-Plus，BP）和总线负（Bus-Minus，BM）组成。采用不归零法（NRZ,Non-Return to Zero）进行编码。

可通过测量BP和BM之间的电压差识别总线状态，这样可减少外部干扰对总线信息的影响，因这些干扰同时作用在两根导线上可相互抵消。每一通道需使用80~110欧的终端电阻。将不同的电压加载在一个通道的两根导线上，可使总线有四种状态：Idle_Lp（Low power）、Idle、Data_0和Data_1

显性：差分电压不为0V（Data_0和Data_1）

隐性：差分电压为0V（Idle_Lp、Idle）

![Image](https://img-blog.csdnimg.cn/img_convert/d04049eafef7e808e50643ebdcab9cb2.png)

## FlexRay帧格式

FlexRay帧由起始段、有效负载段和结束段三大部分构成。

![Image](https://img-blog.csdnimg.cn/img_convert/cb558fff15684fc21fcbc4e847e27d62.png)

1、起始段：由40个bits构成（5 bytes），包括

-Status Bits-5bits

-Frame ID-11bits

-Payload Length-7 bits

-Hedaer CRC-11bits

-Cycle count -6 bits

其中5bits的Status Bits包含四类指示符：净荷指示位（Payload Preamble Indicator）、空帧指示位（Null Frame Indicator-指明该帧是否为无效帧）、同步帧指示位（Sync Frame Indicator-指明该帧是否为一个同步帧）和起始帧指示位（Startup Frame Indicator-指明该帧是否为起始帧）。

Frame ID：数据标志符，定义了在时间窗口（Slot）中发送的号码，每个通道数据标志符需唯一。

Payload Length：工作区长度，指示该帧含有的有效数据长度，在每个Cycle下的静态区中，每帧的数据长度是相同的，在动态区的长度则是不同的。

Hedaer CRC：用于起始段冗余校验，检查传输中的错误。

Cycle count：循环计数器。

2、有效负载段

包含要传输的有效数据，有效数据长度最大254个Bytes（0~127个Words），

3、结束段

包含24  Bits的检验域，由起始段和有效负载段计算得出的CRC校验码，计算CRC时，根据网络传输顺序从保留位到有效负载段的最后一位放到CRC生成器中进行计算。

## FlexRay编码

编码的过程实际就是对要发送的数据进行一定的打包处理，即在节点可传输带有主计算机数据的数据前需将其转换为“比特流（Bitstream）”。

![Image](https://img-blog.csdnimg.cn/img_convert/9b1437ecdaadc373a6567569bdf09a0a.png)

![Image](https://img-blog.csdnimg.cn/img_convert/f96517e2bfa5400ca28dc65de6f1101a.png)

RxD为接收信号，TxD为发送信号，TxEN为通讯控制器请求数据，对于静态帧和动态帧分别按照如下方式进行编码。

![Image](https://img-blog.csdnimg.cn/img_convert/a803dbb209b97362dcd7e50867a4582f.png)

其中TSS（传输启动序列）：用于初始化节点和网络通讯的对接（5~15位的低电平）；FSS（帧启动序列）：用于补偿TSS后第一个字节可能出现的量化误差（一位高电平）；BSS（字节启动序列）：给接收节点提供数据定时信息（一位高电平并紧随一位低电平）；FES（帧结束序列）：用于标识数据帧最后一个字节序列结束（一位低电平紧随一位高电平）。

对于动态区数据还额外需要DST（动态段尾部序列）：仅用于动态帧传输，用于表明动态段中传输时动作点的精确时间防止接收段过早检测到网络空闲状态（一位长度可变的低电平和高电平）。

将这些序列和有效位（MSB到LSB）组装起来完成了编码过程，最终构成在网络传播的比特流。

## FlexRay通讯

FlexRay总线的通讯由通讯周期（Communication Cycle）构成，从总线启动到停止都在不断重复该通讯周期。每个通讯周期具有相同的可配置时间间隔，且每个通讯周期由静态段（Static Segment）、动态段（Dynamic Segment）、特征窗（Symblo Window）和网络空闲时间（Network Idle Time）四部分构成。

![Image](https://img-blog.csdnimg.cn/img_convert/5576b50668c2b2a47314d81e05d6e37a.png)

1、静态段（Static Segment）

静态段采用TDMA(Time Division Multiple Access)方式由固定的时隙（Slot）组成，不可更改且所有时隙大小一致。

![Image](https://img-blog.csdnimg.cn/img_convert/a9a7efd5b8d7d43f5d677107207e4370.png)

![Image](https://img-blog.csdnimg.cn/img_convert/2cebbda2b439af243b1778c61c8ee933.png)

![Image](https://img-blog.csdnimg.cn/img_convert/321740f3b2e6874579486b0381045d72.png)

![Image](https://img-blog.csdnimg.cn/img_convert/72520969e5078f60f06e8186c90d95f7.png)

因此每个节点可拥有一个或多个Slots，这样每个节点在每个通讯周期内都可在其所占有的Slot内发送，两个节点也可在不同的通道上共享同一Slot，单个Slot也可为空（即不被任何节点占用），所有的帧和Slots在静态段都具有相同的长度。单个Slot的长度由总线中最长的FlexRay Message决定，其包括四部分：Action Point Offset、FlexRay Frame、Channel Idle Delimiter（11个隐性位）和Channel Idle。

2、动态段（Dynamic Segment）

动态段采用FTDMA(Flexible Time Division Multiple Access)方式，由较小的时隙（Minislot）组成，可根据需要拓展变动，一般用于传输事件控制型消息。

![Image](https://img-blog.csdnimg.cn/img_convert/f1b9cac00e247c22d6f4c2ef78ae5bf5.png)

![Image](https://img-blog.csdnimg.cn/img_convert/493f389c5cc11d845d31a2050912e315.png)

![Image](https://img-blog.csdnimg.cn/img_convert/2f84b5f5406a7b3dfefa0b53ff62cae1.png)

![Image](https://img-blog.csdnimg.cn/img_convert/7bedf83fe1effa04d7516820e8ce6ee1.png)

在动态段每帧可能有不同的长度，动态时隙（Dynamic Slot）的长度依赖于帧的长度，只有空的Slot才是实际的一个Minislot的大小。

3、特征窗（Symblo Window）

用于传输特征符号，FlexRay的符号有三种:

-冲突避免符号：用于冷启动节点的通讯启动

-测试符号：用于总线的测试

-唤醒符号：用于唤醒过程的初始化

![Image](https://img-blog.csdnimg.cn/img_convert/2304e4c160b592b41cbd74246922e13b.png)

4、网络空闲时间（NIT-Network Idle Time）

用于时钟同步处理

如下是一个通讯示例：

![Image](https://img-blog.csdnimg.cn/img_convert/e659bd4352e5b35000c580e5fca3e72e.png)

## FlexRay总结

从上面可看出，FlexRay相比传统LIN、 CAN和CAN FD要更复杂一些，因此不管对OEM还是供应商的能力要求势必提高不少，其次从传统总线技术向FlexRay迁移在成本及Effort上都要增加很多，普遍应用仍需要时间。

![Image](https://img-blog.csdnimg.cn/img_convert/1ef1fd067afd830a70b53a655cfa04dd.png)

参考文献：

1、FlexRay introduction（EB、Vector、BOSCH等资料）

 [FlexRay 介绍 (qq.com)](https://mp.weixin.qq.com/s/S0XmJWj1OTEEVJbeVDZZLg)