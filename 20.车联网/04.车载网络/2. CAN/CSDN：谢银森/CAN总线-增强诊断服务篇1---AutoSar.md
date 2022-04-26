- [CAN总线-增强诊断服务篇1---AutoSar_谢银森的博客-CSDN博客](https://blog.csdn.net/xieyinsen/article/details/121548115)

OBD 增强型诊断服务设计。本文计划总结14229 15331 诊断的实现，

先总结一个概念，Autosar

资料参考：

![img](https://img-blog.csdnimg.cn/70cc6581528a432e8e54958e804e7e92.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)



AutoSar 型诊断设计。

# 什么是 Autosar

一套国际通用的车载软件标准组织。

包括了车载软件设计、车载以太网、车载诊断、车载网络管理等多个维度，像硬件级维度没有接触。

## 软件设计：

将软件层分为应用层、RTE(运行时环境)、基础软件层。

应用层：纯粹的软件层，不需要关注底层的实现。

RTE：运行时环境。Autosar 核心。

定义了软件层和底线硬件、系统的交互接口。定义了软件应用之间的接口。（多 ECU 系统和单 ECU 系统）

基础运行层：向应用层提供基础服务，包括硬件驱动、总线、网络通信、实时任务调度、汽车诊断服务。包含80个基础模块。（比如 E2PROM 驱动）。

基础运行层从底网上分为微控制器抽象、ECU 抽象、服务、复杂驱动。

### 诊断通信管理模块：DCM

​    能够处理0x10\0x27\0x3E服务。

​    DCM 接受OBD 服务请求，调用 DEM 、应用层软件提供的接口进行处理。

​    分为三个子模块：DSL、DSD、DSP

​    **DSL：**

​         接受诊断请求和数据流建立。

​          请求处理、响应处理、安全等级处理、会话状态处理、诊断协议处理、

​          通信模式处理。 

​    **DSD：**

​        接受 DSL 信息、

​        验证诊断会话服务、服务安全等级、找对应的安全  服务ID表。

​        如果支持该请求，则传给 DSP 模块。        

​    **DSP：**

​        **1、分析请求信息（每个模块都会做）**

​        **2、检查信息格式、确认是否支持。**

​        **3、执行函数**

​        **4、汇总响应。**

### 诊断事件管理模块：DEM Diagnostic EVENT Manager

​    故障检测、和故障存储。（DTC往 flash 和 EEPROM 存储）。

​    **事件状态管理：**

​            **诊断出现后、生成 DTC 码。**

​    **事件相关数据：**

​            **软件内部信息和环境信息的存储。**

​    **事件存储操作：**

​        **EEPROM 或者 FLASH**    

### 硬件在环故障注入：

​    使用 ECU虚拟车辆、进行故障测试。

### 诊断算法模块：

​    比如 IVI 、TBOX、发动机单元诊断模块的设计。    

## AuotoSar 通信协议栈：

AUl’oSAR COM从上到下包括五层协议，分别是COM层、PDU层、 CAN 转发、接口层、驱动层，其中驱动层包括了控制器与收发器 的驱动。

![img](https://img-blog.csdnimg.cn/b1c9074662124a14b75e571c4ed6cde2.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)

# OBD诊断：

服务总揽

![img](https://img-blog.csdnimg.cn/5c929360c10746018b06f46040716424.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)