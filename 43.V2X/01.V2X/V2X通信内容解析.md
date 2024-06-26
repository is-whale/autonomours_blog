- [箩筐分享｜V2X通信内容解析 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/474519758)

前向碰撞预警、交叉路口碰撞预警、逆向超车预警、车内标牌。。。这些名字在近些年已经被叫的耳熟能详，稍微业内的人士大概都知道，通过V2X可以实现这些功能。那么V2X到底向外发送了哪些信息才使得这些功能成为可能呢？

在我国，车辆向外发送的消息叫**BSM**（Basic Safety Message），既基本安全消息。BSM是V2X通信中使用最广泛的消息，目前所有的V2V应用都是基于BSM消息来实现的，下图为BSM的消息内容简介（点击图片看大图）。可以看到BSM内包含的内容从速度到位置，从挡位到加速度应有尽有。



![img](https://pic2.zhimg.com/80/v2-fb859863399631ae752c3acf627cb155_720w.jpg)

BSM是层层嵌套的结构，这里显示的是简化后的架构，BSM除了车辆的基本信息外还有一些附加信息，比如车辆安全辅助信息（VehicleSafetyExtensions）里就车辆特殊状态位、历史轨迹、预测路线、灯光信息内容。安全辅助信息里最重要的就是车辆特殊状态位（VehicleEventFlags），里面包含了很多与安全相关的重要标志位，也是一些V2V应用所使用的重要信息来源。

另一个特殊的数据帧是紧急车辆附加信息（VehicleEmergencyExtensions）。这是专门给救护车、消防车等特种车辆使用的数据帧，通过紧急车辆附加信息即可判断是否提醒驾驶员进行避让，例如下图的紧急车辆提醒。

![img](https://pic4.zhimg.com/80/v2-21410249d06cfa93ee124e26d09db80b_720w.jpg)

（图片来源于网络)

以上就是V2V应用所需要的BSM消息的主要内容，在V2I应用中主要需要的消息为地图消息（**MAP**）、信号灯相位与配时消息（**SPAT**）、路侧消息（**RSI**）、路侧安全消息（**RSM**）。

MAP消息为路侧单元(RSU)发出的局部地图消息，包括局部区域的路口信息、路段信息、车道信息，道路之间的连接关系等。这与我们车载导航中的地图是完全不一样的。MAP消息和BSM一样也是层层嵌套的，它的主要内容如下图所示：



![img](https://pic1.zhimg.com/80/v2-794647c0f08995fbb593d256abe17f7c_720w.jpg)

MAP消息通常和SPAT消息搭配使用，SPAT是Signal Phase and Timing Message的缩写，是红绿灯相位和配时信息，它的主要内容为：



![img](https://pic1.zhimg.com/80/v2-ec6c6d5b2f989e3236c27831c5cc9650_720w.jpg)

得到MAP和SPAT消息后，车辆可以根据当前的位置及红绿灯信息来判断是否要激活闯红灯预警或者绿波车速引导（如下图）等。

![img](https://pic2.zhimg.com/80/v2-54485e2c9b588445a3143074ebf5dcc9_720w.jpg)

（图片源于网络)

若想感知路段上更多的动态交通信息，则需要用到RSI消息，RSI是Roadside Information的缩写，它的内容如下：

![img](https://pic2.zhimg.com/80/v2-0dd608ea0d3fa000a3596140f538ddb5_720w.jpg)

可以看到RSI消息主要由交通事件和交通标志组成，交通事件采用GB/T 29100-2012《道路交通信息服务 交通事件分类与编码》中的格式，可以枚举出交通事故、交通灾害、交通气象、路面状况等事件。交通标志采用GB 5678.2-2009 《道路交通标志和标线 第2部分：道路交通标志》中的定义。 使用RSI可以实现车内标牌（如下图）、前方拥堵提醒等功能。

![img](https://pic3.zhimg.com/80/v2-f5c01481abd478d83889fd73ffb486fa_720w.jpg)

（图片源于网络)

最后一个重要的消息则是RSM，它是Roadside Safety Message的缩写，它的主要内容有：

![img](https://pic1.zhimg.com/80/v2-ffcc3914664defb69b6cfe56e6ab73a0_720w.jpg)

RSM当前主要由RSU通过自己的传感器进行感知并将感知到的交通参与者信息编成RSM消息发送出来，基于V2I的弱势交通参与者碰撞预警(如下图)功能就是使用了这个消息。

![img](https://pic1.zhimg.com/80/v2-8254921be416a156b4cf7eecb806cce4_720w.jpg)

（图片源于网络)

以上简要介绍了V2X的消息内容以及相关的应用，当前在我国有CSAE 53-2020《合作式智能运输系统 车用通信系统应用层及应用数据交互标准（第一阶段》和《YDT 3709-2020 基于LTE的车联网无线通信技术 消息层技术要求》两份标准中定义了V2X的消息集合，两份标准的消息集内容是一样的。