- [自动驾驶入门技术（10）—— 高精地图（HAD MAP）](https://blog.csdn.net/ckc108727ckc/article/details/107242577)

## 1、什么是高精地图

### 1.1 基本含义解析

个人通俗点的理解为：

高精度地图即“两高一多”地图 — “高精度”，“高动态”，“多维度”

1）高精度 —— 精度达到厘米级别

2）高动态 —— 高精地图数据的实时性；为了应对各类突发状况，自动驾驶车辆需要高精地图的数据具有较好的实时性；

3）多维度—— 地图中不仅包含有详细的车道模型、道路部件信息，还包含与交通安全相关的一些道路属性信息，例如GPS信号消失的区域、道路施工状态等。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200710085333459.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NrYzEwODcyN2NrYw==,size_16,color_FFFFFF,t_70)

图1. 高精地图结构内容划分示意图

### 1.2 高精地图图层划分

高精地图的图层按自动驾驶中所起的作用大致可以化为三层：车道级路网图层、定位图层和动态图层；

#### 1）车道级路网图层 —— 导航规划

主要是对路网精确的三维表征（厘米级精度）进行描述，并存储为结构化数据，主要可分为两大类：

—— 道路数据：比如路面的几何结构、车道线类型（实线/虚线、单线/双线）、车道线颜色（白色、黄色）以及每个车道的（坡度、曲率、航向、高程等）数据属

性等；

—— 车道周边的固定对象信息：比如交通标志、交通信号灯等信息、车道限高、下水道口、障碍物以及其他道路细节，还包括高架物体、防护栏数目、道路边缘

类型、路边地标等基础设施信息；

#### 2）定位图层 —— 车辆定位

该层所包含的元素，取决于自动驾驶汽车打算采用何种传感器来匹配定位图层进行定位，但目前自动驾驶汽车在定位方面的解决方案差异性较大，比如有基于视觉

特征匹配定位方案，也有基于激光雷达点云特征匹配定位解决方案，还有基于视觉特征和激光雷达点云特征数据融合的定位方案；同时定位图层所包含元素还与自

动驾驶车辆的应用场景相关；

未来图商有可能会根据不同的场景、不同的传感器生成不同的高精地图的定位图层。

#### 3）动态图层 —— 用于感知和考虑当前道路和交通状况的路线规划

现阶段对于高精度地图动态图层需要哪些信息要素也还没有定论，仍处于探讨研究的阶段。但动态图层包含的内容大致可分为两个的方面：实时路况和交通事件；

例如：道路拥堵情况、施工情况、是否有交通事故、交通管制情况、天气情况等动态交通信息。由于路网每天都有变化，如整修、道路标识线磨损及重漆、交通标

示改变等。这些变化需要及时反映在高精地图上以确保自动驾驶车辆的行驶安全。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200710085414520.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NrYzEwODcyN2NrYw==,size_16,color_FFFFFF,t_70)

图2. 高精度地图需求模型

注：车道级路网层所包含的元素现在业界比较统一，而定位图层以及动态图层所包含的信息要素分歧比较大，目前尚无定论；

## 2、普通导航地图与高精地图的区别

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200710085433993.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NrYzEwODcyN2NrYw==,size_16,color_FFFFFF,t_70)

表1. 普通导航地图与高精地图对比分析

## 3、高精地图在自动驾驶系统中的作用

### 3.1 环境感知辅助

#### 1）扩大自动驾驶车辆的感知范围

超视距感知 - 车载传感器探测范围有自己的性能边界限制，但高精地图可以延伸传感器的感知范围，提前告知车辆前方的道路信息及交通状况信息；

弥补车载传感器在特殊情况条件下的感知缺陷 - 车载感知传感器在复杂的路况或恶劣天气条件下，会遇到探测死角以及感知性能下降甚至失效的情况，此时高精地

图可及时进行环境信息补充，实时状况监测及外部信息反馈；

特殊情况举例：

a、激光雷达在恶劣天气下效果较差，比如大雾，大雨，或面对大范围的尘土；

b、高分辨率相机，视场角窄的情况下，可以检测到很远的距离，但面对暴雨/大雪等恶劣天气，很难检测到正确的车道线/障碍物/马路牙子等信息；

c、前方道路交通标志模糊，摄像头无法读取信息；

d、前方大车遮挡，摄像头无法探测前方红绿灯的情况；

#### 2）提供先验信息 - 节约车载传感器的计算资源（车载传感器相当于无人车的眼睛，而高精地图相当于无人车的记忆）

高精地图可帮助车辆提前预知前方的道路、交通、基础设施等信息，帮助车载传感器缩小检测范围，车载传感器可专注于检测感兴趣区域（ROI）；这样既提高了

车载传感器的检测精度和速度，同时又节约了其计算资源；

#### 3） 提供冗余数据 - 冗余保障（高精地图可以说是无人车最稳定的“传感器”）

a、当某些传感器数据缺失时，可以利用高精地图数据进行推算；

b、当同一个数据有多个车载传感器数据来源时，高精度地图可以用于相互校验，校验其他传感器的可信度，提高整个系统的准确度。

### 3.2 路径规划与决策

提供先验信息给自动驾驶系统以便其做出合理的行为规划决策；

例如：前方具有低速限制、人行横道或道路施工区域，高精地图能让车辆提前预知，并预先减速；

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200710090706232.png)

前方人行横道且限速 前方道路十字路口 前方道路施工

### 3.3 高精度定位辅助 - 确定车辆在地图中的位置

高精地图对路网有精确的三维表征（比如路面的几何结构、道路标示线的位置、周边道路环境的点云模型等），并存储为结构化数据；这些结构化数据都有地理编

码，自动驾驶系统通过车载GPS/IMU、Lidar或摄像头获得的环境信息与高精地图上的信息做对比分析，便可得到车辆在地图上的精确位置；