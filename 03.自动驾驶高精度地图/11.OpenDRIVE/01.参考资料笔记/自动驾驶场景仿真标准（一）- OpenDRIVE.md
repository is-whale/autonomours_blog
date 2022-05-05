- [自动驾驶场景仿真标准（一）- OpenDRIVE - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/352422886)

**1. OpenDRIVE的概况**

OpenDRIVE格式是以可扩展标记语言(XML)为基础，文件后缀为xodr格式的描述道路及道路网的通用标准。存储在OpenDRIVE文件中的数据描述了道路的几何形状以及沿线的特征并且定义了可以影响交通逻辑的交通标志以及道路基础设施，例如车道和信号灯。

路网是OpenDRIVE文件中描述的道路信息，其既是基于经验建造的，也可以是依据真实道路数据生成的。OpenDRIVE的主要目的是提供一种可用于仿真模拟的道路网络描述，并且可以使得这些道路以及道路网的描述可以在仿真平台或仿真软件中被自定义或改变。

OpenDRIVE根据XML的格式以节点和元素描述道路中各类信息。这样的通用格式有助于虚拟仿真测试的高度专业化，并且可以保持不同国家之间数据交换所需的相互操作性。

本文介绍了OpenDRIVE格式以及OpenDRIVE描述道路的方法。因为OpenDRIVE以XML格式为拓展格式，所以描述道路的每一元素和单元都有一定的从属关系，本文当中的元素从属关系若读者想有一个笼统的结构请参考：

