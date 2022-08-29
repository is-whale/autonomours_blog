# 1 范围

本文件规定了基于LTE的车联网无线通信技术（LTE-V2X）支持直连通信的路侧单元技术要求。 

本文件适用于基于LTE-V2X直连通信方式的路侧单元。 

# 2 规范性引用文件 

下列文件对于本文件的应用是必不可少的。凡是注日期的引用文件，仅所注日期的版本适用于本文件。凡是不注日期的引用文件，其最新版本（包括所有的修改单）适用于本文件。 

GB 5768.2-2009 道路交通标志和标线 第2部分：道路交通标志 

GB 14886 道路交通信号灯设置与安装标准规范 

GB/T 27957-2011 冰雹等级 

GB/T 27967-2011 公路交通气象预报格式 

GB/T 29100-2012 道路交通信息服务 交通事件分类与编码 

GB/T 30699-2014 道路交通标志编码 

YD/T 3340-2018 基于LTE的车联网无线通信技术 空中接口技术要求 

YD/T 3707-2020 基于LTE的车联网无线通信技术 网络层技术要求 

YD/T 3709-2020 基于LTE的车联网无线通信技术 消息层技术要求 

YD/T 3755-2020 基于LTE的车联网无线通信技术 支持直连通信的路侧设备技术要求 

YD/T 3756-2020 基于LTE的车联网无线通信技术 支持直连通信的车载终端设备技术要求 

T/ITS 0058-2017 合作式智能运输系统 车用通信系统 应用层及应用数据交互标准

SAE J3161 V2V车载安全通信系统性能需求 (On-Board System Requirements for V2V Safety Communications) 

3GPP TS36.321 Evolved Universal Terrestrial Radio Access (E-UTRA); Medium Access Control (MAC) protocol specification (Release 14) 

3GPP TS36.101 Evolved Universal Terrestrial Radio Access (E-UTRA); User Equipment (UE) radio transmission and reception (Release 14) 

3GPP TS23.285 Architecture enhancements for V2X services (Release 14) 

3GPP TS23.303 Proximity-based services (ProSe); Stage 2 (Release 14) 

# 3 术语、定义及缩略语 

## 3.1 缩略语 

本文件所用缩略语的中英文对照及其含义如下： 

AID: 应用标识(ApplicationIdentifier) 

ASN.1：抽象语法标记(Abstract Syntax Notation One) 

BSM：基本安全消息(Basic Safety Message)

COER：正则八位字节编码规则(Canonical Octet Encoding Rules) 

DE：数据元素(Data Element) 

DF：数据帧(Data Frame) 

DSM：专用短消息(Dedicated Short Message) 

DSMP：专用短消息协议(Dedicated Short Message Protocol) 

GNSS：全球导航卫星系统(Global Navigation Satellite System) 

ID：标识(Identification) 

ITS：智能交通系统(Intelligent Transport Systems) 

OBU：车载单元(On-Board Unit) 

PDB：包延迟预算(Packet Delay Budget) 

PGK：接近服务组秘钥(ProSe Group Key) 

PTK：接近服务流量秘钥(ProSe Traffic Key) 

RSM：路侧单元消息(Road Side Message) 

RSU：路侧单元(Road Side Unit) 

SAE：美国汽车工程师学会(Society of Automotive Engineers) 

SPAT：信号灯相位与配时消息(Signal Phase and Timing Message) 

SPDU：安全协议数据单元(Secured Protocol Data Unit) 

UPER：非对齐压缩编码规则(Unaligned Packet Encoding Rules) 

V2I：车载单元与路侧单元通讯(Vehicle to Infrastructure) 

V2P：车载单元与行人设备通讯(Vehicle to Pedestrians) 

V2V：车载单元之间通讯(Vehicle to Vehicle) 

V2X：车载单元与其他设备通讯(Vehicle to Everything) 

# 4 通用要求 

## 4.1 各层应遵循的标准 

路侧单元通过车辆-路侧单元接口发送MAP、SPAT、RSM、RSI消息，各层应符合的标准如表 1所示：

| 消息层 | YD/T 3709-2020                 |
| ------ | ------------------------------ |
| 网络层 | YD/T 3707-2020                 |
| 接入层 | YD/T 3340-2018、YD/T 3755-2020 |

## 4.2 接入层/PC5 

路侧单元 PC5 接口对应的接入层应符合如下要求： 

a) 路侧单元进行直连通信时的同步源应优先考虑GNSS。

b) 路侧单元的接入层可支持向上层提供拥塞控制相关测量参数的能力。对于支持该能力的路侧单元，应基于YD/T 3755-2020表A.1和表A.4向上层提供如下两种信息中的至少一种： 

1) 当前的CBR测量值； 

2) 当前满足CR limit要求的Max data rate建议值。 

c) 在初次使用前，路侧单元至少应预配置或存储YD/T 3755-2020附录A所规定的初始预配置参数或映射关系，并将初始参数值设置为YD/T 3755-2020所规定的相应取值。 

d) 在未从可信来源处获得参数更新的情况下，路侧单元应将上述初始预配置参数作为当前有效的预配置参数。 

注：预配置参数指的是在没有网络辅助的情况下PC5无线通信子系统通信所需要的参数。 

接入层数据发送应符合如下要求： 

a) 应采用广播发送方式。 

b) 应支持采用传输模式4进行数据发送。宜采用感知加半持续调度的资源选择方式。 

c) PDCP头的3比特SDU类型应设为011，应采用16比特的PDCP SN。PGK标识、PTK标识和PDCPSN应设为0。 

d) 应采用RLC UM模式，采用5比特的RLC SN。 

e) MAC头的4比特V域应设为0011。 

接入层数据接收应符合如下要求： 

a) 路侧单元接收STCH业务数据时，应采用RLC UM模式。 

## 4.3 网络层 

网络层数据发送应符合如下要求： 

a) MAP、SPAT、RSM、RSI在网络层作为专用短消息（DSM）传输，使用YD/T 3707-2020中的DSMP。DSMP版本应填写为0。 

b) 适配层应在[0x010001,0xFFFFFE]范围内产生并维持24比特Source_Layer-2 ID。 

c) 适 配 层 按 照 表 2 将 应 用 标 识 ApplicationIdentifier （ AID ） 参 数 映 射 为 24 比 特Destination_Layer-2 ID并指示给接入层。 

d) 适配层应根据YD/T 3755-2020表A.2，将发送数据包的Priority映射为PPPP并指示给接入层。 

e) 当上层提供Traffic Period参数时，网络层应将其指示给下层。

![image-20220829165936626](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c7ed02c5b3.png)

网络层数据接收应符合如下要求： 

a) 适 配 层 应 根 据 YD/T 3755-2020 表 A.1 ， 将 24 比 特 Destination_Layer-2 ID 映 射 为ApplicationIdentifier并指示给上层。 

b) 适配层应根据YD/T 3755-2020表A.3，将接收数据包的PPPP映射为Priority并指示给上层。 

## 4.4 路侧消息发送周期与 PDB 要求 

MAP、SPAT、RSM、RSI 消息宜采用如下周期发送（周期应为 100 毫秒的整数倍）： 

a) MAP：小于或等于 1 秒 

b) SPAT：小于或等于 500 毫秒 

c) RSM：100 毫秒 

