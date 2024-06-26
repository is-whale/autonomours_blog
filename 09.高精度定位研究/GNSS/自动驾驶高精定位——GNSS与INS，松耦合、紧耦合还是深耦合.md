- [自动驾驶高精定位——GNSS与INS，松耦合、紧耦合还是深耦合_AI汽车人-商业新知 (shangyexinzhi.com)](https://www.shangyexinzhi.com/article/4273869.html)

自动驾驶全局定位里最能打的非 **高精度组合导航** 莫属，空旷地带、短暂遮挡场景都是它施展才华的舞台。

目前可以 **实现** **全局定位** 的主要有 两套系统 ：GNSS（Global Navigation Satellite System，全球卫星导航系统)和INS（Inertial Navigation System，惯性导航系统）。

# **GNSS工作原理简介**

GNSS定位的基本原理 是通过测量在能接收到四颗及以上数量已知位置卫星到地面接收机的位置地方，同时综合四颗及以上数量卫星的数据，来计算出地面接收机的具体位置。

由于卫星误差、轨道误差、电离层和对流层误差等的存在，单纯GNSS的定位精度只有米级，无法满足高等级自动驾驶对厘米级定位的需求。

而 **RTK（Real - Time Kinematic，实时动态差分）** 技术的出现，通过铺设地面GNSS参考基准站，解决了实时获得厘米级定位精度要求这一难题。

