- [【Apollo 6.0算法解析】Apollo EM Planner_Travis.X的博客-CSDN博客_apollo em planner](https://blog.csdn.net/Travis_X/article/details/122337721)

# 前言

EM Planner是基于轻决策的规划算法。与其他基于重决策的算法相比，EM Planner的优势在于能够处理许多复杂的场景（例如多障碍物的场景）。当基于重决策的方法试图预先确定如何处理每个障碍物时，困难是显而易见的：（1）很难理解和预测障碍物如何与主车相互作用，因此它们的跟随运动难以描述，因此很难被任何规则考虑；（2）当多个障碍物阻塞道路时，无法找到满足所有预定决策的轨迹概率大大降低，从而导致规划的失败。

Planning的大概流程是这样：先由 Routing模块通过全局规划得到参考线 Reference Line，然后 Planning模块在此基础上进行局部轨迹规划，EM Planner将路径和速度进行分层规划，并在 SL 和 ST坐标系下，使用动态规划（DP）进行路径和速度的决策和粗规划，然后再使用二次规划（QP）进行平滑处理。
![在这里插入图片描述](https://img-blog.csdnimg.cn/505cf4ccd0274060925681796bd03104.png)
在第一个E-steps中，动态和静态障碍物都被投影到Frenet框架。静态障碍物会直接从笛卡尔坐标系转换到Frenet坐标系，而动态障碍物的信息则以其运动轨迹来描述。在[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020)框架中，可以预测动态障碍物的移动轨迹。考虑到之前的周期规划轨迹，我们可以在每个时间点估计出动态障碍物和汽车的位置。然后动态障碍物和汽车在某个时间点的重叠可以被映射到Frenet框架中。另外，动态障碍物的出现会最终导致自车做出避让的决策。因此，**出于安全考虑，SL投影只考虑低速和来向障碍物，而对于高速的动态障碍物，EM Planner的平行变道策略会考虑这种情景。**

在第二个E-steps中，基于生成的路径在s-t坐标系下，所有障碍物包括高速、低速和即将到来的障碍物都将被估计,生成速度曲线。如果障碍物轨迹和已经规划的路径重叠，将在s-t坐标系下对应区域将会在进行重新生成。

在两个M-steps中，通过动态规划和二次规划生成路径和速度曲线。尽管我们将障碍物投影到SL和ST坐标系下，但是最优的路径和速度解仍在非凸空间中。因此，我们使用动态规划先获得一个粗略解，同时，这个解可以提供避让、减速、超车等障碍决策。通过这个粗略的解，可以构建一个凸包，然后使用基于二次规划的样条优化器来求解。

------

# 一、SL 和 ST 坐标系

## 1.1 SL坐标系

SL坐标系又称为Frenet坐标系，S代表中心线方向，L代表与中心线正交的方向，在SL坐标系中，我们用S、L、侧向速度dL、侧向加速度ddL、侧向加加速度dddL，这5个量描述车辆的状态。

在Frenet坐标系下做运动规划的好处在于借助于指引线做运动分解，将高维度的规划问题，转换成了多个低维度的规划问题，极大的降低了规划的难度。另外一个好处在于方便理解场景，无论道路在地图坐标系下的形状与否，我们都能够很好的理解道路上物体的运动关系。相较直接使用地图坐标系下的坐标做分析，使用Frenet坐标，能够直观的体现道路上不同物体的运动关系。
![1](https://img-blog.csdnimg.cn/b3c6aa69195640999372c5c8d9519732.png)

## 1.2 ST坐标系

ST坐标系可以帮助我们设计和选择速度曲线，“S”表示车辆的纵向位移，“T”表示时间，ST图上的曲线是对车辆运动的描述，因为它说明了车辆在不同时间的位置，由于速度是位置变化的速率，因此可以通过曲线的斜率从ST图上推断速度，斜坡越陡说明在短时间内有更大的移动对应更快的速度。
![在这里插入图片描述](https://img-blog.csdnimg.cn/0cc36cd64bcb4c09a77b9aff08484055.png)

------

# 二、DP Path（E-Step）

基于动态规划的路径步骤提供一条粗略的路径信息，其可以带来可行通道和避障决策。这一步包括**Lattice采样**、**目标代价函数**、**动态规划搜索**。过程如下所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/0beaa697839a49a29e07e8420ae6ced1.png)

## 2.1 Lattice采样

Lattice采样是基于Frenet坐标的。如7图所示，首先在车辆之前对多行lattice进行采样。不同行之间的点通过五次多项式来平滑连接。行之间的间隔距离取决于速度，道路结构，变换车道等。出于安全考虑，路径总长可以达到200m或者覆盖8s的行驶长度。
﻿﻿﻿﻿![在这里插入图片描述](https://img-blog.csdnimg.cn/9af48eb03b1d43ada49c0f2576b84229.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/37a29378676c4fa89ef3327400a6f045.png)

## 2.2 代价函数

每段Lattice路径的代价通过光滑程度、障碍物代价、车道代价来评价：
![在这里插入图片描述](https://img-blog.csdnimg.cn/a56daa30bbde4d3aa9f35502dafca80b.png)

### 2.2.1 光滑程度

光滑程度通过以下方程来衡量，一阶导表示朝向偏差，二阶导表示曲率，三阶导表示曲率导数：
![在这里插入图片描述](https://img-blog.csdnimg.cn/9d70d0506a904fd2b6b0ed5535a87ed8.png)

### 2.2.2 障碍物的代价

障碍物的代价通过以下方程来衡量，“d”代表的是车辆bounding box到障碍物bounding box的距离。
C n u d g e C_{nudge}*C**n**u**d**g**e*​定义为单调递减函数，出于安全方面的考虑，d c d_c*d**c*​设置保留缓冲区，可根据情况微调d n d_n*d**n*​范围。C c o l l i s i o n C_{collision}*C**c**o**l**l**i**s**i**o**n*​是碰撞代价函数，可帮助检测不可行的路径。
![在这里插入图片描述](https://img-blog.csdnimg.cn/6d4d482a2cf24b438de71295daafed61.png)

### 2.2.3 车道代价

车道代价通过以下方程来衡量，主要是考虑在车辆行驶路线与车道中心线的差异。车道代价函数包括两个部分：引导线代价和车道代价。引导线定义为周围没有障碍物时的理想路径，通常这条线提取的是道路中心线。车道代价函数通常被道路边界决定，在道路外的路径点会受到很高代价的惩罚：
![在这里插入图片描述](https://img-blog.csdnimg.cn/81925e81aa564c2ba0451150059dc6d6.png)

## 3.1 动态规划

DP路径算法的基本思路是，基于给定的一条中心路径（称为参考线，Reference Line）和车道边界条件，每隔一定间隔的纵向距离（称为不同级（level）上的S值）对道路截面进行均匀采样（与中心点的横向偏移值称为为L值），这样会得到图中黑点所示的采样点（这些采样点称为航点，waypoint）数组。基于一定的规则，可以给出各航点迁移的代价值（cost）。航点迁移不一定需要逐级进行，可以从第一级跳到第三级甚至终点，只要迁移代价值最小化即可，这显然满足动态规划的求解思路。
![在这里插入图片描述](https://img-blog.csdnimg.cn/907b8d7c26c74cd6ae45dea8238727ab.png)

------

# 三、Spline Path（M-Step）

DP path（E-Step）这一步已经可以求解出一条粗略的可行路径，而样条QP要做的就是平滑这个DP输出来的路径。
![在这里插入图片描述](https://img-blog.csdnimg.cn/ff8cd02bd61c482b893b7015902d4bba.png)
QP求解器求解具有线性约束的目标函数生成最优的QP路径，过程如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/965b39048a0e448f93c8f1154a6fbf83.png)

## 3.1 代价函数

QP路径的代价函数是平滑度代价和引导线代价的线性组合。该步骤中的引导线是动态规划挑选出来的路径。引导线提供了避开障碍物的估计值。在数学上QP路径步骤优化以下函数：
![在这里插入图片描述](https://img-blog.csdnimg.cn/dea189564ec44015bb578445631b8e3e.png)
g ( s ) g(s)*g*(*s*)是动态规划的结果。f ′ ( s ) f'(s)*f*′(*s*)、′ ′ ( s ) ''(s)′′(*s*),、f ′ ′ ′ ( s ) f'''(s)*f*′′′(*s*)分别为航向角、曲率和曲率的变化率。该目标函数描述了避开障碍物和平滑度之间的权衡。

## 3.2 约束条件

DP的约束有两个：**boundary constraints（边界约束）** 和 **dynamic feasibility（动力学约束）**。

### 3.2.1 boundary constraints

边界约束也可以理解为碰撞约束，可以在车头车尾加两个半圆作为车辆边界以保证约束的线性和凸性。
![在这里插入图片描述](https://img-blog.csdnimg.cn/a5b3c5c562f34c81865fd9b0ac3ad91f.png)
然后左前角的横向位置由以下表示，其中θ是汽车和道路状态方向之间的航向角。
![在这里插入图片描述](https://img-blog.csdnimg.cn/c8b18455e8e04fad8597817aa55e2f4d.png)
可用下面的不等式进一步近似地线性约束：
![在这里插入图片描述](https://img-blog.csdnimg.cn/e073b862ae8b4a9b8af28e3845c8e4a5.png)

### 3.2.2 dynamic feasibility

DP给出了由很多points（每个row上一个point）组成guidance line，QP可以此为基础调整每个point在L方向的位置以实现平滑path。但调整是有可行范围的，该范围可量化为QP的约束（lmin和lmax）。另外曲率f ′ ′ ( s ) f''(s)*f*′′(*s*)和曲率变化率f ′ ′ ′ ( s ) f'''(s)*f*′′′(*s*)也可以用dynamic feasibility去约束衡量（如下图）。
![在这里插入图片描述](https://img-blog.csdnimg.cn/7202c916c2f04e73a51121d7af9940de.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/3071e950de0a4aa7a8fb7ccb0e431a68.png)

------

# 四、DP Speed Optimizer（E-Step）

DP速度算法的基本思路是，在DP路径算法生成一条可行驶的路径后，从起点开始，考虑避开路径中的所有障碍物，并且让加减速最为平顺，以最优的速度曲线（即t-s平面中的绿色曲线）安全抵达终点，这也可以使用动态规划的思路求解。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2358d8d9e5724c76b4a6802cc622f39c.png)
DP速度规划步骤包括**ST图网格**、**代价函数**和**动态规划搜索**，分别生成分段性速度曲线，可行的走廊和障碍物速度决策。
![在这里插入图片描述](https://img-blog.csdnimg.cn/6a475cde5f774160982a1236b66b3278.png)

## 4.1 ST图

障碍物信息首先在ST图上离散成网格。将（ t 0 , t 1 … t n ) （t_0,t_1…t_n)（*t*0,*t*1…*t**n*)表示为在时间轴上具有间隔d t d_t*d**t*的等距评估点。分段性速度曲线函数在网格上表示为S = （ s 0 , s 1 , … s n ） S=（s_0,s_1,…s_n）*S*=（*s*0,*s*1,…*s**n*）。此外，利用有限差分方法对导数进行了逼近。
![在这里插入图片描述](https://img-blog.csdnimg.cn/096a123e86f845b4bd2b62b868958476.png)

## 4.2 代价函数

目标是在ST图中优化一个带有约束的代价函数。准确地说，DP速度优化器的代价表示为：
![在这里插入图片描述](https://img-blog.csdnimg.cn/40be2f7cc8dd4cdeb0ee047e05811f9e.png)

- 速度保持代价：表示当没有障碍物或者没有红绿灯时，车辆应按照指定的速度行驶。r e f _{ref}*r**e**f* 描述的是参考速度，参考速度由道路速度限制、曲率和其他交通规则来确定，g函数被用来对小于或者大于V r e f V_{ref}*V**r**e**f*值施加的不同惩罚。
- 加速度a和加加速度jerk描述了速度曲线的平滑代价；
- C o b s C_{obs}*C**o**b**s*值描述了总障碍代价，对车辆到所有的障碍物的距离进行评估，确定总障碍物代价。

## 4.3 动态规划搜索

动态规划搜索空间必须在动力学约束范围内。动态约束包括加速度、加加速度jerk和单调性约束。因为要求生成的轨迹在道路上不执行倒车的操作，只能在泊车或者其他指定情况下直行倒车。搜索算法是向前的，基于车辆动态约束的一些必要修剪也被应用以加速该过程。

------

# 五、Spline QP Speed Optimizer（M-Step）

由于分段线性速度曲线不能满足动力学要求，需要用QP来解决这个问题。在图13中，样条速度包括三个部分：代价函数、线性约束条件。﻿
![在这里插入图片描述](https://img-blog.csdnimg.cn/c67efdcc632a41fa98a752fe77f9ce46.png)

## 5.1 代价函数

代价函数可以这样表示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/56a0cb36a56a4d339aaa9b616751a441.png)
第一项是DP得出的速度曲线和最后QP生成的速度曲线之间的距离。第二、三项是平滑衡量，加速度和jerk。因此，目标函数是为了平衡引导线和平滑度。

## 5.2 约束

QP优化器是带有线性约束的。QP速度优化器的线性约束如下，其中：
第1项是单调性：保证车辆只能前行不能倒车；
第2项是ST曲线在某一时刻的上下界，算是表征碰撞的约束；
第3项是车速上限，一般从交通规则或车辆动力学得出；
第4项是加速度限制，一般由车辆动力学性能、舒适性决定；
第5项是加加速度限制，一般也由车辆动力学性能、舒适性决定；![在这里插入图片描述](https://img-blog.csdnimg.cn/6c25f526c4d943358e6d9e57a49932d0.png)

------

# 参考

[Apollo Lattice Planner从学习到放弃.额不…到实践](https://zhuanlan.zhihu.com/p/164635074)
[[论文解读\]Baidu Apollo EM Motion Planner](https://blog.csdn.net/Travis_X/article/details/109174898)
[动态规划及其在Apollo项目Planning模块的应用](https://blog.csdn.net/davidhopper/article/details/79399640)
[自动驾驶之轨迹规划6——Apollo EM Motion Planner](https://www.cxybb.com/article/IHTY_NUI/115977861)