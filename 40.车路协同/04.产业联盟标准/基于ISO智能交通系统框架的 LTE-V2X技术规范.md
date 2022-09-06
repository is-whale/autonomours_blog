# 1 范围

本标准基于 ISO 智能交通系统框架，在接入层定义了一种新的通信接口，命名为 ITS-LTE-V2X 接口。该接口基于第三代移动伙伴计划（3GPP）定义的 LTE-V2X 技术规范。

本标准通过对基于 ISO 智能交通系统框架的通信适配层(CAL)和管理适配实体（MAE）的标准化定义，使得 LTE-V2X 技术作为智能交通系统站可选的通信接入技术之一。

# 2 规范性引用文件

下列文件对于本文件的应用是必不可少的。凡是注日期的引用文件，仅所注日期的版本适用于本文件。凡是不注日期的引用文件，其最新版本（包括所有的修改单）适用于本文件。

ISO 21217:2014, 智能交通系统-陆地移动通信接入-架构（Intelligent Transport Systems –Communications access for land mobiles (CALM) – Architecture）

ISO 21218:2018, 智能交通系统-混合通信-接入技术支持（Intelligent Transport Systems –Hybrid communications – Access technology support）

ISO 24102-1:2018, 智能交通系统-站管理-第一部分：本地管理（ Intelligent Transport Systems -- Station management -- Part 1: Local management）

ISO 24102-3:2018, 智能交通系统-站管理-第三部分：服务接口（Intelligent Transport Systems -- Station management - Part 3: Service access points）

ISO 24102-6:2018, 智能交通系统-站管理-第六部分：路径和流管理（Intelligent Transport Systems -- Station management -- Part 6: Path and flow management）

3GPP TS 23.285, 车联网通信服务架构增强（3rd Generation Partnership Project; Technical Specification Group Services and System Aspects; Architecture enhancements for V2X services (Release 14)）

3GPP TS 24.386, 用户设备与车联网通信服务控制功能（3rd Generation Partnership Project; Technical Specification Group Core Network and Terminals; User Equipment (UE) to V2X control function; protocol aspects; Stage 3 (Release 14)）

3GPP TS 36.300, 长期演进技术一般描述（3rd Generation Partnership Project; Technical Specification Group Radio Access Network; Evolved Universal Terrestrial Radio Access (E-UTRA) 

and Evolved Universal Terrestrial Radio Access Network (E-UTRAN); Overall description; Stage 2 (Release 14)）

3GPP TS 36.331, 长期演进技术无线资源控制协议规范（3rd Generation Partnership Project; Technical Specification Group Radio Access Network; Evolved Universal Terrestrial Radio Access (E-UTRA); Radio Resource Control (RRC); Protocol specification (Release 14)）

IEEE Std 802™-2014, 本地与城域网：概述与架构（IEEE Standard for Local and Metropolitan Area Networks: Overview and Architecture）

ISO/IEC 8824-1:2015,信息技术-抽象语法记法：基本符号规范 （Information technology –Abstract Syntax Notation One (ASN.1): Specification of basic notation）

ISO/IEC 8825-2:2015, 信息技术-抽象语法记法编码规范-压缩编码规范（Information technology \- ASN.1 encoding rules: Specification of Packed Encoding Rules (PER)） 

# 3 术语和定义

下列术语和定义适用于本文件。

## 3.1层2标识 Layer-2 ID

在开放式系统互联模型中层2 定义的标识，功能类似于IEEE802 MAC地址

# 4 缩略语

下列缩略语适用于本文件。

CI：通信接口（Communication Interface）

CAL：通信适配层(Communication Adaption Layer)

DLC：数据链路控制层(Data Link Control)

eNB：演进型基站（Evolved Node B） 

E-UTRAN：演进型全球陆地无线接入网络(Evolved Universal Terrestrial Radio Access Network)

ITS：智能交通系统（Intelligent Transport Systems）

ITS-AID：智能交通系统应用标识 (ITS Application Identifier)

ITS-S：智能交通系统站（ITS station）

ITS-SU：智能交通系统站单元（ITS station unit）

LLC：逻辑链路控制层(Logical Link Control)

LTE：长期演进技术(Long Term Evolution)

