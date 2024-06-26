- [谈谈车联网--V2X技术](https://www.eet-china.com/mp/a82761.html)

## 车联网的发展背景

**国家政策**

- **2020年3月，中共中央政治局常务委员会召开会议提出，加快5G网络、数据中心等新型基础设施建设进度，新基建的号角正式吹响**
- **2020年5月，《2020年国务院政府工作报告》提出，重点支持“两新一重”（新型基础设施建设，新型城镇化建设，交通、水利等重大工程建设）建设**

**行业需求**

- **汽车正经历新四化的改造大潮，交通从传统交通向智能交通转变**
- **2020年2月，由发改委、工信部等11部委联合发布《智能汽车创新发展战略》**
- **2020年10月，国务院印发《新能源汽车产业发展规划（2021-2035年）》加快推动智能网联汽车产业发展**

## V2X概述及发展路径

**V2X（vehicle to  everything）：**作为智能网联汽车中的信息交互关键技术，主要用于实现车间信息共享与协同控制的通信保障。
在未来的自动驾驶应用中，V2X通信技术是实现环境感知的重要技术之一，与传统车载激光雷达、毫米波雷达、摄像头、超声波等车载感知设备优势互补，为自动驾驶汽车提供雷达无法实现的超视距和复杂环境感知能力。

**历史可追溯到2013年:**

- **2013年，时任大唐电信集团副总裁的陈山枝博士在世界电信日大会上首次公开提出了LTE-V2X的概念，并带领大唐团队积极推动C-V2X技术的国际化标准和演进**
- **2014年和2015年推动3GPP立项**
- **2016年完成国际化3GPP标准**
- **2017年完成国内标准化相关工作，此后一直在推动产业相关发展**
- **在2020年，3GPP完成了基于NR-V2X标准的制定**



![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-8b66286d2b37412173bd6d4090f2bceb.png)

## 中美V2X标准走向统一

**目前，世界上用于V2X通信的主流技术包括:DSRC和C-V2X。**

**DSRC（dedicated short range communication)标准由IEEE（美国电气电子工程师学会）基于WIFI制定，标准化流程开始于2004年。通过OBU与RSU提供车间与车路间信息的双向传输，RSU再透过光纤或行动网络将交通信息传送至后端智能运输系统平台（ITS）。**

 **DSRC系统包含**

- **车载单元（On Board  Unit，OBU）**
- **路侧单元（Road Site  Unit，RSU）**

**C-V2X（cellular vehicle to  everything):基于蜂窝移动通信系统的技术，分为LTE-V2X和5G NR-V2X。**

- **OBU**
- **RSU**
- **UU接口：OBU/RS**U与基站之间的接口，实现与移动网络通信
- **PC5接口：C-V2X的底层直连蜂窝通信协议**

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-f5e73c77a3e9726dde4f86efae1500bb.png)

2020年11月，美国联邦通信委员会正式投票决定将原分配给DSRC（IEEE 802.11p）的5.9GHz频段70MHz带宽划拨给Wi-Fi和C-V2X使用，标志着美国正式宣布放弃DSRC（IEEE  802.11p）并转向C-V2X，也标志着由我国主推的C-V2X逐渐成为在全球范围内被认可的事实行业标准。

**中国和美国作为两个世界上最大的超级经济体，无论是经济总量和活跃度，世界上没有其他经济体可比拟的。汽车工业作为一个国家的支柱产业，体量巨大，影响深远，涉及产业广泛。在汽车工业新四化的大背景下，中美两个超级大国，在V2X技术标准上走向统一，加速V2X产业链的发展，对整个车联网产业重大利好。**

**随着C-V2X车联网技术赋能交通和汽车行业的不断深入，我国将走出有自身特色的发展模式，以基于C-V2X的车路协同发展模式，支持未来自动驾驶和智能交通发展。**

## C-V2X技术原理

**蜂窝通信（LTE）与DSRC（IEEE 802.11p），均无法满足车联网（V2X）通信的高可靠与低时延等需求。**

**“中国智慧”成功地解决了这一世界性难题！**

**陈山枝博士及大唐研究团队在2013年5月17日（国际电信日）最早提出LTE-V2X概念与关键技术，确立了C-V2X的基本系统架构、技术原理和技术路线，其实现了蜂窝通信和短距直通通信融合创新，在蜂窝通信基础上，引入终端直通通信特性，支持车-车、车-路的直接通信，很好适应车联网应用低时延、高可靠传输要求。**

**随后，大唐、华为等中国企业在2015年开始牵头在全球主流通信标准化组织3GPP中积极推动LTE-V2X标准化工作。**

**蜂窝通信与直通通信融合的C-V2X包含了两种通信模式：**

