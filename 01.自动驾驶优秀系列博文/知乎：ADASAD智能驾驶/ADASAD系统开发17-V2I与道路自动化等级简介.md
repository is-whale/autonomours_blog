- [ADAS/AD系统开发17-V2I与道路自动化等级简介 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/146788212)

**前言**

对于地方ZF来讲，单车自动驾驶并不性感，V2I（车路协同）也许才是心中所属。原因至少有两个：1.利益角度：车辆自动化，主要也就是汽车行业的盘子；如果是道路的智能化，盘子就大多了：地方ZF、汽车行业、通信行业、基建等等；2.受益角度：汽车自动化，对提高生产力有益，但是直接对监管部门讲，意义没有那么直接；倘若是车路协同，就不一样了。自动化的交通监管、疏导、管控等等，对地方ZF来说，吸引力可能更大。

**正文**

汽车的自动化程度，有L0到L5的等级划分，如图1；相对应的，道路的自动化程度，也有对应的道路自动化等级，叫ISAD（Infrastructure Support levels for Automated Driving，自动驾驶基础设施支持等级），有A到E一共5个等级，如图2所示。

![img](https://pic4.zhimg.com/80/v2-6ef75424ca6f6f91f62f39d1421508fb_720w.jpg)

图1 SAE自动驾驶分级

![img](https://pic4.zhimg.com/80/v2-ba8c36ea32d71f9af474cb39921a1347_720w.jpg)

图2 ISAD道路自动化分级

针对道路的ISAD分级，主要包括：

- E代表“无自动驾驶支持的传统道路设施”。没有数字化信息，自动驾驶车辆需要感知道路几何形状以及道路标志等信息；
- D代表“静态数字化信息支持（如地图支持）”。数字地图中需要提供静态道路标志，也需要包括道路的物理参考点（比如车道线、地面标志等）、交通信号灯、短期道路施工区域以及VMS（可变信息交通标志）等，供自动驾驶车辆使用；
- C代表“动态数字信息”。所有能提供给自动驾驶车辆的基础设施数字信息，包括动态的、静态的；
- B代表“车路协同感知”。即智能化道路通过安装摄像头、雷达、激光雷达等等传感器，具备“感知能力”，并把微观交通状态等感知信息提供给自动驾驶车辆；
- A代表“车路协同驾驶”。即智能化道路通过各种传感器，向自动驾驶车辆提供道路上其他车辆的实时动态移动信息，帮助控制车辆，并优化交通流信息。

![img](https://pic2.zhimg.com/80/v2-30c58703951dcc070a2e2b12a32e7e61_720w.jpg)

图3 基于不同路段的ISAD智能化等级

如上图所示，道路的ISAD等级与路段相关。在十字路口处，ISAD等级需要达到A级，才能起到有效疏导交通的作用；而在于城市高架等道路场景相对简单的路段，采用C级或D级就能够满足要求。而在高速公路等封闭道路场景，即使完全不需要智能化道路（E级），只靠自动驾驶车辆的单车智能就可满足要求。针对V2I的组成部分，如图4所示。

![img](https://pic2.zhimg.com/80/v2-63c44aaf80980e78abe8e3d8eba08bed_720w.jpg)

图4 V2I系统架构

另外，ODD的完整释义，应该从驾驶维度的“驾驶自动化条件”以及地理维度的“道路场景”两个维度考虑，才能形成完整的ODD概念，如图5所示的XOY平面。自动驾驶ODD与道路智能化等级ISAD之间的关系如图6所示。两者有相互交叠的部分，但是也有相互无法覆盖的部分，毕竟一个是驾驶维度、一个是地理维度（跟随路段）的概念。

![img](https://pic2.zhimg.com/80/v2-ffa8391270e1aea208e033283a547651_720w.jpg)

图5 ODD的完整释义

![img](https://pic2.zhimg.com/80/v2-a86f4bdf315aa5771d66fd9090528899_720w.jpg)

图6 ODD与ISAD的关系

注：素材选自《Connected-Automated-Driving-Roadmap》