MAC：媒体接入控制层(Media Access Control)

MAE：管理适配实体(Mangement Adaption Entity)

MBMS：多媒体广播多播服务（Multimedia Broadcast Multicast Service）

PHY：物理层(Physical)

PPPP：近距离通信数据包优先级(ProSe Per-Packet Priority)

ProSe：近距离服务(Proximity-based Service)

SAE：安全适配实体(Security Adaption Entity)

SC-PTM：单小区单点到多点服务（Single-cell Point-to-Multipoint）

UE：移动终端(User Eqiupment)

Uu：终端设备与基站之间的接口

V2I：车辆到基础设施(Vehicle-to-Infrastructure)

V2N：车辆到网络（Vehicle-to-Network）

V2P：车辆到手持终端（Vehicle-to-Pedestrian）

V2V：车辆到车辆（Vehicle-to- Vehicle）

V2X：车联网通信(Vehicule to Everything)

# 5 一般需求

## 5.1 LTE-V2X 概述

LTE-V2X通信是智能交通系统站单元将智能交通信息发送给其它智能交通系统站单元的通信方式，其中其它智能交通系统站单元可以是车辆单元、路侧单元、个人单元或者中心单元。

备注：3GPP 定义的“V2I”，“V2N”与“V2P”分别对应于 ISO 21217：2014 中定义的“车辆到路侧站”， “车辆到中心站”和 “车辆到个人手持站”。

### 5.1.1 LTE-V2X 规范

ITS-LTE-V2X 接口实现需要满足 3GPP 定义的 LTE-V2X 技术规范

- 3GPP TS 23.285

- 3GPP TS 24.386

- 3GPP TS 36.300

- 3GPP TS 36.331

备注：其它需要满足的LTE-V2X规范见参考文献[1-11]。

### 5.1.2 ITS-LTE-V2X 接口支持的通信模式

#### 5.1.2.1 概述

ITS-LTE-V2X 接口支持以下两种操作模式（Operational Mode）中的任意一种，或者两种都支持：

1) PC5 接口通信（又称为直通链路通信）

   - 运营商管理模式

   - 非运营商管理模式

2) Uu 接口通信

   - 上行单播通信 （UE to eNB） 

   - 下行单播通信 （eNB to UE） 

   - 下行广播通信

a）多媒体广播多播服务（MBMS） 

b）单小区单点到多点服务 （SC-PTM）

#### 5.1.2.2 PC5 接口通信

PC5接口通信，又称为直通链路通信。PC5接口通信支持两种模式，分别是运营商管理模式和非运营商管理模式。

在运营商管理模式下，终端可以使用LTE 基站为其动态调度的资源或者终端从基站配置的资源池中自主选择的资源，这些资源被用来进行LTE-V2X 接口的数据通信。

非运营商管理模式适应于LTE 网络不可达的场景。在非运营商管理模式下，终端自主的从预配置的资源池中获取资源。

#### 5.1.2.3 Uu 接口通信

在Uu接口通信下，通过LTE-V2X接口进行数据传输，终端需要首先通过单播传输方式将数据传递到LTE基站，由LTE基站将数据传递到智能交通系统应用服务器，之后智能交通系统应用服务器负责将收到的原始数据或者经过处理的数据发送到合适的LTE基站，LTE基站可以通过下行单播方式或者多播传输方式将数据发送给LTE基站覆盖下的终端。其中多播传输方式可以是3GPP定义的多媒体广播多播服务（MBMS）或单小区单点到多点服务（SC-PTM）。

在Uu通信中，ITS 数据被封装在IP数据包中从终端发送到智能交通系统应用服务器，或者由智能交通系统应用服务器发送给终端。

## 5.2 智能交通系统站

### 5.2.1 通信框架

图1描述了ISO 21217：2014协议定义的智能交通系统框架，其中ITS-LTE-V2X接口位于该架构的接入层。

![image-20220906093224023](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5f1c967b.png)

图2定义了在智能交通系统框架下的ITS-LTE- V2X 接口的结构图。

![image-20220906093236264](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5ef6955e.png)

如图 3 所示，3GPP 定义了 LTE 的网络和传输层，LTE-V2X 规范中涉及的网络和传输层的功能可能需要在 ISO ITS-S 的网络和传输层中定义，这部分内容不在本规范的范围内。

