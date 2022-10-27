## 一、范围

本标准规定了 RSU 与中心子系统间的接口规范，包括业务数据接口和运维管理接口的要求。

本标准适用于 RSU 向中心子系统上报业务和运维数据，以及中心子系统向 RSU 下发配置和管理数据。

## 二、规范性引用文件

下列文件对于本文件的应用是必不可少的。凡是注日期的引用文件，仅注日期的版本适用于本文件。

凡是不注日期的引用文件，其最新版本（包括所有的修改单）适用于本文件。

T/ITS 0097-2018 合作式智能运输系统 通信架构。

YDT 3709-2020 基于 LTE 的车联网无线通信技术 消息层技术要求。

## 三、术语和定义

### 3.1 合作式智能运输系统 cooperative intelligent transportation system,C-ITS

通过人、车、路信息交互，实现车辆和基础设施之间、车辆与车辆、车辆与人之间的智能协同与配合的一种智能运输系统体系。

### 3.2 中心子系统 central sub-system, CSS

合作式智能运输系统组成部分，包括交通调度、规划、控制等多种设备，负责协调全局和局部区域交通活动。

### 3.3 道路子系统 road sub-system, RSS

合作式智能运输系统组成部分, 包括 RSU、道路传感器、道路交通设施、路侧计算设施等多种设备，负责收集、上报路面交通信息，控制交通流并与其他子系统进行通信。

### 3.4 危险工况 hazardous Situation

LSAD 系统工作时，自车若不改变行驶路线和车速，将与检测到的障碍物（如骑行人，车辆，行人等）发生碰撞。

### 3.5 路侧单元 road side unit, RSU

在道路子系统中，负责车路通信的交通服务逻辑单元。

## 四、符号和缩略语

C-ITS 合作式智能运输系统(Cooperative Intelligent Transportation System)

CSS 中心子系统(Central Sub-system)

C-V2X 基于蜂窝的车联网(Cellular Vehicle-to-Everything)

RSM 路侧单元消息(Road Side Message)

RSI 路侧单元信息(Road Side Information)

RSS 道路子系统(Road Sub-system)

RSU 路侧单元(Road Side Unit)

SPAT 信号灯消息(Signal Phase and Timing Message)

CSU 中心服务单元(Central Service Unit)

ESU 边缘服务单元(Edge Service Unit)

OBU 车载单元(Onboard Unit)

PSU 个人服务单元(Personal Service Unit)

## 五、基本要求

### 5.1 基本要求总则

为了实现中心子系统对 RSU 的配置、RSU 上报数据的采集和分发，以及对 RSU 的运维管理，中心子系统与 RSU 之间的接口分为业务数据接口和运维管理接口。

业务数据接口定义 RSU 与中心子系统之间的上下行消息的数据格式，主要包括 RSU 信息上报、RSU配置下发、MAP 数据上报和下发、BSM 数据上报、RSM 数据上报和下发、RSI 数据上报和下发，以及 SpaT数据上报和下发。

运维管理接口定义 RSU 上报的信息，以及中心子系统对 RSU 进行重启和远程升级 OTA 等。

### 5.2 通信架构

本标准基于《TITS 0098-2017 合作式智能运输系统 通信架构》定义的 C-ITS 系统逻辑框架中心子系统通过车辆子系统和道路子系统汇聚的数据，提供全局或者局部的 ITS 应用服务。道路子系统通过道路安装的各类传感器和设备为车辆和交通管理提供 ITS 应用所需的信息，并根据应用需求，执行相应控制指示。RSU 属于道路子系统中的一类设备，通过 ITS 边界路由器接入网络通信，并由中心子系统对 RSU进行配置和管理。

各子系统及其接口之间的交互如图 1 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/4462f5714f584c05bcee7e5a30624a1e.png)


### 5.3 业务数据接口要求

业务数据接口用于中心子系统与 RSU 之间下发和上报的 V2X 业务数据，包括：RSU 信息/MAP 数据/RSI数据的上报和确认、RSU 信息上报确认、RSU 业务配置/MAP 数据/RSI 数据的下发和确认、BSM 数据的上报、RSM 数据/SpaT 数据的上报和下发。MAP、RSI、BSM、RSM、SPAT 等消息的定义遵从标准 YDT 3709-2020基于 LTE 的车联网无线通信技术 消息层技术要求。

### 5.4 RSU信息上报

#### 5.4.1 基本介绍和要求

RSU 向中心子系统上报自身的经纬度位置和配置参数及运行信息。

