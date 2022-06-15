- [Apollo规划模块详解（五）：算法实现-lane change decider - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/439010910)

主要用来处理refer_line_info,内部有一个状态机，根据换道成功时间和换道失败时间以及当前位置与目标位置来切换状态，以此来处理refer_line_info的changelane信息。如果强制换道，把要换道的参考线放到第一个

![img](https://pic4.zhimg.com/80/v2-586d5a794bb3adb0f5ba98a254692b97_720w.png)

此task中最重要的一个函数就是IsClearToChangeLane(),用来判断动态障碍物对换道的影响来判断目标车道的参考线是否安全，不考虑虚拟障碍物或者静态障碍物

![img](https://pic2.zhimg.com/80/v2-df044ae2755d0b42c917eef9d4759b8d_720w.jpg)

关于IsClearToChangeLane()的内部处理流程，后面会做详细介绍。

根据前一时刻的换道状态以及当前的参考线信息（current_reference_line_info）进行强制换道（PrioritizeChangeLane）的处理，并更新换道状态：

没有换道参考线（参考线数量小于1条）：如果上个周期状态是已经换道完成或者换道失败，则返回进入下个task或者下个周期；如果上个周期状态是正在换道，更新换道状态

![img](https://pic4.zhimg.com/80/v2-a42742e6312718dd2b6a32618d560aa3_720w.jpg)

有换道参考线（参考线数量大于2条）：处理逻辑为下图流程图

![img](https://pic1.zhimg.com/80/v2-d59c275c55be2cb35f8da9409c999c34_720w.jpg)

里面调用最多的就是PrioritizeChangeLane(false, reference_line_info);和 PrioritizeChangeLane(true, reference_line_info);
首先获取第一条参考线的迭代器，然后遍历所有的参考线，根据第一个参数选择下面一个逻辑执行：更新当前迭代器里面最先查到的可换道参考线为第一条参考线。更新当前迭代器里面最先查到的非换道参考线为第一条参考线。IsChangeLanePath的判断标准就是当前车辆是否在该参考线所在的车道里。迭代器里都是按照顺序排放参考线的。

下面来详细介绍此task中比较重要的函数IsClearToChangeLane()的内部处理流程

a) 只对动态障碍物进行处理，忽略虚拟障碍物和静态障碍物；

![img](https://pic2.zhimg.com/80/v2-1748b390e68cc361bb282e8e4e20b659_720w.png)

b) 对于动态障碍物，先进行投影，获取S和L值；

![img](https://pic3.zhimg.com/80/v2-cae91d7754b87981ca2022853a3c7ebe_720w.jpg)

c) 忽略换道目标参考线上2.5米之外的障碍物;

![img](https://pic4.zhimg.com/80/v2-8d21f0d391dda5e548f23dcd4b295a53_720w.png)

d) 对于需要考虑的障碍物进行方向粗略计算，评估是否和自车同向；

![img](https://pic2.zhimg.com/80/v2-006924730fe3af968d9a2e549ad100a9_720w.jpg)

e) 根据方向，计算纵向上的安全距离，考虑了速度差，比较直观。分为前方和后方两个维度。

![img](https://pic4.zhimg.com/80/v2-df04238c00ba403acbf1af46bb22f7fb_720w.jpg)

f) 根据前面计算的阈值，判断障碍物是否安全，采用的是滞回区间的方法，如果障碍物小于安全距离，laneChangeBlocking为true。如果障碍物大于安全距离，laneChangeBlocking为false。通过滞回区间进行滤波。一旦发现有block的障碍物，函数就返回，就认为该Reference 非clear(安全)。

![img](https://pic1.zhimg.com/80/v2-458122644e1656c14a2df6b37a9cd884_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-585b0431f8cddc95adf1330ac273b13a_720w.jpg)