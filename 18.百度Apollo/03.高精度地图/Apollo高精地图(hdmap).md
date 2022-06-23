- [apollo_learning/opendrive_adapter.md at master · chucklqsun/apollo_learning (github.com)](https://github.com/chucklqsun/apollo_learning/blob/master/opendrive_adapter.md)

## 前言

百度的Apollo项目主要是依赖地图，配上各类传感器（加速度传感器，GPS，激光雷达，摄像头等）来实现定位，导航，决策，避障，遵守交规等行为。据我所知，高精地图对perception感知模块的用途是很大，比如说要用CNN分割周围车辆行人物体，hdmap能迅速给出需要关注的区域大小，可以删除不需要关注的绿化带和周围建筑等。另外2.0版本的信号灯识别，就依赖于地图里信号灯的位置和轮廓给定数据，然后投射到图像对应位置，能比所谓的纯图像识别要精确可靠。Apollo的地图是依照opendrive这个开源格式改出来的，接下来让我们一探究竟。

## 相关参考文章

- [从百度Apollo代码谈高精度地图](https://zhuanlan.zhihu.com/p/30728273)
- [Apollo坐标系统](https://github.com/ApolloAuto/apollo/blob/master/docs/specs/coordination.pdf)

## 文件路径:

1. /modules/map/hdmap/adapter/opendrive_adapter.*
2. /modules/map/hdmap/adapter/proto_organizer.*
3. /modules/map/proto/*

## 流水线:

1. 将地图文件作为XML文件载入
2. 抽取头部信息（见下文头结构）
3. 抽取路信息

- 查看是否是交叉口id
- 抽取车道信息
- 抽取物体信息（停止线，人行横道，禁停区，减速带）
- 抽取信号标示（信号灯，停车标示，让车标示）

1. 抽取交叉口信息（特别的是，Apollo格式和OpenDrive不一样）

- outline
- objectOverlapGroup
  - objectReference: id

1. 通过分析路和交叉口，遍历所有道，抽取重叠元素

- 找重叠的物体（4类）
- 找重叠的信号标示（3类）
- 找重叠的车道(起点，终点，是否合并)

1. 本阶段地图准备就绪

**注意**

- 通过地图东和西的最远位置来判断时区是否一致，不支持跨时区地图。
- 通过对地图geoReference字段分析，转换成UTM投影(60等分，每个等分投射成二维直角坐标系)。

## 地图头结构

```
message Header {
  optional bytes version = 1;       //地图数据库的版本
  optional bytes date = 2;          //地图数据库创建的时间
  optional Projection projection = 3;   //投射坐标系:"+proj=utm +zone=时区 +ellps=WGS84 +datum=WGS84 +units=m +no_defs";
  optional bytes district = 4;      //地图数据库名称
  optional bytes generation = 5;    //???
  optional bytes rev_major = 6; //OpenDrive格式的主要版本号
  optional bytes rev_minor = 7; //OpenDrive格式的次要版本号
  optional double left = 8;     //west，最小惯性参考系x值
  optional double top = 9;      //north，最大惯性参考系y值
  optional double right = 10;   //east，最大惯性参考系x值
  optional double bottom = 11;  //south，最小惯性参考系y值
  optional bytes vendor = 12;   //地图厂商名称
}
```

## 地图数据结构

```
message Map {
  optional Header header = 1;

  repeated Crosswalk crosswalk = 2;     //人行横道
  repeated Junction junction = 3;       //交叉口
  repeated Lane lane = 4;               //车道
  repeated StopSign stop_sign = 5;      //停车标志
  repeated Signal signal = 6;           //信号灯
  repeated YieldSign yield = 7;         //让车标志
  repeated Overlap overlap = 8;         //重叠
  repeated ClearArea clear_area = 9;    //禁停区
  repeated SpeedBump speed_bump = 10;   //减速带
  repeated Road road = 11;              //路，包含道路，物体等信息
}
```

## 信号灯（v2.0新增信号灯感知）

### 数据结构

```
message Subsignal {
  enum Type {
    UNKNOWN = 1;        //未知
    CIRCLE = 2;         //圆圈
    ARROW_LEFT = 3;     //向左箭头
    ARROW_FORWARD = 4;  //向前进箭头
    ARROW_RIGHT = 5;    //向右箭头
    ARROW_LEFT_AND_FORWARD = 6;      //向左前进箭头
    ARROW_RIGHT_AND_FORWARD = 7;     //向右前进箭头
    ARROW_U_TURN = 8;           //U型箭头
  };

  optional Id id = 1;       //编号
  optional Type type = 2;   //类型

  // Location of the center of the bulb. now no data support.
  // 中间灯泡的位置
  optional apollo.common.PointENU location = 3;
}
message Signal {
  enum Type {
    UNKNOWN = 1;    //未知
    MIX_2_HORIZONTAL = 2;   //水平2灯
    MIX_2_VERTICAL = 3;     //垂直2灯
    MIX_3_HORIZONTAL = 4;   //水平3灯
    MIX_3_VERTICAL = 5;     //垂直3灯
    SINGLE = 6;
  };

  optional Id id = 1;               //编号
  optional Polygon boundary = 2;    //轮廓
  repeated Subsignal subsignal = 3; //子信号信息
  // TODO: add orientation. now no data support.对应行驶方向，目前无数据
  repeated Id overlap_id = 4; //重叠id
  optional Type type = 5;   //信号灯外形类型，见Type
  // stop line
  repeated Curve stop_line = 6; //车辆停止线位置
}
```

### 轮廓

如下，由坐标给出轮廓位置。Apollo为其outlien定义了cornerGlobal元素，四个角，具体可见 [traffic_light_perception_cn](https://github.com/chucklqsun/apollo_learning/blob/master/traffic_light_perception_cn.md)。有空写一下信号灯感知的具体代码实现。

```
message Polygon {
  repeated apollo.common.PointENU point = 1;
}

message PointENU {
  optional double x = 1 [default = nan];  // East from the origin, in meters.
  optional double y = 2 [default = nan];  // North from the origin, in meters.
  optional double z = 3 [default = 0.0];  // Up from the WGS-84 ellipsoid, in
                                          // meters.
}
```

## 其他

Apollo自己定制了地图数据结构，但是我也没找到合适的工具能编辑他们家地图格式，希望了解这方面工具的同学能提供帮助。

查看地图工具： modules/tools/mapshow/mapshow.py -m modules/map/data/sunnyvale_loop/base_map.bin

[![alter text](https://github.com/chucklqsun/apollo_learning/raw/master/img/sunnyvale_map.png)](https://github.com/chucklqsun/apollo_learning/blob/master/img/sunnyvale_map.png)

## Q&A from GitHub Issue

从github项目相关的issue里找来了一些关于HDMap的内容:

1. 如何制作HDMap?

主流HDMap制作方案主要借助于激光雷达、Camera等设备，借助于点云处理算法、图像视频算法、SLAM等技术来制作。国内的高精度地图受到相关法律法规的约束，如果有大规模的场景应用需求可以考虑与百度合作。

1. /modules/map/data/下的地图目录中通常包含这几个文件base_map.bin，base_map.xml，routing_map.txt，base_map.lb1， default_end_way_point.txt，sim_map.bin，base_map.txt，routing_map.bin， sim_map.txt，想知道每个文件的具体作用是什么？与仿真环境中的哪些有对应关系？

如 README 所示，xml, bin, txt, lb1 都是不同的文件格式，适配不同的读取器，内容是一致的。 base_map: 基础地图; routing_map: routing模块所需的、对 base_map 预处理得来的地图; sim_map: simulation模块所需的、对 base_map 降采样以提高传输和渲染效率; default_end_way_point: 一个proto，定义了该地图默认的终点，用户可以一键导航到这个终点。

我们建议这些文件都成套提供，避免不必要的麻烦。

3.这些文件是如何生成的？具体需要哪些传感器？apollo1.5中是否提供了相应的工具生成这些文件?

base_map的制作比较复杂，需要的设备较多，我们没有包含在代码中，而是作为一项服务。 从base_map制作routing_map，见 modules/routing/topo_creator/topo_creator.cc 从base_map制作sim_map，见 modules/map/tools/sim_map_generator.cc 选取 default_end_way_point 可以用 modules/tools/mapshow/mapshow.py 来选择 lane_id 和 s.

高精度地图的文件(base_map)的制作服务目前以合作方式对外开放。如果只是简单场地的测试，并且希望自己制作，需要首先获取场地的地理坐标(UTM坐标，获取方式依据不同的应用场景不尽相同，比如采用激光雷达，视觉slam，甚至GPS轨迹或者通过人工处理的方式等等),然后将数据组织成高精度地图(base_map)的格式即可．

1. 建议的地图制作流程为：

- 原始数据采集(视觉、激光雷达、GPS等)以及处理；
- 地图数据生成。从步骤一生成的数据通过算法或者人工的方式获取地图数据；
- 地图格式组织。将地图数据转换为Apollo的高精度地图格式（可以参照base_map.xml格式,其他的地图都可以从base_map.xml生成）
- 这三个步骤的工具均需要自己开发，如果只是小规模的简单测试，也可以参照base_map.xml格式手工组织数据。