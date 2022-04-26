- [CAN总线基础_aFakeProgramer的博客-CSDN博客](https://blog.csdn.net/usstmiracle/article/details/121328166)

## 1 概述

汽车电子设备的不断增多，对汽车上的线束分布以及信息共享与交流提出了更高的要求。传统的电气系统往往采用单一连接的方式通信，这必将带来线束的冗余以及维修的成本的提高。

![Image](https://img-blog.csdnimg.cn/img_convert/6a42a89f6df87ebf9f83fae4b96fadaa.png)

单一布线连接

传统的单一通信的对接方式，已经不能满足现代汽车电子发展的需求，采用更为先进的总线技术势在必行。总线技术可以实现信息的实时共享、解决了传统布线方式中线束多、布线难、成本高等问题，从而提高整车通信的质量与品质。

[CAN总线](https://so.csdn.net/so/search?q=CAN总线&spm=1001.2101.3001.7020)（Controller Area Network，控制器局域网络）由德国博世公司于上世纪80年代提出，近20年来，随着CAN总线在工业测控与汽车领域的普及，CAN网络技术不断优化，取得了长足发展。如今CAN总线已经成为了汽车上不可或缺的重要环节，ECU内部的CAN总线开发也占到了ECU开发中的很大分量。在汽车中为了满足车载系统的不同要求，主要采用高速CAN和低速CAN。这两者以不同的总线速率工作以获得最佳的性价比，在两条总线之间采用CAN网关进行连接。

### （1）高速CAN（动力总线）

高速CAN总线的传输速率范围在125kbit/s - 1Mbit/s之间，主要用于传动系数传输的实时性要求（如发动机控制、自动变速箱控制、行驶稳定系统、组合仪表等）。

### （2）低速CAN（舒适总线）

低速CAN总线的传输速率范围在5kbit/s - 125kbit/s之间。主要用于舒适系统和车身系统的数据传输的实时性要求（如空调控制、座椅调节、车窗升降等）。

![Image](https://img-blog.csdnimg.cn/img_convert/8e356cbb4778f47be956ff35f4558988.png)


 整车CAN网络示意图

## 2 CAN总线特点

CAN总线是一种串行数据通讯协议，其中包含了CAN协议的物理层以及数据链路层。可以完成对数据的位填充，数据块编码，循环冗余效验，帧优先级的判别等工作。其主要特点如下：

（1）多主机方式工作，网络上任意一个节点（未脱离总线）均可以随时向总线网络上发布报文帧。

（2）节点发送的报文帧可以分为不同的优先级，满足不同实时要求。

（3）采用载波侦听多路访问/冲突检测（CSMA/CD）技术，当两个节点同时发布信息时，高优先级报文可不受影响地传输数据。

（4）节点总数实际可达110个。

（5）采用短帧结构，每一帧最多有8个有效字节。

（6）当某个节点错误严重时，具有自动关闭功能，切断与总线的联系，致使总线上的其他操作不受影响。

## 3 CAN总线物理层

### （1）总线结构

CAN总线采用双线传输，两根导线分别作为CAN_H、CAN_L，并在终端配备有120Ω的电阻。收到总线信号时，CAN收发器将信号电平转化为逻辑状态，即CAN_H与CAN_L电平相减后，得到一个插值电平。各种干扰（如点火系统）在两根导线上的作用相同，相减后得到的插值电平可以滤过这些干扰。

![Image](https://img-blog.csdnimg.cn/img_convert/cfe55635e5af20ad6499725ef738bcf6.png)

### （2）总线电平

CAN总线有两种逻辑电平状态，即显性与隐性。显性电平代表“0”，隐性电平代表“1”。采用非归零码编码，即在两个相同电平之间并不强制插入一个零状态电平。

高速CAN在传输隐性位时，CAN_H与CAN_L上的电平位均为2.5V；在传输显性位时分别为3.5V与1.5V。

低速CAN在传输隐性状态位时，CAN_H上的电平为0V，CAN_L上的电平位5V。在传输显性状态位时，CAN_H上的电平位3.6V，CAN_L的位1.4V。

![Image](https://img-blog.csdnimg.cn/img_convert/939a8bc61af7bb11b7d779a8d27a8f41.png)

![Image](https://img-blog.csdnimg.cn/img_convert/65e4c72b1735cace0dfe9223f38ff539.png)

为了确保通讯的正确性，总线信号必须在一定时间内出现在总线上，并且保证被正确采样。总线信号传输有一定的时间延迟，最大的可靠的总线波特率与总线长度有关。ISO11898中对各种总线长度有着以下定义：

★ 1Mbit/s 总线长度为40m（规范）。

★ 500kbit/s 总线长度最大值为100m（建议值）。

★ 250kbit/s 总线长度最大值为250m。

★ 125kbit/s 总线长度最大值为500m。

★ 40kbit/s 总线长度最大值为1000m。

## 4 CAN总线硬件设备

（1）CAN通信线缆，实现节点的互联，是传输数据的通道。主要有：普通双绞线，同轴电缆，光纤。

（2）CAN驱动/接收器，将信息封装为帧后发送，接收到的帧将其还原为信息、标定并报告节点状态。

（3）CAN控制器，专按协议要求设计制造，经简单总线连接即可实现CAN的全部功能。包括：SJA1000（Philips），82527（Intel）。

（4）CAN微控制器，嵌有部分或全部CAN控制模块及相关接口的通用型微控制器现如今很多芯片都配备CAN接口。



![img](https://img-blog.csdnimg.cn/68c02582381340ffbe60eb49ed21eb6d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAYUZha2VQcm9ncmFtZXI=,size_15,color_FFFFFF,t_70,g_se,x_16)

- [002 CAN总线基础（一） (qq.com)](https://mp.weixin.qq.com/s/2e7fdRxSzrFLenbXObBxHw)

- [003 CAN总线基础（下） (qq.com)](https://mp.weixin.qq.com/s/f-PCQMN9dkwGoEam2BOisg)