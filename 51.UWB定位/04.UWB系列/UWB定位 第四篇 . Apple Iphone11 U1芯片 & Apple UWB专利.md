- [UWB定位: 第四篇 . Apple Iphone11 U1芯片 & Apple UWB专利 - 简书 (jianshu.com)](https://www.jianshu.com/p/1b0c64d6c298)

## Iphone11 & U1芯片

2019年9月在美国旧金山举行的Apple秋季新品发布会上，Apple推出了Iphone11系列手机，在发布会上提及的Iphone11系列手机的最大亮点是采用了最新的高性能低能耗A13处理器芯片，升级了屏幕和相机配置，以及从软硬件上联合优化了多镜头拍摄时的图片和视频成像质量。

![img](https:////upload-images.jianshu.io/upload_images/19885429-dcafa1218ff08545?imageMogr2/auto-orient/strip|imageView2/2/w/660/format/webp)

Iphone11x Features

![img](https:////upload-images.jianshu.io/upload_images/19885429-9d56923f52a9c8fe?imageMogr2/auto-orient/strip|imageView2/2/w/659/format/webp)

Apple Iphone11 Wireless

在发布会上没有直接提及到的是Apple在Iphone11系列手机新增加了一个代号为U1的UWB(Ultra Wideband，超宽带)硬件芯片，配置的U1芯片使得Iphone11系列手机具有更快的数据通信速度和更强的'**空间感知**'能力，简单的说，利用UWB技术带来的高精度测距定位特性，使得配有U1芯片的Iphone11手机可以和附近其他同样具有U1芯片的设备互相准确地判断出彼此间的**相对位置关系**。精确的位置感知能力可以带来很多便捷和智能的应用，例如：

- 房间/汽车门禁：通过高精度的测距测向能力，当识别到用户靠近门禁时自动打开，相比Bluetooth等其他使用RSSI(接收信号强度)技术，UWB使用的TOF测距方法可提供更高的精度以及有效地防止中间人攻击；
- 物品防丢失：给手机、钱包等易丢失的物品配备UWB定位芯片后，用户可进行高精度(<10cm)的实时监控和追踪；
- 智能家居系统：通过感知用户在家里的位置信息，可有效地对环境的温度、湿度、光强等进行控制调整，从而带来更智能化的体验和更绿色的能效；
- 智能设备控制器：通过确定Iphone11和被控制设备(如电视、空调等)的相对位置和姿态信息，可无需手动切换而智能化地实现利用手机对周围的电器设备进行控制；

![img](https:////upload-images.jianshu.io/upload_images/19885429-55a38009af03e434?imageMogr2/auto-orient/strip|imageView2/2/w/660/format/webp)

隔空投送(AirDrop)

Apple在Iphone11系列手机宣传页面上提到的UWB技术在Iphone11上的使用场景之一是增强'**隔空投送(AirDrop)**'功能，利用U1芯片输出的测向测距结果，Iphone11系列手机可以根据用户的手机指向准确地从附近的人中判断出的用户想要进行文件分享或信息传输的目标，并将其优先置顶以方便用户快速地进行选择。

## Apple AirTag

![img](https:////upload-images.jianshu.io/upload_images/19885429-b5e30c0dfa545717?imageMogr2/auto-orient/strip|imageView2/2/w/462/format/webp)

AirTag概念图

Apple正在开发一款贴片式(Tile-like)的电子追踪器(Tracker)，可置于钱包或贴在笔记本电脑等便携式物品上，通过iPhone对其进行实时的追踪和监控以防止丢失。该追踪器可能同时支持UWB和Bluetooth连接，其中UWB适用于具有U1芯片的iPhone11系列手机对追踪器提供精准定位，而Bluetooth用于兼容旧款iPhone/iPad/Mac等。据MacRumors披露，Apple的电子追踪器可能是一个在正面中心处具有Apple标志的小圆形标牌(Tag)[[1](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.macrumors.com%2F2019%2F10%2F08%2Fapple-tag-tile-new-3m-adhesive-sticker-tracker%2F),[2](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.macrumors.com%2Fguide%2Fairtags%2F)]，此外，从iOS13.2中新增的AirTag文件夹和Apple购买AirTag商标这些信息来推测，该追踪器可能命名为AirTag[[3](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.macrumors.com%2F2019%2F10%2F28%2Fapple-bluetooth-tracker-airtag-name%2F),[4](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.cnet.com%2Fnews%2Fapple-tags-are-rumored-to-track-your-stuff-everything-we-think-we-know%2F)]。Apple在2019.09.10的秋季发布会上并没有提到该标牌，不过据预测其很可能在2019年底或在2020的春季发布。

## Apple UWB相关专利

Apple在Iphone11系列手机加入UWB芯片使得这款产品的主要市场客户是普通消费者，但UWB技术能带给用户哪些可能的应用？

下文我们大致解读下Apple最近几年申请的且具有代表性的几个关于UWB技术及其应用的专利文件。

### Beacon Triggered Processes

### Electronic Devices with Motion Sensing and Angle of Arrival Detection Circuitry

![img](https:////upload-images.jianshu.io/upload_images/19885429-a829f77d5f74b126?imageMogr2/auto-orient/strip|imageView2/2/w/660/format/webp)

AOA定位示意图

该专利是Apple于2019.04进行申请的，专利号为`US20190317177A1`。专利描述了配备有到达角(AOA)检测电路和运动感知器件的电子设备如何计算自己和周围设备间的相对位姿(位置+姿态)关系，其中到达角检测电路用于计算(通过PDOA等)其他设备发出的信号到达接收时相对自己的角度，而运动感知器件(如加速度计、陀螺仪、IMU、轮式编码器等)用于确定自己的相对位移等信息。通过确定设备自身和周围设备的相对位姿关系，设备可以快速地从周围设备中筛选出想要进行信息文件分享的对象从而快速建立高速可靠的短程的无线数据传输链路。虽然专利中提到达角检测电路指基于IEEE80.15.4的UWB多天线的系统，但最新的Bluetooth5.1标准也可提供到达角度测量，因而该专利除了适用配备有UWB芯片的设备外也可适用于配备有支持Bluetooth5.1芯片的设备。该专利最简单的应用场景是用户找寻特定/丢失的位置固定的物品，用户自身的运动行为符合专利的适用条件，因而用户可以在无需使用其他数据/设备辅助的情况下仅利用手中的移动计算设备如Iphone11来快速确定该物品的位置。

### Time Instant Reference For Ultra Wideband Systems

![img](https:////upload-images.jianshu.io/upload_images/19885429-778d707fff3c8987?imageMogr2/auto-orient/strip|imageView2/2/w/660/format/webp)

UWB脉冲形状互用协议

该专利是Apple于2018.12进行申请的，专利号为`US20190199398A1`。该专利和Apple在2017.08申请的专利号为`US10171129B1`的专利"Pulse Shaping Interoperability Protocol for Ultra Wideband Systems"内容大部分相同，因而在这里我们仅介绍最新的这一篇专利。在专利中，Apple提出了一种在UWB设备间通过交换无线测距脉冲形状的信息并利用这种信息来增强测距精度的方案，更好的测距精度有助于在复杂的多径环境下应用高级的信号检测与估计技术。

### Mobile device for communicating and ranging with access control system for automatic functionality

![img](https:////upload-images.jianshu.io/upload_images/19885429-b8a6deaa2f4f9b27?imageMogr2/auto-orient/strip|imageView2/2/w/659/format/webp)

UWB+BT智能访控系统

该专利是Apple于2018.05进行申请的，专利号为`US20190135229A1`。专利提出了一种同时使用UWB技术和Bluetooth技术的用于进行安全的认证授权的访问控制系统，用户通过携带支持UWB和Bluetooh的移动设备如手机、手表等可实现**无感**的身份认证和行为授权，因而该系统属于**被动门禁**系统。该系统可用于各种需要进行安全验证的情形，如车门、房门、安检门等的自动开闭、汽车引擎的授权启动、电子设备的授权操作等。其中Bluetooth主要用来建立安全的数据交换信道用户进行用户授权认证、UWB测距协议参数设置等，而UWB则用来实现高精度的距离测量从而确定移动设备和访问控制系统的相对位置。尽管专利中提到需要同时使用两种无线协议的原因是由于Bluetooth无法提供高精度的距离测量，而UWB当前无法提供固定延迟的可靠数据交换链路，但我们认为随着制定UWB技术标准的的Fira和UWBAlliance这两个组织推动完善IEEE802.15.4协议，最终我们可仅使用UWB技术来实现智能化的访问控制系统。

### Enhanced Automotive Passive Entry

![img](https:////upload-images.jianshu.io/upload_images/19885429-99bd3407f08b5104?imageMogr2/auto-orient/strip|imageView2/2/w/660/format/webp)

UWB+Mag PEPS系统

该专利是Apple于2018.02进行申请的，专利号为`US10285013B2`。专利给出了一种判断移动设备(如手机、手表等)和汽车之间相对位置关系的方法，从而可用于实现智能汽车的无钥匙进入及启动系统PEPS。该系统结合低频(LF)的磁场信号和射频(RF)的电磁信号(如UWB等)测距信息，然后通过机器学习模型过滤干扰/噪声数据并给出融合后的位置估计。结合无线电测距信息一方面可提高位置感知精度和感知范围，另一方面也可避免针对磁场测距系统的中间人攻击从而使得系统更安全。而结合磁场测距信息则可避免在特定情形下(如多径环境等)无线电测距结果的较大误差。

### System And Method for Vehicle Authorization

![img](https:////upload-images.jianshu.io/upload_images/19885429-c75292c140309460?imageMogr2/auto-orient/strip|imageView2/2/w/660/format/webp)

UWB+智能生物特征验证系统

该专利是Apple于2017.02进行申请的，专利号为`US20190039570A1`。当前用于智能汽车的钥匙仅使用了具有较低安全性单步验证机制，因而任何拿到该钥匙的人都可以操控汽车，此外，大多数无线汽车钥匙所用的技术方案都不能对抗中间人攻击。专利提出了一种基于生物统计识别的认证系统，通过检测用户/设备**靠近**汽车过程中的生物数据，例如步态、身形、人脸、声音、指纹等特征，可以有效的鉴别用户身份和使用汽车的权限，以及对授权用户提供个性化的操作模式。利用UWB技术等所提供高精度的用户位置感知能力，可极大地提高汽车预测用户可能的下一步行为，如开启车门、启动引擎、远离汽车锁定车门等。