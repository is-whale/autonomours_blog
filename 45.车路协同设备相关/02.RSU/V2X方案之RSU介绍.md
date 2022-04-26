- [V2X方案之RSU介绍_不懂汽车的胖子的博客-CSDN博客_rsu方案](https://blog.csdn.net/ChrisKKC/article/details/123094890?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-25-123094890.pc_agg_new_rank&utm_term=v2x协议栈&spm=1000.2123.3001.4430)

## 1 RSU背景

V2X（Vehicle to Everything）作为一种车用无线通信技术，是将车辆与一切事物相连接的新一代信息通信技术，其中 V 代表车辆，X 代表任何与车交互信息的对象，当前X 主要包含车（Vehicle to Vehicle, V2V）、人（Vehicle to Pedestrian, V2P）、交通路侧基础设施（Vehicle to Infrastructure, V2I）和网络（Vehicle to Network,V2N）。V2X 将“人、车、路、云”等交通参与要素有机地联系在一起，不仅可以支撑车辆获得比单车感知更多的信息，促进自动驾驶技术创新和应用，还有利于构建一个智慧的交通体系，促进汽车和交通服务的新模式新业态发展，对提高交通效率、节省资源、减少污染、降低事故发生率、改善交通管理具有重要意义。

C-V2X（蜂窝（Cellular）V2X）是基于 3GPP 全球统一标准的通信技术，基于 4G/5G 等 蜂窝网通信技术演进形成的车用无线通信技术，包含 LTE-V2X 和 5G-V2X。

RSU面向智能网联汽车、智慧交通领域，具有车路协同、智能计算、感知融合等功能。路侧示范终端基于C-V2X 协议栈，提供实时高效的交通效率及安全等服务，满足 C-V2X 技术在车联网 IoV、智能交通系统 ITS 领域的快速开发验证需求。

![img](https://img-blog.csdnimg.cn/a4be109a146541dfb8ebd3d35fab2054.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LiN5oeC5rG96L2m55qE6IOW5a2Q,size_13,color_FFFFFF,t_70,g_se,x_16)

 

OBU 是 C-V2X 系统的车载单元，通过无线 Uu 口连接云控中心和操作维护 中心。OBU 通过 PC5 口与 RSU 以及其他车辆实时通信，实现智能网联类应用。

RSU 是 C-V2X 系统的路侧单元，通过无线 Uu 口或者有线接口连接云控中 心和操作维护中心。RSU 通过路侧感知设备实时获取道路信息，由 PC5 口发送 给道路车辆 OBU 设备。

业务云实现 C-V2X 系统业务管理，数据分析与呈现，可以提供设备交通对象、道路状况监控及管理。

运维云实现 C-V2X 系统设备的远程管理、升级等。

## 2 RSU特点

运行基于 MOCAR C-V2X 协议栈应用，提供车-路信息实时交互。

1. 支持中国联通，中国移动，中国电信，中国广电的 2G/3G/4G/5G NR 通讯业务。
2. 多星座高精度 GNSS 卫星定位:支持 BeiDou/GPS/GLONASS/Galileo/QZSS。
3. 内置 HSM 模块，支持主流加密算法加速、验签、签名。
4. C-V2X(Uu mode)：符合 3GPP Rel.15 标准

- 支持 5G SA、NSA 双模组网，5G 和 LTE-A 多种网络制式的全面覆盖

- 支持全球主要地区和运营商的 5G NR/4G/3G 商用网络频段
- 数据峰值速率可达 2.5Gbps(DL)/650Mbps(UL)
- 支持多种网络协议（PPP、IPv4/v6）
- 5G 功能（网络切片管理、FOTA）

   5.C-V2X（PC5 mode4）：符合 3GPP Rel. 14 标准

- 工作频段：5905～5925MHz

- 工作带宽：10MHz

- 最大传输速率：30Mbps

   6.以太网：符合 IEEE1588 规范， 10/100/1000Mbps

   7.可通过 V2X 转发差分数据，RTCM2.X、RTCM3.X 格式

   8.POE 供电：支持 IEEE802.3 af/at POE。

   9.WiFi:支持 2.4G 频段的 802.11b/g/n

   10.防护设计: 符合 IP66 外壳防护

## 3 RSU安全

RSU 产品的安全特性包括传输安全和应用安全，应用安全包括无线安全和 OM 安全

### 3.1 传输安全

RSU 的传输安全使用 TLS（Transport Layer Security）和 HTTPS（Hypertext TransferProtocol over Secure Socket Layer）技术，实现 RSU 与信号机、检测器（如摄像头）、ACS 运维服务器和 V2X Server 之间的安全防护。

TLS 是一种安全通道的协议（比 SSL 更安全的传输协议），为点到点的通信提供了安全保护，有着广泛的应用。

TLS 常用于消减以下威胁：

- 数据流信息泄露威胁：为部署在 SSL/TLS 之上的通信提供了端到端的加密，防止数据在网络中传输的过程中信息泄露。

- 数据流篡改威胁：为 SSL/TLS 传输通道中的消息提供了完整性保护。

- 仿冒威胁：提供认证通讯双方身份的能力（认证方法有：数字证书认证、共享密钥认证等

HTTPs 是以安全为目标的 HTTP 通道，简单讲是 HTTP 的安全版，RSU 与远端运维和近端运维的网管连接，均采用 HTTPs 安全协议。

### 3.2 应用安全

RSU 的应用安全包括空口安全和 OM 安全。

#### 3.2.1 空口安全 

RSU 支持 LTE-Uu 接口和 PC5 口两种接口的通信机制，因此，RSU 的空口安全包括 LTE-Uu 口安全和 PC5 口安全。

-  LTE-Uu 空口安全

 RSU 的 LTE-Uu 空口安全和普通 LTE-Uu 空口安全方案一致，包括鉴权、空口数据加密和完整性保护；

- PC5 口安全

 PC5 口承载着车辆位置、速度、方向等关键 V2V 信息，涉及车辆和生命安 全，因此，RSU 在转发 PC5 口相关消息时，必须保证空口消息传输的安全。

 PC5 口安全包括：

- 空口数据保护：支持空口数据完整性保护，支持终端用户认证。

- 安全服务：用户认证、数字签名及验签。

#### 3.2.2 OM 安全 

OM 安全包括用户认证和访问控制、OM 系统安全和软件数字签名。

- 用户认证和访问控制

 认证和接入控制的对象是接入用户，认证的目的是识别用户身份并对合法用 户合理授权；接入控制的目的是定义并约束用户的操作和可以访问的资源。

- OM 系统安全
  - OM 系统安全包括软件完整性校验。
  - 软件完整性保护是指发布软件时对软件进行 HASH 或数字签名，然后将  软件上传到目标服务器或设备。当目标设备下载或者加载运行这些软件的时候，对 HASH 或数字签名进行校验，确保软件的端到端的可靠性和完整性。
  - 软件完整性保护功能，可以及时发现软件是否被病毒感染或者是否被恶意篡改，避免在设备上运行不安全的或者被病毒感染的软件。

#### 3.2.3 设备安全

RSU 作为道路交通基础设施，本身必须具备充分的安全保护机制。设备安全主要分设备物理安全和运行环境安全。

1. 设备物理安全

- RSU 在物理硬件上增加防拆卸等保护措施。

2. 运行环境安全

- 支持安全存储；

- 支持系统安全，包括操作系统加固，补丁等；

- 支持进程最小化权限，根据用户级别设定控制权限

## 4 RSU参数参考

| OS            | Linux                                                        |
| ------------- | ------------------------------------------------------------ |
| 处理器        | Cortex-A35 四核 1.2GHz                                       |
| 内存          | LPDDR4 2GB                                                   |
| 存储          | eMMC 8GB                                                     |
| 蜂窝          | WCDMA/FDD-LTE/TDD-LTE/NR [5G](https://so.csdn.net/so/search?q=5G&spm=1001.2101.3001.7020)(SA/NSA) |
| V2X           | C-V2X PC 5 mode4 符合 3GPP Rel.14 规范                       |
| GNSS          | 支持北斗/GPS/GLONASS/Galileo/QZSS，最高更新频率 10Hz；       |
| WiFi          | 2.4GHz，内置天线                                             |
| 以太网        | 10/100/1000Mbps*1 路                                         |
| SIM 卡        | 支持 Macro SIM 卡热插拔；兼容 eSIM 卡设计                    |
| HSM           | 支持主流加密算法加速、验签、签名                             |
| 电气环境特性  |                                                              |
| 供电          | IEEE802.3 af/at POE                                          |
| 运行/存储温度 | -40℃**～**+85℃ / -40℃**～**+85℃                              |
| 静电          | IEC61000-4-2(ESD)±15kV(空气),±8kV(接触)                      |
| 功耗          | 48V，250mA                                                   |
| 运行湿度      | 10%**～**95%(无凝结）                                        |
| 振动          | MIL-STD-810G                                                 |
| 防护等级      | IP66                                                         |

## **5 RSU射频性能**

| V2X           |                                                              |         |
| ------------- | ------------------------------------------------------------ | ------- |
| 频带范围      | 5.905～5.925GHz                                              |         |
| 最大发射功率  | 23dBm±2dB                                                    |         |
| 接收灵敏度    | -90dBm                                                       |         |
| RF 输入阻抗   | 50Ω                                                          |         |
| 天线          | N 型 Female                                                  |         |
| WAN           |                                                              |         |
| 制式          | WCDMA/FDD-LTE/TDD-LTE/NR 5G                                  |         |
| 频率范围      | WCDMA: B1/B2/B3/B4/B5/B8/B19LTE-FDD:B1/B2/B3/B4/B5/B7/B8/B9/B12/B13/B14/B17/B18/B1 9/B20/B25/B26/B28/B29/B30 /B32/B66/B71LTE-TDD: B34/B38/B39/B40/B41/B42/B48 5G NR：n1/n2/n3/n5/n7/n8/n12/n20/n25/n28/n38/n40/n41/n48/n66/n71/n77/n78/n79 |         |
| 发射功率      | Class3（24dBm+1/-3dB）for WCDMA Bands Class3（23dBm±2dB）for LTE FDD Bands Class3（23dBm±2dB）for LTE TDD Bands Class 3 (23 dBm ±2 dB) for 5G NR bands  Class 2 (26 dBm ±2 dB) for LTE B38/B40/B41/B42 bandsClass 2(26dBm +2/-3dB) for n41/n77/n78/n79 |         |
| RF 输入阻抗   | 50Ω                                                          |         |
| 天线连接器    | N 型 Female                                                  |         |
| GNSS          |                                                              |         |
| GPS/QZSS 频带 | L1 C/A:1575.42MHz                                            |         |
| GLONASS 频带  | L10F：1602MHz                                                |         |
| 北斗频带      | B1：1561.098MHz                                              |         |
| Galileo       | E1B/C1：1575.42MHz                                           |         |
| RF 输入阻抗   | 50Ω                                                          |         |
| 最高精度      | 2.55m CEP                                                    |         |
| 灵敏度        | 捕获                                                         | -160dBm |
|               | 冷启动                                                       | -148dBm |
| 热启动        | -157dBm                                                      |         |
| 启动时间      | 冷启动                                                       | 26s     |
| 热启动        | 3s                                                           |         |
| 重新捕获      | 1s                                                           |         |
| 天线连接器    | N 型 Female，3.3V 有源天线                                   |         |
| WLAN          |                                                              |         |
| 频带范围      | 2.4GHz                                                       |         |
| 最大发射功率  | 11b/11M                                                      | 18dBm   |
| 11g/54M       | 16dBm                                                        |         |
| 11n/MCS7      | 13dBm                                                        |         |
| 接收灵敏度    | 11b/11M                                                      | -86dBm  |
| 11g/54M       | -72dBm                                                       |         |
| 11n/MCS7      | -67dBm                                                       |         |
| 调制方式      | DBPSK/DQPSK/CCK/BPSK/QPSK/16QAM/64QAM                        |         |
| 加密方式      | WEP/TKIP/AES/WPA-PSK/WPA2-PSK                                |         |
| 天线          | 内置                                                         |         |

## 6 适配天线配件

| 序号 | 名称               | 数量 | 参数                                                         |
| ---- | ------------------ | ---- | ------------------------------------------------------------ |
| 1    | V2X 全向玻璃钢天线 | 2    | 5850**～**5925MHz增益：3dBi                                  |
| 2    | 5G 全向玻璃钢天线  | 4    | 820-960/1710**-**2690/3300-3800/4400-5000MHz增益：2/2.5/3dBi |
| 3    | GNSS 天线          | 1    | L1:1575.42±1.023 MHz B1：1561.098±2.046 MHz GLONASS: 1601.02±1.15MHz增益：28dBi |

## 7 遵循标准

| **项目**       | **标准**                                                     |
| -------------- | ------------------------------------------------------------ |
| 3GPP 协议标准  | PC5 口：3GPP R14LTE：3GPP R13/R14                            |
| 储存环境标准   | ETSI EN 300 019-1-1 Class 1.2: Weather protected, not temperature-controlled storage locations |
| 运输环境标准   | ETSI EN 300 019-1-2 Class 2.3: Public transportation         |
| 防震环境标准   | 满足 GR-63-CORE Zone4 等级要求 满足中国通信行业标准 YD5083 要求 |
| 电磁兼容性标准 | RSU 满足电磁兼容性的要求，并符合以下标准：ETSI EN 300 386 V 1.3.1 (2001-09)CISPR32 Class B GB9254 Class B IEC 61000-3-2 IEC 61000-3-3IEC 61000-4-2IEC 61000-4-3IEC 61000-4-4IEC 61000-4-6IEC 61000-4-11 |
| 雷电防护标准   | IEC 61000-4-5 surge immunity                                 |
| 盐雾防护标准   | IEC 68-2-11                                                  |
| 振动/冲击标准  | ETSI 300 019-2-4                                             |

## 8 RSU功能

### 8.1 红绿灯信息发送：

当行驶车辆遇到遮挡或恶劣天气等因素，无法对当前红绿灯或未来一段时间即将产生红灯变化做出正确判断时，RSU 可周期性广播该路口的道路信息和红绿灯信息到周边车辆，车辆获得该信息后可明确获知路口不同方位红绿灯状态，同时还可以根据自身的位置和地图确定车辆达到路口的时间，实现红绿灯车速引导。 

红绿灯信息发送数据流说明：

1：RSU 通过有线/无线的方式接收本地信号机发送红绿灯信息； 

2：RSU 通过 V2X 广播的方式向路上车辆发送红绿灯状态信息。

![img](https://img-blog.csdnimg.cn/610158e11d4d45c4bf8c479ffb08b169.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LiN5oeC5rG96L2m55qE6IOW5a2Q,size_13,color_FFFFFF,t_70,g_se,x_16)

### 8.2 交通信息播报

交通或公安部门的应用服务器通过路边的 RSU 发布交通信息（通过信号机/传输单元发送给 RSU，或者通过应用服务器从 Uu 口发送给 RSU），从而使道路行驶车辆及时获知前方道路的状态或其他交通指示。 

此外，对于远端十字路口视频信息播报也可以视为一种交通信息发布，本地 RSU 接收视频信息并传输至服务平台，远端车辆通过向平台发送视频请求(LTE Uu 空口方式)来观看特定路段的实时视频信息。 

交通信息播报数据流说明：

1: 交通或公安部门的应用服务器发布交通信息至信号机或传输单元； 

1’: 交通或公安部门的应用服务器发布交通信息传输至运营商平台的应用服务器； 

2: RSU 经由信号机或信息传输单元接收发布信息； 

2’: 运营商平台的应用服务器通过 Uu 口将信息传输至对应 RSU；

3: RSU 通过 V2X 广播方式播报交通信息； 

4：远端车辆通过 Uu 口向运营商平台发送视频请求，观看特定路段的实时视频信息。

![img](https://img-blog.csdnimg.cn/93b3995a8f6148b080b521f64751d83f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LiN5oeC5rG96L2m55qE6IOW5a2Q,size_13,color_FFFFFF,t_70,g_se,x_16)

其他详细内容见

[V2X方案之RSU介绍![icon-default.png?t=M1L8](https://csdnimg.cn/release/blog_editor_html/release2.0.7/ckeditor/plugins/CsdnLink/icons/icon-default.png?t=M1L8)https://download.csdn.net/download/ChrisKKC/81875554](https://download.csdn.net/download/ChrisKKC/81875554)