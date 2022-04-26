- [V2X之V2V介绍 | XuQi's Blog (xuqi1987.github.io)](https://xuqi1987.github.io/2019/07/06/V2X之V2V介绍/)

## 1 术语定义

### 主车host vehicle，HV

装有车载单元且运行应用程序的目标车辆。

### 远车remote vehicle，RV

与主车配合能定时广播V2X消息的背景车辆。

### 车载单元on-board unit，OBU

安装在车辆上的可实现V2X通讯，支持V2X应用的硬件单元。

### 路侧单元road side unit，RSU

安装在路边的可实现V2X通讯，支持V2X应用的硬件单元。

### V2X

车载单元与其他设备通讯，包括但不限于车载单元之间通讯（V2V），车载单元与路侧单元通讯（V2I），车载单元与行人设备通讯（V2P），车载单元与网络之间通讯（V2N）。

## 2 缩略语

4G：第四代移动通信技术the 4th Generation mobile communication technology
5G：第五代移动通信技术the 5th Generation mobile communication technology
ABS：制动防抱死系统Anti-lock Braking System
ADS：应用数据交换服务Application Data-Exchange Service
API：应用程序编程接口Application Programming Interface
ASN.1：抽象语法标记Abstract Syntax Notation One
AVW：异常车辆提醒Abnormal Vehicle Warning
BSM：基本安全消息Basic Safety Message
BSW/LCW：盲区预警/变道预警Blind Spot Warning / Lane Change Warning
CAV：防撞距离Collision Avoidance Range
C-ITS：中国智能交通产业联盟China ITS Industry Alliance
CLW：车辆失控预警Control Lost Warning
CSAE：中国汽车工程学会Society of Automotive Engineers of China
DE：数据元素Data Element
DF：数据帧Data Frame
DME：专用短程通信管理实体DSRC Management Entity
DNPW：逆向超车预警Do Not Pass Warning
DSM：专用短程通信短消息DSRC Short Message
DSRC：专用短程通信Dedicated Short Range Communications
DTI：到交叉口的距离Distance-to-Intersection
HMI：人机交互界面Human Machine Interface
EBW：紧急制动预警Emergency Brake Warning
ESP：车身电子稳定系统Electronic Stability Program

ETC：电子不停车收费系统Electronic Toll Collection
ETSI：欧洲电信标准化协会European Telecommunications Standards Institute
EVW：紧急车辆提醒Emergency Vehicle Warning
FCW：前向碰撞预警Forward Collision Warning
GB：中国国家标准Guo Biao （Nation Standard）
GLOSA：绿波车速引导Green Light Optimal Speed Advisory
GNSS：全球导航卫星系统Global Navigation Satellite System
GPS：全球定位系统Global Positioning System
HLN：道路危险状况预警Hazardous Location Warning
HV：主车Host Vehicle
ICW：交叉路口碰撞预警Intersection Collision Warning
ID：标识Identification
ISO：国际标准化组织International Standards Organization
ITS：智能交通系统Intelligent Transport Systems
IVS：车内标牌In-Vehicle Signage
LDW：车道偏离预警系统Lane Departure Warning
LTA：左转辅助Left Turn Assistant
LTE-V2X：基于LTE的车载设备与其他设备通讯Long Term Evolution-Vehicle to EverythingNHTSA：美国高速公路安全管理局National Highway Traffic Safety Administration
OBU：车载单元On-Board Unit
P2P：点对点Point to Point
RSA：路侧单元发布的交通事件消息Road Side Alert
RSM：路侧单元消息Road Side Message
RSU：路侧单元Road Side Unit
RV：远车Remote Vehicle
SAE：国际自动机工程师学会Society of Automotive Engineers International
SLW：限速预警Speed Limit Warning
SPAT：信号灯消息Signal Phase and Timing Message
SPI：服务提供者接口Service Provider Interface
SVW：闯红灯预警Signal Violation Warning
TC：目标分类Target Classification
TCS：牵引力控制系统Traction Control System
TJW：前方拥堵提醒Traffic Jam Warning
TTC：碰撞预计时间Time-to-Collision
TTI：到达交叉口预计时间Time-to-Intersection
UPER：非对齐压缩编码规则Unaligned Packet Encoding Rules
V2I：车载单元与路侧单元通讯Vehicle to Infrastructure
V2P：车载单元与行人设备通讯Vehicle to Pedestrians
V2V：车载单元之间通讯Vehicle to Vehicle
V2X：车载单元与其他设备通讯Vehicle to Everything
VIN：车辆识别码Vehicle ID Number
VNFP：汽车近场支付Vehicle Near-Field Payment
VRUCW：弱势交通参与者碰撞预警Vulnerable Road User Collision Warning

## 3 相互距离计算

### 3.1 定位

#### 3.1.1 GPS原始定位

有效性

- 4颗以上，每颗信噪比S/N大于20
- 连续两次有速度，认为有车速，有车速更新GPS
- 每秒走过的距离大于60，GPS无效

#### 3.1.2 辅助定位

高精度地图

陀螺仪定位

### 3.2 车辆距离

#### 3.2.1 17个一期应用

![1563158083146](https://xuqi1987.github.io/2019/07/06/V2X%E4%B9%8BV2V%E4%BB%8B%E7%BB%8D/1563158083146.png)

## 4 V2V功能列表

### 碰撞预警FCW(Forward Collision Warning)

**FCW 基本工作原理如下：**

- 分析接收到的 RV 消息，筛选出位于**同一车道**前方（前方同车道）区域的 RV；
- 进一步筛选处于**一定距离范围**内的 RV 作为潜在威胁车辆；
- 计 算 每 一 个 潜 在 威 胁 车 辆 **碰 撞 时 间**（TTC：time-to-collision） 或 **防 撞 距 离**（collision avoidance range），筛选出与 HV 存在碰撞危险的威胁车辆；
- 若有多个威胁车辆，则筛选出最紧急的威胁车

**场景：**

![1563172581437](https://xuqi1987.github.io/2019/07/06/V2X%E4%B9%8BV2V%E4%BB%8B%E7%BB%8D/1563172581437.png)

![1563172589726](https://xuqi1987.github.io/2019/07/06/V2X%E4%B9%8BV2V%E4%BB%8B%E7%BB%8D/1563172589726.png)

![1563172597774](https://xuqi1987.github.io/2019/07/06/V2X%E4%B9%8BV2V%E4%BB%8B%E7%BB%8D/1563172597774.png)

**RV数据交互需求：**

![1563171827023](https://xuqi1987.github.io/2019/07/06/V2X%E4%B9%8BV2V%E4%BB%8B%E7%BB%8D/1563171827023.png)

##### 追尾碰撞

车速>= 15 km/h 时，同车道上同向行驶预计 3S 后将发生碰撞时给用户预警，
计算公式为：两车距离 m /(自车车速 m/s - 前车车速 m/s)

##### 正面碰撞 （异常行驶）

车速>= 15 km/h 时，同车道上反向行驶预计 3S 后将发生碰撞时给用户预警，
计算公式为：两车距离 m /(自车车速 m/s + 前车车速 m/s)

##### 高速跟驰碰撞

车速>=60km/h 时，同车道上与前车距离<= 3m 时给用户预警

##### 低速跟驰碰撞

15km/h > 车速 > 2km/h 时，同车道上与前车距离<= 0.5 M 时给用户预警

##### 贴车碰撞

车速> 0 km/h 时，同车道上前车与自车距离< 0.2M 时给用户预警

- 较高精度

### 路口碰撞预警ICW（Intersection Collision Warning）

**ICW基本工作原理如下：**

- 分析接收到的RV消息，筛选出位于**交叉路口左侧**（intersecting left）或**交叉路口右侧**（intersecting right）区域的RV。RV消息可能是由RV发出或从路侧单元获取；
- 进一步筛选处于**一定距离范围**内的RV作为潜在威胁车辆；
- 计算每一个潜在威胁车辆**到达路口的时间**（TTI：time-to-intersection）和**到达路口的距离**（DTI：distance-to-intersection），筛选出与HV存在碰撞危险的威胁车辆；
- 若有多个威胁车辆，则筛选出最紧急的威胁车辆；
- 系统通过HMI对HV驾驶员进行相应的碰撞预警。

**场景：**

![1563172631192](https://xuqi1987.github.io/2019/07/06/V2X%E4%B9%8BV2V%E4%BB%8B%E7%BB%8D/1563172631192.png)

![1563172661703](https://xuqi1987.github.io/2019/07/06/V2X%E4%B9%8BV2V%E4%BB%8B%E7%BB%8D/1563172661703.png)

##### 短距离碰撞预警

自车车速 > 15km/h 时，根据车速、地图上距离计算出两车路线有交叉，且两车在都没有过交差点前最近距离<= 10 M/6M 时，提示用户交叉路口碰撞危险。注意：某方过交叉点后变为与另一方反向或同向行驶时，两车即使距离<= 10M 时不用提示用户。

**注：**1) 当存在同一方向上有若干辆车同时满足前置条件时，报警界面上同一方向只显示一辆报警车；
\2) 当存在在不同方向上共有若干辆车同时满足前置条件时，**报警界面上不同方向上都需要显示一辆报警车；**

##### 短时间内碰撞预警

自车车速 > 15km/h 时，根据车速、地图上距离计算出两车分别经过交叉点时时间间隔 < 1S 时，提示用户交叉路口碰撞危险。

##### 高速车辆预警

自车车速 > 5km/h 时，行驶到距离**路口两车交叉点** 20M 以内时，如果检测到横向车道 50M 内有车辆即将与自车交汇，且对方车速>= 60M 时，提示用户路口碰撞危险。

##### 同方向车辆预警

自车车速 > 15km/h 时，行驶到距离**路口交叉点** 50m 以内时，如果检测到**横向右侧**车道上**第一辆车打开右转方向灯时**，提示用户路口碰撞危险。

注：怎么判断打开

##### 对方减速碰撞预警

距离路口交叉点 50m 以内，自车车速 > 30km/h 时，如果对方将先到交叉点，且对方车速 > 60 km/h 时，提示用户交叉路口碰撞危险。

##### 对方加速碰撞预警

距离路口交叉点 50m 以内，自车车速 > 30km/h 时，如果对方后到交叉点，且对方车速 < 30km/h 时，提示用户交叉路口碰撞危险。

？？ 30< <60怎么办？如果对方停在交叉路口，一直报警吗？

### 前车刹车预警（Brake Warning System）

紧急制动预警（EBW：Emergency Brake Warning）是指，主车（HV）行驶在道路上，与前方行驶的远车（RV）存在一定距离，当前方RV进行紧急制动时，会将这一信息通过短程无线通信广播出来。HV检测到RV的紧急制动状态，若判断该RV事件与HV相关，则对HV驾驶员进行预警。本应用适用于城市郊区普通道路及高速公路可能发生制动追尾碰撞危险的预警。
EBW应用辅助驾驶员避免或减轻车辆追尾碰撞，提高道路行驶通行安全。

##### 非低速刹车预警

车速>=15km/h时，同车道上前车与自车范围在A内刹车时，提示用户前车刹车预警。 A = 4 * (自车车速m/s)

##### 低速刹车预警

车速 < 15km/h时，同车道上前车与自车范围在A内刹车时，提示用户前车刹车预警。 A = 16 M

**工作原理：**

- RV紧急制动时间，广播这一信息。
- HV接收到RV的信息，判断是否是紧急制动事件。
- HV将出现紧急事件

EBW包括如下主要场景：
a）同车道（或相邻车道）HV前方紧邻RV发生紧急制动（图18）：
1）HV行驶在道路上，RV发生紧急制动事件；
2）HV和RV需具备短程无线通信能力；
3）EBW应用对HV驾驶员发出预警，提醒驾驶员前方紧急制动操作存在碰撞危险；
4）预警时机需确保HV驾驶员收到预警后，能有足够时间采取措施，避免与RV发生追尾碰撞。

![1564103642053](https://xuqi1987.github.io/2019/07/06/V2X%E4%B9%8BV2V%E4%BB%8B%E7%BB%8D/1564103642053.png)

![1564103730655](https://xuqi1987.github.io/2019/07/06/V2X%E4%B9%8BV2V%E4%BB%8B%E7%BB%8D/1564103730655.png)

### 左转弯分析（Left Turn Assistant）

**场景**

![1563172721792](https://xuqi1987.github.io/2019/07/06/V2X%E4%B9%8BV2V%E4%BB%8B%E7%BB%8D/1563172721792.png)

**ICW基本工作原理如下：**

- 接收RV消息，筛选出相邻车道的迎面车辆（oncoming left）和远端车道迎面车辆（oncoming far left）

### 特种车辆避让提醒（如救护车、消防车等）（Special Vehicle Avoid Notification）

- 同路提醒
  - 占道提醒
  - 邻道提醒
- 交汇提醒

### 高速安全距离提醒（Freeway Safety distance Notification）

- 200M以上
- 100M以上
- 100米内

### 故障车提醒

- 发送故障信息
- 故障车

### 危险路段提醒

- 发送ESP警告信息
- 接收ESP警告信息

### 倒车预警

- 自车倒车提示
- 前车倒车提示

### 警告信息历史记录

### 车队应用（跟驰、对讲）

- 组车队
- 跟驰
- 对讲