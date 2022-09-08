- [一文读懂RTK高精度定位算法验证系统 (qq.com)](https://mp.weixin.qq.com/s/g6XJENgVaMzVzZLpJXi0aw)

**实时动态定位**（Real Time Kinematic，RTK）是一种基于载波相位观测值的相对定位技术。RTK高精度定位方法可以消除接收机及卫星钟差、削弱卫星轨道、大气层延迟等误差源对定位的影响，获得厘米级定位精度，因此得到了广泛应用。

仿真验证技术正成为智能网联技术开发和验证的重要手段，对高精度定位技术的要求也不断提升。定位算法作为高精度定位技术中的关键技术，那么**在仿真环境下如何对RTK高精度定位算法进行有效的验证与评价呢？**

# 1 什么是RTK高精度定位技术？

RTK高精度定位技术是GNSS系统获取高精度实时动态定位的重要手段，RTK定位主要由三部分组成，分别是**基准站接收机**、**移动站接收机**以及**两站之间数据传输链路**。RTK基准站将修正数据或采集的载波相位观测值通过数据传输链路发送给建设在其数据传输范围内的移动站，移动站接收机接收到的卫星观测数据与基准站发送的数据进行相位差分定位的过程，即为RTK定位过程。

![图片](https://mmbiz.qpic.cn/mmbiz_png/InRzPPAWvxXx5qXB7fxueYv6OPsic1olZWxKu4XVm9zian0Zw54TdicxZMPEI1FEGIgzTLJC2bNvsms37Nfcf2tuw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图1 RTK原理框图

RTK定位方法使用基准站和移动站的同步观测数据，但基准站数据传输存在延迟，导致获得的定位结果滞后。通常的解决办法是利用前一时刻移动站位置及接收的观测数据，进行短时间延迟处理，作为虚拟基站的观测数据，与当前时刻移动站接收的观测数据，一起进行RTK定位解算。定位算法及数据处理流程如下图所示：

![图片](https://mmbiz.qpic.cn/mmbiz_png/InRzPPAWvxXx5qXB7fxueYv6OPsic1olZ66ukriamsPsYD9VIbiauMRBdEj6zFGqx5WAN6tVZkvcZzebdHeQ9YE8Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图2 RTK定位算法框图

# 2 如何进行RTK算法验证？

**中汽数据RTK高精度定位算法验证系统**是基于场景仿真软件搭建虚拟的车辆行驶场景，同时利用卫星信号模拟器模拟出卫星信号及RTK基准站仿真的通信环境，进而对RTK高精度定位算法进行验证与评价。系统架构如图3所示，其中包括场景仿真软件、卫星信号模拟器、RTK被测算法运行模块、高性能上位机等。该验证系统通过场景仿真软件构建三维虚拟交通场景测试用例，在上位机中实时显示测试场景中的三维信息及测试用例状态，然后对场景数据进行筛选与分类，再将主车位置平面坐标转换为经纬度坐标，与主车其它运动状态信息一并以UDP的形式发送至卫星信号模拟器。模拟器根据以上信息对BDS/GPS等卫导系统的导航定位信号进行模拟，同时对RTK基准站的差分观测数据进行仿真，并以标准的RTCM数据协议格式输出数据。RTK定位算法运行模块接收到以上数据后，由被测算法进行解算和处理，处理完成的结果数据再发送到上位机与场景仿真软件的原始数据进行对比分析，进而对定位算法的测试与验证进行客观的评价，至此完成算法验证的整个过程。

![图片](https://mmbiz.qpic.cn/mmbiz_png/InRzPPAWvxXx5qXB7fxueYv6OPsic1olZmRMpJTMxyCk7ibFpqLo8EIaVBswoa9ib4yiceU83hjLcMvygUUTibKaO9A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图3 RTK算法验证系统架构

# 3 RTK算法验证系统构成

**场景仿真软件**

基于成熟的场景仿真软件平台，如VTD、Prescan、CarMaker等，设计并搭建交通场景，场景设计元素包含主车、远车、其他交通参与者等模型，以及道路、环境及交通流等。

![图片](https://mmbiz.qpic.cn/mmbiz_png/InRzPPAWvxXx5qXB7fxueYv6OPsic1olZ00KXgeqiaVjicdMicQmSjibtnlbttjET5xWIcNo3Xr0FlCXnJOKhWkIcrg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图4 场景仿真软件VTD及其测试用例

**数据处理插件**

数据处理插件将场景仿真软件构建的三维虚拟交通场景数据进行筛选与分类，然后将主车位置的平面坐标转换为经纬度坐标，与主车其它运动状态信息一并以UDP的形式发送至卫星信号模拟器。

![图片](https://mmbiz.qpic.cn/mmbiz_png/InRzPPAWvxXx5qXB7fxueYv6OPsic1olZAWRh6sC8sNkZT0mbTkBjtfweZfqcrx79VjXKB1khPWCSjOOsKBRJHA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图5 数据处理插件关键代码

**GNSS卫星信号模拟器**

卫星信号模拟器具有先进的GNSS系统星座模型和多载体/多天线/多目标模拟，支持差分、定向、测姿和模拟编队测试能够模拟复杂多变的验证环境/场景，根据不同的测试需求可对场景星座环境、信号模拟、载体轨迹模型等参数配置，以实现弱信号、高动态、多径干扰或者特定测试环境的呈现。

![图片](https://mmbiz.qpic.cn/mmbiz_png/InRzPPAWvxXx5qXB7fxueYv6OPsic1olZ2ox4j7YbeWVngp8DPgq1bMzJ3wtfrkQjaX6ic0kOlQibYo4ibLovbUJ7A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图6 卫星信号模拟器控制与显示终端

# 4 RTK算法验证系统功能介绍

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/InRzPPAWvxXx5qXB7fxueYv6OPsic1olZ7rjg0bIWtV4IPGVnhQdBsgwtOY9GsKsteaSh1cjLefTYHSa0A4J5AA/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

图7 RTK算法验证系统

中汽数据RTK高精度定位算法验证系统通过将场景仿真软件、数据处理插件等软件与GNSS卫星信号模拟器、算法运行模块等硬件相结合，同时借助配套的控制与显示终端，对BDS、GPS等多制式系统全频点卫星导航信号及RTK基准站观测值数据进行模拟，实现对高精定位算法的验证与评价。

该验证系统可用于RTK高精度定位算法验证、GNSS HIL验证等应用与研究，可在实验室的环境下对相关软硬件进行测试与验证。**此方法通过仿真验证手段可大幅降低开发成本，在加快产品开发速度的同时，也能够保证产品的质量**。

中汽数据具有完整的RTK高精定位算法验证系统、OTA功能验证、TBOX功能验证、V2X数据采集、V2X仿真验证等设备及解决方案，目前已与行业内多家企业展开相关合作与研究，共同推进相关技术和产品的落地与应用，促进智能网联汽车和智慧交通体系的新模式和新形态发展。