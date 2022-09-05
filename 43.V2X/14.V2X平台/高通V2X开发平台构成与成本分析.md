- [高通V2X开发平台构成与成本分析_应用 (sohu.com)](https://www.sohu.com/a/427432422_391994)

高通目前有三套平台，一套以MDM 9150芯片组为核心，主要针对R14标准，以非网络的直接连接应用为主。第二套以MDM9250芯片组为核心，主要针对R15标准，以网络连接，提高交通效率应用为主。奔驰与宝马的下一代TCU已确定使用MDM9250。

第三套以SA2150P芯片组为核心，主要针对R16标准，从4G推进到5G NR领域，移远通信已经有基于SA2150P的产品。和前两代不同，SA2150P是应用处理器，专为V2X应用开发的处理器。此外高通还有针对5G的SA415/SA515M平台可选外挂V2X功能，SA151M核心就是骁龙X50 Modem，高通骁龙X50是单模的5G基带芯片，需要与4G LTE基带搭配使用，且骁龙X50设计之初就是为了28GHz频段上运行，更属意于高频毫米波（mmWave）方案，因此实际上在Sub－6GHz下的表现可能不尽人意。当然高通目前最新的有X55 Modem。

![img](http://p8.itc.cn/q_70/images03/20201026/95b49598512a4c309b10943f49bebf65.png)

上图为5GAA联盟的CTO在2020年10月对V2X量产应用时间线做的一个预测。高通的三代产品将可能会长期共存。目前还有频谱分配的问题没解决。在2020年10月5GAA联盟的白皮书里呼吁美国政府应该给予C-V2X足够的频谱带宽。

5GAA认为基础安全的ITS应用如5.9GHz下的V2V和V2I应用需要10-20MHz的带宽。先进ITS应用如5.9GHz下的V2I/P需要40MHz以上带宽，C-V2X里的V2N通讯，低于1GHz的低波段（乡村环境）Service-agnostic需要至少50MHz带宽，1-7GHz的中波段（城市环境）需要至少500MHz的带宽。

目前主要还是高通的二代产品。V2X目前频带主要为B46D和B47，B46D是全球标准，即5725MHz-5825MHz，B47是针对日本的，即5850-5925MHz。

![img](http://p5.itc.cn/q_70/images03/20201026/24b19ae7894c496d914cde294d8f8c37.png)

高通C-V2X平台架构

![img](http://p4.itc.cn/q_70/images03/20201026/dc172ee59a3143b9ad6a0be8ec0343ad.png)

高通第二代C-V2X开发平台框架图

![img](http://p0.itc.cn/q_70/images03/20201026/302572e9b5ef45ef89971bb244fdc2e3.jpeg)

上表为主要集成电路，价格仅供参考

高通第二代C-V2X开发平台主要部件包括MDM9250芯片组，其中有MDM9250 SoC，PMD9655电源管理，WTR5975收发器，LPDDR与NAND的MCP封装存储器为美光的MT29RZ4B2DZZHHWD，256MB的LPDDR2，512MB的NAND。还有5.9GHz的RFEE，即射频前端，包括功率放大器、天线开关、低噪音放大器和滤波器。应用处理器是高通的APQ8096AU，即车载版骁龙820，还有PM8996AU电源管理，三星的4GB LPDDR4，K4F6E3D4HB。2.4GHz，802.11n，WiFi与蓝牙4.2模块QCA6574AU。东芝的TC9560XBG是汽车级PCIe转以太网界面IC，支持单通道2代PCIe，支持EAVB协议，包括IEEE802.1AS、IEEE802.1Qav，拥有RGNII/RMII/MII的MAC。包括Cortex M3。

高通C-V2X开发平台包含NXP MPC5746（达到ASIL-D级）和NXP K61。K61是一个支持IEEE1588以太网界面和USB OTG的MCU。K61把轮速传感器传来的USB信号转变为SPI输出给MDM9250。IMU采用博世的BMI160，这是一个16比特3轴重力加速度计和3轴陀螺仪的IMU，不过博世现在推荐BMI270取代BMI160。

V2X采用HSM硬件加密，可以选择NXP的SFX1800或英飞凌的SLi97。SFX1800是特别为V2X开发的。

上图为NXP的V2X架构图，不过这是针对DSRC的，但在C-V2X中也可以用SFX1800，SFX1800采用ARM SC300核心，内含2MB的Flash。SFX1800采用NXP的Java Card OperatingSystem (JCOP) 操作系统，密码保护支持IEEE1609.2和ETSI TS 103 97密码学标准。

Sli97主要为eCall和V2X开发的，其中Sli97CSI专为V2X设计，拥有1MB Flash，界面为SO7816，支持I2C和SPI接口。

![img](http://p5.itc.cn/q_70/images03/20201026/341e9fc5a6e647e4842016b8687c89f3.png)

![img](http://p3.itc.cn/q_70/images03/20201026/09d99b0065d24062bbb6b0f2013dd361.png)

上图为高通SA2150P的内部框架图，采用低成本的4核A53设计，也去掉了高成本的GPU，用ARM NEON的SIMD设计来应对并行计算，成本比APQ8096AU要低至少一半。高通C-V2X开发平台主要集成电路大约200美元，将来APQ8096AU会被SA2150P取代，价格估计会降低20-25美元。

![img](http://p5.itc.cn/q_70/images03/20201026/cd8f8401f29f4907a77d0451c253e5fe.png)

天线方面采用两个4芯FAKRA，分别针对WLAN和C-V2X，还有一个针对GPS的FAKRA接口。此外如果考虑欧洲标准，还要考虑eCall，还要增加A2B数字音频，高通开发平台使用ADI的AD2410WCCSZ，还有音频CODEC，使用德州仪器的TLV320AIC3104IRHBT。需要增加20脚音频与麦克风连接器，还有一个包含CAN、OABR物理层和12V供电的20脚连接器。当然还得有电池。

![img](http://p9.itc.cn/q_70/images03/20201026/d3f7f327447745419cc24496f200daf8.png)

上图为协议栈架构，实际Facilities就是智能交通ITS的协议栈

![img](http://p1.itc.cn/q_70/images03/20201026/c0ccc8a808cf4354989fa3dd23e23e45.png)

上图为V2X协议栈。上层协议栈或者说ITS协议栈，欧美起步都很早，这主要是欧美从1999年就开始构思了利用无线通讯打造ITS系统的构思并付诸实施。欧洲电信标准协会即ETSI从2008年就开始着手制定ITS协议栈标准，目前已经基本制定完成。不过是基于DSRC的，但转移到C-V2X的速度很快，毕竟只是通讯方式的不同。

2020年1月，ETSI释放了ETSI EN 303 613接入层标准，将来还有ETSI TR 101 607标准。美国方面，比欧洲稍晚，大约在2010年开始制定以DSRC为通讯方式的标准，即SAE J3161。2019年则有针对C-V2X的J2945标准。

![img](http://p4.itc.cn/q_70/images03/20201026/946aa4f48b8c4c98a3d7efca2aa8dc58.png)

DSRC与C-V2X协议栈的对比，上层差别甚微。

![img](http://p6.itc.cn/q_70/images03/20201026/abc2e59053ce4dfc8e9dca27fd4554ed.png)

高通在第二代MDM9250开发平台上提供基于SAE和ETSI标准的ITS协议栈。同时也支持第三方ITS协议栈。对SAE来说，关键的ITS信息包括BSM（Basic Safety Message，即SAE J2735），Emergency VehicleAlert (EVA) ，Signal Phase and Timing (SPaT)，Map Data（MAP），TravelerInformation Message (TIM)。对ETSI来说，关键信息包括DecentralizedEnvironmental Notification Message (DENM) ， Cooperative AwarenessMessage (CAM)，Signal Phase and Timing (SPaT)，LDM（本地动态地图）。

![img](http://p0.itc.cn/q_70/images03/20201026/fe47dad3500c44f0b8478a1db1f3d20f.png)

中国目前已经基本完成的标准如上表，主导力量是CCSA，即中国通信标准化协会，其余还有C-ITS中国智能交通产业联盟，中国汽车工程协会C-SAE，国家汽车标准化委员会NTCAS，车载信息服务产业应用联盟TIAA。

还需要制订的标准如下表所列。

![img](http://p7.itc.cn/q_70/images03/20201026/878171edaddf4513800469a2ea16bac8.png)

![img](http://p5.itc.cn/q_70/images03/20201026/d8e76b0061a649ed9aebf39b97af545b.png)