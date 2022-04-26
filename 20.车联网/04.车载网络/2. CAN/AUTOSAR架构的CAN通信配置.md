- [AUTOSAR架构的CAN通信配置 — 宋超超 | Charles (neyzoter.cn)](https://neyzoter.cn/2019/12/30/AUTOSAR-CAN-Com-Related-Conf/)

> AUTOSAR框架的CAN通信配置说明，涉及CAN Driver、CAN IF、PDUR、BSW任务、RTE任务等模块的信息流和模块介绍

# 1.AUTOSAR的CAN通信介绍

[CAN通信相关配置资源](https://pan.baidu.com/s/1m3uolhnf1HOUL_6P33fKwg) 提取码: 5r2i

AUTOSAR的通信是其最复杂的部分之一，其中CAN在汽车上有大量的应用，故对CAN通信进行介绍、配置和实验。

下图是AUTOSAR的通信框图。整个图贯穿了基础软件层（Basic SoftWare layer, BSW）的MCAL、硬件抽象层和系统服务层。

![总体结构](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/CAN_COM_Diff_Layers.png)

- **MCAL**

  最底层（红色）是MCAL，具体而言是不同通信协议的驱动，比如FlexRay Driver、CAN Driver、LIN Low Level Driver等。对于CAN Driver而言，数据接收中断服务函数在此处定义，最终也通过此处驱动实现数据的发送。

- **硬件抽象层**

  再上一层（绿色）是硬件抽象层，具体而言是不同通信协议的接口，比如FlexRay Interface、CAN Interface、LIN Interface等。MCAL层和硬件抽象层之间的PDU称为L-PDU，其（以CAN为例）全称为CAN(Data Link Layer) Service Data Unit，包括CAN数据帧ID、DLC（数据长度）、数据（L-SDU）。

- **通信服务器层**

  再上一层（蓝色）是通信服务器层，此处包含了诸多服务模块，比如CAN TP、PDURouter、COM、NM等。

  - 数据形式

    硬件抽象层和通信服务层的TP（Transport Protocal）间数据形式称为N-PDU，（以CAN为例）全称Network Protocal Data Unit of the CAN Transport Layer，包括唯一ID、数据长度、数据，**可以处理多帧CAN数据**。另外，还有一个**J1939TP**（此处未画出），可能对于汽车电子行业来说也非常重要。

    硬件抽象层和除了TP外的通信服务之间的数据形式称为I-PDU，全称Interaction Layer Protocal Data Unit。一个I-PDU中可以包含多个信号Signal，比如一个CAN数据帧长度为8个字节，第`[0:15]`位是空气悬架压力信号，第`[16:31]`位是左侧气囊压力信号，第`[32]`位是制动开关信号，具体见下图。需要说明的是，自此以后，上层已经无法知道底层的通信协议。

    ![IPDU可以包含多个信号](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/Singal_Example.png)

  - 模块

    PDUR（PDU Router）主要用于分发数据，比如可以决定CANIF（CAN Interface）模块传上来数据下一步传到某个模块（具体配置和解释见下面章节）。

    COM模块主要进行数据的缓存、管理（比如某一个Signal在一个IPDU中的定位），面下可以接收和发送数据，面上可以给SWC提供信号获取接口（具体配置和解释见下面章节）。

    CANSM全称CAN State Manager，主要用于管理CAN通信的不同网络（总线）。*我的理解是，对于**不同总线**上的CAN控制器，可以分配到不同的CAN网络，进而进行管理。*

    NM全称Network Manager，主要用于管理不同的通信协议，比如CAN、LIN、FlexRay等。

# 2.简单系统——CAN控制PWM

我们以一个CAN控制PWM的系统（CanCtrlPwm子工程）为例来解释CAN通信和上层应用如何合作运行，如下图所示。

![CAN控制PWM的系统框图](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/Global_System.png)

1. CAN Controller挂在CAN 总线上，如果有PWM设置数据发送到总线，ECU就会进入CAN接收中断；
2. 接受中断经过`CAN Driver -> CANIF -> PDUR -> COM`这个流程后，穿过BSW层，将数据存放到COM模块的缓存，即图中的`IPdu_Rx`（图中配置了64字节长度，其实对于单个CAN数据帧而言，图中三个缓存区都只需要8字节即可）；
3. BSW任务会周期性将`IPdu_Rx`的数据拷贝到`Deferred_IPdu`中；
4. 上层SWC应用pwmValueDeal可以通过RTE将`Deferred_IPdu`中的PWM设置数据拷贝到和pwmValueDeal绑定的结构体成员变量`manager.msg.value`中，对应图中的第① 步；
5. 上层SWC应用pwmValueDeal再通过RTE读取`manager.msg.value`中的PWM设置数据，进行处理后，进而存放到`manager.duty.value`，对应图中的第② ③ ④ 步；
6. 为了让执行器pwmSetActuator能够拿到处理后的数据`manager.duty.value`，上层SWC应用pwmValueDeal再通过RTE将`manager.duty.value`存放到RteBuff中，对应图中的第⑤步，**不同SWC间的通信都通过Buff来实现**；另外，pwmValueDeal还将`manager.duty.value`存放到CAN的发送缓存区`IPdu_Tx`，BSW任务会将其发送出去，对应图中的第⑤步；
7. 上层SWC应用pwmSetActuator可以通过RTE从RteBuff中读取pwmValueDeal存放的数据，进而存放到和pwmSetActuator自身绑定的结构体成员变量`actuator.duty.value`中，对应第⑥ 步；
8. 上层SWC应用pwmSetActuator可以通过RTE从`actuator.duty.value`中读取PWM设置数据，并通过回调函数来设置PWM占空比（回调函数的形式在AUTOSAR中成为CS模式，即客户端服务器模式，前面获取数据的过程成为RS模式，即接收者发送者模式），对应第⑦ ⑧ ⑨步。

*说明1：目前SWC都放在RTE任务中周期性运行，BSW任务也作为一个任务周期性运行。后期考虑将SWC分任务运行。*

*说明2：这个简单的系统只使用了1路CAN，而且IPDU和信号也非常简单。下一章节设计的multican则包含了多个CAN、多个IPDU、IPDU可能还包含了多个信号Signal。*

*说明3：目前设计的CAN通信信息流都是`CAN Driver -> CANIF -> PDUR -> COM`（接收）、`COM -> PDUR -> CANIF -> CAN Driver`（发送）的流程。*

# 3.CAN通信信息流



上一章节说明了一个简单的系统是如何工作的，但是CAN数据具体如何在BSW中流通未进行详细说明。这一部分对于整个代码来说也是较大的一块工作量。在修改代码（[master主分支](https://nescar.coding.net/p/SORL-Example/d/SORL-Example/git)，[multican分支](https://nescar.coding.net/p/SORL-Example/d/SORL-Example/git/tree/multican)）的过程中，和CAN通信相关的修改内容均进行了备注，并且带有标签`[MULTICAN]`，可以使用VSCode全局搜索查看。需要说明的是本部分对`multican`子工程进行配置说明，而不是简单系统CanCtrlPwm。下面就进行CAN通信信息流的详细说明。

## 3.1 CAN控制器配置和信号定义

- **CAN控制器配置**

  目前共配置2个CAN控制器（英飞凌芯片共 3个CAN控制器），每个CAN控制器包含3个HRH（接收的Handler）和3个HTH（发送的Handler），对应配置了不同的ID类型（标准帧或者拓展帧）、ID掩码（进而HRH可以对应接受某一些数据帧）等。具体配置信息如下所示，共12个HOH（6个HRH和6个HTH），其中HRH0配置为可以接收ID为`0x1XX`（XX表示掩码设置为0，不关心）的CAN数据帧，HRH1接受ID为`0x2XX`的CAN数据帧，HRH2接受ID为`0x3XX`的CAN数据帧，HRH3接受ID为`0x4XX`的CAN数据帧，HRH4接受ID为`0x5XX`的CAN数据帧，HRH5接受ID为`0x6XX`的CAN数据帧（掩码在下图中未体现）。该配置文件通过EB软件生成。

  ![CAN接收和发送Handler](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/HRHandHTH.jpeg)

- **信号定义**

  CAN数据分为接收和发送两部分。

  接收过程中，在CAN IF模块过滤时，共设置7个接收的PDU ID规则（ID分别是`0x100`、`0x200`、`0x201`、`0x300`、`0x400`、`0x500`、`0x600`），除了HRH1对应2个之外，其他HRH都只接收一个PDU。CAN IF过滤后，体现在通信服务层中的PDUR和COM模块的IPDU，也是共7个。为了体现出信号的概念，在IPDU1中定义了3个信号，也就是说这一CAN数据帧中包含了3个信号——空气悬挂压力、左侧气囊压力、制动开关。具体见下图。

  ![CAN接收信号定义](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/Receive_IPDUs.png)

  发送过程中，在COM模块中共设置了6个IPDU，和HTH一一对应，ID分别为`0x10`、`0x11`、`0x12`、`0x13`、`0x14`、`0x15`。为了体现出信号的概念，在IPDU7中定义了3个阀的6个PWM信号（每个阀有2个PWM控制），其余的IPDU都只包含1个信号。具体见下图。

  ![CAN发送信号定义](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/Transmit_IPDUs.png)

## 3.2 CAN数据接收

### 3.2.1 CAN Driver到CAN IF

我们以IPDU1为例，也就是ID为`0x200`的CAN数据帧。IPDU1对应的HRH1有2个过滤规则，除了IPDU1的`0x200`，还有IPDU2的`0x201`。

CAN控制器接收到CAN数据后，会进入中断，对于并根据硬件滤波器设置的数值可以对应到HRH1，也就是`0x2XX`。CAN中断会调用CAN IF模块接口`CanIf_RxIndication( Can_HwHandleType Hrh, Can_IdType CanId, uint8 CanDlc, const uint8 *CanSduPtr ) @ CanIf.c`。Hrh参数可以**索引**到CAN IF模块中对应的HRH过滤规则集合（在本例程中为结构题`CanIf_RxPduConfigType HrhRxPdu_CanIfHrhCfg1 @ CanIf_PBCfg.c`，HRH1共有2个过滤规则）。

`CanIf_RxIndication`函数内首先进行CAN IF模块设置的过滤规则搜索。过滤规则分为两种方式——`binarySearch`（二分查找）、`linearSearch`（线性查找）。从字面上就可以理解为，`binarySearch`使用二分查找的方法找到过滤规则集合中的某一个规则，而`linearSearch`使用线性查找的方法找到过滤规则集合中的某一个规则。需要注意的是，使用`binarySearch`时，HRH过滤规则集合中的过滤规则必须是从小到大排列。由于单个HRH的过滤规则很少，我们本次使用线性查找方法。

CAN数据帧的ID是`0x200`，`linearSearch`可以搜索到对应的`0x200`过滤规则。

![CAN Driver 到 CAN IF](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/CanDriver2CanIf.png)

### 3.2.2 CAN IF到PDUR

上一步找到了CAN数据帧对应的过滤规则，在过滤过则中，还会定义下一个模块是什么（通过定义回调函数编号实现）、对应下一个模块中的PDU编号是什么、CAN数据帧长度、标准帧还是拓展帧等。

根据上述定义的回调函数编号`PDUR_CALLOUT(=3)`，搜索到CAN IF模块的`CanIfUserRxIndications @ CanIf_Cfg.c`第3个接口，也就是PDUR模块的面向CAN IF模块的接口`PduR_CanIfRxIndication(PduIdType pduId, PduInfoType* pduInfoPtr) @ PduR_CanIf.c`。`pduId`可以搜索到PDUR模块中的某一条路径。对于ID为`0x200`的CAN数据帧来说，对应了PDUR模块的Path1。

![CAN IF到PDUR](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/CANIF2PDUR.png)

### 3.2.3 PDUR到COM

上一步找到了CAN数据帧对应的PDUR模块中的Path1，其中定义了源模块（此处是CANIF）、源PDU ID（此处是CAN IF模块中规则的编号）、PDUR的目的PDU定义（包括目的模块、目的PDU ID等）。

上一小节提到的`PduR_CanIfRxIndication`函数最终会调用PDUR模块的`PduR_ARC_RxIndication(PduIdType PduId, const PduInfoType* PduInfo, uint8 serviceId) @ PduR_Logic.c`。该函数根据Path1定义的目的模块（本例程是COM模块）找到了下一个模块COM的接口`Com_RxIndication(PduIdType RxPduId, PduInfoType* PduInfoPtr) @ Com_Com.c`。`Com_RxIndication`会将CAN数据帧拷贝到缓存区中，也就是**简单系统——CAN控制PWM**的`IPdu_Rx`缓存区。

COM模块会对IPDU进行管理。比如对于IPDU1，有3个信号空气悬挂压力、左侧气囊压力和制动开关。那么在COM中，对应该IPDU会设置3个信号配置信息，如下图所示。

![PDUR到COM](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/PDUR2COM.png)

具体而言，每个信号定义了信号ID、所在IPDU的ID、初始化数值、在IPDU中的起始位置、信号长度、数据类型等。上层的SWC可以直接通过信号ID来索引拿到对应的信号数据。

![信号具体定义](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/Signal_Define_IPDU1.png)

*COM的IPDU配置和信号配置*

COM的配置文件中配置了`ComIPdu`（IPDU的配置信息）和`ComSignal`（信号的配置信息），一个IPDU可以包含1个或者多个信号。而COM还会维护一个`Com_Arc_Config`，为每个IPDU和Signal维护`Arc_IPdu`和`Arc_Signal`，`Arc_IPdu`中存放了IPDU缓存区、Deferred缓存区、发送IPDU定时器等信息。COM工作的时候，会以`Arc_IPdu`来管理对应的IPDU。

### 3.2.4 BSW任务和COM

CAN中断将数据放到COM的缓存区`IPdu_Rx`后，可以再设置一个对应的缓存区，专门用于存放`IPdu_Rx`中的数据，我们命名其为`Deferred_IPdu`。**设置`Deferred_IPdu的原因：`**由于中断到来的时间不确定，所以上层从IPDU缓存区`IPdu_Rx`获取数据的时候，必须将中断关闭（进入临界区）。如果上层太多次直接从`IPdu_Rx`获取数据，就会造成对中断极大的影响，所以需要一个任务统一将`IPdu_Rx`数据先拷贝到`Deferred_IPdu`（此时需要进入临界区），供大家一块使用。

上述统一将`IPdu_Rx`数据先拷贝到`Deferred_IPdu`的任务是`OsBswTask @ BSWMainFunctionTask.c`，如下图所示。

![BSW任务将数据拷贝到Deferred IPDU](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/COM_Storage_BSW_Task.png)

本项目将IPDU的单个缓存区都设置成了8个字节，那么对于接收而言，共7个IPDU，所以共`7 * 8 * 2 = 112个字节`。公式中的`2`表示一个 IPDU包括`IPdu_Rx`和`Deferred_IPdu`一对；对于发送而言，共6个IPDU，所以共`6 * 8 = 48个字节`，总发送和接收共160个字节。

具体过程是，COM接收到PDUR分发的数据存放在`IPdu_Rx`缓存区中，然后BSW任务将`IPdu_Rx`缓存区中的数据拷贝到`Deferred_IPdu`缓存区（此时需要进入临界区，不能被CAN中断打断，否则读取数据可能不对），而SWC取`Deferred_IPdu`缓存区中的数据不需要进入临界区。

### 3.2.5 SWC任务和COM

SWC应用目前都放在RTE任务里，周期性执行。某一些SWC需要获取CAN数据，具体是通过函数`Com_ReceiveSignal(Com_SignalIdType SignalId, void* SignalDataPtr) @ Com_Com.c`实现的。第一个参数`SignalId`是信号编号，即在**CAN控制器配置和信号定义**已经定义，第二个参数`SignalDataPtr`是数据读出来后存放的位置。比如需要获取制动开关信号，则调用函数`Com_ReceiveSignal(ComConf_ComSignal_BrakeStatus_RX, &brakestatus)`。

![IPDU可以包含多个信号](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/Singal_Example.png)

## 3.3 CAN数据发送

CAN数据发送的配置和接收类似，下面进行简略说明。

### 3.3.1 SWC任务和COM

SWC任务会将数据写入到对应IPDU的信号区域。以HTH1的诊断状态为例。具体通过函数`Com_SendSignal(Com_SignalIdType SignalId, const void *SignalDataPtr) @ Com_Com.c`实现的。第一个参数`SignalId`是信号编号，即在**CAN控制器配置和信号定义已经定义**，`SignalDataPtr`是数据源。

![诊断状态信号的定义](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/Diagnosis_Status.png)

调用该函数后，会将数据拷贝到对应的IPDU缓存区。

![拷贝到缓存区](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/Copy2IPduTx.png)

### 3.3.2 BSW任务和COM

BSW任务周期性调用`Com_MainFunctionTx @ Com_Sched.c`。**BSW周期发送数据的流程**：`Com_MainFunctionTx`函数一次就会将每个发送IPDU计数器减小1，在COM模块中配置了不同发送IPDU的发送周期`ComTxModeTimePeriodFactor`（计数器初始值），如果计数器等于0则发送数据。需要注意的是，还可以设置最小的发送延时`ComTxIPduMinimumDelayFactor`。下图仅作为示意图，和multican工程无直接关系。

![BSW任务发送数据](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/BSW2COM2PDUR.png)

### 3.3.3 COM到PDUR

COM的配置信息中配置了发送IPDU的管理信息，比如IPDU发送周期（该周期是调用`Com_MainFunctionTx`函数的次数，如配置为10，则调用函数10次后发送数据）、IPDU数据长度、接收或者发送等，具体见代码。

![COM到PDUR](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/COM2PDUR.png)

### 3.3.4 PDUR到CAN IF

PDUR模块在发送的时候也作为一个分发的角色，每个IPDU都有对应的Path，进而索引到对应CAN IF模块中配置的HTH和对应CAN数据帧ID等信息。

![PDUR到CANIF再到CANDriver](https://neyzoter.cn/images/posts/2019-12-30-AUTOSAR-CAN-Com-Related-Conf/PDUR2CANIF2CANDriver.png)

### 3.3.5 CAN IF到CAN Driver

CAN IF模块中包含每个发送IPDU的配置信息，比如CAN数据帧ID、标准帧或者拓展帧等。CAN IF模块会调用`Can_Write`来将数据发送出去。