- **蜂窝方式（LTE-V-cell）：**

- - **终端和基站之间通过Uu接口通信，由基站作为集中式的控制中心和数据信息转发中心，完成集中式无线资源调度、拥塞控制和干扰协调**

- **直通通信模式（LTE-V-direct）：**

- - **车、人、路之间通过PC5接口实现短距离直连通信，解决车联网中终端间低时延、高可靠传输的问题**

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-e1a05e0301b36922c1a0febee67d7a72.png)


这两种通信模式共同支持车联网多样化的应用需求，直通方式可支持在没有蜂窝基站覆盖的场景下工作。

通过增加V2X应用层与接入层间的适配层，实现通信模式智能选择，支持业务分流控制、无线传输控制、业务质量管理、连接控制管理等功能。

**蜂窝通信（Uu）和直通通信（PC5）两种模式优势互补，通过合理分配系统负荷，自适应快速实现车联网业务高可靠和连续通信——Uu接口基于4G/5G频段支持时延不敏感业务（如地图下载、信息娱乐等），PC5接口基于ITS专用频段支持低时延、高可靠业务（如V2V、V2I、V2P等道路安全业务）。**

**V2X主要包含:**

- **车-车通信V2V---vehicle-to-vehicle**
- **车-路通信V2I---vehicle-to-infrastructure**
- **车-网通信V2N---vehicle-to-network**
- **车-人通信V2P---vehicle-to-pedestrian**

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-31a672db4821a84c1f1f7495a3688426.png)

**V2V车--车通信：**

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-9be46793e6e972de417a98ab30559d05.png)

**V2V的工作原理如下图：**

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-805e358455878803379e63783882faa5.png)

**V2I车--路侧设备通信：**

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-a54b06daf04aee46967b838c9010f621.png)

**V2I的工作原理如下图：**

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-d09ca5f3c3c738e84d0b17dc8233807d.png)

**基于V2X技术的软件通用流程：**



![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-8cd7817d186f3570fec5ee6a35fb1ac4.png)

**应用软件“栈”视图：**

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-eeaadb3bb94952f8ed11c347917a3621.png)

下面，我们就V2X技术的软件通用流程，作详细解释。

**数据**采集流程：

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-71ace3132db602d20da29cefa92f641f.png)

**数据组织：**

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-78e86ab2524d006f8d9a3049c130664c.png)

**数据交换：**

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-7a9652bc606b6d70d8e392dcdeb390d5.png)

**数据处理：**

如何处理十字路口的碰撞预警？

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-6fc6fd56f31a8b4fe5b5cb01312e9cd6.png)

- 计算本车到远车的方向角
- 通过比较本车朝向与方向角判断远车在本车前方
- 两车朝向必然成一定角度
- 两车朝向的延长线的交点为疑似碰撞点
- 两车到达碰撞点的时间差满足设定值

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-8b5e06e06d56bdd2b9a2e0def788d725.png)

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-d3504c165f48e2527612b34c6cfa9f98.png)



![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-8c8d8815bcf98ef30e4e91c64adffbe6.png)

## 基于C-V2X的车联网产业链 

**C-V2X产业链上下游分为:**

- 通信芯片商

- - 华为、大唐等可提供支持LTE-V2X的通信芯片

- 通信模组商

- - 华为、大唐、高通、移远、芯讯通等可提供基于LTE-V2X的芯片模组

- 硬件终端商

- - 华为、大唐、金溢、星云互联、东软、万集等厂商可提供基于LTE-V2X的OBU、RSU硬件设备，以及相应的软件协议栈

- 测试验证厂商

- - 中国信通院、中汽中心、上机检、中国汽研、上海国际汽车城等科研和检测机构开展C-V2X通信、应用相关测试验证工作
  - 奇虎科技、华大电子等安全企业开展C-V2X安全研究与应用验证

- 运营服务商

- - 国内三大电信运营商均大力推进C-V2X业务验证示范
  - 百度、阿里、腾讯等互联网企业进军车联网，加速C-V2X应用落地

- 高精度定位和地图

- - 北斗、高德、百度、四维图新等企业致力于高精度定位的研究，为V2X行业提供高精度定位和地图服务

## 车路协同与自动驾驶风雨同舟 

谈到车路协同对于自动驾驶的意义，我们首先看看当前**自动驾驶的瓶颈**：

- 信息感知能力差，成本高

- - 自动驾驶采用的传感器都是类视觉传感器，在感知距离、感受视野、分辨率等因素上互相制约
  - 多传感器融合加大软件算法复杂度
  - 在高速自动驾驶中，传感器能力有限
  - “鬼探头”挑战悬而未决

