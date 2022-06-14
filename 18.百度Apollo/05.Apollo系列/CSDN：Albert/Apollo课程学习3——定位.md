- [Apollo课程学习3——定位_Albert的博客-CSDN博客](https://blog.csdn.net/weixin_43476492/article/details/107959534)

# 无人车自定位系统

## 一、什么是无人车自定位

![img](https://img-blog.csdnimg.cn/20200812112739809.jpg#pic_center)

- **坐标系**可以是一个局部的坐标系，也可以是一个全局的坐标系（如全球坐标系）。
- **位置**对应X，Y，Z，即相当于某个坐标系，汽车的平移是多少。
- **姿态**是三个方向的一个旋转，一般用欧拉角来表示（本地坐标系的三个轴和相对坐标系的这三个轴之间的夹角）。包括**横滚、俯仰和航向**，分别相对于X，Y，Z三个坐标轴。

![img](https://img-blog.csdnimg.cn/20200812113543222.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812113943800.jpg#pic_center)
除去速度、加速度和角速度外，自定位系统还需要对位置和姿态加上一个**置信度**，表示这次输出的定位结果好不好。

## 二、系统的指标要求

![img](https://img-blog.csdnimg.cn/20200812114239560.jpg#pic_center)

- **定位精度**必须控制在10厘米以内，才能使行驶中的自动驾驶车辆**避免出现碰撞/车道偏离**的情况。
- **鲁棒性**也就是**最大的误差**不要超过30厘米。但是在有些时候，纵向误差稍微大一点点，只要没有偏离车道就没有太大影响。超过30厘米的误差对前后距离的控制会有很大风险。
- **场景**：L4和L5自动驾驶需要自定位系统在各种场景下面都能够工作。

## 三、为什么无人车需要该系统

![img](https://img-blog.csdnimg.cn/20200812115916582.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812120015601.jpg#pic_center)

# 三维几何变换

## 一、旋转

![img](https://img-blog.csdnimg.cn/20200812154831536.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812155733427.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812155823575.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812160011133.jpg#pic_center)

## 二、平移

![img](https://img-blog.csdnimg.cn/20200812160305451.jpg#pic_center)

## 三、刚体的位置和朝向

![img](https://img-blog.csdnimg.cn/20200812160559698.jpg#pic_center)

## 四、坐标系

![img](https://img-blog.csdnimg.cn/20200812160755538.jpg#pic_center)

### 1、ECI地心惯性坐标系

![img](https://img-blog.csdnimg.cn/2020081216093124.jpg#pic_center)

### 2、ECFF地心地固坐标系

![img](https://img-blog.csdnimg.cn/20200812161431387.jpg#pic_center)

### 3、当地水平坐标系

![img](https://img-blog.csdnimg.cn/20200812161805337.jpg#pic_center)

### 4、UTM坐标系

![img](https://img-blog.csdnimg.cn/20200812162143693.jpg#pic_center)

### 5、车体坐标系

![img](https://img-blog.csdnimg.cn/20200812162449971.jpg#pic_center)

### 6、IMU坐标系

![img](https://img-blog.csdnimg.cn/20200812162751322.jpg#pic_center)

### 7、相机坐标系

![img](https://img-blog.csdnimg.cn/20200812163128816.jpg#pic_center)

### 8、激光雷达坐标系

![img](https://img-blog.csdnimg.cn/20200812163312450.jpg#pic_center)

### 9、无人车定位中涉及的坐标系

![img](https://img-blog.csdnimg.cn/20200812163603755.jpg#pic_center)

# 定位方法

## 一、定位方法概述

![img](https://img-blog.csdnimg.cn/20200812120609188.jpg#pic_center)

## 二、GNSS定位技术

GNSS定位其实是单点定位，没有用到基站，它的精度一般是5到10米。它们的原理是测距，有三颗卫星就可以交会两点，舍弃外部的空间点就可以得到自己测绘这个点。但是一般情况下，由于钟差，一般需要**四颗**卫星去做误差剔除。

![img](https://img-blog.csdnimg.cn/20200808220635509.jpg#pic_center)
类似的系统还有中国的**北斗定位**、俄罗斯的格罗纳斯和欧洲的伽利略定位。

![img](https://img-blog.csdnimg.cn/2020081217020634.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812170822975.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812172759673.jpg#pic_center)

## 三、载波定位技术

载波定位技术具体分为两类，RTK技术和PPP技术。它们都是为了确定载波的整周数，然后消除噪声，达到更精准的定位。

![img](https://img-blog.csdnimg.cn/20200808222027331.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812171826513.jpg#pic_center)
**RTK**的工作原理如下：卫星把观测数据给基站，也给车端的移动站。基站根据多个卫星的钟差计算出误差项，然后把误差项传递给车端，车端用这个误差项消除观测误差，得到精准的位置。它的问题是硬件成本高，需要建基站，需要4G通信的链路，需要基站传数据。

![img](https://img-blog.csdnimg.cn/20200812172203686.jpg#pic_center)
**PPP**可以简单理解为一个很强的单点，它有很多种基础基站的建设。这些基站通过卫星数据，把这些误差都在基站做分离处理，再传递给卫星。卫星已经做了误差的消除，再去对车端进行定位，得到一个非常高精度的定位信息。

![img](https://img-blog.csdnimg.cn/20200812122757630.jpg#pic_center)

## 四、激光定位

### 1、激光定位原理

激光定位是**预先制作一个地图**（3D的Voxel地图或点云地图或2D的概率地图），然后拿**实时的激光点云和这个地图去做匹配**。

如2D概率地图是把所有的这个点云数据铺到一块，压成了一个2D地图。2D地图分成很多小格子，每个格子里面存储了颜色信息，位置。另外还包括一些概率，概率和点云的数量以及分布相关。

如下图所示的搜索方式，选择了一个XY化的矩形，在每个小格子里面去匹配。每个里面会算一个匹配的概率，匹配可能会用实时点云里面的颜色值或者高度值的分部和2D地图去匹配，最后会得到一个概率图。根据该概率图，用加权平均会得到XY的位置。

![img](https://img-blog.csdnimg.cn/20200812124548490.jpg#pic_center)

### 2、迭代最近点（ICP）

对于一次扫描中的每个点，我们需要**找到另一次扫描中最接近的匹配点，形成匹配点对**。然后把每对点之间的距离误差相加，计算得平均距离误差，最后通过点云旋转和平移来**最大限度地降低该平均距离误差**。然后我们就可以在扫描数据和地图之间找到匹配，实现了将传感器扫描到的车辆位置转换为全球地图上的位置的操作，然后就可以计算在地图上的精确位置了。

![img](https://img-blog.csdnimg.cn/20200808235353976.jpg#pic_center)

### 3、滤波算法

滤波算法可**消除冗余信息**，并在地图上找到最可能的车辆位置。

#### 3.1 [直方图](https://so.csdn.net/so/search?q=直方图&spm=1001.2101.3001.7020)滤波算法

直方图滤波算法又称**误差平均和算法（SSD）**。首先将传感器扫描的点云滑过地图上的每个位置，同时**计算每个扫描的点与高精度地图上对应点之间的误差**，然后对误差的平均求和。**误差平均和越小，表示扫描结果与地图之间的匹配度越高。** 下图中，红色表示对齐较好的点，蓝色表示对齐较差的点，绿色表示中等对齐的点。

![img](https://img-blog.csdnimg.cn/20200809000015367.jpg#pic_center)

#### 3.2 卡尔曼滤波

卡尔曼滤波**使用了预测更新周期**，我们可**根据之前的状态以及对移动距离和方向的估计来预测新位置**。这种运动估计并不完美，所以**需要使用传感器测量位置来加以纠正**。一旦用传感器测量了新位置，**便可使用概率规则**，将不完美的传感器测量结果与现有的位置预测结合起来。然后我们**将永远遵循这个预测更新周期**。

### 4、激光定位的优缺点

- 优点：**稳健性**。只要拥有高精度地图和有效的传感器，我们就能够一直定位。
- 缺点：**难以构建高精度地图并使其保持最新**。事实上几乎不可能让地图完全保持最新，因为每个地图**均包含瞬态元素**（如汽车、行人、街道上的垃圾等），世界上的许多元素都在不断发生变化。

### 5、百度的激光点云定位模块

![img](https://img-blog.csdnimg.cn/20200812173115427.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812173446708.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812174032736.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812174242311.jpg#pic_center)

## 五、视觉定位

![img](https://img-blog.csdnimg.cn/2020081217452960.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/2020081217472741.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812175043509.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812175154844.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812175317130.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812175612112.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812175718802.jpg#pic_center)

- 优点

  ：

  - **图像数据比较容易获得**。
  - 对于一些具有**明显Semantic意义的特征**，如车道线、红绿灯的柱子、红绿灯、一些Traffic Sign等，视觉定位非常有用。

- 缺点

  ：

  - **缺乏三维信息**和对三维地图的依赖。
  - 对于视觉定位，如果**环境变化越大**，视觉所受到的影响就越大。所以基于这种特征点（如SIFT特征）的定位（如SLAM里面用的Relocalization）并不是很合适用于车的视觉定位。

## 六、惯性导航(IMU)

![img](https://img-blog.csdnimg.cn/20200812180005989.jpg#pic_center)

- **三轴加速度计**提供瞬时的加速度（车辆坐标系下）。
- **三轴陀螺仪**提供瞬时角速度。将车辆坐标系下的测量值转换为全局坐标系。如下图所示，三轴陀螺仪的三个外部平衡环一直在旋转，但其旋转轴始终固定在世界坐标系中。所以我们可以通过测量旋转轴和三个外部平衡环的相对位置来计算车辆在坐标系中的位置。

![img](https://img-blog.csdnimg.cn/20200808230100803.jpg#pic_center)

- **IMU的工作过程**：首先是角速度通过积分后得到一个姿态，并把它应用到加速度上，对加速度积分得到速度，再得到最后的位置。

![img](https://img-blog.csdnimg.cn/20200812181035780.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812131501874.png#pic_center)
![img](https://img-blog.csdnimg.cn/20200812180651117.jpg#pic_center)

## 七、多传感器融合定位

多传感器融合定位的核心是中间的**卡尔曼滤波器**，这是一个状态误差的卡尔曼滤波器。该滤波器接收惯性导航输出的递推，作为它的时间更新，保证滤波器往前走和高频的输出。还接受GPS、激光点云定位，或者是视觉定位的输出去做低频的状态更新。

![img](https://img-blog.csdnimg.cn/20200812132200628.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200809212118905.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812181458135.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200812181700268.jpg#pic_center)
绿车是定位结果，蓝车是只有GPS和IMU的结果。

![img](https://img-blog.csdnimg.cn/20200812181947961.jpg#pic_center)

# 百度无人车定位进化历程

![img](https://img-blog.csdnimg.cn/20200812164047660.jpg#pic_center)

- **“探路者”** 这套车的传感器配置非常贵，都用最好的惯导和最好激光雷达，目的是用于探索一些新的算法，一些复杂的场景、有挑战的场景，主要是为了给工程师提供一个较好的平台，用更好的数据去开发更好的算法。下图是探路者在雨天、林荫路上时的场景。

![img](https://img-blog.csdnimg.cn/20200812165805945.jpg#pic_center)

- 无人驾驶微循环车是和厦门金融合作，叫**阿波龙**。它配备了一个很便宜的惯导。激光雷达用了16线，再加上一些双目摄像头。下图是阿波龙在海淀公园时的场景。

![img](https://img-blog.csdnimg.cn/20200812165458126.jpg#pic_center)

- **无人驾驶物流车**，是和**新石器**一起开发的，一般情况下可以用于小区的无人快递车或者是广场上面去卖饮料的无人车。下图是无人驾驶物流车在百度大厦（稍微封闭的环境）时的场景。

![img](https://img-blog.csdnimg.cn/20200812164850579.jpg#pic_center)