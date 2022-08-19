- [低速自动驾驶技术：APA_苏州程序大白的博客-CSDN博客_自动驾驶apa](https://blog.csdn.net/weixin_46931877/article/details/123014583)

# 简介：什么是“自动泊车”？

**自动泊车系统简称APA。**

搭载有自动泊车功能的汽车可以不需要人工干预，通过车载传感器、处理器和控制系统的帮助就可以实现自动识别车位，并自动完成泊车入位的过程。一般来说，在20万以上的中高端汽车上往往才有搭载，或者作为一项选装功能独立存在。(现在已经下探到15万左右，当然了，一般是自主品牌才敢给出这个极具性价比的配置)
![在这里插入图片描述](https://img-blog.csdnimg.cn/f7bb37652261477bb3dfb270be8d547f.gif#pic_center)
自动泊车系统可以大大简化泊车过程，特别是在极端狭窄的地方，或者是对于新手而言，自动泊车系统可以带来更加智能和便捷的体验。

# 💝自动泊车系统

## 💖定义

自动泊车系统主要是利用遍布车辆自身和周边环境里的传感器，测量车辆自身与周边物体之间的相对距离、速度和角度，然后通过车载计算平台或云计算平台计算出操作流程，并控制车辆的转向和加减速，以实现自动泊入、泊出及部分行驶功能。

**整个泊车过程大致可包含以下五大环节：**

- 环境感知
- 停车位检测与识别
- 泊车路径规划
- 泊车路径跟随控制
- 模拟显示

**按照泊车方式，分为三种模式，如下方图所示：**

- 平行式泊车
- 垂直式泊车
- 斜列式泊车

![在这里插入图片描述](https://img-blog.csdnimg.cn/c3c3b137732d4bbb8703878875a4cdb9.png)

按照自动化程度等级，自动泊车可以分为：

**半自动泊车**

**全自动泊车**

半自动泊车系统为驾驶员操控车速，计算平台根据车速及周边环境来确定并执行转向，对应于SAE自动驾驶级别中的L1；

全自动泊车为计算平台根据周边环境来确定并执行转向和加减速等全部操作，驾驶员可在车内或车外监控，对应于SAE L2级。

**按照所采用传感器的种类，半自动/全自动泊车可以分为：**

- 超声波自动泊车
- 基于超声波与摄像头的融合式自动泊车

**两种传感器的对比如下方图所示：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/51dd473a57534ba8b66104dc0c6a1947.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_20,color_FFFFFF,t_70,g_se,x_16)

## 💗原理方案

**整个泊车过程是哪几个环节？**

`环境感知`、`停车位检测与识别`、`泊车路径规划`、`泊车路径跟随控制`以及模拟显示五大环节！

**下面我们就以最常见的超声波自动泊车系统为例，从五大环节来介绍：**

```
▲环境感知
```

如下方图所示，为一种典型的超声波自动泊车系统的环境感知方案，由12个超声波雷达组成。
![在这里插入图片描述](https://img-blog.csdnimg.cn/7d24d482d89440dda3b2641a08f2e1ea.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_13,color_FFFFFF,t_70,g_se,x_16)
**■8个超声波雷达**：泊车过程中检测车身周边的障碍物，避免剐蹭。

**■4个超声波雷达**：泊车开始前进行车位的探测及在泊车过程中提供侧向障碍物信息。

```
▲停车位检测与识别
```

自动泊车超声波车位探测系统主要是由布置在车身侧面的超声测距模块构成的， 通过超声传感器对车辆侧面的障碍物进行探测， 即可完成车位探测及定位。

**超声波车位探测的过程如下方图所示。在探测车位时， 车辆以某一恒定车速V平行驶向泊车位：**

1、当车辆驶过 1 号车停放的位置时，装在车身侧面的超声波传感器开始测量车辆与 1 号车的横向距离 D。

2、当车辆通过 1 号车的上边缘时，超声波传感器测量的数值会有一个跳变，记录此时时刻。

3、车辆继续匀速前进，当行驶在 1 号车与 2号车之间时，处理器可以求得车位的平均宽度W。

4、当通过 2 号车下边缘时，超声波传感器测量的数值又发生跳变，处理器记录当前时刻，算得最终的车位长度L。

5、处理器对测量的车位长度 L 和宽度 W 进行分析，判断车位是否符合泊车基本要求并判断车位类型。

![在这里插入图片描述](https://img-blog.csdnimg.cn/80f5c43bba3f45a696114383aabc906e.png)

```
▲泊车路径规划
```

**考虑到自动泊车实现原理，泊车路径规划一般尽可能满足以下要求：**

a、完成泊车路径所需要的动作必须尽可能少。

因为每个动作的精度误差会传递到下一个动作，动作越多，精度越差。

b、在每个动作的实施过程中，车辆的转向轮（绝大部分为前轮）的角度需要保持一致。

因为系统是通过嵌入式系统实现的，而嵌入式系统的性能有限，转向轮角度保持一致能够将运动轨迹的计算归结为几何问题，
反之需要涉及复杂的积分问题，这对嵌入式系统的性能是一个挑战。

一般平行泊车和垂直泊车采用如下方两图所示路径。

平行泊车分为单次和多次：

单次为如下方图所示路径一次泊车完成；

多次则为当车位长度比较小时，可采用多次“揉库”的方法泊车。

![在这里插入图片描述](https://img-blog.csdnimg.cn/c7f71b1c9fb34a94a8b59be114b59bfa.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_9,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/520fc17dd0ca49bc8fbd38989172bdc5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_11,color_FFFFFF,t_70,g_se,x_16)

```
▲泊车路径跟随控制
```

该过程为通过车载传感器不断探测环境，实时估算车辆位置，实际运行路径与理想路径对比，必要时做局部校正。

```
▲模拟显示
```

由传感器反馈构建泊车模拟环境，具有提示与交互作用。提示用户处理器意图以及做必要的操作。

另外，路径规划后进行泊车时为了知晓处理器定位和计算路径运行情况，需要将这些处理器信息反馈给用户。

如果处理器获取环境信息或者处理过程中出现重大错误，用户可以及时知晓与停止。

# 🙊自主泊车系统

## 💖定义

随着自动驾驶技术的发展，自动泊车逐渐往自主泊车方向演进。

**自主泊车又称为代客泊车或一键泊车：**

- 指驾驶员可以在指定地点处召唤停车位上的车辆，或让当前驾驶的车辆停入指定或随机的停车位。
- 整个过程正常状态下无需人员操作和监管，对应于SAE L3级别。

**自主泊车系统包含两个功能，即泊车与唤车：**

```
>>>>泊车功能
```

是指用户通过车载中控大屏或手机APP选定在园区、住宅区等半封闭区域内的停车位或者选定停车场（有高精地图覆盖）。

然后车辆通过获取园区、住宅区等半封闭道路上的车道线、道路交通标志、周围其他车辆等交通环境、参与者信息。

控制车辆的油门、转向、制动来实现安全自动驾驶，并通过自动寻找可用停车位或识别用户选定停车位。

实现自动泊入、自动停车、挂P档、熄火、锁车门，同时防止潜在的碰撞危险的功能。

```
>>>>唤车功能
```

是指用户通过手机APP选定园区、住宅区等半封闭区域内的某一唤车点，

然后车辆从停车位自动泊出、低速自动驾驶到达唤车点，

从而实现唤车，同时防止潜在的碰撞危险的功能。

## 💗原理方案

**按主要技术路线，自主泊车系统可分为：**

- 偏车端方案
- 偏场端方案
- 车端场端并重方案

**偏车端和偏场端的自主泊车方案对比如下方图所示：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/0c2552bb1c894086bd515cc2b5ee8cd7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_12,color_FFFFFF,t_70,g_se,x_16)
**偏车端自主泊车系统方案：**

典型的偏车端自主泊车系统的组成见以下图所示和下方表所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/0d0939c02217487da5904a82b6fa5ecc.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_15,color_FFFFFF,t_70,g_se,x_16)
**主要传感器信息：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/52c241d119224e838e03520bbfdaa53a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_20,color_FFFFFF,t_70,g_se,x_16)
偏车端方案的系统逻辑流程图见下方图所示：

由图可知，偏车端方案主要借助车载传感器对周围环境以及自身状态的感知来决策并执行车辆动作，并在必要时提醒用户进行车内或远程接管操控。

![在这里插入图片描述](https://img-blog.csdnimg.cn/6aa55588811c4175a9d5dbd338f35817.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_15,color_FFFFFF,t_70,g_se,x_16)

**偏场端自主泊车系统方案：**

如下方图为一种偏场端方案的系统示意图：

◤在停车场内布置激光雷达或双目摄像头来实现对车辆状态及周边环境的监控，通过预埋式停车场传感器探测当前占用状态。

所有传感器数据均在数据中心进行汇总分析，根据储存的元信息（如停车位尺寸、费用、诸如残疾人停车位等的特殊情况等）完成匹配。

数据中心根据这所有的信息实时生成停车地图。

驾驶员通过智能手机APP接收所有的信息，从而始终能了解最近可用停车位的概况，以及所有相关详情，如距离和价格。

而车辆只需要具备与停车场设施的通信能力和可控的底盘执行系统，即可在场端的辅助下完成自主泊车。◢

![在这里插入图片描述](https://img-blog.csdnimg.cn/336ec296532544679602f74197bd89a1.png)

# 🐵自动泊车系统

**自动泊车系统组成及功能如下方表所示：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/d7510706d2af40abbf4ad0db1d27c106.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_20,color_FFFFFF,t_70,g_se,x_16)

# 🐒自主泊车系统

自主泊车系统方案如下方图所示，主要采用智能化车端+智能化场端的方式。

车端智能化主要依赖于融合式全自动泊车的传感器配置，外加前视摄像头、V2X设备等实现特定区域内的点到点自动驾驶、自动车位扫描、自动泊入泊出等功能。

车辆自身具备车辆、行人等动态障碍物检测和识别功能，可实现自动紧急制动、避障等决策规划。

场端智能化主要依托摄像头检测技术，实现停车场车位占用情况检测，并上传至停车场服务器，并实现为自主泊车车辆提前分配车位信息。

![在这里插入图片描述](https://img-blog.csdnimg.cn/e0a868ccc07f47ab876c5e12ba21e1af.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_14,color_FFFFFF,t_70,g_se,x_16)

```
▲障碍物坐标检测及多目标识别
```

超声波传感器单纯的距离检测能力在泊车预警辅助场景已可满足使用要求，但是在智能化泊车应用场景、及多传感器融合应用中还远远不够。

为此开发了障碍物坐标检测技术及多目标识别技术，如下方两图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/08848fd09efc4e6bb1bada6aeb267cbf.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_9,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/cb169012a74f43849ceb6bdcb325c5e0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_10,color_FFFFFF,t_70,g_se,x_16)

```
▲高精度车位检测及车位融合
```

基于超声波传感器可实现空间车位的探测、360环视摄像头可实现线车位的检测。

同时结合超声波传感器及环视摄像头的障碍物信息检测，对车位进行多层次的融合，实现泊车位的高精度检测，大大提升了泊车场景的覆盖范围。如下方图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/70260bc51ded4ee3ad248c906da559fd.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_12,color_FFFFFF,t_70,g_se,x_16)
`▲轨迹动态规划技术`

泊车过程中有诸多不可控因素，如转向系统执行速度与精度问题、参考障碍物位置变动问题等，导致在泊车过程中出现泊车轨迹偏离路径规划轨迹现象。

为此开发了泊车轨迹动态规划技术，可实现泊车过程中的轨迹实时修正甚至轨迹重规划，如下方图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/ac2dfa1e5066435d986df5811bf21fdb.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_10,color_FFFFFF,t_70,g_se,x_16)

```
▲室内定位技术
```

如下方图所示，通过采用视觉SLAM+标签辅助定位方式，解决地下停车场无GPS的问题，同时通过多源信息融合，提升定位精度。
![在这里插入图片描述](https://img-blog.csdnimg.cn/577e69a7a2f8475ea336f6d4f0579bb7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_9,color_FFFFFF,t_70,g_se,x_16)

```
▲基于视觉的停车场车位状态检测技术
```

如下方图所示，通过停车场安装的监控摄像头，基于深度学习算法，实现车位占用状态的实时检测，并将此信息上传至停车场车位管理后台服务器，为自主泊车车辆提供可泊车位信息。
![在这里插入图片描述](https://img-blog.csdnimg.cn/01260bb0a5f1442caea5de6e2d9e63d0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_9,color_FFFFFF,t_70,g_se,x_16)

```
▲全局与局部路径规划技术
```

如下方图所示，基于`A*算法实现`任意两点间的全局路径规划，支持路径规划重置、选路以及速度规划功能，同时结合实时环境感知状态，进行局部路径规划，实现紧急制动、 跟车巡航以及换道避让、 换道超车等自主决策。

![在这里插入图片描述](https://img-blog.csdnimg.cn/bb954047948f431dbc236e5239da4f30.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_9,color_FFFFFF,t_70,g_se,x_16)

# 💥以上总结

泊车场景作为用户痛点感受最深，技术实现相对容易，客户最愿买单且最有机会率先落地的场景，是乘用车L4自动驾驶企业兵家必争之地。

之前看过一篇文章，里面列举了汽车十大最没用的配置，自动泊车位列其中。而随着自动泊车从半自动到全自动发展，我们看到了自动泊车作为低速自动驾驶更多的闪光点。自动泊车也逐渐从“鸡肋”变成了“真香”。

![在这里插入图片描述](https://img-blog.csdnimg.cn/c961624dd0674b008fd5b222eb54269b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6IuP5bee56iL5bqP5aSn55m9,size_14,color_FFFFFF,t_70,g_se,x_16)