- [OpenDRIVE格式地图数据——极简概述（三） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/361246912)

## 八、交叉口

交叉口是指的是三条或更多道路相聚的地方，与其相关的道路被分为两种类型：来路（IncomingRoad）和连接道路（ConnectonRoad）。

![img](https://pic4.zhimg.com/80/v2-b80bb29270cac285dc6cab1d8c72aceb_720w.jpg)

对于来路和连接道路的理解，OpenDRIVE中似乎没有很详细的说明，大致可理解为：

**来路**包含了通向交叉口的车道，当然来路肯定不再交叉口中。而OpenDRIVE中没有对去路做定义，那么来路也可以视为去路，具体的判断标准是通过<connection>元素内的<contactPoint>属性来判断（后面会讲）。

**连接道路**是在交叉口中的道路，该道路描述了车辆穿过一个交叉口的路线。其连接道路和来路的道路参考线应当是连接的，否则车道之间线路不通。

如上图所示，画出了来路和连接道路之间的前后连接关系，当然这里的来路也包括去路。其实很好理解，就是能否在交叉口中通行，能的话就一定有道路的连接。多想想就明白来。

那么有了道路的连接之后，相应的车道连接就也有了，如下图所示：

![img](https://pic3.zhimg.com/80/v2-61d0eb4c5033fc6e77b5bb1ac480bdf2_720w.jpg)

下面我们看一下在OpenDRIVE文件中是如何描述交叉口的。

![img](https://pic2.zhimg.com/80/v2-e7a47bf6c168424205604463d4654785_720w.jpg)

首先我们会有<junction>元素，在该元素内有以下属性

- name 交叉口的名称
- id 交叉口的唯一ID
- type 交叉口的类型，有default和virtual两种选项，在默认情况下即为常规的交叉口。

<junction>元素中的每一个<connection>元素就代表了一个道路连接。<connection>元素的属性为：

- id 该道路连接的唯一ID

- type 仍然是交叉口的类型

- incomingRoad 这个就是我们上面提到的来路ID

- connectingRoad 这个是上面提到的连接道路ID

- contactPoint 这个是连接道路的连接点，很重要，用来区分是来路还是去路：“start”代表来路连接到连接道路的起始点，即来路就是来路；“end”代表来路连接到连接道路的终点，此时来路是去路。这里的起始点和终点都是相对于道路参考线来看的。官方文档中也给出了start和end值的描述，尽管有些晦涩难懂：

- - "start"值必须用于标明联接道路正在沿<laneLink>元素中的连接延伸。
  - "end"值必须用于标明联接道路正在沿<laneLink>元素中的连接的反方向延伸。

在<connection>元素中会有至少一个车道连接，用<laneLink>来表示：

- from 来路的车道ID
- to 连接道路的车道ID

此时，当我们熟悉了这些元素以及他们的属性以后，我们也就自然而然的明白了OpenDRIVE中是怎样对交叉口进行描述的。

**注意**：

1. 连接道路不能作为来路。
2. 如果来路拥有驶离交叉口的车道，其也可被视作为去路。
3. 若一条连接道路有多个用于两条特定车道连接的<laneLink>元素，则在该交叉口中变道是可行的。
4. 在<junction>元素内，只给出道路以及车道的连接关系，而对于交叉口内车道的建模仍然是在<road>元素中（junction_id不等于-1）。

最后，对于一个交叉口也有交叉口内道路表面和交叉口组的描述，这里不再详细概述，有需要的话可以查看OpenDRIVE官方说明文档。

## 九、物体

这一节我没有太仔细看，因为暂时还用不到，下面只作简单的概述。

物体指的是通过拓展、定界以及补充道路走向从而对道路产生影响的项。最常见的例子是停车位、人行横道以及交通护栏。一般用物体的边界框来描述物体，对于常见的四边形物体，定义宽度、长度以及高度；对于常见的圆形物体，定义半径以及高度。

如下图所示：

![img](https://pic3.zhimg.com/80/v2-df1ddb95d8c4830a14346d45d423d1c6_720w.jpg)

在OpenDRIVE中，物体用<objects>元素中的<object>元素来表示，<objects>被定义在<road>元素中。关于<object>的属性这里就不再提了，当然对于一个物体，仍也有对物体轮廓、材质、物体的车道有效性等的描述。

如果你非常感兴趣，那么OpenDRIVE中还有对桥梁和隧道的描述。现在，我们不必深入 。

## **十、标志**

标志是指交通标志、交通灯以及为控制和规范道路交通所设的路标。

![img](https://pic2.zhimg.com/80/v2-d7fbd625520ae289c2611822a3af3abd_720w.jpg)

标志具备不同的功能和属性：

- 标志用于控制交通行为，例如限速和转弯限制。除此之外，它们还用于警示道路交通路上的危险情况。
- 它们可以是静态或动态的，例如停车标识这样的静态标志并不会改变其传递信息。而例如交通灯等动态标志可在仿真过程中改变其传递信息，它们的状态均可在OpenSCENARIO中得到定义。

标志默认为对一条道路上所有车道均有效，在OpenDRIVE中，使用<signal>元素里的<validity>元素来表示车道有效性，可以对一条或多条车道有效。

此外，简单说一下控制器的概念，控制器为一个或多个动态标志提供相同的状态，是标志组行为的包裹容器。控制器用于对高速公路上的动态速度以及交通灯切换相位进行控制。控制器的附加内容（如交通灯相位）存储在OpenDRIVE文件之外。在OpenDRIVE中，控制器用<controller>元素来表示。

![img](https://pic1.zhimg.com/80/v2-bf8fe14dc8ebf13c0d9fd3abe14ba61c_720w.jpg)

其中

- SignalId 被控制标志的ID
- type 控制类型，根据应用而定的自由文本

## 十一、铁路

OpenDRIVE中还给出了铁路的描述，这部分离我更加遥远了，所以，感兴趣的小伙伴可以自己去看这一节

![img](https://pic1.zhimg.com/80/v2-21a3dfa6a37e642746cf9d35ff1160e8_720w.jpg)

------

**最后，还是那句话，我们这几篇文章的目的是极简的。此时，你应当能够看懂一份OpenDRIVE格式的地图文件了，如果有不懂的元素或属性，你也应当知道借助官方说明文档得知他们的定义或描述。**

下面是两份LGSVL中使用到的OpenDRIVE格式的地图，分别是borregasave和sanfrancisco。我非常建议你下载下来查看，以此来更好的加深对OpenDRIVE的理解。

[https://content.lgsvlsimulator.com/maps/borregasave.xodrcontent.lgsvlsimulator.com/maps/borregasave.xodr](https://link.zhihu.com/?target=https%3A//content.lgsvlsimulator.com/maps/borregasave.xodr)

[https://content.lgsvlsimulator.com/maps/sanfrancisco.xodrcontent.lgsvlsimulator.com/maps/sanfrancisco.xodr](https://link.zhihu.com/?target=https%3A//content.lgsvlsimulator.com/maps/sanfrancisco.xodr)

------

如果你能看到这里的话， 我已经默认了你把这三篇文章都看完了~

那么，俗话说光看是不行的，要动手实践，所以我自己因为毕设科研需要，加上也没有搜到有人做这项工作，

这段时间写了一份初版的**OpenDRIVE格式地图解析数据库**，目前已经可以完成常用的地图查询API，包括但不限于：Frenet坐标和笛卡尔坐标的转换、车道中心线采样点获得、车道边界线采样点获取、车道信息查询、车道限速查询、车道是否在交叉口内、简单版本的Astar寻路算法等等。

下面是一些解析完之后的效果图

![img](https://pic1.zhimg.com/80/v2-7166e176b91127623df711987477c0e4_720w.jpg)

Sanfrancisco路网



![img](https://pic1.zhimg.com/80/v2-e6862bb2016aeb29b926fe1357ca55e8_720w.jpg)

Borregasave路网-蓝色是车道-红色是道路中心线

![img](https://pic2.zhimg.com/80/v2-9e69723d826f76891ddf584184377145_720w.jpg)

Astar算法寻找到的路由车道-两个绿色的点是起点和终点（一）

![img](https://pic4.zhimg.com/80/v2-cc183981c7f2a5a88ebc86965d142013_720w.jpg)

Astar算法寻找到的路由车道-两个绿色的点是起点和终点（二)