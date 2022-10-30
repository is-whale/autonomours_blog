- [车联网T-BOX小结_panamera12的博客-CSDN博客_tbox车联网](https://blog.csdn.net/wteruiycbqqvwt/article/details/120526194?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-3-120526194.pc_agg_new_rank&utm_term=rsu协议&spm=1000.2123.3001.4430)

T-BOX，telematics box ，远程通信模块，从名字即可看出其核心功能是给车辆赋予联网能力。

从[TBOX](https://so.csdn.net/so/search?q=TBOX&spm=1001.2101.3001.7020)功能的演进过程可以看到两点：

1、TBOX作为量产件产生的最初原因是法规等要求的数据传输，但在法规未强制时，TBOX已经在车厂具备形态。**车，联网，是万物互联时代的大势所趋；**

2、同时，TBOX是在基于传统车辆的功能和[架构](https://so.csdn.net/so/search?q=架构&spm=1001.2101.3001.7020)衍生出的新模块，因此，**TBOX的功能定义、硬件形态等都具有架构的时代特征**，与网络相关的功能，基本都是体现在TBOX上，比如远控、OTA、远程诊断等。

接下来从宏观到微观对T-BOX分析。

![img](https://img-blog.csdnimg.cn/20210928114732432.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

整个智能网联通讯系统架构是由车端、通道、云端、后端、智能终端组成的。

对此细分，车端包括T-BOX、网关、各种控制器，网络通道包括接入基站、运营商核心网、后端包括OTA平台、TSP、呼叫中心等在内的各种业务网络服务器，以及业务后端，如国家监控平台、新能源监控平台、售后监控平台等数据运维平台，智能终端则是特指APP的承载硬件。

以TBOX为主的通讯系统的通用功能，都需要依托这样的系统架构实现。

T-BOX在车端电子架构中的位置，基本是独立一路TBOX域，或者在信息娱乐域。通常与EHU通过USB或ETH连接，为EHU供网。

简化T-BOX车端接口如下：

![img](https://img-blog.csdnimg.cn/20210928114956744.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

TBOX自身接口包括Call 按键信号输入、按键检测，音频输入输出，射频天线、无线通信天线、内置WIFI/BT天线等。

 功能对手件包括EHU\BCM\VCU\ACU等，具备高精度定位功能的整车，如果不将高精度定位硬件集成在TBOX中，则TBOX会与专门负责高精度定位的PBOX有一路硬线连接，实现RTK云端差分感知数的数据传输，结合双频GNSS天线实现厘米级高精度定位。

下图是简化的T-BOX内部硬件系统架构。以市面上目前比较复杂的功能，具备5G+V2X功能做示意。

![img](https://img-blog.csdnimg.cn/20210928115305124.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

主要包括三大件：支持5G+V2X功能的通讯模组、SOC、MCU。

1.通讯模组主要完成无线数据、V2X数据收发，

2.SOC为主要的AP单元，通常集成V2X、以太网协议栈，做业务的逻辑运算，

3.MCU则主要负责网络管理、电源管理等与车端强相关的业务。

各大件的软件架构简化如下图示，由下至上基本遵循 HW, BSP，Kernel, SDK, Middleware, Application 的层级关系。

![img](https://img-blog.csdnimg.cn/20210928115527767.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

传统的独立TBOX 平台，较多采用AG35模组，其中TBOX APP运行在baseband芯片实体，在高通基带被封装后的通信SDK上层。

![img](https://img-blog.csdnimg.cn/20210928115711455.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

TBOX的主要业务流程。

 ![img](https://img-blog.csdnimg.cn/20210928115838981.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

 ![img](https://img-blog.csdnimg.cn/20210928115919296.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

![img](https://img-blog.csdnimg.cn/20210928115931409.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

 ![img](https://img-blog.csdnimg.cn/20210928120026316.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

 ![img](https://img-blog.csdnimg.cn/20210928120042111.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

![img](https://img-blog.csdnimg.cn/20210928120054198.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)



#  V2X

应用意义，主要分三个大方面，提升行驶安全、提高交通效率、提供出行信息服务。

 ![img](https://img-blog.csdnimg.cn/20210928120212187.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

其中，辅助智能驾驶、提升行驶安全的应用场景是目前V2X的首要目的。

相比于在单车感知上投入更多传感器、更多优化算法，结合了高精度地图及厘米级高精度定位的V2X，在智能驾驶上能起到四两拨千斤的效果。

与5G类似，V2X路侧设备RSU及边缘计算模块等的大规模铺设，是实现V2X功能真正落地的前提；**但也同5G类似，V2X也已经不再仅仅是个民生项目，在与各国、各联盟标准、产业链竞赛的过程中，V2X被赋予了ZZ使命和战略意义。**

![img](https://img-blog.csdnimg.cn/20210928123123264.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

数据流大致示意如下。

![img](https://img-blog.csdnimg.cn/20210928123153879.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

 以场景之一 绿波车速引导举例，场景的实现方法。

![img](https://img-blog.csdnimg.cn/20210928123436319.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAcGFuYW1lcmExMg==,size_20,color_FFFFFF,t_70,g_se,x_16)

 

 

# 小结

**功能上，**TBOX从最初的车辆监控数据传输、网络供给等基础功能，增加了如远控等舒适性功能，安防、CALL等安全功能，远程诊断、OTA等便利性功能。随着无线通信网络的不断升级，智能驾驶的时代召唤，TBOX也在朝着5G、V2X、高精度定位的功能上演进。

**形态上，**随着电子架构的升级，域控制器、SOA的不断炒热，未来的TBOX形态可能是归为信息娱乐域控，不再以独立的硬件形态存在。

但从信息安全的角度考虑，部分车厂将TBOX作为了dirty端，从硬件上将云端数据与车端数据隔离，再结合5G+V2X、高精度定位等功能迭代对硬件有变更需求，因此TBOX也可能继续作为独立硬件形态存在。

**资源配备上，**5G也好、V2X也好、进军欧盟的法规摸底也好，这些都需要巨额资金完成测试环境的搭建。同时，随着域控的转型、SOA的服务设计、智能驾驶、V2X企业自定义场景，这些都需要车企自建软件能力，互联网企业、供应商、车企将会进行大规模的人员流动乃至融合共建。