![新知达人, 自动驾驶高精定位——GNSS与INS，松耦合、紧耦合还是深耦合](https://img.shangyexinzhi.com/xztest-image/article/0dd8dedede9fe3541323ece4cffe65df.png?x-oss-process=image/resize,w_670)

# **RTK定位原理**

Real - Time Kinematic

具体地说，RTK技术包含 参考基准站、流动站、通讯基站 以及可以接收到卫星信号的 解算卫星 。

参考基准站 实际上就是一个 固定位置的GNSS接收机 ，但是通过传统测绘等手段已知其准确的固定位置，并将此位置数据写入到参考基准站的处理单元中。参考基准站同时通过 接收卫星信号 来实时解算自身位置，而此位置是加入了钟差、轨道误差、电离层误差后得到的。

参考基准站将解算出来含有误差的定位数据与自身准确的固定位置进行比较，从而计算出由于卫星信号传递过程的误差所导致的定位偏差。而在参考基准站半径几十公里范围内可以认为这一误差对所有GNSS流动站来说都是相同的。

参考基准站通过5G/4G/WIFI/电台等无线通讯方式将偏差数据广播给覆盖范围内的流动站（通常覆盖半径为20Km）， 流动站 通过内部 **RTK算法进行差分解算** ，从而实时得到去除定位偏差后的厘米级定位数据。

但融合了RTK的高精度GNSS **存在如下缺点** ：

（1）在完全遮蔽、一颗卫星信号也搜不到的场景（隧道、高层密集建筑、浓密树荫等）是没办法输出准确定位数据的；

（2）在无网络通讯或通讯链路被切断时，无法获得参考基准站RTK差分数据，也将导致无法输出厘米级的定位数据；

（3）在不增加额外硬件条件下，是无法输出姿态（航向、俯仰、横滚角）信息的；

（4）在多金属的工作场景，由于严重的多径影响，会导致定位数据的假固定；

（5）定位数据输出频率较低（通常为5/10Hz），短期精度较低。

# **INS工作原理简介**

**INS** 是一种 彻底自主的导航系统 ，它不需要从外部接收信号，只靠内部的陀螺仪和加速度计硬件，在牛顿三大定律的魔法下，输出在导航坐标系下的三轴（x，y，z）加速度和角速度原始测量值，同时在惯性解算模块的积分下，可输出三轴的速度和角度信息。

更为人们熟悉的 **IMU** （Inertial Measurement Unit，惯导测量单元）则是当前INS系统里的主流硬件，集成了三个单轴的加速度计和三个单轴的陀螺仪。

与GNSS一样，IMU也是起源于军工。之前，受限于高昂的成本，IMU仅为国防和航天所用。直到价格更加亲民的MEMS加速度计和陀螺仪出现后，IMU才逐渐打开了商用和民用的市场。

INS可以输出高频（100/200HZ/2000HZ）定位和姿态数据，具有优秀的短期定位精度，但是 **单一INS** 存在以下 **缺点** ：

（1）由于解算模块存在积分计算，因此存在累积误差，而且随着时间的增长，误差会越来越大；

（2）高频振动会降低INS中IMU硬件的可靠性和精度；

（3）高精度的IMU成本（光纤陀螺）依旧很高。

既然各有所长，都是定位界的狠角色， 合二为一也是顺理成章的事情。

这么一合，问题就来了： 在什么时间进行耦合，在什么地点进行耦合，业界还未达成一致。

这也导致出现了 松耦合、紧耦合和深耦合 几种组合类型。本篇就来破解自动驾驶圈高精度组合导航里的 **“耦合”黑话** 。

# **高精度导航组成**

**整个高精度组合导航** 主要包括 GNSS模块 、 INS模块 和 组合导航模块 三大部分。

GNSS模块 主要由射频前端、信号捕获、信号跟踪、RTK解算四大单元组成。其中，射频前端是最重要的硬件部分，主要担负频率搬移、信号放大、噪声抑制的作用；信号捕获则是通过伪码对齐、载波对齐来实现信号的捕获；信号跟踪通过动态调整策略，来实现捕获到的伪码和载波信号的稳定跟踪；RTK解算结合伪距值和差分数据，输出RTK定位结果。

INS模块 主要包括IMU测量单元和解算单元。其中，IMU负责六轴自由度数据测量，解算单元负责对IMU输入数据及滤波器误差数据进行处理。

组合导航模块 ，根据耦合的程度不同，设计不同的滤波器耦合算法。

## **松耦合**

从名字就可以看出来，这是 最简单的一种组合方式 。

在松耦合结构中，GNSS和INS独立工作，GNSS输出RTK定位结果，INS输出惯性数据，两者将数据送入滤波器内。滤波器通过比较二者的差值，建立误差模型以估计INS的误差，并将误差补偿反馈给INS。最终得到位置、速度、姿态的组合导航结果。

这种简单的组合方式 优点 是易于实现，性能比较稳定。 缺点 是当卫星数量低于最低数量时，GNSS的输出就会失效。且在信号存在遮挡的场景，定位稳定性、可靠性不如另外两种耦合。

下图展示了松耦合的一种原理图：

![新知达人, 自动驾驶高精定位——GNSS与INS，松耦合、紧耦合还是深耦合](https://img.shangyexinzhi.com/xztest-image/article/bc5bd845339052670fb7831ff0e73df1.png?x-oss-process=image/resize,w_670)

△松耦合原理图

## **紧耦合**

从名字来看， 两者融合的程度更深 。GNSS输出观测量（伪距、伪距率）来与INS输出的惯性数据作差，并将差值输出给滤波器，从而用来进行INS误差的估计，并将误差补偿通过反馈的方式补偿给INS，经过校正的INS惯性数据输入到组合导航模块滤波器，结合RTK定位结果最终得到组合导航解。

紧耦合在原始GNSS观测量端进行融合 ，因此当卫星数量低于最低数量时，紧耦合的模式依然可以提供GNSS信号更新。但结构上会比较复杂，复杂带来的好处就是在相同硬件配置下，紧耦合的鲁棒性会更上一层楼。

紧耦合的难点 在于RTK算法要做组合导航的厂家自研。非RTK专业的厂家，很难把算法打磨到行业一流水平。毕竟卫星离我们3万公里，速度4千米/秒，通过载波相位双差，要使得行驶在各种场景下的载体保持实时厘米级精度且无论何时、何地都稳定运行还是有很大难度的。

![新知达人, 自动驾驶高精定位——GNSS与INS，松耦合、紧耦合还是深耦合](https://img.shangyexinzhi.com/xztest-image/article/fb1a5edfcd1ad81bf4bd9dcc1f3025c2.png?x-oss-process=image/resize,w_670)

△紧耦合原理图

## **深耦合**

**深耦合** 在紧耦合的基础上， 将INS的部分数据直接送到基带芯片里 ，INS的惯性数据作为GNSS解算的一部分。通过INS准确的相对多普勒变化信息，辅助信号跟踪，提高恶劣环境下多普勒的估计准确度。从而提高恶劣环境下载波相位、伪距等观测量的精度和连续性，减少观测量中断和跳变，从而有效提高组合导航精度和可靠性。

可以看到深耦合 直接在基带模拟端进行融合 ，因此，除了具备紧耦合算法能力外，还需要具备GNSS基带芯片模拟端接收能力，这意味着， 只有自研基带芯片能力的公司才有做深耦合的能力 。这也导致目前仅有少数公司掌握深耦合的技术。

![新知达人, 自动驾驶高精定位——GNSS与INS，松耦合、紧耦合还是深耦合](https://img.shangyexinzhi.com/xztest-image/article/729a255c9ee52fdd7e715c08037f1e4d.png?x-oss-process=image/resize,w_670)

△深耦合原理图

## **三种耦合比较**

在 **空旷、无遮挡环境** ，三种耦合方式都能杠杠地接收到三四十颗卫星信号，对于厘米级的定位精度简直是小菜一碟，因此在这种开阔环境下没有多大差别。

而在 **完全遮挡环境下（地库、隧道等）** ，三者都接收不到一颗卫星信号，这个时候即使深耦合可以辅助信号跟踪，可射频前端一个信号也没有输入，后端再牛逼也无济于事。因此在这种完全遮挡环境下，三种耦合方式也没有差别。

而在 **有部分遮挡的环境（高楼林立的城市、高大金属林立的港口等）** ，卫星信号时有时无、时好时坏（接收卫星信号的数量多时40多颗，少时个位数）。此时极易出现频繁失锁、观测量跳变等容易引发定位异常的现象，而基于更前端融合的 深耦合 可以通过辅助信号跟踪，从而非常优秀的 解决这个问题，紧耦合次之 。

某厂家某款产品实测发现，在部分遮挡环境下，深耦合定位精度是紧耦合定位精度的3倍，是松耦合定位精度的5倍。绝对是解决自动驾驶定位Corner Case领域的大杀器。

**然而** ， 深耦合固然是好，但是系统复杂，成本高 ，也不是所有场景都需要深耦合。

在 高速行驶 的情况下，卫星信号追踪存在实时性和精准性的问题，所以深耦合会很合适； 低速情况 ，本身卫导在信号追踪上就能处理好，松耦合会更经济实惠。这也充分考验自动驾驶开发团队对场景的理解。

**其实归根结底** ，无论哪种耦合算法，对于集成商来讲，是性能、品质、价格和售后综合考虑后的折中方案。

对于最终消费者来讲就是用户体验，如某某车型高速上可以NGP，某家车型可以扩展到复杂城市道路，那就牛掰了。

![新知达人, 自动驾驶高精定位——GNSS与INS，松耦合、紧耦合还是深耦合](https://img.shangyexinzhi.com/xztest-image/article/bf0de0724f5751ef9e9cc1e83d998ae1.png?x-oss-process=image/resize,w_670)

△ 几种耦合方案对比

# **主流玩家现状**

伴随着自动驾驶在各细分领域的遍地开花，高精度组合导航厂商迎来了难得的发展契机。做组合导航产品的企业也是百家争鸣，数量少说也有四五十家，大体可以分为 三种类型 ：

（1）非GNSS和IMU厂家，全外购做松耦合；

（2）GNSS厂家，外购IMU，做紧耦合或深耦合；

（3）惯导厂家，外购GNSS部分做松耦合。

而目前 **组合算法** 80%左右是松耦合，紧耦合算法凤毛麟角，深耦合就更少了。

这里介绍上述 **三种类型中有代表性的三家厂商的主打产品** 。

一家是测绘起家的代表， **华测（中海达、司南与之类似）** ；

一家是做IMU起家的代表， **导远** ；

一家是做GNSS芯片起家代表， **北云** 。

**华测** 依托其在测绘领域多年的积累，正在将这些宝贵的经验带到自动驾驶领域，其松耦合产品在前装市场已具有一定的市场，即将推出的紧耦合产品能否打开前装市场的大门，拭目以待；

**导远** ，基于IMU的研发积累，通过集成外购卫导板卡，专注于前装量产。在获得小鹏定点后，前装量产订单一举突破10万台，可谓前装量产市场一枝独秀。

**北云** ，专注于自研芯片和深耦合算法，在后装市场风生水起，但前装量产一直未有大的突破。其近期推出的千元级的车规级深耦合产品，让人期待。

三家当前主打产品介绍如下：

![新知达人, 自动驾驶高精定位——GNSS与INS，松耦合、紧耦合还是深耦合](https://img.shangyexinzhi.com/xztest-image/article/f20c44ad1fffbb6c96c9d3a8a9eac153.png?x-oss-process=image/resize,w_670)

△ 主流玩家及产品介绍

# **发展趋势和最终归宿**

高精度组合导航归根结底是一个传感器 ，所以其最终形态必将是芯片化、小型化的小模组，和域控制器融为一体。独立盒子形态的的PBOX很快会被时代淘汰。最终这个市场会有哪几家称雄称霸，就得看大家 芯片化的进程和速度 了。

如上文介绍，GNSS接收机硬件部分主要包括卫导模块（射频芯片、基带芯片）、IMU和处理单元。 想要实现芯片化，必须同时具备这四大模块的自主研发能力。

目前多数厂家的主流产品 将一块卫导板卡（包括射频、基带和处理单元）和惯导模块（IMU和处理单元）集成在一块大的PCB板上，加上自己的RTK及松/紧/深耦合算法，来提供零件级解决方案。

已有部分厂商 （诺瓦泰、天宝、北斗星通、北云等）推出板卡级别产品，通过在一块小的PCB上集成卫导、惯导模块和处理单元。上游自动驾驶厂商可灵活的进行集成、开发，从而实现板卡级的集成度更高的解决方案。

少部分厂家 在积极布局集成卫导板卡和IMU的SOC解决方案，这意味着他们将具备所有模块的自研能力，也为最终的芯片化铺平道路。