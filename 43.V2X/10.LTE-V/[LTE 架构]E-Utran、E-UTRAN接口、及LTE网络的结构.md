- [[LTE 架构\]E-Utran、E-UTRAN接口、及LTE网络的结构_Love_marginal的博客-CSDN博客_eutran和utran区别](https://blog.csdn.net/m0_37495408/article/details/104841243)

# 定义

在LTE网络（即俗称的4G网络）中，因为演进关系，我们将接入网部分称为E-UTRAN（Evolved UMTS Terrestrial Radio Access Network，演进的UMTS陆地无线接入网），即**LTE中的移动通信无线网络**。

# [架构](https://so.csdn.net/so/search?q=架构&spm=1001.2101.3001.7020)中的定位

历史版本为UTRAN（陆地无线接入网），是**3G网络的接入网**。

作为连接[EPC（4G核心网络）](https://baike.baidu.com/item/EPC/14767302)和UE（用户设备）的**中间结构**，向上通过S1与4G[核心网](https://so.csdn.net/so/search?q=核心网&spm=1001.2101.3001.7020)连接，向下通过Uu接口与用户设备相连

# 结构

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9ia2ltZy5jZG4uYmNlYm9zLmNvbS9waWMvNjNkOWYyZDM1NzJjMTFkZmRmNTMyMmQ0NjkyNzYyZDBmNjAzYzI0Mw?x-oss-process=image/format,png)

通信结构的最上层称作核心网，上图中的核心网为EPC[EPC的内容可见我的[这篇博文](https://blog.csdn.net/m0_37495408/article/details/104868840)]

中间部分负责核心网与UE的连接，称作接入网，上图中的接入网为E-UTRAN

| 缩略语  | 中文                                  | 概念范围      |
| :------ | :------------------------------------ | :------------ |
| EPS     | 演进分组系统                          | 接入网+核心网 |
| EPC     | 演进分组核心网                        | 核心网        |
| E-UTRAN | 演进通用接入无线网                    | 核心网        |
| SAE     | 研究系统结构演进（与EPC重叠）         | 核心网        |
| LTE     | 研究无线接口长期演进（与E-UTRAN重叠） | 接入网        |

 

##  与UTRAN的区别

![img](https://img-blog.csdnimg.cn/20200313150822871.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3NDk1NDA4,size_16,color_FFFFFF,t_70)

 上图表示了两者的区别，其中左侧为UTRAN（3G），右侧为E-UTRAN（4G）。

E-UTRAN由多个eNB（演进的NodeB）组成，相比于左侧的NodeB（代表基站），在包含NodeB的功能外，还**承担了RNC（无线网络控制器）的大部分功能**。由此接入网的组成变得更加简洁【扁平化】。

此外，E-UTRAN中的eNB接口：

- eNodeB之间由X2接口相互连接，支持数据和信令的直接传输；
- eNodeB与EPC之间由S1接口连接，其中：S1-MME是eNodeB连接MME的控制面接口，S1-U是eNodeB连接S-GW的用户面接口。[[1\]](https://www.cnblogs.com/kkdd-2013/p/3863267.html)

# eNB的主要功能

1. 无线资源管理相关功能，包括：无线承载控制、接纳控制、连接移动性管理、上/下行动态资源的分配与调度；
2. IP头压缩与用户数据流加密；
3. UE附着时的MME选择；
4. 提供到业务网关（S-GW）的用户面数据的路由；
5. 寻呼消息的调度（scheduling）与传输（transmission）；
6. 系统广播消息的调度与传输；
7. 测量与测量报告的配置。

 

# 接口的示意图

![img](https://img-blog.csdnimg.cn/20200308201428676.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3NDk1NDA4,size_16,color_FFFFFF,t_70)以LTE D2为baseline的LTE V2X的结构

为了更直观的展现架构中各成分之间的通信接口，借用了我读过的[这篇论文](https://blog.csdn.net/m0_37495408/article/details/104747877)中的图片，此图片是LTE-V2X的架构接口图。其中E-UTRAN就是我们所关注的对象，他是由eNB构成的。

## 【**E-UTRAN****网络接口**】[1]

![img](https://img-blog.csdn.net/20160509210258828)

### **S1接口**

  **S1—用户平面**

​          ![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2kvNTA5Mzc0LzIwMTQwNy8yMzE4MTAzNjc3NTkyMzQueC1wbmc?x-oss-process=image/format,png)

​    S1接口用户平面（eNodeB~S-GW）

   S1用户面接口（S1-U）是指连接在eNodeB和S-GW之间的接口， S1-U接口提供eNodeB和S-GW之间用户平面PDU的非保障传输。S1接口用户平面协议栈如上图所示，传输网络层建立在IP层之上，并且位于UDP/IP 之上的GTP-U 用于在eNodeB和S-GW之间传输用户平面PDU。

**附：**

- 使用GTP用户平面的一个优点是它固有的可鉴别隧道的机制并支持3GPP内部的移动性。
- 对于用户平面协议栈，IP版本号和数据链路层是完全任选的。
- 一个传输承载是由GTP隧道端点和IP地址来鉴别的（源TEID（隧道端点标识）、目的TEID、源IP地址、目的IP地址）。
- S-GW将给定承载的下行数据包发送给与该承载相关的eNodeB IP地址。
- eNodeB将给定承载的上行数据包发送给该承载在相关的EPC Ip地址。

  **S1—控制平面**

​         **![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2kvNTA5Mzc0LzIwMTQwNy8yMzE4MTI1MDA3MjY3NzkueC1wbmc?x-oss-process=image/format,png)**

​    S1接口控制平面（eNodeB~MME）

   在IP传输层，点对点传输被用于传送信令PDU。每个S1-MME接口实例都关联一个单独的***SCTP*** ，与一对流指示标记作用于S1-MME 公共处理流程中。只有很少的流指示标记作用于S1-MME 专用处理流程中。

   MME分配的针对S1-MME 专用处理流程的MME通信内容指示标记，以及eNodeB分配的针对S1-MME 专用处理流程的eNodeB通信内容指示标记，都应当对特定UE的S1-MME信令传输承载进行区分；通信内容指示标记在S1-AP消息中单独传送。

>    SCTP（Stream Control Transmission Protocol）—流控制传输协议：是一种可靠的传输协议，它在两个端点之间提供稳定、有序的数据传递服务（非常类似于 TCP），并且可以保护数据消息边界（例如 UDP）。然而，与 TCP 和 UDP 不同，SCTP 是通过多宿主（Multi-homing）和多流（Multi-streaming）功能提供这些收益的，这两种功能均可提高可用性 。
>
> **SCTP** **提供如下服务**：
>
> \* 确认用户数据的无错误和无复制传输；
>
> \* 数据分段以符合发现路径[最大传输单元](http://baike.baidu.com/view/545115.htm)的大小；
>
> \* 在多[数据流](http://baike.baidu.com/view/166248.htm)中用户信息的有序发送，带有一个选项，用户信息可以按到达顺序发送；
>
> \* 选择性的将多个用户信息绑定到单个 SCTP 包；
>
> \* 通过关联的一个[终端](http://baike.baidu.com/view/105503.htm)或两个终端多重宿主支持来为网络故障规定容度。

### **X2接口**

  **X2—用户平面**

​          ![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2kvNTA5Mzc0LzIwMTQwNy8yMzE4MTU1NjQ2MzExNzAueC1wbmc?x-oss-process=image/format,png)

​      X2接口用户面(eNodeB-eNodeB)

​    X2 用户面接口(X2-U)是指连接在eNodeB之间的用户面接口， X2-U接口提供非保证的用户面PDU的交付。X2的用户面协议栈如上图所示，传输网络层是建立在IP传输上，GTP-U是在UDP/IP上承载用户面的PDU。

​    X2-UP接口协议栈和S1-UP协议栈是一样的。

  **X2—控制平面**

​           ![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2kvNTA5Mzc0LzIwMTQwNy8yMzE4MTc0MTE5Nzc0MDUueC1wbmc?x-oss-process=image/format,png)

​       X2接口用户面(eNodeB-eNodeB)

​     每 X2-C接口含一个单一的SCTP并具有双流标识的应用场景应用X2-C的一般流程。具有多对流标识仅应用于X2-C的特定流程，特定流程标识数目的上线是FFS。源eNodeB为X2-C的特定流程分配源eNodeB通讯的上下文标识，目的eNodeB为X2-C的特定流程分配目的eNB通讯的上下文标识。这些上下文标识用来区别UE特定的X2-C信令传输承载。通讯上下文标识通过各自的X2-AP消息传达。

【**S1****接口例程——承载管理**】

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2kvNTA5Mzc0LzIwMTQwNy8yMzE4MTkxNDQ2MzMwODAueC1wbmc?x-oss-process=image/format,png)

- 目的：在CN和eNodeB上为UE建立业务通道。
- E-RAB Setup Request主要信元：MME和eNodeB为UE分配的ID号，需要建立的SAE承载列表（具体包括SAE承载ID，承载的QoS参数信息，承载的传输地址等），NAS-PDU等。
- E-RAB Setup Response主要信元：MME和eNodeB为UE分配的ID号，建立成功的SAE承载列表以及没有建立成功的承载列表。

【**S1接口例程——上下文管理**】

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2kvNTA5Mzc0LzIwMTQwNy8yMzE4MTk1OTk3OTQ3NzMueC1wbmc?x-oss-process=image/format,png)

-  目的：在eNB中建立UE的初始上下文。
-  Initial Context Setup Request主要信元：MME和eNB为UE分配的ID号，需要建立的SAE承载列表（具体包括SAE承载ID，承载的Qos参数信息，承载的传输地址等），NAS-PDU，安全信息，切换限制列表，UE无线能力等。
-  Initial Context Setup Response主要信元：MME和eNB为UE分配的ID号，建立成功的SAE承载列表以及没有建立成功的承载列表。

【**S1****接口例程——切换资源分配**】

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2kvNTA5Mzc0LzIwMTQwNy8yMzE4MjEwMzg1NDQwMjYueC1wbmc?x-oss-process=image/format,png)

- 目的：通知目标eNB为即将切换过来的UE分配资源。
- Handover Request主要信元：MME和eNB为UE分配的ID号，切换类型，切换原因，需要为UE建立的SAE承载列表（具体包括SAE承载ID，承载的Qos参数信息，承载的传输地址等）。
- Handover Request ACK主要信元：MME和eNB为UE分配的ID号，切换类型, 成功建立的SAE承载列表以及没有建立成功的承载列表。

**【****S1****接口例程——寻呼**】

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMwLmNuYmxvZ3MuY29tL2kvNTA5Mzc0LzIwMTQwNy8yMzE4MjIxNTgyMjc4NzIueC1wbmc?x-oss-process=image/format,png)

- 目的：MME通过寻呼与处于IDLE状态的UE建立信令连接。
- Paging主要信元：要寻呼的UE的ID，寻呼原因，要寻呼的跟踪区列表。

 

Reference：

[1] https://www.cnblogs.com/kkdd-2013/p/3863710.html 接口说明全部来自这篇博文的后半段