d) RSI：对于静态类 RSI 为 1 秒，半静态类 RSI 为 500 毫秒，动态类 RSI 为 100 毫秒（静态、半静态、动态类型的说明见 8.3 节） 各情况下对应的 PDB 设置宜与发送周期保持一致。 

## 4.5 路侧消息的优先级设置 

MAP、SPAT、RSM、RSI 消息应按照表 3 设置 Priority：

表 3 路侧消息的优先级设置

| 消息类型   | Priority |
| ---------- | -------- |
| MAP        | 16       |
| SPAT       | 176      |
| RSM        | 208      |
| 静态 RSI   | 48       |
| 半静态 RSI | 80       |
| 动态 RSI   | 112      |

注：静态、半静态、动态类型的说明见8.3节。 

# 5 MAP 消息要求 

## 5.1 应用层要求 

应用层标准采用 YD/T 3709-2020，具体条目及要求如表 4 所示。

表 4 MAP 消息应用层标准要求

![image-20220829170113540](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c7f837fc4d.png)

![image-20220829170123116](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c7f81a2f69.png)

![image-20220829170133837](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c7f7f96054.png)

![image-20220829170205829](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c7f7d8f92b.png)

![image-20220829170225163](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c7f7b719c0.png)

## 5.2 消息内容 

MAP 消息应满足以下条件： 

a) 当满足表 5 中定义的 MAP 发送最小准则的要求时，系统应按照 4.4 节中的时间要求传输MAP 消息。 

b) MSG_MAP消息表示的内容与对应的物理标志标线设施保持协调一致。 

c) 系统在传输一个MAP消息时，会相应生成一个带MSG_MAP的MSG_MessageFrame，MSG_MAP的数据帧和数据单元在本文件和YD/T 3709-2020中定义。 

d) MSG_MAP 消息中，必须包含一个地图节点数据列表 DF_NodeList，其中包含一个或多个地图节点信息 DF_Node。 

e) 地图节点 DF_Node 一般是地图中的路口或者道路的端点，它是为了表示道路连接而抽象出来的概念。对于城市道路，不应在同一路段中间区域选取节点。它必须包含一个节点编号DF_NodeReferenceID，由地域号 DE_RoadRegulatorID 和本地编号DE_NodeID 共同组成。本地编号则是该节点在同一地域号下唯一的编号。 

f) 地图节点 DF_Node 中的参考位置坐标字段 refPos，一方面表示了该地图节点的位置，同时也为该节点数据帧嵌套的所有位置偏移量提供一个参考坐标。由于地图节点是表示该路口或端点区域的抽象概念，因此其位置坐标也是在该节点实际表示范围内的一个近似中心的坐标值，该坐标是无偏差的。一般情况下，该参考坐标值不直接参与应用计算。 

g) 有序的一对地图节点唯一确定了一条有向路段 DF_Link，原则上一对 node 之间最多存在唯一一个 link。如果没有特殊说明，本文件中的 link 包含的 lane 均指机动车道，含应急车道。地图节点 DF_Node 数据帧包含一个以该节点为下游节点的路段列表DF_LinkList。因此，所有不同的地图节点所包含的路段都是不重复的。 

h) DF_Link 数据帧必须包含一个地图节点编号，表示该路段的上游节点。 

i) DF_Link 数据帧中的 DF_SpeedLimitList，定义整个路段的限速。根据不同类型可以包含一个或多个限速值。当该 DF_Link 所包含的 DF_Lane 中进一步定义了车道限速，则应用方应以当前车道限速为优先，路段限速作为缺省值使用。 

j) DF_Link 数据帧中的 DF_PointList 字段，用有序的位置点列，拟合该路段的中心线。起点为该路段的起始线中心，终点为该路段的停止线中心。点列的选取应该遵循附录 D 中要求。

k) DF_Link 数据帧中的 DF_MovementList 字段，定义了该路段下游处的允许转向情况，以及可能对应的下游节点处信号灯相位 编号 。 通常一个路段下游可能包含多个转向DF_Movement 信息。一个转向由 remoteIntersection 地图节点编号字段，来表示该路段转向通过路口节点后的目标下游节点 ， 从而确定该转向 。 DF_Movement数据帧中的DE_PhaseID 字段表示的信号灯相位，必须与 Msg_SPAT 消息中对应地图节点处的信号灯关联。当 DF_Movement 数据帧中的 DE_PhaseID 字段填写了有效值，则当前 DF_Link 数据帧必须包含 DF_PointList 字段，且同一 MSG_MAP 消息中与该DF_Movement 的转向下游路段对应的 DF_Link 数据帧（嵌套在 remoteIntersection 指示的 DF_Node 帧中）也必须包含DF_PointList 字段。 

l) DF_Lane 数据帧，必须包含一个车道编号 DE_LaneID 字段。一个路段内，每一个连续的车道有唯一的 LaneID，且以该车道行驶方向为参考，自左向右从 1 开始编号。如果一个路段内车道有增减的情况，优先满足路段出口处车道编号自左向右从 1 顺序递增。 

m) DF_Lane 数据帧中的 DF_ConnectsToList，定义了当前车道到下游路段特定车道的连接关系以及相应的路口信号灯相位。该字段可用于为车辆提示和定位下游驶入的目标车道，以及对应的信号灯。当 DF_ConnectsToList 中有 DF_Connection 数据帧的DE_PhaseID 字段填写了有效值，则当前 DF_Lane 数据帧必须包含 DF_PointList 字段。同时，在同一 MSG_MAP 消息 中 ， 与 当 前 DF_Connection 的 DF_ConnectingLane 字 段 （ 如 填 写 有 效 值 ） 对 应 的DF_Lane 必须包含 DF_PointList 字段；或者，在与当前 DF_Connection 字段对应的下游路段 DF_Link 数据帧中，必须包含 DF_PointList 字段。 

n) DF_Lane 数据帧中的 DF_PointList 字段，用有序的位置点列，拟合该车道的中心线。起点为该车道的起始线中心，对于终结在交叉口的车道，终点为该车道的停止线中心；对于终结在路段中间的车道，终点为该车道的终止线中心。点列的选取应该遵循附录 D 中要求。

o) DF_Link 中 ， 在 DF_MovementList 中 可 将 每 个 phaseId信号灯相位额外关联一个remoteIntersection地图节点编号取值 ， 指示该信号灯相位所对应的行驶方向（ maneuver ） ； 当 该 DF_Link 所 包 含 的 DF_Lane 并 未 在 DF_ConnectsToList 中 指 示phaseId 对应的行驶方向（maneuver）时，上述指示应作为该车道的缺省值使用。其中remoteIntersection 取值见附录 E。 

p) 当该 DF_Link 所包含的 DF_Lane 在 DF_ConnectsToList 中指示 phaseId 对应的行驶方向（maneuver）时，应使用该车道级别指示。 

## 5.3 发送最小准则 

当系统发送 MAP 消息时，其消息内容应满足表 5 中定义的最小准则。否则，系统将终止发送MAP 消息，直到系统重新满足该准则的要求为止。

![image-20220829170536236](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c80373524e.png)

![image-20220829170551574](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c8046a8011.png)

![image-20220829170648318](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c807f3afc5.png)

## 5.4 数据单元（data element）要求 

### 5.4.1 Msg_MAP 

#### 5.4.1.1 msgCnt 

当系统设备启动后发送第一条 MAP 时，系统应将 msgCnt 初始化为一个随机值，其范围为 0 到127（见 YD/T 3709-2020）。 

