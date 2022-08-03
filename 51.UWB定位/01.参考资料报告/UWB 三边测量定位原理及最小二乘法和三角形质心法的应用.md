- [UWB 三边测量定位原理及最小二乘法和三角形质心法的应用—通俗解析_M Choppe的博客-CSDN博客_三边定位最小二乘法](https://blog.csdn.net/Luuunatic/article/details/107784856)

# UWB 三边测量定位原理及[最小二乘法](https://so.csdn.net/so/search?q=最小二乘法&spm=1001.2101.3001.7020)和三角形质心法的应用—通俗解析

本人二线城市小程序员一枚，这段时间因为公司的原因，开始研究UWB，定位原理部分花了三整天看CSDN上的各种文章，零零散散，大多是讲的某一部分的原理，没有能给串联起来的。

把学习过程中一些心得分享给大家。

## 关系架构

把我自己整理的关系架构贴上

![TWR定位求解流程](https://img-blog.csdnimg.cn/2020080509365493.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0x1dXVuYXRpYw==,size_16,color_FFFFFF,t_70)

## 三边测量定位-理想模型

对于理想模型（三个圆交于一点）的求定位点坐标就不必赘述了，好多讲解的文章里已经说得很明白了。

喂，等等，不会有人不知道啥是三个圆吧？？？？
就是下图说的以基站为圆心，以测距结果为半径画圆，三个基站可以画出三个圆，理论上三个圆的交点就应该是所求的定位点。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200805095027236.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0x1dXVuYXRpYw==,size_16,color_FFFFFF,t_70)

## 三边测量定位-现实模型

在现实模型里面，三个圆基本不可能交于一点，可能相交于一片区域，或者根本不相交，方程组无法直接求解。

这咋办呢？

采取方法来找一个解！

用啥方法呢？

** 最小二乘法、三角形质心法等！**

## 最小二乘法

最小二乘法的原理我看了很多篇文章，这篇讲的不错https://www.cnblogs.com/wangkundentisy/p/7505487.html

推导过程很复杂，但好在我们只需要应用就行。

你可以看不懂最小二乘法如何推导的

但你需要记住这句话：

**应用最小二乘法就是要先找到最小二乘法的矩阵形式！**

![在这里插入图片描述](https://img-blog.csdnimg.cn/202008051011274.png)

只要找到这个，就能直接带入最小二乘法的计算公式

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200805101452169.png)

**直接求出最优解**，参数的定义看我上面贴的那篇文章。

## 三角形质心法

通用的三角形质心法只适用于三个圆交于一片区域的情况。

需要用三个圆两两相交的交点构建一个三角形，通过简单的求三角形质心来得到估计的定位坐标。

大家理解了这个道理之后，过程就很好理解了。

同样，不做赘述https://blog.csdn.net/qq_37967635/article/details/81334988

参见这篇文章最后一部分