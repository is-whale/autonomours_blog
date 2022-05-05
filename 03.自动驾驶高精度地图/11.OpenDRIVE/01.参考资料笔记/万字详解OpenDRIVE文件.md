- [opendrive简介_whuzhang16的博客-CSDN博客_opendrive](https://blog.csdn.net/whuzhang16/article/details/110198356)
- [一文读懂opendrive的xodr文件内容_布拉德先生的博客-CSDN博客_xodr格式](https://blog.csdn.net/weixin_44108388/article/details/111303985?ops_request_misc=%7B%22request%5Fid%22%3A%22164718048216780255286689%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fblog.%22%7D&request_id=164718048216780255286689&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-6-111303985.nonecase&utm_term=OpenDrive&spm=1018.2226.3001.4450)
- [自动驾驶场景仿真标准（一）- OpenDRIVE - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/352422886)
- [opendrive坐标系_whuzhang16的博客-CSDN博客_opendrive坐标系](https://blog.csdn.net/whuzhang16/article/details/110388309)

## 1 OpenDRIVE概要

ASAM OpenDRIVE描述了**自动驾驶仿真应用**所需的静态道路交通网络，并提供了标准交换格式说明文档。该标准的主要任务是对道路及道路上的物体进行描述。OpenDRIVE说明文档涵盖对如**道路、车道、交叉路口**等内容进行建模的描述，但其中并不包含动态内容。

OpenDRIVE格式使用文件拓展名为xodr的可扩展标记语言（XML）作为描述路网的基础。存储在OpenDRIVE文件中的数据描述了道路的几何形状以及可影响路网逻辑的相关特征(features)，例如车道和标志。

OpenDRIVE中描述的路网可以是人工生成或来自于真实世界的。OpenDRIVE的主要目的是**提供可用于仿真的路网描述，并使这些路网描述之间可以进行交换。**

该格式将通过节点(nodes)而被构建，用户可通过自定义的数据扩展节点。这使得各类应用（通常为仿真）具有高度的针对性，同时还保证不同应用之间在交换数据时所需的互通性。

### 1.1 OpenDRIVE文件结构

OpenDRIVE数据存储在扩展名为.xodr的XML文件中。OpenDRIVE文件结构符合XML规则。元素按XML格式的等级进行排列，级别大于零（0）的元素为 子元素。级别为（1）的元素称为主元素。每个元素都可以用户定义的数据进行扩展。每个OpenDRIVE文件都会有一个主元素`<OpenDRIVE>`，所有描述道路的特征类都是它的子元素。

OpenDRIVE中使用的所有浮点数都是IEEE 754双精度浮点数的数字。为了保证浮点数字在XML中的准确表示，在XML中的浮点数表示应该使用一个已知的正确精度，一般使用保留17位有效数字来描述数字。

所有可以在 OpenDRIVE 文件中使用的属性都在 UML 模型中被完全注释：

- 单位： 道路长度或速度等的单位
- 种类： 描述一个属性的数据类型， 可以是一个原始数据类型，例如，string、double、float，或者是指代对象的复杂数据类型。
- 值： 值决定了给定属性的值范围，例如：

```xml
<geometry s="4.9957524872074799e+02" x="4.9469346060416666e+02" y="5.3447643627860181e+01" hdg="5.8804473418180125e-02" length="6.2079164697363019e+01"> <line/> </geometry>
```

其中geometry代表了当前元素所要描述的道路单元，其中geometry的属性有s，x，y，hdg和length，他们的值跟随在后面。

### 1.2 OpenDrive重要节点介绍

> [Unity解析OpenDRIVE地图数据，并生成路网模型_方寸想法，编码宇宙-CSDN博客_opendrive unity](https://blog.csdn.net/qq_36622009/article/details/107006508?ops_request_misc=&request_id=&biz_id=102&utm_term=OpenDrive&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~sobaiduweb~default-6-107006508.nonecase&spm=1018.2226.3001.4450)

XML节点和属性的导图。“【】”表示这个节点一般有多个。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200628195626147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NjIyMDA5,size_16,color_FFFFFF,t_70#pic_center)

## 2 格式说明

范例：

```xml
<?xml version="1.0"?>
<OpenDRIVE>
...
</OpenDRIVE>
```

OpenDRIVE的例子可以参考如下的xodr文件：

[.xord示例文件github.com/ruomusim/Intro_OpenDRIVE](https://link.zhihu.com/?target=https%3A//github.com/ruomusim/Intro_OpenDRIVE)

### 2.1 header 描述文件整体属性 （仅一个）

`<header>`元素是 `<OpenDRIVE>`中的第一个元素。

注意：instances 意思是实例数，而该实例数要求是1。

说明：

1. revMajor.revMinor就是最终的版本号，比如1.6；

2. north，south，east，west指的是惯性坐标系下的东南西北方向的位置坐标值，也就是惯性坐标系下对应的x，y最大最小值等。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216215246449.PNG)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216215246519.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216215246538.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70)

示例：

```xml
<header date="XXXX日期" west="-10" revMinor="2" name="XXX名称" revMajor="1" east="10" north="10" version="1" south="-10"/>
```

### 2.2 road 描述道路属性 （可多个）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216215351340.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)
完整道路的表示方式：

①道路参考线 —— `<planView>`

②一条道路上的单独车道 ——

```xml
<lanes> 
	<laneSection> 
		<center/left/right> 
			 <lane> 
		 	 	<width/...> 
```

③沿道路放置的道路特征（如标志）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216215427535.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)
解释：

1. length就是所有路径geometry长度的累加

2. junction：交叉口的ID，道路作为联接道路属于该交叉口（无（none）使用= -1）

3. 使用道路的基本规则； RHT =靠右行车，LHT =靠左行车。当缺少此属性时，将假定为RHT。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216215449635.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216215449636.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70)
示例：

```xml
<road length="53" id="0" name="XXX名称" junction="-1">
```

以及

```xml
<road id="3" name="prototype" length="5" junction="0">
```

#### 2.2.1 link

```
t_road_link
```

在OpenDRIVE中，道路连接用 `<road>` 元素里的 `<link>` 元素来表示。 `<predecessor>` 以及 `<successor>` 元素在 `<link>` 元素中被定义。对于虚拟和常规的交叉口来说， `<predecessor>` 以及 `<successor>` 元素必须使用（shall）不同的属性组。

属性：

![](https://img-blog.csdnimg.cn/2020121621552491.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

##### 2.2.1.1 Successor

前驱

```
t_road_link_predecessorSuccessor
```

- 必须（shall）将不同属性用于虚拟以及常规的交叉口。

- @contactPoint须（shall）用于常规交叉口；@elementS 和 @elementDir则须（shall）用于虚拟交叉口。

**只有在连接（linkage）清晰的情况下，才能（shall）直接连接两条道路。如果与前驱或后继的关系模糊，则必须（shall）使用交叉口。**

示例：

```xml
<link>
    <predecessor elementId="0" elementType="junction"/>
</link>
```

以及

```xml
<link>
     <predecessor elementId="0" contactPoint="end" elementType="road"/>
     <successor elementId="1" contactPoint="start" elementType="road"/>
</link>
```

##### 2.2.1.2 predecessor

后继

```
t_road_link_predecessorSuccessor
```

- 必须（shall）将不同属性用于虚拟以及常规的交叉口。

- @contactPoint须（shall）用于常规交叉口；@elementS 和 @elementDir则须（shall）用于虚拟交叉口。

#### 2.2.2 planView

`<planView>` 元素是每个 `<road>` 元素里必须要用到的元素。

##### 2.2.2.1 planView -> geometry

在OpenDRIVE中，参考线的几何形状用`<planView>`元素里的 `<geometry>` 元素来表示。

通用属性（对于不同类型的参考线形式，比如螺旋线、样条曲线等，可能有新增的附属属性，通用属性写在`<geometry>`标签里，而新增的附属属性在`<geometry>`标签下再写一层）：

![](https://img-blog.csdnimg.cn/20201216215557491.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

以下规则适用于道路参考线：

1. 每条道路必须（shall）有一条参考线。
2. 每条道路只能（shall）有一条参考线。
3. 参考线通常在道路中心，但也可能（may）有侧向偏移。
4. 几何元素应（shall）沿参考线以升序（即递增的s位置）排列。
5. 一个 元素应（shall）只包含一个另外说明道路几何形状的元素。
6. 若两条道路不使用交叉口来连接，那么新的道路的参考线应（shall）总是起始于其前驱或后继道路的 。参考线有可能（may）被指向相反方向。
7. 参考线不能（shall）有断口（leaps）。
8. 参考线不应（should）有扭结（kinks）。

分类：

1. 直线
2. 螺旋线
3. 线
4. 直线
5. 螺旋线或回旋曲线（曲率以线性方式改变）
6. 有恒定曲率的弧线
7. 三次多项式曲线
8. 参数三次多项式曲线

![](https://img-blog.csdnimg.cn/20201216215620393.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

1. 直线

在OpenDRIVE中，直线用`<geometry>` 元素里的 `<line>` 元素来表示。

示例：

```xml
<planView>
    <geometry
              s="0.0000000000000000e+00"
              x="-4"
              y="7"
              hdg="6"
              length="5">
        <line/>
    </geometry>
</planView>
```

2. 螺旋线Spiral

`t_road_planView_geometry_spiral`里描述了层次关系，先是`road`，然后`planView`是`road`的下一层，同理，`geometry`和`spiral`。

螺旋线是以起始位置的曲率(@curvStart)和结束位置的曲率(@curvEnd)为特征。沿着螺旋线的弧形长度（见 `<geometry>` 元素@length），曲率从头至尾呈线性。

在OpenDRIVE中，螺旋线用`<geometry>` 元素里的`<spiral>`元素来表示。

属性：
![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-65iItsbR-1608126737953)()]](https://img-blog.csdnimg.cn/20201216215640645.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

示例：

注意：曲率：

正曲率：左曲线（逆时针运动）

负曲率：右曲线（顺时针运动）

```xml
<geometry s="100.0" x="38.00" y="-1.81" hdg="0.33" length="30.00">
    <spiral curvStart="0.0" curvEnd="0.013"/>
</geometry>
```

3.弧线

弧线描述了有着恒定曲率的道路参考线。

在OpenDRIVE中，弧线用`<geometry>` 元素里的`<arc>`元素来表示。

属性：
![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-GLFs4Mxz-1608126737953)()]](https://img-blog.csdnimg.cn/20201216215656694.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

示例：

注意：曲率：

正曲率：左曲线（逆时针运动）

负曲率：右曲线（顺时针运动）

```xml
<planView>
    <geometry
              s="3"
              x="-4"
              y="4"
              hdg="5"
              length="9">
        <arc curvature="-1.2e-01"/>
    </geometry>
</planView>
```

4. 组合曲线，连接处

通过对OpenDRIVE中所有可用的几何形状元素进行组合，便可以创建诸多种类的道路线。

示意图：

![](https://img-blog.csdnimg.cn/20201216215723247.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

示例：

写上连接的路的属性：

这个示例是用圆弧和直线相连，注意后者的起始弧长s处就是前者的起始弧长s加上其长度。

```xml
<planView>
    <geometry x="278" y="-828" hdg="0.50" s="0" length="34">
        <arc curvature="0.06"/>
    </geometry>
    <geometry x="2" y="-51" hdg="2.9" s="39.2" length="4.2">
        <line/>
    </geometry>
</planView>
```

5.三次多项式（弃用）

```
t_road_planView_geometry_poly3
```

三次多项式可（may）用来生成衍生于测量数据的复杂道路走向。测量对为x/y坐标系中沿参考线的被测量坐标的指定次序定义了线段的多项式极限。

**局部**三次多项式描述了道路的参考线。通过对线段极限处的连续性条件例如线段连续性、切线和/或曲率连续性等进行详细说明，可以对多个三次多项式线段进行融合并且为整个道路走向生成一条全局三次样条线插值曲线。另一个优点则是，沿着多项式的路径方式比沿回旋曲线更有效。

**局部**三次多项式表达式：

```
v(u) = a + b*u + c*u2 + d*u³
```

不能用全局三次多项式，原因：
![](https://img-blog.csdnimg.cn/20201216215745320.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

而全局坐标系和局部坐标系之间的转换：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216215805686.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216215805478.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70)

在OpenDRIVE中，三次多项式用 `<geometry>` 元素里的 `<poly3>` 元素来表示。

属性：
![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-gH54co3F-1608126737955)()]](https://img-blog.csdnimg.cn/20201216215836855.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

要求：

`<geometry>`元素的起始点(@x,@y)定位在局部u/v坐标系的v轴上。

示例：

```xml
<geometry
          s="0.0000000000000000e+00"
          x="-6.8858131487889267e+01"
          y="4.1522491349480972e-01"
          hdg="6.5004409066736524e-01"
          length="2.5615689718113455e+01">
    <poly3
           a="0.0000000000000000e+00"
           b="0.0000000000000000e+00"
           c="1.4658732624442020e-02"
           d="-5.7746497381565959e-04"/>
</geometry>
```

6.参数三次曲线

```
t_road_planView_geometry_paramPoly3
```

只需使用x轴和y轴便可以用参数三次曲线生成道路线。为保持三次多项式的连贯性，可（may）利用u轴和v轴同时将它们计算到三次多项式里。

**参数**三次曲线表达式：

```xml
u(p) = aU + bU*p + cU*p2 + dU*p³
v(p) = aV + bV*p + cV*p2 + dV*p³
```

示意图：

`u(p) = aU + bU*p + cU*p2 + dU*p³`:

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-K6p56DP1-1608126737956)()]](https://img-blog.csdnimg.cn/2020121621591080.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

`v(p) = aV + bV*p + cV*p2 + dV*p³`:

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-5mHFw6Ea-1608126737956)()]](https://img-blog.csdnimg.cn/20201216215923538.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

```
u(p) = aU + bU*p + cU*p2 + dU*p³`
`v(p) = aV + bV*p + cV*p2 + dV*p³
```

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-jnwtOE7P-1608126737957)()]](https://img-blog.csdnimg.cn/20201216215939232.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

在OpenDRIVE中，参数三次曲线用 `<geometry>` 元素里的 `<paramPoly3>` 元素来表示。

要求：

- 若@pRange=“arcLength”，那么p可（may）在[0, @length from ]范围内对其赋值。

- 若@pRange=“normalized”，那么p可（may）在[0, 1]范围内对其赋值。

属性：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020121622000094.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216220000199.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70)
示例：

