- [CAN FD 介绍_aFakeProgramer的博客-CSDN博客_can fd](https://blog.csdn.net/usstmiracle/article/details/121328733)

随着电动汽车，无人驾驶汽车技术的快速发展，以及对汽车高级驾驶辅助系统和人机交互HMI需求的增加，传统的[CAN总线](https://so.csdn.net/so/search?q=CAN总线&spm=1001.2101.3001.7020)在**传输速率**和**带宽**等方面越来越显得力不从心，其主要原因如下：

1、通常整车CAN网络负载大大超过推荐值(50％)

2、CAN消息中只有大约40-50％的带宽用于实际数据传输

3、总线速率通常被限制在1Mbit/s，在实际使用中的速度更低，大多数情况下为500Kbit/s；在J1939网络中使用250Kbit/s

4、最大总线速度受响应机制限制，即错误帧，ACK等

5、ACK延迟 = 收发器延迟+总线传播延迟

![Image](https://img-blog.csdnimg.cn/img_convert/4bed0f5397797d86af2eab0a61f129fe.png)



可见最直接的办法就是使用下一代总线FlexRay，这样可以一劳永逸的解决这一难题。但如果将原来所有的CAN节点全部升级为FlexRay节点，由此会带来巨大的硬件开销，软件通讯移植开发，以及漫漫长的开发周期。人们不禁会共同产生一个统一的想法，那就是 **Make CAN fast ！**

## CAN FD 的出现

![Image](https://img-blog.csdnimg.cn/img_convert/e31f31eb53da088477c85eadbfe622aa.png)



为了缩小CAN网络(Max:1MBit/s)与FlexRay(Max:10MBit/s)网络的带宽差距，BOSCH推出了CAN FD方案。CAN FD(CAN with Flexible Data rate)继承了CAN总线的主要特性。

CAN总线采用双线串行通讯协议，基于非破坏性仲裁技术，分布式实时控制，可靠的错误处理和检测机制使CAN总线有很高的安全性，但CAN总线带宽和数据场长度却受到制约。CAN FD总线弥补了CAN总线带宽和数据场长度的制约，CAN FD总线与CAN总线的区别主要表现在：

 可变速率 

CAN FD采用了两种位速率：从控制场中的BRS位到ACK场之前（含CRC分界符）为可变速率，其余部分为原CAN总线用的速率。两种速率各有一套位时间定义寄存器，它们除了采用不同的位时间单位TQ外，位时间各段的分配比例也可不同。

![Image](https://img-blog.csdnimg.cn/img_convert/d8dba03a4dfdaf91cec1cc765a2152ff.png)

 新的数据场长度 

CAN FD对数据场的长度作了很大的扩充，DLC最大支持64个字节，在DLC小于等于8时与原CAN总线是一样的，大于8时有一个非线性的增长，所以最大的数据场长度可达**64字节**。

![Image](https://img-blog.csdnimg.cn/img_convert/4b886ccf0742bc20c4944aca3124f850.png)

CAN FD示波器电平

## CAN FD 结构

与普通CAN报文相同，CAN FD报文一共具有，帧起始SOF，仲裁段，控制段，数据域，CRC域，ACK域，帧结束，共七个部分组成。

![Image](https://img-blog.csdnimg.cn/img_convert/097e289eaa3418fbcb377555587c4471.png)

 帧起始 

CAN与CANFD使用相同的SOF标志位来标志报文的起始。帧起始由单个显性位构成，标志着报文的开始，并在总线上起着同步作用。

![Image](https://img-blog.csdnimg.cn/img_convert/98ee0a44d0fc49e4d09cf82244f9ca84.png)

 仲裁域 

与传统CAN相比，CAN FD取消了对远程帧的支持，用RRS位替换了RTR位，为常显性。IDE位仍为标准帧和扩展帧标志位，若标准帧与扩展帧具有相同的前 11 位 ID，那么标准帧将会由于 IDE 位为 0，优先获得总线。

> RTR(Remote Transmission Request Bit)：远程发送请求位，RER位在数据帧里必须是显性，而在远程帧里为隐性。
>
> 
>
> RRS(Remote Request Substitution)：远程请求替换位，即传统CAN中的RTR位，CAN FD中为常显性。

![Image](https://img-blog.csdnimg.cn/img_convert/7d8566f5ebeb02ec6beb6fad24b81b26.png)

 控制域 

控制域中CANFD与CAN有着相同的IDE，res，DLC位。同时增加了三个控制bit位，FDF、BRS、ESI。

> FDF(Flexible Data Rate Format)：原CAN数据帧中的保留位r。FDF常为隐性，表示CAN FD 报文。
>
> 
>
> BRS( Bit Rate Switch)：位速率转换开关，当BRS为显性位时数据段的位速率与仲裁段的位速率一致，当BRS为隐性位时数据段的位速率高于仲裁段的位速率。 
>
> 
>
> ESI(Error State Indicator)：错误状态指示，主动错误时发送显性位，被动错误时发送隐性位。

![Image](https://img-blog.csdnimg.cn/img_convert/04fb03497a87f2b5a101eebdcce26ba6.png)

DLC数据域长度位，CAN FD同样使用4bit来确认报文数据场的长度。

![Image](https://img-blog.csdnimg.cn/img_convert/ee3c86a226b09eba3341b9feba4d80b0.png)

数据域 

CAN FD不仅能支持传统的0-8字节报文,同时最大还能支持12, 16, 20, 24, 32, 48, 64字节。

![Image](https://img-blog.csdnimg.cn/img_convert/2111606c61c7cc35255c7dcffd301337.png)

CRC

CAN FD对CRC算法作了改变，即CRC以含填充位的位流进行计算。在校验和部分为避免再有连续位超过6个，就确定在第一位以及以后每4位添加一个填充位加以分割，这个填充位的值是上一位的反码，作为格式检查，如果填充位不是上一位的反码，就作出错处理。

CAN FD的CRC场扩展到了21位。由于数据场长度有很大变化区间，所以要根据DLC大小应用不同的CRC生成多项式，CRC多项式如下图所示：

![Image](https://img-blog.csdnimg.cn/img_convert/c9f546f09331455291fc134f324a7c48.png)

![Image](https://img-blog.csdnimg.cn/img_convert/1626fa4e3fcc11be9f085948e0647ad0.png)

ACK & SOF

相比于传统CAN，在CAN FD中最多可接受2个位时间有效的ACK，允许1个额外的位时间来补偿收发器相移和传播延迟）。EOF(End of frame)同样由连续7个隐性位来表示。

![Image](https://img-blog.csdnimg.cn/img_convert/1e19134bb18335337fb2a08c87ea8ca0.png)

## CAN FD 特点

CAN FD继承了CAN总线的主要特性，提高了CAN总线的网络通信带宽，改善了错误帧漏检率，同时可以保持网络系统大部分软硬件特别是物理层不变。这种相似性使ECU供应商不需要对ECU的软件部分做大规模修改即可升级汽车通信网络。

![Image](https://img-blog.csdnimg.cn/img_convert/e45d54476f87302ee160349121c191c4.png)

尽管CAN FD继承了绝大部分传统CAN的特性，但从传统CAN升级到CAN FD依旧需要做很多工作：

1、硬件与工具：首先需要选取支持CAN FD的控制器和收发器，还需要采用新的网络调试和监测工具。

2、网络兼容性：对于传统的CAN网段的部分节点升级到CAN FD的情况需要特别注意。CAN FD节点可以正常收发传统CAN报文，但是传统CAN节点不能正确收发CAN FD报文，其原因是帧格式不一致，会导致传统CAN节点发送错误帧。

![Image](https://img-blog.csdnimg.cn/img_convert/468fc2279de2ecf9c7965bea9ec5d8ac.png)

基于以上场景，为了解决传统CAN和CAN FD的兼容性问题，芯片厂提出了一种解决方案，在传统CAN节点上采用CAN FD Shield模式的收发器，当收到CAN FD报文时，收发器会将其过滤掉，防止传统CAN节点发出错误帧，从而实现网络的兼容。

------

参考文献：

1、CAN FD introduction（Vector）

2、CAN with Flexible Data-rate（Bosch）

3、车载网络技术革新-CAN FD浅析（控制工程网）

 [CAN FD 介绍 (qq.com)](https://mp.weixin.qq.com/s/ddHv0UWNH_0v3CSyNHd9yA)