消息发送频率： RSU 启动时和参数发生变化时上报。

#### 5.4.2 消息集

RSU 信息上报的消息 V2X.RSU.INFO.UP 中各数据元素如表 1 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/595b431f9b144025a11f188996d5c257.png)


#### 5.4.3 数据帧

RSU信息上报的消息V2X.RSU.INFO.UP中各数据元素的数据类型如表2-表10所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/5fc7e8def3b942c38d04770bab1e6274.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/ae7d26c255ea4438a2c8ce62299ae925.png)


### 5.5 RSU信息上报确认

#### 5.5.1 基本介绍和要求

中心子系统收到 RSU 上报自身经纬度位置和配置参数的确认响应消息。

消息发送频率：收到上报消息后发送一次确认消息。

#### 5.5.2 消息集

V2X.RSU.INFO.UP.ACK。

#### 5.5.3 数据帧

### 5.6 RSU业务配置下发

#### 5.6.1 基本介绍和要求

中心子系统向 RSU 下发消息配置参数，包括对 BSM、RSI、SpaT、RSM 和 MAP 消息的配置。根据该配置，确定 RSU 向中心子系统发送 BSM、RSI、SpaT、RSM 和 MAP 的规则。

消息发送频率：中心子系统对 RSU 配置时下发消息。

#### 5.6.2 消息集

RSU 业务配置下发的消息 V2X.RSU.CONFIG.DOWN 中各数据元素如表 11 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/4ecf2f7e64994587937c83f37ba8fdfe.png)


#### 5.6.3 数据帧

RSU 业务配置下发的消息 V2X.RSU.CONFIG.DOWN 中各数据元素的数据类型如表 12-表 16 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/d5c8cbaf3f284d2abdc993a23ce26b94.png)


### 5.7 RSU业务配置下发确认

#### 5.7.1 基本介绍和要求

RSU 收到中心子系统下发消息配置参数的确认响应消息。

消息发送频率：RSU 收到消息配置参数后发送一次确认消息。

#### 5.7.2 消息集

V2X.RSU.CONFIG.DOWN.ACK

#### 5.7.3 数据帧

### 5.8 MAP数据下发

#### 5.8.1 基本介绍和要求

中心子系统向 RSU 下发 MAP 数据。

消息发送频率：RSU 数据初始化，或者 MAP 数据发生变化时发送一次。

#### 5.8.2 消息集

MAP 数据下发的消息 V2X.RSU.MAP.DOWN 中各数据元素如表 17 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/a397bbac1219446b8e9bedf975691a7d.png)


#### 5.8.3 数据帧

MAP 消息集参考业界发布的最新的应用层数据标准。

### 5.9 MAP数据下发确认

#### 5.9.1 基本介绍和要求

RSU 在收到中心子系统下发的 MAP 消息的确认消息。

消息发送频率：收到下发数据后发送一次确认消息。

#### 5.9.2 消息集

V2X.RSU.MAP.DOWN.ACK

#### 5.9.3 数据帧

### 5.10 MAP数据上报

#### 5.10.1 基本介绍和要求

RSU 向中心子系统上报 MAP 数据。

消息发送频率：根据 DF_MAPCONFIG 的 upLimit 参数，决定每秒最多发送多少条消息。

#### 5.10.2 消息集

MAP 数据上报的消息 V2X.RSU.MAP.UP 中各数据元素如表 18 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/1e1f2f96ce4f4c519f13720ebff5b9c8.png)


#### 5.10.3 数据帧

MAP 消息集参考，业界发布的最新的应用层数据标准。

### 5.11 MAP数据上报确认

#### 5.11.1 基本介绍和要求

中心子系统收到 RSU 上报的 MAP 消息的确认消息。

消息发送频率：收到上报消息后发送一次确认消息。

#### 5.11.2 消息集

V2X.RSU.MAP.UP.ACK

#### 5.11.3 数据帧

参见附录 A“应用层确认消息结构”。

### 5.12 BSM数据上报

#### 5.12.1 基本介绍和要求

RSU 向中心子系统上报的 BSM 数据。

消息发送频率：根据 DF_BSMCONFIG 的 upLimit 参数，决定每秒最多发送多少条消息。

#### 5.12.2 消息集

BSM 数据上报的消息 V2X.RSU.BSM.UP 中各数据元素如表 19 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/5ec3d66609d944a9a5083c2296121133.png)


#### 5.12.3 数据帧