[OpenDRIVE结构图github.com/ruomusim/Intro_OpenDRIVE](https://link.zhihu.com/?target=https%3A//github.com/ruomusim/Intro_OpenDRIVE)

![img](https://pic1.zhimg.com/80/v2-f1cdf08de89d8f6ac87f45d7d021a1ec_720w.jpg)OpenDRIVE结构图

OpenDRIVE的例子可以参考如下的xodr文件：

[.xord示例文件github.com/ruomusim/Intro_OpenDRIVE](https://link.zhihu.com/?target=https%3A//github.com/ruomusim/Intro_OpenDRIVE)

本文根据ASAM OpenDRIVE对OpenDRIVE格式这一描述道路场景的格式进行介绍。

OpenDRIVE官方文档请见ASAM官网（ASAM官网已有中文版OpenDRIVE）：

[OpenDRIVE官网www.asam.net/standards/detail/opendrive/](https://link.zhihu.com/?target=https%3A//www.asam.net/standards/detail/opendrive/)

## **2. OpenDRIVE和OpenSCENARIO以及OpenCRG之间的关系**

OpenDRIVE是ASAM仿真标准的一部分，它专注于定义用于描述静态道路网的存储格式，并使其可以对仿真中车辆道路环境进行描述。除了OpenDRIVE之外，ASAM还为仿真领域提供其他标准，如OpenSCENARIO和OpenCRG。OpenDRIVE与OpenCRG相结合可以对道路添加非常详细的道路表面信息。ASAM OpenDRIVE和ASAM OpenCRG只包含交通场景的静态内容，要添加动态内容，则需要ASAM OpenSCENARIO。这三个标准结合起来，提供了一个包含静态和动态信息的模拟交通场景描述。

![img](https://pic1.zhimg.com/80/v2-190b96d5c909f8a64b03f92ef1031e3c_720w.jpg)

## **3. OpenDRIVE文件结构**

OpenDRIVE数据存储在扩展名为.xodr的XML文件中。OpenDRIVE文件结构符合XML规则。元素按XML格式的等级进行排列，级别大于零（0）的元素为 子元素。级别为（1）的元素称为主元素。每个元素都可以用户定义的数据进行扩展。每个OpenDRIVE文件都会有一个主元素<OpenDRIVE>，所有描述道路的特征类都是它的子元素。

OpenDRIVE中使用的所有浮点数都是IEEE 754双精度浮点数的数字。为了保证浮点数字在XML中的准确表示，在XML中的浮点数表示应该使用一个已知的正确精度，一般使用保留17位有效数字来描述数字。

所有可以在 OpenDRIVE 文件中使用的属性都在 UML 模型中被完全注释：

- 单位： 道路长度或速度等的单位
- 种类： 描述一个属性的数据类型， 可以是一个原始数据类型，例如，string、double、float，或者是指代对象的复杂数据类型。
- 值： 值决定了给定属性的值范围，例如：

<geometry s="4.9957524872074799e+02" x="4.9469346060416666e+02" y="5.3447643627860181e+01" hdg="5.8804473418180125e-02" length="6.2079164697363019e+01"> <line/> </geometry>

其中geometry代表了当前元素所要描述的道路单元，其中geometry的属性有s，x，y，hdg和length，他们的值跟随在后面。

## **4. OpenDRIVE中的坐标系**

OpenDRIVE使用三种类型的坐标系，如下图所示：

![img](https://pic1.zhimg.com/80/v2-ae973da8ef8b88da2b74fccc47678768_720w.jpg)

- 惯性x/y/z坐标系
- 参考线s/t/h坐标系
- 局部u/v/z轴坐标系

**4.1 惯性x/y/z坐标系**

惯性坐标系描述的是地图中具体某个点在当前参考坐标系下的位置。其中x y z坐标可以具体表示某一个点所在的位置。如在描述某一段道路时，道路起始点的位置就是由x y z坐标定义。道路就是在这个点开始，按一定方向和一定长度延伸。

<geometry s="4.9957524872074799e+02" x="4.9469346060416666e+02" y="5.3447643627860181e+01" hdg="5.8804473418180125e-02" length="6.2079164697363019e+01"> <line/> </geometry>

在上述例子中，x="4.9469346060416666e+02" 和y="5.3447643627860181e+01"描述的是此段道路的起始点在惯性坐标系中的位置。

**4.2 参考线s/t/h坐标系**

参考线坐标系适用于沿道路的参考线。它是一个右旋坐标系。s方向沿参考线的切线。需要注意的是，参考线总是位于惯性坐标系定义的x/y平面内。t方向与s方向正交。右手系统通过定义与x轴和y轴正交的上方向h来完成。

![img](https://pic1.zhimg.com/80/v2-8e0ac810a0aa3f1698d1f64740f1354c_720w.jpg)

如下为参考线坐标系的三个方向描述：

- s方向：沿参考线的坐标，从道路起点开始测量，单位为[m]。参考线，在 xy 平面上计算（即，不考虑道路高程剖面图)
- t方向：横向位置，从参考线出发，延与s方向正交的方向，指向s方向左侧。
- h方向：在右手坐标系中与st平面正交

<geometry s="4.9957524872074799e+02" x="4.9469346060416666e+02" y="5.3447643627860181e+01" hdg="5.8804473418180125e-02" length="6.2079164697363019e+01"> <line/> </geometry>

在上述例子中，s="4.9957524872074799e+02"描述的是此段道路的起始点在参考线坐标系中的位置。

**4.3 三种坐标系的关系**

三种坐标系的关系可通过下图清晰展示：

![img](https://pic2.zhimg.com/80/v2-bb29319b4f609a6bf08d93ddb3545fc1_720w.jpg)

## **5. 道路的几何描述**

一条完整的道路由很多段分开的道路组成，每段道路可以有很多不同的几何形状。有长而直的道路，也有固定几何图形的弯路。如高速公路上的很长的直到，或山间的狭窄弯道。为了建立道路模，OpenDRIVE提供了表达这些道路的数学方式的解决方案。每一段道路都是以<road>为主元素，属性为：<name>, <length>, <id>, <junction>。Name为道路名字，length是当前道路总长度，id为道路的id，junction为当前道路所属的道路连接处的id，若id为-1则表示该道路不与任何道路相连接。

下图展示了定义道路参考线几何元素的五种可能方式：

- 直线
- 曲率呈线性变化的螺旋线或回旋曲线
- 具有恒定曲率的弧线（圆弧）
- 三次多项式
- 参数化的三次多项式

![img](https://pic1.zhimg.com/80/v2-1e45f747d94019f68303702717fd2c28_720w.jpg)

**5.1 道路参考线**

道路参考线是OpenDRIVE中所有路的基本且必要元素。道路几何线代表了道路的几何形状，并且其余道路的所有特征都如车道线都是以道路参考线为基础而扩展的。如下图所示，在这一段路中，道路参考线代表了这段路的基本几何形状，以此为基础添加了车道线和道路上其余的特征共同组成了一段路。

![img](https://pic4.zhimg.com/80/v2-9e189aadb207963b38cdde738537baaf_720w.jpg)

在OpenDRIVE中道路参考线元素属于<planView>元素下。<planView>元素为<road>的子元素。道路参考线使用上述介绍的道路参考线坐标系s/t/h，s的正方向即为道路参考线的延伸方向。所以在OpenDRIVE的描述中s的范围为[0;+∞]。

geometry所需的属性为：

- s: 当前道路线的起点在参考线坐标系中s方向的位置
- x: 当前道路线的起点在惯性坐标系中x方向的位置
- y: 当前道路线的起点在惯性坐标系中y方向的位置
- hdg: 当前道路线的朝向（对于直线来说即为arctan（直线斜率），对于曲线来说为起始点的切线方向arctan（切线斜率））
- length: 当前道路线段的长度

**5.2 直线**

![img](https://pic4.zhimg.com/80/v2-0235d08332f84a648be1357512c88acb_720w.jpg)

直线的参考线描述为在参考线的描述中加如<line>无需其他描述。

例如：

<geometry s="4.9957524872074799e+02" x="4.9469346060416666e+02" y="5.3447643627860181e+01" hdg="5.8804473418180125e-02" length="6.2079164697363019e+01"> <line/> </geometry>

**5.3 螺旋线**

螺旋线是曲率线性变化的弧线，在描述螺旋线时需加入属性<curvStart>和<curvEnd>来表示螺旋线曲率的线性变化。

![img](https://pic2.zhimg.com/80/v2-58da850b1e5531225fa7742fd0701ac5_720w.jpg)

螺旋线开始时的曲率和螺旋线结束时的曲率的范围为负无穷到正无穷。曲率为正为向下弯曲，曲率为负为向下弯曲。

例如：

<geometry s="4.9957524872074799e+02" x="4.9469346060416666e+02" y="5.3447643627860181e+01" hdg="5.8804473418180125e-02" length="6.2079164697363019e+01"> <spiral curvStart=“0.0” curvEnd=“0.013”/> </geometry>

5.4 圆弧：

![img](https://pic3.zhimg.com/80/v2-bf31de5899f7c80fd7428e7c5b9d7e52_720w.jpg)

圆弧为曲率为固定常数的弧线，在描述圆弧时在<arc>中加入curvature表示不变的圆弧。

例如：

<geometry s="4.9957524872074799e+02" x="4.9469346060416666e+02" y="5.3447643627860181e+01" hdg="5.8804473418180125e-02" length="6.2079164697363019e+01"> <arc curvature="3.7654369743233681e-03"/> </geometry>

5.5 多项式拟合曲线：

OpenDRIVE提供了更复杂的几何线形表述形式三次多项式拟合曲线。

![img](https://pic1.zhimg.com/80/v2-3e76ad6f0011267d055c0feca1fd481c_720w.jpg)

在描述三次多项式拟合曲线的<poly3>中加入属性a，b，c，d表述三次多项式表达式的参数a，b，c，d：

<geometry s="4.9957524872074799e+02" x="4.9469346060416666e+02" y="5.3447643627860181e+01" hdg="5.8804473418180125e-02" length="6.2079164697363019e+01"> <poly3 a=xxxx b=xxxx c= xxxx d=xxxx/> </geometry>

## **6. 车道**

在OpenDRIVE中，有了道路参考线之后就可以以此为基础定义所有沿着当前道路参考线的车道。每条车道都会有一定数目的一定宽度的车道。

![img](https://pic3.zhimg.com/80/v2-e67123aa41c0625cfba3c39b1ccc9cca_720w.jpg)

如上图所示，道路中间id为0的线即为道路参考线，其余的车道在沿着道路参考线的方向（s坐标的正方向）在其左右两边扩展。沿着t方向正方向拓展的车道的id大于0，沿着t方向负方向拓展的车道id小于0。每一条车道的款的和其余的属性都可以在定义车道时被自定义。

在定义车道时，车道的元素<lanes>的父元素为<road>。

首先应定义<laneOffset>，其中属性：

- s: 该offset从s方向上的此位置出发
- a: 以三次多项式拟合线形状偏离道路参考线中三次多项式的参数a
- b: 参数b
- c: 参数c
- d: 参数d

而后定义<laneSection>，每一条道路都会被分为多段车道段，每一段车道段可以有不同的车道线数量和车道线宽度。如下图所示：

![img](https://pic2.zhimg.com/80/v2-e51ff60756492f8cd3f1491a8593d061_720w.jpg)

<laneSection>的属性s表示该段车道从车道线s方向的何处出发。

而后定义其子元素<left>和<right>元素，意为道路参考线左侧和右侧的车道。

<left>的子元素<lane>的属性有：

- id: 该车道的编号，延参考线坐标系的t方向增加
- type:该车道线的种类（驾驶车道，停车道，边界线）
- level:道路是否保持在同一水平面上

而后定义<lane>的子元素<link>，表示该车道是否与之前一段或后一段车道段中的车道相连。

<width>的属性有：

- sOffset: 在s方向上从何处开始有宽度的变化
- a:宽度变化以三次多项式的形状，其中参数a
- b:参数b
- c:参数c
- d:参数d

再定义子元素<roadMark>作为车道线的属性，其中的属性有：

- sOffset:从s方向的何处开始有roadMark属性
- type:车道线的类型（实线，虚线，双实线等）
- weight:车道线粗细
- color:车道线的颜色
- width:车道线的宽度
- laneChange:考虑到车道按照升序从右到左被编号，该属性可使车辆朝指定方向变道。若属性缺失，则选用“both”作为默认值。
- height:车道线的高度

再定义子元素<speed>作为车道的速度定义，其中属性：

- sOffset: 从车道的s方向的何处开始有最大速度的定义
- max: 在此车道线上最大的速度

<laneSection>的子元素<right>与<left>完全一致，只是需要注意车道的id，沿着车道参考线t方向的负方向减少。

<laneSection>的子元素<center>相对简单，只定义了道路参考线的车道线的属性，即为道路中心线的属性。

例子如下图所示：

![img](https://pic2.zhimg.com/80/v2-2c20aa0e2e8e816766303701b0891f0d_720w.jpg)

## **7. 道路的相互连接**

单条道路已经被定义后，要组成道路网需要完成道路和道路之间的相互连接。如何对进行道路的有序连接，OpenDRIVE提供了两种道路的连接方式：connecting road和junction。

Connecting road是指两条道路前后相连，有明确的前后连接的路。如下图所示：

![img](https://pic2.zhimg.com/80/v2-4898e8e2fbe218363db16f6595030081_720w.jpg)

对于每段路的车道，都有明确的id，而每段车道id的相连和连接方向，都可以在OpenDRIVE中进行表示。

定义connecting road时是在<lane>的父元素下<link>的子元素中进行定义。所需要的属性为：

- Predecessor：表示与当前车道相连前面的路
- Successor：表示与当前车道相连后面的路。

在表示两条道路的车道相连后，对于更复杂的连接情况，需要用到junction。

junction是指三条或三条以上道路相交的区域。如下图所示，有两种不同类型的道路与junction的关系。

![img](https://pic1.zhimg.com/80/v2-34c4f183f1ba960405057ecc8d57acd8_720w.jpg)

Incoming road：代表着沿着道路在进入connecting road之前的路

Connecting road：这些道路代表通过路口的路径。

在OpenDRIVE中，表示junction使用<junction>作为主元素。连接路为交叉口<junction>的子元素以<connection>表示。

在定义junction时，<junction>的属性有：

- name: junction的名字
- id: junction的id。

然后定义其子元素<connection>,其中要定义的属性有：

- id: connecting road的id
- incomingRoad：进入connecting road之前的路的id
- connectingRoad：进入connecting road之后的路的id
- contactPoint：在connecting road处的连接点（start or end）

用一个例子来说明connecting road和junction的连接：

![img](https://pic3.zhimg.com/80/v2-f9d4d59bf920c216d92ed9f0c81dad8a_720w.jpg)

对于Road 10来说，其车道-1和车道-2的连接关系为：

![img](https://pic2.zhimg.com/80/v2-afd83d2695366a15be49d38f35366859_720w.jpg)

对于Road30来说，其与Road 10和Road 70先后相连接，用connecting road的表达方式来表达这段路的连接即为：

![img](https://pic4.zhimg.com/80/v2-98c3e33049ef8822e2aebfaab59da943_720w.jpg)

对于Road 10与Road 20，Road 30和Road 40的交界处的junction，其表示为：

![img](https://pic2.zhimg.com/80/v2-4e9dd73b95a8c88649588a23f56eb061_720w.jpg)

通过这样的两种道路连接方式，可以将道路进行连接。