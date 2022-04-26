- [[LTE 信道\]控制信道 PUCCH PUSCH PSCCH PSSCH_Love_marginal的博客-CSDN博客_lte控制信道](https://blog.csdn.net/m0_37495408/article/details/104844125)

## 前言

在论文中看到PSSCH和PSCCH，后来又见PUCCH等，在这里干脆就整理一下这些CCH.

# 控制[信道](https://so.csdn.net/so/search?q=信道&spm=1001.2101.3001.7020)CC

控制信道 (control channel)在多信道共用系统中，主要用于**传送信令或同步数据**，又记作CC。在模拟蜂窝系统中，主要由寻呼及接入信道组成。而在数字蜂窝系统中，主要由广播信道、公共控制信道和专用控制信道构成。 

## 在[架构](https://so.csdn.net/so/search?q=架构&spm=1001.2101.3001.7020)中的位置

作为管道而存在的控制信道，根据完成的功能存在多个细分：

- 广播信道 BCH (Broadcast Channels Channel)
  - 频率矫正信道 FCCH
  - 同步信道 SCH
  - 广播控制信道 BCCH
- 公共控制信道 SCH (Synchronization Channel)
  - 寻呼信道 PCH
  - 随机接入信道 RACH
  - 允许接入信道 AGCH
- 专用信道 CCCH (Common Control Channels)
  - 独立专用控制信道 SDCCH
  - 小区广播信道 CBCH
  - 慢速随路控制信道 SACCH

# PUCCH & PUSCH

因为在论文中遇到了用于这两者相关解调的DMRS（解调参考信号），因此先对这两者进行整理吧、

## PUCCH

PUCCH，Physical Uplink Control Channel，物理***上行控制信道\***。

UE为支持上下行数据传输，需要给L1/L2发送控制信息（称之为上行控制信息，UCI），这些信息通过PUCCH来发送。

### 在架构中的位置

> L1：物理层；L2：数据链路层；L3：网络层；L4：传输层；L5：会话层；L6：表示层；L7：应用层

用于传输**UE**的**上行**控制信息

### 限制

- 一个UE在一个TTI（调度周期）内至多发送一个PUCCH
- 对TDD而言，PUCCH不能在特殊子帧上发送

更加详细的信息请见[这篇博文](http://blog.sina.com.cn/s/blog_927cff010101alpi.html)

## PUSCH

PUSCH，Physical Uplink Share Channel，物理层***上行共享信道***。除了可以传输控制信息外，还可传输数据。

### 在架构中的位置

用于传输**UE**的**上行**信息，又叫导频信道，是UE与基站建立通信的基础

# PSCCH & PSSCH

> **Sidelink**：只在物理层适用，不携带来自上层的消息。Sidelink物理信号定义为：解调参考信号、同步信号

## PSCCH

PSCCH，Pysical Sidelink Control Channel 物理Sidelink控制信道，用于传输SCI(Sidelink Control information)控制信息。

## PSSCH

PSSCH， Pysical Sidelink Share Channel，物理sidelink共享信道。

### 在架构中的位置

在**UE间**传输数据，**不参与**跨层的信息传输。

与PSCCH共同组成一个PSCCH周期传输的帧