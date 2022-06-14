- [【Apollo 6.0学习笔记】定位_Travis.X的博客-CSDN博客_apollo 定位](https://blog.csdn.net/Travis_X/article/details/121355796)

# 前言

定位系统可以与高精地图配合提供静态场景感知，可将感知得到的动态物体正确放入静态场景，而位置和姿态用于路径规划和车辆控制。因此 定位系统对于无人驾驶至关重要。

对于自动驾驶汽车，定位的指标要求大概分为三个部分：

- **精度**：定位精度必须控制在10厘米以内，才能使行驶中的自动驾驶车辆避免出现碰撞/车道偏离的情况。
- **鲁棒性**：一般情况下用最大值来衡量。也就是最大的误差不要超过30厘米。但是在有些时候，纵向误差稍微大一点点，只要没有偏离车道就没有太大影响。
- **场景**：需要覆盖很多的场景。

------

# 一、 定位方法

![在这里插入图片描述](https://img-blog.csdnimg.cn/d893cd6f68814c85922c26e2dae41f7c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

定位的方法大体上可以分为三个部分：**基于电子信号的定位**、**航迹的推算**和**环境特征的匹配**。

## 1.1 基于电子信号的定位方法

基于电子信号的定位方法中最有特点的就是GNSS（全球导航卫星系统）。它的作用机制是通过一组卫星的伪距、星历、卫星发射时间等观测量，以及用户钟差，得知你现在大概的位置。

GPS手机定位的精度非常低。大概都是五到十米，好一点的话三四米。但是对于车辆定位，这远远不够。

因此现在有一些差分的技术，即卫星的实时动态差分技术可以弥补这些方面的缺憾。差分技术分为物理差分和距离差分两种。距离差分又分为**伪距差分**和**载波相位差分**。

伪距差分的精度不会特别的高，大概在米级的这么一个量级，还不能满足要求。因此发展出实时动态的载波相位差分。

载波相位差分最主要的是要估计一个载波相位的一个整周，定位精确基本上是在厘米级（小于5厘米）。

![在这里插入图片描述](https://img-blog.csdnimg.cn/9ca0e8f8f12b4f88a7a18d45329485ae.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 1.2 航迹推算

航迹推算最有特点的就是IMU。航迹推算就是根据上一时刻“我“的”位置“和”姿态“，叠加一些测量信息可以知道现在的”位置“和”姿态“。IMU是一个惯性测量单元。它包含了加速度计和陀螺仪。加速度计会输出加速度的信息。但是不止加速度，它还包含重力加速度。陀螺仪是一个旋转，即是前面所讲到三个轴上的一个旋转。

IMU的出频率非常的高，基本上都是200赫兹以上，这样有助于同步。因为它有精准的时间戳，它检测的数据传输过来之后可以精准知道它的时间戳，有精准的时间戳就可以给出精准的位姿，即它在全局坐标系下有自己的位置。

![在这里插入图片描述](https://img-blog.csdnimg.cn/d7366858d5fa41889647d931f190a5a0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 1.3 环境特征匹配

环境特征匹配相对较多，比如LiDAR的匹配。在建好了点云定位的地图，将LIDAR的数据和已建好的地图做匹配，以此来计算自己的位置。或者通过摄像头，知道了一些车道线，红绿灯的标志，然后确定自身的位置。

### 1.3.1 激光雷达定位

激光定位是预先制作一个 地 图，不管是3D的Voxel地图或者是点云地 图，又或者是2D的概率地图。

2D概率地图是把所有的这个点云数据铺到一块，压成了一个2D地图。2D地图分成很多小格子，每个格子里面存储了颜色信息，位置。位置上面可能只存Z的这个维度，因为2D地图有XY。

另外，还包括一些概率，概率和点云的数量以及分布相关。拿实时的激光点云和这个地图去做匹配。

举例而言，如图6左下角所示的搜索方式，选择了一个XY化的矩形或者正方形，在每个小格子里面去匹配。这个范围大概是几米乘几米。

每个里面会算一个匹配的概率，匹配可能会用实时点云里面的颜色值或者高度值的分部和2D地图去匹配，最后会得到一个概率图。根据该概率图，用加权平均会得到XY的位置。

### 1.3.2 视觉定位

如果照明环境变化比较大，基于特征点的定位方式并不适合于车的视觉定位，如SLAM中的Relocalization，很可能会因为下次检测不到那些特征造成定位的失败。

但是有一些特征具有明显Semantic意义，比如车道线或者旁边立的这些柱子，红绿灯的柱子或者红绿灯本身或者一些Traffic Sign之类的，对于定位而言非常有用。

另外

![在这里插入图片描述](https://img-blog.csdnimg.cn/7a2ac8f8d41b42368216cba400e58362.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 1.4 多传感器融合定位

多传感器融合定位的核心是中间的卡尔曼滤波器，这是一个状态误差的卡尔曼滤波器。

该滤波器接收惯性导航输出的递推，作为它的时间更新，保证滤波器往前走和高频的输出。还接受GPS、激光点云定位，或者是视觉定位的输出去做低频的状态更新。

![在这里插入图片描述](https://img-blog.csdnimg.cn/5e305f3f855142b388573e45f13d9daf.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 二、坐标系

下图给出了定位模块中输出的各个坐标系下的信息以及不同坐标系之间的关系。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2e67c7303cb145c79ee226418bfb8ed0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 2.1 ECI地心惯性坐标系

它是一个固定的坐标系。**IMU测量得到的加速度，角速度都是相对于这个坐标系的。**

![在这里插入图片描述](https://img-blog.csdnimg.cn/ca4ea68b5a594b50a6fa549326677ae8.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 2.2 ECEF地心地固坐标系

常用的如**WGS84坐标系**。其特点是与地球固定在一起，随地球一起转动。

![在这里插入图片描述](https://img-blog.csdnimg.cn/d114b49658844cfe97cf2c58942a2cf0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 2.3 当地水平坐标系（局部坐标）

![在这里插入图片描述](https://img-blog.csdnimg.cn/bd6d113ec51644929d4d91b63849beb3.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 2.4 UTM坐标系

![在这里插入图片描述](https://img-blog.csdnimg.cn/6f090eeba8d343a9a4e9098682660526.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 2.5 车体坐标系

![在这里插入图片描述](https://img-blog.csdnimg.cn/159c28409d0c46ecb05169e151aa45f0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 2.6 IMU坐标系

IMU坐标系其实和车体坐标系基本上是一样。

![在这里插入图片描述](https://img-blog.csdnimg.cn/d61f5e2e58124bd7aa3a5cd3f148b9ee.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 2.7 相机坐标系

![在这里插入图片描述](https://img-blog.csdnimg.cn/bf7e041d0ea24ab9bb10e8c5528678cc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 2.8 激光雷达坐标系

![在这里插入图片描述](https://img-blog.csdnimg.cn/871174fa228147fc83b0133e9b13e719.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 三、百度无人车定位技术

百度[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020)系统使用了**激光雷达**、**RTK与IMU融合**的方案，多种传感器融合加**误差状态卡尔曼滤波器**使得定位精度可以达到5-10厘米，且具备高可靠性和鲁棒性。

百度无人车系统中使用的定位技术，主要分为四个部分：**GNSS定位技术**、**载波定位技术**、**激光点云定位技术**和**视觉定位技术**。

## 4.1 GNSS技术

GNSS定位技术中最著名的是GPS。原理是测距，有三颗卫星就可以交会两点，舍弃外部的空间点就可以得到自己测绘这个点。但是一般情况下，由于钟差，一般需要四颗卫星去做误差剔除。

## 4.2 载波定位技术

载波定位技术具体分为两类，RTK技术和PPP技术。它们都是为了确定载波的整周数，然后消除噪声，达到更精准的定位。

RTK的工作原理如下：卫星把观测数据给基站，也给车端的移动站。基站根据多个卫星的钟差计算出误差项，然后把误差项传递给车端，车端用这个误差项消除观测误差，得到精准的位置。它的问题是硬件成本高，需要建基站，需要4G通信的链路，需要基站传数据。

PPP可以简单理解为一个很强的单点，它有很多种基础基站的建设。这些基站通过卫星数据，把这些误差都在基站做分离处理，再传递给卫星。卫星已经做了误差的消除，再去对车端进行定位，得到一个非常高精度的定位信息。

![在这里插入图片描述](https://img-blog.csdnimg.cn/dff54f9d6b1b4f63b85284ab899a92b1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 4.3 激光雷达定位

激光点云定位系统包括两个模块：**图像对齐**和**SSD-HF**。图像对齐模块主要是用于航向角的优化，SSD-HF主要用于位置xy的优化。

点云定位里面会输出四个维度的信息，XYZ和Yaw（航向角）。

首先做航向角的优化，然后SSD-HF做XY优化，Z则由定位地图提供。定位地图是一种数据的的存储方式。激光点云定位的输入还包括预测位姿和实时点云数据。输出信息将会给融合算法，进行更加精确的定位。

![在这里插入图片描述](https://img-blog.csdnimg.cn/bb8d6a7420a34690a7f5b98648220147.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 4.4 视觉定位

视觉定位的输出也是XYZ和Yaw，即位置和朝向。视觉定位通过摄像头识别图像中具有语义信息的稳定特征并与地图做匹配，得到位置和朝向信息。视觉定位算法的流程图如下所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/ac96b2815e86446ba1b09d13d22ac843.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

视觉定位的流程主要包含三个部分，一是**3D特征地图的离线的生成**，第二是**图像特征的检测**，最后是**数据的整合输出**。

首先是摄像头进行图像特征的检测，主要是进行车道线和杆状物的检测。通过GPS给出的初始位置，基于初始位置对3D地图和摄像头检查到的信息进行特征匹配。用IMU和轮速计去做姿势的预测，给出一个不错的姿势。最后的结果输出给融合模块，融合可以将GPS、视觉定位、IMU数据整合，优化定位结果并提供高频输出。

------

# 参考

【1】《Apollo进阶课程13 | Apollo无人车自定位技术入门》

【2】《Apollo进阶课程14 | 三维几何变换和坐标系介绍》

【3】《Apollo进阶课程15 | 百度无人车定位技术》