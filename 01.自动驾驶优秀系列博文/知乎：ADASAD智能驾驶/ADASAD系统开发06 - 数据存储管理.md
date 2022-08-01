- [ADAS/AD系统开发06 - 数据存储管理 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/45205839)

本文属于ADAS控制器开发系列。以智能前视摄像头模块为基础。主要介绍下ADAS/AD ECU的存储topic。

数据存储与管理这个系统BB（Building Blocks），主要是定义控制器模块的存储机制。在智能前视摄像头模块中，一般有三个存储位置，即MCU芯片中的RAM、片上Flash （Embedded Flash Memory）和位于MCU外部的一个大型Flash芯片。其中，**片上Flash和外部大型Flash就叫做该控制器模块的NVM**。所谓NVM，即Non-volatile memory，非易失存储器，具有非易失、按字节存取、存储密度高、低能耗、读写性能接近DRAM，但读写速度不对称，寿命有限。

外部大型Flash主要存储Mobileye芯片的VFP需要运行的程序和相关数据。

片上Flash主要存储主MCU芯片的程序和相关数据，如图1所示的Memory Map：

![img](https://pic3.zhimg.com/80/v2-d26726b46c8017ca2156de15a439e996_720w.jpg)

图1 片上Flash的Memory Map

如上图所示，片上Flash包括CodeFlash、DataFlash和NVRAM三大片区域。

CodeFlash用于存储代码，包括BL和APP的程序代码，以及相关的固定标定参数代码。

DataFLash包括两部分，空间较小的那部分存储一些基本信息，例如制造相关信息、SBAT相关信息、TAC相关信息等。空间较大的那部分区域则是用来做模拟EEPROM。这部分区域主要有软件层面的一个叫做KAMM（Keep-Alive Memory Manager）的模块来管理，因此这部分空间也叫KAM（Keep-Alive Memory）空间。

NVRAM（ Non-Volatile Random Access Memory） 又叫非易失性随机访问存储器，指断电后仍能保持数据的一种RAM。

**一、KAM与KAMM**

KAM空间在存储策略上采用模拟EEPROM存储策略（也叫EED存储策略，即EEPROM Emulation Driver，**EEPROM仿真驱动器**）。

而KAMM（Keep-Alive Memory Manager），则：

1. ECU第一次启动时，KAMM负责将CodeFlash中对应KAM的参数数据加载到KAM区域，使KAM区域的参数有初始值。
2. 负责将NVRAM中（正在运行的APP程序中的相关参数，以结构体形式存在）与KAM相关的数据写入KAM中，以及将KAM中的数据按优先权大小写入NVRAM。需要提一点，APP程序不能改CodeFlash的，只能用Bootloader来刷写。
3. 负责记录KAM的写入/擦除次数，使其不得超过NVM（主要指片上Flash）的最大写入/擦除次数。

**二、EED策略详解：**

1. 将KAM（Keep-Alive Memory）空间划分成四个页（EEPROM Block），其中Block0=Block1=16k，Block2=Block3=32k大小。
2. KAM采用EDD策略，因此只支持页操作，要删除就整个页的删；不能像正常Flash那样，按位（bit）来读、写、删。
3. 这4个页，每个页的首地址，包含每页的标志位，记录该页的状态，一般有三种标志位：Active、Alternate和Erased。被这三种标记位标记的页，分别称为活动页（Active）、备用页（Altenate）和已擦除页（Erased）。活动页代表正使用（正在读写）的页；备用页代表一旦活动页写满，可随时将活动页中的**有效数据**copy到该页中，然后擦除之前的活动页（擦的比较）。
4. 第3条上加粗了“有效数据”四个字，为啥要将活动页中的有效数据复制？难道有无效数据存在？？？事实上这就是FLash模拟EEPROM的精髓所在。如果是在EEPROM中存数据，那很简单，给个地址指针，要读要写直接操作啊，每次写一次，就更新一次那个地址的存储信息就好了。比如有个参数vehicle*speed，vehicle*speed也是指针，指向指针的地址是0x1111，数值80kph，想更改为100kph，如果是EEPROM，那简单，直接改0x1111地址的值就可以了。但是Flash想写数据，就必须先擦除操作（先擦除后写入原则）为了减少擦除次数（况且擦除时间很长，你也等不起啊），不想因为改一个数据的值，就擦除一次，因此设置了一个虚拟地址和数值，例如vehicle speed和80kph，当想“修改”该数值时，实际上不是真修改，而是让虚拟地址会指向同一个页内的、未使用的、擦除过了的区域直接进行写操作（满足了先擦除后写入原则），再写一个100kph进去。这样不就偷懒了一次，少了一次擦写啦。而且，老的80kph数据，和新的100kph数据都存在于该活动页当中。不过系统可以智能的知道，100kph是最新数据，假如需要读vehicle speed数据时，系统不会再去找那个80kph，而是直接找100kph这个数。那么这个80kph就是无效数据，100kph就是有效数据。等到这个页都写满了之后，把都是最新的参数拷贝到下一个页中，那些老的不用的数值，一次性随着整个页的擦写，而消失。（PS：感觉有点像iOS中的Auto Release Pool哇！攒一坨废数据后，一起释放）。

![img](https://pic3.zhimg.com/80/v2-bbe1b90842d3b7d0634c5aa83ad4375a_720w.jpg)

图1 DataFLash的Memory Map

![img](https://pic3.zhimg.com/80/v2-9a98fde2fcd0197945119db2aabf90fa_720w.jpg)

图2 外部Flash与片上FLash的比较

**三、KAM数据结构**

KAM的数据结构为了操作方便，一般也会按照功能逻辑上写操作时机（Trigger timing）相似的方法进行分组，分成Type0、Type1、Type2和Type3等几个不同的结构体，每个结构体里，封装一组参数。当然，这里的Type0/1/2/3等结构体，跟之前的Block1/2/3/4页没有关系，Block1/2/3/4的大小都是16k以上级别的，而每个Type0/1/2/3结构体占用空间才1b~2kb。这些Type结构体是存储在页中的。

分组结构体有不同的优先权，当几个结构体同时需要读写时，会优先处理优先权最高的结构体。