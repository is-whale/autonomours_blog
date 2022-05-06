- [百度Apollo|无人驾驶|高精度地图的制作和opendrive格式详细讲解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/373769242)

## １．如何制作高精度地图

[开发者说 |Apollo介绍之Map模块](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzI1NjkxOTMyNQ%3D%3D%26mid%3D2247495869%26idx%3D1%26sn%3D4a63f9963d9add39bce907a83b9d232b%26chksm%3Dea1de2cfdd6a6bd9efe00f58a67020bacae491d88e21340e8dfaa2e89ab10de8260b7c62a594%26mpshare%3D1%26scene%3D1%26srcid%3D0607j7UTdHdJuRVQns1xSlwr%26sharer_sharetime%3D1623065068022%26sharer_shareid%3D4364372046978bb504a21f48514f0710%26exportkey%3DAf4RT%2FK3U0XjZ0xotx57OTY%3D%26pass_ticket%3Dvuv43uHrneD5Bre0RZ4ASacrSl%2Fgejz8BSVgU3dr%2B9wBOD8192YOGJc7cTYxO%2BkP%26wx_header%3D0%23rd)

[开发者说｜数据驱动的自动驾驶仿真](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzI1NjkxOTMyNQ%3D%3D%26mid%3D2247496071%26idx%3D1%26sn%3D982ad2cca616ee7e251667c0348d8c26%26chksm%3Dea1de3f5dd6a6ae3bfc061b609690a89068314f6f2777b283d7d016718ec880d7c47f6ea8bdf%26mpshare%3D1%26scene%3D1%26srcid%3D0607UtVDWh0vHV6r744nqUAR%26sharer_sharetime%3D1623065092926%26sharer_shareid%3D4364372046978bb504a21f48514f0710%26exportkey%3DAWvq9%2FAIldgWxgN4vsttGDM%3D%26pass_ticket%3Dvuv43uHrneD5Bre0RZ4ASacrSl%2Fgejz8BSVgU3dr%2B9wBOD8192YOGJc7cTYxO%2BkP%26wx_header%3D0%23rd)

[进阶课程⑪ | Apollo地图生产技术](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzI1NjkxOTMyNQ%3D%3D%26mid%3D2247486473%26idx%3D1%26sn%3D14078ce761e8074dd49fb855ce2ef025%26chksm%3Dea1e1e7bdd69976d9d7f4cdc900ad49d4aa4b577a59c79ffda48ffee85da90509076aa8f5de2%26mpshare%3D1%26scene%3D1%26srcid%3D0607t2ZO62XNNCCBdcLf8q4t%26sharer_sharetime%3D1623063632560%26sharer_shareid%3D4364372046978bb504a21f48514f0710%26exportkey%3DAQUMQT3V%2Bo%2FqWYh3cKWvJwk%3D%26pass_ticket%3Dvuv43uHrneD5Bre0RZ4ASacrSl%2Fgejz8BSVgU3dr%2B9wBOD8192YOGJc7cTYxO%2BkP%26wx_header%3D0%23rd)

[技术文档 | Apollo的坐标系](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzI1NjkxOTMyNQ%3D%3D%26mid%3D2247486548%26idx%3D1%26sn%3D5ca7ba8f49baf7fe4f304e8bf5ab6f4d%26chksm%3Dea1e1e26dd6997307757ddd618d72964647345a97a2e4e7993471c54a61d42c875bf4442fc51%26mpshare%3D1%26scene%3D1%26srcid%3D0607HsVlGyRYGMxRtzMUgqGz%26sharer_sharetime%3D1623063823089%26sharer_shareid%3D4364372046978bb504a21f48514f0710%26exportkey%3DAYlK2wLkfZRk7WVsqL6VYlc%3D%26pass_ticket%3Dvuv43uHrneD5Bre0RZ4ASacrSl%2Fgejz8BSVgU3dr%2B9wBOD8192YOGJc7cTYxO%2BkP%26wx_header%3D0%23rd)