![image-20220906093251096](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5ed719da.png)

ITS-LTE-V2X 接口通信协议层包括：

a) 物理层 (PHY)；

b) 数据链路层(DLL)；

其中 ITS-LTE-V2X 接口是一种无线类型的接口：

- 其中媒体类型( MedType) 为 c-ITSatt-iso17515； 

- 接口类型为(CI 类型)为 CIC-l6， 如 ISO 21218：2018 所定义：在有蜂窝网络基站支持或者没有蜂窝网络基站支持下的一对多通信接口；

- 接口接入类型为 CIAC-3，如 ISO 21218：2018 所定义；

ITS-LTE-V2X 接口提供 ISO 21218：2018 协议中 IN-SAP 定义的功能，同时能够使用 ISO 24102-3：2018 中 MI-SAP 和 SI-SAP 所定义的功能。

备注：目前，ISO 规范中 SI-SAP 没有定义服务原语。

备注：单个智能交通系统站单元可能包含多个 ITS-LTE-V2X 接口。

### 5.2.2 服务访问点 SAP

#### 5.2.2.1 通信服务访问点

ITS-LTE-V2X 接口需要支持 ISO 21218：2018 定义的 IN-SAP 功能。

备注：SAP 仅定义功能性行为，SAP 可以被实现成不同的形式。本规范中提及的支持 SAP 以及相应的原语意味着支持相应的功能，实现形式可以是具体的，即使用 ASN.1 形式定义的服务原语，或者实现形式是抽象的，允许专用解决方案。

#### 5.2.2.2 管理服务访问点

ITS-LTE-V2X 接口需要支持 ISO 24102-3：2018 中描述的 MI-SAP 功能，其细节定义在 ISO 21218：2018。

#### 5.2.2.3 安全服务访问点

ITS-LTE-V2X 接口需要支持 ISO 24102-3：2018 中描述的 SI-SAP 功能，其细节定义在 ISO 21218：2018。

#### 5.2.2.4 混合通信支持

ITS-LTE-V2X 接口支持 ISO 21217：2014 中引入的混合通信，即在一个智能交通系统站单元内（如ISO 21218：2018， ISO 24102-1：2018， ISO 24102-6：2018 中定义），多个通信协议栈可以同时运行。

#### 5.2.2.5 ITS-S 应用与通信需求映射

ITS-LTE-V2X 接口可能支持 ITS-S 应用进程与通信协议栈之间的自动映射，如 EN ISO 17423\[14 ](体现 ITS-S 应用进程的通信需求)和 ISO 24102-6：2018（对接口路径和流进行管理）所定义。

路径和流管理使用 MI-COMMAND 和 MI-REQUEST 服务原语功能，如 ISO 24102-6：2018 定义。如果ITS-LTE-V2X 接口在接收到这样的 MI-COMMNAD 命令之后的特定行为以及体现 MI-REQUEST 的流程超出了ISO 21218：2018 定义的一般需求，这部分内容在附录 F 中进行标准化。

# 6 通信接口协议栈

## 6.1 物理层

ITS-LTE- V2X 接口物理层需要满足 3GPP 定义的 LTE-V2X 技术规范。

## 6.2 数据链路层

### 6.2.1 一般描述

ITS-LTE-V2X 接口数据链路层需要满足 3GPP 定义的 LTE-V2X 技术规范。

### 6.2.2 数据链路层通信地址

LTE-V2X 直通链路通信并不采用 48 比特 MAC 地址，使用的是 24 比特的层 2 标识（L2 ID）来区分源地址和目的地址，详见 3GPP TS 36.300；其中源地址（Source Layer 2 ID）是车辆自己生成的 24 比特的数字序列；根据 3GPP TS 23.285，目的层 2 地址（Destination Layer 2 ID）是由上层提供的，旨在体现 V2X消息的服务类型。ITS-LTE-V2X 接口将会接收到 V2X 消息，以及该消息对应的智能交通系统应用标识（ITS-AID），如 EN ISO 17419[15]定义，终端会根据该标识映射出目的层 2 地址。

