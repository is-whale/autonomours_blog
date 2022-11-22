- [OpenV2X 车路协同 Beihai 版本发布 (qq.com)](https://mp.weixin.qq.com/s/QtANHVrskau2to1n2RzkIw)

## 1 OpenV2X 简介

OpenV2X 致力于提供一个车路协同“路侧”开放基础架构的开源解决方案。

1. 在“云-边-端”三层架构中，OpenV2X 主要关注“远边”（5G/6G 基站侧边缘云计算），以及基于通用硬件的“近边”（RSU 工控机）计算，不涉及软硬件耦合的 RSU 工控机部分。
2. 在“车路协同”框架中，OpenV2X 只关注“路侧”开放基础架构，不涉及“车载”部分。

2022年6月30日，OpenV2X Albany 版本发布，Albany 版本提供了路侧设备（RSU、毫米波雷达、摄像头）管理及MAP 配置，事件管理中包含了路侧单元信息 RSI、路侧单元消息 RSM、逆向超车预警三种事件信息，同时支持边缘站点的配置及云控大屏的路况信息查看。

**2022年11月18 日，OpenV2X Beihai 版本发布。**

## 2 Beihai名称的由来

《孟子·梁惠王上》中说，“挟泰山以超北海”，极言其难。智慧车路已发展多年，但距离 L4 技术成熟仍路漫漫其修远。苏武牧羊也在北海，十九年望雁听风，吞毡饮雪而志节不改。如今智慧车路中重重的技术难关正是我们的面前“北海”，OpenV2X 研发团队也会像苏武面对北海那样潜心磨砺，矢志不移。

## 3 OpenV2X 功能介绍

### 3.1 功能列表

| 产品模块                          | 功能点         | Albany 版本 | Beihai 版本 |
| --------------------------------- | -------------- | ----------- | ----------- |
| OpenV2X 设备管理平台              | RSU 设备管理   | ✅           | ✅           |
| 摄像头设备管理                    | ✅              | ✅           |             |
| 毫米波雷达设备管理                | ✅              | ✅           |             |
| 信号灯设备管理                    | ❌              | ✅           |             |
| 激光雷达设备管理                  | ❌              | ✅           |             |
| MAP 配置                          | ✅              | ✅           |             |
| RSU 信息上报、日志下发配置        | ✅              | ✅           |             |
| 边缘站点配置                      | ✅              | ✅           |             |
| 事件管理 - 路侧单元信息 RSI       | ✅              | ✅           |             |
| 事件管理 - 路侧单元消息 RSM       | ✅              | ✅           |             |
| 事件管理 - 逆向超车预警           | ✅              | ✅           |             |
| 事件管理 - 弱势交通参与者碰撞预警 | ❌              | ✅           |             |
| 事件管理 - 交叉路口碰撞预警       | ❌              | ✅           |             |
| 事件管理 - 感知数据共享           | ❌              | ✅           |             |
| 事件管理 - 协作式变道             | ❌              | ✅           |             |
| OpenV2X 云控中心                  | RSU 设备在线率 | ✅           | ✅           |
| 摄像头设备在线率                  | ✅              | ✅           |             |
| 毫米波雷达设备在线率              | ✅              | ✅           |             |
| 激光雷达设备在线率                | ❌              | ✅           |             |
| 信号灯设备在线率                  | ❌              | ✅           |             |
| 摄像头视频展示                    | ❌              | ✅           |             |
| 激光雷达点云图展示                | ❌              | ✅           |             |

### 3.2 设备管理模块

相较于 Albany 版本，Beihai 版本增加了信号灯及激光雷达设备的增删改查管理，提供了信号灯和激光雷达设备接入的标准接口。通过各类标准接口，可以将各路路侧设备接入到边缘节点进行统一的管理、运维、监控，增强可维护性。

![图片](https://mmbiz.qpic.cn/mmbiz_png/OzEcg7CtMiaIicJa2RgibVEBfkvvgUnpUjTicpLymPeKqlKsG0ZHCxoghczBNAYORwgXj1ccyxTB4sibyC2l7sF7FKw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 3.3 事件管理模块

增加了弱势交通参与者碰撞预警、交叉路口碰撞预警、感知数据共享、协作式变道四种团标定义的事件类型。实现了碰撞预警算法、感知数据共享算法、车辆协同换道算法、逆行超车预警算法等交通场景算法。

![图片](https://mmbiz.qpic.cn/mmbiz_png/OzEcg7CtMiaIicJa2RgibVEBfkvvgUnpUjTnPjt1ZwmSF6iaLvztDn36XzlWjIISz1S0XQsG5gamavJ7MvEotXgBRw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

### 3.4 云控中心模块

增加信号灯在线率和激光雷达在线率的接入，支持查看摄像头视频及雷达点云图。雷达点云图能够以较高的精度反映路口交通的实际情况，视频和点云图的相互结合，可以更好的了解路况信息，及时发现交通事故。

![图片](https://mmbiz.qpic.cn/mmbiz_png/OzEcg7CtMiaIicJa2RgibVEBfkvvgUnpUjTC5FIrYSfsg11GKjhyQ62x0kADqiaZH1Bq9jrsVN1thPcHzvicBlOE8CA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### 3.4.1 视频流展示原理

Hippocampus 是 OpenV2X 视频感知模块，支持视频目标检测（VID）和多目标跟踪（MOT）。摄像头生成的 rtsp 流，经过 hippocampus 算法服务的图像处理，生成 rtmp 流输出到 lalserver，lalserver 将rtmp 流转换为 http-flv 形式的数据流，前端读取 http-flv 渲染在前端。更多信息可参考<https://github.com/open-v2x/hippocampus>

![图片](https://mmbiz.qpic.cn/mmbiz_png/OzEcg7CtMiaIicJa2RgibVEBfkvvgUnpUjTxgctyUfso8DKcFNYVfXOloSmqcXTMdvGicJxPyHCAdgzQnC8UyW2y7g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

#### 3.4.2 雷达点云图展示原理

雷达生成的数据通过 udp 传输至 lidar 服务，经过解析生成点云图数据，通过 websocket 传输至前端进行渲染。更多信息可参考 <https://github.com/open-v2x/lidar>

## 4 结语

OpenV2X 开源项目计划实现设备、GIS 和综合可视化管理等多个功能，后续版本会持续完成车辆慢行、车辆超速、拥堵等事件识别，以及绿波车速引导、紧急车辆提醒等29个典型应用场景。OpenV2X 开源项目希望通过提供信息服务、保障交通安全、提高交通效率、实现自动驾驶等功能，打造智能交通数字基础，支撑智慧公交、无人驾驶清扫车、无人驾驶货运卡车等典型应用场景，赋能实现自动驾驶和自主交通。

1. Github 地址：

   https://github.com/open-v2x

2. Gitee地址：

   https://gitee.com/open-v2x

3. OpenV2X All-in-One 部署文档：

   https://github.com/open-v2x/docs/blob/beihai/docs/v2x-quick-install.md

4. OpenV2X 用户快速操作手册：

   https://github.com/open-v2x/docs/blob/beihai/docs/v2x-quick-start.md

5. 开发者指南：

   https://github.com/open-v2x/docs/blob/master/docs/v2x_developer_guide-zh_CN.md