当用于签名 MAP 的证书自从发送最近一条 MAP 之后有变化时，系统在发送下一条 MAP 之前应将 msgCnt 重新初始化为一个随机值，其范围为 0 到 127（见 YD/T 3709-2020）。 

当用于签名 MAP 的证书自从发送最近一条 MAP 之后无变化时，系统应将 msgCnt 设置为相比发送前一条 MAP 所用的值增加 1，若编号达到 127 则下一个回到 0。 

#### 5.4.1.2 timeStamp 

系统应采用 UTC 作为参考时间。数值用来表示当前年份，已经过去的总分钟数。 

为确保所传输信息的准确性，该时间值与生成 MAP 的 UTC 真实时间之间，如果在分钟切换时形成数值 1 的偏差，则该偏差至多存在 1 秒。 

### 5.4.2 DF_Node 

#### 5.4.2.1 name 

由字符串表达的地图节点名称或者描述，最小为 1 字节，最大为 63 字节。 

#### 5.4.2.2 id 

由一个区域号字段与一个局部编号字段共同构成的地图节点编号。 

region DE_RoadRegulatorID： 全局唯一的区域号，一般由国家或区域性的管理部门统一管理和分配。0 保留为测试编号。 

id DE_NodeID： 相同区域号范围内，唯一的局部编号。0-255 保留为测试编号。

#### 5.4.2.3 refPos 

地图节点位置值以及该节点范围内的参考坐标。以无偏差的绝对三维坐标表示。对于地图节点为信号灯控制路口，应将该点尽量选取在接近路口中心的位置。 

### 5.4.3 DF_Link 

#### 5.4.3.1 name 

由字符串表达的路段名称或者描述，最小为 1 字节，最大为 63 字节。 

#### 5.4.3.2 upstreamNodeId 

定义节点 ID，参见 5.4.2.2。 

节点 ID 是由一个全局唯一的地区 ID 和一个地区内部唯一的节点 ID 组成。 

此 ID 为关联 Link 的上游节点 ID。 

#### 5.4.3.3 linkWidth 

路段宽度。选取路段正常行驶部分（除去进入段或离开段可能存在的车道扩展，以及公交站台等扩展部分）的平均宽度，作为路段宽度。由车道扩展或站台等引起的宽度改变，在车道级信息中体现。路段宽度精度应精确到 0.1 米。 

#### 5.4.3.4 points 

路段中心点列。需满足附录 D 要求，其中 ActualError 不大于 2 米。 

### 5.4.4 DF_Movement 

#### 5.4.4.1 remoteIntersection 

由一个全局唯一的地区 ID 和一个地区内部唯一的节点 ID 组成，参考 5.2。 

#### 5.4.4.2 phaseId 

信号灯相位 ID，数值 0 表示无效 ID。phaseId 应与路段受实际信号灯控制情况保持一致。参考 6.4.3.1。 

### 5.4.5 DF_RegulatorySpeedLimit 

#### 5.4.5.1 speed 

限速值大小，精度为 0.02m/s。 

### 5.4.6 DF_RoadPoint 

#### 5.4.6.1 posOffset 

表示根据附录 D 选出的道路抽象位置点关于参考坐标的偏差值大小，包含经纬度偏差以及高度偏差。宜采用节省开销的原则选择适当的编码方式。 

offsetLL PositionOffsetLL： 经纬度偏差确定的位置点与该点对应的实际位置水平方向偏差，不超过 0.5 米。 

offsetV VerticalOffset： 高度偏差确定的位置点与该点对应的实际位置垂直方向偏差，不超过 0.5 米。

### 5.4.7 DF_Lane 

#### 5.4.7.1 laneID 

一个路段内，每一个车道有唯一的 ID，且以该车道行驶方向为参考，自左向右从 1 开始编号，最大有效值为 254。0 表示无效 ID 或者未知 ID。255 为保留数值。 

#### 5.4.7.2 laneWidth 

表示本车道的平均宽度。精度为 0.1 米。 

#### 5.4.7.3 laneAttributes 

定义车道属性，包括车道共享情况（shareWith）以及车道本身所属的类别特性（laneType）。 

如需表示人行横道，可将 laneType 设为 crosswalk，同时设置其 ID、宽度、中心点列等属性。 

#### 5.4.7.4 maneuvers 

表示本车道允许的所有转向行为。若实际中本车道允许连接到下游路段，则应在 maneuvers中表达至少一种可达的连接方式。车道允许转向行为的表达方式参考 5.4.8.2。 

#### 5.4.7.5 connectsTo 

表示本车道连接到下游路段的连接信息。若实际中本车道允许连接到下游路段，则应以connectsTo 表达至少一种可达的连接方式。车道与车道宜采用几何最大化连接原则。 

#### 5.4.7.6 points 

表示路段中心点列。需满足附录 D 要求，其中 ActualError 不大于 0.5 米。 

### 5.4.8 DF_Connection 

#### 5.4.8.1 remoteIntersection 

由一个全局唯一的地区 ID 和一个地区内部唯一的节点 ID 组成，参考 5.2。 

#### 5.4.8.2 connectingLane 

表示与本车道连接的下游路段车道编号，以及对应的转向。 

lane LaneID: 表示与本车道连接的下游路段车道编号。根据该车道编号，能够在下游路段对应的 DF_Link 数据帧中索引到该车道信息。在本车道上的车辆，允许从本车道驶出，进入该下游车道。 

maneuver AllowedManeuvers: 表示当前上下游车道连接对应的转向行为，并根据表 6 设置。maneuver 各比特位初始置 0，各条件分别独立判断和设置。

![image-20220829170852898](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c80fbc8b87.png)

#### 5.4.8.3 phaseId 

信号灯相位 ID，数值 0 表示无效 ID。phaseId 应与车道受实际信号灯控制情况保持一致。参考 6.4.3.1。 

# 6 SPAT 消息发送要求 

## 6.1 应用层要求 

应用层标准采用 YD/T 3709-2020，具体条目及要求如表 7 所示。

![image-20220829170915553](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c811288293.png)

![image-20220829170931638](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c812265be1.png)

## 6.2 消息内容 

SPAT 消息应满足以下条件： 

a) 当满足表 8 中定义的 SPAT 发送最小准则的要求时，系统应按照 4.4 的时间要求传输 SPAT消息。SPAT 消息的内容应与道路交通信号设施保持一致。 

b) 系统在传输一个SPAT消息时，会相应生成一个带MSG_SPAT的MSG_MessageFrame，MSG_SPAT的数据帧和数据单元在本文件和YD/T 3709-2020中定义。 

c) MSG_SPAT 消息中，必须包含一个路口信号灯数据列表 DF_IntersectionStateList，其中包含一个或多个路口的信号灯状态信息 DF_IntersectionState。 

d) DF_IntersectionState 数据帧必须包含数据帧 DF_NodeReferenceID，使该信号灯数据与 MSG_MAP 中包含的相同路口匹配。 

e) DF_IntersectionState 数据帧必须包含数据帧 DF_PhaseList，其中包含一个或多个信号灯

相位信息 DF_Phase。 

f) 数据元素 DE_IntersectionStatusObject，表示该信号灯当前的控制方式。 

