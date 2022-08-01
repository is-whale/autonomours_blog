- [ADAS/AD系统开发10 - ACC功能简介 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/51731936)

本文属于ADAS控制器开发系列。以智能前视摄像头模块为基础。

**前言**

ACC基本算是几种常见ADAS功能中实现起来最复杂的一个了。

ACC属于Headway类型的Feature，且属于舒适性ADAS功能，跟AEB这种NCAP强相关的安全类feature还不太一样。

ACC在快速的汽车智能化风潮中，迭代的很快，也有很多ACC版本同时在市场中存在。从我写该系列第一篇文章到现在，才过去4个多月，就又冒出来很多ACC的变种。这里在前言中一并梳理下：

ACC的最早版本是CC（定速巡航）开始的；

经历了ACC（自适应巡航控制），即加了跟车模式，能够在35kph至145kph（上下限都是标定值，不过就算有出入也差不到哪去）速度域开启的功能；

然后又迭代出了FSRA（或者叫ACC Stop&Go,下文的Stop&Go简写为S&G），FSRA是直接来自ISO标准的名字（德尔福就这么叫），ACC S&G我知道博世在用。FSRA - Full Speed Range ACC，全速域自适应巡航；ACC S&G，带自动启停的ACC。实质都一样，就是将0-35（这里35只是为了跟刚才说ACC35-145的速度域接上，反正意思就是把低速域的控制给补上了）速度域的空缺给补上。能够在ACC开启时，前车减速停车时，自车也跟停，然后再跟着前车起步的场景。

之后又有了带智能限速的ACC（Sli-ACC），通过识别路边的限速标志，来智能限速。假如你定速巡航设置的车速为120kph，在路上如果有限速80的标志出现，汽车会自动降低到80车速进行巡航。

去年又出了iACC（集成式自适应巡航），其实就是单车道的驾驶辅助，有纵向控制，也有单车道的横向控制。

**正文**

ACC模块除了Translator IN 和 Translator Out等因feature复用导致的常规模块外，主要包括

- 输入信号处理模块（Input Signals Processing）
- 自车状态检测模块（Host Vehicle State Detection）
- 目标车辆的坐标系转换模块（Target VCS to CCS），主要是笛卡尔坐标转化成曲线坐标
- 前车状态检测模块（Lead Vehicle State Detection）
- 激活抑制门限模块（Enable-Inhibit Latch）
- ACC状态控制模块（ACC Mode Manager）
- 车辆控制模块（Vehicle Control）
- 人机交互界面模块（HMI）
- 蜂鸣报警模块（Chime Master）