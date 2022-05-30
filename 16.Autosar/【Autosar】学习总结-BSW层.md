- [【Autosar】学习总结-BSW层_David 's blog的博客-CSDN博客_bsw模块](https://blog.csdn.net/zDavid_2018/article/details/121806288)

# 一、简介

**AUTOSAR** – **AUT**omotive **O**pen **S**ystems **AR**chitecture，汽车开放系统[架构](https://so.csdn.net/so/search?q=架构&spm=1001.2101.3001.7020)。

 ![img](https://img-blog.csdnimg.cn/eb314432746241d0b9f871f3439ce7a5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBARGF2aWQgJ3MgYmxvZ3M=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 1.优势：

①有利于提高软件复用度，尤其是跨平台的复用度；

②便于软件的交换与更新；

③软件功能可以进行先期架构级别的定义和验证，从而能减少开发错误；

④减少手工代码量，减轻测试验证负担，提高软件质量；

⑤使用一种标准化的数据交换格式，方便各公司之间的合作交流等。

## 2.规范：

AUTOSAR规范主要包括：分层架构、方法论和应用接口三部分内容。

AUTOSAR 提供了指定在 ECU 上集成软件组件所需的所有方面的方法，以及将不同的 ECU 集成到各种不同总线系统上的整个网络通信的方法。该方法定义了活动对工作产品的依赖性，预计将支持AUTOSAR中工具的活动，描述和使用。

描述 （.arxml） 基于 AUTOSAR 模板，这些模板定义了正式交换格式（AUTOSAR Schema）以及与交换格式一起使用的语义约束。描述用于保存在 AUTOSAR 方法中生成或使用的信息。各种生成器可以利用描述中的信息来支持 RTE 和 AUTOSAR 基本软件（包括操作系统）的配置和生成。

# 二、AutoSar架构分层架构

## 1.各ECU之间通信

![img](https://img-blog.csdnimg.cn/8cbf7e23f5604adfbb51ec2823115006.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBARGF2aWQgJ3MgYmxvZ3M=,size_18,color_FFFFFF,t_70,g_se,x_16)



## 2.总体架构：4层

![img](https://img-blog.csdnimg.cn/f89e7b85a7b04ff9ae5143a11d28c2eb.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBARGF2aWQgJ3MgYmxvZ3M=,size_12,color_FFFFFF,t_70,g_se,x_16)

![img](https://img-blog.csdnimg.cn/fdd1fd2fc31a4892ba787737a84e6cdd.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBARGF2aWQgJ3MgYmxvZ3M=,size_20,color_FFFFFF,t_70,g_se,x_16)每一层只能使用下一层所提供的接口，并向上一层提供相应的接口。 

## 3.各层简述：

（1）应用软件层（Application Software Layer，ASW）：包含若干个软件组件（Software Component，SWC），软件组件间通过端口（Port）进行交互。每个软件组件可以包含一个或者多个运行实体（Runnable Entity，RE），运行实体中封装了相关控制算法，其可由RTE事件（RTE Event）触发。

（2）运行时环境（Runtime Environment，RTE）：RTE封装了基础软件层的通信和服务，为应用层软件组件提供了标准化的基础软件和通信接口。

（3）基础软件层（Basic Software Layer，BSW）: 又可以分三层

![img](https://img-blog.csdnimg.cn/87bc25cae1d74e66aa879ce61c2aa8bd.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBARGF2aWQgJ3MgYmxvZ3M=,size_18,color_FFFFFF,t_70,g_se,x_16)



（4）硬件层（HardWare）：MCU芯片

# 三、MCAL

## 1.简述

Mcal是BSW层中的最下层，这里单独拿出来讲；

在我理解，这一层的代码直接与硬件打交道，就像是单片机中的HAL库或者标准固件库+BSP板级支持包，这部分代码可以直接驱动芯片引脚以及片内资源。

配置Mcal层所使用到的EB-Tresos，就类似于STM32CubeMX工具，用图形化界面的工具配置管脚（GPIO DIO PWM ADC SPI...等等）、时钟、分频等等....

![img](https://img-blog.csdnimg.cn/a8878347226d4519a554f8935bdf55b3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBARGF2aWQgJ3MgYmxvZ3M=,size_20,color_FFFFFF,t_70,g_se,x_16)

如图：EB-Tresos配置MCAL:

![img](https://img-blog.csdnimg.cn/692aff88dac34e368ccc86c5f637275c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBARGF2aWQgJ3MgYmxvZ3M=,size_19,color_FFFFFF,t_70,g_se,x_16)



### 2.各驱动模块

PORT：对单片机各引脚属性的配置；MCU每个引脚都是一个port，对port引脚的方向（输入或输出）、运行期间引脚方向的可变性、引脚的工作模式、运行期间引脚工作模式的可变性、引脚的初始值、内部上拉的激活等进行配置。

DIO：digital i/o ，即单片机中GPIO；AUTOSAR中，将一个单片机数字I/O引脚（Pin）定义为DIO通道（Dio channel），可把若干个DIO通道通过硬件分组成为一个DIO端口（DIO Port），DIO端口中相邻几个DIO通道的逻辑组合则称为DIO通道组（DIO Channel Group）。

Dio模块中涉及的DIO Channel，即单片机引脚（Pin），用之前，必须在PORT模块中配置引脚属性为GPIO。

ADC：Analog-to-Digital Converter Driver 模/数转换单元。

PWM：pluse width modulation 脉宽调制；可产生占空比和周期都可改变的脉冲；应用场景：调节灯光亮度 调节电机转速等...

ICU：输入捕获 input capture unit
OCU：输出比较

Ethernet：以太网

FlexRay：没见过 应该不重要
CAN：Can通信驱动
LIN：总线相关接口
SPI：一般就是用来读写存储器

EepROM：外部存储器
Flash： 内部 外部 
RAM：NMRAM ： 一个类型的东西

Core：
MCU：（Microcontroller Unit Driver 提供微控制器的初始化、复位、休眠等功能；使能MCU时钟；设置MCU时钟相关的参数（：CPU时钟、锁相环（PLL） 、外设时钟、预分频器等）；进入低功耗模式

WatchDog：看门狗
GPT：（（General Purpose Timer Driver） 通用定时器，硬件定时器；提供启动和停止硬件定时器、得到定时器数值、控制时间触发的中断、控制时间触发的中断唤醒等功能。

【MCAL层的各驱动看这篇博客，写的很详细】

[图解AUTOSAR（五）——微控制器抽象层（MCAL）_肥嘟嘟的左卫门-CSDN博客_autosar mcal](https://blog.csdn.net/ChenGuiGan/article/details/80310713)

# 四、BSW

## 1.BSW层的内部分层架构

![img](https://img-blog.csdnimg.cn/3b11090ca8904a1492e1e337b591079f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBARGF2aWQgJ3MgYmxvZ3M=,size_20,color_FFFFFF,t_70,g_se,x_16)

 AUTOSAR BSW：提供基础软件服务，包括标准化的系统功能以及功能接口，并且由一系列的基础服务软件组件构成，包括系统服务、内存服务、通信服务等。

（1）BSW-服务层 ：OS系统服务、存储器服务，通信服务 （像Java程序的服务，应用层调用服务层的服务接口完成上层业务逻辑，不需要关心下面怎么是实现）。

（2）BSW-ECU抽象层：看门狗抽象，存储器硬件抽象，通信硬件抽象，io硬件抽象。

看ECU抽象层的位置 上层是服务 下层是硬件驱动，所以这一层的目的就是：使上层软件与ECU硬件设计无关。

（3）MCAL驱动层：微控制器驱动，存储器驱动，通信驱动。

```objectivec
同时，基础软件层模块按照类型可以分为驱动模块、接口模块、处理模块以及管理器。
1、驱动模块驱动模块包含了控制和使用内部或者外部器件的功能，分为内部驱动和外部驱动。
（1）内部驱动内部器件位于微控制器（单片机）的内部，例如内部EEPROM、内部CAN控制器、内部ADC模块等。内部驱动程序就是针对单片机内部器件资源的驱动程序，这部分驱动程序属于微控制器抽象层（MCAL）。
（2）外部驱动外部器件是指单片机外部的ECU硬件，比如外部EEPROM、外部看门狗、外部Flash等。外部驱动程序就是针对单片机外部硬件资源的驱动程序，属于ECU抽象层。外部驱动程序需要通过微控制器抽象层（MCAL）驱动程序来实现对外部器件的驱动。这种方法下AUTOSAR也支持嵌入在系统基础芯片（SBCs）中的组件，像收发器以及看门狗等。例如，使用SPI通信接口的外部EEPROM驱动程序是通过SPI总线处理程序来驱动外部EEPROM的。但是有一种例外，对于和内存映射相关的外部器件（如外部Flash存储器），其驱动程序是可以直接对微控制器进行存取访问的，所以这部分驱动程序属于微控制器抽象层（MCAL）。
 
2、接口模块包含了对其次级模块进行抽象的功能，比如对一个特定功能的硬件进行抽象。它提供一个通用的接口函数（API）来访问一种特定的器件类型，且与该类型器件的数目无关，同时也与器件的具体硬件实现无关。接口模块不会改变数据的内容。一般来说，接口属于ECU抽象层。例如，CAN通信系统的接口模块提供一个通用的接口函数来访问CAN通信网络，并且与ECU上CAN控制器的数目以及硬件实现无关。
 
3、处理模块是一个专用的接口，它控制一个或多个客户端对一个或多个驱动程序进行并行、多重以及异步地访问。也就是说，它起着缓冲、队列、仲裁以及多路复用的功能。同时，处理程序也不会改变数据本身的内容。处理模块通常会并入驱动程序或是接口模块中（如SPIHandlerDriver、ADC Driver等）。
 
4、管理器为多重的客户端提供特定的服务。当单纯的处理程序不能满足对多重的客户端进行抽象时，就需要用到管理器来进行处理。除了处理功能外，管理器还可以对数据内容进行评估、改变或是适应数据内容。一般而言，管理器属于服务层。例如，非易失性随机存储器（NVRAM）的管理器负责对内部或是外部存储设备进行并行的访问，如Flash、EEPROM存储器等。同时，它也可以完成分布式并且可靠的数据存储、数据校验以及默认值的规定等。
```

总结下来就是：BSW主要提供4个服务：通信 存储 系统 复杂驱动

## 2.通信服务

### 2.1简述

通信服务（Communication Services）：包括CAN、LIN、FlexRay在内的整车网络系统、ECU网络及软件组件内的访问进行了统一封装，模块则通过通信硬件抽象层进行通信：

（1）对上层的应用软件层隐藏了协议以及报文属性

（2）提供了统一的总线通信接口供应用软件层调用

（3）提供了统一的网络管理服务

（4）提供了统一的诊断通信接口

### 2.2这里用Can通信举例

![img](https://img-blog.csdnimg.cn/b31a8f621dff4ff495f421276b9d80a7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBARGF2aWQgJ3MgYmxvZ3M=,size_14,color_FFFFFF,t_70,g_se,x_16)

 如图所示：完成Can通信的整个过程，BSW主要配置5个模块：Com、PduR、CanTp、CanIf、Can。

1.Com模块介绍：

```css
①运行在RTE-PduR之间,将信号装载到I-PDU中发送，从接收到的I-PDU中解析出信号；
 
②提供信号路由功能，将接收到的I-PDU中的信号打包到发送I- PDU中；
 
③通信发送控制（启动/停止I-PDU组）；
 
④发送请求的应答等.
```

2.上图的数据流程：

```objectivec
BSW调度器周期性调用Com模块的Com_MainFunction_Tx函数，Com模块将从其缓存器中读取需发送的数据；
 
Com模块的Com_MainFunction_Tx函数将调用PduR模块的PduR_ComTransmit函数，将数据传给PduR模块；
 
CanTp分情况 ，有的需要经过 有的不经过。
 
PduR模块路由到CAN Interface模块，调用CanIf_Transmit函数，这样数据从PduR模块传给了下层的CAN Interface模块；
 
CAN Interface模块再调用Can Driver模块的Can_Write函数，将数据写入相应的寄存器；
与CAN接收功能一样，Can_Write函数将访问仲裁，数据长度和数据寄存器，将数据写入。
```

3.配置通信模块Can过程

```objectivec
Com :目的是在上层RTE和下层PDU路由器之间建立系统通信，而不考虑通信协议.
Com模块获取应用层的信号（Signal），经一定处理封装为I-PDU（Interaction Layer Protocol Data Unit）发送到PduR模块
	信号I-DPU可以包含一个或多个信号，可以理解为一个I-PDU为一帧CAN消息，信号就是dbc中定义的。（DBC就是can信号的描述文件）
	如果需要将多个信号发送到同一I-PDU，则信号可以进一步形成信号组。COM模块包含两个主要的部分，分别为ComGeneral和ComConfig
 
ComGeneral
	ComConfigurationUseDet：如果此布尔参数设置为ON，则任何当COM模块出现错误时，会调用Det_ReportError函数，记录在DET模块中。
	ComCancellationSupport：这是一个布尔参数，用于启用/禁用用于取消PDU传输请求的取消功能。 
	ComEnableSignalGroupArrayApi：这是一个布尔参数，用于激活/禁用信号组阵列访问API。 
	ComSupportedIPduGroups：它是一个整数参数，用于说明所支持的IPDU组的最大数量。 
	ComVersionInfoApi：布尔参数，用于激活/禁用版本信息API Com_GetVersionInfo。 
	ComRetryFailedTransmitRequests：如果此参数设置为true，则启用重试失败的传输请求。 
	ComEnableMDTForCyclicTransmission：如果启用此选项，则它将在I-PDU的循环传输和重复传输之间提供最小延迟时间监视	
 
ComConfig:它包含四个容器，分别为ComSignals、ComIPdus、ComIPduGroups、ComSignalGroups。
	ComIPdus:该容器用于为不同的IPDU参数提供定义，如果没有该参数，则无法通过COM模块进行通信。ComIPdu也通过ComPduIdRef链接到PDU。I-PDU包含一个或多个信号和/或信号组
		ComIPduHandleId：这是分配为该IPDU的ID的数字值。此ID用于在各种发送和接收API调用以及相应的回调API中引用此IPDU。
		ComPduIdRef：它提供对COM栈的全局PDU结构的引用。
		ComIPduGroupRef：它是指IPDU所属的IPDU组。
		ComIPduSignalRef：提供对该IPDU中包含的所有信号的引用。一个IPDU可以包含一个或多个单独的信号。
		ComIPduSignalGroupRef：提供对此IPDU中包含的所有信号组的引用
		ComIPduCallout：此参数可定义相应I-PDU的callout函数的名称，该函数在接收IPDU时或在发送PDU之前调用。
		ComIPduDirection：定义I-PDU是为发送（SEND）PDU还是为接收（RECEIVE）PDU。
		ComIPduSignalProcessing: 用于配置信号是立即处理还是周期性处理，分别对应immediate或DEFERRED模式。如果将ComIPduDirection设置为SEND，则需要设置其他参数，例如传输是周期的还是混合型的等等。这是通过添加ComTxIPdu对象来添加的。
	
	ComIpduGroups:它包含COM模块的IPDU组的配置参数。如果不包含ComIPduGroup容器，则未定义IPDU组。在这种情况下，无法通过COM模块进行通信
		ComIPduGroupHandleId：用作此IPDU组ID的数值。API调用需要它来启动和停止IPDU组。
		ComIPduGroupGroupRef：它提供对包括该IPDU组的所有IPDU组的引用
	
	ComSignals : IPDU可以由一个或多个信号组成。来自RTE不同应用程序的这些信号在被传输到PduR之前被打包到PDU中。该容器提供各种参数来配置PDU中的信号位位置，信号位大小和其他属性。
			ComHandleId：这是分配给每个信号ID的数字值。与信号操作有关的不同API调用需要它。
			ComTimeoutFactor：它定义了监视的超时时间。
			ComTransferProperty：以下选项定义此信号是否可以触发相应IPDU的传输；可以设置以下五个选项之一：
				TRIGGERED
				PENDING
				TRIGGERED_ON_CHANGE，
				TRIGGERED_WITHOUT_REPETITION TRIGGERED_ON_CHANGE_WITHOUT_REPETITION
			ComBitPosition：指出信号在IPDU中的开始位置，
			ComBitSize：它定义信号的大小（以位为单位）。
			ComSignalEndianess：定义信号网络表示的字节排序。可以是BIG_ENDIAN，LITTLE_ENDIAN，OPAQUE。
			ComSignalInitValue：用于设置信号的初始值。
			ComSignalLength：它指定UINT8 [n]类型的n（以字节为单位：1 ... 8）。对于其他类型，它将被忽略。
			ComSignalType：它指定符合BOOTEAN，SINT8，UINT8等。
			ComTimeoutNotification：定义发生超时时在发送方或接收方要调用的函数的名称
	
配置完Com模块 ，下面数据就PduR模块处理：
 
PduR：负责将PDU路由到特定的总线接口。在PduR之上的所有层中，所有PDU都是独立于协议的。在PduR之下的所有PDU都属于特定的协议接口模块
PduRBswModules：每个容器都描述了PDU路由器必须连接的特定BSW模块 
	CanIf
	CamNm
	CanTp
	Com
	Dcm
PduRGeneral :这是PduR模块的子容器，它指定PDU路由器的常规配置参数
	PduRDevErrorDetect：如果启用，它将默认错误跟踪器（Det）检测和通知打开。
	PduRVersionInfoApi：如果设置为true，则PduR_GetVersionInfo API可用.
PduRRoutingTables: 它表示路由路径表。此路由表允许使用多个配置，这些配置用于在同一配置中创建多个路由表
	PduRRontingTable
		PduRRoutingPaths: 路由路径为源 PDU（Source PDU）到目标PDU（Destination PDU）的描述，它们都需 要通过引用前述EcuC中定义的全局PDU来进行关联
 
PduR转发数据到下面的子模块 ，例如到CanIf模块
 
CanIf：是访问CAN总线的标准接口。抽象Can控制器，提供向上的接口，这样上层就不用关心Can控制器是片上资源还是片外。
	功能：1.完成对CanIf和控制器中全局变量及配置缓冲区的初始化；
		  2.发送请求服务，提供供上层应用在CAN网络上发送PDU的接口；
		  3.发送确认服务，发送成功后通知上层，或者发送取消确认后存于 发送缓存；
		  4.接收指示服务，成功接收PDU后通知上层。
		  5.CanIf主要配置Hth（Hardware transmit handle）和 Hrh（Hardware receive handle），Hrh Hth需要引入CanHardwareObject，CanHardwareObject是对CAN邮箱 （MailBox，MB）的抽象，在后续MCAL配置中会进行讲解
		  6.每个PDU需要引用一个Hth或者Hrh，即 完成PDU向MB的分配。
CanIfCtrlDrvCfg ： 它提供了基础CAN驱动程序模块的配置参数。一个CanIfDrvCfg引用一个Can Driver模块。
CanIfInitCfg ： 它包含CanIf的所有初始化参数。此容器至少有一个实例。它定义了所有与PDU相关的配置
	CanIfBufferCfg：CanIfInitCfg的子容器。此容器包含传输缓冲区配置。必须为将用于传输帧的每个CanController添加此容器的一个实例
	CanIfTxPduCfg ： 它是CanIfInitCfg的子容器。它包含发送CAN L-PDU的配置参数。每次需要发送CAN L-PDU时都要对其进行配置
 
CanIf模块发送数据到具体的某个Can控制器
 
Can ：MCAL层中的Can驱动
CanGeneral : Can模块整体功能的配置
	Can Change Baudrate Api：改变CAN波特率API使能。 
	Development Error Detection：Can模块开发错误检测使能。 
	Can Driver Index：CAN驱动Id号。 
	Can Main Function Busoff Period： Can_Main Function_Bus OFF（）函数调用周期。 
	Can MainFunction Wakeup Period： Can_MainFunction_Wakeup（）函数调用周期。 
	Can Main Function Mode Period：Can_MainFunction_Mode（）函 数调用周期。 
	Can Multiplexed Transmission：双路复用功能的传输使能。 
	Can Identical Id Cancellation：取消挂起的发送报文使能。 
	Can Extended Id Support：支持扩展Id使能。 
	Message buffer data size：CAN MailBox装载数据长度等。
 
CanConfigSet
	CanController：CAN控制器属性配置 :芯片上有几路Can 使用到几路Can
		Can Controller Activation：CAN控制器使能。 
		Can Controller Id：CAN控制器Id号
		Can Rx Processing Type：接收数据的处理方式，轮询（POLLING）或中断（INTERRUPT）。 
		Can Tx Processing Type：发送数据的处理方式，轮询（POLLING）或中断（INTERRUPT）。 
		Can BusOff Processing Type：CAN BusOff事件处理方式，轮询 （POLLING）或中断（INTERRUPT）。 
		Can Wakeup Processing Type：CAN Wakeup事件处理方式，轮询 （POLLING）或中断（INTERRUPT）。 
		Can Controller Default Baudrate：CAN控制器默认的波特率配置。 
		Can CPU Reference Clock：CAN模块引用的时钟，即Mcu模块中 配置的McuClockReferencePoint_CAN_CLK。 
		CanRxFifoWarningNotification：RxFifo Warning通知函数。
		CanRxFifoOverflowNotification：RxFifo Overflow通知函数。 
		Can Error Notification Enable：Can Error通知函数等
		CanControllerBaudrateConfig ： CanController的波特率配置
			Can Time Segments Checking：CAN时间段检测使能。 
			②Can Automatic Time Segments Calculation：自动时间段计算使 能，若使能，则CanControllerPropSeg、CanControllerSeg1、 CanControllerSeg2、CanControllerSyncJumpWidth将禁用。 
			③Can Controller Prescaller：CAN控制器时钟分频。 
			④Can Controller BaudRate Config Id：CAN控制器波特率配置Id 号，被SetBaudrate API使用。 
			⑤Can Controller BaudRate：设置CAN控制器的波特率，本文示例 使用100（Kbps）。 
			⑥Can Synchronization Segment：同步段的时间。
			⑦Can Propagation Segment：传播段的时间。 
			⑧Can Phase Segment 1：采样点前的时间段。 
			⑨Can Phase Segment 2：采样点后的时间段。 
			⑩Can Resynch Jump Width：同步跳跃宽度 （Synchronization Jump Width），用于重同步的时间。
		CanFilterMask：滤波器掩码，
 
	CanHardwareObject：为CAN MailBox（MB）的抽象，MainBox来做报文的收发。 一个CanHardwareObject即一个can报文
		Can Implementation Type：FULL CAN（一个MB只能发送或者接 收一帧CAN报文）；BASIC CAN（一个MB可以发送或者接收多帧CAN 报文）。
		②Can Id Message Type：CAN Id的类型，标准帧（Standard Identifier-11 bits）、扩展帧（Extended Identifier-29 bits）与 混合模式（Mixed Mode）。 
		③CanIdValue（Message Id）：结合CanFilterMask，定义CAN报文 接收Id范围。 
		④Can Object Id（MB Handle）：MB的Id号。 
		⑤Can MB Type：MB类型，接收（RECEIVE）或者发送 （TRANSMIT）。 
		⑥Can Controller Reference：CAN控制器引用，本书示例涉及一个 CAN控制器。 
		⑦Can Filter Mask Reference：引用滤波器掩码
```

## 3.存储

![img](https://img-blog.csdnimg.cn/f05fae0ad8d648e2b8c0fd85a49888ed.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBARGF2aWQgJ3MgYmxvZ3M=,size_20,color_FFFFFF,t_70,g_se,x_16)

NVM：NVRAM Manager

MEMIF：Memory Abstraction Interface

FEE：Flash EEPROM Emulation

EA：EEPROMAbstraction

FLS：Flash Driver

EEP：EEPROM Driver

## 4.OS

![img](https://img-blog.csdnimg.cn/93e83c48dc7e4aada275d5df61c4cc65.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBARGF2aWQgJ3MgYmxvZ3M=,size_12,color_FFFFFF,t_70,g_se,x_16)



可以理解为单片机中的STM32CubeMX配置FreeRTOS。

Ventor DavinCi Configuration Pro中的Os配置如下：

```sql
OsAlarms    
Os中的定时器
OsApplications Os应用 支持多核芯
OsAppModes  
OsBarriers
OsCores Os配置多核或者单核
OsCounters 计数器
OsEvents 事件
Oslsrs   中断
OsResources 
OsScheduleTables
OsSpinLocks  锁
OsTasks  任务Task
OsOs
OsPublishedInformation
 
介绍:
Alarms：警报器，就像我们的上课铃声，到时间就会响。操作系统用它来做一些定时的事，比如激活一个任务Task。
 
Applications：从字面意思理解，它是一个应用，准确地说，它应该是一个分区。它部署到某个Core上，主要的作用就是管理放到其中的对象。为什么要这个东西呢？举个不恰当的例子。MCU像一个国家，核就是它的省，那么Applications就可以理解成省管辖下的地级市。比如，某一天一个地级市里发现了大量新冠病毒感染者，为了防止扩散，将该市封闭处理，以免扩张到其他市区。想想看，当这个市里出现疫情时，是封闭整个省好呢，还是仅封闭该市好呢？显然是后者。芯片里的划区也是非常合理的。AUTOSAR OS中分区分为可信的和非可信的。
 
Application modes：用的极少，这里不展开介绍；
 
Counters：把Counter比作心脏比较合适，对于芯片来说，它就是晶振。在操作系统它的作用就是计时或者计数，一般Counter与芯片的Timer结合起来，Counter的精度决定了操作系统能计时的准确度。
 
Events：事件。在嵌入式操作系统中，事件一般是和任务绑定一起来实现调度功能的，当然也可以由Alarms来触发。比如，我通过设定某个任务在10ms进程执行，10ms的任务就和该事件一起来实现。
 
ISRs：interrupt service routine，就是我们讲的中断。中断的概念对于嵌入式开发的同学而言，应该都比较熟悉。在AUTOSAR OS中中断有两种类型，Autosar OS中将中断分为Cat1 和Cat2，即所谓的1类中断和2类中断，所谓的2类中断其实就是完全被OS接管的中断，这类中断的上下文切换，堆栈管理全部由OS管理；而1类中断则不被OS接管，因此它的上下文由自己管理。另外Autosar Os中要求Cat1的中断的最低优先级高于Cat2的最高优先级，也就是说Cat1的中断优先级更高。所以Cat1的中断一般用于时间要求更紧急的场合。要知道，中断有比任何任务都高的优先级，即中断可以抢占任务。
 
Register Sets：几乎没有使用，暂时不介绍。
 
Resources：资源。嵌入式系统内部的资源是用来强制任务分组运行的，在分组内部，是没有抢占一说的，即共享资源。当然，中断就不能使用内部分组资源了。共享什么资源呢？比如栈的共用，为了降低系统的负荷，我们可以让同分组的任务共用分配的栈资源，你用完我用，像和谐社会一般。
 
Schedule tables：调度表，可以将其理解为包含了很多调度点的表，Autosar Os中一般这么用调度表，比如有 1ms，2ms，5ms 三个周期需要调度的任务，那么会根据公约数，生成一个表，这个表在1ms 处调度1ms任务，2ms处调度1ms和2ms任务，4ms处调度1ms和2ms任务，5ms处调度1ms和5ms任务，10ms处调度1ms，2ms，5ms任务，然后按照这样的关系循环，这就是所谓的调度表。目前有部分主流的Autosar开发商使用这种方式进行任务的调度。淡然，Schedule Table有自己的状态机，Schedule Table调度方式最大的好处在于保持调度的同步性。
 
Spinlocks：没有项目使用，暂不介绍。
 
Tasks：任务应该也比价熟悉，Autosar Os有些自己的性质，简单介绍下。Task 类型：
 
Basic Task：包含状态Ready，Running，Suspend
Extend Task：包含状态Ready，Running，Suspend和Waiting
所谓的扩展task，就是多了一个Waiting状态，因此它一般就是等待一个Event的到来。
 
此外Task的调度分为抢占式和协作式，对于可抢占的Task，OS会根据Task的优先级进行排序调度，优先级高的可以抢占优先级低的。在AUTOSAT OS中数字越大优先级越高。
```

## 5.复杂驱动

这个好理解，相当于一些复杂的驱动不能适配到Autosar架构中，那就直接连接上层RTE和下层MCAL

# 五、RTE

RTE是为上层应用软件提供通信服务的层。 在RTE之上，软件架构风格从“layer”变为“component”. RTE封装好了下层如OESK、COM等通信层BSW后，为上层提供数据通信所需的RTE API，再使用端口或者Sender-Receiver通信和Client-Server通信的方式进行交互。

它的作用是对上层隐藏bsw层的细节，使上层的swc的实现不必依赖于某个特定的ecu。

# 六、SWC

略

# 七、开发工具

### 1.劳特巴赫 （Lauterbach ）：

用来调试

### 2.[vector](https://so.csdn.net/so/search?q=vector&spm=1001.2101.3001.7020) DaVinci Developer  

设计SWC的[图形界面](https://so.csdn.net/so/search?q=图形界面&spm=1001.2101.3001.7020)工具

### 3.vector DaVinci Configurator Pro

配置BSW和MCAL等；输出RTE、BSW代码。

## 4.ETAS

### 5. EB Tresos ：

配置MCAL 和 BWS模块。和ventor竞争关系，他俩能做一样的事。

### 6.CANoe

### 7.MATLAB/Simulink：

Simulink是Matlab最重要的组件之一，它提供了一个动态系统建模、仿真和综合分析的集成环境。在该环境中，无须大量编写程序，只需要通过简单直观的鼠标操作就可以构造出复杂的系统

# 八、参考学习资料

分层架构：

[Classic AutoSAR 简介-云社区-华为云 (huaweicloud.com)](https://bbs.huaweicloud.com/blogs/142759)

学习书籍：

《AUTOSAR规范与车用控制器软件开发.pdf》 这个本书是学习autosar入门书籍，讲的通俗易懂。我的资源中有上传：[AUTOSAR规范与车用控制器软件开发.rar_autosar规范与车用控制器软件开发-其它文档类资源-CSDN文库](https://download.csdn.net/download/zDavid_2018/62929828?spm=1001.2014.3001.5503)

厂商的培训资料：vector家的 EB家的 网上都能搜到

我的资源中有上传：[EB_AUTOSAR基础培训EB_AUTOSAR_Basic_Training_Slides_v2018.rar_ebautosar-嵌入式文档类资源-CSDN文库](https://download.csdn.net/download/zDavid_2018/62934562?spm=1001.2014.3001.5503)

# 九、感想总结

写一波感想吧，学习autosar几个月了，就有机会到全球最大电池供应商参与autosar架构的BMS系统配置与移植。用到了vector-DaVinci Configurator Pro配置了BSW的多个模块，用EB-Tresos配置了MCAL层。

# 十、项目经验

## 1.项目简述

基于AutoSar架构的BSM移植与调试

## 2.开发环境

EB-Tresos ：配置MCAL

Ventor DavinCi Configuration Pro : 配置BSW ，生成代码

Build工具

## 3.MCAL配置

略

## 4.BSW配置

```haskell
项目移植：英飞凌某芯片 -> 某国产芯片
移植流程：先配置BSW各模块（工具：Ventor Davinci Configurator Pro）；后修改编译路径文件、c文件、以及编译产生的报错（头文件缺少 或者未定义函数和变量）
 
一、Code移植 ：(放到最后做)
	1.各类库文件的改动：替换芯片的Mcal库 SDK库等
	2.inclide.txt 此文件存放编译所需要的头文件路径（需要删除TC275的路径 新增G9E的路径）  ；
	  build.txt文件的修改（此文件下存放各个模块c文件） 需要新增 删除
	3.驱动文件修改：CDD复杂驱动
	4.MCAL工程移植：EB配置的Mcal工程
	
二、BSW模块修改-Can配置 ： Davinci工具目前不支持G9E芯片的Can底层驱动（这一点类似Keil工程或者stm32CubeMX工程新建时选择芯片型号，G9E是新产品，目前不支持。）
	所以底层驱动需要在EB-Tresos中配置
	把MCAL的Can底层配置和Davinci中的CanIf以及上层配置衔接
	1.EB->MCAL->cam->CanHardwareObject中的配置性 和 Davinci Configurator -> Can的HardwareObject配置一致
	
三、BSW模块配置：NVM Ea/Eep模块配置 ：G9E内部没有Flash不能使用Fls、Fee做nvm存储 ，所以用外部Eeprom来做NVM存储
	1.选择添加模块：添加bsw模块到Basic_Editor界面下
	Project->Project_Settings->Modules->[Add] -> Select from software integration package ->next -> eep、 ea -> finish
		然后再Basic_Editor界面就可以看到添加的模块了
	2.配置Eep模块属性：EepGeneral : Block Size : 256
	3.配置EepInitConfiguration属性：
		Eep芯片型号25LC512，根据芯片进行读写配置
			Default Mode ： MEMIF_MODE_FAST
			Device Information Ref : 选择芯片Microchip_25LC512
			Fast Read Block Size : 128 
			Fast Write Block Size : 128
			Normal Read Block Size : 128 
			Normal Write Block Size : 128
			Size : 65536
			（...其他配置就不一一描述了）
	4.在Eep中添加Dem模块
		配置Eep的Dem Parameter，然后设置技术方式 ，需要配置4个Dem ： Eep_E_READ_FAILED等
		Dem -> DemConfitSet -> DevEventParameters -> Create DemEventParemeters Container （添加了一个诊断事件参数） -> 配置这个参数下的各个属性
			Short name: Eep_E_READ_FAILED等
			Event kind: DEM_EVENT_LIND_BSW
		配置触发条件：
			Eep_E_READ_FAILED -> DemEventClass -> DemDebounceAlgorithmClass -> DemDebounceCounterBased :
				OperationCycle Ref:IgnitionCycle 
		然后回到Eep模块把配置的Dem按照名字进行Mapping
			EEp ->EepInitConfiguration -> EepDemEventParamterRefs : 
				E READ FAILED :选择刚才在Dem莫夸添加的EEP_E_READ_FAILED
			
	5.Eep使用Spi通信，所以配置Spi模块
		Spi -> SpiDrivers_0 -> Create SpiChannel Container : 配置short name 、channel ID 、Max Length 、 Transfer Strat
 
	6.配置上层的Ea模块 ： External Abstraction(外部存储)
		因为用过NVM，所以需要关联两个NVM的Notification ： NVM_JobEndNotification 、NVM_JobErrorNotification
			Basic_Editor -> Ea -> EaGenenal : 配置上述两个属性的值
		新增两个Partition用来存储EolData和EbIData，配置完成后一共有三个：EaPartitionConfiguration_App EaPartitionConfiguration_EolData、 EaPartitionConfiguration_FblData
			Basic_Editor -> Ea -> EaPartitionConfiguration -> 右键 -> Create ... Container
			配置每个新增Partition设置新增的属性：
				Partition Device：
				Partition Size ： 61440
				Partition Start Address ： 0
		配置每个NVM的存储Block：需要和之前Fee模块中的Block保持一致
			Ea -> EaBlockConfiguration -> 右键 -> Create EaBlockConfiguration Container
				设置属性：
					short name:
					Block Size : 
					Device Index : 选择APP 或者 Eol 或者 Fbl
					...其余略
		
	7.配置NVM 和 MEMIf ：从Fls/Fee换到了Ea/Eep ,所以NVM 、Memory Interface这两层需要选择从FeeGeneral换到EaGeneral
		MemIf -> MemIfMemHwAs -> -> Reference to Fee/Ea -> 选择EaGeneral
		NVM -> NvmBlockDescriptors -> ASWC_NvmBlockNeed1 -> NvmTargetBlockReference -> 右键 -> choose - NvmEaRef ； 然后重新Mapping对应Ea的配置
	
	总结：（1）先配置总体AutoSar结构缺失的：缺模块的先添加， 缺Dem的参数就补充上；
		  （2）然后各bsw模块从下往上配置：spi -> eep - ea -> nvm 。
 
四、配置Os ： Os双核运行 配置Os 单核运行
	1.Os Configuration界面 （和Basic_Editor同一级别界面） ： 查看OS Task等的分配情况
	2.Task的重新分配： 有的task在Core0核心下运行 有的task在Core1下运行 需要重新分配 将Core1的Task移植到core0下
		有两类case ：case1 case2
	3.case1的task处理：运行周期不相同 ：这类的task直接移植到core0
		Basic_Editor -> Os -> OsApplication -> OsApp_QM_Core_1 -> App Task Ref : 
			查看core1下的task 
			找到需要删除的task ： xxx_task_core_1 点击X删掉
			上述步骤去core0下的添加Task
		OsConfiguration界面下找到刚才core0新增的task 修改名字
				OsConfiguration->OsApplications -> Tasks-> xxx_task_core_1 :
					short name : 修改为 xxx_task_core_0
				可以在该配置界面看到多个task的属性：Task Priority 、Task Schedule  、Task Stack Size 等等等等...
	4.case2的task处理 ：周期相同的task ：需要把core1中的task下面的Mapped Function重新映射到Core0
		Mapped Functions是否存在Triggered Function，不存在就直接删除即可
		存在Triggered Function ，需要重新map，把需要重新map的函数重新映射到对应的task下 ，再按照case1的方式处理task
			例如：core0 core1下都有同样周期的task ：xxx_task_100ms ,把task下面的triggered Functions防到一个task下面
		Idle Task不需要重新移植，删除core1中的
	
	5.Core1中的Isr 需要删除的 需要重新映射到Core0下的 做处理
	6.移植core1中的Alarm到Core0上 ，重新映射下
		Os Configuration -> Os Application -> OsCore_Coer1.OsApp -> Alarms -> Alarm Counter Ref :delete 、edit （edit之后会有默认值写上去）
		Basic_Editor ->Os -> OSApplication -> OsAPP_QM_Core1 -> APP Alarm Ref ：X掉，再去OsAPP_QM_Core0中添加
	7.OsConfiguration -> Os Cores : 删掉其他core
	8.Core0的task 重新选择TaskAccessingApplication ，点解delete自动选择为Core0
	9.Rtm模块中关闭Multicore Support
	10.Ecuc模块中删除Core1的配置
	11.Ecum中关闭Slave Core Handing选项
	12.Os中配置Hardware Init Core重新选择Core0 ；System Timer重新选择System_Timer_Core0
	13.Can模块中重新选择Counter Ref
		Basic_Editor -> Can -> CanGeneral -> Counter Ref：点击按钮选择SystemTimer_Core0
	14.Xcp模块删除多核的配置(标定模块 ： 用来标定和测量，反馈变量值供给上位机查看，如转速)
		Basic_Editor -> Xcp -> XcpConfig -> XcpCoreConfig -> XcpCore1 ：delete
		Basic_Editor -> Xcp -> XcpConfig ->XcpEventChannels -> Event Channel Core Ref ： 全选 delete ；edit Xcp_Core0
	15.	检查错误项 valid
		check各个模块 ： 点状态栏√按钮 ，点击[validate]按钮进行检查模块 
	16.Core0中用到的外设中断进行配置
		OsConfiguration -> OS Application -> Interrupt Service Routines -> add ： 添加一个外设中断
			添加外设中断配置属性：
				Short Name： ADC_GROUP0
				Isr Interrupt Priority : 40 
				Isr Interrupt Source : 140 
				Isr Special Function Name : adc_callback
				Isr Stack Size[Byte]: 2048
				(...省略...)
				
五、 generate代码
	检查各个模块下是否还有错误 ，解决一些报错， 按照validate下的提示解决错误。
	
	MCAL静态库：MCAL_Static_G9  : adc.c pwd.c等外设的库函数
	MCAL动态配置文件McalConfig： adc_bswd.arxml 各种arxml文件 ，是Davinci配置完后保存的配置信息文件
		同目录下MicroSarConfig : Davinci生成的bsw层各模块的代码 .c .h在此文件夹下
	SDK包：G9_SDK
 
六、编译代码 
	解决编译产生的报错：头文件 路径 变量名 宏开关等的缺失.....
```

## 5.总结

​    完成一个项目的配置整个过程需要一天左右，需要很细心很耐心得用DaVinci工具配置BSW中的各个模块，需要对autosar架构中各层各个模块了解，知道每个模块的作用，每个模块下的参数、属性等等。。。配置后需要耐心细心解决各类报错...

## 6.学习阶段

（1）**入门阶段 ： 了解基本框架**

 看一边书、看一遍PPT，了解框架，了解各个模块作用

（2）**进阶阶段：选择某个领域切入**

如果选择BSW：配置BSW模块：以下实现方式搞懂

BSW的IO实现

BSW的通信实现

BSW的存储实现

BSW的模式管理

BSW的看门狗实现

BSW的诊断系统实现

BSW的操作系统实现

（3）**高阶阶段 ：**实际的项目代码世界

搞懂代码如何层层封装，层层调用的 ；每个c文件的作用，每个函数的作用，每行代码的作用都能搞懂。 