g) 数据元素 DE_MinuteOfTheYear，DE_DSecond 以及 DE_TimeConfidence，用来表示该信号灯状态信息记录的时间戳与可信度，以消除由后续通信和处理时延导致的误差。

h) DF_Phase 数据帧必须包含一个本地唯一的相位号 DE_PhaseID，以及一个相位状态列表DF_PhaseStateList，包含一个或多个相位状态 DF_PhaseState。 

i) DF_PhaseState 数据帧必须包含一个相位灯色状态 DE_LightState。可以选择性包含该相位对应的时间信息DF_TimeChangeDetails。该相位时间信息，根据不同的信号控制设备和RSU 的处理方式，可以选择性地使用倒计时式或者 UTC 时刻式计时方式。 

j) 若信号机执行的是预设的固定相位配时方案，应将此完整的配时方案信息填写SPAT，若信号灯当前相位是非固定配时，应根据当前相位配时信息填写SPAT。在两种情况下，信号机都应将尽可能多的相位配时信息发送出来，而且SPAT消息与信号机实际配时信息保持一致。 

k) 当信号机给出的数据发生变化时，下一周期的 SPAT 消息要反映这一状态变化，端到端时延应小于 1 秒。 

## 6.3 发送最小准则 

当系统发送 SPAT 消息时，其消息内容应满足表 8 中定义的最小准则。否则，系统将终止发送SPAT 消息，直到系统重新满足该准则的要求为止。 

![image-20220829171028063](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c815ae722e.png)

![image-20220829171048300](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c816f45634.png)

## 6.4 数据单元（data element）要求 

### 6.4.1 Msg_SPAT 

#### 6.4.1.1 msgCnt 

当系统设备启动后发送第一条 SPAT 时，系统应将 msgCount 初始化为一个随机值，其范围为 0到 127（见 YD/T 3709-2020）。 

当用于签名 SPAT 的证书自从发送最近一条 SPAT 之后有变化时，系统应在发送下一条 SPAT 之前应将 msgCount 重新初始化为一个随机值，其范围为 0 到 127（见 YD/T 3709-2020）。 

当用于签名 SPAT 的证书自从发送最近一条 SPAT 之后无变化时，系统应将 msgCount 设置为相比发送前一条 SPAT 所用的值增加 1，若编号达到 127 则下一个回到 0。 

#### 6.4.1.2 moy & timeStamp 

系统应按照 YD/T 3709-2020 所示设置 moy（DE_MinuteOfTheYear）和 timeStamp （DE_DSecond），采用 UTC 作为参考时间。 

moy 数值用来表示当前年份，已经过去的总分钟数（UTC 时间）。timeStamp 数据用来表述当前分钟内的毫秒级时刻（

UTC 时间）。 

两个数值配合，则可以表示以毫秒记的全年已过去的总时间。 

为确保所传输信息的准确性，两个数值所表示的时间与生成 SPAT 的 UTC 真实时间之间的偏差应小于 *vMaxPosAge*（

150）毫秒。 

#### 6.4.1.3 name 

由字符串表达的 SPAT 消息名称或者描述，最小为 1 字节，最大为 63 字节。

### 6.4.2 DF_IntersectionState 

#### 6.4.2.1 intersectionId 

由一个全局唯一的地区 ID 和一个地区内部唯一的节点 ID 组成。参见 5.4.2.2。 

#### 6.4.2.2 status 

表示信号灯当前的控制模式状态，需根据信号控制系统实际的工作状态设置内部数值。具体的设置方法见表 9。status 各比特位初始置 0，各条件分别独立判断和设置。

![image-20220829171142477](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c81a570c06.png)

注：status 中的 recentMAPmessageUpdate，recentChangeInMAPassignedLanesIDsUsed，noValidMAPisAvailableAtThisTime 和 noValidSPATisAvailableAtThisTime 位，除一些场景特别说明外，一般情况下不使用。 

#### 6.4.2.3 timestamp & timeConfidence 

timeStamp（DE_DSecond）和 timeConfidence（DE_TimeConfidence），表示该信号机采集当前所有相位状态的 UTC 时刻。用于在用户终端侧进行时间同步，以抵消由于信息传输等因素引起的误差。

DE_TimeConfidence 应包含在 SPAT 消息里面，且其表示的含义为 TimeStamp 与实际信号灯的误差。 

### 6.4.3 DF_Phase 

#### 6.4.3.1 Id 

定义信号灯相位编号。数值 0 表示无效编号 ID，数值 1-255 表示有效编号。 

同一个信号灯中的不同相位，必须分配不同的相位编号。 

### 6.4.4 DF_PhaseState 

#### 6.4.4.1 说明 

DF_PhaseState 定义一个信号灯的一个相位中的相位状态列表。列表中每一个相位状态物理上对应了一种相位灯色，其属性包括了该状态的实时计时信息。至少包含 1 个相位状态，至多包含16 个相位状态。当可以从信号灯获得该相位的所有相位状态时，应将所有相位状态信息全部发送。 

当仅能获得当前相位状态信息时，可以只发送当前相位状态。 

#### 6.4.4.2 light 

定义信号灯相位的灯色状态。支持 GB 14886 规定的红、绿、黄三种信号灯灯色，以及亮灯、闪烁和熄灭三种状态。对于绿灯状态，在应用实现时应参考实际路口的情况，选择采用通行允许相位（permissive-green）或通行保护相位（protected-green）。具体的设置方法见表 10。

![image-20220829171225700](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c81d08e050.png)

注：LightState 的中 flashing-green 状态，除一些场景特别说明外，一般情况下不使用。 

同一相位不同的相位状态，必须有不同的灯色。 

### 6.4.5 DF_TimeCountingDown 

#### 6.4.5.1 说明 

DF_TimeCountingDown 定义一种倒计时形式的相位计时状态。 

#### 6.4.5.2 startTime 

如果当前该相位状态已开始（未结束），则该数值为 0；如果当前该相位状态未开始，则表示当前时刻距离该相位状态下一次开始的时间。 

#### 6.4.5.3 minEndTime 

表示当前时刻距离该相位状态下一次结束的最短时间（不管当前时刻该相位状态是否开始）。 

对于固定周期配时信号灯，minEndTime 应该等于 maxEndTime。 

#### 6.4.5.4 maxEndTime 

表示当前时刻距离该相位状态下一次结束的最长时间（不管当前时刻该相位状态是否开始）。 

#### 6.4.5.5 likelyEndTime 

表示当前时刻距离该相位状态下一次结束的估计时间（不管当前时刻该相位状态是否开始）。 

如果该信号灯相位是定周期、固定时长，则该数值就表示当前时刻距离该相位状态下一次结束的准确时间。如果信号灯当前相位是非固定配时（感应配时、手动控制等），则该数值表示预测的结束时间，且预测时间必须在 minEndTime 和 maxEndTime 之间，可能由历史数据或一些事件触发等来进行预测。如果当前灯色时长不断延长，则应同步更新 likelyEndTime 以实时反应最新配时方案。

对于该相位只有固定一种相位状态时（常绿、黄闪等），应将该相位状态的 likelyEndTime设置为 36000。 

#### 6.4.5.6 timeConfidence 

上述 likelyEndTime 预测时间的置信度水平。 

#### 6.4.5.7 nextStartTime 

如果当前该相位状态已开始（未结束），则该数值表示当前时刻距离该相位状态下一次开始的估计时长；如果当前该相位状态未开始，则表示当前时刻距离该相位状态第二次开始的时间。 

