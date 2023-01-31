- [无人驾驶的关键：高精度定位盒子P-Box (qq.com)](https://mp.weixin.qq.com/s/gyOG-4zwsO1Vw-nS62p0GQ)

无人驾驶或者车道级导航最基础的根源是基于绝对定位的车道级定位，想象一下，高速公路上不知道自己在哪条车道上，很有可能会错过出口或走错路，当然谈不上智能驾驶了。用视觉或激光雷达的相对定位只能知道相对于参照物的位置，高速公路上各车道间是没有差异明显的参照物做对比。视觉对光线变化很敏感，光线每时每刻都在变化，数据的一致性绝对不可能，逆光与背光完全不一样。因此**智能驾驶的导航离不开绝对定位，尤其是路径规划阶段。再有就是自动泊车也需要高精度的绝对定位**。认识到定位的重要性后，一些车厂开始导入高精度定位盒子，即P-Box，准确地说应该叫高精度与高准确度定位盒子。比较典型的P-Box有华测导航的CGI-220、广州导远的INS570D、戴世智能的IFS-2000。



那么P-Box与目前车机上常用的导航定位有什么差异？主要是精度差异，单纯GPS L1/L2波段，不加卫星增强的P-Box的CEP精度至少应该达到1.5米，普通车机上是2.5米。不过现在P-Box的定义似乎是只要支持RTK就是P-Box。



GNSS定位精度单位常见的有CEP、RMS、2D RMS。均方根差（RMS），英文名为：root mean square error，中国学者将其称为“中误差”或“标准差”。它的探测概率以置信椭圆（confidence ellipse，用于二维定位）和置信椭球（confidence ellispsoid，用于三维定位）来表述。



置信椭圆的长短半轴，分别表示二维位置坐标分量的标准差（如经度的σλ和纬度的σφ）。一倍标准差（1σ）的概率值是68.3%，二倍标准差（2σ）的概率值为95.5%，三倍标准差（3σ）的概率值是99.7%。许多中外文献所述的“精度”多为一倍标准差（1σ），且用距离均方根差（DRMS）表示二维定位精度。



距离均方根差也称为圆径向误差（circular radial error）或均方位置误差，另有一些作者常采用“双倍距离均方根差”（2DRMS）。笔者认为做车道级L3级智能驾驶，至少要达到95.5%的概率，即定位精度2DRMS达到1.5米，做市区无人驾驶，2DRMS至少要达到1米。当然业内目前没有统一的标准。



圆概率误差（CEP）是在以天线真实位置为圆心的圆内，偏离圆心概率为50%的二维点位离散分布度量。注意，50%不代表平均值，95%概率的二维点位精度（R95），是在以天线真实位置为圆心的圆内，偏离圆心概率为95%的二维点位精度分布度量。球概率误差（SEP），对于三维位置而言，则以球概率误差表示。球概率误差（SEP）是在以天线真实位置为球心的球内，偏离球心概率为50%的三维点位精度分布度量。CEP乘以1.2能转换为RMS，CEP乘以2.4能转换为2D RMS。



拿5M CEP说吧，意思是以5M为半径画圆，有50%的点能打在圆内，也就是说，GNSS定位在5M精度的概率是50%，相应的RMS（66.7%）2DRMS（95%）当然很多商家愿意给出CEP，因为单位大了，前面的数就小了，好看。特斯拉的GPS模块是NEO-M8L-01A-81，水平精度圆概率误差(CEP)为2.5米，加上卫星辅助后是1.5米，转换成RMS就是1.8米，转换成2DRMS就是3.6米。



这些都是基于理想状态下的测量值，所谓理想状态就是4颗导航卫星的仰角不低于15度，仰角也有称之为截止高度角。但在高楼林立的市区或山谷，GPS卫星仰角很容易低于15度，或者没有GPS信号。定位一般需要4颗卫星，三线确定距离，定位3D空间2个点（保留近地点，舍去远地点），一星确定时间戳精度。在卫星信号丢失的情况下，就需要IMU了。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvBTFYfmP7aicjct2A5nOibtZxMkrz7abn7iaIUlZQqKNLh9aaGXYAMoKqeQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：知乎



一般IMU包括三轴陀螺仪（刚体也可以在3个自由度中旋转：纵摇Pitch、横摇Roll和垂摇Yaw）及三轴加速度计（刚体可以在3个自由度中平移：向前/向后、向上/向下、向左/向右），即6DOF，某些9轴IMU还包括三轴磁力计，即9DOF，还有加上高度计，即10DOF。



- 加速度计：检测物体在载体坐标系统独立三轴的加速度信号，对单方向加速度积分即可得到方向速度；
- 陀螺仪：检测载体相对于导航坐标系的角速度信号；
- 磁力计：检测载体相对于地球磁场的东南西北方向信号。



测量物体在三维空间中的角速度和加速度，并以此解算出物体的姿态，陀螺仪知道“我们转了个圈”，加速度计知道“我们又前进了几米”，磁力计知道“我们是向西偏北方向的”。



IMU的工作就是DR（Dead Reckoning）航迹推算：根据t时刻车身的加速度，推算t+1时刻的位置。注意，高通芯片已经包含了DR算法，业内称之为QDR黑盒算法。IMU价格差异极大，低端的价格大约1美元，高端超过100万美元。早期研发阶段的无人车如百度的，一般使用价格接近20万人民币的外置专业IMU，NovAtel IMU-IGM-A1 10秒单点的水平精度为1.41米，简单说就是GPS信号丢失10秒，不借助任何辅助因素，单IMU可以将水平位置推算误差保持在1.41米内。如果是2美元的MEMS IMU，如最常见的日本TDK的ICM-20948，估计10秒单点的水平精度超过40米。车载领域常见的是博世的6轴IMU，价格大约1美元。P-BOX用的通常是TDK的IAM-20680或IAM-20685，大概7-10美元。估计10秒单点的水平精度超过10米。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvBtRhe8fpapNMibbXCsP3IZvPUUJCIeFkhPjdyxRf4JS2qYSJ1icnrKrIg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网



上图是典型的车道级定位原理图，使用了三个扩展开曼滤波器和一个HMM隐马尔可夫模型。也有将IMU称之为INS惯性导航系统。



**典型P-BOX框架图**

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvB3ohbemGmRqwH4DmPicSSeqcvXpcoztTVTCXJAnXfU3szZegAvjUCicNw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网



P-BOX实际就是一个增加了车辆信息输入的高精度GNSS接收机。说到高精度定位，RTK也是不可或缺的元素，也就是图上的移动网络差分信息，实际就是增加一个4G模块。



RTK是Real - time kinematic的缩写，是一种差分定位。其原理是利用一个参考站提供基准观测值，然后用设备的观测值与基准站的观测值进行差分，差分后可以消掉星历误差、卫星钟差、电离层误差，再进行星间差分后可以进一步消除掉设备的钟差，最终可以算出设备相对基准站的相对坐标，如果基准站位置已知，就可以完成准确的绝对坐标，精度可以达到厘米级甚至毫米级。



RTK能提升精度的另一个原因是引入了载波相位观测，相比伪距观测值，载波相位观测值的误差更小。使用RTK，需要在附近20km内有参考站（距离太远，电离层误差不一样，做差分无法完全消除误差），同时需要持续不断的获得参考站的观测数据（一般通过互联网传输，使用RTCM协议），因此相对普通的定位，RTK定位成本较高。RTK服务一般由专业服务商提供，如千寻位置、六分科技，这些服务商在全国范围内部署了数千个基准站，持续对订阅用户播发数据。现在的服务费据说很低，一年不到一千元人民币。RTK的算法则是全免费的。



不过RTK也有缺点，那就是播发数据一般要依赖无线通信网，也就是手机。通常RTK都是和地地基增强在一起，即CORS（Continuous Operation Reference Stations）即连续运行参考站系统，网络CORS主流技术有四种，分别是VRS、主辅站技术（i-MAX）、区域改正参数（FKP）技术和综合误差内插法技术。其中VRS技术市场占有率最高，是目前公认的主流，VRS由天宝公司发明。南方公司则对VRS进行了改进，命名为NRS，本质上还是VRS。



GNSS部分主要就是采用诺瓦泰Novatel的板卡，近年来U-BLOX以及和芯星通凭借价格优势也快速崛起。



通常只有demo级无人车才会用双模GNSS接收机，例如百度一直用NovAtel的ProPak6，天线是NovAtel GPS-703-GGG-HV，现在ProPak6是老产品，在打折，大约要2万人民币。



**NovAtel的ProPak6**

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvBaxxRfh3TMQX7v8lwiag4vs3uXQcGn7JMJGLgxLDBvnEMLwVBhM1Q0Vw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



图片来源：NovAtel



单点双频可做到1.2米级的定位，RMS是1 sigma或1倍标准差，如果结果是无偏的，概率为67%。也就是说67%的情况下定位可到1.2米，其余情况就做不到了，可能是2米，也可能3米。缺点就是太贵了，还有装一个露在外面的天线，这恐怕是量产车无法接受的。量产车能接受的大约是5000元以下。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvBRfwvKt7BicVv3kicWA9N7vbicic5HQS567Yyx7fIibFckSx34dQAIicDgyBw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：NovAtel



诺瓦泰的背后则是意法半导体的Teseo APP，这是最早达到ASIL-B级的高精度定位套片，包括调谐器STA5635S和引擎STA9100MGA。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvB0Hwbib4ol9X8tumgEmYojIxxu4ib0Apodb4DxF5wyMibfQpHHQIU2T47w/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：NovAtel



诺瓦泰7系列框架图，MINOS7是诺瓦泰第七代芯片，也是诺瓦泰的核心技术。



U-BLOX的F9P目前最为常见，板卡价格大概700-800人民币，整机价格不超过5000元人民币。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvBPdcnRJ4iawSTch5wOwicoMaSnCyibj50pqYzEIhwVJJbtKe7BJNYLcrgQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：U-BLOX



F9P参数如上表，与诺瓦泰7系列比主要是通道数少很多，诺瓦泰有400-500通道。车机的GPS通常只有50以下通道数。诺瓦泰7系列的RTK精度达到1毫米+1 ppm cep，F9P是10毫米。不过F9P的温度上限达到85度，诺瓦泰是75度，F9P冷启动是24秒，诺瓦泰是39秒以下。



还有很多人忽视的天线，天线非常重要。测绘级天线典型的如诺瓦泰的GPS-703-GGG-HV，体积颇大，直径185毫米，厚度大约69毫米，重量530克，典型电流36毫安。车厂当然无法接受这么大的天线，并且无人驾驶可能需要3个天线，定位、定向、定姿态，车厂能接受的都是鲨鱼鳍那样的智能天线，不过即使直径70毫米的天线其信号强度仅仅是通常使用的测量型天线180mm的15%，鲨鱼鳍天线就不用说了。由于RTK的载波跟踪需要使用信号更加微弱的L2\E2\B2，RTK还没上车性能就大打折扣。



**奔驰旗舰EQS的天线系统**

![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/2icOarNW84W7Od9eZyv76veNKj3kicUVvBRfVicM7hpHAXk0k25SzxXWLpgqib9H3Np3nuS5M5AlRic52hq1ywxKj6Q/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：FCC



上图是奔驰旗舰EQS的天线系统，体积巨大，超过1米长，估计是放在后备箱处，这样大的天线或许能用RTK系统。



P-BOX厂家的核心资产是算法，算法分两种，一种是松耦合度算法Loose Coupled，将IMU和GPS接收机各自输出的位置/速度解算值做差，差值作为组合导航融合滤波器的输入，从而通过滤波器来估计出INS的误差，再利用估计误差来对惯导结果进行修正，最终得到松耦合导航的输出结果。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvB0G8y7QME0icUWfDLvb9ibDt0cqrgMVbOWOAcj5lqCNHCnibgqJ0Z5toPQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网



紧耦合算法Tightly Coupled高大上，不过一般只有测量级GNSS接收机才具备。紧耦合结构利用伪距/伪距率来实现导航子系统耦合，GPS跟踪环路进入稳定跟踪后提取/计算出伪距/伪距率；INS经解算后的定位结果，根据卫星星历计算的卫星位置和速度，推算出相应卫星的伪距、伪距率。再次，把INS和GPS对应的伪距/伪距率进行比较后得到两子系统伪距/伪距率偏差，作为组合导航融合滤波器的观测量，通过融合滤波器生成包含INS定位位置/速度/姿态及惯性器件（陀螺仪和加速度计）和接收机时钟误差在内的误差状态。最后，利用滤波估计误差状态去修正INS定位信息并输出该信息，输出的信息即为组合导航定位信息。近来还有人提出超紧耦合Ultra-Tightly Coupled算法和深耦合，深耦合大约介于紧耦合和松耦合之间。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvBkO3muMJSD5JwAn4VDoVysoia6Gor63qQArc2NGTMjO9DvVmiaDytPlvQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网



基于相位载波的紧耦合算法如上图。深耦合是发展方向，不过需要长时间摸索，没有人能短期内掌握深耦合。



最后来拆一个高精度GNSS接收机来看。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvBdjWJAGW0EChZzbwxaUVUdjAoMDr41PocAt5TVVxNiacjQU4Wialcshzg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：FCC



整个底座就是个大天线，直径估计有180毫米。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvBYElrYu5iaQxO0xxT07tWXicRZK9gcxONWqch9UI2eTQ0pQ2qrzpWOFDw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：FCC



底座背面，这样仍然需要一个专业的外接天线。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvBa1FQEN52X7KgdCwLJY9bxZ65dbJCUibWibgORbRXcdlgZW3GbIzo79tA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

三个大模块。图片来源：FCC



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvBbZAoXRBu1ePVcoWhJqT5JnkQHUxAsuFwCnicR9gUaTHqIONvtOWXhkw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

和芯星通的UM4B0模块拆开的样子。图片来源：FCC



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvBAcO3MawH0q5cTvfJwKE8OVicPTjlaWvPI7qt6K5DZmMf6pTzsiafrK5g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

4G通信模块，核心是高通的MDM9207。图片来源：FCC



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7Od9eZyv76veNKj3kicUVvB25pD0YphjeVshsg8aqqlfNk3DM0gwzViaftFwzjYUQYXMfAYrAQNt1A/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

主处理模块核心是NXP的I.MX6Y2C。图片来源：FCC



即便不考虑无人驾驶，车道级导航还是需要P-BOX的，P-BOX市场前景还是非常广阔的。