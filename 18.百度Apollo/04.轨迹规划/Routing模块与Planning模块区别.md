- [杂谈-Routing模块与Planning模块区别 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/486835272)

## 1.Routing简介

routing类似于日常生活中使用的导航软件，当我们需要前往某个地方时，输入当前位置作为起点，输入目的地信息作为终点，导航根据现有的路网信息，规划处一条最优路径（可以选择不同条件触发最优：例如公交、驾车、高速优先等），如下图所示：

![img](https://pic1.zhimg.com/80/v2-44f54e9f33ab6f0646be98c794fd96d8_720w.jpg)



该路径可以理解为一系列**与时间无关** 的点的集合s(x,y)，除非路网信息发生变化或者其他触发条件发生变化，否则相同起始点，规划的轨迹都是相同的，该路径是**道路级别** 的导航，不能直接用于自动驾驶。

## 2.planning简介

planning得到的是一系列**与时间相关** 的点的集合s(x,y,t)，不同的时刻到达同一地方，需要根据周围环境信息（前方是否有障碍物信息，是否需要换道行驶等），规划出**当前时刻** 安全行驶的一条轨迹，如下图所示：

![img](https://pic4.zhimg.com/80/v2-f449a8a4fb8dd4efb06051e1783eb217_720w.jpg)



routing模块规划的路径是planning模块规划轨迹的**输入信息** 来源之一，能够协助planning规划出安全，可靠的行驶轨迹。

## 3.routing与planning的关系

routing模块规划的路径是planning模块规划轨迹的**输入信息** 来源之一，能够协助planning规划出安全，可靠的行驶轨迹。

planning模块通常根据传感器输入（激光雷达、视觉摄像头等）规划出车辆当前时刻需要的一条行驶轨迹，但是一些特殊场景下，如大雾天气、夜晚、匝道进出口等，由于输入信息缺失，不能规划出安全行驶轨迹，此时可以根据routing规划导航路径，结合高精地图信息，能够得到一条满足行驶的轨迹。