备注：3GPP 讨论了利用 ITS-AID 来选择目的层 2 地址，后续有可能修订。

备注：目前无可用信息用于确定谁决定可支持的 ITS-AID，以及该信息如何通知给 ITS-S 应用及终端 UE。

LTE Uu 接口不使用 L2 地址，因为 Uu 接口通信在终端与确定的基站之间进行。

### 6.2.3 上层协议的确定

LTE 系统中采用“层 3 协议数据单元类型”（Layer 3 protocol data unit types ）以及“V2X 消息家族”（V2X message familily）来确定装载数据的类型。

![image-20220906093444374](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5ea45aaf.png)

如果“层3协议数据类型”指示该装载为 “Non-IP”类型，更多的细节信息体现在“Non-IP 头”域中，其中“Non-IP 头”与EtherType对应关系如表2所示：

![image-20220906093457970](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5e87d5cc.png)

备注： Ethertype 地址是由 IEEE 分配的，被用来确定 ITS-S 接入层之上（网络和传输层）采用的通信协议。这种寻址被 IEEE Std 802™-2014 命名为“EtherType 协议区别”，EtherType 值的分配见链接 http://standards.ieee.org/develop/regauth/ethertype/eth.txt，其中 0-1535 不用于 EtherType 地址。

## 6.3 通信适配层

通信适配层（CAL）概念是在 ISO 21218：2018 中引入的，其主要的功能是为 ISO 21218：2018 中描述的 IN SAP 功能，另外 ISO 21218：2018 同时提供了 IN-UNITDATA 服务原语的 ASN.1 形式。

ITS-LTE-V2X 接口需要在 IN-UNITDATA 服务原语中携带 EtherType，来识别智能交通系统站中的网络与传输层协议，因此 6.2 节中规定的负荷类型相关的信息应被转化成 EtherType，以支持 ISO 21218：2018 协议。

在其它实现上下文中，EtherType 取值会被用于适用的 SAP 服务原语中，这些原语用于改变ITS-LTE-V2X 与网络实体之间的服务数据单元。其中，相应细节不在本规范的范围内。

IN SAP 服务原语 DL-UNITDATA 中包含参数“priority”，该参数指 ISO 21218：2018 中定义的用户优先级，用户优先级与 LTE-V2X 直通链路中 PPPP 之间的对应关系如表 3 和表 4 所示。其中 ISO 定义的用户优先级中 0 表示最低优先级，255 表示最高优先级，目前 3GPP 协议中定义的优先级 PPPP 中 1 表 示最高优先级，8 表示最低优先级。

表 3 列出了用户优先级与 PPPP 之间的映射关系，该映射关系应用于发送链路。其中 PPPP 定义和取值见 3GPP TS 24.334。 一旦 PPPP 的取值范围发生变化，表 3 中的映射关系按照线性关系进行调整。

![image-20220906093615543](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5e5ebb28.png)

表 4 列出了用户优先级与 PPPP 之间的映射关系，该映射关系应用于接收链路。其中 PPPP 定义和取值见 3GPP TS 24.334。一旦 PPPP 的取值范围发生变化，表 4 中的映射关系按照线性关系进行调整。

![image-20220906093634071](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5e3d4ed0.png)

# 7 通信接口管理

## 7.1 一般描述

ITS-LTE-V2X 接口管理需要满足 3GPP 定义的 LTE-V2X 技术规范。

## 7.2 管理适配实体

### 7.2.1 LTE-V2X 参数与 ISO 参数 I-Parameter

ITS-LTE-V2X 接口的实现需要符合 ISO 21218：2018 规范，对于附录 A 中的参数，需要满足如下基本规则：

- 对于某个 LTE-V2X 的参数，如果与 ISO 21218：2018 定义的 I-Parameter 参数等价，则该参数需要映射到 I-Parameter

- 对于某个 LTE-V2X 参数，其与 ITS-LTE-V2X 有关，但是不能在 ISO 21218：2018 定义的I-Parameter 中找到等价的参数，则该参数需要被 ITS 管理实体可见，可以通过媒体专用（ITS-LTE-V2X 接口专用）的 I-Parameter 来实现