不涉及

### 5.13 RSM数据上报

#### 5.13.1 基本介绍和要求

RSU 向中心子系统上报的 RSM 数据。

消息发送频率：根据 DF_RSMCONFIG 的 upLimit 参数，决定每秒最多发送多少条消息。

#### 5.13.2 消息集

RSM 数据上报的消息 V2X.RSU.RSM.UP 中各数据元素如表 20 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/a144e0d6e4eb4b60ac7bf9ff7f26ff9b.png)


#### 5.13.3 数据帧

RSM数据上报的消息V2X.RSU.RSM.UP中各数据元素的数据类型如表21-表23所示

![在这里插入图片描述](https://img-blog.csdnimg.cn/d11a7eecc924482abf728808b3d93381.png)


### 5.14 RSM数据下发

#### 5.14.1 基本介绍和要求

中心子系统向 RSU 下发的 RSM 消息，该消息可携带多条 RSM。

#### 5.14.2 消息集

RSM 数据下发的消息 V2X.RSU. RSM.DOWN 中各数据元素如表 24 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/d53e5488dc524ce1a5f43412e4091d2c.png)


#### 5.14.3 数据帧

RSM 数据下发的消息 V2X.RSU.RSM.DOWN 中各数据元素的数据类型如表 25-表 27 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/f29a7953436e413bb204507f40d1f289.png)


### 5.15 RSI数据上报

#### 5.15.1 基本介绍和要求

RSU 向中心子系统上报的 RSI 消息，该消息用于向车辆发送告警。

消息发送频率：根据 DF_RSICONFIG 的 upLimit 参数，决定每秒最多发送多少条消息。

#### 5.15.2 消息集

RSI 数据上报的消息 V2X.RSU.RSI.UP 中各数据元素如表 28 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/9e50730a0458431b953a5fbf78eb0d62.png)


#### 5.15.3 数据帧

RSI 数据上报的消息 V2X.RSU.RSI.UP 中各数据元素的数据类型如表 29 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/3684406f32934ee995ce09d10c6b35e3.png)


### 5.16 RSI数据上报确认

#### 5.16.1 基本介绍和要求

RSU 向中心子系统上报的 RSI 消息的确认信息。

消息发送频率：中心子系统收到 RSU 上报的 RSI 消息后发送一次。

#### 5.16.2 消息集

V2X.RSU.RSI.UP.ACK

#### 5.16.3 数据帧

参见附录 A“应用层确认消息结构”。

### 5.17 RSI数据下发

#### 5.17.1 基本介绍和要求

中心子系统向 RSU 下发的 RSI 消息。

消息发送频率：当有告警信息时，由中心子系统向 RSU 发送一次。

#### 5.17.2 消息集

RSI 数据下发的消息 V2X.RSU.RSI.DOWN 中各数据元素如表 30 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/4626723bdf5d423ca751e80d9a89fcb9.png)


#### 5.17.3 数据帧

RSI 数据下发的消息 V2X.RSU.RSI.DOWN 中各数据元素的数据类型如表 31-表 33 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/599db8cb89734ab8b9916c091cde08e5.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/a7a90aa7920e415090f965354581bfc0.png)



### 5.18 RSI数据下发确认

#### 5.18.1 基本介绍和要求

中心子系统向 RSU 下发的 RSI 消息的确认信息。

消息发送频率：RSU 收到中心子系统发送的消息后，发送一次确认消息。

#### 5.18.2 消息集

V2X.RSU.RSI.DOWN.ACK

#### 5.18.3 数据帧

参见附录 A“应用层确认消息结构”。

### 5.19 SPAT数据上报

#### 5.19.1 基本介绍和要求

RSU 向中心子系统上报的信号机相位信息。

#### 5.19.2 消息集

Spat 数据上报的消息 V2X.RSU.SPAT.UP 中各数据元素如表 34 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/effe5f9420c94b8b884ebd5bd34960ad.png)


#### 5.19.3 数据帧

Spat 数据上报的消息 V2X.RSU.SPAT.UP 中各数据元素的数据类型如表 35-37 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/1b1ca0350ddb46349ccae3752e87c87f.png)


### 5.20 SPAT数据下发

#### 5.20.1 基本介绍和要求

中心子系统向 RSU 下发的信号机相位信息。

消息发送频率：事件触发上报。

#### 5.20.2 消息集

RSU.SPAT.DOWN

#### 5.20.2 数据帧

同 SPAT 数据上报一致。

