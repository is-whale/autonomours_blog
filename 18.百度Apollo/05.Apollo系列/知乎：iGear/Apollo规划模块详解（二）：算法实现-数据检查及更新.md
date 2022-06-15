- [Apollo规划模块详解（二）：算法实现-数据检查及更新 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/430773327)

## 3.3.2 算法实现

Proc主要是检查数据，并且执行注册好的planning，生成路线并且发布。

### **数据检查及更新**

\1) 检查是否需要重新规划路线，Routing模块先规划出一条导航线路，然后Planning模块根据这条线路做局部优化，如果Planning模块发现局部规划的路径行不通（比如前面修路，或者错过了路口），会触发Routing模块重新规划线路。

![img](https://pic4.zhimg.com/80/v2-21ad02257fe49478bf93a3766c68095f_720w.jpg)

发布重规划请求；

\2) 数据放入local_view中，local_view源自于结构体struct LocalView，其中包含上述从别的模块订阅到的所有信息。

![img](https://pic4.zhimg.com/80/v2-ff754eba719e2613ae633e8b17ee795b_720w.jpg)

并对以上输入数据进行检查，因为planning是事件触发，只有收集完所有的TOPIC的信息，才能触发执行。

![img](https://pic3.zhimg.com/80/v2-3cbb8889f1fc8ee0a90b7ac96f8d843a_720w.png)

\3) 数据更新

![img](https://pic4.zhimg.com/80/v2-47e541f4f8b2afa2b6a6757e44ba7dbf_720w.png)

这里的RunOnce是OnLanePlanning::RunOnce，该函数的实现主要分为三个部分

a) 车辆状态更新，保证车辆状态时刻保持最新状态，从chassis中获取车辆的速度等状态信息，从localization中获取车辆的位置信息，如果未获取到相关信息或者车辆状态信息不对，则不再往下执行业务流，输出报错结果然后函数返回（退出并停车）。

![img](https://pic3.zhimg.com/80/v2-34813764bcf0acd5c3955527e2db8d86_720w.jpg)

b) 查看全局路径是否有更新，如果有更新，reference_lineprovider需要更新信息，因为参考线就是reference_lineprovider提供的，因此通过更新reference_lineprovider来更新参考线。如果重规划之后参考线规划失败，生成停止路径，输出报错结果然后函数返回。

![img](https://pic2.zhimg.com/80/v2-b76803b5f60da6c55a8de410f60ae79d_720w.jpg)

更新车辆状态到reference_line_provider。

c) Planning模块运行需要时间，所以我们需要设置规划路径的起点位置，当局部路径需要更新时，其中涉及到轨迹拼接，再规划时起始点的计算。

![img](https://pic4.zhimg.com/80/v2-7944cfe268584df7ae0683990bb46a0b_720w.jpg)

车辆需要重规划的情况：

1.根据配置文件设置，当FLAGS_enable_trajectory_stitcher为false时

2.无原始轨迹时

3.人工驾驶模式时

4.上一周期轨迹点个数为0

5.上一周期轨迹点中的时间头与当前时间不吻合（分为两种情况：1，当前时间小于上一周期的轨迹时间，2，按照时间差得到的轨迹点的索引值大于轨迹点的个数）

6.按照时间差所得到的轨迹索引值对应的轨迹点无轨迹点值，即轨迹点缺失

7.计算车辆当前位置点和根据时间获取的轨迹点的横纵向坐标差，当横纵向差值较大时，需要进行重规划

若需要重规划时，以车辆当前的位置或者根据车辆模型预测的一个规划周期后的车辆位置作为规划的起点

若不需要进行重规划，则根据计算截取上一周期轨迹中对应段的轨迹作为需要拼接的轨迹，（轨迹拼接的目的时截取上一周期的轨迹作为本周期轨迹的前半段）并更新轨迹上的相对时间和path_point中s的信息，后续规划以截取后的轨迹（stitching_trajectory）的最后一个点为起点

d）初始化Frame，初始化Frame比较重要，是在为整个的轨迹规划和速度规划做准备；

![img](https://pic4.zhimg.com/80/v2-dac86458d6513fa526976ef2280e1d97_720w.png)

InitFrame里调用了GetReferenceLines和frame_->Init，其中GetReferenceLines是获取参考线，整个规划都是基于Reference Line开始的，可以说是通过Reference Line来把整个业务流程组织起来，前期输入的很多信息以及各个流程处理后的信息都会存储在Reference Line中，Reference Line上承Rounting模块，又下启决策，轨迹规划和速度规划。

在获取参考线时，如果有单独的线程，则直接赋值，否则通过函数CreateReferenceLine()来创建并通过函数UpdateReferenceLine()来更新，如果未能获取到最新的reference_lines，则采用上一次的参考线；关于参考线的生成会另开章节进行介绍，此处不做详细说明。

frame_->Init里为每个referenceLine扩充为referenceLineInfo类。referenceLine为原始的参考线信息，主要包含的还是偏向于地图层面的信息。referenceLineInfo里面包含了障碍物，车道宽度，路口信息，红绿灯信息，决策信息等和轨迹规划、速度规划各阶段业务相关联的信息，几乎所有的道路信息都会体现再参考线上。

e）根据障碍物计算车前最近障碍物的距离

所有的规划和算法都是基于全局而非车辆坐标系规划的，包括障碍物的信息也都是全局坐标系，另外感知障碍物的坐标分为两部分，一部分是凸包点，一部分是box形状，box形状的参数为长、宽、朝向角，然后障碍物再基于长、宽、朝向角重新构造障碍物的顶点坐标，非静态障碍物预测3s轨迹。

![img](https://pic3.zhimg.com/80/v2-408e2160510980e9b657461a27ae5626_720w.png)

至此，完成了各个数据源的条件检查，获取到的信息都被放在了frame里面，其中包括：

local_view:其中包含交通信息，从预测模块的道德动态障碍物的预测轨迹，相对地图信息，车辆当前状态，车辆当前位置信息

vehicle_state：车辆状态，包括从定位和Canbus模块获取到的一些信息（与local_view重复）

reference_line_info:参考线线信息

路径规划的起点