通常用在一些经济驾驶模式（ECO Drive）等相关的应用中。 

#### 6.4.5.8 nextDuration 

如果当前该相位状态已开始（未结束），则该数值表示该相位状态下一次开始后的持续时长；如果当前该相位状态未开始，则表示该相位状态第二次开始后的持续时长。与 nextStartTime 配合使用，通常用在一些经济驾驶模式（ECO Drive）等相关的应用中。 

图 1 显示了在当前时刻该相位状态开始或未开始两种情况下的各时间值。

![image-20220829171315524](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c82025bf67.png)

### 6.4.6 DF_UTCTiming 

#### 6.4.6.1 说明 

DF_UTCTiming 定义一种 UTC 世界标准时间形式的相位计时状态。 

#### 6.4.6.2 startUTCTime 

如果当前该相位状态已开始（未结束），则该数值为当前状态开始的时刻；如果当前该相位状态未开始，则表示当前该相位状态下一次开始的时刻。

#### 6.4.6.3 minEndUTCTime 

表示该相位状态下一次以最短时间结束所对应的时刻（不管当前时刻该相位状态是否开始）。 

#### 6.4.6.4 maxEndUTCTime 

表示该相位状态下一次以最长时间结束所对应的时刻（不管当前时刻该相位状态是否开始）。 

#### 6.4.6.5 likelyEndUTCTime 

表示该相位状态估计下一次结束的时刻（不管当前时刻该相位状态是否开始）。如果该信号灯相位是定周期、固定时长，则该数值就表示该相位状态下一次结束的准确时刻。如果信号灯当前相位是非固定配时（感应配时、手动控制等），则该数值表示预测的结束时刻，且预测时刻必须在 minEndUTCTime 和 maxEndUTCTime 之间，可能由历史数据或一些事件触发等来进行预测。如果当前灯色时长不断延长，则应同步更新 likelyEndUTCTime 以实时反应最新配时方案。 

#### 6.4.6.6 timeConfidence 

上述 likelyEndUTCTime 预测时间的置信度水平。 

#### 6.4.6.7 nextStartUTCTime 

如果当前该相位状态已开始（未结束），则该数值表示该相位状态估计下一次开始的时刻； 如果当前该相位状态未开始，则表示该相位状态估计第二次开始的时刻。通常用在一些经济驾驶模式（ECO Drive）等相关的应用中。 

#### 6.4.6.8 nextEndUTCTime 

如果当前该相位状态已开始（未结束），则该数值表示该相位状态下一次开始后再结束的估计时刻；如果当前该相位状态未开始，则表示该相位状态第二次开始后再结束的估计时刻。与nextStartUTCTime 配合使用，通常用在一些经济驾驶模式（ECO Drive）等相关的应用中。 

图 2 显示了在当前时刻该相位状态开始或未开始两种情况下的各时间值。

![image-20220829171400667](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c822f752f0.png)

# 7 RSM 消息发送要求 

## 7.1 应用层要求 

应用层标准采用 YD/T 3709-2020，具体条目及要求如表 11 所示。

![image-20220829171420391](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c82438dfb6.png)

![image-20220829171434365](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c825179ba6.png)

![image-20220829171447743](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c825e9edac.png)

![image-20220829171500565](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c826b7125e.png)

![image-20220829171510503](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c827568d0c.png)

## 7.2 消息内容 

RSM 消息应满足以下条件： 

a) 当满足表 12 中定义的 RSM 发送最小准则的要求时，系统应按照 4.4 中的时间要求传输 RSM消息。 

b) 系 统 在 传 输 一 个 RSM 消 息 时 ， 会 相 应 生 成 一 个 带 MSG_RoadsideSafetyMessage 的MSG_MessageFrame，MSG_RoadsideSafetyMessage的数据帧和数据单元在本文件和YD/T3709-2020中定义。 

c) MSG_RSM消息中的id为路侧单元的ID，表明RSM消息的发送数据来源。 

MSG_RSM 消息中，refPos 字段用来提供 RSM 消息作用范围内的参考三维位置坐标，消息中所有的位置偏移量，均基于该参考坐标计算。实际位置坐标等于偏移量加上参考坐标。 

d) MSG_RSM 消息中必须包含一个 DF_ParticipantList 数据列表。 

e) DF_ParticipantList 数据列表由一个或多个 DF_ParticipantData 数据帧组成，至少包含一条表示 RSU 自身信息的 DF_ParticipantData 数据帧。 

f) DF_ParticipantData 数据帧中的 ptcId 数据元素，当此数据帧表示 RSU 自身信息时，ptcId 应当填写为 0，若此数据帧表示其他由 RSU 检测到的参与者信息时，ptcId 范围为 1到 255。不同的 ptcId 在 RSU 中必须唯一。 

g) DF_ParticipantData 数据帧包含可选项 id 数据元素，若填写此项，当该参与者信息来源于 RSU 收到的 BSM 消息时，此 id 字段必须与 BSM 中的车辆 id 字段一致。

h) DF_ParticipantData 数据帧中的 DF_VehicleSize 字段，表示机动车/非机动车/行人/RSU的尺寸。 

## 7.3 发送最小准则 

当系统发送 RSM 消息时，其消息内容应满足表 12 中定义的最小准则。否则，系统将停止发送RSM 消息，直到系统重新满足该准则的要求为止。

![image-20220829171558477](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c82a540c67.png)

![image-20220829171646141](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c82d5459c6.png)

![image-20220829171654218](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c82dcdcb2c.png)

## 7.4 数据单元（data element）要求 

### 7.4.1 MSG_RSM 

#### 7.4.1.1 msgCount 

当系统设备启动后发送第一条 RSM 时，系统应将 DE_MsgCount 初始化为一个随机值，其范围为 0 到 127（见 YD/T 3709-2020）。 

当用于签名 RSM 的证书自从发送最近一条 RSM 之后有变化时，系统在发送下一条 RSM 之前应将 DE_MsgCount 重新初始化为一个随机值，其范围为 0 到 127（见 YD/T 3709-2020）。 

当用于签名 RSM 的证书自从发送最近一条 RSM 之后无变化时，系统应将 DE_MsgCount 设置为相比发送前一条 RSM 所用的值增加 1，若编号达到 127 则下一个回到 0。 

#### 7.4.1.2 Id 

对于系统设备启动后生成的第一条 RSM，系统应将 RSU 设备标识 id 初始化为一个随机值，其取值范围由 YD/T 3709-2020 定义。 

当用于签名 RSM 的证书自从发送最近一条 RSM 之后有变化时，系统在发送下一条 RSM 之前应将 id 重新初始化为一个随机值，其范围由 YD/T 3709-2020 定义。 

#### 7.4.1.3 refPos 

系统应将 refPos 中的 lat 和 lon 设置为 2D 水平位置参照。 

系统应将 elevation 设置为参考椭圆之上或之下的与其对应的位置参照的海拔（“参考椭圆上方高度”）。 

#### 7.4.1.4 participants 

定义交通参与者信息集合，至少包含一条 RSU 自身信息，应用于 RSM 消息中，表示 RSU 当前探测到的所有或者部分交通参与者信息。

### 7.4.2 DF_ParticipantData 

#### 7.4.2.1 ptcType 

路侧单元检测到的交通参与者类型。0 是未知，1 是机动车，2 是非机动车，3 是行人，4 是rsu 自身。 

