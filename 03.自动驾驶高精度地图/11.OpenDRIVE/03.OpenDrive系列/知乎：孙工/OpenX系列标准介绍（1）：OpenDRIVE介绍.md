- [OpenX系列标准介绍（1）：OpenDRIVE介绍 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/163811654)

“ 本系列尝试对ASAM OpenX系列标准进行介绍。这是第一篇：介绍OpenDRIVE地图数据格式所能描述的内容和思路。”

## 01 概述

作为一个完整的仿真测试场景描述方案，OpenX系列标准包括：OpenDRIVE、OpenSCENARIO和OpenCRG。仿真测试场景的静态部分（如道路拓扑结构、交通标志标线等）由OpenDRIVE文件描述，道路的表面细节（如坑洼、卵石路等）由OpenCRG文件描述，仿真测试场景的动态部分（如交通车的行为）由OpenSCENARIO文件描述。如下图所示：

![img](https://pic1.zhimg.com/80/v2-16b71dc9ec9eaa45ac81e156b8316500_720w.jpg)

OpenDRIVE是一种高精地图格式，2006年由德国VIRES公司发布，并反复迭代，期间德国戴姆勒驾驶模拟器部门和德国宇航中心DLR也发挥了很大作用。OpenDRIVE 1.5版本于2019年发布。2018年9月，OpenDRIVE的开发团队将维护工作转交给德国ASAM标准化组织，1.6及之后的版本由ASAM负责。1.6版本已由ASAM在2020年3月发布，本文使用该版本进行介绍。

OpenDRIVE开发起因是VIRES公司在提供驾驶模拟器方案时，发现不同工具的道路数据格式中需要包含逻辑内容是基本一致的，为了方便在不同的驾驶模拟器间进行道路数据的传递，VIRES公司与Daimler Driving Simulator部门决定开发OpenDRIVE格式。转交给ASAM组织后，ASAM组织同样把OpenDRIVE定位为用于仿真测试的地图格式。

OpenDRIVE文件按XML格式编写，文件扩展名为.xodr。

## 02 OpenDRIVE的道路结构介绍

OpenDRIVE将道路（roads）分为三个部分：道路参考线（reference line）、车道（lanes）和道路设施（features）。如下图：

![img](https://pic3.zhimg.com/80/v2-6f578004e06a6b05766e2c47292f513e_720w.jpg)

除此以外，还可以设置道路的高度（elevation），对于多条道路汇聚的位置需要用路口（junctions）来描述。

### （1）道路参考线（reference line）

道路参考线可以理解为道路中心线在水平面的投影，也就是说道路参考线反映的是道路俯视的形状，而不包括坡度、起伏等特征。

每条道路有且仅有一条道路参考线，该参考线可以有多条曲线连接而成，这些曲线的形式包括：直线、螺旋线、圆弧、三次多项式（不再使用）和三次多项式参数方程等。如下图所示：

![img](https://pic1.zhimg.com/80/v2-90b1888a7ccdc31a4628abc85f0000a4_720w.jpg)

### （2）车道（lanes）

每条道路都可以设置大于等于一条车道，可以有多条车道，还可以通过设置不同的车道分段来实现不同区域的车道数量和车道宽度的变化，如下图所示：

![img](https://pic4.zhimg.com/80/v2-e58cdd2ed8e92d09349b2a042495bdeb_720w.jpg)

车道可以设置不同的属性，包括：宽度（可以用沿道路方向的三次多项式描述）、类型（如行车道、停车区域、行人道等）、材质（包括摩擦系数）、限速、路权（可以设置公交专用道）、车道线等。车道类型和车道线设置的示例如下图所示：

![img](https://pic2.zhimg.com/80/v2-7764f21731bfcb779a4fbfe33bf5d239_720w.jpg)

### （3）道路设施（features）

道路设施包括物体（objects）和交通信号（signals）两种。

物体包括停车位、隧道、桥梁、人行道和路障等类型，通过在道路s-t坐标系的位置、朝向和高度等属性进行定义。不仅可以放置数量不同的多种物体，还提供了repeat的方法放置多个重复的物体。下图是对物体位置和轮廓描述方式的示意：

![img](https://pic1.zhimg.com/80/v2-80308ac7a14c58e45511365ff9ec875c_720w.jpg)

交通信号包括信号灯和标志牌等可能会对交通产生影响的元素。交通信号既包括静态信号（如限速标志），也包括可以动态变化的信号（如红绿灯）。

可以为交通信号制定其作用的车道，比如可以为不同车道设置不同的限速；也可以将一个交通信号在多个车道重复引用，方便设置。

可以将一个交通信号的物理位置和逻辑位置设置在不同地方，这很适用于红绿灯的场景：红绿灯的逻辑位置在路口这一侧的停止线，而物理位置在路口对面。同样，对于红绿灯，可以使用一个相位控制器控制多个红绿灯的状态，方便进行相位同步。

### （4）道路高度

道路的高度包括纵向坡度（即沿行驶方向的高低起伏）、超高（即道路横向的坡度，例如转弯处外侧较高），甚至可以设置横向复杂的形状（如路面中间凸起便于排水的形状）。如下图所示：

![img](https://pic3.zhimg.com/80/v2-d01de0ab9e95ec01c8bcf07b5e606006_720w.jpg)

### （5）路口

当三条及以上道路相交、无法清楚描述道路的连接关系时，需要用到路口。

路口由三个部分组成：来路（incoming roads）、去路（outgoing roads）和连接路（connecting roads）组成。其中：来路为进入路口的道路，可以有不止一条；去路为离开路口的道路，可以有不止一条；来路可同时作为去路；连接路作为来路和去路之间连接。如下图所示，一条来路可以对应多条连接路，而每条连接路都只连接一条来路和一条去路，这样就明确了路口处道路的连接关系。