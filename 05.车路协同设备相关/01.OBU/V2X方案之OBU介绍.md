- [V2X方案之OBU介绍_不懂汽车的胖子的博客-CSDN博客](https://blog.csdn.net/ChrisKKC/article/details/123055235?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-19-123055235.pc_agg_new_rank&utm_term=v2x协议栈&spm=1000.2123.3001.4430)

# OBU概要

OBU，直译就是车载单元的意思，就是采用DSRC技术，与RSU进行通讯的微波装置。在V2X方案中就是集C-V2X、高[精度](https://so.csdn.net/so/search?q=精度&spm=1001.2101.3001.7020)定位、4G/5G通信、等多功能V2X的5G智能车载终端，面向智能网联汽车、智慧交通领域，具有车路协同、智能计算、远程交互等功能。 基于C-V2X协议栈，与高级辅助驾驶ADAS、自动驾驶ADS深度融合，提供丰富的交通效率及安全等服务，满足高速公路场景下车辆主动安全，效率提升等需求，并支持拓展其他场景应用，满足对C-V2X技术在车联网IoV领域的应用需求。

# V2X车路协同架构]

![img](https://img-blog.csdnimg.cn/img_convert/762002a12cc9d3d582e0e52d309eeedd.png)

C-V2X系统包含两种通信接口:

• Uu 口是以运营商基站为中心的空口通信，实现V2N连接。典型特征是 大带宽连接互联网，实现广域的联网需求。当前阶段主要是4G/[5G](https://so.csdn.net/so/search?q=5G&spm=1001.2101.3001.7020)的空口通信。

• PC5 口是设备到设备的直接通信，该模式下不需要运营商的网络覆盖即 可实现自组网通信，典型特征是自组网、低时延，主要用于传输安全类 消息；当前阶段主要是LTE-V2X的空口。

OBU是C-V2X系统的车载单元，通过无线Uu 口连接云控中心和操作维护 中心。OBU通过PC5 口与RSU以及其他车辆实时通信，实现智能网联类应用。

RSU是C-V2X系统的路侧单元，通过无线Uu 口或者有线接口连接云控中 心和操作维护中心。RSU通过路侧感知设备实时获取道路信息，由PC5 口发送 给道路车辆OBU设备。

业务云实现C-V2X系统业务管理，数据分析与呈现，可以提供设备交通对象、道路状况监控及管理。

运维云实现C-V2X系统设备的远程管理、升级等。

# OBU特点

一般OBU需满足以下需求：

- 运行基于C-V2X协议栈应用，提供高级辅助驾驶、车路协同服务。
- 支持 WCDMA/FDD-LTE/TDD-LTE/NR 5G，以及后续各代移动通信服务。
- 多星座高精度卫星定位：BeiDou/GPS/GLONASS/Galileo/QZSS。
- 内置HSM模块，支持主流加密算法加速、验签、签名。
- 支持厘米级高精度定位，使用RTK差分定位技术 支持IMU惯导，包括加速度计、陀螺仪
- CAN：符合CAN2. 0B规范，支持2路高速CAN,每路速率可高达1 Mbps。
- C-V2X(Uu mode)：符合 3GPP Rel. 15 标准；
- 支持5G SA、NSA双模组网，5G和LTE-A多种网络制式的全面覆盖
- 支持全球主要地区和运营商的5G NR/4G/3G商用网络频段，数据峰值速率可达2. 5Gbps (DL) /650Mbps (UL)  ；
- 支持多种网络协议(PPP、IPv4/v6) ；
- 5G功能(网络切片管理、F0TA)；
-  C-V2X (PC5 mode4):符合 3GPP Rel. 14 标准，支持 T/CSAE 53-2017 应用层标准；工作频段：5905〜5925MHz；工作带宽：10 MHz或20MHz                                                   最大传输速率：30Mbps ；发射功率：-30〜23dBm               
- Ethernet：符合 IEEE1588 规范，10/100/1000Mbpso USB: 1 路
- USB 2. 0-host 接口。
- WiFi:支持 2.4GHz/5GHz 频段 Ua/b/g/n/ac,传输速率高达 433.3Mbps。
- 蓝牙:支持BT 4. 2+EDRo
- RS232:支持外接设备扩展。
- 支持高精度定位平台接入
- 支持车辆数据上传至V2X平台进行处理
- 支持对应app和数据管理平台开发
- 车载以太网

# OBU参数参考

![img](https://img-blog.csdnimg.cn/4d59d3acae734d9ba14aa8312beb58cf.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LiN5oeC5rG96L2m55qE6IOW5a2Q,size_13,color_FFFFFF,t_70,g_se,x_16)

![img](https://img-blog.csdnimg.cn/442000003374486b96a3cd9533e3630c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LiN5oeC5rG96L2m55qE6IOW5a2Q,size_13,color_FFFFFF,t_70,g_se,x_16)

#  OBU功能

![img](https://img-blog.csdnimg.cn/2680ab3a4eab49dabc742571818e5130.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LiN5oeC5rG96L2m55qE6IOW5a2Q,size_14,color_FFFFFF,t_70,g_se,x_16)

#  OBU射频性能

![img](https://img-blog.csdnimg.cn/236973b3d8144bd58c63148937d58590.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LiN5oeC5rG96L2m55qE6IOW5a2Q,size_14,color_FFFFFF,t_70,g_se,x_16)

![img](https://img-blog.csdnimg.cn/55a63a7ef7374d40ae670749103baca7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5LiN5oeC5rG96L2m55qE6IOW5a2Q,size_14,color_FFFFFF,t_70,g_se,x_16)

