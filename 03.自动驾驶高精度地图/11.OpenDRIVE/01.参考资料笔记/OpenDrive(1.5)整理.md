- [OpenDrive(1.5)整理 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/161694387)

首先把需要把经纬度数据(lat, lon, h)或者其他什么坐标转到utm坐标系。

## 1.坐标系

### 1.1 惯性系（xy系）

ISO 8855投影到drawing plane

### 1.2 Track系（st系）

s沿着reference line的切线方向，t指向s左侧，h指向平面外，表示heading。

### 1.3 Local系（uv系）

自定义，别搞复杂，和起始st一致。

## 2.Road Layout

![img](https://pic3.zhimg.com/80/v2-0498f7d320a384f198c9ecb32cedc8a6_720w.jpg)

reference line是最重要的字段了，road的各种性质都是沿着ref_line定义的。

### 3. Offset

可以使用<offset> element来避免数值很大的坐标

### 4. Geometry

![img](https://pic3.zhimg.com/80/v2-12e40467811c060ba33a2840dea8e34e_720w.jpg)第4个不推荐使用

### 4.1 reference line

参考线是使用<geometry>元素表示的，这个 <geometry>以<planView>为parent，<planView>是每个<road>的必选字段。

![img](https://pic1.zhimg.com/80/v2-5c5be47dc839f9638d8c92f007c12c9c_720w.jpg)line example

### 4.1.1 Cubic polynom(deprecated)

一般不用xy：

![img](https://pic1.zhimg.com/80/v2-7ad3ea0da73f15092ce535d3ea8fbe34_720w.jpg)

一般使用local坐标系(uv坐标系)，一段一段来表示，避免多对一的case。

![img](https://pic1.zhimg.com/80/v2-da31bad51af91f1950a58c5abc5cc0c0_720w.png)

a,b一般为0，也可以不为0，不嫌麻烦就行。通常做法是在start point处st方向建立local坐标系，所以v(0) = 0；而曲线此时的切线方向正好是u轴所指的方向，所以 ![[公式]](https://www.zhihu.com/equation?tex=v%27%28u%29%3D0) .

![img](https://pic4.zhimg.com/80/v2-673ae14bf6be9afcc9bd287d194891b7_720w.jpg)

### 4.1.2 Parametric cubic curve

opendrive推荐使用参数化的三次多项式，大部分厂商都用这个，方便处理连接点切线一致的问题，所以使用spline就很通顺了。

![img](https://pic1.zhimg.com/80/v2-f6ca51f7100634df20d7780d3d33156c_720w.jpg)

![[公式]](https://www.zhihu.com/equation?tex=p%5Cin%5B0%2C+1%5D) ，别搞复杂。

![[公式]](https://www.zhihu.com/equation?tex=aU%2C+aV%2C+bV%3D0) , 分子分母对p求个导，分子为0分母不为0，就得到这个结论了。

![img](https://pic3.zhimg.com/80/v2-5e385311c3c22e7deeb71af2ab397c7e_720w.jpg)

求它的话使用Cardinal曲线，你查一下吧，看懂hermite秒懂这个。

## 5. Roads

![img](https://pic2.zhimg.com/80/v2-99f4a2e74c9c8531def075e02df040c9_720w.jpg)

name可选字段，length是reference line的长度，junction字段是road所属junction的id。

### 5.1 Road linkage

road之间必须链接到彼此，可能连到另外一条路，也可能连到junction

![img](https://pic1.zhimg.com/80/v2-6815187a2d56a6c14b0380e9fb9a2f0c_720w.jpg)

reference line要连起来，lane之间也要连起来，第一个图的现象叫leap，第二个叫overlap，这两种一定要避免。

![img](https://pic3.zhimg.com/80/v2-8ac4cfbf3719d7221dc3802c39f1dada_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-5471eabcfded80b586c29072e29ed310_720w.jpg)

road之间的连接是通过<link>element实现的，The <predecessor> and <successor> elements are defined within the <link> element. For virtual and regular junctions, different attribute sets shall be used for the <predecessor> and <successor> elements.

## 6. Lanes

每条road至少有一条宽度大于0的lane，为了便于描述，需要使用<center>字段，center lane没有宽度，center lane的number为0，左侧递增，右侧递减，没有offset的时候center lane和reference line重合，lane type仅为单向行驶时，center左右侧行驶方向不同：

![img](https://pic2.zhimg.com/80/v2-8a38d6b4f7268a6b5aeaf6a4be36efc9_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-f0120d4fb1376bbb2c01e05bcbfa3cef_720w.jpg)

在<laneSection>里定义了<center>, <left>, <right>字段。

### 6.1 lane section

沿着reference line，lane section are defined in ascending order. lane的数目一旦发生变化，必须新建一个lane section：

![img](https://pic2.zhimg.com/80/v2-b1e5b5c8772f450a5d261970cafc14b9_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-db1ff68a1e8ea9a6b3b05740ee30f236_720w.jpg)

复杂路段，可以使用singleSide字段，singleSide为真时，lane scetion的元素只能包含一边的lane，每个lane section必须包含一个center以及right和left之中至少一个。

### 6.2 Lane offset

![img](https://pic2.zhimg.com/80/v2-9003e0f6106bb5a6bab59999976a3c81_720w.jpg)

### 6.3 lane linkagke

![img](https://pic3.zhimg.com/80/v2-3ccfe10b3d4c2a3c5e75d36a8f851956_720w.jpg)

### 6.4 Lane width

Defined along the t-coordinate. Lane width 和 lane border是互斥字段，二选一。都存在的时候规定选width。

![img](https://pic2.zhimg.com/80/v2-d45f83ea748da019f2f55f7f652eff15_720w.jpg)lane width 的属性

![img](https://pic4.zhimg.com/80/v2-4fbb5529cfe8356351e318d3ffecabbb_720w.png)how to calculate

### 6.5 Lane borders

Easier than specifying the lane width because it avoids creating many lane sections.

![img](https://pic1.zhimg.com/80/v2-586491765510822d4b62376c8d247040_720w.jpg)

### 6.6 Lane type

![img](https://pic4.zhimg.com/80/v2-57e8bb2a94ceb95d30c18ce5546f7e17_720w.jpg)