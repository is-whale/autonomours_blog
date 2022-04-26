- [UWB的定位算法（简单详细易懂）_小阳先生的宝库的博客-CSDN博客_uwb定位](https://blog.csdn.net/qq_49864684/article/details/115870377)

## 一、控制部分

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210419232040240.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ5ODY0Njg0,size_16,color_FFFFFF,t_70)

## 二、UWB 的测距原理是什么？

> 双向飞行时间法（ TW-TOF, two way-time of flight）每个模块从启动开始即会生成一条独立的时间戳。
>
> 模块 A 的发射机在其时间戳上的 Ta1发射请求性质的脉冲信号，模块 B 在 Tb2时刻发射一个响应性质的信号，被模块 A 在自己的时间戳Ta2时刻接收。 由此可以计算出脉冲信号在两个模块之间的飞行时间，从而确定飞行距离 S。
>
> S=Cx[(Ta2-Ta1)-(Tb2-Tb1)]/2 (C 为光速)

图示：![在这里插入图片描述](https://img-blog.csdnimg.cn/20210419231428858.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ5ODY0Njg0,size_16,color_FFFFFF,t_70)

> UWB 定位的原理是什么？
>
> 1) 距离 = 光速 * 时间差 / 2； XY 平面， 3 个圆，能够确定一个点； 
> 2) 2) XYZ 空间， 4个圆，能够确定一个空间点；

## 三、TOF 数学计算

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210419230802189.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ5ODY0Njg0,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210423173641863.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ5ODY0Njg0,size_16,color_FFFFFF,t_70#pic_center)

**T1 -T6 会在下一节代码里标注出来，官方提供的代码主要用的就是此类算法。**

## 四、Trilateration 三边测量法的原理与计算方法(TDOA平面)

> 三边测量法的原理如右图所示，以三个节点 A、 B、 C 为圆心作圆，坐标分别为(Xa， Ya)， (Xb， Yb)， (Xc， Yc)，这三个圆周相交于一点 D，交点 D 即为移动节点， A、 B、 C 即为参考节点， A、 B、 C 与交点 D 的距离分别为da，db， dc。假设交点 D 的坐标为(X , Y)

计算公式：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210419232946958.png)

如图所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210419232834549.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ5ODY0Njg0,size_16,color_FFFFFF,t_70)
可以得到交点 D 的坐标为：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210419233055573.png)

### 1.三边测量法的缺陷是：

> 由于各个节点的硬件和功耗不尽相同，所测出的距离不可能是理想值，从而导致上面的三个圆未必刚好交于一点，在实际中，肯定是相交于一个小区域，因此利用此方法计算出来的(X, Y)坐标值存在一定的误差。这样就需要通过一定的算法来估计一个相对理想的位置，作为当前移动节点坐标的最优解。

### 2.Z 轴准确度比 X 轴 Y 轴要差一些？

> 如图所示， A0/A1/A2 为 3 个基站， T0 为标签， LA0T0 LA1T0 LA2T0
> 表示为每个基站到标签的距离。在测距完全准确的情况下，解算的 Tag 坐标应该在 T0， 但是， 由于实际测量值 LA0T0 LA1T0 LA2T0 可能偏大， 解算的位置在 T0’。 因为 A0/A1/A2 都在 xoy 平面，所以，测距的误差绝大多数会累加到 z 轴上，造成 z 轴数据的抖动。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210419233626664.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ5ODY0Njg0,size_16,color_FFFFFF,t_70)

## 五、TDOA(3D空间)。

1. 概念

> 到达时间差（Time Difference of Arrival，TDOA）是一种利用到达时间差进行定位的方法又称为双曲线定位。标签卡对外发送一次UWB信号，在标签无线覆盖范围内的所有基站都会收到无线信号，如果有两个已知坐标点的基站收到信号，标签距离两个基站的间隔不同，那么这两个基站收到信号的时间点是不一样的。

2.举例

> 例如，小明的妈妈在村口喊“小明，回家吃饭啦！”，根据距离=时间*速度，其中速度不变（声音在空气中的传播速度是340m/s），那么声音传播的时间是由距离决定的，因此村里的人听到小明妈妈声音的时间点是不一样的。

> 同理，标签与不同基站的距离不同，不同基站收到同一标签信号的时间节点不同，因此得出一个“到达时间差”的概念。

> TDOA定位的原理正是利用多个基站接收到信号的时间差来确定标签的位置

3.图解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210423181020913.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ5ODY0Njg0,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210423180936146.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ5ODY0Njg0,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210423181045123.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ5ODY0Njg0,size_16,color_FFFFFF,t_70)

> TDOA技术不需要定位标签与定位基站之间进行往复通信，只需要定位标签发射一次UWB信号，工作时长缩短了，功耗也就大大降低了，故能做到更高的定位动态和定位容量。

## 六、优化定位，更加准确。

> UWB 模块测量值，总是比实际距离要大一些；部分用户反应， UWB模块测量值比实际距离要小，这是怎么一回事呢？这是由于，我们使用的现场，环境都是不同的，受经纬度、空气质量、环境障碍物、海拔等等因素干扰，所以在产品化的进程中，必须要对模块进行校准。
>
> 一般情况下，校准只需要在现场进行一次，通过 1 个 Anchor 和 1 个 Tag 的测距，得到修正系数，并不需要每个 Anchor和 Tag 都进行标定。
>
> 利用 Microsoft 2016 Excel 软件，进行数据拟合，并生成拟合公式。拟合公式有很多，最简单的是线性方程。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210419233936876.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ5ODY0Njg0,size_16,color_FFFFFF,t_70)

> 测距值存在 instancegetidist_mm(0), instancegetidist_mm(1), instancegetidist_mm(2), instancegetidist_mm(3)，这四个变量里，
> 每个距离，都需要代入刚才计算出来的校准公式内。在 main.c 函数中， 对于 mc 帧的程序：
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210419234057119.png)
> 修正后：
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210419234126527.png)

解释下：
消息 ID, 一共有三类，分别为 mr, mc, ma
**mr 代表标签-基站距离（原生数据）
mc 代表标签-基站距离（优化修正过的数据，用于定位标签）
ma 代表基站-基站距离（修正优化过，用于基站自动定位）**

## 七、图示测试。

**注意：其中一个基站必须跟电脑的USB口相连。**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210419234427628.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ5ODY0Njg0,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210419234452414.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ5ODY0Njg0,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210419234713565.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ5ODY0Njg0,size_16,color_FFFFFF,t_70)

四个基站，两个标签运动轨迹。