- 对于 ISO 21218：2018 定义的 I-Parameter 中的参数，如果其与 ITS-LTE-V2X 接口有关，但是却不能在 LTE-V2X 参数中找到对应的参数，则该参数通过 ISO 21218：2018 中的管理适配实体来实现

### 7.2.2 LTE-V2X 管理命令与 ISO 管理命令

ITS-LTE-V2X 接口实现需要符合 ISO 21218：2018 规范，对于附录 B 和附录 C 中的命令，需要满足如下基本规则：

- 对于某个 LTE-V2X 的管理命令，如果与 ISO 24102-3：2018 中定义的 MI-COMMAND/MI-REQUEST中的某个命令等价，则该管理命令需要映射到 MI-COMMAND/MI-REQUEST

- 对于某个 LTE-V2X 的管理命令，如果其与 ITS-LTE-V2X 有关，但是不能在 ISO 24102-3：2018中定义的 MI-COMMAND/MI-REQUEST 中找到相应的命令，则该管理命令可以基于实现

-  对于ISO 24102-3：2018定义的MI-COMMANDs/MI-REQUEST中的某个命令，如果其与ITS-LTE-V2X相关，但是不能在 LTE-V2X 中找到对应的管理命令，则该命令可以通过 ISO 21218：2018 中定义的管理适配实体来实现

# 8 流程

## 8.1 通信接口流程

### 8.1.1 发送流程

当接收到发送请求服务原语，即 ISO 21218：2018 定义的 IN-UNITDATA.request 服务原语，通信适配层将执行下面步骤：

a) 使用 I-Parameter 中“Operational Mode”中的操作模式；

b) 根据 IN-UNITDATA.request 服务原语中“access parameters”，即 ITS-AID，设定 LTE-V2X 发送参数；

c) 对于 LTE-V2X 直通链路通信，确定 LTE-V2X PPPP。具体的，对于直通链路通信，根据IN-UNITDATA.request 服务原语中的元素“priority”与表 3 的对照关系，确定 PPPP；

备注：对于 LTE-V2X Uu 接口通信不使用用户优先级“priority”。

d) 根据 IN-UNITDATA.request 服务原语中元素“nt_protocol_id”，该元素体现 EtherType 值，来创建 LTE-V2X“Non-IP 头” 以及 LTE-V2X “层 3 PDU 类型”；

e) 请求将产生的数据帧（ISO 21217：2014 中定义的接入层协议数据）发送到 IN-UNITDATA.request服务原语中“destination_address”定义的目的地址。

备注：如果支持 ISO 24102-6：2018 中定义的路径和流管理，上文提到的地址和协议类型信息会被隐含在INUNITDATA.request 服务原语中的可选参数“flowID”中。

### 8.1.2 接收流程

当接收到一个数据帧时，通信适配层将执行如下步骤：

a) 对于 LTE-V2X 直通链路通信，根据收到数据包携带的 PPPP 来计算用户优先级，具体的，基于表 4 中用户优先级与 PPPP 的映射关系；

b) 根据 LTE-V2X“层 3 PDU 类型”和“LTE-V2X non-IP 头”递交 EtherType 值;

c) 将接收到的数据包（ITS-NTPDU）递交给 ITS 站网络和传输层，即使用 ISO 21218：2018 中定义的 IN-UNITDATA.indication 服务原语。

## 8.2 管理流程

### 8.2.1 Corss-CI 优先级

ISO 21218:2018 中定义了基本的 Cross-CI 优先级流程。对于 ITS-LTE-V2X 在频段 5800 MHz ± DSRCBW, 其中 DSRCBW = 200 MHz 时, 在所有包含 CEN 中速率 DSRC 片上单元, 或者高速率 DSRC 片上单元的实现中, “CI protection”为强制项。如果 ETSI TS 102792[12]中定义的消除技术“DSRC 检测” 实现，则 ISO 21218：2018 中定义的“CI protection”为可选项，如果法规没有其它约束的话。

备注：单边保护带宽 DSRCBW的取值取决于对干扰消除技术的研究，以避免对中速率 DSRC 和高速率 DSRC 的干扰。

### 8.2.2 操作模式