## 六、运维管理接口要求

### 6.1 RSU心跳信息上报

#### 6.1.1 基本介绍和要求

RSU 向中心子系统上报自身心跳信息。

消息发送频率：周期性上报，频率默认为 60s，也可通过运维管理配置消息集更改。

#### 6.1.2 消息集

RSU 心跳信息上报的消息 V2X.RSU.HB.UP 中各数据元素如表 38 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/7f04dddb9f88420db845743f51a698eb.png)


#### 6.1.2 数据帧

不涉及。

### 6.2 RSU基本信息上报

#### 6.2.1 基本介绍和要求

RSU 向中心子系统上报 RSU 基本信息。

消息发送频率：RSU启动时，或参数发生变化时，RSU上报此消息。

#### 6.2.2 消息集

RSU 基本信息上报的消息 V2X.RSU.BaseINFO.UP 中各数据元素如表 39 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/75bc76911142435da4c5626cfc6a7c02.png)


#### 6.2.3 数据帧

RSU 基本信息上报的消息 V2X.RSU.BaseINFO.UP 中各数据元素的数据类型如表 40-表 42 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/e2811d8b8f3946ce96c1f238602198a3.png)


### 6.3 RSU运行状态信息上报

#### 6.3.1 基本介绍和要求

RSU 向中心子系统上报设备运行状态信息上报。

消息发送频率：周期性上报，频率默认为 60s，也可通过运维管理配置消息配置上报频率。

#### 6.3.2 消息集

RSU 运行状态信息上报的消息 V2X.RSU.RunningIfo.UP 中各数据元素如表 43 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2bc05a8da9e64cc69579a04eebc42780.png)


#### 6.3.3 数据帧

RSU运行状态信息上报的消息V2X.RSU.RunningInfo.U中各数据元素的数据类型如表44-表48所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/8a1d864545264dfd9c69582db1a8a1aa.png)


### 6.4 RSU日志上报配置下发

#### 6.4.1 基本介绍和要求

配置下发，用于 RSU 上报其日志到中心子系统，用于远程诊断及调试。

消息发送频率： 配置下发。

#### 6.4.2 消息集

RSU 日志上报配置下发的消息 V2X.RSU.Log.UP 中各数据元素如表 49 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/d062cf79682d44d5a092d2fb64c31932.png)


#### 6.4.3 数据帧

不涉及

### 6.5 远程升级OTA

#### 6.5.1 基本介绍和要求

中心子系统可以对设备进行远程OTA升级。

#### 6.5.2 消息集

远程升级 OTA 的消息 V2X.RSU.OTA.UP 中各数据元素如表 50 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/49d00a6b4b9f42379a1bf2bde3cc81d9.png)


远程升级 OTA 的消息 V2X.RSU.OTA.DOWN 中各数据元素如表 51 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/413dcfefcd71428c889f39ade592c7be.png)


#### 6.5.3 数据帧

不涉及

### 6.6 运维管理配置

#### 6.6.1 基本介绍和要求

中心子系统可以对设备进行远程配置。

#### 6.6.2 消息集

运维管理配置的消息 V2X.RSU.MNG.DOWN 中各数据元素如表 52 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/f916897491a44eb6bee7fea342ef60ed.png)


#### 6.6.3 数据帧

运维管理配置的消息 V2X.RSU.MNG.DOWN 中各数据元素的数据类型如表 53 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2cdf58d2e9844d8183d1444d10a4b632.png)


### 6.7 信息查询

#### 6.7.1 基本介绍和要求

中心子系统查询RSU信息时下发此消息。

#### 6.7.2 消息集

信息查询的消息 V2X.RSU.INFOQuery 中各数据元素如表 54 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/15a969ed0dba449bbfe7302cd9eb6308.png)


#### 6.7.3 数据帧

不涉及

### 6.8 信息查询响应

#### 6.8.1 基本介绍和要求

RSU向中心子系统返回查询信息的结果。

#### 6.8.2 消息集

信息查询响应的消息 V2X.RSU.INFOuery.Response 中各数据元素如表 55 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/626f426a09a94b1e97127d50d49b604a.png)


#### 6.8.3 数据帧

信息查询响应的消息 V2X.RSU.INFOQuery.Response 中各数据元素的数据类型如表 56 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/0ca55df529bd41de96eeb4965ab4b594.png)


## 附录A：应用层确认消息结构

应用层确认消息结构如表 A.1 所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/65258e0d1833490cbf450d1412d4aecc.png)