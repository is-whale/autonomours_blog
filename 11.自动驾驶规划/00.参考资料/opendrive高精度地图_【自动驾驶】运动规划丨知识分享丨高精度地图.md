- [opendrive高精度地图_【自动驾驶】运动规划丨知识分享丨高精度地图_weixin_39636176的博客-CSDN博客](https://blog.csdn.net/weixin_39636176/article/details/111675081)

![7487212ffb5240ea6e5b4b6f4aa2b5f7.png](https://img-blog.csdnimg.cn/img_convert/7487212ffb5240ea6e5b4b6f4aa2b5f7.png)

------

## **高精度地图的数据信息**

[轨迹规划](https://so.csdn.net/so/search?q=轨迹规划&spm=1001.2101.3001.7020)模块中，需要基于高精度地图计算本车当前位置，障碍物在地图上基于参考线的坐标。因此，需要了解高精度地图为运动规划提供哪些具体参数信息。

高精度地图可以为无人车提供的某些先验信息。包括道路**曲率、航向、坡度和横坡角。**这些信息对于无人车的安全性和舒适性都至关重要。

开发者说 | 看不见的“传感器”——高精度地图mp.weixin.qq.com

![c353988fe56938eedb39b917ac1b93fa.png](https://img-blog.csdnimg.cn/img_convert/c353988fe56938eedb39b917ac1b93fa.png)

------

## **高精度地图的重要性**

**L3的定义解读为：**以高精地图数据为主，以感应器数据为辅，可以实现法定限速内全速域、全路况的[自动驾驶](https://so.csdn.net/so/search?q=自动驾驶&spm=1001.2101.3001.7020)，才是真正的L3级别自动驾驶。只能实现部分速域（如长安L3的0-40KM/h）或依靠道路标线识别的产品，严格来说只能算是升级版的L2级别自动驾驶。

在SAE的分级体系中，L0至L2为低等级的驾驶系统，而L3至L5为高级自动驾驶系统。在L2到L3的跨越中，最为重要的就是环境的监控主体从驾驶员变为了系统。只有当系统能够自动地探查与分析附近区域的状况时，高阶的自动驾驶才能成为可能。

需要说明的是，这里的环境监控主体不仅需要持续不断地获取汽车周边的环境信息，更重要的是根据信息进行驾驶环境安全状况的判定。因此仅仅拥有夜视（Night Vision）、交通标志识别（Traffic Sign Recognition）等功能并不代表环境监控主体为系统。

因此，仅仅升级L2自动驾驶的摄像头与雷达，已经不能满足系统接管汽车时对环境监控的需求，直到高精地图的出现才解决了这个问题。高精地图对于L3自动驾驶的重要性可以体现为4点：

**（1）提供精准度更高更安全的地图**

要让交通工具自己拥有可以做出行为判断的超级大脑，这就对地图精准度要求极高，定位的偏差会造成系统无法获取准确车道定位，在转向、变道等情况下可能发生安全风险。普通的导航地图只能为驾驶员提供道路信息，而高精地图则专为自动驾驶而生。我们日常所使用的电子地图，都是基于商用GPS，精度为5米左右，而高精地图定位精度达到0.1m，直接服务于智能驾驶决策控制器



![1939cf8d44473538ccf809326284157a.png](https://img-blog.csdnimg.cn/img_convert/1939cf8d44473538ccf809326284157a.png)

无高精度地图导致的定位差异问题

**（2）在恶劣天气下保障安全**

自动驾驶安全性常常受到恶劣天气的威胁，例如光线、浓雾、雨雪等。这种情况下传感器受到影响甚至失灵。高精地图的优势在于可以弥补传感器数据缺失，利用高精地图数据对前方1km的道路情况进行补充。无论是在日常驾驶还是浓雾环境，高精地图都能更精确、全面地感知前方路况，行车安全性得到更高保障。



![39c2b032895b7676a322053b2c419956.png](https://img-blog.csdnimg.cn/img_convert/39c2b032895b7676a322053b2c419956.png)

无高精度地图在恶劣环境下的劣势

**（3）高速过弯时获取路况信息**

当车辆处在高度过弯状态时，由于车载传感器的局限性，无法对弯道后路况进行提前监测，这时候自动驾驶系统就会将自动驾驶回归于人。高精地图的优势就显现出来了。有了高精地图后，可提前获取前方道路的限速、弯道曲率等信息，为车辆提供超视域的路径规划和决策依据，可安全舒适的通过。



![d20da4c8062fe70eb343229835d8dc3f.png](https://img-blog.csdnimg.cn/img_convert/d20da4c8062fe70eb343229835d8dc3f.png)

**（4）高效静态物体判断力**

搭载高精地图后，自动驾驶能让监测系统更加智能，提前去除路灯、标志牌等固有静态物体，让资源集中在动态物体监测，这无疑增加了系统运行效率、提高了传感器监测精度、提升了自动驾驶安全性。以上四点对于实现环境监控而言都是至关重要的。当前全球主流汽车的自动驾驶水平普遍在L1到L2级别，而没能广泛实现自动驾驶技术的跨越，很大程度上就是因为高精地图的缺失。可以说，高精地图是L3及以上自动驾驶的关键钥匙，离开了高精地图，高阶自动驾驶只能是“空中楼阁”。

什么才是真正的L3自动驾驶？mp.weixin.qq.com

![5bbded5962caccf7bd0f5c566c5b45f8.png](https://img-blog.csdnimg.cn/img_convert/5bbded5962caccf7bd0f5c566c5b45f8.png)

## **高精度地图的格式**

文章来源:

自动驾驶中基于车道线的高清制图方法回顾mp.weixin.qq.com

![98a52f91e8dcf560e59801068f9b8d2a.png](https://img-blog.csdnimg.cn/img_convert/98a52f91e8dcf560e59801068f9b8d2a.png)


在开始引入之前，先提一下两个地图格式：**1. OpenDrive**
一种逻辑描述道路网的开源格式，主要在仿真器领域使用。其中OpenCRG是路面特性描述格式。



![99b06a3a87236e750e803d116ba696d8.png](https://img-blog.csdnimg.cn/img_convert/99b06a3a87236e750e803d116ba696d8.png)


OpenDRIVE Manager (OdrManager) 管理如何读取数据。



![ee93d66e63dbd6dd44764f2c825025c2.png](https://img-blog.csdnimg.cn/img_convert/ee93d66e63dbd6dd44764f2c825025c2.png)

**2. NDS：Navigation Data Standard**
汽车业导航数据库标准化的格式。下图是NDS的HD Map层：



![87a7091054370e70f9cb22db99398ba8.png](https://img-blog.csdnimg.cn/img_convert/87a7091054370e70f9cb22db99398ba8.png)


二者之间的转换如下图：



![36bb91d542180858232cf641931cb3eb.png](https://img-blog.csdnimg.cn/img_convert/36bb91d542180858232cf641931cb3eb.png)

**地图相关的论文**

1. Creating Enhanced Maps for Lane-Level Vehicle Navigation
   Enhanced maps(Emaps)定义车道线的拓扑结构，能辅助车辆的车道线级别的定位。在法国，德国和瑞典已经使用。**一般车辆定位分三个级别：**

- Macroscale: 10米精度，GPS和道路数据库匹配；
- Microscale: 亚米精度，地图不带有绝对坐标;
- Mesoscale: 车道级精度，带绝对坐标的地图；

下图是同一地区的比较：(a) 谷歌地图. (b) 标准地图. (c) 线段，节点和形状点的表示. (d) Emap。



![8232acff2801874948f5fb3882367563.png](https://img-blog.csdnimg.cn/img_convert/8232acff2801874948f5fb3882367563.png)


Emap 提供线段的拓扑信息：

- 左/右/前方邻居线段特性；
- 在每个道路的车道线段相对侧向位置；
- 确定线段连接性；短时间建立复杂连接图的能力。

**2. Lanelets: Efficient Map Representation for Autonomous Driving**
注：德国高清地图公司Atlatec采用Lanelets编辑地图结果。
Lanelets记录自动驾驶环境的几何和拓扑特性，Lanelet指那些相互连接的驾驶区域道路线段，主要用于行为层（behavior layer）。
如图所示：Lanelets是有左右边界的折线，以一定精度近似车道几何，确定驾驶方向。



![6b3ce19db101b7382c79de27ccbf3bbc.png](https://img-blog.csdnimg.cn/img_convert/6b3ce19db101b7382c79de27ccbf3bbc.png)


基于连接的Lanelets，路径规划可以执行。下图展示一个Lanelet在汇聚和交叉的状况 (ID: 6451) 。



![f284a10d3961110d54f1591894f00342.png](https://img-blog.csdnimg.cn/img_convert/f284a10d3961110d54f1591894f00342.png)


下图展示的是JOSM，即Java OSM (OpenStreetMap) ，在这个编辑器上重叠的Lanelets。



![4d578dd12629b622edf33474c794b791.png](https://img-blog.csdnimg.cn/img_convert/4d578dd12629b622edf33474c794b791.png)

**3.A Smart Map Representation for Autonomous Vehicle Navigation**
该论文提出的地图包括三个元素：道路，车道，和车道线。不过，它是基于激光雷达的高清地图，采用ArcGIS制成的。这个地图提供道路级和车道级的规划能力，辅助智能车通过交叉路口的驾驶。
注：ESRI (Environmental Systems Research Institute)是一个国际的，GIS（Geographic information system）软件的提供商，ArcGIS是商用位置服务的软件工具。



![f1a55a8e3eba4d2dfb2820bf94bf4e5d.png](https://img-blog.csdnimg.cn/img_convert/f1a55a8e3eba4d2dfb2820bf94bf4e5d.png)


这种地图描述车道网和道路网的拓扑结构，同时给出车道线的几何描述，如上图。车道线级的规划通过路口的例子见下图：[3,59,55,36,30,35]



![ec2fb330f679e8d964a3f77e1fd769eb.png](https://img-blog.csdnimg.cn/img_convert/ec2fb330f679e8d964a3f77e1fd769eb.png)


算法如下：



![fe76cc08e9c89ff078c6c3e844ed08f9.png](https://img-blog.csdnimg.cn/img_convert/fe76cc08e9c89ff078c6c3e844ed08f9.png)

**4.High Precision lane-level road map building for vehicle navigation****道路地图的要求是：**

- 每个道路分成道路段序列；
- 每个道路段，车道数目不变；
- 每个路段相邻的车道在同一方向是隐含连接的；
- 不同的道路其车道是可以不连接而相交的；
- 每个路段的车道可定义为解析曲线；
- 车道曲线图的精度期望是分米级别。

这里采用Cubic Hermite Spline (CHS)描述车道线。基于CHS的分割方法是：道路相交提供一个长路的初始化分解；在两个节点之间假设 (n − 1) 顶点和n 基函数；然后估计顶点位置和切向方向。
看看下面的道路地图例子：（A）“• ”符号相连的道路例子，(B) 道路网的一部分，包括各个路的车道线，红色“⚬”代表节点之间的顶点。



![ec5649a4a993e1db5de5bbb9d6bf75f4.png](https://img-blog.csdnimg.cn/img_convert/ec5649a4a993e1db5de5bbb9d6bf75f4.png)

**5.Road Lane Semantic Segmentation for High Definition Map**
这里提出一个基于车道语义分割的高清地图自动构成方法：采用单镜头，通过FCN检测车道线，然后提取车道特征，用来检测闭环。最后基于图的SLAM生成地图，流程如下图。



![23dbd336f5574deb729902a40dbd5e79.png](https://img-blog.csdnimg.cn/img_convert/23dbd336f5574deb729902a40dbd5e79.png)


关于特征提取见下图：主要是每个道路线段的key point。



![cb280627604b1cc1c73ddd3c09e9a151.png](https://img-blog.csdnimg.cn/img_convert/cb280627604b1cc1c73ddd3c09e9a151.png)


关于闭环的检测参见下图：一般停止线比较适合做定位的landmark，并估计车辆的姿态。



![274069d6ff5c94f1179263f83eae42fb.png](https://img-blog.csdnimg.cn/img_convert/274069d6ff5c94f1179263f83eae42fb.png)


下图是一个多种颜色表示的语义分割结果：(a) 输入，(b)ground truth，FCN(c)，SegNet(d), PSPNet(e) 和 颜色意义(f).



![b88512ad266abb2389db4a3a60943e20.png](https://img-blog.csdnimg.cn/img_convert/b88512ad266abb2389db4a3a60943e20.png)


下图是高清地图的例子：依次是最终地图，以及蓝色和红色部分的细节放大。



![1e812539f3114cebf1f231a890062dc3.png](https://img-blog.csdnimg.cn/img_convert/1e812539f3114cebf1f231a890062dc3.png)



![c74cb2bc2eaa8095f483e47c1fdd0d7b.png](https://img-blog.csdnimg.cn/img_convert/c74cb2bc2eaa8095f483e47c1fdd0d7b.png)



![e018f6564722824ae9cc63c5929954a9.png](https://img-blog.csdnimg.cn/img_convert/e018f6564722824ae9cc63c5929954a9.png)

**6.Map Management for Efficient Long-Term Visual Localization in Outdoor Environments****如图给出的系统示意图：**多个车辆定位通过一个移动通信的共享远程地图，“Appearance-based landmark selection”可以从地图检索路标。一旦车辆完成一次驾驶，搜集的数据上传到后端，并入地图更新。随后，后端的“map summarization”保证地图规模，也保证存储容量限制条件。



![6e0db2bafe1a4496c1a6227ac6a43d4f.png](https://img-blog.csdnimg.cn/img_convert/6e0db2bafe1a4496c1a6227ac6a43d4f.png)

**下图是地图更新的流程图：**首先新数据需要在地图定位。一旦定位精度过低，就从数据中建立新的路标加入地图中，然后进入summarization降低地图中的路标数目到固定的数目。其他情况下，在定位中所有路标的观测统计会被更新，但不会添加新的路标。



![8f9080c9b26d58318aaa142488867fc0.png](https://img-blog.csdnimg.cn/img_convert/8f9080c9b26d58318aaa142488867fc0.png)

**7.Design of a Multi-layer Lane-Level Map for Vehicle Route Planning**
建立了一个车辆路径规划的三层车道级别的地图，即road-level-layer, intermediate layer和 lane-level layer。其中lane-level layer的几何由Cubic Hermite Spline 描述，用一组control points 产生车道的几何关系。下图是三层车道级别的地图模型：



![ee63ee61f01dfd97a5a87798987211ac.png](https://img-blog.csdnimg.cn/img_convert/ee63ee61f01dfd97a5a87798987211ac.png)


road-level-layer包括道路和交叉路口，可以支持成熟的路径规划算法。intermediate layer 是上下层的桥梁，存储的车道集合之间的相关性可用于路径规划。lane layer 提供了车道级别的细节， 除了车道和交叉路口，还有一些车道线和车道中心线的高清点。
下图是intermediate layer中道路级和车道级的路口信息描述：其中进出路口几个道路之间的拓扑连接描述为traffic matrix形式。



![348de84393a048807e3bfc20b9f0df4c.png](https://img-blog.csdnimg.cn/img_convert/348de84393a048807e3bfc20b9f0df4c.png)

**8.Generation of a Precise and Efficient Lane-Level Road Map for Intelligent Vehicle Systems**
系统框图如下：包括三部分，数据获取，数据处理和道路建模。



![4ae46d58b2a419a1948cc76f684aff4b.png](https://img-blog.csdnimg.cn/img_convert/4ae46d58b2a419a1948cc76f684aff4b.png)


下图是车道线点的提取和聚类：提取区域是车辆的姿态决定的，每个区域提取的点就是marking point。这些被用于道路建模。



![23d34e24f2100eb2e958d9c66315e9d8.png](https://img-blog.csdnimg.cn/img_convert/23d34e24f2100eb2e958d9c66315e9d8.png)


下图是局部地图数据和图像平面之间的转换，其中二者的匹配良好。



![f97f17dd87409dafb6821dd625d68213.png](https://img-blog.csdnimg.cn/img_convert/f97f17dd87409dafb6821dd625d68213.png)


注：这篇文章特别，直接从航拍图像建地图。**9.Automated Map Generation from Aerial Images for Precise Vehicle Localization**
航空图像产生路标地图需要图像分类和路标结合描述的工作。本文提出了一个基于规则的从路标估计车道边界的算法。
下图是路标检测分类：(a) 原始图像 (b) 9x9 HSV 特征向量做比较(c) 提出一种旋转不变性特征向量。



![07aaa4b586062db9903879589c300160.png](https://img-blog.csdnimg.cn/img_convert/07aaa4b586062db9903879589c300160.png)


而这个是车道边界的检测：(a) 原始路标. (b) 计算的车道边界 (c) 滤除非车道边界的剩余路标。



![c751e868d42f7c157c0745bcbb3c388a.png](https://img-blog.csdnimg.cn/img_convert/c751e868d42f7c157c0745bcbb3c388a.png)


注：这里用2-D激光雷达（单线），成本低于一般高清地图所用的多线雷达。**10.Lane Map building and Localization for Automated Driving Using 2D Laser RangrFinder**
路标识别是建立车道地图的前提。



![71f3340e5b22f60cdc68ad8b18007577.png](https://img-blog.csdnimg.cn/img_convert/71f3340e5b22f60cdc68ad8b18007577.png)



![1a510c5bf11b32a2f33596b787173ed7.png](https://img-blog.csdnimg.cn/img_convert/1a510c5bf11b32a2f33596b787173ed7.png)



![77ba3d9021967548ece036abe0ed2f58.png](https://img-blog.csdnimg.cn/img_convert/77ba3d9021967548ece036abe0ed2f58.png)



![bfab992fd0bbf04134e0dfab84f8ef5b.png](https://img-blog.csdnimg.cn/img_convert/bfab992fd0bbf04134e0dfab84f8ef5b.png)


上图是一个车道线识别的例子：激光雷面向地面，180°平扫，最大距离80米，测距5厘米误差；算法要求先做路面估计（折线表示法），车道线通过反射值提取，差分GPS和IMU做数据校准，将车身位置变换到UTM决定坐标系上。**车道地图建图就是找到车道。**下图是提取车道的例子：首先是道路中线分割出来，采用一种简化的折线段表示，基于Douglas-Peucker算法沿不同方向将中线段分成多个部分，并得到形状点集。最后车道通过Radon transform得到。



![1ffe04d6e0f0db509b7d788d919563cb.png](https://img-blog.csdnimg.cn/img_convert/1ffe04d6e0f0db509b7d788d919563cb.png)


其中道路中线分割的例子见下图：



![1cc08588d5de97f7e93e35f98389e360.png](https://img-blog.csdnimg.cn/img_convert/1cc08588d5de97f7e93e35f98389e360.png)


定位重要的是车辆姿态估计，文中采用GPS和激光雷达数据-车道地图的ICP匹配算法实现。车辆姿态数据进入一个卡尔曼滤波器，做递推。下图是车道地图的定位流程图：



![842f96ea5cde2450fc92c9a87a2b974e.png](https://img-blog.csdnimg.cn/img_convert/842f96ea5cde2450fc92c9a87a2b974e.png)


下图给出一个定位的例子：



![275eb23fe8869089b37081120d326fa4.png](https://img-blog.csdnimg.cn/img_convert/275eb23fe8869089b37081120d326fa4.png)

**11.LineNet: a Zoomable CNN for Crowdsourced High Definition Maps Modeling in Urban Environments**
这篇文章主要是车道线检测，在实验中提到一些HD Maps的制作过程：开源工具：
mapillary/OpenSfM(https://github.com/mapillary/OpenSfM)
加自己的车道线检测方法LineNet，还有一些后处理。**看看一些结果如下：**



![bac872d76f652e1b4189fe54822c0682.png](https://img-blog.csdnimg.cn/img_convert/bac872d76f652e1b4189fe54822c0682.png)



![cc24bc0e6707866b54aaca7b2554b1a0.png](https://img-blog.csdnimg.cn/img_convert/cc24bc0e6707866b54aaca7b2554b1a0.png)

相关资源：[*高精度**地图**OpenDrive*格式说明_*opendrive**高精度**地图*,*高精度**地图*...](https://download.csdn.net/download/guyanf/12438732?spm=1001.2101.3001.5697)