任何 LTE 网络引起的操作模式的变化都被记录在 I-Parameter “OperationalMode”中，任何非 ITS站管理模块要求的操作模式的变化会通过 MI-REQUEST 命令中包含的{Event21218Notification {E21218-5} }命令通知 ITS 站管理模块。

### 8.2.3 LTE-V2X MAC 地址映射

#### 8.2.3.1 LTE-V2X 直通链路通信

LTE-V2X 直通链路通信采用具有 24 比特的层 2 地址，而不是 48bit 的 MAC 地址，另外源层 2 地址与目的层 2 地址不同，具体参见 6.2.2 节。

1) 目的层 2 地址: 

   - LTE-V2X 直通链路目的层 2 地址指向 ITS 服务类型标识 ITS-AID。ITS-AID 是由 EN ISO 17419:2018 定义的，具体的目的层 2 地址与 ITS-AID 的映射关系目前仍未标准化。

   - ITS-AID 通过 IN-UNITDATA.request 原语中“access parameters” 中的“transmit access parameter”来传递

2) 源层 2 地址：
   - 源层 2 地址是终端自己生成的，其初始值也可能由 LTE 网络配置。

LTE-V2X 直通链路源层 2 地址是伪随机的。具体的，源层 2 地址的值是本地分配的，并且在接收到ITS 站管理请求之后更新为新值。目前没有规范定义更新速率和更新条件。LTE-V2X 直通链路源层 2 地址可能会进行变化，当其在接收到 ITS 站管理命令 MI-COMMAND {ChangePseudonymMACaddress} 对于基于 IP 的通信，如 TS 23.303 中描述，LTE-V2X 接口会自动配置本地 IPv6 源地址；隐私需求可能要求 IP 源地址与源层 2 地址一起改变。

将LTE-V2X 层2地址映射到ISO 21218：2018中定义的Link-ID （包含Local CIID 和Remote CIID）采用 ISO 21218：2018 附录 C.3 中的装配方法，其中 link-ID 采用的 EUI-64 形式，具体如下：

a) LocalCIID：

根据 ISO 21218：2018 中的定义，LTE-V2X 直通链路源层 2 地址可以映射到“VCISerialNumber” 全 0，同时“UC/GC” 域设置为‘000000’。

b) RemoteCIID：

根据 ISO 21218：2018 中的定义，LTE-V2X 直通链路目的层 2 地址映射到“VCISerialNumber” 值 65535，同时“UC/GC”设定为‘111111’用来指示广播通信。

备注：VCISerialNumber 为 ITS-S 内部编号，它不受隐私需求的约束。LTE-V2X 源层 2 地址的伪随机变化并不一定会导致 Link-ID 的变化。采用 IPv6 进行通信时，Link ID 可能出现在无线链路上。

#### 8.2.3.2 LTE-V2X Uu 接口通信

Uu 接口可以采用 48 比特 MAC 地址或者采用如下规则进行来设置地址：

a)LocalCIID: 与 LTE-V2X 直通链路相同

b)RemoteCIID: 

- “VCISerialNumber” 设定为 65535 ，“UC/GC”设定为‘000000’用来标识与基站的单播通信

- “VCISerialNumber” 设定为 65535, “UC/GC” 设定为 ‘111111’用来标识 LTE 基站的广播

- 其中“ITS-SCU-ID”和“MedID”的设置按照 ISO 21218：2018 规范进行

#### 8.2.3.3 小结

表 5 总结了 LTE-V2X MAC 层地址映射。

![image-20220906093917513](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5df2b52a.png)

### 8.2.4 接口连接流程

当 ITS-LTE-V2X CI 状态为“active”且 I-Parameter 中参数 Connect 被设定为“automatic”(0)，

或者接收到 MI-COMMAND 命令，其中 “CIstateChange” 值为 “connect”, 则 ITS-LTE-V2X 接口将执行 3GPP 规范的 LTE-V2X 连接流程。

### 8.2.5 接口状态管理

ISO 21218:2018 规范的 CI 状态管理，以及 ISO 24102-1：2018 规范的 ITS 站管理都需要被支持。

ITS-LTE-V2X 连接状态以及状态之间的转移事件见附录 D。

如果支持下面的特性，状态机是必须的。

- 混合通信（如 5.2.2.4 介绍）以及相应的干扰消除技术（如 8.2.1 介绍）

