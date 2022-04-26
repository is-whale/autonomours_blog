- [CAN总线-增强诊断服务篇2---网络传输_谢银森的博客-CSDN博客](https://blog.csdn.net/xieyinsen/article/details/123927023)

# 资料来源15765

ISO 15765-2的第一版为ISO 15765-2：2004。ISO 15765的中文含义为道路车辆 - 基于CAN网络的诊断通信（DoCAN），整套协议由以下部分组成：
\- 第1部分：一般信息和用例定义
\- 第2部分：传输协议和网络层服务
\- 第3部分：统一诊断服务的实施（CAN上的UDS ）
\- 第4部分：与排放有关的系统的要求



# 术语



客户端：诊断仪

服务端：ECU

物理寻址：客户端和服务端一对一的关系

功能寻址：客户端和服务端一对多的关系

安全状态：ECU 默认状态为安全状态，通过0x27服务切换为非安全状态（扩展和会话）

诊断会话模式：

1、默认

2、扩展

3、会话（编程）

协议数据单元：

​    1、标识--寻址信息

​    2、协议控制信息 PCI

3、数据 Data

A_PDU:应用层协议数据单元

BS：Block Size 连续帧发送次数

CF：连续帧

FC：流控帧

FF：第一帧

FS：流状态

ID：CAN 数据标识

PCI：协议控制信息

NRC：否定响应码





# 诊断概述

整车拓扑：

数据来源开源

![img](https://img-blog.csdnimg.cn/42e9eb619b91407197d91e90ee9ee0a3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)

 [BMW 总线系统详解](http://www.360doc.com/content/18/1005/00/52908041_792061373.shtml)

[奥迪A8的整车网络架构及供电系统](http://www.360doc.com/content/20/0902/22/68188258_933676892.shtml)



CAN 标识符 11 bit 也就是7XX (111= 3 )+ 4+4 = 11

0x700 开始 请求 ID 

应答 ID + 0x0偏移地址8 

0x700请求 0x708应答



# 网络层  

普通 can 报文协议单元：

| ID            | DLC                 | DATA |        |
| ------------- | ------------------- | ---- | ------ |
| 7(bit10~bit8) | N(A)_AI(bit 7~bit0) | 8    | L_data |

普通 CAN 信息仅仅通过以上报文（ ID 和DATA 区）就可以完成状态查询和控制下发的指令。

但是对应诊断涉及超过8字节的数据传输控制问题，引入网络层。同时区分诊断和普通服务区别即使少于8字节也通过单帧来标识。

网络层：

​    共计8字节，这样有效数据就是7字节，N_PCI 告知对方自己是类型的数据

| L_data |        |
| ------ | ------ |
| N_PCI  | N_data |

网络层帧的分类：

​        单帧 SF 

​        连续帧FF

​        流控帧FC

​        后续帧CF

![img](https://img-blog.csdnimg.cn/17c7ccf5318e49f98a1073887e390bce.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)

 下图偏移地址02 发送 06 应答 0x04 应答。

ID741 请求 ID0x7C1 应答 ID 偏移：0x08

SN 为后续帧序号，比如图中21 22 23 其中2 代表后续帧、1 、2、3代表序号

当SN的值**计到0xF时，SN的值在发送的下一个后续帧时重置为0**。再依次往上计数。

FF_DL 代表要发送的数据长度，理论少于4095 因为长度最长12bit

流控帧 FS 流控状态：

​    ![img](https://img-blog.csdnimg.cn/d47353d77c65487eaadc06606970f301.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)

BS：告知发送端，在没有接收端流控帧期间，最多能发送的连续帧数量。

![img](https://img-blog.csdnimg.cn/303ef0103bca48348d87c72607b45722.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)

STmin：**相邻的后续帧所允许的最小时间间隔**

![img](https://img-blog.csdnimg.cn/de259423547248d09aa6bf907365ae1e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16) 

 

![img](https://img-blog.csdnimg.cn/fce047a538d846e48057c4fab0bd0cdc.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)

 

帧的流控处理

![img](https://img-blog.csdnimg.cn/d438246368604245a05f2feb91d77a12.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)

 

![img](https://img-blog.csdnimg.cn/bfb43cee84d0402abf2b61574b778063.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)

 其他例子

![img](https://img-blog.csdnimg.cn/8e2ac107b54943bb91a2f0b0694a3879.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)

![img](https://img-blog.csdnimg.cn/5b50ae13093540ab95b82ddb15cb3f6c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16) 

 

 [ISO 15765-2（网络层服务）_第55号小白鸭的博客-CSDN博客_15765-2](https://blog.csdn.net/weixin_44536482/article/details/98652882)

[UDS(二)网络层_车小猿的博客-CSDN博客_uds 流控帧](https://blog.csdn.net/weixin_44522306/article/details/113104819)

# 应用层





# 增强诊断服务