#### 7.4.2.2 ptcId 

RSU 设置的临时 ID，0 是 RSU 本身，1...255 代表 RSU 检测到的参与者，RSU 中不同参与者的ptcId 必须唯一。 

#### 7.4.2.3 source 

定义交通参与者数据的来源。包括以下类型。 

unknown：未知数据源类型； 

selfinfo：RSU 自身信息； 

v2x：来源于参与者自身的 v2x 广播消息； 

video：来源于视频传感器； 

microwaveRadar：来源于微波雷达传感器； 

loop：来源于地磁线圈传感器； 

lidar：来源于激光雷达传感器； 

integrated：2 类或以上感知数据的融合结果。 

#### 7.4.2.4 id 

来自 BSM 的临时车辆 ID，当该参与者信息来源于 RSU 收到的 BSM 消息时，id 字段必须与 BSM中的车辆 id 字段一致。 

#### 7.4.2.5 secMark 

系统应按照 YD/T 3709-2020 所示设置 secMark ，采用 UTC 作为参考时间。 

#### 7.4.2.6 pos 

定义三维的相对位置（相对经纬度和相对高程）。约定偏差值等于实际值减去参考值。 

#### 7.4.2.7 posConfidence 

定义当前实时位置（经纬度和高程）的精度大小，包括水平位置精度和高程精度，由系统自身进行实时计算和更新。 

#### 7.4.2.8 transmission 

transmission 应当正确反映车辆的档位状态。 

#### 7.4.2.9 speed 

在 68%的测试测量中，speed 相对交通参与者实际速度的差距应在 *vSpeedAccuracy*（1kph）之内。当交通参与者的数据来源于 RSU 感知时，其数据精度要求取决于具体的 RSU 指标，在本文中不做规定。 

#### 7.4.2.10 heading 

heading 描述交通参与者参考点的方向，其值以正北方向为 0 点顺时针增加。

当交通参与者速度不超过 *vHeadingSpeedThresh*（45kph）时，DE_Heading 应当在 68%的测试 测量中相对交通参与者的实际航向角的差距在 *vHeadAccuracyB*（3 度）之内。 

当交通参与者速度超过 *vHeadingSpeedThresh*（45kph）时，DE_Heading 应当在 68%的测试测 量中相对交通参与者的实际航向角的差距在 *vHeadAccuracyA*（2 度）之内。 

当交通参与者速度下降至低于 *vHeadLatchThresh*（4kph）时，系统应将 DE_Heading 的值锁存为当交通参与者速度高于 *vHeadLatchThresh* 时的上一个已知的航向角值。 

当交通参与者速度高于 vHeadUnlatchThresh（5kph）时，系统将 DE_Heading 的值解除锁存。 

当交通参与者的数据来源于 RSU 感知时，其数据精度要求取决于具体的 RSU 指标，在本文中不做规定。 

#### 7.4.2.11 angle 

定义车辆方向盘转角。向右为正，向左为负。分辨率为 1.5°。数值 127 为无效数值。 

#### 7.4.2.12 motionCfd 

描述车辆运行状态的精度。包括车速精度、航向精度和方向盘转角的精度。 

#### 7.4.2.13 accelSet 

此字段的 lat 和 long 应当在 68%以上的测试测量中相对实际的交通参与者纵向加速度和横向加速度的差距在 *vAccelAccuracy*（0.3 m/s2）之内。 

此字段的 vert 应当在 68%以上的测试测量中相对实际的交通参与者垂直加速度的差距在*vVertAccelAccuracy*（1m/s2）之内。 

此字段的 yaw 应当在 68%以上的测试测量中相对实际的交通参与者横摆角速度的差距在*vYawRateAccuracy*（0.5deg/s）之内。 

当交通参与者的数据来源于 RSU 感知时，其数据精度要求取决于具体的 RSU 指标，在本文中不做规定。 

#### 7.4.2.14 size 

此字段的的 length 和 width 相对实际的交通参与者长度和宽度的差距应当在*vSizeAccuracy*（0.2 米）之内。当交通参与者的数据来源于 RSU 感知时，其数据精度要求取决于具体的 RSU 指标，在本文中不做规定。 

#### 7.4.2.15 vehicleClass 

定义车辆的分类，从多个维度对车辆类型进行定义。包含车辆基本类型，以及燃料动力类型。 

# 8 RSI 消息发送要求 

## 8.1 应用层要求 

应用层标准采用 YD/T 3709-2020，具体条目及要求如表 13 所示。

![image-20220829171938025](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c838153e34.png)

![image-20220829171950698](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c838de994b.png)

## 8.2 消息内容 

RSI 消息应满足以下条件： 

a) RSI 消息需要满足表 14 中规定发最小准则，系统应按照 4.4 的要求发送 RSI 消息。 

b) RSI 消息必须配合 MAP 发送。 

c) 系统在传输 RSI 消息时，会相应的生成一个带 Msg_RSI 的 Msg_MessageFrame，Msg_RSI 的数据帧和数据单元在本文件和 YD/T 3709-2020 中定义。 

d) 所有的 RSI 消息在 YD/T 3709-2020 中定义，其中 DF_RTSData 代表交通标志信息，DF_RTEData 代表交通事件信息。 

e) RSI 中的 id，标识路侧单元的 ID，表明 RSI 消息的发送数据来源。 

f) DF_RTEData 中 rteid 需要保证 RSU 设备中唯一性。 

g) DF_RTSData 中 rtsid 需要保证 RSU 设备中唯一性。 

h) 使用中需要根据 Msg_RSI 中设备 ID，及 DF_RTSData 和 DF_RTEData 中 id，作为 RTS 或 RTE的唯一标识。 

i) DF_RTSData 中 DF_SignType 表明该交通标志的类型，具体类型可以参考 GB 5768.2-2009。 

j) DF_RTSData 中 DF_ReferencePathList 和 DF_ReferenceLinkList，必须有一个不为空，交通标识信息，具体影响的 Path 或者 Link，至少发送其中之一。 

k) DF_RTSData 中应包含 description 字段，且 description 应使用 textGB2312 格式，其中字符 1~16 应使用道路交通标志对应的 GB/T 30699-2014 中的道路交通标志编码，之后可附加其他字符。

l) DF_RTEData 中 DF_EventType 标识交通事件类型，具体可以参考附录 C。 

m) DF_RTEData 和 DF_RTSData 的 priority 只在各自的范围内编码，两个 priority 之间无需统一编排。 

## 8.3 RSI 消息的分类 

RSI 消息所含信息分为 3 类： 

a) 动态信息：与道路交通参与者密切相关，事件信息随交通参与者数量在动态变化。 

b) 半静态信息：与道路交通参与者有关，但慢变的过程；或者表示该事件一旦发生会维持事件状态一段时间。 

c) 静态信息：典型为道路标识和标牌，其中标牌可以为电子标牌或者静态标牌；信息不随交通参与者多少而变化，或者该事件消息长期存在。 

所有 RTS 数据单元均属于静态信息，各个 RTE 数据单元的分类见附录 C 表 C.1。 

每条 RSI 消息中只能携带动态、半静态、静态信息中的一种，并设置相应的用户优先级和 AID 发送。 

## 8.4 发送最小准则 