```xml
<planView>
    <geometry
              s="0"
              x="6.8"
              y="5.4"
              hdg="5.2"
              length="6.56">
        <paramPoly3
                    aU="0"
                    bU="1.0"
                    cU="-4.66e-09"
                    dU="-2.62e-08"
                    aV="0.00e+00"
                    bV="1.66e-16"
                    cV="-1.98e-04"
                    dV="-1.31e-09"
                    pRange="arcLength">
        </paramPoly3>
    </geometry>
</planView>
```

#### 2.2.3 elevationProfile

##### 2.2.3.1 elevationProfile -> elevation

```
t_road_elevationProfile_elevation
```

纵向。

在OpenDRIVE中，高程Road elevation 用 `<elevationProfile>` 元素中的 `<elevation>` 元素来表示。

该属性定义了参考线上给定点处的高程元素。此外，必须（shall）沿参考线按升序对元素进行定义。s的长度不随高程而改变。
![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-HqC7MZTk-1608126737958)()]](https://img-blog.csdnimg.cn/20201216220020170.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

表达式：

```
elev(ds) = a + b*ds + c*ds² + d*ds³
```

示例：

```xml
<elevationProfile>
    <elevation b="1.2" s="0" c="0.175" d="-0.0175" a="0"/>
    <elevation b="-0.5499999999999989" s="10" c="-0.5800000000000002" d="0.04050000000000001" a="12"/>
    <elevation b="0" s="20" c="0" d="0" a="-11"/>
</elevationProfile>
```

#### 2.2.4 lateralProfile

##### 2.2.4.1 lateralProfile -> superelevation

```
t_road_lateralProfile_superelevation
```

在OpenDRIVE中，超高程用`<lateralProfile>`元素中的 `<superelevation>` 元素来表示。

横向。

超高程从数学角度被定义为围绕参考线的道路横截面的倾斜角。这意味着超高程对于向右边倾斜的道路具有正值，对于向左边倾斜的道路具有负值。

该属性被定义为围绕着s轴的路段**倾斜角**。必须（must）沿参考线按升序定义元素。元素的参数将持续有效，直到下一个元素开始或道路参考线结束。道路的超高程程默认为零。

给定位置的超高程表达式：

```
sElev (ds) = a + b*ds + c*ds2 + d*ds3
```

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-LVSIK8y2-1608126737959)(images/超高程属性.png)]](https://img-blog.csdnimg.cn/20201216220043696.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

示例：

```xml
<lateralProfile>
    <superelevation b="0" s="0" c="0" d="0" a="0"/>
    <crossfall b="0" s="0" side="both" c="0" d="0" a="0"/>
</lateralProfile>
```

##### 2.2.4.2 lateralProfile -> shape

```
t_road_lateralProfile_shape
```

该属性被定义为相对于参考水平面路段的路面。一个拥有不同t值的s位置上可（may）存在多个形状，从而对道路的弯曲形状进行描述。

给定位置参考平面上方的高度表达式：

```
hShape (ds)= a + b*dt + c*dt2 + d*dt3
```

#### 2.2.5 lanes

在OpenDRIVE中，所有道路都包含了车道。每条道路必须（shall）拥有至少一条宽度大于0的车道，并且每条道路的车道数量不受限制。

需要使用中心车道对OpenDRIVE中的车道进行定义和描述。中心车道没有宽度，并被用作车道编号的参考，自身的车道编号为0。对其他车道的编号以中心车道为出发点：车道编号向右呈降序，也就是朝负t方向；向左呈升序，也就是朝正t方向。

在OpenDRIVE中，车道用 `<road>` 元素里的 `<lanes>` 元素来表示。

示例：

```xml
<lanes>
    <laneSection s="0.0">
        <left>
            <lane id="2" type="border" level="false">
                <link>
                </link>
                <width sOffset="0.0" a="1.0" b="0.0" c="0.0" d="0.0"/>
            </lane>
            <lane id="1" type="driving" level="false">
                <link>
                </link>
                <width sOffset="0.0" a="4.0" b="0.0" c="0.0" d="0.0"/>
            </lane>
        </left>
        <center>
            <lane id="0" type="none" level="false">
                <link>
                </link>
            </lane>
        </center>
        <right>
            <lane id="-1" type="driving" level="false">
                <link>
                </link>
                <width sOffset="0.0" a="4.0" b="0.0" c="0.0" d="0.0"/>
            </lane>
            <lane id="-2" type="border" level="false">
                <link>
                </link>
                <width sOffset="0.0" a="1.0" b="0.0" c="0.0" d="0.0"/>
            </lane>
        </right>
    </laneSection>
</lanes>
```

##### 2.2.5.1 Lanes -> laneSection

```
t_road_lanes_laneSection
```

在OpenDRIVE中，车道组用 `<laneSection>` 元素内的 `<center>` 、 `<right>` 和 `<left>` 元素来表示。ID属性用嵌套在 `<center>` 、`<right>` 和 `<left>` 元素里的 `<lane>` 元素来定义。

在OpenDRIVE中 ，车道段用 `<lanes>` 元素里的 `<laneSection>` 元素来表示。

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-OOieIgXk-1608126737961)()]](https://img-blog.csdnimg.cn/20201216220211214.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

示例：

```xml
<laneSection s="0" singleSide="false">
...
</laneSection>
```

##### 2.2.5.2 Lanes -> laneSection -> center

```
t_road_lanes_laneSection_center
```

###### 2.2.5.2.1 Lanes -> laneSection -> center -> lane

```
t_road_lanes_laneSection_center_lane
```

车道元素被包括在左/中/右元素中。车道元素必须（should）使用降序ID从左到右展示车道。

lane属性：level, type

**level**: 0 —— 不采用超高程；1 —— 采用超高程。

**type**: 每条车道都会被定义一个类型。车道类型定义了车道的主要用途及与其相对应的交通规则。

属性：
![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-g6Yf8ewI-1608126737962)()]](https://img-blog.csdnimg.cn/2020121622023391.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

包括：

- 路肩shoulder：描述了道路边缘的软边界。

 -边界border：描述了道路边缘的硬边界。其与正常可供行驶的车道拥有同样高度。

- 驾驶driving：描述了一条“正常”可供行驶、不属于其他类型的道路。

- 停stop：高速公路的硬路肩，用于紧急停车。

- 无none：描述了道路最远边缘处的空间，并无实际内容。其唯一用途是在（人类）驾驶员离开道路的情况下，让应用记录OpenDRIVE仍在运行。

- 限制restricted：描述了不应有车辆在上面行驶的车道。该车道与行车道拥有相同高度。通常会使用实线以及虚线来隔开这类车道。

- 泊车parking：描述了带停车位的车道。

- 分隔带median：描述了位于不同方向车道间的车道。在城市中通常用来分隔大型道路上不同方向的交通。

- 自行车道biking：描述了专为骑自行车者保留的车道。

- 人行道sidewalk：描述了允许行人在上面行走的道路。

- 路缘curb：描述了路缘石。路缘石与相邻的行车道在高度有所不同。

- 出口exit：描述了用于平行于主路路段的车道。主要用于减速。

- 入口entry：描述了用于平行于主路路段的车道。主要用于加速。

- 加速车道onramp：由乡村或城市道路引向高速公路的匝道。

- 减速车道offRamp：驶出高速公路，驶向乡村或城市道路所需的匝道。

- 连接匝道connectingRamp：连接两条高速公路的匝道。例如高速公路路口。

高速公路的车道类型：

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-JnYZcISw-1608126737962)()]](https://img-blog.csdnimg.cn/2020121622025958.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

在OpenDRIVE中，车道类型用`<lane>`元素内属性@type元素来表示。

示例：

```xml
<lane level="0" id="0" type="border">
```

###### 2.2.5.2.2 Lanes -> laneSection -> center -> lane -> link

```
t_road_lanes_laneSection_lcr_lane_link
```

在OpenDRIVE中，车道连接用`<lane>`元素里的`<link>`元素来表示。`<predecessor>`和`<successor>`元素在`<link>`元素内得到定义。

###### 2.2.5.2.2.1 Lanes -> laneSection -> center -> lane -> link -> predecessor

```
t_road_lanes_laneSection_lcr_lane_link_predecessorSuccessor
```

`<predecessor>`和`<successor>`元素在`<link>`元素内得到定义。

要求：

一条车道可（may）拥有另外一条车道作为其前驱或后继。

只有当两条车道的连接明确时，它们才能（shall）被连接。若与前驱或后继部分的关系比较模糊，则必须（shall）使用交叉口。

若车道结束于一个路口内或没有任何连接，则必须（shall）删除 `<link>` 元素。

示例：

```xml
<lane level="0" id="-1" type="driving">
    <link>
        <predecessor id="-1"/>
        <successor id="-1"/>
    </link>
    ...
</lane>
```

###### 2.2.5.2.2.2 Lanes -> laneSection -> center -> lane -> width

```
t_road_lanes_laneSection_lr_lane_width
```

车道属性描述了车道的用途以及形状。每个车道段都定义了一条车道属性，该属性也可能（may）在该车道段中有变化。如果没有特意为车道段定义一条属性，应用便可（can）采用默认属性。

车道属性的示例是车道宽度、车道边界和限速。

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-fLab3NEH-1608126737962)()]](https://img-blog.csdnimg.cn/20201216220323880.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

车道的宽度是沿t坐标 (s坐标系下的横向偏移) 而定义的。车道的宽度有可能（may）在车道段内产生变化。

车道宽度与车道边界元素在相同的车道组内互相排斥。若宽度以及车道边界元素在OpenDRIVE文件中同时供车道段使用，那么应用必须（must）使用 `<width>` 元素提供的信息。

在OpenDRIVE中，车道宽度由 元素中的 `<width>` 元素来描述。

表达式：

```
Width (ds) = a + b*ds + c*ds² + d*ds³
```

示例：

```
<width b="0" sOffset="0" c="0" d="0" a="3.75"/>
```

###### 2.7.1.1.1.3 Lanes -> laneSection -> center -> lane -> speed

```
t_road_lanes_laneSection_lr_lane_speed
```

在OpenDRIVE中，车道速度用`<lane>`元素内的`<speed>`元素来表示。

该属性定义了给定车道上允许的最大行驶速度。直到新的元素得到定义，每个元素都在s坐标的增长方向中继续有效。

属性：
![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-1lduAPp8-1608126737963)()]](https://img-blog.csdnimg.cn/20201216220353189.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

示例：

```xml
<speed max="-1" sOffset="0" unit="m/s"/>
```

以及

```xml
<lane id="-1" type="driving" level="false">
    <link>
        <successor id="-3"/>
    </link>
    <width sOffset="0.0" a="2.0" b="0.0" c="0.0" d="0.0"/>
    <speed sOffset="0.0" max="80.0" unit="km/h"/>
    <height sOffset="0.0" inner="0.12" outer="0.12"/>
</lane>
```

###### 2.7.1.1.1.4 Lanes -> laneSection -> center -> lane -> border

车道边界是用来描述车道宽度的另一种方法，它并不会直接定义宽度，而是在独立于其内部边界参数的情况下，对车道的外部界限进行定义。根据上述情况，内车道也被定义为车道，该车道虽然与当前被定义的车道有着相同ID符号，但内车道的ID绝对值要更小。

相比较对宽度进行详细说明而言，此类定义要更加地便利。尤其是在道路数据是源自于自动测量结果的情况下，该方式可以避免多个车道段被创建。

车道宽度与车道边界元素在相同的车道组内互相排斥。若宽度以及车道边界元素在OpenDRIVE文件中同时供车道段使用，那么应用必须（must）使用 `<width>` 元素提供的信息。

在OpenDRIVE中，车道边界用 `<lane>`元素中的 `<border>` 元素来表示。

表达式：

```
tborder (ds) = a + b*ds + c*ds² + d*ds³
```

示例：

```xml
<lane level="0" id="0" type="border">
    <link/>
    <userdata value="0" code="frictionCoeff"/>
    <userdata value="0" code="adhesionCoeff"/>
    <width b="0" sOffset="0" c="0" d="0" a="0"/>
    <border b="0" sOffset="0" c="0" d="0" a="0"/>
</lane>
```

###### 2.7.1.1.1.5 Lanes -> laneSection -> center -> lane -> material

```
t_road_lanes_laneSection_lr_lane_material
```

在OpenDRIVE中，车道材质用`<lane>`元素内的`<material>`元素来表示。

属性：
![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-V3hB4MWY-1608126737963)()]](https://img-blog.csdnimg.cn/20201216220416681.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

###### 2.7.1.1.1.6 Lanes -> laneSection -> center -> lane -> access

```
t_road_lanes_laneSection_lr_lane_access
```

车道可（can）局限于特定的道路使用者，例如卡车或公共汽车。这类限制可（may）在道路标识描述的限制之上另外在OpenDRIVE中得到定义。

OpenDRIVE在 `<lane>` 元素内提供了 `<access>` 元素，以便描述车道使用规则。

属性：
![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-YUISQTaV-1608126737964)()]](https://img-blog.csdnimg.cn/20201216220428950.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

示例：

```xml
<lane id="2" type="driving" level="false">
    <link>
        <successor id="2"/>
    </link>
    <width sOffset="0.0" a="2.0" b="0.0" c="0.0" d="0.0"/>
    <access sOffset="0.0" rule="allow" restriction="bus"/>
</lane>
```

###### 2.7.1.1.1.7 Lanes -> laneSection -> center -> lane -> height

```
t_road_lanes_laneSection_lr_lane_height
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216220525735.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

在OpenDRIVE中，车道高度用`<lane>`元素内的`<height>`元素来表示。

属性：
![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-HL3lNbyY-1608126737965)()]](https://img-blog.csdnimg.cn/20201216220509586.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

示例：

```xml
<lane id="-2" type="sidewalk" level="false">
    <link>
        <successor id="-3"/>
    </link>
    <width sOffset="0.0" a="2.0" b="0.0" c="0.0" d="0.0"/>
    <height sOffset="0.0" inner="0.12" outer="0.12"/>
</lane>
```

###### 2.7.1.1.1.8 Lanes -> laneSection -> center -> lane -> roadMark

`t_road_lanes_laneSection_lcr_lane_roadMark`
该属性定义了车道**外边界线条**的样式。而分隔左右车道的中心线的样式由专为中心车道而设的路标元素来确定。

```
t_road_lanes_laneSection_lcr_lane_roadMark_type
t_road_lanes_laneSection_lcr_lane_roadMark_type_line
```

道路上的车道可（can）拥有不同的车道标识，比如不同颜色和样式的线。OpenDRIVE为路标提供了 `<roadMark>` 元素。路标信息定义了车道外边界上的线的样式，在左车道上则为左边界，在右车道则为右边界。而作为分隔左右车道的中心线的样式则由中心车道路标元素来确定。

可（may）为道路横断面内的每一条车道定义多个路标元素。也可（may）使用多个属性（如@type, @weight和 @width）来描述车道标志的属性。

有两种规定路标类型的方法：
(1) 通过`<roadMark>`元素内的@type属性可以输入存储在应用内的关键词。这些关键词被用于描述简化的路标类型如实线、虚线或草地。
(2) `<type>`元素包含了更多`<line>`元素，这些元素将对路标进行更详细的描述。

在OpenDRIVE中，路标用 `<lane>` 元素内的 `<roadMark>` 元素来表示。

属性：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216220549965.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216220549954.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216220549846.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70)

示例：

```xml
<roadMark color="standard" sOffset="0" weight="standard" laneChange="none" width="0.13" material="none" height="0.004999999888241291" type="solid"/>
```

###### 2.7.1.1.1.8.1 Lanes -> laneSection -> center -> lane -> roadMark -> sway

`t_road_lanes_laneSection_lcr_lane_roadMark_sway`
可（may）使用 `<sway>` 元素来描述非直线但有侧边曲线的车道标志。 `<sway>` 元素为以下的（显性）类型定义转移了横向参考位置，从而定义了一个偏移。横向偏移曲线（sway）偏移将相对于车道标识的名义参考位置，即车道边界。

横向曲线的主要应用案例为创建穿过施工现场的道路。行车道在黄线之间，白线被横向偏移（swayed），并只作为标志存在。
示意图：
![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-tNiESEG6-1608126737967)()]](https://img-blog.csdnimg.cn/20201216220610299.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

表达式：

```
tOffset (ds) = a + b*ds + c*ds² + d*ds³
```

##### 2.2.5.3 Lanes -> laneSection -> left

```
t_road_lanes_laneSection_left
```

为了能够便利地在OpenDRIVE的道路描述中进行查找，一个车道段内的车道可分为左、中和右车道。每个车道段均必须（shall）包含一个`<center>`元素和至少一个`<right>`或`<left>`元素。

###### 2.2.5.3.1 Lanes -> laneSection -> left -> lane

```
t_road_lanes_laneSection_left_lane
```

###### 2.2.5.3.2 Lanes -> laneSection -> right

```
t_road_lanes_laneSection_right
```

###### 2.2.5.3.3 Lanes -> laneSection -> right -> lane

```
t_road_lanes_laneSection_right_lane
```

##### 2.2.5.4 Lanes -> laneOffset

```
t_road_lanes_laneOffset
```

在OpenDRIVE中，车道偏移用`<lanes>`元素内的`<laneOffset>`元素来表示。

车道偏移可（may）用于将中心车道从道路参考线上位移。

表达式：

```
offset (ds) = a + b*ds + c*ds² + d*ds³
```

属性：
![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-S9dZGAU4-1608126737968)()]](https://img-blog.csdnimg.cn/20201216220629520.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

要求：
若边界定义已存在，则不允许（shall）出现偏移。

示例：

```xml
<lanes>
    <laneOffset s="25.0" a="0.0" b="0.0" c="3.9e-03" d="-5.2e-05"/>
    <laneOffset s="75.0" a="3.25" b="0.0" c="0.0" d="0.0"/>
    …
    <\lanes>
```

#### 2.2.6 type

```
t_road_type
```

Road type 道路类型

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-Wq3NTYeY-1608126737960)()]](https://img-blog.csdnimg.cn/20201216220101693.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

示例：

```xml
<type s="0" type="motorway"/>
```

##### 2.2.6.1 type -> speed

```
t_road_type_speed
```

在OpenDRIVE中，速度限制用 `<type>` 元素里的 `<speed>` 元素来表示。

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-wCSWnhs4-1608126737961)()]](https://img-blog.csdnimg.cn/20201216220116541.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

示例：

```xml
<speed max="-1" sOffset="0" unit="m/s"/>
```

#### 2.2.7 surface

```
t_road_surface_CRG
```

### 2.3 junction 描述交叉路口属性 （可多个）

```
t_junction
```

交叉口指的是三条或更多道路相聚的地方，与其相关的道路被分为两种类型：含有驶向交叉口车道的道路称为来路Incoming roads。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216220647952.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201216220646789.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70)

特点：

与道路不同，交叉口并不具备任何前驱或后继交叉口。

#### 2.3.1 connection

在OpenDRIVE中，交叉口用 `<junction>` 元素来表示。联接道路则用 `<junction>` 元素中的 `<connection>` 元素来表示。OpenDRIVE并未特意将去路定义为元素或属性，来路也可被视作为去路，因此二者在此处可被相提并论。通往该道路的联接道路将此类道路隐性地定义为去路。

为能详细说明一条作为来路的道路，将通过在 `<connection>` 元素中使用@incomingRoad属性来引用该道路的ID。

示意图：
![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-aXfuMjmV-1608126737969)()]](https://img-blog.csdnimg.cn/20201216220730885.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)
解释：

1. incomingRoad 表示连接的两条车道的进入交叉路口的车道（Incoming roads）的id

2. connectingRoad 表示交叉路口中间新增的车道（Connecting roads）（会同时在road里也更新一条路来，该路的`junction`属性不为-1）的id
   示例：

   ```xml
   <road length="64.18763436526746" id="20" name="prototype" junction="2">
   ```

3. contactPoint 表示联接道路的方向:
   "start"值必须（shall）用于标明联接道路正在沿`<laneLink>`元素中的连接延伸。
   "end"值必须（shall）用于标明联接道路正在沿`<laneLink>`元素中的连接的反方向延伸。

![[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-u7cxQ7wm-1608126737970)()]](https://img-blog.csdnimg.cn/2020121622085636.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

##### 2.3.1.1 connection -> laneLink

`t_junction_connection_laneLink`
该属性提供了关于在一条来路和一条联接道路之间被连接的车道信息。强烈建议使用该元素。忽略`<laneLink>`元素的做法已经不符合时宜。

联接道路将基于其车道对路线进行描述。联接道路详细说明了相同交叉口的来路和去路的车道之间的连接。如果该车道并没有被连接，就意味着这些车道之间的路线不通。

![[外链图片转存中...(img-T7AOxbYR-1608126737971)]](https://img-blog.csdnimg.cn/20201216221018959.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDEwODM4OA==,size_16,color_FFFFFF,t_70#pic_center)

示例：

```xml
<junction name="myJunction" id="555" >
    <connection id="0"
                incomingRoad="1"
                connectingRoad="2"
                contactPoint="start">
        <laneLink from="-2" to="-1"/>
    </connection>
</junction>
```

## 3 OpenDRIVE中的坐标系

OpenDRIVE使用三种类型的坐标系，如下图所示：

![img](https://img-blog.csdnimg.cn/img_convert/54ae66c0718bf4d5af6095ed0d525872.png)

- 惯性x/y/z坐标系
- 参考线s/t/h坐标系
- 局部u/v/z轴坐标系

若无另外说明，对局部坐标系的查找与定位将相对于参考线坐标系来进行。对参考线坐标系位置与方向的设定则相对于惯性坐标系来开展，具体方法为对原点、原点的航向角/偏航角、横摆角/翻滚角和俯仰角的旋转角度及它们之间的关系进行详细说明。

![img](https://img-blog.csdnimg.cn/20201130145409470.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dodXpoYW5nMTY=,size_16,color_FFFFFF,t_70)

### 3.1 惯性x/y/z坐标系

惯性坐标系描述的是地图中具体某个点在当前参考坐标系下的位置。其中x y z坐标可以具体表示某一个点所在的位置。如在描述某一段道路时，道路起始点的位置就是由x y z坐标定义。道路就是在这个点开始，按一定方向和一定长度延伸。

```xml
<geometry s="4.9957524872074799e+02" x="4.9469346060416666e+02" y="5.3447643627860181e+01" hdg="5.8804473418180125e-02" length="6.2079164697363019e+01"> <line/> </geometry>
```

在上述例子中，x="4.9469346060416666e+02" 和y="5.3447643627860181e+01"描述的是此段道路的起始点在惯性坐标系中的位置。

根据[ISO](https://so.csdn.net/so/search?q=ISO&spm=1001.2101.3001.7020) 8855惯性坐标系是右手坐标系，其轴的指向方向如下（见图7）：

- x轴 ⇒ 右方
- y轴 ⇒ 上方
- z轴 ⇒ 指向绘图平面外

以下惯例适用于地理参考：

- x轴 ⇒ 东边
- y轴 ⇒ 北边
- z轴 ⇒ 上方

通过依次设置航向角/偏航角（heading）、俯仰角（pitch）和横摆角/翻滚角（roll），元素（如物体、标志等）可被置于惯性坐标系中：

![img](https://img-blog.csdnimg.cn/20201130145627958.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dodXpoYW5nMTY=,size_16,color_FFFFFF,t_70)

图7展示了对应角的正轴与正方向。

![img](https://img-blog.csdnimg.cn/20201130145754428.png)

![img](https://img-blog.csdnimg.cn/20201130145808123.png)

![img](https://img-blog.csdnimg.cn/20201130145827492.png)

![img](https://img-blog.csdnimg.cn/20201130145846727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dodXpoYW5nMTY=,size_16,color_FFFFFF,t_70)

x’/y’/(z’=z) 指的是以航向角/偏航角围绕z轴旋转x/y/z轴之后的坐标系。坐标系x’’/(y’’=y’)/z’’指的是以俯仰角围绕y’轴旋转x’/y’/z’轴之后的坐标系。最后，坐标系(x’’’=x’’)/y’’’/z’’’在用横摆角/翻滚角旋转x’’/y’’/z’’后获得。

### 3.2 参考线s/t/h坐标系

参考线坐标系适用于沿道路的参考线。它是一个右旋坐标系。s方向沿参考线的切线。需要注意的是，参考线总是位于惯性坐标系定义的x/y平面内。t方向与s方向正交。右手系统通过定义与x轴和y轴正交的上方向h来完成。

![img](https://img-blog.csdnimg.cn/img_convert/12b0684d7aa4ae7f73f4350a40106418.png)

如下为参考线坐标系的三个方向描述：

- s方向：沿参考线的坐标，从道路起点开始测量，单位为[m]。参考线，在 xy 平面上计算（即，不考虑道路高程剖面图)
- t方向：横向位置，从参考线出发，延与s方向正交的方向，指向s方向左侧。
- h方向：在右手坐标系中与st平面正交

```xml
<geometry s="4.9957524872074799e+02" x="4.9469346060416666e+02" y="5.3447643627860181e+01" hdg="5.8804473418180125e-02" length="6.2079164697363019e+01"> <line/> </geometry>
```

在上述例子中，s="4.9957524872074799e+02"描述的是此段道路的起始点在参考线坐标系中的位置。

### 3.3 三种坐标系的关系

三种坐标系的关系可通过下图清晰展示，惯性坐标系、参考线坐标系和局部坐标系将在OpenDRIVE中同时被使用。图中的示例描述了三个坐标系相对于彼此的位置与方向设定。

![img](https://img-blog.csdnimg.cn/img_convert/12f5744c2c19e5aad738e0479535d075.png)



### 3.4 OpenDRIVE中的地理坐标参考

空间参考系的标准化由欧洲石油调查组织(EPSG)执行，该参考系由用于描述大地基准的参数来定义。大地基准是相对于地球的椭圆模型的位置合集所作的坐标参考系。

通过使用基于PROJ（一种用于两个坐标系之间数据交换的格式）的投影字符串来完成对大地基准的描述。该数据应标为CDATA，因为其可能包含会干预元素属性XML语义的字符。

在OpenDRIVE中，关于数据集的地理参考信息在`<header>`元素的`<geoReference>`元素中得以呈现。Proj字符串（如以下XML示例中所示）包含了所有定义已使用的空间参考系的参数：

关于proj字符串的细节信息，参见 https://proj.org/usage/projections.html

投影的定义不能（shall）多于一个。若定义缺失，那么则假定为局部笛卡尔坐标系。

这里强烈建议使用proj字符串的官方参数组（使用该链接查询字符串： https://epsg.io/ ）。参数不应（should）被改变。一些空间参考系如UTM具有隐东及北伪偏移，这里使用+x_0与+y_0参数对它们进行定义。

若想应用偏移，请使用`<offset>`元素，而不是改变所有参数值。

XML示例:

```XML
<geoReference>
    <![CDATA[+proj=utm +zone=32 +ellps=GRS80 +towgs84=0,0,0,0,0,0,0 +units=m +no_defs]]>
</geoReference>
```

![img](https://img-blog.csdnimg.cn/2020113015124997.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dodXpoYW5nMTY=,size_16,color_FFFFFF,t_70)

规则：

- `<offset>` 应使OpenDRIVE 的x和y坐标大致集中在(0;0)周围。在x和y坐标过大的情况下，由于IEEE 754双精度浮点数的精确度有限，在内部使用浮点坐标的应用可能无法对它们进行精确处理。

## 4 OpenDrive格式地图数据解析

> [OpenDrive格式地图数据解析_lyf's blog-CSDN博客_opendrive](https://blog.csdn.net/lewif/article/details/78575840)

**OpenDrive地图解析代码可以参考，https://github.com/liuyf5231/opendriveparser**

该xml文件中中包含了很多地图信息，例如Road、Junction等，下图是xml文件的主要结构，

![这里写图片描述](https://img-blog.csdn.net/20171122102541888?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGV3aWY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

下图为绘制地图的一个简单思路，读取OpenDRIVE文件，即地图数据，构造路网，通过渲染展示给用户。

![这里写图片描述](https://img-blog.csdn.net/20171122102613527?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGV3aWY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

下面结合OpenDRIVE文件中的数据，介绍如何构造路网。



## 5 与其他标准的关联

### 5.1 ASAM OpenDRIVE在ASAM标准系列中的角色

ASAM OpenDRIVE是ASAM仿真标准的一部分，该标准专注于车辆环境的仿真数据。除了ASAM OpenDRIVE，ASAM还提供其他仿真领域的标准，例如ASAM OpenSCENARIO和ASAM OpenCRG。

### 5.2 OpenDRIVE与OpenCRG以及OpenSCENARIO之间的关联

ASAM OpenDRIVE为路网的静态描述定义了一种存储格式。通过与ASAM OpenCRG结合使用，可以将非常详细的路面描述添加至路网当中。OpenDRIVE和ASAM OpenCRG仅包含静态内容，若要添加动态内容，则需要使用ASAM OpenSCENARIO。三个标准的结合则提供包含静态和动态内容、由场景驱动的对交通模拟的描述。

![img](https://img-blog.csdnimg.cn/20201126175442116.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dodXpoYW5nMTY=,size_16,color_FFFFFF,t_70)

图 OpenDRIVE, OpenCRG 以及 OpenSCENARIO之间的关联

### 5.3 向后兼容早期版本

OpenDRIVE 1.6版包含了在1.5版中出现过的元素，但这些元素与1.4版不兼容。为了确保能与1.4版和1.5版兼容，这些元素在1.6版的[XML](https://so.csdn.net/so/search?q=XML&spm=1001.2101.3001.7020)模式中从技术上被定义为可选。在UML模型的注释中，它们被标记为“向后兼容的可选”。

## 6 OpenDRIVE高精地图查看器

[OpenDRIVE地图在线查看 - BimAnt](http://opendrive.bimant.com/)是一个免费的OpenDRIVE高精地图在线查看工具，可以直接在浏览器网页内打开.xodr格式的高精地图并自动创建并显示相应的道路三维模型。

地址：http://opendrive.bimant.com/