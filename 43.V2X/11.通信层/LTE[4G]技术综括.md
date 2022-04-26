- [LTE4G技术综括_Chahot的博客-CSDN博客](https://chahot.blog.csdn.net/article/details/120722190?spm=1001.2014.3001.5502)

4G的很多技术被沿用到了[5G](https://so.csdn.net/so/search?q=5G&spm=1001.2101.3001.7020)中，本章节将介绍4G相关的基本技术，从而为5G技术奠基。
![在这里插入图片描述](https://img-blog.csdnimg.cn/42bfdbdb02be4f329e4c66ca25ac5be7.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
4G是2004年WiMAX失败后被提案的。基准的3G与4G网可以说完全是两种技术。但5G参考了相当一部分4G的成熟技术并改善，因此与3-4G的差异有区别，4-5G有共同的背景且共享几个技术组件，但不兼容。5G基站无法给4G设备提供服务，只能服务于兼容5G技术的新设备。虽然5G出现并且持续发展，但是并不等于4G技术就停止不前了，4G仍然在继续优化，但是比起5G拥有一些上限问题，因此虽然仍在持续发展，与5G不可相提并论。
2008-2010-2015三个年份可以看做3.9G-4G-4.5G[`LTE/LTE Advanced/LTE Pro`]，它们都属于4G，因此它们的设备具有兼容性，之所以赋予它小数点，是想通过这种方式说明技术的成熟过程。

### LTE Release 8 – Basic Radio Access

![在这里插入图片描述](https://img-blog.csdnimg.cn/a57ee021005d4c639f017867d6fdbccb.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
术语说明：
无线网络中大体可以分为两个网络，分别是终端到基站之间的网络，我们称为无线接入网【Radio Access Network/RAN】,从基站到网络运营商的网络我们称为核心网【Core Network】。
如图所示，4G中的RAN其实也就是我们说的LTE，当然也可以用E-UTRAN称呼，但LTE称呼更为普遍。其次CN则对应到4G是EPC。这两部分网络组成的网络又称为EPS【也叫SAE(Service Architecture Evolutaion)】。
此外就是进入真实网络以后的IP网络，根据OSI三层网络层我们可以知道网络传输并不可靠，因此IP连接的维持效果就决定了通信服务的品质。为了提高通信品质我们需要维持稳定的IP连接，需要的系统就是IP Multimedia Service[IMS]。当然，作为营利性企业单位，不可能通下所有的网络，但是对于企业内网，可以通过投资IMS来稳固并提高内网通信质量。
所以EPS加上IMS就成为了Service Conectivity Layer，用来稳固逻辑连接的质量。
实际上ECP并不知道外部公网或者企业内网的情况，但是可以通过SAE GW的一些应用来提供一些IMS的服务。当然本章内容不包括IMS和EPC的详细内容，IMS牵扯到了更多网络知识而非通信知识而EPC涉及到了更多企业运营商内部设备及构造的知识，即本章主要以LTE内容为主。

## Physical link view

频谱灵活性(spectrum flexibility)：LTE使用1~20MHz的带宽，并且能够从这部分带宽中分割出多小块用于不同的服务。

成对(FDD)和非成对频谱(静态TDD)：

> 成对频谱：本质上是事业单位购买上行、下行两条信道的使用权，然后用两信道分别提供上下行服务，即两个单方向通信链路。实现方式是FDD，用两种频率分别实现上行和下行的数据通信需求
> 非成对频谱：本质上是事业单位购买一条信道使用权，并根据时间单位分割上行和下行数据，在不同时间内传输不同方向的数据

![在这里插入图片描述](https://img-blog.csdnimg.cn/9a5953199c264eeaac895e0e613a0f1f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
在FDD/TDD中，有一个名为帧[frame]的单位，但是帧在它们二者中的结构有所不同。
如图所示，帧是不论无线还是有线网中，都有的一个通信单位，一个单位帧是10ms。在LTE中又将帧分为十等分，每个子帧[subframe]为1ms，实际通信中就以子帧为单位传输数据，而一个子帧可以供多个不同的使用者轮换使用。
这要的子帧再分割为两等份，每份为0.5ms，称为一个slot。而分割的理由是我们上一章节提到的，当无线通信中假如我们知 道信道向量h的值时，收信的质量将会很高。当基站发信号给终端时，除了数据之外还会发一些参照信息，根据这些信息判断终端发给基站的信息有多少干扰或失真，然后在基站对这些信号进行干扰筛选或者数理操作（如矩阵求逆）等就可以复原失真信号。

------

下行链路中，我们使用OFDM(上一章详细说明过)。可以看做频分复用和时分复用的结合体，根据时间轴细分频段需要时间轴保护带(OFDM中称为CP)，但是不需要频段保护带(guard)。 然后我们再细看图中的symbol基本单位，每个阴影小块可以看做时间保护带。CP越大， 也就说对应symbol继续拓展使用的可能性越大，也就说需要时可以拓宽一部分频段来提供服务。因此CP也被分为了两种，Normal和Extended，一般情况下都是使用普通CP。

OFDM网格阵是12x14的方格组成的，每一个方格内是使用64-QAM来区分数据信号的，也就说每个网格可以发送6bits数据。每一个方格是15Hz*(1/14)ms。
此外，下行链路使用MIMO通信方式。

------

上行信道使用SC-FDMA(Single Carrier-FDMA)，这种方式在Tx结构很简单，而在Rx结构很复杂。上行链路的Rx是基站，因此对应的服务应在终端上设置，费用也是终端支付。实际上在二阶层以上的我们将OFDM看做等价SC-FDMA，所以实际上把上行链路也看做试用OFDM也无妨。但是要知道本质上他们使用的不是一种技术。

### CRS

刚才我们说过，一个子帧可以分为2个slot，其中有一个不用于传输数据而是用于传输控制信息（主要包括告知基站信号的失真程度和干扰情况），从而方便对信号进行复原处理,也就说我们说的CRS。我们还说过基站与基站之间扫描区域内Cell时，发送的信号不是CRS而是SS（同步信号），这种同步信号是5ms发信一次。

![在这里插入图片描述](https://img-blog.csdnimg.cn/71cb3b1e29234cfa872d4493835a8100.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
不论是否有下行数据传输，基站连续传输一个或多个参考信号(每层一个)。CRS是以slot为单位传输[0.5ms/slot]。
通过CRS我们可以知道信号的失真程度，也可以知道基站与终端间的信道质量。包括之前说过的选择性服务，终端会按固定频率报告自己当前的信号状况，基站选择更好的信号状况下进行通信连接，此时也可以使用到 CRS。再就是也可以对频率错误进行筛选修订， 以及移动量测量（上一章也说明过）等。也可以用于下行信道的`相干解调`。

> coherent demodulation
> 接收机接收到的信号为已调信号, 该信号通过解调器再次与一个载波信号相乘, 解调后的信号包含一个低频信号和一个高频成分，用一个低通滤波器把高频成分滤掉，就得到了要传递的基带信号。以上这个解调过程就是相干解调。只有可以预测所有时间内的频率信号才可能完成相干解调。

CRS与移动性也密切相关，当移动速度超过300km/h，只维持连接但是几乎没有任何控制能力。一般来说走着的速度是性能最佳的，驾车也可以提供较为不错的服务。

CRS随天线接口增加而增加，因为天线本身无法被分割，只能分割信号，通过不同天线端口发送的信号，在某一个symbol如果一个天线端口发送数据，一个发送CRS，这两者在调制时会对彼此进行干扰，这种干涉信号会降低相干解调的性能，为此应该避开CRS与数据信号同时间内位于相同的symbol。因此天线数量越多，CRS的开销就会变的严重。但是也不能不发送参考信号，所以性能方面有一些问题，在5G里使用了略有不同的方式处理这个问题。

CRS可以限制关闭基站以减少电力消耗的可能性：
这句话解释起来有点麻烦，当一个基站提供的服务人群数少时，对应提供的服务质量越高，因为CRS信号减少后有更多的symbol可以用来传数据。但是当服务人群过少，也就说可能归零时，当一个基站无信号传输时就会被减少电力消耗的机制给关停作业，因此CRS因为要一直持续发送，避免了这个问题。

### Multi-diversity

本节将讨论选择性通信的具体实现，是什么已经在之前的章节说明过了
![在这里插入图片描述](https://img-blog.csdnimg.cn/50f3c80120694625af68e52ca492c4a3.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
我们说过一个方格可以传输6bits数据，而且里面包含多个用户的数据。如果一个方格只包含一个用户的数据，则方格的坐标轴将会变得特别长。这样一个小格子也被称为resource element，然后RE聚成多个形成一个块（resource block）RB。一个RB由12个subcarrier和7个symbol构成。也就说0.5s/180Hz。这样 调整每1ms进行一次。
在此基础上，由于终端的移动性，她们的信号质量会不断改变，如图所示蓝色是用户2黄色是用户1，基站根据她们信号质量较好的时候分配给她们信道资源以供传输数据。且拥有自适应的传输速率，当传输数据过程中出现信号质量变差的情况时，可以从QPSK~64-QAM 进行自我调整也就说单位RE发送的数据量可以从2~6bits中调整。信号稳定时，信道使用64QAM传输数据，最差则使用QPSK传输数据。传输的数据缓慢时，数据几乎不会丢包或者不完全，但是数据率角度上显得效率很低；反之传输效率高但是受移动性影响，以及数理原理上比特传输数目增加失败率会增加等情况，会出现数据不完整现象。

Hybird ARQ：使用stop-wait方式，收到ACK后才会继续发下一个数据，不然会在一段时间后因为超时重传或NACK再次传送相同数据。有特点的是，即便出现数据不完整的现象，不像一般的ARQ会丢弃数据包，HARQ会暂时存储不完整数据到缓存，然后下次接收到完整数据后，两者结合后再去联合解码。也就说通过这种方式补正无线通信中通信质量不佳的问题。动作周期为8ms,很快。

![在这里插入图片描述](https://img-blog.csdnimg.cn/b0a103c1114f4703a140597a1bf97ab1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
前三个OFDM symbol被称为PDCCH，携带了一个子帧里面所有资源分配、收送信方式等控制信息。
跨越全载波带宽，最大化频率分集。数据传输最不稳定的时候可能会数据传输失败，一旦某一帧的某几个symbol传输失败，对应的子帧也就不完整从而失败，因此须要传输时在各个频段传播一些相同的数据解决这个问题。
携带下行控制信息(DCI)，如调度决策，DCI就是调度信息。

PUCCH：
Hybrid-ARQ ACK
下行调度的通道状态信息，用于用户选择。在其信号最佳的时候选择它分配给信道RE。
上行数据等待传输调度请求
这些都是和终端发给基站时需要的些控制信息。

### Multiantena

![在这里插入图片描述](https://img-blog.csdnimg.cn/ec12b06a58e44c6681d1a2f510c84ea5.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
多重天线技术：基准4G技术里是4X2的。即终端2个天线而基站4个天线的情况。
多个传输层映射到多达4个天线
Layer/rank: 用来表示空间复用流的数量，用layer表示的更多。rank偶尔也可以这么表示，因为在矩阵里rank有其他 意思，和这里并不一致。
商业部署通常在DL和UL中只使用两层传输，在UL中只使用单层传输
我们假设一个2x2的通信环境，每天线对应对面的两根天线。一共有4个数据流，把他方在矩阵里就可以构造一个2x2的矩阵。根据这矩阵我们去考虑空间复用(spatial multiplexing)，这个矩阵的rank[不是秩的意思]往往都是2，也就说最大可以分2个数据流传数据。但是并不是多个数据流同时传数据就一定好，像有线网络这种网路稳定通信质量高的，像多TCP连接一样可以提高通信效率，但是如果无线网这种介质比较杂不可控问题比较多的，或者基站特别远的情况下，反而一条layer更好，因为分两条链路也就意味着设备的能量开支也要均分，未必能够提供稳定的性能支持和服务。
终端也会向基站报告信道的信息，然后基站就可以决定下行链路的送信beamforming（串联到上章内容）。同理，
在UL中使用20 MHz 75 Mbit/s的两层传输，DL则最高可达150 Mbit/s的数据速率，现实角度上上行链路并不兼容MIMO
LTE： 主要使用closed-loop 空间复用

> closed loop:Rx会提供一个信息给Tx，然后Tx可以根据这个进行一系列的反馈并调整各项参数。基站发送给终端数据后，终端回传会带有报告类型的信息，从而基站可以进行一系列调整。
> open loop: Rx不会携带控制信息给Tx，需要Tx自己制定一些规则去尝试推测需要的参数

LTE在混合ARQ协议中提供8ms的往返时间[发送后失败，收到NACK再重传]，在LTE RAN中(理论上小于4ms，偶尔高于4但是不会超过5ms)提供小于5ms的单向延迟[终端和基站之间的传输时间]。在实际部署中，包括传输和核心网络处理，大约10毫秒的端到端延迟就算非常好的网络设计了。

![在这里插入图片描述](https://img-blog.csdnimg.cn/a13aba0305c3445f836a68f6b20e4654.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
这是LTE的发展史，实际上从R-10才开始有质的变化。
Backward compatibility: 向后兼容性：后续出现的技术与更正要求兼容当前市场的设备。这也是4G和5G最大的区别之一，5G网络不支持4G设备，但是4G的新网络(4G Ad/Pro)必须支持 LTE。为了方便理解，实际上4G有三代大的技术体，我们将其称为3.9/4/4.5。它们都需要新建基站来提供更多样的服务，且它们也必须兼容之前的设备，如4.5G基站应兼容3.9G设备接入网络。
![在这里插入图片描述](https://img-blog.csdnimg.cn/ab02a7d0a5fc454691f11e4f23296102.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
R-14为止，都是以LTE为中心的补全计划，从R-15开始中心已经转移到了5G上。
简单说明一下变化：
R10: LTE-Advanced 登场。天线阵数量从4X2->8X8 MIMO，因此数据量成为了两倍，载波聚合也变为了原来的两倍，综合作用下数据率能达到1G。
R11： 解决了临近两基站的边界彼此信号互为干涉信号的问题
R12: 出现了D2D通信，即即便两台设备距离非常近也要通过很远的基站通信，这是很不效率的。让设备之间可以直接通信的技术在此被提出。除此之外还有MTC的问题也被提案了。
R13: LTE-Pro出现。Lora和sigfox等由我国华为和其他企业合作出台的新标准出台了，期初是因为3GPP的标准协议对国内某些应用有部署困难的问题，后来标准也被推广到3GPP并进行补充扩展。 核心目标是NB-IoT， NBIoT比起MTC，MTC作为物联网实在是太贵了，而NBIoT能够以更低廉的价格实现物联网通讯，是真正意义上的物联网通信技术。
此外还有LAA：授权辅助接入（Licensed Assisted Access）是以一种wifi技术，虽然企业能够对wifi进行控制，但是实时控制是困难的。也就说无线AP不能够与基站进行实时联动，LAA解决了这个问题。LAA有本地物理控制由AP提供，而数据服务控制由基站实时提供的特点。
R14: 车联网，车与附近的设备的通信【5G】。小部分为4G改进技术
R15：更短的传输间隔，减少时延/延迟

### CA&LAA

![在这里插入图片描述](https://img-blog.csdnimg.cn/699d2b3afcb14efe953cd2f6bc0c8ad1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
CA: 载波聚合。聚合的载波可以不是连续的。一个载波就如下图的蓝色提醒方块一样，不同的载波一般有不同的事业单位提供。载波简称为CC。一般情况下如下行信道中，将CC分为很多小频段，然后每个小频段承载一部分数据，而通过载波聚合可以把不同频段的频率也用来处理相同的数据。
LAA： 紧耦合使用未经许可的频谱和许可的频谱，LAA是让5GHz服务完成移动数据的“艰苦工作”，特别是在室内空间，它将与3G和LTE一起通过小蜂窝部署。信号仍然在传统网络上，但在需要时，它会将LTE有效负载切换到公共频段，以利用那里更大的5GHz可用容量。简单地说，它在下行链路中使用载波聚合，将未许可频段(5GHz)的LTE与许可频段的LTE结合起来，从而大大提高通信质量。
![在这里插入图片描述](https://img-blog.csdnimg.cn/7ae8690968ea498a9f5a45d290860f10.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
LTE: 1.4~20MHz.期初只能以单个CC提供LTE服务
CA： R10出现。可以将连续的两个CC合并成一个LTE服务。至于为什么不直接用1个40MHz的频率而是用2个基础20MHz的原因，是因为信号分为模拟信号和电子信号，模拟信号层面上40M的频段太贵了，任何技术摆脱造价来考量都是不正确的。
组件载波聚合(CC)，N个频段用于对单个设备进行发送/接收
最多支持5个组件载波，最大带宽(BW) 100mhz。【R10基准】
基带无差异，但增加了射频实现的复杂性
CA有三种类型，分别是连续聚合，不连续聚合和跨带宽聚合。
![在这里插入图片描述](https://img-blog.csdnimg.cn/4b053799e3f74e4cb875a5164530efe0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
Licensed spectrum: 无干涉，速率高，价格昂贵，且想要继续扩张频带会更加困难，不仅受制于经济，实际上频带资源是有限的。由运营商直接决定送信目标等信息，可以同时保证多个设备通信。
Unlicensed spectrum:免费，但是存在多种干涉。 2.4GHz基本上已经饱和，即便作为wifi也基本没有市场价值了。无管理信息，当有设备通信时，其他设备应等待通信结束，但是也不会发生饿死的情况，因为有最大的限定送信时间。

5GHz现在还没有那么多设备使用，所以是相对比较干净的频带，所以LAA就是使用5G来辅助下行链路了【R13标准】。
基本的LAA概念是将一个Ls作为主频带，原来使用的ULs作为副频带，主频带对副频带有控制效果。主频带传输控制信息而副频带传输单纯数据，这就是LAA的基本模式。传输数据用两频段发给同设备，而设备回信时通过控制信息让设备向两个频段发送信息。
LTE配Wi-Fi
目标:运营商控制的小单元部署，运营商有对网络的控制权
基于未经许可的频谱的Listen-before-talk[LTB], 发送信号之前应该先接收信号的特点。也就是CSMA/CA协议（老熟人了昂）
LTE Release 14开始 LAA 也开始作用于上行链路。
在5G里LAA叫NR-U，现在正在进行标准化。

### Multi-Antenna Enhancements

![在这里插入图片描述](https://img-blog.csdnimg.cn/c624ae8942dd4cc1b31d44f925436641.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
R10: 8X8 MIMO。如图所示的速率是理想速率，实际上商用LTE很难达到这些理论速率，都是实验室速率。
DL RS概念在这里简单一提，5G中很重要的概念，5G章节会细讲。这里只需要了解因为CRS的局限性，通信效率不高。

因为天线阵的丰富，从而MIMO系统变成了全方位MIMO，对其进行波束赋形时，可以对纵横两个方向进行2元波束赋形。在FD-MIMO中达到了32个。可以持续增加天线阵，5G在考虑256个天线数的天线阵。这是迈向带有大量可操纵天线元件的大规模MIMO的一步

![在这里插入图片描述](https://img-blog.csdnimg.cn/dcdc9d5d525c443ebd2615e3f68e19f7.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
ICIC(小区间干扰协调)：基站A服务设备1，基站B服务设备2，基站A对2的信号是干扰信号，基站B对1的信号也是干扰信号。为了减小这种相互干扰，可以控制功率或者使用不同的资源分配。这种行为就是ICIC。但是实际上几乎没有效果。

用于DL的CoMP(协调的多点传输/接收)
CB技术：利用波束赋形，只接受自己想要的信号，其他信号通过这种方式将其衰减化。
DPS：在瞬间由终端选择使用哪个基站通信，通信时屏蔽另一个基站从而杜绝干扰
JT：将设备的天线分为两组，一组接收基站A的信号一组接收基站B的信号，两个基站同时提供服务给一台设备，这样就能避免信号串扰。
但是这三种技术现在都已经不常使用了，因为她们的性能都不高。

LTE Release 11中通过多天线Tx进行小区间干扰协调以及调度，将基站集中放置于一个位置，然后只讲天线阵排布到其他多个位置，由天线阵传到信号回基站，基站之间物理距离可以视为同处一个空间，因此实时数据交换性能很好，可以大幅提升数据通信质量。 现在这种方式在城市里应用比较多。

## Network view

### Densification, Small Cells, and Heterogeneous Deployments

【致密化、小单元和异构部署】
通过提升通信技术来提高通信质量有一大前提，物理设备需要跟得上节奏。基准环境下，除非对天线本身进行优化，否则很难继续在物理层面上提升通信效率。
![在这里插入图片描述](https://img-blog.csdnimg.cn/23bfe58f44324e9fbb696b9ccb738216.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
目标:通过空间再利用来支持非常高的容量,即通过多设置基站，减轻原本一个服务区的基站压力，同样数量的服务群体，每个基站只需要承担原来1/n的流量，对于每个用户而言就能分配到更多的信道资源。问题是每一个基站都需要新的有线网络来连接运营商高速网络或外部网络，而有线网络的设置费用相当高。城市环境中有网络运营商提前设置好的则很容易，即便重新设置也不是大问题，但是如我国西部或美国地广人少的区域内，设置有线网需要的投资是相当巨大的。

因此比起基站，出现了更灵活成本更低的中继节点[Relay Node],终端对于这些中继节点感知不到她们与基站的不同【R10以后】。中继站连接基站，然后终端连接中继站的模式出现了。

中继节点:使用LTE无线接口技术将低功耗BS无线连接到供体小区，终端到中继站的信道称为接入链路[access link],中继站到基站的信道称为回程链路[backhaul link]

虽然这种模式实现了尽可能多的设置基站的初心，但是接入链路与回程链路本身使用的是不同的LTE网络，换言之二者彼此之间会相互干扰。一般他们需要使用不同的频段和时间槽，总体而言就是需要将一般时间用于提供两部分的网络。也就说可能会出现设置了中继以后反而因为回程链路使用的带宽而减少了用户的体验速率。因此从增加基站数的角度上虽然能够增加速率，从频带上观察反而可能会减少无线资源。整体来看反而只是扩展了一个基站原来的覆盖范围。
实际上在都市环境中，因为覆盖范围很好，使用中继可以达到增加基站的目的，从而提升数据率。但是对于人烟稀少的地方，覆盖范围比较差，反而需要从原有的有限信道资源中剥离一部分用于传递数据，整体而言反而会降低数据率。
向后兼容

IAB： 使用微波来设置接入链路或者回程链路，可以解决回程链路的资源占据问题。因为微博在基站上，距离越远质量越差，即小范围内性能良好但大范围内性能不足，故使用基站不能好好利用的微波来作为回程链路是一种很好的提升性能的方法。

### Heterogeneous Depolyment

![在这里插入图片描述](https://img-blog.csdnimg.cn/09191e7b09f2423cb394fddef18b1429.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
如上图所示，都市环境中，同一基站内部设置多个中继，从而提升用户的体验速率。但是由于基站的信号强而中继信号弱，而本身终端连接的就是基站网络，中继不过是一个分支。因此基站的强信号会干扰到中继的信号。再加上不同传输功率的网络节点混合，地理覆盖重叠，更可能发生的层间干扰，例如，微层(用于热点)和覆盖宏之间。我们从扩大信道容量的角度上来说多设置基站是好的，从频带利用率上来看使用相同频段效率更高，但是相同频段也存在干涉问题，因此这也是一个持续讨论中的问题。
还有一个问题，就是由于中继服务的人群基数少，可能会在某些时刻（小县城深夜）像热点这样的服务就完全没人使用了，但是如果一直开着中继，对于运营商而言，设备的运行维护费用（电费非常高，基站释放能量会放热，对应的也需要其他电子设备去给他降温，再加上开着还要周期性探索用户是否登录）导致无意义的支出变大，因此我们希望能够动态检测中继的活跃情况，在没有用户时关闭服务，但是实时探测用户登录并不是一件容易的事，因此这一个点也是一个持续存在的问题。

### Dual connectivity

![在这里插入图片描述](https://img-blog.csdnimg.cn/7b76b9600e174d70a3f7274be2ac9efd.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
基站和中继同时为一个范围内设备提供服务，这种方式有很多类型。可以基站和中继分别提供下行和上行链路【该方式节电效果好，因为用户使用上行链路的情况比较少】，也可以基站负责控制信息的传输而中继负责单纯数据的传输【该方式管理层面更优越】。该概念在4.5G中被提出【LTE Pro】，这是一种非常有用的技术。
LAA环境下控制信息由基站[Ls,授权频带]控制，信息由ULs控制，这也是LAA的应用之一。同时该技术也可以用于技术换代，如4-5G，3-4G，2-3G，WIFI-4G，技术换代时将数据分类并由合适的技术来提供服务。

FDD由于使用不同的频带，所以并不需要控制信息来控制上下行链路，但是TDD因为是时间槽基准的分类，控制上下行信道占用时间的比率。
静态TDD:用于大宏单元(避免DL和UL之间的干扰)，实际应用层面这一类使用的最多。
动态TDD:适用于小小区(通常是室内和少数用户)
ex）eIMTA(增强干扰缓解和流量适应)

## Device Enhancements

![在这里插入图片描述](https://img-blog.csdnimg.cn/dbc3d2c0acdd459e9896b4be35276da2.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
干扰消除： 当一个终端与一个基站进行通信，另一个基站的信号就会称为干扰信号，我们想在本次通讯中屏蔽这段信号，就需要知道这个干扰信号的信道信息。

### D2D

![在这里插入图片描述](https://img-blog.csdnimg.cn/0fa57ce3202f4e3fa0d1a3dc3af3b450.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
物理距离非常近的设备不应该通过基站通信，而是直接将数据彼此互换，这样数据率才能达到最高。分为两种，一种是两个设备都在基站范围内，基站或者公网会帮助两设备建立私有链接，我们称为sidelink用于给二者直接通信。还有一种是一个在基站范围内一个在基站范围外，基站无法辅助帮助二者建立连接，则两设备需要承担连接需要的所有前置准备，而基站内只需要发现另一个设备则基站就可以帮助连接。
D2D商业化几乎失败，只停留在发现邻居的程度。但是V2V领域正在标准化且是一种D2D的进化，未来可期。D2D是一种静态环境而V2V是一种动态环境，V2V需要考虑相对速度

## IoT

本小节简单介绍一下4-5G过渡期出现的新型产业
![在这里插入图片描述](https://img-blog.csdnimg.cn/466a39ddc5b54a05b9004472211b80ae.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
MTC是IoT的前身，即机械间通信。
要求：
海量MTC:用于物联网的传感器、执行器和类似设备
URLLC(超可靠低延迟通信):用于工业流程的交通安全/控制或无线连接

NB-IoT：使用Lora或者sigfox，使用与LTE相同的高级协议(MAC、RLC和PDCP)，并通过扩展实现更快的连接设置，是MTC的进化。eMTC和NB-IoT在mMTC(大规模机器通信)5G网络中发挥重要作用

![在这里插入图片描述](https://img-blog.csdnimg.cn/c8a606337cdc4c16abd285c4a01abb8d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
STTI用于减少延迟，以向后兼容的方式加入LTE， LTE 初期该技术并不成熟，因此可以看做在5G NR中重写，URLLC是一个全新的设计。
![在这里插入图片描述](https://img-blog.csdnimg.cn/909a9edd63b442c68c02c74eb3c985be.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
车联网，到车联万物。R12~R14为提案期，从R14开始5G雏形开始发展。车联网主要就是要安全，实时道路信息检测，包括预测碰撞防止事故等功能，车联万物则除去安全之外还会有更多娱乐媒体类的扩展。
![在这里插入图片描述](https://img-blog.csdnimg.cn/df80d5c001c14af7b8fe9b3cc5f83d8c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ2hhaG90,size_20,color_FFFFFF,t_70,g_se,x_16)
航空领域的应用，特别是UAV[无人机]。
无人机作为中继，在非覆盖区域提供蜂窝覆盖
用于各种工业和商业应用的无人机远程控制
地面和机载无人机之间的传播条件与地面网络不同
无人机与分组设备的干扰情况不同，因为无人机可以看到更多的基站