当系统发送 RSI 消息时，其消息内容应满足表 14 中定义的最小准则。否则，系统将停止发送RSI 消息，直到系统重新满足该准则的要求为止。

![image-20220829172049664](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c83c8a2def.png)

![image-20220829172130332](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c83f152d41.png)

![image-20220829172140084](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c83fad44b1.png)

## 8.5 数据单元（data element）要求 

### 8.5.1 Msg_RSI 

#### 8.5.1.1 msgCnt 

当系统设备启动后发送第一条 RSI 时，系统应将 DE_MsgCount 初始化为一个随机值，其范围为 0 到 127（见 YD/T 3709-2020）。

当用于签名 RSI 的证书自从发送最近一条 RSI 之后有变化时，系统在发送下一条 RSI 之前应将 DE_MsgCount 重新初始化为一个随机值，其范围为 0 到 127（见 YD/T 3709-2020）。 

当用于签名 RSI 的证书自从发送最近一条 RSI 之后无变化时，系统应将 DE_MsgCount 设置为相比发送前一条 RSI 所用的值增加 1，若编号达到 127 则下一个回到 0。

#### 8.5.1.2 id 

对于系统设备启动后生成的第一条 RSI，系统应将 RSU 设备标识 id 初始化为一个随机值，其取值范围由 YD/T 3709-2020 定义。 

当用于签名 RSI 的证书自从发送最近一条 RSI 之后有变化时，系统在发送下一条 RSI 之前应将 id 重新初始化为一个随机值，其范围由 YD/T 3709-2020 定义。 

#### 8.5.1.3 moy 

系统应按照 YD/T 3709-2020 所示设置 DE_MinuteOfTheYear，采用 UTC 作为参考时间。

数值用来表示当前年份，已经过去的总分钟数（UTC 时间），其分辨率为 1 分钟。 

为确保所传输信息的准确性，DE_MinuteOfTheYear 的数值所表示的时间与生成 RSI 的 UTC 之间的偏差应小于 vMaxPosAge（

150）毫秒。 

#### 8.5.1.4 refpos 

RSI 参考坐标点，作为 RTS 和 RTE 的 referencePath 中的点集的参考点。 

系统应将 refPos 中的 lat 和 lon 设置为 2D 水平位置参照。 

系统应将 DE_Elevation 设置为参考椭圆之上或之下的与其对应的位置参照的海拔（“参考椭圆上方高度”）。 

#### 8.5.1.5 rtes 

定义道路交通事件集合，至少包含 1 个道路交通事件信息，最多包含 8 个。 

注：RSI 消息中，DF_RTEList 和 DF_RTSList 不能同时为空。 

#### 8.5.1.6 rtss 

定义道路交通标志集合。 

至少包含 1 个道路交通标志信息，最多包含 16 个。 

### 8.5.2 DF_RTEData 

#### 8.5.2.1 说明 

DF_RTEData 定义道路交通事件信息。交通事件分类当前支持国标 GB/T 29100-2012。该数据帧中，包含该交通事件的信息源、发生区域、时效、优先级以及影响区域等。还可以用文本的形式，对事件信息进行补充描述或说明。 

#### 8.5.2.2 rteid 

该 ID 为此 rte 在 RSU 内部的唯一 ID 标识，接收方需要使用 Device ID 和 rteId 作为该 RTE的唯一标识。 

此 ID 必须保持设备内的 RTE 列表中的唯一性。 

#### 8.5.2.3 eventType 

定义道路交通事件的类型，参考附录 C。其中，道路交通事件包括恶劣天气、异常路况和异常车况。GB/T 29100-2012 中定义的事件分类代码作为该值的千位和百位，交通事件分类顺序码作为该值的十位和个位。 

#### 8.5.2.4 eventSource 

定义道路交通事件的信息来源，包括：

\- 标识事件来源未知 

\- 交警部门 

\- 政府部门 

\- 气象部门 

\- 互联网服务 

\- 为本地设备感知 

#### 8.5.2.5 eventPos 

定义三维的相对位置（相对经纬度和相对高程）。约定偏差值等于实际值减去参考值。

表明 RTE 事件发生的位置信息。 

#### 8.5.2.6 eventRadius 

表示绝对值半径大小，分辨率为 10 厘米。 

当该值用于 RTE 中时，表明该交通事件或交通标志点的影响半径范围； 当该值用于影响路径中时，表明其路径范围内的影响半径。 

#### 8.5.2.7 description 

定义文本描述信息。提供两种编码形式。 

提供 ASCII 字符文本形式，支持长度 1 字节到 512 字节。 

提供中文编码形式，符合 GB2312-80 的编码规则，1 个中文字符由 2 字节信息编码，支持长度1 到 256 个中文字符。 

#### 8.5.2.8 timeDeatils 

定义道路交通事件和道路交通标志信息的生效时间属性。 

用 UTC 世界标准时间定义，包括生效起始时刻、结束时刻以及结束时刻的置信度。精确到分钟。

#### 8.5.2.9 priority 

表示此 RTE 交通事件的优先级。数值长度占 8 位，其中低五位为 0，为无效位，高三位为有效数据位。数值有效范围是 B00000000 到 B11100000，分别表示 8 档由低到高的优先级。

#### 8.5.2.10 referencePaths 

定义道路交通事件关联的路径集合。 

最多添加 8 个影响路径。 

#### 8.5.2.11 referenceLinks 

定义道路交通事件关联的路径集合。 

最多添加 8 个影响路径。 

### 8.5.3 DF_RTSData 

#### 8.5.3.1 说明 

DF_RTSData 定义道路交通标志信息。交通标志信息当前支持国标 GB 5768.2-2009，包含其中所有标志内容。该数据帧中，可以用文本的形式，对相关的交通标志进行补充描述或说明。

#### 8.5.3.2 rtsid 

该 ID 为 RSU 内部 RTS 的唯一 ID 标识，接收方需要使用 Device ID 和 rtsId 作为该 RTS 的唯一标识。此 ID 必须保持设备内 RTS 的唯一性。 

#### 8.5.3.3 signType 

定义道路交通标志的类型。 

数值 0 表示未知类型，或文本描述信息。大于 0 数值表示交通标志标牌信息，其编号参照国标 GB 5768.2-2009 中“交通标志中文名称索引”表序号。 

#### 8.5.3.4 signPos 

定义三维的相对位置（相对经纬度和相对高程），标牌位置应为标牌实际的物理位置。约定偏差值等于实际值减去参考值。 

#### 8.5.3.5 description 

定义文本描述信息。提供两种编码形式。 

提供 ASCII 字符文本形式，支持长度 1 字节到 512 字节。 

提供中文编码形式，符合 GB2312-80 的编码规则，1 个中文字符由 2 字节信息编码，支持长度1 到 256 个中文字符。 

#### 8.5.3.6 timeDetails 

定义道路交通事件和道路交通标志信息的生效时间属性。 

用 UTC 世界标准时间定义，包括生效起始时刻、结束时刻以及结束时刻的置信度。精确到分钟。

#### 8.5.3.7 priority 

表示 RTS 消息中不同类型交通标志的优先级。数值长度占 8 位，其中低五位为 0，为无效位，高三位为有效数据位。数值有效范围是 B00000000 到 B11100000，分别表示 8 档由低到高的优先级。 

RTS 中的 priority 应遵循 GB 5768.2-2009 的分类，如表 15 所示：

![image-20220829172351674](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c847ee3f0e.png)