[开发者说 | 看不见的“传感器”——高精度地图](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzI1NjkxOTMyNQ%3D%3D%26mid%3D2247490080%26idx%3D1%26sn%3Dcea20bbbaedffb20b61b80d59d74b8cb%26chksm%3Dea1e0852dd69814457eb9a4635dbccae02eea8d87422f55f08df5ba13aa9e188c3207f2e508b%26mpshare%3D1%26scene%3D1%26srcid%3D0607u0ruYh44jppIUlCk9PUN%26sharer_sharetime%3D1623065011002%26sharer_shareid%3D4364372046978bb504a21f48514f0710%26exportkey%3DAQH%2BphSZt90Om%2B0AB22Yokg%3D%26pass_ticket%3Dvuv43uHrneD5Bre0RZ4ASacrSl%2Fgejz8BSVgU3dr%2B9wBOD8192YOGJc7cTYxO%2BkP%26wx_header%3D0%23rd)

[开发者说丨地理与平面坐标系对齐及INS数据可视化](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzI1NjkxOTMyNQ%3D%3D%26mid%3D2247489099%26idx%3D1%26sn%3Db193f95970cc76efeff14afcc559f640%26scene%3D21%23wechat_redirect)

[丁前利：自动驾驶之高精度地图（六）如何实现高精度地图的制作和更新](https://zhuanlan.zhihu.com/p/369258953)

![img](https://pic4.zhimg.com/80/v2-559243821f5f0c816c8f1b7909a635c3_720w.jpg)地图采集硬件方案

![img](https://pic4.zhimg.com/80/v2-a72bca97ff086f9466a193efa048d0ef_720w.jpg)地图采集方案采集流程

![img](https://pic3.zhimg.com/80/v2-f735af46138aff578551126dd4dba2be_720w.jpg)地图数据服务平台



![img](https://pic1.zhimg.com/80/v2-e7e7150e59cc62fe3d318b1ac8f14d7c_720w.jpg)高精地图生产流程

![img](https://pic1.zhimg.com/80/v2-9a8387c9bc5b74fec31a062cbe745c80_720w.jpg)全自动数据融合加工

![img](https://pic1.zhimg.com/80/v2-e0380421a981cb2e206485f16a3d12dc_720w.jpg)基于深度学习的地图要素识别

![img](https://pic2.zhimg.com/80/v2-2abcf4584760abcf706fabadbf04f70d_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-f77858bb64e01f4d1eb2c0c74be741c7_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-12a487cf1a2b775c760223edd4807b90_720w.jpg)高精地图的数据元素

![img](https://pic4.zhimg.com/80/v2-22873b3a05329ffab74279098b182cc3_720w.jpg)高精地图的车道模型

![img](https://pic3.zhimg.com/80/v2-9bed68fa803524ef01a13b0f127bd8a2_720w.jpg)高精地图的Junction模型

![img](https://pic4.zhimg.com/80/v2-525b04c17b9d10e3961e4c9f4c92a82f_720w.jpg)高精度地图的坐标系-UTM

![img](https://pic3.zhimg.com/80/v2-4decce3eb6544c9d1fbe545c40274c46_720w.jpg)高精度地图的坐标系-WGS84

![img](https://pic1.zhimg.com/80/v2-a58b0b0323a55fe8e7a472df1a28e31c_720w.jpg)高精度地图的坐标系-Track System



![img](https://pic4.zhimg.com/80/v2-ae9b8debf48ec7aeacd0a089c361b373_720w.jpg)左侧是一个高精度地图，右侧是实时相对地图。实时相对地图是车辆通过传感器来感知车身周围环境，可以帮助开放者更友好、方便地运行Apollo。

![img](https://pic4.zhimg.com/80/v2-3d71a560df4b07643d9f84a8a420ecb3_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-4e17bc6e62facc31cce91ea53ed5fef0_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-f5d2a1daa7711263ad513408c7792f1a_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-0c98087b6b534a6fb6beabcb77a4cc8f_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-f753d7f63f0eb1fb762d663b1adf6e6e_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-cb3b5de8d43517b1c749c42eb3bec0db_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-62e4c297fa7608c1c1e772a70483a479_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-ecd6e6b11c865cfdc42d1e4c690c8194_720w.jpg)



![img](https://pic4.zhimg.com/80/v2-048b18b61b5b803d913c80eb0032a4e7_720w.jpg)

## ２．百度Apollo相对地图

熟悉Apollo 1.5和2.0的开发者都知道，Apollo的规划决策模块依赖于绝对坐标下的高精地图。高精地图中有丰富的地图元素，可以帮助决策规划进行复杂道路上的自动行驶，但高速的场景相较于复杂的城市道路路况而言，比较简单。所以，我们采用了动态相对地图的方法。

什么是相对地图，相对地图是基于车辆坐标系的一个地图，车身就是相对地图的原点。众所周知，车道线是高精地图里很重要的一个元素。车道的中心线的边界线信息，是保证决策规划进行合理安全路径规划的必要信息。那么在相对地图中，车道线的信息也是必不可缺的，相对地图的车道线就是依据视觉感知对于车道线的检测，且内嵌了云端服务提供的指引线。

这样构造出来的相对地图，用于高精地图统一的数据格式，并与视觉感知同样的频率而实时更新。其中的道路指引线的云端服务，也便于将指引线数据分享给其他需要在同样路段进行自动驾驶测试的车辆来使用。

我们试想一下，如果是在2.5之前的版本，开发者即使能力非常强，也不能在没有高精度地图的情况下运行Apollo，而有了高精度地图，会变得非常友好。开发者可以自己采集，直接使用。

[Apollo 2.5版导航模式的使用方法](https://link.zhihu.com/?target=https%3A//blog.csdn.net/davidhopper/article/details/79999339)

## ３．高精地图的格式规范（OpenDRIVE）

先来补充点OpenDRIVE知识

[opendrive格式简单解读及carsim导入_哔哩哔哩_bilibili](https://link.zhihu.com/?target=https%3A//www.bilibili.com/video/BV1MK4y1o7Bw%3Ffrom%3Dsearch%26seid%3D9979093366829776640)

[openscenario文件简单解读_哔哩哔哩_bilibili](https://link.zhihu.com/?target=https%3A//www.bilibili.com/video/BV1dU4y1h7ZK)

感谢小姐姐的讲解

![img](https://pic3.zhimg.com/80/v2-9a9c05c8db2a4531972259851545710a_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-821ae5a305f74b14aec441a840248619_720w.jpg)高精地图-Apollo OpenDrive规范



![img](https://pic3.zhimg.com/80/v2-f479b299ca9159e92189b7f504874356_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-4bb0b0160f69eacaf7203ec174f7948f_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-4599eb483f6d1ce9fc98689c20767eea_720w.jpg)

**Section的概念**

![img](https://pic1.zhimg.com/80/v2-cc9d6d90aeb8b0c17bdb5bcedb988e8c_720w.jpg)

- 切分Section的原则
- 车道数变化
- 虚实线变化
- 道路属性变化，比如说有一段有栅栏，有一段没有
- etc

Lane的概念

有一个Reference Line的ID是0，向左的车道的ID是1.2.3……，向右的是-1.-2.-3……

![img](https://pic4.zhimg.com/80/v2-25f05ac6ea75c0ee6bf94d8043b7731f_720w.jpg)

路口的概念（junction area）

![img](https://pic4.zhimg.com/80/v2-949f502d632fcb294364ad1e44dcf05b_720w.jpg)

**对地图做了一些分层**

- Road&Lane Model Layer（最底层）

A highly precise representation of road network.

包括虚实线、道路边界、车道边界、限速，标志牌一些基本道路信息。

- Localization Model Layer

Help a Vehicle to find its exact Position of lane it is driving in.

包含了一些用于定位的信息，比如说路上的电线杆子还有一些树等。

- Activity Layer（动态信息层）

Understanding dynamic events in the road network.

包括事故、修路等动态信息，对实时性要求高。

- Analytics Layer（司机驾驶习惯层）

Tell how humans actually behave In a piece of road.

收集司机的驾驶习惯，比如在哪加速，在哪减速等，让自动驾驶的汽车开的更像一个人。



![img](https://pic3.zhimg.com/80/v2-dff8c8d21487d5742ad99ed45198c316_720w.jpg)



![img](https://pic2.zhimg.com/80/v2-74669abec55471e7603cef76747c43b5_720w.jpg)

\- 机动车车道标线

\- 人行横道斑马线

\- 道路边缘马路牙子

\- 道路中间隔离墎

\- 道路两侧防护栏

\- 机动车道转向箭头、文字及图案标识

\- 道路资产/道路部件，如灯柱、标志牌等

\- 道路表面高程点/道路表面三维模型

\- 任意里程道路横断面

\- 主路或辅路与台阶以上道路接驳的斜坡

\- 与道路交跨的高架桥到路面净空分析

\- 与道路交跨的电力线

![img](https://pic1.zhimg.com/80/v2-bf1a9ffc0d76b18b49aa4f1e448bf328_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-42bb65d67061794b085c7f9e10ae807a_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-18f31e0bb0953376165ab3e69e2ed4ee_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-44a1bb53338e285b1608f4f3e18f38a1_720w.jpg)

## ４．OpenDrive如何与Apollo地图互转

百度Apollo地图包括base_map.bin、rouring_map. bin、sim_map. bin三个文件，是基于**OpenDrive**升级的，**Roadrunner**工具可以实现**OpenDrive**与Apollo互转，实现Carla输出的**OpenDrive**地图在Apollo中使用。

![img](https://pic1.zhimg.com/80/v2-fe06c5995586920522b99cfd1fd707a8_720w.jpg)

与普通地图不同，高精地图主要服务于自动驾驶车辆，通过一套独特的导航体系，帮助自动驾驶解决系统性能问题，扩展传感器检测边界。目前 Apollo 内部高精地图主要应用在高精定位、环境感知、决策规划、仿真运行四大场景，帮助解决林荫道路 GPS 信号弱、红绿灯的定位与感知以及十字路口复杂等导航难题。目前，Apollo 高精地图的自动化程度已经达到了 90%、准确识别率达到了 95% 以上，预计 2020 年可以覆盖全国所有的重点道路。

*** GitHub相关模块地址：**

[https://github.com/ApolloAuto/apollo/tree/master/modules/map](https://link.zhihu.com/?target=https%3A//github.com/ApolloAuto/apollo/tree/master/modules/map)

[Apollo 高精地图解析](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s%3F__biz%3DMzI1NjkxOTMyNQ%3D%3D%26mid%3D2247484107%26idx%3D1%26sn%3Df3d79cb208afef14470622f0dd9b8dcf%26scene%3D21%23wechat_redirect)

高精度地图的输出格式是怎样的？包括用于导航的矢量图、用于定位的点云图以及全局的路径。对于高精度地图这一块希望能够有详细的介绍。

可以参考

[https://github.com/ApolloAuto/apollo/blob/master/modules/map/data/README.md](https://link.zhihu.com/?target=https%3A//github.com/ApolloAuto/apollo/blob/master/modules/map/data/README.md)

如 README 所示，xml, bin, txt, lb1 都是不同的文件格式，适配不同的读取器，内容是一致的。

base_map 是基础地图；

routing_map 是routing模块所需的、对 base_map 预处理得来的地图；

sim_map 是simulation模块所需的、对 base_map 降采样以提高传输和渲染效率；

default_end_way_point 是一个proto，定义了该地图默认的终点，用户可以一键导航到这个终点。

我们建议这些文件都成套提供，避免不必要的麻烦。具体的格式，可以在完成Apollo安装后，在/apollo/modules/map/data/下面找到以上目录查看具体的格式文件。

在Apollo 1.5和2.0中，规划模块和控制模块依靠的是全球定位和高精地图。高精地图中有丰富的地图元素，可以帮助车辆规划，实现复杂路况下的自动驾驶，但高速交通场景比城市道路场景更简单。因此，在此次发布的Apollo 2.5我们决定使用动态的实时相对地图（Real-time Relative Map ）。

该地图基于车辆坐标系，其原点位于车辆本身。众所周知，车道线是地图中的重要元素，以确保自动驾驶车辆做出合理的决策并进行安全的轨迹规划。在相对地图中，车道线数据是通过基于摄像头的车道感知而生成的，并且包含基于云端的导航线（Navigation Line）。

通过使用这种方法，我们可以创建一个与高精地图的数据格式相匹配的相对地图（Relative Map），并基于事先录制好的人工驾驶轨迹和摄像头的感知进行车道检测。

大家都知道高精地图在自动驾驶中的价值所在。它们不仅有着较高的精准度，而且适用于任何路况。但因其生产成本高，周转时间较长。许多开发人员告诉我们，他们因缺乏用于其地区的高精地图使得测试受到阻碍。

相较来讲，相对地图的精度较低，仅限于某些用途。但它却有着不容忽视的优势，成本更低，周转更快 ，并且制作起来更容易，还可以实现实时更新。尤其对于从事高速场景的工作人员来说，Apollo 2.5的相对地图确实有助于加速自动驾驶的开发和测试。

熟悉Apollo 1.5和2.0的开发者都知道，Apollo的规划决策模块依赖于绝对坐标下的高精地图。高精地图中有丰富的地图元素，可以帮助决策规划进行复杂道路上的自动行驶，但高速的场景相较于复杂的城市道路路况而言，比较简单。所以，我们采用了动态相对地图的方法。

什么是相对地图，相对地图是基于车辆坐标系的一个地图，车身就是相对地图的原点。众所周知，车道线是高精地图里很重要的一个元素。车道的中心线的边界线信息，是保证决策规划进行合理安全路径规划的必要信息。那么在相对地图中，车道线的信息也是必不可缺的，相对地图的车道线就是依据视觉感知对于车道线的检测，且内嵌了云端服务提供的指引线。

这样构造出来的相对地图，用于高精地图统一的数据格式，并与视觉感知同样的频率而实时更新。其中的道路指引线的云端服务，也便于将指引线数据分享给其他需要在同样路段进行自动驾驶测试的车辆来使用。

通过基于人工驾驶路径的实时地图生成方案，和相对地图模块特点及应用等技术内容，进一步剖析了Apollo相对地图，让开发者了解相对地图在自动驾驶中的使用及实际应用。

![img](https://pic2.zhimg.com/80/v2-d4dd60ccd8becfe0fc074f657b01e775_720w.jpg)



**感知模块**主要提供了动态障碍物的信息，比如车辆，行人及其他障碍物。由于感知模块加入了视觉感知的能力，该模块也能够提供部分实时地图信息，比如车辆的当前车道线，斑马线，和红绿灯停止线。这类信息通过实时计算得到，成本低，但是依赖于路面状况及标识清晰度。

和实时地图相对应的是**高清地图模块**提供的静态地图信息。相比实时地图信息，静态地图信息是在线下提前收集和处理，这个信息由于是在线下制作，准确度高，信息种类更加丰富细致，而且不依赖于路面状况，但是制作周期长，成本高。

为了将有不同特点和格式的实时地图信息和静态地图信息更好的结合在一起，同时也为了给规划和控制模块提供统一的接口。我们设计和开发了**相对地图模块**。