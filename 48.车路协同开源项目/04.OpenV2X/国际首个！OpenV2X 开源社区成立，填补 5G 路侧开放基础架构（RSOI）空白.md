- [国际首个！OpenV2X 开源社区成立，填补 5G 路侧开放基础架构（RSOI）空白 (qq.com)](https://mp.weixin.qq.com/s/r7-3MeuI6wDaSXeB2lPOKg)

2022 年 5 月 17 日，一年一度的“517 世界电信日”（暨 2022 世界电信和信息社会日大会）在内蒙古呼和浩特召开。针对在 5G V2X 领域中路侧开放基础架构（Road Side Open Infrastructure，RSOI）的空白，**在上海开源信息协会组织下，由上海白玉兰开源开放研究院、东南大学、南开大学、九州云、一汽富晟、天翼智联、东揽智能等多家单位共同倡议，联合发起 OpenV2X 开源项目，同时发布《5G 车路协同开源项目 OpenV2X 架构白皮书》**，阐述计划开源的 OpenV2X 的价值定位、目标架构、功能规划和初步原型展示。OpenV2X 将在 6 月底正式公布源码。更多信息请详见《5G 车路协同开源项目 OpenV2X 架构白皮书》**（文末点击阅读原文阅读下载白皮书）**。
![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/Hl54p4MexNdFQkWvGn0Jb8MTicyfHu5D4aPLUMwJyKjN6fyPlFAwiahflicgDOq4lDhYiaWg8msAxMaXBIicq6ZGEog/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## **为什么要发起OpenV2X 开源项目？**

在《5G 车路协同开源项目 OpenV2X 架构白皮书》中，我们阐述了当前 V2X 面临的四大问题和挑战：开放中立、软硬解耦、数据增长挑战和 5G 技术深度融合的挑战。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/Hl54p4MexNdFQkWvGn0Jb8MTicyfHu5D4IVagfh2dAJoJORXia5NOr1LV1YmAJlVP9OtibpzliaCWwjZpDKicrExbBg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



OpenV2X 项目希望通过**产、学、研**结合方式，整合科研高校、行业厂商、标准机构和开源协会等资源，集中力量攻克 **“平台不开放”**、**“生态不多样”**、**“软硬不解耦”**、**“5G 用不上”**的问题。通过源码开放、硬件开放、算法开放以及构建和 5G 深入融合的模块等方式，解决当前 5G 模式下 V2X 产业遇到的挑战。

## OpenV2X 项目是完全开放的吗？

**以什么协议开源？**

Open V2X 将在 6 月底开放种子源码，源码将会完全开放，采用国际标准的开源协议，具体协议选择将在 6 月底公布。

## **OpenV2X 和其他开源项目的不同之处在于？**

OpenV2X 项目主要致力于路侧开放基础设施的模块，和现有开源项目的不同点在于：

- OpenV2X 只关注路侧信息处理，不涉及车载领域（如：自动驾驶相关的车内操作系统／车载设备等）
- OpenV2X 由中立的开源协会、高校等机构主导，不受某一厂商的上下游生态影响，相对中立
- OpenV2X 完全是软件产品，和硬件解耦，不绑定任何硬件厂商和设备
- OpenV2X 项目中的数据融合、识别、预警算法完全开放，不和硬件绑定
- OpenV2X 的目标架构是 5G，和 5G 能力深度融合，现有大部分开源项目没有涉及这一领域

## **OpenV2X 的目标架构是如何规划？**

OpenV2X 的目标架构是基于开放源码方式，构建未来 5G/6G 网络下路侧开放基础架构（Road Side Open Infrastructure，RSOI），支持基于 V2X 标准接口，通过 5G、AI、MEC 等技术组合，构建具备海量路侧信息收集、智能融合、生态兼容、性能优异的基础架构开源和开放平台，服务智慧的路，服务智能的车，普惠行动的人。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/Hl54p4MexNdFQkWvGn0Jb8MTicyfHu5D4NR15Se7dTeB9nlswvle1l1EDkSlmU4sMZYjibibMqzRpvpvgSunicVEdQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

OpenV2X 开源项目将会实现 V2X 边缘云控平台路侧，车载，综合数据融合感知分析，支持业务数据南北向接口，运维管理南北向接口；实现设备，GIS 和综合可视化管理等基础功能。当前计划开发中的算法包括交叉路口碰撞预警，逆向超车预警，弱势交通参与者碰撞预警等 17 种典型应用场景和感知数据共享，协作式变道，协作式车辆汇入等 12 种典型应用场景算法实现。 

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/Hl54p4MexNdFQkWvGn0Jb8MTicyfHu5D4sAOWT1Nib1Y6rIuC7bTfmicDhpDDD9jPsww0XL23fC9PSwib5UhdZsV3g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**OpenV2X 现有种子源码包含哪些模块？**

OpenV2X 将在明确开放的协议之后，于6月底公布所有现有合作伙伴已经开发的种子源码，

| OpenV2X 项目范围内                                           | 项目范围外（不包含在 OpenV2X 规划中）                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 种子源码已经包含（部分实现）：1）路侧数据的数据融合2）路侧数据的 A I识别和预警算法3）RSU 发送的结构化数据处理4）符合 V2X 标准的 MQTT 接口实现5）用于测试路侧设备和 RSU 模拟器 未来计划开发（2022 年 12 月之前）：6）5G MEC 模式下的云边协同7）支持不同场景的应用兼容和推送8) 路侧摄像头、雷达设备的原始信息处理 | 1）车载操作系统2）车路相关的硬件3）第三方服务如精准地图、位置信息等4）RSU 硬件和软件能力，比如通过 PC5 或者 V2X 向车推送的控制信息等 |

部分项目的功能截图如下：

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/Hl54p4MexNdFQkWvGn0Jb8MTicyfHu5D4icYJGr0ENCtj9Ks1h2F9W67qFZqcCuxbg4JunnztqxTxlx1pXj4eK8w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

## **OpenV2X 项目希望哪些合作伙伴加入？**

OpenV2X 项目欢迎 V2X 领域用户、V2X 运营方、科研院校、车路协同供应商、5G 技术、AI 领域、开源等领域的合作伙伴加入。

现有发起方中，上海开源信息协会、上海白玉兰开源开放研究院是开源领域相关的组织，东南大学、南开大学是研究相关车路协同算法的科研机构，九州云和东揽智能是在 5G V2X 相关的专业技术厂商，一汽富晟、天翼智联是在智能物流、交通领域实践丰富的机构，同时还包括谷梵智能等AI硬件供应商以及其他上下游合作伙伴。OpenV2X 是开放、共享、合作的社区，希望关注这一领域伙伴一起加入，共同推进产业的发展。

## OpenV2X 如何加入？

OpenV2X 开源项目还在积极筹备中，欢迎这一领域的合作伙伴一起加入推进项目发展，您可以通过以下方式加入这一社区：

- 联系邮箱：[openv2x@shanghaiopen.org.cn](mailto:openv2x@shanghaiopen.org.cn) 
- 联系电话：17612030493