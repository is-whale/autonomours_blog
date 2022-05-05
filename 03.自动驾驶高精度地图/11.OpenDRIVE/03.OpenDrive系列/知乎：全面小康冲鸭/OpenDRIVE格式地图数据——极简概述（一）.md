- [OpenDRIVE格式地图数据——极简概述（一） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/360377363)

OpenDRIVE格式的HD Map就是一份XML文件，我们只需要理解这份XML文件的内容就好了（我们先假设不自己造高精地图，而是会用像C++里的tinyXML2等包来解析它）。

OpenDRIVE的文档地址，中英文版，不得不说很良心了，难得有官方文档会有中文版的：

[https://www.asam.net/index.php?eID=dumpFile&t=f&f=3768&token=66f6524fbfcdb16cfb89aae7b6ad6c82cfc2c7f2#_foreword_%E5%89%8D%E8%A8%80www.asam.net/index.php?eID=dumpFile&t=f&f=3768&token=66f6524fbfcdb16cfb89aae7b6ad6c82cfc2c7f2#_foreword_%E5%89%8D%E8%A8%80](https://link.zhihu.com/?target=https%3A//www.asam.net/index.php%3FeID%3DdumpFile%26t%3Df%26f%3D3768%26token%3D66f6524fbfcdb16cfb89aae7b6ad6c82cfc2c7f2%23_foreword_%E5%89%8D%E8%A8%80)

文章中的例子大部分来自于

- 说明文档
- [LGSVL simulator](https://link.zhihu.com/?target=https%3A//content.lgsvlsimulator.com/)中的Borregas Avenue地图对应的XML文件的高精地图

## 一、写在前面

OpenDRIVE说明文档前面几章（前言、介绍、与其他标准的关联、通用架构、附加数据）在我看来没太多要关注的地方，也和理解XML文件的关系不大，吧啦吧啦讲了一堆OpenDRIVE交付的内容、规范惯例、其他ASAM仿真标准、版本兼容、在OpenDRIVE中以URL的方式附加其他/用户数据等等。所以，我大概摘录、总结了以下几点。是比较重要的，也是一些语言性的内容，看看了解即可。

- ASAM OpenDRIVE描述了驾驶仿真应用所需的静态道路交通网络（以下简称路网），如道路、车道、交叉路口等内容，但OpenDRIVE中并不包含动态内容。OpenDRIVE中描述的路网可以是人工生成或来自于真实世界的。
- 存储在OpenDRIVE文件中的数据描述了道路的几何形状以及可影响路网逻辑的相关特征(features)，例如车道和标志。
- OpenDRIVE数据存储于XML文件中，文件拓展名为.xodr。OpenDRIVE压缩文件的拓展名为".xodrz"（压缩格式gzip）。
- OpenDRIVE允许将外部文件包含在OpenDRIVE文件中，而如何处理该类文件则视应用而定。包含数据用<include>元素来表示，可被存储在OpenDRIVE里任意位置。

![img](https://pic2.zhimg.com/80/v2-13a7a9b887f1267a3742ad06af8bc661_720w.jpg)

- **ASAM OpenDRIVE** 静态道路网络描述；**ASAM OpenCRG** 静态道路表面描述；**ASAM OpenSCENARIO** 动态道路网络描述。三个标准的结合则提供包含静态和动态内容、由场景驱动的对交通模拟的描述。

![img](https://pic1.zhimg.com/80/v2-0f1a7b800a6e46c1d253b6701746aa2c_720w.jpg)

- 你应当熟悉或基本了解XML文件的格式，在说明文档和本文章下面的内容中，注意区分“元素 Element ”和“属性 Attribute”的概念，蓝色框为元素，红色框为属性。除此之外，注意区分“道路 Road”和“车道 Lane”的概念，道路包含车道，车道是依附在道路上的，道路的概念远远大于车道。从英文上理解应该更为容易。

![img](https://pic1.zhimg.com/80/v2-9c6ca1ca9c561b2b8fd6f73a0042fa1c_720w.jpg)

## 二、OpenDRIVE中用到的三种坐标系

OpenDRIVE使用三种类型的坐标系，如下图所示：

- 惯性x（东）/y（北）/z（上）轴坐标系
- 参考线s/t/h轴坐标系
- 局部u/v/z轴坐标系

![img](https://pic2.zhimg.com/80/v2-e4babea88322eeac12cff8706e0e6939_720w.jpg)

当然，与参考系相关联的还有航向角/偏航角（heading）、俯仰角（pitch）和横摆角/翻滚角（roll），下图以惯性参考系为例。

![img](https://pic2.zhimg.com/80/v2-1bf0a8b00d44ac96861cf14269124a45_720w.jpg)

对于惯性坐标系和局部坐标系我们都非常熟悉，在这里只简单说一下参考线坐标系。参考线坐标系也是右手坐标系，参考线一般被定义为车道中心线，s的方向跟随参考线的切线方向。

![img](https://pic4.zhimg.com/80/v2-64b6984f5a9978f425fa387bb988ee03_720w.jpg)

下面是官方对s/t/h给出的解释：

- s：坐标沿参考线，以[m]为单位，由道路参考线的起点开始测量，在xy平面中计算（也就是说，这里不考虑道路的高程剖面，简单理解，从道路的横截面看过去，道路不是水平的，而是有高度差，这就叫高程，高程分为道路高程和超高程，后面会讲）
- t：侧面，在惯性x/y平面里正向向左
- h：在右手坐标系中垂直于st平面

一般来讲，定义参考线坐标系的作用是为了简化计算，想象一下，s方向的值即是我们沿着车道中心线走了多远，这个被称为纵向距离。t方向的值即是我们的车辆偏移车道中心线的距离，也就是所谓的走歪了，这个被称为横向偏移。h方向的值就比较简单，就是高度值。此外，在其他地方也会把t方向叫做d方向。

最后给出三大坐标系的全家福：

![img](https://pic4.zhimg.com/80/v2-f274704f2def53e5092b6ee83d048fcb_720w.jpg)

## 三、OpenDRIVE中使用的几何形状

上面讲了OpenDRIVE中使用的坐标系，这里说一下几何形状。一般，几何形状是用来描述道路的走向，对道路线进行建模，像空旷地面上的直线、高速公路上细长的弯道、亦或山区狭窄的转弯。在OpenDRIVE中有以下几种，根据曲率以及曲率的变化方式来分类：

- 直线
- 螺旋线或回旋曲线
- 有恒定曲率的弧线
- 三次多项式曲线（弃用）
- 参数三次多项式曲线

![img](https://pic4.zhimg.com/80/v2-bb0d94d9876238de6fd09d258250322f_720w.jpg)

**注意：这些几何形状是用来描述道路参考线的。**

**直线 Straight Line**

大部分的车道很简单，直线没有曲率，在OpenDRIVE中，直线用<geometry>元素里的<line>元素来表示，如下图所示，此时你应当暂时不需要理解其他元素和属性是什么意思。

![img](https://pic4.zhimg.com/80/v2-8115d6499c28c18f28d430d3fc4b83fb_720w.jpg)

**螺旋线 Spiral**

螺旋线是以起始位置的曲率(curvStart)和结束位置的曲率(curvEnd)为特征。沿着螺旋线的弧形长度，曲率从头至尾呈线性。

- curvStart 螺旋线开始点的曲率
- curvEnd 螺旋线结束点的曲率

使用方法如下图所示：

![img](https://pic4.zhimg.com/80/v2-425a3f5926be01bbd03d6dacc998a7e7_720w.png)

**弧线 Arc**

描述有恒定曲率（不等于0）的道路参考线。

- curvature 弧线的恒定曲率

也很简单，如下图所示：

![img](https://pic1.zhimg.com/80/v2-c576a759cb0cb156abcaa4582ebd76b4_720w.jpg)

**三次多项式 Cubic Polynom**

这个已经被弃用了，被下面的参数三次多项式，也即是参数方程形式的三次多项式代替了，被代替的原因应该是要计算离散的点，那么直接用参考线的s值或归一化后的值当作参数，计算起来更加方便。

**注意**：这里不再是用曲率表示了，而是改用三次多项式的参数来代表整个曲线。

v(u) = a + b*u + c*u2 + d*u³

- a 多项式参数a
- b 多项式参数b
- c 多项式参数c
- d 多项式参数d

如下图所示：

![img](https://pic2.zhimg.com/80/v2-0a472cdde3c2c2cfc9301602f86d93ad_720w.jpg)

**参数三次多项式 Parametric Cubic Curve**

参数形式的三次多项式，使用插值参数p来分别计算u/v轴向的值。若无另外说明，插值参数p则在[0;1]范围内，另外，也可在 [0，参考线长度]的范围内对其赋值。这样是不是插值计算起来要简单很多。

```
u(p) = aU + bU*p + cU*p2 + dU*p³
v(p) = aV + bV*p + cV*p2 + dV*p³
```

- aU 多项式u(p)参数a
- bU 多项式u(p)参数b
- cU 多项式u(p)参数c
- dU 多项式u(p)参数d
- aV 多项式v(p)参数a
- bV 多项式v(p)参数b
- cV 多项式v(p)参数c
- dV 多项式v(p)参数d
- pRange 参数p的范围，无该属性的话p是默认范围

如下图所示：

![img](https://pic3.zhimg.com/80/v2-bbcd52cd2a2fdec38334c5a43d546fde_720w.jpg)

**生成任意车道线 Generating Arbitrary Road Course**

这个就是把不同的几何形状拼接起来，对应着不同的曲率变换方式，以便创建诸多种类的道路线，很简单，看下面的图就能看明白。

![img](https://pic3.zhimg.com/80/v2-500fc7b16de736986cb46ed3e564ec5e_720w.jpg)

## 四、XML文件的开始部分

从这一节开始，我们将开始分析XML文件，主要关注文件中不同元素和属性的定义和描述，当你在阅读的时候，请思考现实生活中的路网，会更加容易理解。

我姑且称以下部分为XML的开始部分，它也确实是XML的开始部分。

![img](https://pic1.zhimg.com/80/v2-9d2a4ee66ad4c6d7d34e626820c9cfb8_720w.png)

这一部分我们主要关注<header>元素中的内容，很好理解。<header>元素是<OpenDRIVE>中的第一个元素。

- revMajor OpenDRIVE格式主版本号
- revMinor OpenDRIVE格式次版本号
- name 数据库的名称
- version 本路网的版本号
- date 根据ISO 8601采用的数据库创建时间/日期
- north 惯性坐标系中y轴最大值
- south 惯性坐标系中y轴最小值
- east 惯性坐标系中x轴最大值
- west 惯性坐标系中x轴最小值
- vendor 开发商的名称

简单说一下<geoReference>元素，该元素代表地理坐标系，我们前面说的惯性坐标系就是地理坐标系的投影，那么描述大地基准的参数等就在<geoReference>元素内定义。

- Proj字符串 包含了所有定义已使用的空间参考系的参数

此外，该元素中还定义了地理坐标参考偏移，也即是尽可能的让路网偏移、聚集在原点附近，如图所示：

![img](https://pic4.zhimg.com/80/v2-ede315f219ab9fdc5e62f8a6c5cd1fbb_720w.jpg)

------

**最后先简单说一下“高程”的概念吧，毕竟前面提到了。**

高程 Elevation，从参考线坐标系来看，是指h方向的高度不是固定不变的。根据h方向高度的变化方式分为下面三种：

**道路高程 Road Elevation**

道路高程详细说明了沿道路参考线（s方向）的高程。那么在s/t平面内，道路应该被投影为一条曲线，比较类似于上下坡。

![img](https://pic2.zhimg.com/80/v2-b3c9893482a4fc827580056c237673ed_720w.jpg)

**超高程 Super Elevation**

超高程是横断面图的一部分，它描述了道路的横坡。它可用于将道路往内侧倾斜，从而使车辆更容易驶过，尤其是在转弯的时候，利用超高程的向心力来抵消车速转弯时的离心力。从横断面来看，如下图所示：

![img](https://pic1.zhimg.com/80/v2-536629a9f2b0f3501b7ebb86006a7314_720w.jpg)

更多的关于超高程的知识可以去搜搜火车是怎样过弯的，是怎样变轨的，如果你不知道的话，相信你一定很好奇。

**道路形状 Road Shape**

一般来讲，看道路的时候有两个视角：

- 一个是从上（天上）往下看，这时最值得关注的是道路参考线，代表道路在俯视下的走向。在OpenDRIVE中对应为<planView>元素。
- 另一个就是看道路的横断/截面或侧截面，这样能够看出来道路的高度变化。

下面是最常见的一种道路形状，主要是为了防止道路有雨水积存，便于排水。从横断面看：

![img](https://pic2.zhimg.com/80/v2-aee35f635fcda37a823cbb67fed3490d_720w.jpg)

还有下面这种，高速测试跑道以及路拱上的弯曲路面：

![img](https://pic1.zhimg.com/80/v2-a51b3aba3b0ef58630a7d19c961a257c_720w.jpg)

那么像这种复杂的横向道路形状怎样描述呢，怎么建模呢，OpenDRIVE中也提到了，还是使用三次多项式来建模。简单来说，就是给定s值下的关于t的三次多项式（自变量是t,函数值是h），计算不同的横向偏移t处的高度值h，然后再通过插值的方式计算出任意点的高度值h。如下图所示：

![img](https://pic2.zhimg.com/80/v2-94f66c43366f83ef34eef5cb7402f195_720w.jpg)

最后，关于这些高程、道路形状具体在OpenDRIVE中元素和属性是如何定义的，后面再讲~~~

------

最后，使用高精地图的目的是为了在仿真中找到全局的路径规划（Route Planning）,找到车辆当前所处的道路、道路段、车道，以及某些静态的交通标识等等，所以我就抽时间看了高精地图，看了OpenDRIVE说明文档。