- [V2X全息路面感知解决方案](http://www.mwradar.com/ruda888/vip_doc/21401218.html)

## 一、行业背景   

​    为抢占市场先机，近几年我国积极开展智能网联汽车测试示范区的建设工作，实施制造强国、网络强国和交通强国战略，以便为车联网产业提供充裕的测试环境和技术验证手段，引领国际车联网技术发展，在激烈的国际竞争环境中把握主动权。目前各地测试示范区建设已初具成效，充分利用各地地形、气候差异，实现示范区的差异化发展，但在推进示范区建设中，规模示范应用与行业服务能力建设方面存在不足，技术成果转换较慢且缺乏创新。为满足以上国家战略、经济社会效益、产业发展、居民出行的要求，满足车联网产业对基础环境标准化、行业服务能力、应用场景丰富性和运营管理能力等方面面临的关键问题，需要加速车联网创新技术转化及先导应用部署。 

​    在此背景下，2017年中国汽车工程学会发布了《合作式智能运输系统 车用通信系统应用层及应用数据交互标准》，定义了17个Day I应用场景，5类V2X数据集，主要针对辅助驾驶（安全警告类）。2020年中国智能交通产业联盟发布了《合作式智能运输系统 车用通信系统应用层及应用数据交互标准（第二阶段）》，定义了13个DAY  II应用场景，9类V2X数据集，主要针对路侧感知（共享、引导、协作规划类）。

​    经统计，DAY I应用场景有13个采用V2I通信方式，DAY  II应用场景中有9个采用V2I通信方式，这也从侧面印证了车路协同技术对自动驾驶的重要性。 为了能够将路侧信息精准快速地传递到车端，需要采用具备高精度、低延迟、高稳定的传感器和数据融合计算设备打造路侧感知系统。

## 二、现状分析 

​    当前国内车联网先导区或示范区所采用的路侧感知系统基本均为“前端感知设备+MEC”的组网架构，前端感知设备涉及的硬件包括视频监测设备、交通监测雷达和气象监测设备。摄像头实现路口斑马线，较近距离道路行人、非机动车、机动车的识别与预警;雷达实现路口各方向路段，中远距离车辆非机动车的识别与预警;气象监测设备用于探测周围环境信息，服务于智能网联汽车对外界环境的感知。MEC用于处理前端感知设备采集的环境数据并进行结构化，形成智能网联汽车、云平台、交通发布系统等可识别的数据信息。

​    由于交通场景的复杂程度不同，路侧感知环境需求也不尽相同，根据前端感知设备的差异，大致可分为如下三种建设方案：

1、毫米波雷达+激光雷达+摄像机方案：可实现对交通路况的原始信息感知，进而通过边缘计算能力，对数据进行处理，形成局部感知和统计结果，支撑路侧智慧应用。存在造价高，对MEC性能要求高等劣势，一般仅局部部署。

2、毫米波雷达+摄像机方案：可实现对行人、车辆、交通事件的检测和监控，路侧设备将传感器的原始数据进行处理、融合，最终得到盲区交通目标信息，通过智能路侧终端将该信息发送至智能网联车辆，车内的智能车载设备根据实际情况给驾驶员告警。MEC需要摄像机视频图像进行目标提取，并将结构化数据投放到雷达坐标中，存在数据延迟明显、MEC功耗高等弊端，还未能成熟落地。

3、摄像机方案：可实现对行人、车辆、交通事件的检测和监控，终得到盲区交通目标信息，通过智能路侧终端将该信息发送至智能网联车辆，车内的智能车载设备根据实际情况给驾驶员告警。由于摄像机采集视频清晰度易受天气影响，无法保证全天候运行，同时MEC需要对视频图像进行目标提取，容易产生数据延迟，故该方案仅适用于环境需求不高的路段，无法广泛推广。

鉴于上述方案的局限性，为解决当前面临的问题，我司对路侧感知应用场景和数据标准深入剖析，结合国内路况特点，推出了基于雷视融合的全息路侧感知解决方案，如下将进行详细介绍。

## 三、系统架构

​    基于雷视融合的全息路侧感知系统架构遵循C-V2X的技术架构，前端感知设备采用雷森雷视融合一体机，即可实现对路口、路段机动车、非机动车、行人及交通事件的全天候检测和监控，雷视融合一体机直接输出交通流矢量化数据，通过目标融合边缘服务器可对雷视数据做深度融合，输出路侧全息感知数据，同时还可部署满足C-V2X多数路侧应用场景的算法模型，在低延迟、低功耗、低成本的前提下，实现人车路高效协同。

![1617094474778313.jpg](https://aimg8.dlssyht.cn/u/1921947/ueditor/image/961/1921947/1629450016108256.jpg)

## 四、应用场景

​    基于雷视融合的全息路侧感知方案可满足C-V2X行业标准中定义的绝大多数场景应用需求，显著提升自动驾驶的安全性、时效性和舒适度。例如交叉口碰撞预警、左转辅助、前方拥堵提醒等11个DAY I应用场景和协作式变道、协作式交叉口通行、特殊车辆优先等9个DAY  II应用场景，均可采用雷视融合一体机作为路侧感知设备，采集输出路侧交通流的即时数据、统计数据、事件数据，为边缘计算服务器场景应用模型提供数据支撑，辅助系统控制和高效决策。以下对常用的4个场景进行示例说明：

### 场景示例1：前方拥堵提醒

道路在车流量大、路段管制或交通事故等情况下容易发生拥堵，未到达的车辆往往要开到拥堵路段时才发现道路拥堵事件，从而进一步加剧道路的拥堵，如果拥堵事件发生在弯道或隧道等特殊路段，车辆还可能会因为反应不及而发生交通事故，造成更大的损失。

![1617094827551368.jpg](https://aimg8.dlssyht.cn/u/1921947/ueditor/image/961/1921947/1629450057681477.jpg)

在基于雷视融合的全息路侧感知系统中，边缘计算服务器可以从部署在拥堵路段的雷视融合一体机实时获取交通路况及拥堵信息，然后通过路侧单元快速下发至周边车辆，提醒车辆减速避让或选择其他行驶路线，达到缓解道路拥堵、避免交通事故发生的目的。在该场景中，道路拥堵状况的初步判断和预警信息的下发都在边缘侧独立完成，同时事件也会上报给云端进行统计和显示，由中心云与公安交管中心系统进行信息交互，便于交通指挥人员掌握路况全局信息。

### 场景示例2：弱势交通参与者碰撞预警

​    车辆行驶到路口时一般要减速，防止与行人或非机动车碰撞。但由于驾驶者的视觉盲区或行人不守交规现象的存在，路口事故仍然频发。

![1617094845435275.jpg](https://aimg8.dlssyht.cn/u/1921947/ueditor/image/961/1921947/1629450081718091.jpg)

在基于雷视融合的全息路侧感知系统中，路口部署的雷视融合一体机可对穿行行人和车辆实时检测，边缘计算服务器基于行人的实时位置和信号灯的状态进行计算，将红灯或靠近车道的行人信息通过路侧单元下发至周边车辆，协助驾驶者做出减速决策。边缘云也可以将初步分析后的行人数据上报给中心云，中心云根据大数据预测行人行动轨迹，将离路口有一定距离、但正在接近路口的行人信息追加发送给车辆，进一步降低了事故发生率。在该场景中，边缘云快速响应路口实时出现的意外状况，中心云统一处理计算量大、俯瞰全局的数据，做到交通数据的最大化利用。

### 场景示例3：异常车辆提醒

​    不文明驾驶行为是影响道路出行安全的主要因素之一，为了纠正驾驶人违章行为，可借助雷视融合一体机对异常车辆进行检测，一旦监测到道路上的超速行驶、逆行、长期占用应急车道、频繁变道、异常停车等行为，将预警信息通过路侧单元下发至周边通行车辆，防止引发交通事故，造成不必要的人员伤亡和经济损失，同时边缘云还可将异常车辆取证信息（如关联视频、图片、即时数据）等上传至中心云，由中心云与公安交管中心系统进行信息交互，通过进一步分析判别，依法完成对违章事故的处置和处罚。

![1617094860687630.jpg](https://aimg8.dlssyht.cn/u/1921947/ueditor/image/961/1921947/1629450106733506.jpg)

### 场景示例4：匝道合流辅助

​    据统计，我国高速公路上有一半的事故发生在匝道口处。高速公路出口区域上游或下游路段通常还设有高速公路入口，在出、入口附近的路段，驶离、驶入、驶过高速公路的三股不同流向、不同速度的车流交织，形成了车辆间横向干扰，刚驶入的车辆与欲驶离的车辆速度相对较低，与主线正常通行车辆间又形成了纵向干扰。行车环境的复杂性，导致高速公路出口区域易发交通事故，加之有些驾驶人在匝道处突然变道、停车、倒车等，瞬间加剧了行车的危险性。

![1617094881119631.jpg](https://aimg8.dlssyht.cn/u/1921947/ueditor/image/961/1921947/1629450126137288.jpg)

通过在匝道合流处部署雷视融合一体机，对主路和匝道同时进行监测，对路面上的人、车、障碍物的位置、方向、速度等信息识别并上传，边缘云可将融合后的路况信息通过路侧单元下发至匝道口周边车辆，以便车辆能够及时获取到匝道口汇入汇出的道路态势，增强车辆对周边环境的感知范围和距离，从而避免交通事故的发生。同时边缘云还可将当前路况信息上传至中心云，由中心云与公安交管中心系统进行信息交互，协助交管部门完成道路监管工作。

## 六、系统优势

### 1、低延迟

​    视频数据与雷达数据的融合直接在雷视融合一体机中完成，无需再通过专业处理器对视频目标进行提取分析和坐标映射，最大限度降低数据延迟，数据延时可控制在50ms内，充分满足车路协同场景下时效性需求。

### 2、高精度

​    雷视融合一体机采用先进的信号调制方式，能够准确识别机动车、非机动车、行人的实时位置、速度、方向等信息，通过设置多维检测断面生成高置信度的交通流统计数据，并基于道路态势对异常事件精准感知。基于雷视融合的路侧感知系统对断面交通流的统计和异常事件识别的精度均在98%以上。

### 3、易标定

​    雷达与视频所采集的交通目标航迹、坐标、方位、速度等数据在雷视融合一体机中通过内置AI芯片完成深度融合，输出的即为全局矢量化数据，借助雷森自研的标定工具，单台雷视融合一体机的标定工作可在30分钟内完成，可显著降低调试难度，节省工程时间，更适合大规模部署安装。

### 4、全室外

​    基于雷视融合的路侧感知系统无需对视频目标单独提取，雷视融合一体机平均功耗低至12W，同时MEC采用“ARM+NPU”低功耗高性能架构和自散热机体结构，不存在因风扇散热导致灰尘进入的弊端，系统整体更适合在室外长期稳定运行，有效保护用户投资。