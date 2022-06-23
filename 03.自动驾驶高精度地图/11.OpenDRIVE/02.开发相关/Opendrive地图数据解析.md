- [Opendrive地图数据解析 - 简书 (jianshu.com)](https://www.jianshu.com/p/a362758da9c8)

# 1.前言

高精度电子地图也称为高分辨率地图(HD Map，High Definition Map)，是一种专门为无人驾驶服务的地图。与传统导航地图不同的是，高精度地图除了能提供的道路(Road)级别的导航信息外，还能够提供车道(Lane)级别的导航信息。无论是在信息的丰富度还是信息的精度方面，都是远远高于传统导航地图的。 目前市面上提供高精度地图的厂商有：tomtom、here、百度、高德等。 高精度地图流行的格式有很多种，有的厂商直接基于rndf地图增加属性来制作高精度地图，也有厂商使用osm格式增加属性来制作高精度地图。 对于ADAS系统，则有ADASIS定义了地图的数据模型及传输方式，以CAN作为传输通道。 OpenDRIVE是一种开放的文件格式, 用于路网的逻辑描述，常用于高精度地图的制作，百度Apollo则使用基于OpenDRIVE格式改进过的高精度地图。

本文主要对OpenDRIVE文件格式进行简述，详情可参考：[http://www.opendrive.org/docs/OpenDRIVEFormatSpecDelta_1.5M_vs_1.4H.pdf。](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.opendrive.org%2Fdocs%2FOpenDRIVEFormatSpecDelta_1.5M_vs_1.4H.pdf%E3%80%82)

# 2.正文

OpenDRIVE文件格式为XML，该XML文件种包含了很多地图信息，如Road、Junction、station等。

![img](https:////upload-images.jianshu.io/upload_images/15863171-ebca56d8e5ad3731.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/803/format/webp)

example

主要结构如下：



```ruby
 OpenDRIVE 
|-header 
| |-geoReference 
| |-offset 
|-road  
| |-link 
| | |-predecessor 
| | |-successor 
| | |-neighbor 
| |-type 
| | |-speed 
| |-planView 
| | |-geometry | | | |-line 
| | | |-spiral 
| | | |-arc 
| | | |-poly3 
| | | |-paramPoly3 
| |-elevationProfile 
| | |-elevation 
| |-lateralProfile 
| | |-superelevation 
| | |-crossfall 
| | |-shape 
| |-lanes 
| | |-laneOffset 
| | |-laneSection 
| | | |-left 
| | | | |-lane 
| | | | | |-link 
| | | | | | |-predecessor 
| | | | | | |-successor 
| | | | | |-width 
| | | | | |-border 
| | | | | |-roadMark 
| | | | | | | -sway 
| | | | | | | -type 
| | | | | | | | -line 
| | | | | | | -explicit 
| | | | | | | | -line 
| | | | | |-material 
| | | | | |-visibility 
| | | | | |-speed 
| | | | | |-access 
| | | | | |-height 
| | | | | |-rule 
| | | |-center 
| | | | |-lane 
| | | | | |-link 
| | | | | | |-predecessor 
| | | | | | |-successor
| | | | | | |-predecessor 
| | | | | | |-successor 
| | | | | |-roadMark 
| | | | | | | -sway 
| | | | | | | -type 
| | | | | | | | -line 
| | | | | | | -explicit 
| | | | | | | | -line 
| | | |-right 
| | | | |-lane 
| | | | | |-link 
| | | | | | |-predecessor 
| | | | | | |-successor 
| | | | | |-width 
| | | | | |-border 
| | | | | |-roadMark 
| | | | | | | -sway 
| | | | | | | -type 
| | | | | | | | -line 
| | | | | | | -explicit 
| | | | | | | | -line 
| | | | | |-material 
| | | | | |-visibility 
| | | | | |-speed 
| | | | | |-access 
| | | | | |-height 
| | | | | |-rule 
| |-objects 
| | |-object 
| | | |-repeat 
| | | |-outlines 
| | | | |-outline 
| | | | | |-cornerRoad 
| | | | | |-cornerLocal 
| | | |-material 
| | | |-validity 
| | | |-parkingSpace 
| | | |-markings 
| | | | |-marking 
| | | | | |-cornerReference 
| | | |-borders 
| | | | |-border 
| | | | | |-cornerReference 
| | |-objectReference 
| | | |-validity | | |-tunnel 
| | | |-validity | | |-bridge 
| | | |-validity 
| |-signals 
| | |-signal 
| | | |-validity 
| | | |-dependency 
| | | |-reference 
| | | |-positionRoad 
| | | |-positionInertial 
| | |-signalReference 
| | | |-validity 
| |-surface 
| | |-CRG 
| |-railroad 
| | |-switch 
| | | |-mainTrack 
| | | |-sideTrack 
| | | |-partner 
|-controller 
| |-control 
|-junction 
| |-connection 
| | |-predecessor 
| | |-successor 
| | |-laneLink | |-priority 
| |-controller 
| |-surface 
| | |-CRG 
|-junctionGroup 
| |-junctionReference 
|-station 
| |-platform 
| | |-segment 
```

## A.坐标系

最先考虑的应该时坐标系的表达方式，在GIS中一般使用两种常用的坐标系类型：

- 全局坐标系或球坐标系，例如经纬度。这些坐标系通常称为地理坐标系。(GCS)

![img](https:////upload-images.jianshu.io/upload_images/15863171-a07130d806719646.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/640/format/webp)

wgs

- 基于横轴墨卡托、亚尔勃斯等积或罗宾森等地图投影的投影坐标系，这些地图投影（以及其他多种地图投影模型）提供了各种机制将地球球面的地图投影到二维笛卡尔坐标平面上。(PCS)

![img](https:////upload-images.jianshu.io/upload_images/15863171-10386c482350a427.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/636/format/webp)

utm

在OpenDRIVE中可表示为：



```xml
<geoReference>
           <![CDATA[+proj=utm +zone=32 +ellps=WGS84 +datum=WGS84 +units= m+no_defs]]> 
</geoReference> 
```

从OpenDRIVE 1.4 开始, 可以使用格式化为 “proj4”-字符串的投影定义对路网进行地理参照转化. [PROJ 是一种通用坐标变换软件](https://links.jianshu.com/go?to=https%3A%2F%2Fproj.org%2F), 它将地理空间坐标从一个坐标参考系统 (CRS) 转换为另一个坐标参考系统。这包括制图投影和大地测量转换。 geoReference元素定义了该文件使用的投影坐标系，其中地理坐标系为WGS-84。

在OpenDRIVE数据中大量使用的位置信息都是投影后的xy坐标，而除了该投影坐标系，还定义了一种轨迹坐标系，如下所示，s坐标是沿着reference line的，关于reference line后面介绍，长度是在xy坐标下计算的。 t坐标，是相对于reference line的侧向位置，左正，右负。

![img](https:////upload-images.jianshu.io/upload_images/15863171-abe82c8b34a066a3.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/1039/format/webp)

cs

## B.Road Layout

OpenDRIVE中路网结构中的一个road，该road有三部分组成，蓝色的reference line，车道lane，车道lane的其他feature(限速等)。

所有道路都由一条参照线组成, 用于定义基本几何 (弧线、直线等)。沿着参考线, 可以定义道路的各种属性。这些是, 例如海拔概况、车道、交通标志等。道路可以直接连接 (当两个给定的道路之间只有一个连接时), 也可以通过路口 (当从某一道路到其他道路有一个以上的连接时)。

所有属性都可以根据本规范中规定的标准进行参数化, 也可以通过用户定义的数据进行参数化。

## C.Reference Line

整个地图路网由很多的road构成，而每个road中都会包含reference line，就是一条线，它没有宽度。 reference line，线条有好几种类型，直线，螺旋线等， The geometry of the reference line is described as a sequence of primitives of various types. The available primitives are:

straight line (constant zero curvature) spiral (linear change of curvature) curve (constant non-zero curvature along run-length) cubic polynom parametric cubic curves

下图为几种常见的reference line，注意图中的两个坐标系,xy和st

![img](https:////upload-images.jianshu.io/upload_images/15863171-3fdadd6cbf03fcea.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/907/format/webp)

rrl

## D.Lane

车道是由数字识别的, 这些数字是唯一的 (每个车道部分, 见下文) - 顺序 (即没有缝隙), - 从参考线上的0开始 - 向左上升 (正 t 方向) - 向右下降 (负 t 方向)

车道总数不受限制。参考线本身被定义为车道零, 不能有宽度条目 (即其宽度必须始终为 0.0)。

![img](https:////upload-images.jianshu.io/upload_images/15863171-0a7e566108a94ef0.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/928/format/webp)

l

## E.Road Linkage

road之间的连接定义了两种(每个road有唯一的ID)，一种是有明确的连接关系，例如前后只有一条road，那么通过 successor/predecessor进行连接(例如下图中的road 1和road 2)。

![img](https:////upload-images.jianshu.io/upload_images/15863171-c5aa0f215abf86e9.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/1158/format/webp)

R

# 3.总结

总之，对于一个road来说，先确定reference line，有了reference line的几何形状和位置，然后再确定reference line左右的车道lane,车道lane又有实线和虚线等属性；road 和road之间通过普通连接和Junction进行连接，同时还要将road中的相关车道进行连接

参考文献：[https://ryanadex.github.io/2019/06/04/opendrive/](https://links.jianshu.com/go?to=https%3A%2F%2Fryanadex.github.io%2F2019%2F06%2F04%2Fopendrive%2F)