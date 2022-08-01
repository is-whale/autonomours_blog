- [ADAS/AD系统开发04 - UDS与引导加载程序 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/44653586)

本文属于ADAS控制器开发系列。以智能前视摄像头模块为基础。

**前言**

引导加载程序，即Bootloader（简称BL），是ECU的基本模块，实现方式也很多。

本文介绍的Bootloader刷写功能的下载协议是通过UDS诊断服务实现的。

**一、Bootloader简介：**

Boot Loader在嵌入式系统里，一般分为两部分：PBL（Primary Bootloader，第一引导加载程序）和SBL（Second Bootloader，第二引导加载程序）。

PBL一般固化在程序Flash存储空间中，地址一般在CodeFlash（类似于PC程序或手机APP程序中的代码区）的最开始位置，例如0x0000到0x7FFF。FBL本身一般无法通过车辆CAN网络进行刷写。需要刷写FBL一般有两种方式：1.将ECU拆开，利用ECU刷写夹具进行刷写（如图1）。2. 利用CCP或XCP协议，通过SBL来刷写PBL。

PBL中一般会存储重置（reset）和中断（interrupt）的向量地址。换句话说，PBL中唯一的中断处理任务就是reset。PBL一般只用来引导SBL加载到RAM中，真正刷写任务是由SBL完成的，在内存中keep alive的SBL，还可以反过来刷写PBL。

![img](https://pic1.zhimg.com/80/v2-f0d0783a154c647521d362ba871766c8_720w.jpg)

图1 ECU刷写夹具

SBL一般存在于RAM（内存）中，一般是由PBL将SBL代码加载到RAM中，每次使用过后会从RAM中删除。SBL可以认为是PBL的超集。大部分Bootloader既有PBL，也有SBL。SBL可以刷写CodeFlash和外挂Flash等等。但是有些要求较低的OEM，也会允许只有PBL的情况。下文以只有PBL为例，进行简单说明：

**2. FBL的入口：**

产品正常情况下，是运行在APP模式中的；想要进入Bootloader模式中，必须要Reset后才能进入。在Bootloader刷写完程序后，需要自行退出Bootloader模式，并触发Reset后，再次进入APP模式中。

Reset有Hard Reset（硬重启）和Soft Reset（软重启）之分，一般情况下，我们要求进入Bootloader模式中的重启必须是硬重启。除非OEM主机厂要求支持软重启。

PBL使用UDS服务实现的，因此在BL模式中需要支持的UDS服务展示如下：

**3. BL模式需要支持的一些DID汇总**

a. 判断目前ECU在哪个模式下运行的DID，例如是在APP模式，还是BL模式；

b. 计数刷写成功次数的DID，且在APP模式下，只能读；在BL模式下，能读能写；

c. 计数尝试刷写次数的DID，且在APP模式下只能读；在BL模式下，能读能写；

d. CRC计算的起始地址的DID，可读可写；

e. CRC计算大小的DID，可读可写；

f. 最大允许刷写时间的DID，可以通过配置模块来更改数值，如果超过时间还没刷写完成，就强制退出，刷写失败；如果设置为0，相当于不限制刷写时间。

**4. BL模式需要支持的一些RID汇总**

在BL进行刷写过程中，需要调用几种Routine Control UDS服务，

a. 擦除Flash存储空间的RID；该RID只能用于BL模式；

b. 检查刷写相关项，若只有单一应用软件镜像在使用，则总是返回ture；只允许在BL模式中运行；

c. 检查刷写前置条件的RID，用于实施刷写前，检查外部环境条件是否满足要求；只允许在APP模式下运行，检查通过，就会Reset ECU，进入BL模式；

d. 检查Flash驱动内部锁完整性的RID，用于检查Flash驱动加载到RAM后，是否无效；只允许在BL模式中运行；

e. 检查应用软件数据有效性的RID，用于检查应用程序在加载到ECU的flash后，程序和数据影像是否有效；只能在BL模式中运行；

f. 保持Bootloader始终在BL模式，尽管有效的应用程序已经找到；只能在BL模式运行。

**二、Bootloader程序一般刷写流程（基于PBL）：**

![img](https://pic3.zhimg.com/80/v2-39df68b9437c878a54d60de81e6f2126_720w.jpg)