- 路径和流管理机制（如 5.2.2.5 介绍）

# 9 一致性

ITS-LTE-V2X 接口的一致性测试需要与 ISO 21218：2018 定义的应用需求进行结合。

实现一致性声明是对该规范中未涉及但是与 ISO 21218：2018 相关的部分的补充。

一致性测试具有区域性特征，例如干扰消除技术可以适用。

# 10 测试方法

测试结构和测试目的以及一致性测试的架构会在未来文稿中制定。

# 附 录 A（规范性附录）通信接口参数

## A.1 ITS-LTE-V2X特有的参数

通信接口参数，也称为 I-Parameter 是由 ISO 21218：2018 定义的，其适应于所有接入技术。附录A 主要提供 ITS-LTE-V2X 特有的细节以及专门用于 ITS-LTE-V2X 的参数。表 A1 给出了 ITS-LTE-V2X 特有的 I-Parameter。

![image-20220906094058548](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5dca4538.png)

![image-20220906094035988](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5da923a4.png)

## A.2 ITS-LTE-V2X默认参数

表 A2 定义了适用于 ITS-LTE-V2X 的 I 参数的默认取值。

![image-20220906094119556](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5d4f104c.png)

![image-20220906094148831](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5d32004e.png)

![image-20220906094227660](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5d0c8fb1.png)

![image-20220906094249326](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5cf0761e.png)

![image-20220906094259333](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5cd31646.png)

# 附 录 B（规范性附录）MI-COMMANDS

## B.1 一般描述

管理原语 MI-COMMAND.request 和 MI-COMMAND.confirm 以及 MI-COMMAND.request 的部分功能通过 ISO 24102-3：2018 来定义，更进一步的功能通过 ISO 21218：2018 和 ISO 24102-6：2018 来定义。

## B.2 需要的功能

表 B 中的 MI-COMMANDs 以及其功能需要被支持。

![image-20220906094349472](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5ca789d6.png)

# 附 录 C (规范性附录)MI-REQUESTS 

## C.1 一般描述

管理原语MI-REQUEST.request 和MI-REQUEST.confirm以及 MI-REQUEST.request的部分功能通过ISO 24102-3：2018 来定义，更进一步的功能通过 ISO 21218：2018 和 ISO 24102-6：2018 来定义。

## C.2 需要的功能

表 C 中的 MI-REQUESTs 以及功能需要被支持。

![image-20220906094409462](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5c7efb3a.png)

# 附 录 D（资料性附录）CI状态转移

## D.1 接口状态转移

ITS-LTE-V2X 接口状态转换与 LTE-V2X 状态映射以及状态转换触发事件可参考表 D1、D2 和 D3。 

![image-20220906094423422](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5c5b83cf.png)

![image-20220906094443606](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5c3e266e.png)

![image-20220906094516284](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5c20717b.png)

![image-20220906094558541](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5c038e05.png)

![image-20220906094607149](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5be54be9.png)

