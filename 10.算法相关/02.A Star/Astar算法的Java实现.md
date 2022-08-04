- [Astar算法的Java实现 (其他很多都是错的，没有计入曼哈顿值的代价)_无数_mirage的博客-CSDN博客_astar java](https://blog.csdn.net/qq_43413788/article/details/108630704)
- [A*算法 JAVA实现_ldstartnow的博客-CSDN博客_a*算法java实现](https://blog.csdn.net/ldstartnow/article/details/51897970)

# 错误描述

项目里的寻路算法是主程(已经走了)从网上直接copy下来用的
简单测试了下，以`80*80`的地图为例，发现从`(0,0)到((30,30)`之间随便找个起点，到终点`(length-1,length-1)`的路径全都寻不到

排查到问题之后**挺无语**，百度搜到的Java实现的Astar算法都一模一样，计算出的曼哈顿值都没乘代价10就直接当做H使用，**不止有很多路寻不到，并且还会产生大量(非常大量)多余的消耗** (断点分析时发现有异常大量的结点被加入到closeList，地图越大越多、消耗越大）。稍后会分析错误是如何产生的

> 2021.07.28 补充
> 通过查询到的错误资料的时间推断，大概率认为是各路"神仙"从这个[github](https://so.csdn.net/so/search?q=github&spm=1001.2101.3001.7020)复制的代码：[仓库链接](https://github.com/ClaymanTwinkle/astar)
> 经过和作者沟通，确定了是有错误的。相关issues：[issue#1](https://github.com/ClaymanTwinkle/astar/issues/3)、[issue#3](https://github.com/ClaymanTwinkle/astar/issues/1)
> 我也创建了一个仓库对该实现进行了bug修复、效率优化并进行以后的维护：[仓库链接](https://github.com/wushu037/java-astar)

> **贴几个无脑复制的错误示例**，目的是让大家找资料的时候不要拿来就用
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/ca3281d829494e0889a478c0639a6742.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNDEzNzg4,size_16,color_FFFFFF,t_70)
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/bbb44421eafe45d89104da7b4b23ef48.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNDEzNzg4,size_16,color_FFFFFF,t_70)
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/8cb27e90ee884806acc6e7cf514d5040.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNDEzNzg4,size_16,color_FFFFFF,t_70)

## 错误分析

简单举个例子，当曼哈顿值不乘代价直接作为H值来用时，造成最直接的问题就是 会取到错误的最优点

```java
假设在12*12的无障碍物地图中，从(4,4)寻路到(11,11)
(4,4)周围的八个格子中，理论上最优点(F值最小的点)应该是(5,5)
而实际在这个错误的算法中最优点会变成(4,5)
因为曼哈顿值不乘10(DIRECT_VALUE横竖移动代价)而直接作为H使用，是这样的：
	(4,5)的G=10,H=13;
	(5,5)的G=14,H=12;
	明显10+13 < 14+12
乘上DIRECT_VALUE再看：
	(4,5)的G=10,H=130;
	(5,5)的G=14,H=120;
	明显10+130 > 14+120
1234567891011
```

在整个算法过程中，这会导致有非常多错误的最优点，产生大量没用的分析计算，资源浪费非常严重，并且经常寻不到路

## 效率

下面对比一下错误代码和进行修复之后的代码(具体实现思路都一样，进行了修复和效率优化)
差距非常悬殊 (再次吐槽无脑复制)

消耗大的是网上错误的代码(曼哈顿值直接作为H使用)，不仅非常慢，还会有很多点寻不到
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200917173034670.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNDEzNzg4,size_16,color_FFFFFF,t_70#pic_center)

消耗小的是正确的代码(曼哈顿值乘代价(10)作为H)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200917173216497.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNDEzNzg4,size_16,color_FFFFFF,t_70#pic_center)

# 找到的正确的代码

百度筛选截止到16年的博文，基本都是正确的实现
如 [A*算法的java实现](https://blog.csdn.net/ldstartnow/article/details/51897970)
![在这里插入图片描述](https://img-blog.csdnimg.cn/236c3dec827f44e592070bbf83e48d2f.png)

# 个人维护的Astar仓库

github：[wushu037/java-astar](https://github.com/wushu037/java-astar)
对原有实现进行了bug修复、效率优化，添加了寻路用例、路径地图打印