- [一文读懂UWB超宽带技术 - 通信技术 - cnBeta.COM](https://www.cnbeta.com/articles/tech/1142875.htm)

目前已经大规模使用UWB超宽带技术的消费电子品牌并不多，大家最熟悉的一个品牌是苹果。搭载苹果U1超宽带芯片的[iPhone](https://apple.pvxt.net/c/1251234/435400/7639?u=https%3A%2F%2Fwww.apple.com%2Fcn%2Fiphone%2F)[手机](https://c.duomai.com/track.php?site_id=242986&euid=&t=https://shouji.jd.com/)之间可以实现快速的文件共享功能，手机靠近内置超宽带芯片的HomePod mini智能音箱即可播放音乐、获得个性化聆听建议；与内置超宽带芯片的AirTag绑定后，iPhone便具备了室内精准定位、追踪物品的能力。

那么到底什么是UWB超宽带技术、UWB技术的应用场景还有哪些，以及UWB技术的市场有多大，今天我爱音频网就来跟大家详细聊聊。

## 一、什么是UWB技术？

UWB（Ultra Wide Band）超宽带技术是一种使用1GHz以上频率带宽的无线载波通信技术，它不采用传统通信体制中的正弦载波，而是利用纳秒级的非正弦波窄脉冲传输数据，因此其所占的频谱范围很大，尽管使用无线通信，但其数据传输速率可以达到几百兆比特每秒以上。

[![img](https://static.cnbetacdn.com/thumb/article/2021/0620/ec8d8db22986d84.png)](https://static.cnbetacdn.com/article/2021/0620/ec8d8db22986d84.png)

图源：Qorvo半导体

UWB技术具有系统复杂度低，发射信号功率谱密度低，对信道衰落不敏感，截获能力低，定位精度高，穿透能力强等优点，尤其适用于室内等密集多径场所的高速无线接入。

UWB并非是一项新技术，它最早应用于军用雷达和通信应用中，名称为“脉冲无线电”，自2002年起，美国联邦通信委员会（FCC）授权其未经许可使用，为其划分了3.1~10.6GHz频段，占用500MHz以上的带宽。

**与大家熟知的、可以提供定位服务的GNSS、Wi-Fi和低功耗蓝牙三项技术相比，UWB技术的优势如下：**

首先，传统的全球导航卫星系统（Global Navigation Satellite System，GNSS），可以提供卫星定位服务，但由于卫星信号会被建筑物遮挡，因此不能实现室内定位。

Wi-Fi和蓝牙通常是通过接收信号强度的指标 (RSSI) 来确定位置，仅显示“弱”或“强”接收信号的粗略类别，定位精度最高能够达到米级。

UWB定位有三种技术，分别是TOF（Time of flight）时差法、TDOA（Time Difference of Arrival）到达时差和PDOA（Phase Difference Of Arrival）到达角相位差。虽然这仅适用于较短的范围，但可以以小于 50 厘米的精度（在最佳条件和部署下）和极低的延迟来确定信号源的位置。

[![img](https://static.cnbetacdn.com/thumb/article/2021/0620/2f9d8a1b90d7021.png)](https://static.cnbetacdn.com/article/2021/0620/2f9d8a1b90d7021.png)

通过这张图大家可以更直观地了解到UWB技术与Wi-Fi、蓝牙技术的不同点。简言之，**UWB技术的优势在于：**

1、定位精度高：带宽很宽，多径分辨能力强，抗干扰，对于距离的分辨能力高于Wi-Fi和蓝牙。

2、实时定位速度快：UWB的超宽带脉冲信号的带宽在纳秒级，可以实现实时的室内定位，延迟低，可以即刻感知追踪物体的运动状况。

3、高可靠性和安全性：UWB的发射功率低、信号带宽宽，能够很好地隐蔽在其它类型信号和环境噪声之中，传统的接收机无法识别和接收，必须采用与发射端一致的扩频码脉冲序列才能进行解调。

当然，UWB、Wi-Fi和蓝牙这三项技术并不是孤立存在的，完全可以同时使用，优势互补，能够给智能手机这样的终端产品带来多种需求的定位和数据传输服务，对于相关的天线和射频设计有较高要求。

## 二、UWB技术有标准组织吗？

每项无线传输技术通常有一个标准组织负责相关标准的制定和技术认证等，比如蓝牙技术联盟、 Wi-Fi联盟、国际电信联盟等等，在UWB技术领域，目前主要有FIRA联盟和UWBA超宽带联盟两大组织，均成立于2019年。

[![img](https://static.cnbetacdn.com/thumb/article/2021/0620/67eda3c6f363d63.jpg)](https://static.cnbetacdn.com/article/2021/0620/67eda3c6f363d63.jpg)

FiRa联盟致力于开发和广泛采用可协同操作的超宽带 (UWB) 技术所带来的安全精细测距和定位功能，为用户提供无缝的使用体验。

FiRa联盟的成员主要开发基于 IEEE 802.15.4 标准的UWB安全精细测距技术的产品，跨各种垂直业务领域开发 UWB 技术的应用场景。FiRa联盟与其他行业组织密切合作，例如 IEEE、Wi-Fi 联盟、汽车连接联盟 (CCC) 等，注重在可用的 6-9 GHz 频段中运行的 UWB 用例。

[![img](https://static.cnbetacdn.com/thumb/article/2021/0620/7ae265221ccdf4e.jpg)](https://static.cnbetacdn.com/article/2021/0620/7ae265221ccdf4e.jpg)

FiRa联盟的成员分为赞助会员、贡献者成员、准会员、采用者成员、测试实验室成员、学术和[教育](https://c.duomai.com/track.php?k=yUSQzUycwRHdo1Ddm0DZpVXZmkDN2ITPklWYmYDO5IDNy0DZp9VZ0l2cmYiJ05WZkVHdzZkMl42Yu02bj5SZy9GdzRnZvN3byNWat5yd3dnRyUiR)成员这几类，很多都是大家耳熟能详的品牌。

[![img](https://static.cnbetacdn.com/thumb/article/2021/0620/831cde3620535ea.jpg)](https://static.cnbetacdn.com/article/2021/0620/831cde3620535ea.jpg)

UWB Alliance 超宽带联盟专注于在 802.15.4z 标准下提供有利的监管和频谱管理环境，以最大限度地促进 UWB 市场的增长。超宽带联盟旨在促进各类垂直行业展示 UWB 对物联网和工业 4.0 的价值，在从芯片到服务的整个 UWB 价值链中构建全球生态系统。

此外，超宽带联盟当前在倡导新的频谱，拟在欧洲将 UWB 扩展到 12.4 GHz 以及新兴的更高频段。

[![img](https://static.cnbetacdn.com/thumb/article/2021/0620/2b8f4b972e5aa21.jpg)](https://static.cnbetacdn.com/article/2021/0620/2b8f4b972e5aa21.jpg)

超宽带联盟目前的成员有以上品牌，同样有很多知名的半导体厂商。

我爱音频网后续会单独出一篇UWB芯片原厂和方案商汇总的文章，敬请期待。

## 三、UWB技术有哪些应用场景？

得益于苹果的大力宣传，UWB技术近年来在消费电子领域逐渐被大家熟知，但是其在工业和商业领域的运用已经非常成熟了，是RTLS（Real Time Location Systems）实时定位系统的重要组成，主要利用的就是UWB超宽带技术定位精度高、短距离通信传输速度快、能够快速响应等特点。

今天跟大家分享的主要是UWB技术目前在消费电子类产品上的应用场景。

[![img](https://static.cnbetacdn.com/thumb/article/2021/0620/febeee736c966b0.jpg)](https://static.cnbetacdn.com/article/2021/0620/febeee736c966b0.jpg)

自iPhone 11系列手机以来，苹果已先后在Apple Watch S6、HomePod mini智能音箱、iPhone12 系列手机和Aritag无线追踪器这些产品内，应用了自沿的UWB方案，其控制芯片是苹果U1。

三星主要是在手机上导入了UWB方案，具体型号包括Galaxy Note 20 Ultra、 Galaxy Z Fold 2、 S21+和S21 Ultra，同时三星也推出了采用UWB技术的SmartTag+智能追踪器。

国产手机品牌小米推出了名为“一指连”的UWB连接技术，充分发挥自己智能家居生态的优势，未来将能够与各类智能家居产品联动。此外，[OPPO](https://c.duomai.com/track.php?site_id=242986&euid=&t=https://oppo.jd.com/)也曾在科技大会上展示了手机与 IoT 设备间的精准定向和精准操控能力。

[![img](https://static.cnbetacdn.com/thumb/article/2021/0620/3537882b7db00c6.png)](https://static.cnbetacdn.com/article/2021/0620/3537882b7db00c6.png)

苹果起初在iPhone 11系列机型上推出U1芯片，利用UWB技术的高速传输能力，提升了“隔空投送”功能的传输速度，同时利用其精准的定位能力，“隔空投送”会优先与指向的iPhone建立连接。

[![img](https://static.cnbetacdn.com/thumb/article/2021/0620/728fc0e29e50c89.png)](https://static.cnbetacdn.com/article/2021/0620/728fc0e29e50c89.png)

使用搭载U1超宽带芯片的iPhone靠近同搭载U1的HomePod mini智能音箱，音箱正在播放的音频会顺畅过渡到 iPhone 上接着听；没有播放音乐时，拿着 iPhone 靠近 HomePod mini，屏幕上会弹出个性化的音乐播放推荐，无需解锁手机。

[![img](https://static.cnbetacdn.com/thumb/article/2021/0620/4fa14d6c085c385.png)](https://static.cnbetacdn.com/article/2021/0620/4fa14d6c085c385.png)

AirTag无线追踪器是蓝牙低功耗与UWB超宽带两项技术的结合，凭借超宽带技术，用户可以在手机上看到 AirTag 离得有多远，该朝哪个方向找，便于资产追踪。

![一文读懂UWB超宽带技术-我爱音频网](https://www.52audio.com/wp-content/uploads/2021/06/2021061912132284.gif)

小米“一指连”UWB技术功能演示。手机指向支持UWB技术的电[风扇](https://c.duomai.com/track.php?site_id=242986&euid=&t=https%3A%2F%2Flist.jd.com%2Flist.html%3Fcat%3D737%2C738%2C751)，手机上会弹出一个控制卡片，进而实现对风扇的操控，无需实体遥控器，手机也不用再下载单独的APP。除此以外，小米“一指连”还实现了智能[电视](https://c.duomai.com/track.php?site_id=242986&euid=&t=https%3A%2F%2Flist.jd.com%2Flist.html%3Fcat%3D737%2C794%2C798%26ev%3D4155_76344%26sort%3Dsort_rank_asc%26trans%3D1%26JL%3D2_1_0%23J_crumbsBar)投屏、智能门锁靠近即开等操作。

[![img](https://static.cnbetacdn.com/thumb/article/2021/0620/ec2a960911a727c.jpg)](https://static.cnbetacdn.com/article/2021/0620/ec2a960911a727c.jpg)

FiRa联盟官网的这一张图，更加详细地展示了 未来UWB技术会对我们生活产生的影响，包括：数字车钥匙、身份识别、室内导航、线下购物行为分析、展会数据分析、无线支付、VR游戏、手势识别等等。

[![img](https://static.cnbetacdn.com/thumb/article/2021/0620/100c179d81f52b0.png)](https://static.cnbetacdn.com/article/2021/0620/100c179d81f52b0.png)

前面提到，基于UWB超宽带技术的RTLS实时定位系统目前在工业端已经非常成熟了，主要应用于工业制造（跟踪人员、车辆和设备等）、位置跟踪（特殊建筑物如监狱等特殊场所中的人员/设备/文件定位）、仓储物流（图书馆、电商行业等）、无线测量、智能驾驶（车辆自动出入库），以及增强现实、竞技体育等领域，应用非常广泛。

## 四、UWB市场有多大？

UWB超宽带技术在工业端已经发展了很多年，有数据表明，未来UWB技术将在室内定位市场中占据30%~40%的市场份额，到2022年其市场规模将有望达到164亿美元，市场前景非常广阔。但这里主要跟大家讨论的是UWB技术在消费端未来的市场能有多大。

目前UWB超宽带技术要想实现上述功能，最需要依附的主要产品就是智能手机。单以苹果、三星已经支持UWB技术的手机来说，就已带动了UWB芯片千万级的销量。

国内市场方面，目前已经推出“一指连”技术的小米，其2020年高端手机的销量近1000万台，AIoT生态已经逐渐完善，米家APP月活用户在4500万，其迭代产品都有升级UWB技术的可能；未来还会有OPPO、[vivo](https://c.duomai.com/track.php?site_id=242986&euid=&t=https://vivo.jd.com/)等手机头部厂商加入进来。

可以说，中高端手机庞大的出货量，可以持续引领C端UWB市场的发展，进而带动智能手表、智能可穿戴、智能家居等各类UWB设备的出货增长。

据 ABI Research 预测，支持 UWB 的智能手机出货量将从 2019 年的 4200 万多部增加到 2025 年的近 5.14 亿部。

[![img](https://static.cnbetacdn.com/thumb/article/2021/0620/c3301d8c985c909.jpg)](https://static.cnbetacdn.com/article/2021/0620/c3301d8c985c909.jpg)

不仅如此，专注于工业级解决方案的UWB技术厂商们，在看到消费端市场的巨大发展潜力后，也纷纷入局，比较具有代表性的是清研讯科，他们是FIRa联盟唯一一家具有投票权资格的中国解决方案商，目前已经推出了兼容Android生态的类AirTag硬件产品TicTag，并将为OEM开放硬件平台，提供兼容802.15.4z及FIRa的平台级产品，可以帮助更多消费品牌快速进入这一市场。

超宽带联盟认为，自 2019 年以来，超宽带一直在扩展为智能手机、可穿戴设备、汽车和工业的主流消费技术，预计到 2025 年每年将推动超过 10 亿台设备的销量。

## 我爱音频网总结

UWB超宽带不是新技术，其功能以定位为主、通讯为辅，在体量上未能成为像蓝牙、WiFi那样的通用标准有多方面的原因。近年来在苹果的带动下，UWB技术被C端市场和更多消费者所了解，因为在万物互联时代，它的确是让各室内设备间互通互联的一项有效技术，简化了配对的流程，未来仍然有很大的发展空间和市场机会。

同时我们也看到，要想让移动设备拥有更好的空间感知能力，需要一整个应用生态的支持，目前苹果已经开始搭建，三星、小米、OPPO等品牌也逐步跟进，届时不同厂家的设备能否实现互操作、互兼容可能又是一个新的问题。

可以预见的是，未来会有更多企业和产品采用UWB技术，不仅仅是智能手机、可穿戴设备、智能家居等小物件，室内空间也需要各种基于位置的 UWB 基础设施。据了解，预计到2022-2024年间，随着IEEE 802.15 4z标准开发完成，UWB传感器和执行器将能够组合成一个无线网络，更积极地应用于大众市场和企业解决方案，为室内定位和短距离通信增添一种新的维度。