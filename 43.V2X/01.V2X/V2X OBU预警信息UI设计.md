- [V2X OBU预警信息UI设计 - 腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1907202)

Garmin研发中心正在开发下一代车载信息娱乐系统，因此计划将最新技术之一V2X（Vehicle to Everything）分阶段用于道路状况警报的新系统中。

本文作者是该项目用户体验设计的负责人，帮助团队研究V2X技术，开发趋势和其他竞争对手的相关应用，然后设计了针对不同路况场景的警报UI的概念，这些场景可以适应Garmin的下一代信息娱乐系统。

## 1、V2X简介

V2X是一种技术，它将信息从车辆传递到可能影响车辆的任何实体，例如交通信号灯等。它允许车辆相互之间或其他实体进行"通信"，以提高交通安全性。

Garmin研发中心计划将V2X技术逐步引入下一代信息娱乐系统。作为这个项目的用户体验所有者，我专注于6个V2X基本场景的路况变化设计。

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/349a028de3a895afd2c600ee692f344a.png?imageView2/2/w/1620)

## 2、V2X车载系统竞争分析

我根据V2X 6基本安全方案进行了竞争分析。 由于V2X是仍处于实验阶段的技术，因此在现实世界中没有这种通用的基础设施。挑战在于，没有参考嵌入V2X技术的真实信息娱乐系统，其他竞争对手主要是在路况警报的概念设计期间。

但是，根据这项研究，我仍然可以了解竞争对手如何为不同的场景设计警报用户界面，并且可以成为我们设计的良好参考。

我还向工程团队提交了分析报告，并与他们讨论了可能的实施方式。竞争分析有2个主要结论：

- 警告消息应尽可能简单直观，以便让用户尽快了解正在发生的事情
- 警告图标映射用户的视角（现实世界），使警告更加直观。

## 3、将路况聚焦到3个主要类别

从V2X 6基本安全场景来看，我发现它们实际上可以分为3大类，这有助于简化场景，并以更系统的方式分析这些场景：

- **案例1：前方路况警告** 当驾驶员前方发生路况时。
- **案例2：进站车辆警告** 当驾驶员经过十字路口时，会有来电车辆。
- **案例3：盲点警告** 当驾驶员与其他车辆在盲点中变道时。

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/99b7d2788599b84188ebf25f2204ac4e.png?imageView2/2/w/1620)

## 4、开发旅行地图

基于上述3个类别，我开发了一个旅行地图，用于设计警报系统的UI，以关注驾驶员在特定路况下的心态。

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/9d9756d604507c157939fb29dcc42fca.png?imageView2/2/w/1620)

## 5、设计指南

上面的旅行地图帮助我制定了设计指南：

- 定义**"紧急级别"：** 我根据驾驶员在特定路况下可以做出反应的时间定义了紧急级别。反应时间越短，应急水平越高。

记住紧急级别可以帮助我决定改变UI的强度。

在获得3个主要类别的路况场景和行程地图后，我可以更清楚地了解这3个场景的"紧急级别"：对于**案例1，**我们期望车辆以更高的速度移动，因此驾驶员做出反应的时间会更短，紧急级别会更高。

对于案例2和**案例3，**由于通常车辆正在改变车道或以较低的速度接近十字路口，因此驾驶员做出反应的时间会更长，因此紧急级别会更低。

## 6、UI概念设计和原型设计

在这个阶段，我根据之前的竞争分析、驾驶员旅程地图和设计指南设计了警报 UI，并考虑了 Garmin 当前平台的适用范围。

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/3ae98db92d420bdfa0923ee227be4324.png?imageView2/2/w/1620)

###### 案例1：前方路况警告

警报消息的设计理念来自竞争分析的要点，我使警告消息尽可能简单直观，并设计警告图标以映射用户在现实世界中的观点。

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/db9af9790fb918cc30651c6d36a00f41.png?imageView2/2/w/1620)

由于这种情况具有较高的紧急级别，因此警报消息从顶部弹出，以增加对视觉的影响并吸引驾驶员的意识。

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/171e488e728fdce485b4765d522186bd.png?imageView2/2/w/1620)

从旅程地图中，它还可以帮助我定义何时应弹出警报，以及何时将其关闭：

- 何时弹出：当另一辆车前方制动时。
- 何时关闭：当驾驶员踩下刹车或按下方向盘上的某个硬键时。

###### 案例2：进货车辆警告

**来往车辆警告警报**的设计 遵循相同的设计原则，警告消息设计简单直观，图标映射到用户的视角。

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/21cf37bb51a6b217f523fe0cac2dff7b.png?imageView2/2/w/1620)

遵循相同的设计原则，警告消息设计为简单直观，图标映射到用户的视角。

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/6207240b6f30acc2d42e6685ace773e0.png?imageView2/2/w/1620)

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/7169c72703dc323777fe4d052f3f6ad3.png?imageView2/2/w/1620)

**设计十字路口的路况预览**

由于 Garmin 工程团队还能够开发检测十字路口附近其他车辆并在仪表板上显示相对位置的技术，因此我帮助他们设计了一个快速原型，以在仪表板上显示它们。

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/ffd3e7f2e5856ee4a5f7267d483ad682.png?imageView2/2/w/1620)

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/c857f324b92d4d9f9653bcc96228e00d.png?imageView2/2/w/1620)

**十字路口路况预览和来电车辆警报的设计也可以组合成以下场景：**

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/2cba6f3cf294c6b0729ed4e1732fb078.png?imageView2/2/w/1620)

###### 案例3：盲点警告

**来往车辆警告警报**的设计 遵循相同的设计原则，警告消息设计简单直观，图标映射到用户的视角。

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/2ad353cb0ab05ccc74991b9eb9531173.png?imageView2/2/w/1620)

盲点警告的设计类似于进站车辆警告的设计，它从左侧或右侧弹出警报以指示进入车辆的方向，并使用原始UI（左侧的速度字段和右侧的齿轮字段）， 并减少对驾驶员的干扰，因为它是低紧急水平。

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/57a2eb71681a224ad4907d59e5786622.png?imageView2/2/w/1620)

![img](https://ask.qcloudimg.com/http-save/yehe-1758543/b9023949fb3b3d2c5bd73024e5087058.png?imageView2/2/w/1620)

从旅程地图中，它还可以帮助我定义何时应弹出警报，以及何时将其关闭：

- 何时弹出：当另一辆车接近盲点时。
- 何时关闭：当驾驶员踩下刹车或按下方向盘上的某个硬键或在3秒后自动结束时。

## 7、推出

最终的UI概念首先由Graphics UI团队针对着色和[视觉设计](https://cloud.tencent.com/solution/design?from=10680)进行了优化，然后交付给工程团队，供他们参考下一代信息娱乐系统的未来开发。

## 8、此项目的反思

由于资源有限，我们无法为该项目进行用户研究。如果资源可用，则可以进行用户研究，以观察驾驶员如何与信息娱乐系统交互或对不同路况做出反应，以构建可以更适合真实场景的旅程地图。 此外，如果将来有可用的资源，我希望可以在车辆中嵌入原型模型，以便我们进行用户测试，以评估警报系统设计的有效性。

原文链接：[基于V2X的车辆预警UI设计 — BimAnt](http://www.bimant.com/blog/v2x-based-road-alerts-design/)