- [OpenDRIVE格式地图数据——极简概述（二） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/360540863)

## 五、道路参考线 Road Reference Line

道路参考线（一般是道路的中心线）用来描述道路在俯视下的走向，是OpenDRIVE中每条道路的基本元素。前面讲到的道路几何形状就是用来描述道路参考线的，没有什么特别之处，就是一段或几段曲线，没有宽度和高度。

下图中蓝色的线就被称为道路参考线。同时，下图还说明了道路应当包含的内容：参考线、至少一条车道、道路标志等。现在，是否明白“道路”和“车道”是两个不同的概念了。

![img](https://pic2.zhimg.com/80/v2-8df07cf07bada267a4651134e688c409_720w.jpg)

那么在XML文件中，OpenDRIVE用<road>元素里的<planView>元素来表示道路参考线。参考线的几何形状用<planView>里的<geometry>元素来表示。注意：由于道路的原因，一般不可能用一条曲线（一个几何形状）来完全表述整个道路，所以在<planView>元素里，一般都包括几个<geometry>元素，它们一定是前后相连接（s值）无重叠的。

如下图所示，表明该道路参考线由“**直线+参数三次多项式+参数三次多项式+直线**”构成，他们的s值是前后相接的。

![img](https://pic3.zhimg.com/80/v2-eb62f0feee84c746814d26765e0350ba_720w.jpg)

至于每种几何形状的元素表示我们前面已经讲过了，直线是<line>，参数三次多项式是<paramPoly3>。下面是其他属性的介绍：

- s 该段曲线起始位置的s坐标 （参考线坐标系）
- x 该段曲线起始位置的x值 （惯性坐标系）
- y 该段曲线起始位置的y值 （惯性坐标系）
- hdg 该段曲线起始点的方向 （惯性航向角/偏航角 yaw）
- length 该段曲线的长度 （参考线坐标系）

**注意**：每条道路必须有且只有一条道路参考线。

## 六、道路

在有了道路参考线之后，我们就可以开始描述道路的属性，每条道路都沿着一条道路参考线延伸，一条道路必须至少拥有一条宽度大于0的车道。道路参考线只是一条宽度为0的线。在OpenDRIVE中用<road>元素来表示。

那么是如何区分不同道路的呢，或者何时应当开始一条新的道路？OpenDRIVE中也有提到，只有在道路的属性不能在先前<road>元素中得到或者需要一个交叉口的情况下，才应该开始一个新的<road>元素。注意，此处是指道路的属性，而非车道的属性。简单来说，就是当前的<road>元素不能描述下一段道路了，或下一段道路是交叉口，那么我们就要另起一个<road>元素来描述一个新的道路。

![img](https://pic1.zhimg.com/80/v2-36be948424aefbed3b14689425c0fd7c_720w.jpg)

针对于道路元素，有以下几个属性：

- name 道路的名称，可以随意选择
- length 道路的总长度，指道路参考线的总长度，此时不考虑**高程**的影响。
- id 该条道路在数据库中的唯一id，其形式应当是一个uint32类型的整数
- junction 如果该条道路在交叉口内，那么该值就代表交叉口的id，否则该值为-1。
- rule 使用道路的基本规则； RHT =靠右行车，LHT =靠左行车。当缺少此属性时，将假定为RHT。

**道路横截面属性和道路段属性**

前面我们说过看一条道路可以从两个视角来看：俯视和横截面。那么某些道路属性是基于道路横截面得到描述的，比如**超高程**，如果元素对道路横截面有效，那么它对道路参考线上给定点处的整个宽度都有效。其他道路属性是基于道路俯视图得到描述的，其中包括**车道和道路高程**。这些属性称为道路段，其描述了道路的各个部分以及它们沿道路参考线s坐标的特定属性。对路段有效的属性仅对特定车道有效，可能对整个道路宽度无效。

上面一堆话什么意思呢，就是道路有两种不同的属性，一种对**整个道路**都有效，另一种只对**道路段**有效。简单说一下道路段：为了描述不同的车道和道路高程属性而给道路分段，每一段就叫做道路段，在OpenDRIVE中用<laneSection>元素表示。举个栗子，比如你现在在开车，你开车的方向现在有四条车道，然后突然道路汇流，变成了三车道，那么这就是两个不同的道路段，因为车道的个数发生了改变。

![img](https://pic2.zhimg.com/80/v2-31d76268ef53b3317f485800ed1b6b69_720w.png)

**道路连接**

很简单，给你一条道路，应当能够通过道路索引获得该条道路的前驱和后继，如果是孤立的道路可以忽略。在OpenDRIVE中用<road>里的<link>元素来表示。注意区分道路连接和后面的车道连接是有区别的。

![img](https://pic1.zhimg.com/80/v2-cdce61d28b6af09662226d68bab817c8_720w.png)

其中<predecessor>代表前驱，<successor>代表后继。属性介绍如下：

- elementType 被连接道路的类型，一般为“road”或“junction”。
- elementId 被连接道路的id
- contactPoint 被连接道路的连接接触点，一般被连接道路的起点为start，终点为end，用来表明连接的方向。
- elementS 用于虚拟交叉口的接触点处的s值，因为接触点不在起点或终点。是contactPoint的替代。
- elementDir 该属性将在用elementS定义连接时提供，它标明了道路进入的前面的道路的方向。

**道路类型和限速**

道路类型（例如高速公路以及乡村公路）定义了道路的主要用途以及相关的交通规则。道路类型对于整个道路横截面均有效。在OpenDRIVE中道路类型用<road>元素中的<type>元素来表示。此外，可为道路类型设置速度限制（限速），用<type>元素中的<speed>来表示。

![img](https://pic4.zhimg.com/80/v2-24bb1bfcaa8e8aca41e75f74da5dcc7b_720w.png)

- s 道路起始位置的s坐标
- type 道路的国家/地区代号
- country 道路的国家/地区代号
- max 道路限速
- unit 单位

注意：

- 当道路类型有变更时，必须在父级<road>元素中创建一个新的<type>元素。
- 单独车道可能与其所属道路的类型不同。道路类型和车道类型代表不同的属性，若有具体说明，那么两种属性都为有效。
- 单独车道可以拥有不同于所属道路的速度限制，其将被定义为<laneSpeed>。

**高程**

上一篇文章最后写了高程的概念和分类，现在来看一下怎样在OpenDRIVE中定义高程。这一小节我可能表述的不是很清楚，毕竟语文不好，建议对照着看官方的说明文档。

道路高程

一般用的最多的就是道路高程，还记得道路高程是什么吗，类似于上下坡，道路沿着参考线是跌宕起伏的。用<elevationProfile>元素里的<elevation>来表示。值得注意的是，道路高程的建模是三次多项式函数：

elev(ds) = a + b*ds + c*ds² + d*ds³

其中，ds是沿着参考线上新高程元素的起点s与给定位置之间的距离（即相对计算起点的距离），elev是给定s位置上的高程。如图所示：

![img](https://pic4.zhimg.com/80/v2-4acd5be34e7682dbaffc954eed3d8427_720w.png)

元素里面属性的意义也很简单：

- s 该道路高程元素起始位置的s坐标
- a 多项式的参数a
- b 多项式的参数b
- c 多项式的参数c
- d 多项式的参数d

每当出现一个新的<elevation>元素时,我们就要使用下面的公式重新计算ds。

s_cur = s+ ds，其中s_cur是你给定的计算高程的位置。

超高程

超高程是横断面图的一部分，它描述了道路的横披。它可用于将道路往内侧倾斜，从而使车辆更容易驶过。超高程从数学角度被定义为围绕参考线的道路横截面的倾斜角。这意味着超高程对于向右边倾斜的道路具有正值，对于向左边倾斜的道路具有负值。超高程也是用三次多项式描述的，只不过函数值是当前s位置处的**路段倾斜角**，计算方式与道路高程相同。

s_Elev(ds) = a + b*ds + c*ds^2 + d*ds^3

超高程在OpenDRIVE中用<lateralProfile>元素中的<superelevation>元素来表示。

道路形状

由于某些横向道路形状过于复杂，仅使用超高程来描述是不够的。通过形状则能够更详细地描述在参考线上给定点处的道路横截面的高程。这意味着，一个有多个t值的s坐标上可以拥有多个形状定义，从而对道路的弯曲形状进行描述。

额，上面这段描述听起来不太好懂，什么意思呢，就是因为有些道路高低不平，也没有什么规律性而言，不能用超高程来建模，只好再定义一个更复杂的道路形状来建模好了。通过插值的计算方式上一篇文章说过了，我们简单了解一下就好，知道有“道路形状”这个东西。

在OpenDRIVE中，用<lateralProfile>元素中的<shape>元素来描述，

![img](https://pic4.zhimg.com/80/v2-39535e20519043469a9f9fb47c30c2af_720w.jpg)

其中属性的定义为：

- s 起始位置的s坐标
- t 起始位置的t坐标
- a 多项式参数a
- b 多项式参数b
- c 多项式参数c
- d 多项式参数d

**道路表面**

OpenDRIVE并不包含对道路表面的描述，该类描述则包含在OpenCRG中，但OpenDRIVE可以引用在OpenCRG中生成的数据。两者均不包含有关道路表面视觉展示的数据。借助OpenCRG可以对更细节化的道路表面属性进行建模，如下图的卵石或坑洼：

![img](https://pic2.zhimg.com/80/v2-02d6f9183226f4cb1d0b7d3fd69d6231_720w.jpg)

这个道路表面好像也挺复杂的，我也没怎么看懂。我的理解就是在OpenDRIVE中道路表面用<surface>元素来表示，在该元素内会有一个属性是包含CRG数据的文件名称，用来对道路表面建模。如下所示：

![img](https://pic4.zhimg.com/80/v2-368a1777aa9dd8bdecb67072d600fcd7_720w.jpg)

现在，你应当对道路（road）有足够的了解，并且能够看懂一部分XML内容了。

比如你应当不假思索的知道下面这些元素和属性都是如何定义的，我们该怎样去理解，最好在脑海中开一下车。

![img](https://pic4.zhimg.com/80/v2-108e3ad8832ae275b31b8585140ded0b_720w.jpg)

## 七、车道

在理解道路的基础之上，我们看看车道都是如何建模的。看下面这张图，这是一条道路，请问有几个道路段呢？是一个还是两个？

![img](https://pic4.zhimg.com/80/v2-f11402b8ac6eb7de111114918ff146c7_720w.jpg)

答案是两个，因为车道的个数发生了改变。同时，我们也能从这张图上直观的看出车道、道路、甚至是道路参考线的关系。你有注意上图每条车道上面的数字了吗，对的，这是车道的id，沿着道路参考线方向的左侧为正，右侧为负，且每侧的绝对值从小到大递增，道路参考线的id为0。当然，我们也可以拥有单行道：

![img](https://pic3.zhimg.com/80/v2-266edb8907db4b864829534ba67da0ea_720w.jpg)

在OpenDRIVE中，车道用<road>元素里的<lanes>元素来表示。这是一块非常庞大的内容。

在<lanes>元素中，每一个<lane>元素即是一条车道，如图所示：

![img](https://pic4.zhimg.com/80/v2-ce7c7647852a418592b3c58a29e22a23_720w.png)

<lane>元素内的属性解释为：

- id 车道的唯一编号
- type 车道类型 driving 代表一条“正常”可供行驶、不属于其他类型的道路。关于其他车道类型的定义可以去看说明文档，这里不再给出。
- level 当该属性为true时，那么该车道将会被道路的超高程和道路形状定义排除。车道的高程则与内侧连接车道的高度保持一致。也即是不受高程限制。例如人行横道、自行车道。如下图所示，**id = 4和id = -4的车道从道路超高程中被排除**。

![img](https://pic4.zhimg.com/80/v2-1e67cfab52fb6642c118efc175a9e2eb_720w.jpg)

**车道段**

我更想称之为道路段，其实在我看来是一个意思。像刚才提到的图片上的道路就是两个道路段，不要想的很复杂，简单想想就能想明白。

![img](https://pic3.zhimg.com/80/v2-28701ad0f01d0f83626598e2a1d3e40e_720w.jpg)

在<lanes>元素里，至少拥有一个道路段，也即是至少包含一个<laneSection>元素，该元素中有一个属性用来表示该道路段开始的s坐标。另外，每条车道都是定义在车道段内的。

为了能够便利地在OpenDRIVE的道路描述中进行查找，一个车道段内的车道可分为左、中和右车道，对应于<left>、<center>、<right>元素。每个车道段均必须包含一个<center>元素和至少一个<right>或<left>元素。车道元素被包括在左/中/右元素中，车道元素必须使用降序ID从左到右展示车道。比如下图所示，只有右侧车道，是单行道：

![img](https://pic1.zhimg.com/80/v2-2bb64587009df753b5e35b733f22df10_720w.jpg)

**车道限速**

可对车道上允许的最大行驶速度进行定义，该车道限速随即将覆盖道路限速。在OpenDRIVE中，车道速度用<lane>元素内的<speed>元素来表示。

- sOffset 起始位置的s坐标, 相对于<laneSection> 元素的位置
- max 最大允许速度
- unit max属性的单位

**车道高度**

在OpenDRIVE中，车道高度用<lane>元素内的<height>元素来表示。

- sOffset 起始位置的s坐标, 相对于<laneSection> 元素的位置
- inner 道路水平的内偏移
- outer 道路水平的内外偏移

![img](https://pic4.zhimg.com/80/v2-ad536b9bed01b34e35e961ce10125a3f_720w.jpg)

**车道规则**

OpenDRIVE在<lane>元素内提供了<access>元素，以便描述车道使用规则。

- sOffset 起始位置的s坐标,相对于<laneSection>元素的位置
- rule 详细说明了在@restriction属性中指定的参与者是否可以使用车道
- restriction 限制所针对参与者的标识符

![img](https://pic3.zhimg.com/80/v2-52ac096d85acad832451fbb88c99245a_720w.jpg)

**车道偏移**

车道偏移用来将中心车道从道路参考线上位移，也即是中心车道（id = 0）不和道路参考线重叠，至于为什么这样做呢，OpenDRIVE中提到是为了更轻松地在道路上对车道地局部横向位移进行建模（比如对左转车道进行建模）。

![img](https://pic1.zhimg.com/80/v2-4fe0132b922b9b17f3891acbbca86bf0_720w.jpg)

在OpenDRIVE中，车道偏移用<lanes>元素内的<laneOffset>元素来表示。

![img](https://pic1.zhimg.com/80/v2-f02f294553332c67f5e18c890d127d94_720w.jpg)

其计算方式仍然是三次多项式，自变量是ds，也即是是新的道路偏移元素起始处和给定位置之间沿道路参考线产生的距离。函数是在给定点处的**横向偏移**。其元素内包含的属性如下：

- s 该段三次多项式s坐标的起始位置
- a 三次多项式参数a
- b 三次多项式参数b
- c 三次多项式参数c
- d 三次多项式参数d

注意：

- 车道偏移不能与道路形状一同使用。
- 若边界定义已存在，则不允许出现偏移。

**车道连接**

很简单，我们既然有了道路连接，也会有车道连接，不然怎样知道车道的前后关系呢，想想一下我们在现实的道路上开车，我们不仅要知道当前道路后面是哪条道路，我们也应当知道当前车道的下一车道是哪一条车道。

![img](https://pic3.zhimg.com/80/v2-5f6cb8917df22467bb46e15a8dff5a32_720w.jpg)

在OpenDRIVE中，车道连接用<lane>元素里的<link>元素来表示。<predecessor>和<successor>元素在<link>元素内得到定义。

- id 前驱/后继连接的车道ID

![img](https://pic2.zhimg.com/80/v2-364f8a8cd9fa8d9224a73f598b3e09e5_720w.jpg)

**车道宽度和车道边界**

想一想，车道宽度和车道边界应当是属于一类的，都是描述车道的横向属性。车道宽度与车道边界元素在相同的车道组内互相排斥。

在OpenDRIVE中，车道宽度由<lane>元素中的<width>元素来表示。

在OpenDRIVE中，车道边界用<lane>元素中的<border>元素来表示。

其建模方式都是相似的，仍然是使用三次多项式，只不过一个函数值是车道宽度，另一个是边界t值。

这里只说一下车道宽度，如下图所示：

![img](https://pic1.zhimg.com/80/v2-23476152c88897f8a5b9ae0a91d63748_720w.jpg)

其属性为：

- sOffset 相对于<laneSection> 起始位置s处的偏移坐标
- a 三次多项式参数a
- b 三次多项式参数b
- c 三次多项式参数c
- d 三次多项式参数d

使用如下三次多项式函数来计算给定点的宽度：

Width (ds) = a + b*ds + c*ds² + d*ds³

**值得注意的是，这里的ds和之前提到的其他三次多形式里的ds稍有不同，这里的ds定义为**：

s = s_section + offset_start + ds

当我们要求一个绝对坐标点s处的宽度时，我们需要根据<laneSection>中的起始坐标s值（s*section），当前三次多项式偏移属性sOffset(*offset_start)来计算出ds，再带入到三次多项式中。

注意：

- 车道的宽度必须在每个车道段中至少被定义一次。
- 中心车道不能拥有宽度，也就是说不能对中心车道使用<width>元素。
- 不能在相同车道组里同时使用宽度元素以及边界元素。

**道路标识**

这就很简单了，道路上的车道可拥有不同的车道标识，比如不同颜色和样式的线。

OpenDRIVE为路标提供了<roadMark>元素。路标信息定义了车道外边界上的线的样式，在左车道上则为左边界，在右车道则为右边界。而作为分隔左右车道的中心线的样式则由中心车道路标元素来确定。

![img](https://pic4.zhimg.com/80/v2-277c81db3c3cd0a7f8e31bd63824963b_720w.png)

其实这一部分OpenDRIVE说明文档里面讲的挺多的，也挺细，讲到了路标类型和线条、显性的路标类型和线条、路标偏移等等，额，我没仔细看，大概说一下几个属性的定义吧：

- sOffset <roadMark>元素起始位置的s坐标, 相对<laneSection>元素的位置。
- type 路标类型
- weight 路标粗细
- color 路标颜色
- material 路标材质
- height 路标高度
- laneChange 考虑到车道按照升序从右到左被编号，该属性可使车辆朝指定方向变道。若属性缺失，则选用“both”作为默认值。有下面四个值：increase; decrease; both; none。

------

车道的介绍我可能写的比较简单，**因为我写这几篇文章的目的就是尽可能地简单，如果你能够理解各个元素和属性的定义、那就已经足够了**，至于一些具体的细节还是需要你去看说明文档。

然后，现在你应该能够看懂XML文件大部分的内容了。

------

最后，还有一部分：**交叉口、信号、物体和轨道等**，应该还要再写一篇......

![img](https://pic2.zhimg.com/80/v2-7204dedec2b02346546116c125446399_720w.jpg)