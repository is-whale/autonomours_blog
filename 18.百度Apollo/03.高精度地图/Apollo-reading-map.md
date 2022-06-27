- [Apollo-reading-map - 掘金 (juejin.cn)](https://juejin.cn/post/7031875050147938311#heading-1)

## 1.Proto文件

因为和硬件结合比较多，我认为Proto文件可以看成是一个封装消息的文件，不同的代码或者客户端读取到这个封装好的文件，都能识别到其中的消息。感觉上有点类似于**CAN报文的DBC文件**

### 1.1 Proto文件格式

引用自

> [www.jianshu.com/p/6a6dbff2b…](https://link.juejin.cn?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2F6a6dbff2b5cd%3Futm_campaign%3Dmaleskine%26utm_content%3Dnote%26utm_medium%3Dseo_notes%26utm_source%3Drecommendation)

在学习Proto的过程中，有两个地方需要注意，一个是消息封装格式，另一个是里面常用的字段

#### 1.1.1 常用字段

```
required: //必须赋值的字符，必须有1个，但是在实际使用过程中，如果定义必须存在会带来一些想不
到的意外，因此经常被optional替代
optional: 可有可无的字段，可以使用[default = xxx]配置默认值，0个或者1个
repeated: 可重复变长字段，类似数组，0个或者多个
```

#### 1.1.2 消息格式

```
syntax = "proto2"; // 定义语法类型，通常proto3好于proto2，proto2好于proto1

package tutorial; // 定义作用域

message Person {        // 生成类class Person : public ::google::protobuf::Message 
  required string name = 1;
  required int32 id = 2;
  optional string email = 3;

  enum PhoneType { // 定义枚举类型，生成Person_PhoneType类型
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber { // 生成Person_PhoneNumber类
    required string number = 1;
    optional PhoneType type = 2 [default = HOME]; // 值必须是枚举类型中的一个
  }
  repeated PhoneNumber phones = 4;
}

message AddressBook {
  repeated Person people = 1;
}
```

## 2. Apollo 6.0 map

map位于modules目录下

```
├── data           // 生成好的地图
│   └── demo
├── hdmap          // 高精度地图
│   ├── adapter    // 从xml文件读取地图(opendrive保存格式为xml)
│   │   └── xml_parser
│   └── test-data
├── pnc_map        // 给规划控制模块用的地图
│   └── testdata
├── proto          // 地图各元素的消息格式(人行横道，车道线等)
├── relative_map   // 相对地图
│   ├── common
│   ├── conf
│   ├── dag
│   ├── launch
│   ├── proto
│   ├── testdata
│   │   └── multi_lane_map
│   └── tools
├── testdata       // 测试数据？
│   └── navigation_dummy
└── tools          // 工具
```

### 2.1 map.proto

首先来分析map.proto下的代码,主要分为两块，一块是头文件，另一块是对不同场景的proto文件的定义，首先来看头文件

```
message Header {
  optional bytes version = 1;//头文件版本
  optional bytes date = 2;//日期
  optional Projection projection = 3;//投影方法
  optional bytes district = 4;
  optional bytes generation = 5;
  optional bytes rev_major = 6;
  optional bytes rev_minor = 7;
  optional double left = 8;
  optional double top = 9;
  optional double right = 10;
  optional double bottom = 11;
  optional bytes vendor = 12;//供应商
}
```

其次是Map的消息格式，相比于之前的版本，Apollo 6.0增加了RSU，即路边单元。

```
message Map {
  optional Header header = 1; //上面的地图信息

  repeated Crosswalk crosswalk = 2; // 人行横道
  repeated Junction junction = 3; // 交叉路口
  repeated Lane lane = 4; // 车道线
  repeated StopSign stop_sign = 5; //停车标志
  repeated Signal signal = 6; // 信号灯
  repeated YieldSign yield = 7; // 让车标志
  repeated Overlap overlap = 8; // 重叠区域，表述不同的类型是否有重叠，
                                //比如lane 和 junction是有重叠区域的
  repeated ClearArea clear_area = 9; //禁止停车区域
  repeated SpeedBump speed_bump = 10; //减速带
  repeated Road road = 11; // 道路
  repeated ParkingSpace parking_space = 12; //停车区域
  repeated PNCJunction pnc_junction = 13; //新加入的部分，具体场景有待查询
  repeated RSU rsu = 14; // 路边单元
}
```

### 2.2 人行横道 Crosswalk.proto

接着来分析不同的场景下的proto文件, 人行横道Crosswalk

```
message Crosswalk {
  optional Id id = 1;//编号

  optional Polygon polygon = 2;//多边形

  repeated Id overlap_id = 3;//
}
```

### 2.3 交叉路口 Junction.proto

Apollo 6.0 增加了交叉路口的类型，类型用enum描述了五种场景，交叉路口的类型主要是为感知周围的车辆环境增加一些约束和参考

```
message Junction {
  optional Id id = 1;

  optional Polygon polygon = 2;

  repeated Id overlap_id = 3;
  enum Type { //交叉路口类型（有的地方叫做道路汇集点）
    UNKNOWN = 0;//未知区域
    IN_ROAD = 1;//道路非路口（具体有待继续了解）
    CROSS_ROAD = 2; //十字路口
    FORK_ROAD = 3; //三岔路口
    MAIN_SIDE = 4; //道路主侧
    DEAD_END = 5; //死胡同
  };
  optional Type type = 4;
}
```

### 2.4 车道线 Lane.proto

车道线的描述相对于其他模块比较复杂 很明显相对于之前的版本，在地图这块丰富了很多场景和类型

```
syntax = "proto2";

package apollo.hdmap;

import "modules/map/proto/map_id.proto";
import "modules/map/proto/map_geometry.proto";

message LaneBoundaryType {//车道线类型
  enum Type { //车道线边界类型 ，相比于之前版本也属于丰富的内容
    UNKNOWN = 0; //未知
    DOTTED_YELLOW = 1; //黄色虚线
    DOTTED_WHITE = 2; //白色虚线
    SOLID_YELLOW = 3; //黄色实线
    SOLID_WHITE = 4; // 白色实线
    DOUBLE_YELLOW = 5; // 双黄线
    CURB = 6; //（停车线？）
  };
  // Offset relative to the starting point of boundary
  optional double s = 1;//与起点之间的相对偏移，理解为纵向偏差？
  // support multiple types
  repeated Type types = 2;
}

message LaneBoundary {//车道线
  optional Curve curve = 1;

  optional double length = 2;
  // indicate whether the lane boundary exists in real world
  optional bool virtual = 3; //真实世界中车道线是否存在
  // in ascending order of s
  repeated LaneBoundaryType boundary_type = 4;
}

// Association between central point to closest boundary.
message LaneSampleAssociation {//中心点与最近车道线的关联
  optional double s = 1;
  optional double width = 2;
}

// A lane is part of a roadway, that is designated for use by a single line of
// vehicles.
// Most public roads (include highways) have more than two lanes.
message Lane {//车道
  optional Id id = 1;

  // Central lane as reference trajectory, not necessary to be the geometry
  // central.
  optional Curve central_curve = 2;//中心线

  // Lane boundary curve.
  optional LaneBoundary left_boundary = 3;//左边界
  optional LaneBoundary right_boundary = 4;//右边界

  // in meters.
  optional double length = 5;

  // Speed limit of the lane, in meters per second.
  optional double speed_limit = 6;//车速限制

  repeated Id overlap_id = 7;

  // All lanes can be driving into (or from).
  repeated Id predecessor_id = 8;//上一个车道的ID
  repeated Id successor_id = 9;//下一个车道的ID
                               //相当于描述了车道之间的连接关系

  // Neighbor lanes on the same direction.
  repeated Id left_neighbor_forward_lane_id = 10;
  repeated Id right_neighbor_forward_lane_id = 11;

  enum LaneType {//车道类型
    NONE = 1;//无，指的是无车道线小路吗
    CITY_DRIVING = 2;//城市道路
    BIKING = 3;//自行车道
    SIDEWALK = 4;//人行道
    PARKING = 5;//停车位
    SHOULDER = 6;//路肩
  };
  optional LaneType type = 12;

  enum LaneTurn {//转弯类型，应该指的是路面的转弯标志
    NO_TURN = 1;//直行
    LEFT_TURN = 2;//左转
    RIGHT_TURN = 3;//右转
    U_TURN = 4;//掉头
  };
  optional LaneTurn turn = 13;

  repeated Id left_neighbor_reverse_lane_id = 14;//左边相邻反向车道ID
  repeated Id right_neighbor_reverse_lane_id = 15;//右边相邻反向车道ID

  optional Id junction_id = 16;

  // Association between central point to closest boundary.
  repeated LaneSampleAssociation left_sample = 17;//中心点与最近左边界之间的关联
  repeated LaneSampleAssociation right_sample = 18;//中心点与最近右边界之间的关联

  enum LaneDirection {//车道方向
    FORWARD = 1;//向前
    BACKWARD = 2;//向后（参考博客提到可能是潮汐车道借用的情况）
    BIDIRECTION = 3;//双向
  }
  optional LaneDirection direction = 19;

  // Association between central point to closest road boundary.
  repeated LaneSampleAssociation left_road_sample = 20;
  //中心点与最近左路边界之间的关联
  repeated LaneSampleAssociation right_road_sample = 21;
  //中心点与最近右路边界之间的关联
  repeated Id self_reverse_lane_id = 22;
}
```

### 2.5 StopSign.proto 停车标志

这个指的是路边的停车标志

```
message StopSign {
  optional Id id = 1;

  repeated Curve stop_line = 2;

  repeated Id overlap_id = 3;

  enum StopType {
    UNKNOWN = 0;
    ONE_WAY = 1;//只有一个车道可以停
    TWO_WAY = 2;
    THREE_WAY = 3;
    FOUR_WAY = 4;
    ALL_WAY = 5;
  };
  optional StopType type = 4;
}
```

### 2.6 Signal.proto 信号灯

这个模块描述了规定的信号灯的类型

```
syntax = "proto2";

package apollo.hdmap;

import "modules/common/proto/geometry.proto";
import "modules/map/proto/map_geometry.proto";
import "modules/map/proto/map_id.proto";

message Subsignal {
  enum Type {//子信号灯类型，指的是每个信号灯的情况
    UNKNOWN = 1;
    CIRCLE = 2; //环线
    ARROW_LEFT = 3; //允许左转
    ARROW_FORWARD = 4;//允许直行
    ARROW_RIGHT = 5;//允许右转
    ARROW_LEFT_AND_FORWARD = 6;//允许左转和直行
    ARROW_RIGHT_AND_FORWARD = 7;//允许右转和直行
    ARROW_U_TURN = 8;//允许掉头
  };

  optional Id id = 1;
  optional Type type = 2;

  // Location of the center of the bulb. now no data support.
  optional apollo.common.PointENU location = 3;
}

message SignInfo {
  enum Type {
    None = 0;
    NO_RIGHT_TURN_ON_RED = 1;
    //一些车道最右侧车道可以直接转弯，一些车道最右侧车道有红灯限制，不能直接转弯
    //此处增加一个信号，表示没有右转红灯限制
  };

  optional Type type = 1;
}

message Signal {
  enum Type {//信号灯的结构类型，由几个什么样的信号灯组成
    UNKNOWN = 1;
    MIX_2_HORIZONTAL = 2;//两个水平信号灯
    MIX_2_VERTICAL = 3;//两个垂直信号灯
    MIX_3_HORIZONTAL = 4;//三个水平信号灯
    MIX_3_VERTICAL = 5;//三个垂直信号灯
    SINGLE = 6;//单独一个信号灯
  };

  optional Id id = 1;
  optional Polygon boundary = 2;
  repeated Subsignal subsignal = 3;
  // TODO: add orientation. now no data support.
  repeated Id overlap_id = 4;
  optional Type type = 5;
  // stop line
  repeated Curve stop_line = 6;

  repeated SignInfo sign_info = 7;
}
```

### 2.7 YieldSign.proto 让车标志

这个指的是路边的让车标志

```
syntax = "proto2";

package apollo.hdmap;

import "modules/map/proto/map_id.proto";
import "modules/map/proto/map_geometry.proto";

// A yield indicates that each driver must prepare to stop if necessary to let a
// driver on another approach proceed.
// A driver who stops or slows down to let another vehicle through has yielded
// the right of way to that vehicle.
message YieldSign {
  optional Id id = 1;

  repeated Curve stop_line = 2;

  repeated Id overlap_id = 3;
}
```

### 2.8 Overlap.proto 重叠区域

重叠区域应该算的上是经常涉及到的一个模块了，根据上方的消息类型可以看到，几乎每个message都会有一个overlap。 具体的规则还没有完善完成

```
syntax = "proto2";

package apollo.hdmap;

import "modules/map/proto/map_id.proto";
import "modules/map/proto/map_geometry.proto";

message LaneOverlapInfo {//车道重叠信息
  optional double start_s = 1;  // position (s-coordinate)//纵向起点
  optional double end_s = 2;    // position (s-coordinate)//横向起点
  optional bool is_merge = 3;//是否合并

  optional Id region_overlap_id = 4;
}
//以下的具体规则还没有详细的完善
message SignalOverlapInfo {}

message StopSignOverlapInfo {}

message CrosswalkOverlapInfo {
  optional Id region_overlap_id = 1;
}

message JunctionOverlapInfo {}

message YieldOverlapInfo {}

message ClearAreaOverlapInfo {}

message SpeedBumpOverlapInfo {}

message ParkingSpaceOverlapInfo {}

message PNCJunctionOverlapInfo {}

message RSUOverlapInfo {}

message RegionOverlapInfo {
  optional Id id = 1;
  repeated Polygon polygon = 2;
}

// Information about one object in the overlap.
message ObjectOverlapInfo {
  optional Id id = 1;

  oneof overlap_info {//重叠区域信息，具体的可以和前面的对照
    LaneOverlapInfo lane_overlap_info = 3;
    SignalOverlapInfo signal_overlap_info = 4;
    StopSignOverlapInfo stop_sign_overlap_info = 5;
    CrosswalkOverlapInfo crosswalk_overlap_info = 6;
    JunctionOverlapInfo junction_overlap_info = 7;
    YieldOverlapInfo yield_sign_overlap_info = 8;
    ClearAreaOverlapInfo clear_area_overlap_info = 9;
    SpeedBumpOverlapInfo speed_bump_overlap_info = 10;
    ParkingSpaceOverlapInfo parking_space_overlap_info = 11;
    PNCJunctionOverlapInfo pnc_junction_overlap_info = 12;
    RSUOverlapInfo rsu_overlap_info = 13;
  }
}

// Here, the "overlap" includes any pair of objects on the map
// (e.g. lanes, junctions, and crosswalks).
message Overlap {
  optional Id id = 1;

  // Information about one overlap, include all overlapped objects.
  repeated ObjectOverlapInfo object = 2;

  repeated RegionOverlapInfo region_overlap = 3;
}
```

### 2.9 ClearArea 禁停区

用来描述禁止停车的区域

```
syntax = "proto2";

package apollo.hdmap;

import "modules/map/proto/map_id.proto";
import "modules/map/proto/map_geometry.proto";

// A clear area means in which stopping car is prohibited

message ClearArea {
  optional Id id = 1;
  repeated Id overlap_id = 2;
  optional Polygon polygon = 3;
}
```

### 2.10 SpeedBump.proto 减速带

```
syntax = "proto2";

package apollo.hdmap;

import "modules/map/proto/map_id.proto";
import "modules/map/proto/map_geometry.proto";

message SpeedBump {
  optional Id id = 1;
  repeated Id overlap_id = 2;
  repeated Curve position = 3;
}
```

### 2.11 road.proto 道路

用来描述道路的信息

```
syntax = "proto2";

package apollo.hdmap;

import "modules/map/proto/map_geometry.proto";
import "modules/map/proto/map_id.proto";

message BoundaryEdge {
  optional Curve curve = 1;
  enum Type {//用来描述路面信息
             //有一些路面是标准的，而有一些路面，只有一侧有分界线，或者没有分界线
    UNKNOWN = 0;
    NORMAL = 1;//标准路面
    LEFT_BOUNDARY = 2;//左分界线
    RIGHT_BOUNDARY = 3;//右分界线
  };
  optional Type type = 2;
}

message BoundaryPolygon {
  repeated BoundaryEdge edge = 1;
}

// boundary with holes
message RoadBoundary {
  optional BoundaryPolygon outer_polygon = 1;
  // if boundary without hole, hole is null
  repeated BoundaryPolygon hole = 2;
}

message RoadROIBoundary {
  optional Id id = 1;
  repeated RoadBoundary road_boundaries = 2;
}

// road section defines a road cross-section, At least one section must be
// defined in order to
// use a road, If multiple road sections are defined, they must be listed in
// order along the road
message RoadSection {
  optional Id id = 1;
  // lanes contained in this section
  repeated Id lane_id = 2;
  // boundary of section
  optional RoadBoundary boundary = 3;
}

// The road is a collection of traffic elements, such as lanes, road boundary
// etc.
// It provides general information about the road.
message Road {
  optional Id id = 1;
  repeated RoadSection section = 2;

  // if lane road not in the junction, junction id is null.
  optional Id junction_id = 3;

  enum Type {//道路类型
    UNKNOWN = 0;
    HIGHWAY = 1;//高速公路
    CITY_ROAD = 2;//城市道路
    PARK = 3;//停车位
  };
  optional Type type = 4;
}
```

### 2.12 ParkingSpace.proto 停车区域

用来描述停车区域信息

```
message ParkingSpace {
  optional Id id = 1;

  optional Polygon polygon = 2;

  repeated Id overlap_id = 3;

  optional double heading = 4;
}

// ParkingLot is a place for parking cars.
message ParkingLot {
  optional Id id = 1;

  optional Polygon polygon = 2;

  repeated Id overlap_id = 3;
}
```

### 2.13 PNCJunction.proto

这里的PNC根据消息内容推测可能是通道，具体的还要根据规划分析一下

```
package apollo.hdmap;

import "modules/map/proto/map_id.proto";
import "modules/map/proto/map_geometry.proto";
message Passage {
  optional Id id = 1;

  repeated Id signal_id = 2;
  repeated Id yield_id = 3;
  repeated Id stop_sign_id = 4;
  repeated Id lane_id = 5;

  enum Type {
    UNKNOWN = 0;
    ENTRANCE = 1;// 入口
    EXIT = 2;// 出口
  };
  optional Type type = 6;
};

message PassageGroup {
  optional Id id = 1;

  repeated Passage passage = 2;
};

message PNCJunction {
  optional Id id = 1;

  optional Polygon polygon = 2;

  repeated Id overlap_id = 3;

  repeated PassageGroup passage_group = 4;
}
```

### 2.14 RSU.proto 路边单元

猜测是路边单元，具体的信息还有待后续的学习

```
syntax = "proto2";

package apollo.hdmap;

import "modules/map/proto/map_id.proto";

message RSU {
  optional Id id = 1;
  optional Id junction_id = 2;
  repeated Id overlap_id = 3;
};
```

### 2.15 map_geometry.proto 地图几何形状

这个里面地图的几何形状主要是分为直线和曲线

```
syntax = "proto2";

import "modules/common/proto/geometry.proto";

package apollo.hdmap;

// Polygon, not necessary convex.
message Polygon {
  repeated apollo.common.PointENU point = 1;
}

// Straight line segment.
message LineSegment {//直线段
  repeated apollo.common.PointENU point = 1;
}

// Generalization of a line.
message CurveSegment {//曲线段
  oneof curve_type {
    LineSegment line_segment = 1;
  }
  optional double s = 6;  // start position (s-coordinate)
  optional apollo.common.PointENU start_position = 7;//起始点
  optional double heading = 8;  // start orientation //起始方向
  optional double length = 9;//长度
}

// An object similar to a line but that need not be straight.
message Curve {
  repeated CurveSegment segment = 1;
}
```

### 2.16 Id.roto地图编号

这个ID是独一无二的

```
syntax = "proto2";

package apollo.hdmap;

// Global unique ids for all objects (include lanes, junctions, overlaps, etc).
message Id {
  optional string id = 1;
}
```

### 2.17 SpeedControl.proto 车速限制

```
syntax = "proto2";

import "modules/map/proto/map_geometry.proto";

package apollo.hdmap;

// This proto defines the format of an auxiliary file that helps to
// define the speed limit on certain area of road.
// Apollo can use this file to quickly fix speed problems on maps,
// instead of waiting for updating map data.
message SpeedControl {
  optional string name = 1;
  optional apollo.hdmap.Polygon polygon = 2;
  optional double speed_limit = 3;
}

message SpeedControls {
  repeated SpeedControl speed_control = 1;
}
```

## 总结

可以看得出来，Apollo 6.0相比于之前的，在地图这块增加了不少东西，对各个场景也进行了很详细的分类

这个部分主要要学习两块

```
   1.首先是Proto文件的作用和基本格式，方便看懂消息内容
   2.其次是每个消息具体有哪些东西，方便规划方面不断完善
```