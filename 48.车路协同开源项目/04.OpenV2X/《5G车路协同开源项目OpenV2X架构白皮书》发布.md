- [《5G车路协同开源项目OpenV2X架构白皮书》发布 (qq.com)](https://mp.weixin.qq.com/s/foUnxQ_fAjZR370Gy9klWw)

2022年5月17日，一年一度的“517世界电信日”（暨2022世界电信和信息社会日大会）在内蒙古呼和浩特召开。针对在5G V2X 领域中路侧开放基础架构（Road Side Open Infrastructure，RSOI）的空白，在上海开源信息协会组织下，由上海白玉兰开源开放研究院、东南大学、南开大学、九州云、一汽富晟、天翼智联、东揽智能等多家单位共同倡议，联合发起的**OpenV2X开源项目**，同时发布《5G 车路协同开源项目OpenV2X架构白皮书》，阐述计划开源的OpenV2X 的价值定位、目标架构、功能规划和初步原型展示。**OpenV2X将在6月底正式公布源码**。更多信息详见**《5G 车路协同开源项目OpenV2X架构白皮书》**（[点击下载](https://pan.baidu.com/s/1aF9GZuShilv3x5FAGMsngA?pwd=h3vm)）。

![图片](https://mmbiz.qpic.cn/mmbiz_png/dDQiaOLDWBfpkqAO9G0IzKJCMHPDnk7Tic3UuTJPbiaqLxejftgI5cT9gTbmcoHBDrPKRUoW7lQwddPFibXkQ909Pw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**为什么要发起OpenV2X开源项目？**

在《5G 车路协同开源项目OpenV2X架构白皮书》中，我们阐述了当前V2X面临的四大问题和挑战：开放中立、软硬解耦、数据增长挑战和5G技术深度融合的挑战。

![图片](https://mmbiz.qpic.cn/mmbiz_png/dDQiaOLDWBfpkqAO9G0IzKJCMHPDnk7TicsBAxhjCj8nHhMFxiaHcRSa1hic7jy7bJDeUmib6kS8VlhgKZCb2LC24pA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

OpenV2X项目希望通过**产、学、研**结合方式，整合科研高校、行业厂商、标准机构和开源协会等资源，集中力量攻克 **“平台不开放”**、**“生态不多样”**、**“软硬不解耦”**、**“5G用不上”**的问题。通过源码开放、硬件开放、算法开放以及构建和5G深入融合的模块等方式，解决当前5G模式下V2X产业遇到的挑战。

**OpenV2X项目是完全开放的吗？**

**以什么协议开源？**

OpenV2X将在6月底开放种子源码，源码将会完全开放，采用国际标准的开源协议，具体协议选择将在6月底公布。

**OpenV2X和其他开源项目的不同之处在哪里？**

OpenV2X项目主要致力于路侧开放基础设施的模块，和现有的开源项目的不同点在于：

1）OpenV2X只关注路侧信息处理，不涉及车载领域（如：自动驾驶相关的车内操作系统／车载设备等）

2）OpenV2X由中立的开源协会、高校等机构主导，不受某一厂商的上下游生态影响，相对中立

3）OpenV2X完全是软件产品，和硬件解耦，不绑定任何硬件厂商和设备

4）OpenV2X项目中的数据融合、识别、预警算法完全开放，不和硬件绑定

5）OpenV2X的目标架构是5G，和5G能力深度融合，现有大部分开源项目没有涉及这一领域

**OpenV2X 的目标架构是如何规划？**

OpenV2X的目标架构是基于开放源码方式，构建未来5G/6G网络下路侧开放基础架构（Road Side Open Infrastructure，RSOI），支持基于V2X标准接口，通过5G、AI、MEC等技术组合，构建具备海量路侧信息收集、智能融合、生态兼容、性能优异的基础架构开源和开放平台，服务智慧的路，服务智能的车，普惠行动的人。

![图片](https://mmbiz.qpic.cn/mmbiz_png/dDQiaOLDWBfpkqAO9G0IzKJCMHPDnk7TicibMEjXtGHPUibUvdY8IGAWhStZDCDCb97HBGTHz3cGbITVtOd0FJiaB2Q/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

OpenV2X开源项目将会实现v2x边缘云控平台路侧，车载，综合数据融合感知分析，支持业务数据南北向接口，运维管理南北向接口；实现设备，GIS和综合可视化管理等基础功能。当前计划开发中的算法包括交叉路口碰撞预警，逆向超车预警，弱势交通参与者碰撞预警等17种典型应用场景和感知数据共享，协作式变道，协作式车辆汇入等12种典型应用场景算法实现。

![图片](https://mmbiz.qpic.cn/mmbiz_png/dDQiaOLDWBfpkqAO9G0IzKJCMHPDnk7TicF6nRPhib8c0om4NGJNFAS02ALW43kb1mzKJrwdI7Qd7nHS4ZAYQAp0w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**OpenV2X 现有种子源码包含哪些模块？**

OpenV2X 将在明确开放的协议之后，将于6月底公布所有现有合作伙伴已经开发的种子源码。

| OpenV2X 项目范围内                                           | 项目范围外（不包含在OpenV2X规划中）                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **种子源码已经包含（部分实现）：**1）路侧数据的数据融合2）路侧数据的AI识别和预警算法3）RSU发送的结构化数据处理4）符合V2X标准的MQTT接口实现5）用于测试路侧设备和RSU模拟器 **未来计划开发（2022年12月之前）：**6）5G MEC 模式下的云边协同7）支持不同场景的应用兼容和推送8) 路侧摄像头、雷达设备的原始信息处理 | 1）车载操作系统2）车路相关的硬件3）第三方服务如精准地图、位置信息等4）RSU硬件和软件能力，比如通过PC5或者V2X向车推送的控制信息等 |

部分项目的功能截图如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/dDQiaOLDWBfpkqAO9G0IzKJCMHPDnk7TicmSskDopW7iaibebVmDToRYguH0sP4HnhGILn9FLHib81tODAEYmibrZjpQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**OpenV2X项目希望哪些合作伙伴加入？**

OpenV2X项目欢迎V2X领域用户、V2X运营方、科研院校、车路协同供应商、5G技术、AI领域、开源等领域的合作伙伴加入。

现有发起方中，上海开源信息协会、上海白玉兰开源开放研究院是开源领域相关的组织，东南大学、南开大学是研究相关车路协同算法的科研机构，九州云和东揽智能是在5G V2X相关的专业技术厂商，一汽富晟、天翼智联是在智能物流、交通领域实践丰富的机构，同时还包括谷梵智能等AI硬件供应商以及其他上下游合作伙伴。OpenV2X是开放、共享、合作的社区，希望关注这一领域的伙伴一起加入，共同推进产业的发展。