- 高车流量交通环境应变能力差

- 车辆密度的提升，环境越来越复杂，自动驾驶车辆容易陷入寸步难行的窘境（比如当下的拥堵自动跟随功能）

**针对以上自动驾驶的当前问题，车路协同能做些什么？**

- 车车协同（V2V）增强信息感知能力

- - RSU“站得高、看得全”，合理布设传感器位置，消除盲区
  - RSU位置固定，有足够的先验信息可以用来辅助感知，提升感知准确性
  - RSU通过先进的通信技术组合成泛在感知网，提升车辆感知范围

- 车路线协商机制

- 数字化，有非常高的抗干扰性，可以无损传递到车内

- 提供两车协同、多车协同和车队协同

- 基础设施改造，分摊到每台车的成本低

## 当下车联网产业面临的挑战与机遇

**单车智能“瓶颈”凸显**

- 长尾问题日益显著

- - 投入了10%的资源、精力和时间，已解决了90%的问题；但剩下的10%的长尾安全问题，却可能需要投入90%的资源、精力和时间才能解决掉

- 单车智能造成信息孤岛，车路协同有限

- 缺乏超视距感知能力，易受天气和光线的影响

- 缺乏上帝视角关于路况全局的信息，“鬼探头”问题棘手

- 软件算法低时延和高可靠有待提高

**C-V2X+ADAS融合势在必行**

- 车车协同（V2V）增强信息感知能力，构建泛在感知网络

**路侧前期投入比较大，商业模式尚不清晰**

- 各级政府以及高速业主单位，敢于尝试创新，有勇气，有魄力
- 需要国家顶层设计规划，各部委协同互动
- 消除信息共享鸿沟，管理部门、企业等收集的信息如何通过公共平台共享

**安全风险与威胁不容忽视**

- 保证功能安全，避免随机性故障和环境影响造成的安全风险
- 保证网络安全，避免被人为利用造成的安全风险
- 车载网络安全无法量化设计与验证度量
- 保险行业不敢为智能网联汽车提供保险服务

**全天候、全场景乘用车无人驾驶道路坎坷**

- NR-V2X关键技术仍需验证
- 芯片模组等尚不具备商用能力
- 算力匮乏
- 带宽需求有待研究
- 频谱规划有待确定
- 法律法规尚未明确

**车联网低时延高可靠的演进**

由辅助驾驶-------->自动驾驶

![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-e9ec10a0e931c47d303bcae29134288a.png)

**C-V2X产业界的典型案例：**

- **华为C-V2X解决方案：**

- - 红绿灯信号机的对接
  - 高精定位
  - 高精地图动态图层
  - 丰富的路侧感知能力

- 可以用于城市、高速、封闭园区等场景

- - 在云端，华为的解决方案已经跟六家V2X平台进行过对接
  - 在路侧，信号机跟十几家知名厂商进行过对接，雷达跟四家厂商进行过对接，T-Box跟十家以上的厂商进行过对接

- ***\*中国信科基于C-V2X的研究起步较早\****

- - 大唐高鸿是中国信科集团旗下发展车联网产业的骨干载体，从2012年即开展我国核心知识产权的C-V2X（LTE-V2X和NR-V2X）技术标准研究、产品开发和市场推广工作
  - 其产品涵盖基于集团自研芯片的车规级模组、车载终端（OBU）、路侧设备（RSU）、路侧多传感器融合感知站等硬件产品，以及基于自研模组的LTE-V SDK开发包，ITS协议栈、LTE-V-APP、CA安全验证平台、云控平台等软件产品和方案
  - 基于智慧高速、城市道路、智慧园区、智慧出行、智慧矿山、智慧隧道等解决方案已在不同应用场景下完成部署，具有丰富成熟的落地项目经验

- ![img](http://mianbaoban-assets.oss-cn-shenzhen.aliyuncs.com/xinyu-images/MBXY-CR-559b2324d70942d73e6d15998f5c7883.png)

- 

## 对车联网未来展望和建议

- 积极推进LTE-V2X技术将向5G V2X演进，解决5G新空口（5G new radio，5G NR）的兼容性问题
- 大力推动C-V2X+ADAS深度融合，从单车智能走向网联智能
- 大力发展半导体产业，软件定义汽车，不能让芯片依然"卡脖子"
- 大力发展高精度定位和地图服务
- 政策要有所突破，通过政策和法规进行受控共享开放，推进商业模式的创新，推进路网运营商的形成
- 促进出行服务行业提供变革、保险行业商业创新
- 大胆创新，小心求证、我们有理由相信，基于车路协同的自动驾驶有望成为我国继高铁之后，实现由交通大国走向交通强国的又一张新名片