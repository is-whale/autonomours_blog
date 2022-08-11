- [路径规划算法学习笔记6-JPS - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/146756164)

Jump Point Search: 又名跳点算法，是在保留A*算法框架的同时，进一步优化了A*算法寻找后继节点的操作。A*是寻找所有当前点的邻居，会有频繁的点集合加入和删除到点集合操作，JPS的优点就是会根据当前点的方向以及当前点周围邻居的特点进行选择某些特殊的点才执行加入和删除到点集合操作。

**一、与A\*的区别：**

![img](https://pic4.zhimg.com/80/v2-0b340f1c38ee2e2289ab32894f1577cf_720w.jpg)

**二、定义：**

**点：**当前点为current, 邻居点为neighbor, 上一点parent.

**点集合：**寻路过程中需要保存有效点的集合，分为可探索点集合openset, 已探索点集合closedset.

**路径权值：**gCost为起点经过其他点到当前点的代价和，hCost为到目标点的代价,

fCost = gCost + hCost.

![img](https://pic2.zhimg.com/80/v2-1a18af78ebad7f74c6ad40ed8fe07cd1_720w.jpg)Fig. 1

**Inferior neighbors(gray nodes):** when going to them, the path without x is cheaper. Discard.

**Natural neighbors(White nodes):** We only need to consider natural neighbors when expand the search.

![img](https://pic3.zhimg.com/80/v2-c84b36a5bb083e00f58aa3b29eaffeaa_720w.jpg)Fig. 2

**Forced neighbors(Red nodes):** There is obstacle adjacent to x . A cheaper path from x′s parent to them is blocked by obstacle.

(如果点neighbor是current的邻居，并且点neighbor的邻居有阻挡，从parent、current、neighbor的路径长度比其他任何从parent到neighbor且不经过current的路径短，则neighbor为current的强迫邻居，current为neighbor的跳点。)

**Jump point**：(1) 如果点y是起点或者目标点，则y是跳点；（2）如果y有强迫邻居则y是跳点，强迫邻居和跳点两者的概念是伴生的；（3）如果parent到y是对角线移动，并且y经过水平或垂直方向移动可以到达跳点，则y是跳点。

**三、规则：**

![img](https://pic4.zhimg.com/80/v2-db8c7b14af6c5928296b6fd37bfead13_720w.jpg)Fig. 3

**规则1：**JPS搜索跳点的过程中，如果直线方向（为了和对角线区分，直线方向代表水平方向与垂直方向）、对角线方向都可以移动，则首先在直线方向搜索跳点，再在对角线方向搜索跳点。

**规则2：**如果从parent到current是直线移动，n是current的邻居，若有从parent到n的路径不经过current且路径长度小于或等于从parent经过x到n的路径，则走到current后下一个点不会走到n(Fig.1左图)

如果从parent到current是对角线移动，n是current的邻居，若有从parent到n的路径不经过current且路径长度小于从parent经过current到n的路径，则走到current后下一个点不会走到n(Fig.2右图)

**规则3：**只有跳点才会加入openset,因为跳点会改变行走方向，而非跳点不会改变行走方向，最后寻找出来的路径也只会是跳点集合的子集。

***简单概括，假如方向是往右上角方向，则判断右方、上方、右上方，寻找跳点，如果邻居中有强迫邻居，当前点是跳点。\***

**四、举例**

**例一**[[1\]](https://zhuanlan.zhihu.com/p/146756164#ref_1)

![img](https://pic3.zhimg.com/80/v2-c6804701bde2334dd36d20d81919298e_720w.jpg)



5*5的网格，黑色代表阻挡区，S为起点，E为终点。JPS要寻找从S到E的最短路径。

（1）首先初始化将S加入openset。从openset取出F值最小的点S，并从openset删除，加入closedset,S的当前方向为空，则沿八个方向寻找跳点，只有下、右、右下三个方向可以走，但是向下（一直走）会遇到边界，向右会遇到阻挡，因此都没找到跳点。沿右下方向寻找跳点，在G点，根据**Jump point的定义3，**parent(G)为S, S到G为对角线移动，并且G经过垂直方向移动（向下）可以到达跳点I，因此G为跳点，将G加入openset。

（2）从openset取出F值最小的点G，并从openset删除，加入closedset,因为G当前方向为对角线方向（从S到G的方向），因此在右、下、右下三个方向寻找跳点，在该图中只有向下可走，因此向下寻找跳点，根据**Jump point的定义2,**找到跳点I，将I加入openset.

（3）从openset取出F值最小的点I，并从openset删除，加入closedset,因为I当前方向为直线方向（从G到I的方向），在I点时I的右上方不可走且右方可走，因此沿下、右、右下寻找跳点，但向下、右下都遇到边界，只有向左寻找到跳点Q（根据**Jump point的定义2**）因此将Q加入openset.

（4）从openset取出F值最小的点Q，并从openset删除，加入closedset,因为Q的当前方向为直线方向，Q的右上方不可走，因此沿上、下、左下寻找跳点，向下、左下都遇到了边界，只有向上寻找到了跳点E（根据**Jump point的定义1**），因此将E加入openset.

（5）从openset取出F值最小的点E，因为E是目标点，因此寻路结束，路径是S->G->I->Q->E.

**例二**[[2\]](https://zhuanlan.zhihu.com/p/146756164#ref_2)

![img](https://pic3.zhimg.com/80/v2-9da9fd33f14819138af20844073e830e_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-bd8e28749a63d2f3963c154fdb9b0b02_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-9bd7d8dfe05367fab27cec2f6d7e3439_720w.jpg)

## 参考

1. [^](https://zhuanlan.zhihu.com/p/146756164#ref_1_0)https://blog.csdn.net/u011265162/article/details/91048927
2. [^](https://zhuanlan.zhihu.com/p/146756164#ref_2_0)https://blog.csdn.net/yuxikuo_1/article/details/50406651