#### 8.5.3.8 referencePaths 

定义道路标志的关联路径集合，为标牌内容指示的作用范围。用于 RSI 消息中。 

最多添加 8 个影响路径。 

#### 8.5.3.9 referenceLinks 

定义关联路段集合，为标牌内容指示的作用范围。 

最多添加 16 个影响 Link。 

### 8.5.4 DF_ReferencePath 

#### 8.5.4.1 activePath 

为 RTS 交通标识牌影响的路径的点集。 

点集内容为 PositionOffsetLLV 类型，最少为 1 个点，最多为 32 个点。 

#### 8.5.4.2 PathRadius 

为 RTS 交通标识牌影响的路径的影响半径。 

### 8.5.5 DF_ReferenceLink 

#### 8.5.5.1 upstreamNodeId 

定义节点 ID，参见 5.4.2.2。 

节点 ID 由一个全局唯一的地区 ID 和一个地区内部唯一的节点 ID 组成。 

此 ID 为关联 Link 的上游节点 ID。 

#### 8.5.5.2 downstreamNodeId 

定义节点 ID，参见 5.4.2.2。 

节点 ID 由一个全局唯一的地区 ID 和一个地区内部唯一的节点 ID 组成。 

此 ID 为关联 Link 的下游节点 ID。 

#### 8.5.5.3 referenceLanes 

定义路段中指定的关联车道。将指定车道号对应的比特位置 1 表示该车道为有效的关联车道。 

最多支持 15 条车道。车道号，以该车道行驶方向为参考，自左向右从 1 开始编号。 

# 9 消息调度与拥塞控制 

多运营商重叠覆盖场景下，RSU 设备应根据当前 CBR 进行拥塞控制。当测量 CBR 较高时，RSU应考虑降频发送。

# 10 射频性能要求 

## 10.1 发射机指标 

路侧单元通信时，发射机相关指标应满足国家无线电管理机构相关规定和 YD/T 3755-2020 第 9.2 节要求。 

## 10.2 接收机指标 

路侧单元使用 PC5 进行通信时，接收机指标应满足 YD/T 3755-2020 第 9.3 节要求。 

# 11 安全要求 

## 11.1 数据格式要求 

RSU发送消息时应使用安全协议数据单元（Secured Protocol Data Unit，SPDU），并采用正则八位字节编码规则（COER）进行编码。 

## 11.2 安全层消息发送要求 

a) RSU系统应使用SM2椭圆曲线公钥密码算法、SM3密码杂凑算法对发送的消息生成消息签名，并附加于该消息发送； 

b) RSU系统应为发送的消息附加用于签名该条消息的应用证书或应用证书的摘要，不应附加证书链的CA证书； 

c) RSU系统应使用SM3密码杂凑算法对应用层消息、应用证书进行杂凑运算获得其摘要； 

d) RSU系统发送消息时，应在以下情况中附加完整的应用证书，在其它情况中附加应用证书的摘要： 

1) 系统进行启动/重启操作后发送的第一条消息； 

2) 使用的应用证书发生改变后发送的第一条消息； 

3) 本条消息距离上一条附加了完整的应用证书的消息，其时间间隔等于或大于vMaxCertDigestInterval（450ms）； 

e) RSU系统应使用有效的应用证书用于生成SPDU，当RSU系统没有有效的应用证书时，RSU系统不应发送消息。当正在使用的应用证书过期时，RSU系统应立即使用其他有效的应用证书。 

f) RSU系统不应对发送的消息进行加密； 

g) RSU系统发送消息时，构造的安全协议数据单元（SPDU）的消息安全头HeaderInfo中，应当附加消息生成时间，应用标识值与网络层AID值相同。 

h) RSU使用的应用证书及其所在证书链中，appPermissions、certIssuePermissions、 certRequestPermissions三个字段中出现3618、3619、3620、3621、3622、3623这些AID时， ssp或ssprange字段默认设置为ABSENT，默认权限如表 16所示。

![image-20220829172528325](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c84df2afe8.png)

## 11.3 系统安全要求 

RSU 系统应采取措施保护与私钥相关的运算的安全性，具备环境失效保护能力，具备对抗非入侵式攻击的能力。

# 附录 A （资料性附录） 系统概述 

基于 LTE 的车联网直接通信系统是通过人、车、路信息交互，实现车辆和基础设施之间、车辆与车辆、车辆与人之间的智能协同与配合的一种通信系统。车载通信系统实现了智能运输系统的不同子系统之间的信息交互。通过与交通系统中各个参与元素的直接通信，车载通信系统可以为包括提升道路安全、提高交通通行效率和提供各种信息服务的应用提供有效信息支持。 

图 A.1 给出了车辆-路侧单元（V2I）直接通信系统架构，对于路侧单元设备而言，其通常包括了以下几个子系统： 

a) 无线通信模块——接收和发送 LTE V2X PC5 信号。 

b) 路侧处理模块——运行程序以生成需要发送的空中信号，以及处理接收的空中信号。 

c) 定位模块——负责路侧单元的实时定位以及时间同步。 

d) 天线——实现射频信号的接收和发送。 

路端还可部署信息处理计算单元，与 RSU 进行信息交互，负责将其接收到的数据和/或感知到的数据进行处理；其他可能存在的相关单元可负责具体信息的收集和感知，如传感器感知路面情况等。

![image-20220829172559548](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c84fe40f30.png)

# 附录 B （资料性附录） 应用描述 

本小节对本文件所考虑和涉及到的主要应用进行描述，详细的应用描述和相应需求参见 T/ITS0058-2017。表 B.1 给出了本文件所使能的主要应用列表。

![image-20220829172622509](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c85159aac3.png)

# 附录 C （规范性附录） DE_EventType（交通事件索引）、DF_Description（附加说明）类型及取值

![image-20220829172645702](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c852cd917e.png)

![image-20220829172659581](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c853a69018.png)

![image-20220829172713350](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c85484ce4d.png)

恶劣天气事件由 DF_Description 补充说明：

![image-20220829172727141](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c8555ed786.png)

![image-20220829172740350](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c85637782a.png)

![image-20220829172751496](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c856e67703.png)

![image-20220829172805406](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c857d00bd1.png)

![image-20220829172817493](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c85884b8a1.png)

# 附录 D （规范性附录） 道路抽象点选取要求 

MAP 和 RSI 消息中通过 PointList 的方式抽象描述了道路的几何形状，这里我们对选取点的精度进行要求。具体要求如下（该要求适用于城市道路）： 

a) 所有点都为道路中心线上的点； 

b) 第一个点为上游节点进入该路段的第一个点； 

c) 最后一个点为进入下游节点的停止线的中心点； 

d) Link 点集中任意连续两点连线上的点，距离实际覆盖的道路中心线的垂直距离，需要小于ActualError值； 

e) Lane 集中任意连续两点连线上的点，距离实际覆盖的车道中心线的垂直距离，需要小于ActualError值； 

f) 在RSI消息中，当RTS和RTE影响的Path为实际的道路时，也需要符合上面的规范。

![image-20220829172844811](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c85a4d5222.png)

![image-20220829172855090](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c85ae82e4a.png)

# 附录 E （规范性附录） DF_MovementList 中扩展指示 maneuver

![image-20220829172912890](https://www.lovebetterworld.com:8443/uploads/2022/08/29/630c85bfa17ab.png)