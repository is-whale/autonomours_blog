- [【Apollo 6.0学习笔记】高精地图_Travis.X的博客-CSDN博客_高精地图](https://blog.csdn.net/Travis_X/article/details/121288565)

# 前言

## 什么是高精地图？

高精地图是为自动驾驶而生的，L3/L4级别的自动驾驶必备。

（1）高精地图并不是特指精度，而主要体现在两个以下特征上；
（2）**在道路环境的描述上更加的全面**，高精地图最主要的特征是需要描述车道、车道的边界线、 道路上各种交通设施和人行道；
（3）**对实时性的要求更高**，能实时反映当前道路情景。

------

# 一、高精地图与各模块之间的关系

![在这里插入图片描述](https://img-blog.csdnimg.cn/7415063b785d4814b556d7262108e1f5.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 1.1 高精地图与定位模块的关系

**现在主流的自动驾驶的定位方案有两种：一种是基于点云，另一种是基于Camera。**

基于点云的定位方案是通过激光雷达扫描到的点云信息进行特征的提取，然后通过复杂的组合变换、视角变换，最终通过与周围环境的对比得到比较准确的定位结果。

基于Camera的视觉定位方案是实时作Location Feature 的提取，HD Map里也存储了对应的一些location相关的特征，经过两个提取特征的比较后就能得到相对准确的定位结果。

------

## 1.2 高精地图与感知模块的关系

自动驾驶车辆上搭载的传感器很多，但激光雷达、相机和毫米波雷达等传感器都有一定的局限性。

激光雷达点云会随着检测距离增大而变得更稀疏，检测的可信度也会随之下降。另外，自动驾驶车辆行驶过程中如果遇到洒水车、或者碰上雾霾天气，也会对激光雷达的检测可信度带来影响。

相机在夜间、逆光或者极端天气下很难达到非常好的视觉效果。

毫米波雷达虽然穿透力强，但精度不高。

这些传感器自身的局限性，会对自动驾驶的感知带来影响，这时高精地图就能提供非常大的帮助。**开发者可以把高精地图看作离线的传感器**，地图上可以标注物体的位置等信息，感知模块就能提前进行针对性的检测，一方面减少了感知模块的工作量，另一方面也可以解决Deep Learning的部分缺陷，识别可能会有些误差，但先验后可提高识别率。

------

## 1.3 高精地图与规划、预测、决策模块的关系

规划模块中轨迹的约束，经高精地图运算后，可以让自动驾驶车辆避让时会清楚地知道目的地在哪/怎么选，并提供可行的解空间。

预测的体系比较复杂，但底层仍依赖于高精地图。

决策模块主要是根据规划和预测的结果决定自动驾驶车辆是跟车、超车还是在红绿灯灯前停下等决策。

------

## 1.4 高精地图与安全模块的关系

高精地图能提供离线的标准信息，能有效解决传感器、操作系统、控制系统和通信系统失效时带来的安全隐患。

------

# 二、高精地图的采集与生产

## 2.1 采集的传感器

高精地图采集所需要的传感器主要有 **GPS** 、 **IMU** 、 **轮速计**三类。

### 2.1.1 GPS

在空间点位置的计算过程中，我们经常要检测四颗或四颗以上卫星，才能实现“精准定位”。在无人车复杂的动态环境，尤其是在大城市中，由于高大障碍物的遮挡，GPS多路径反射问题会更加明显，导致GPS定位信息很容易就有几十厘米甚至几米的误差。

### 2.1.2 IMU

IMU是测量三轴加速度的一个装置，通过算出积分，得到任意两帧间的相对运动。

IMU有“高端”和“低端”之分。高端IMU能保持较长时间的计算精确度，而低端IMU在GPS信号丢失的情况下，能够维持比较精确的时间非常短。

实际工作中，由于不可避免的各种干扰因素， 如果不对该运动加以校正，IMU的误差会就随着时间的推移变得越来越大 。

### 2.1.3 轮速计

通过轮距推算无人车的位置，可是由于不同地面材质（比如冰面与水泥地）上转数对距离转换的偏差，随着时间推进，测量偏差会越来越大。

------

## 2.2 制图方案

目前主流的制图方案有基于 **激光雷达** 和 **Camera融合激光雷达** 两种方案。

### 2.2.1 激光雷达

通过将GPS 、 IMU和轮速计，再通过SLAM算法对 Pose 进行矫正，最终才能得出一个“相对精确的Pose”。
最后把空间信息通过激光雷达扫描出三维点，转换成一个连续的三维结构，从而实现整个空间结构的三维重建。
![在这里插入图片描述](https://img-blog.csdnimg.cn/4b7bd17f1c504841846a7c1976b078cc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
![img](https://img-blog.csdnimg.cn/fff9b781c4564adfae89e6521eb7503d.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

### 2.2.2 Camera融合激光雷达

虽然激光雷达采集的信息非常精确，但它采集的信息非常少，无法提供像图像那样丰富的语义信息、颜色信息。通过融合二者的优势，综合运用丰富的图像信息和精确的激光雷达数据，最终得出一个非常精确的高精地图。
![在这里插入图片描述](https://img-blog.csdnimg.cn/c5585149bcbe491d8e4247b23cd77769.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 三、高精地图的格式分类

目前最主流的通用格式规范分NDS和OpenDRIVE两种。

## 3.1 NDS格式

![在这里插入图片描述](https://img-blog.csdnimg.cn/10851685f9cd4e7c8fd26b52ecacc4ec.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
NDS是一种非常全面的地图表述方式。

由于其数据库可以细分和运用了Level两种技术，NDS对地图的格式规范做得非常到位。

NDS有上百页格式文档，因此NDS把数据库做了细分，每个细分后的产品都能够独立更新升级。

其最典型表现是一个NDS不仅包括基本导航技术数据、B公司的POI数据（即地图上的一个点，地图上每一家商家店铺都可以被称之为一个POI数据点），还支持局部更新，即使是对一个国家或者省市的相关内容进行局部更新，都十分便捷。

为了方便用户，NDS还提供语音、经纬度等描述功能。

------

## 3.2 Open DRIVE格式

![在这里插入图片描述](https://img-blog.csdnimg.cn/2c436c83e82d4f2e91263941f3261f23.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
OpenDRIVE是目前国际上较通用的一种格式规范，由一家德国公司制定。

在运用OpenDRIVE格式规范表述道路时，会涉及**Section**、**Lane**、**Junction**、**Tracking**四个概念。

### 3.2.1 Section

无论车道线变少或变多，都是从中间的灰线切分。切分之后的地图分为Section A、Section B和Section C三部分。

一条道路可以被切分为很多个Section。按照**道路车道数量变化**、**道路实线和虚线的变化**、**道路属性的变化**的原则来对道路进行切分。

### 3.2.2 Lane

基于Reference Line，向左表示ID向左递增，向右表示ID向右递减，它是格式规范的标准之一，同时也是固定的、不可更改的。比如，Reference Line的ID为0，向左是1、2、3，向右是−1、−2、−3。
![在这里插入图片描述](https://img-blog.csdnimg.cn/f81d291d39734304a87a6e1ee46446a9.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

### 3.2.3 Junction

Junction是OpenDRIVE格式规范中的路口概念。Junction中包含虚拟路，虚拟路用来连接可通行方向，用红色虚线来表示。
在一张地图中，在遇到对路口的表述时，虽然说路口没有线，但我们要用虚拟线来连接道路的可通行方向连，以便无人驾驶车辆明确行进路线。
![在这里插入图片描述](https://img-blog.csdnimg.cn/1c0e471a61b4477384bc55fa4809320e.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

### 3.2.4 Tracking

Tracking的坐标系是ST，S代表车道Reference Line起点的偏移量，T代表基于Reference Line的横向偏移量。
![在这里插入图片描述](https://img-blog.csdnimg.cn/6394340f2ca04805bf407c3573224173.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 四、 高精地图的生产流程

在城市道路环境下，高精地图生产分为**数据采集**、**数据处理**、**元素识别**、**人工验证**四个环节。
![在这里插入图片描述](https://img-blog.csdnimg.cn/13c9beb2886141fa80e1ac18d69b4982.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

- **数据采集**：百度采取的是激光雷达和Camera二者相结合的制图方案。

- 数据处理

  ：传感器采集到的数据分为点云和图像两大类。

  - 点云拼接：采集过程中出现信号不稳定时，需借助SLAM 或其他方案，对Pose进行优化，才能将点云信息拼接，并形成一个完整的点云信息。
  - 反射地图 ：点云拼接后，可将其压缩成可做标注、高度精确的反射地图，甚至基于反射地图来绘制高清地图。其生产过程与定位地图的制图方式一样。

- **元素识别**：包括基于深度学习的元素识别和基于深度学习的点云分类。

- **人工验证**：人工验证的环节包括识别车道线是否正确、对信号灯、标志牌进行逻辑处理、路口虚拟道路逻辑线的生成等。

------

# 五、 [Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) OpenDRive

## 5.1 Apollo OpenDRive结构

Apollo高精地图文件的整体结构如下所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/19835f8f191449048f5e9f00cd0dba85.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
百度高精地图坐标采用**WGS84经纬度坐标**表示。WGS84为一种大地坐标系，也是目前广泛使用的GPS全球卫星定位系统使用的坐标系。

## 5.2 Apollo OpenDrive与标准OpenDrive区别

Apollo的OpenDrive跟标准OpenDrive的区别主要有以下四点。
（1）**Apollo里面的OpenDrive，都是坐标点，没有采用方程的方式**。采用方程方式的好处在于数据量非常小，通过三四个参数就可以描述一个非常长的线。采用坐标点的方式，数据量会稍微大一点。但是也有很多的好处。第一，用点表示对于下游的计算非常友好，不需要再重新通过线去做点的采样；第二，在道路急于转弯的地方，原始的OpenDRive把基于Reference Line的方式还原成点的方式，会导致道路上存在毛刺。这种处理方式对于无人驾驶来说非常危险；
（2）**Apollo对OpenDrive进行了元素类型的扩展**。比如增加了禁停区，人行横道、减速带等元素的表示；
（3）**增加了一些道路元素关系的表述**。比如新增了Junction与Junction内元素的关联关系；
（4）**增加了诸如停止线与红绿灯的关联关系，中心线到边界的距离等的描述**。

------

# 总结

【1】《Apollo进阶课程6 | 高精地图与自动驾驶的关系》
【2】《Apollo进阶课程7 | 高精地图的采集与生产》
【3】《Apollo进阶课程8 | 高精地图的格式规范》
【4】《Apollo进阶课程9 | 业界的高精地图产品》
【5】《Apollo进阶课程10 | Apollo地图采集方案》
【6】《Apollo进阶课程11 | Apollo地图生产技术》
【7】《Apollo进阶课程12 | Apollo高精地图》
【8】[Apollo 高精地图解析](https://mp.weixin.qq.com/s?__biz=MzI1NjkxOTMyNQ==&mid=2247484107&idx=1&sn=f3d79cb208afef14470622f0dd9b8dcf&scene=21#wechat_redirect)