- [[LTE 协议\]概述 LTE协议_Love_marginal的博客-CSDN博客_lte协议](https://blog.csdn.net/m0_37495408/article/details/104902603)

### 【**LTE****协议栈的两个面**】

用户面协议栈——负责用户数据传输

控制面协议栈——负责系统信令传输

**在架构中的位置**

横跨UE和eNB

 

![img](https://img-blog.csdnimg.cn/20200316171958505.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3NDk1NDA4,size_16,color_FFFFFF,t_70)用户面协议架构

![img](https://img-blog.csdnimg.cn/202003161719118.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3NDk1NDA4,size_16,color_FFFFFF,t_70)控制面协议架构

**用户面主要功能**：

- 头压缩、加密、调度、ARQ/HARQ（自动重传请求/混合自动重传请求）

**控制面主要功能**：

- PDCH层完成**加密**与**完整性**保护；
- RLC和MAC层功能与用户面中的功能一致；
- RRC完成**广播**、**寻呼**、**RRC连接**管理、**资源控制**、**移动性**管理、**UE测量**报告与控制；我的[这篇博文](https://blog.csdn.net/m0_37495408/article/details/104878269)对RRC进行了详细描述
- NAS层完成**核心网承载**管理、鉴权及安全控制

### 【**用户平面与控制平面协议栈中共有的****LTE数据****链路层（L2）**】

> ARQ：Automatic Repeat-reQuest，自动重传请求，是OSI模型中数据链路层的**错误纠正协议**之一。
>
> HARQ，Hybrid Automatic Repeat reQuest，混合自动重传请求，是一种将前向纠错编码（FEC）和自动重传请求（ARQ）相结合而形成的技术。

**LTE数据链路****层****含有三种协议：**

- PDCP（Packet Data Convergence Protocol）：分组数据汇聚协议
- RLC（Radio Link Control）：无线链路控制
- MAC（Medio Access Control）：媒体接入控制

**LTE数据链路****层主要功能：**

- 调度、优先级处理、复用/解复用、*HARQ*；
- 分段/串接、*ARQ*；
- 头压缩、加密

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2kvNTA5Mzc0LzIwMTQwNy8yMzE3NTI1MjcxMzk5MTIueC1wbmc?x-oss-process=image/format,png) **数据链路** **层** 下行功能框架图

 

 

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2kvNTA5Mzc0LzIwMTQwNy8yMzE3NTQxMTcyOTQ4MzkueC1wbmc?x-oss-process=image/format,png) **数据链路** **层** 上行功能框架图

​                  

**MAC****、****RLC****、****PDCP****各自的功能如下：**

\1) MAC层的主要功能：

- 逻辑信道（logical channel）与传输信道（transport channel）之间的映射；
- 复用过程，即将RLC层的协议数据单元PDU（Protocol Data Unit）复用到传输块TB（transport block）中，然后通过传输信道传输到物理层。相反的过程即为解复用过程；
- 业务量测量报告；
- 通过HARQ纠错；
- 对单个UE的逻辑信道优先级处理；
- 多个UE间的优先级处理（动态调度）；
- 传输格式选择；
- 填充

\2) RLC层的主要功能：

- 上层协议数据单元PDU的传输支持确认模式AM（Acknowledge Mode）和非确认模式UM（Un-acknowledge Mode）；
- 数据传输支持透传模式TM（Transparent Mode）；
- 通过ARQ纠错（无需CRC校验，由物理层提供CRC校验）；
- 对传输块进行分段（segmentation）处理，仅当RLC SDU不完全符合TB大小时，将SDU（Service Data Unit）分段到可变大小的RLC PDU中，而不用进行填充；
- 对重传的PDU进行重分段Re-segmentation处理：仅当需要重传的PDU 不完全符合用于重传的新TB大小时，对RLC PDU进行重分段处理；
- 多个SDU的串接（Concatenation）；
- 顺序传递上层PDU（除切换外）；
- 协议流程错误侦测和恢复；
- 副本侦测；
- SDU丢弃；
- 复位；

> 【**补充说明**】
>
> - 透明模式(TM)
>
> ​       不添加RLC头
>
> ​       可以分段/级联
>
> - 非确认模式(UM)
>
> ​      必须添加RLC头
>
> ​      两种传送数据方式:检测且将没有出错的数据传递到高层;立即传递到高层
>
> - 确认模式(AM)
>
> ​      必须添加RLC头
>
> ​      无错传递(通过重发机制保证)
>
> ​      顺序传递或无序传递(仅用于上行切换）
>
> ​      唯一传递(相同检测功能)

\3) PDCP层的主要功能

-  用户平面的功能

​        头压缩/解压缩：ROHC（Robust Header Compression）；

​        用户数据传输：接收来自上层[NAS](https://so.csdn.net/so/search?q=NAS&spm=1001.2101.3001.7020)层的PDCP SDU，然后传递到RLC层，反之亦然；

​        RLC确认模式AM下，在切换时将上层PDU顺序传递；

​        RLC确认模式AM下，在切换时下层SDU的副本侦测；

​        RLC确认模式AM下，在切换时将PDCP SDU重传；

​        [加密](https://so.csdn.net/so/search?q=加密&spm=1001.2101.3001.7020)；

​        基于计时器的上行SDU丢弃

- 控制平面的功能

​        加密及完整性保护；

​        控制数据传输：接收来自上层RRC层的PDCP SDU，然后传递到RLC层，反之亦然。

### 【**E-UTRAN****接口通用模型**】

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2kvNTA5Mzc0LzIwMTQwNy8yMzE4MDEwNjg1NDMxMTYueC1wbmc?x-oss-process=image/format,png)

- 适用于E-UTRAN相关的所有接口，即S1和X2接口
- 控制面和用户面相分离，无线网络层与传输网络层相分离
- 无线网络层：实现E-UTRAN的通信功能
- 传输网络层：采用IP传输技术对用户面和控制面数据进行传输