![image-20220906094619150](https://www.lovebetterworld.com:8443/uploads/2022/09/06/6316a5bc125db.png)

# 附 录 E（规范性附录）ASN.1定义

## E.1 概述

ASN.1 基本符号如 ISO/IEC 8824-1:2015 定义，下面的 ASN.1 模块在 E2 中定义：ITSltev2x {iso (1) standard (0) lte (17515) v2x (3) version0 (0)}

另外，本标准同时提供 ASN.1 类型和取值，如 E3 定义，将会在 ISO 21218 中注册。

为了防止本附录中 ASN.1 定义与本标准中其它地方描述或者定义不一致，本附录中定义的 ASN.1在未来可能会被更新，以发布在 http://standards.iso.org/iso/17515/上的为准。

如果没有特殊说明，本标准中的ASN.1类型和取值的编码采用ISO/IEC 8825-2:2015中定义的ASN.1 BASIC-PER, UNALIGNED 来进行编码。

## E.2 ITSltev2x 模块

```ASN.1
ITSltev2x {iso (1) standard (0) lte (17515) v2x (3) version0 (0)}
DEFINITIONS AUTOMATIC TAGS ::= BEGIN

IMPORTS 
-- From EN ISO 17419-1
EUI64 FROM CITSdataDictionary1 {iso(1) standard(0) cits-applMgmt (17419) dataDictionary (1) version1 (1)}
; -- End of IMPORTS

-- Medium-specific I-Parameter
LTE-V2X-OperationalMode::=INTEGER{
    unknown (0),
    sideLink (1),
    uuV2X (2),
} (0..255)

-- Link Layer Address
LTE-V2X-Layer2Address::=EUI64 -- Layer-2 Identifier encapsulated in EUI64
/* 将在后续版本中定义，如果适用。
-- RegulatoryScheme
LTE-V2X-RegulatoryScheme::=
--SimPin
LTE-V2X-SimPin ::=
-- RXsensitivity
LTE-V2X-RXsensitivity ::=
-- TXpower
LTE-V2X-TXpower ::= 
-- LinkDataRate
LTE-V2X-DataRateLink ::= 
-- PhysicalChannelIdentifier
LTE-V2X-PhysicalChannelIdentifier ::=
-- QoSrequirement
LTE-V2X-QoSrequirement ::=
*/
--
END
```

## E.3 将在ISO 21218 中添加的定义

```ASN.1
最新的 ITS-LTE-V2X 模块定义和更新见 http://standards.iso.org/iso/21218.
-- From ISO 17515-3
LTE-V2X-OperationalMode, LTE-V2X-Layer2Address FROM ITSltev2x {iso (1) standard (0) lte 
(17515) v2x (3) version0 (0)}
-- From EN ISO 17419-2 c-ITSatt-iso17515 FROM CITSapplMgmtComm {iso(1) standard(0) cits-applMgmt (17419) 
applRegistry (2) version2 (2)}
Medium specific general I-Parameter to be added:
OperationalModes MEDSPEC::={
{ LTE-V2X-OperationalMode IDENTIFIED BY c-ITSatt-iso17515 } ,
...
}
LLaddress MEDSPEC::={
{ LTE-V2X-Layer2Address IDENTIFIED BY c-ITSatt-iso17515 } ,
...
}
一旦 E.2 中的基本数据类型被定义，如下定义可能会被添加。
-- From ISO 17515-3
LTE-V2X-SimPin, LTE-V2X-RXsensitivity, LTE-V2X-TXpower, 
LTE-V2X-DataRateLink, LTE-V2X-PhysicalChannelIdentifier, 
LTE-V2X-QoSrequirement, LTE-V2X-RegulatoryScheme FROM ITSltev2x {iso (1) standard (0) lte 
(17515) v2x (3) version0 (0)}
--SimPin
SimPins MEDSPEC::={
{ LTE-V2X-SimPin IDENTIFIED BY c-ITSatt-iso17515 } ,
...
}
-- RXsensitivity
RxSenss MEDSPEC::={
{ LTE-V2X-RXsensitivity IDENTIFIED BY c-ITSatt-iso17515 } ,
...
}
-- TXpower
TxPowers MEDSPEC::={
{ LTE-V2X-TXpower IDENTIFIED BY c-ITSatt-iso17515 } ,
...
}
-- LinkDataRate
DataRatesLink MEDSPEC::={
{ LTE-V2X-DataRateLink IDENTIFIED BY c-ITSatt-iso17515 } ,
...
}
-- PhysicalChannelIdentifier
PhysicalChannelIds MEDSPEC::={
{ LTE-V2X-PhysicalChannelIdentifier IDENTIFIED BY c-ITSatt-iso17515},
...
}
-- QoSrequirement
QoSrequirements MEDSPEC::={
{ LTE-V2X-QoSrequirement IDENTIFIED BY c-ITSatt-iso17515},
...
}
-- RegulatoryScheme
lte-vtxRegScheme REGULSCHEME::={&regID c-RegScheme-iSO17515, &RegInfo 
LTE-V2X-RegulatoryScheme} 
RegulSchemes REGULSCHEME::={lte-vtxRegScheme, ...}
```

# 附 录 F（规范性附录）路径和流管理支持

## F.1 路径和流管理支持

ISO 24102-6：2018 中定义的路径和流管理是一个可选的特性。

LTE-V2X 相关细节可能会在该标准的补充规范中定义。