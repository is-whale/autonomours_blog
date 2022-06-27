- [Apollo 6.0 pnc_map解析 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/419350318)

pnc_map在Apollo中属于相对独立的一块内容，作为hd_map与planning的中间层，在生成reference_line即规划参考线的时候会使用到。换句话说，pnc_map就是解析routing结果，再由每个路由段转换为reference_line的数据形式，这是它的主要功能。实现这一模块的目的也是为了让规划模块的独立性更高，避免与高精地图的数据格式所耦合，导致可迁移性变差。而提到pnc_map，也就不得不提到Apollo规划中最重要的数据结构reference_line，但这篇文章还是想将重点放在pnc_map所实现的相关功能上，在reference_line_provider中如何调用，在planning的主循环中如何创建reference_line等等，之后会单独再写文章进行分析。

想写这部分内容的主要原因就是最近在写规划算法时，直接从地图中读到的道路信息过于粗糙，需要经过一系列处理才能给到规划使用，便想要在地图与规划之间再加上一层地图数据处理模块，也使得规划算法更加独立。想到这里，便联想到了Apollo中的pnc_map，之前一直没有深入研究过，趁着这次机会也深入了解下大厂是如何处理该问题的。

## 一、相关数据结构

首先，需要了解pnc_map中用到的几个地图数据结构。因为pnc_map中一个重要功能就是处理routing数据，因此首先分析下routing的proto文件

1. **RoutingRequest**

```protobuf
message RoutingRequest {
  optional apollo.common.Header header = 1;
  // at least two points. The first is start point, the end is final point.
  // The routing must go through each point in waypoint.
  repeated LaneWaypoint waypoint = 2;
  repeated LaneSegment blacklisted_lane = 3;
  repeated string blacklisted_road = 4;
  optional bool broadcast = 5 [default = true];
  optional apollo.hdmap.ParkingSpace parking_space = 6 [deprecated = true];
  optional ParkingInfo parking_info = 7;
}
```

RoutingRequest为dreamview上发出的routing请求点，主要关注waypoint这个数据类型，表示routing算法导航出的路径需经过这些waypoint点。

```protobuf
message LaneWaypoint {
  optional string id = 1;
  optional double s = 2;
  optional apollo.common.PointENU pose = 3;
}
```

LaneWayPoint为地图道路上的一个点，包含了道路id，s值，全局坐标信息。

**2. RoutingResponse**

```protobuf
message RoutingResponse {
  optional apollo.common.Header header = 1;
  repeated RoadSegment road = 2;
  optional Measurement measurement = 3;
  optional RoutingRequest routing_request = 4;

  // the map version which is used to build road graph
  optional bytes map_version = 5;
  optional apollo.common.StatusPb status = 6;
}
```

RoutingResponse表示的是routing的结果信息，主要由多段RoadSegment组成。

```protobuf
message RoadSegment {
  optional string id = 1;
  repeated Passage passage = 2;
}

message Passage {
  repeated LaneSegment segment = 1;
  optional bool can_exit = 2;
  optional ChangeLaneType change_lane_type = 3 [default = FORWARD];
}

message LaneSegment {
  optional string id = 1;
  optional double start_s = 2;
  optional double end_s = 3;
}
```

RoadSegment又由多段Passage组成，而Passage又由多段LaneSegment组成，LaneSegment就是最基础的车道数据类型，包含了道路id，起始点终点s值信息。

------

除了routing相关的数据结构外，pnc_map也用到了地图模块中所定义的几种基础数据类型。

1. **hdmap::LaneWaypoint**

```cpp
struct LaneWaypoint {
 LaneWaypoint() = default;
 LaneWaypoint(LaneInfoConstPtr lane, const double s)
      : lane(CHECK_NOTNULL(lane)), s(s) {}
 LaneWaypoint(LaneInfoConstPtr lane, const double s, const double l)
      : lane(CHECK_NOTNULL(lane)), s(s), l(l) {}
 LaneInfoConstPtr lane = nullptr;
 double s = 0.0;
 double l = 0.0;

 std::string DebugString() const;
};
```

LaneWayPoint与routing.proto中的LaneWayPoint有一些区别，是hdmap中直接拿取的道路点，包含道路信息以及s和l信息

**2. hdmap::RouteSegments**

RouteSegments可以理解为routing中的passage信息，也是由多段LaneSegment组成，在pnc_map中会将routing结果的passage信息转换为RouteSegments，后面会讲到，说明两者其实可以看做同一东西。

```cpp
class RouteSegments : public std::vector<LaneSegment>
```

RouteSegments这个数据类型非常重要，其包含了pnc_map的主要功能及输出结果，这个类中也有许多对路由段进行处理的函数，为之后更好地生成ReferenceLine做准备，后面会挑几个重点的来讲一下。

**3. hamap::LaneSegment**

这就是hdmap中最基础的道路数据类型，和routing.proto中的LaneSegment类似，但包含了更多的信息。

```cpp
struct LaneSegment {
 LaneSegment() = default;
 LaneSegment(LaneInfoConstPtr lane, const double start_s, const double end_s)
      : lane(CHECK_NOTNULL(lane)), start_s(start_s), end_s(end_s) {}
 LaneInfoConstPtr lane = nullptr;
 double start_s = 0.0;
 double end_s = 0.0;
 double Length() const { return end_s - start_s; }

  /**
   * Join neighboring lane segments if they have the same lane id
   */
 static void Join(std::vector<LaneSegment>* segments);

 std::string DebugString() const;
};
```

## 二、pnc_map的主要步骤

pnc_map封装在reference_line_provider下，主要供其在由routing结果到ReferenceLine的过程中做前期准备，而实现这一功能的主要步骤分为两步：

1. 处理routing结果，将routing数据处理并存储在map相关的数据结构中，供后续使用
2. 根据routing结果及当前车辆位置，计算可行驶的passage信息，并转换成RouteSegments结构

在这之后，reference_line_provider 里会将RouteSegments转换为hdmap::Path结构，再转成ReferenceLine结构，继而进行平滑，后面我单独写ReferenceLine时会讲到，不在本篇文章的讨论范围之内。接下来我会详细阐述下上面两个步骤的代码流程。

1. **PncMap::UpdateRoutingResponse()**

首先，每当有新的routing结果时，都需要用最新的RoutingResponse对PncMap里的数据进行更新。这块的代码并不复杂，用三个for循环分别剥离出routing结果里的road，passage，lane，将每条lane的id存进all_lane_ids这个数组中；将每个lane_segment及{road_index, passage_index, lane_index} 存进**route_indices_**中。route_indices_该数组非常重要，后续会频繁使用到它，读者需要记一下里面存储的数据格式。

```cpp
  for (int road_index = 0; road_index < routing.road_size(); ++road_index) {
    const auto &road_segment = routing.road(road_index);
    for (int passage_index = 0; passage_index < road_segment.passage_size();
         ++passage_index) {
      const auto &passage = road_segment.passage(passage_index);
      for (int lane_index = 0; lane_index < passage.segment_size();
           ++lane_index) {
        all_lane_ids_.insert(passage.segment(lane_index).id());
        route_indices_.emplace_back();
        route_indices_.back().segment =
            ToLaneSegment(passage.segment(lane_index));
        if (route_indices_.back().segment.lane == nullptr) {
          AERROR << "Failed to get lane segment from passage.";
          return false;
        }
        route_indices_.back().index = {road_index, passage_index, lane_index};
      }
    }
  }
```

接着，将routing结果的lane_id存放进range_lane_ids_中

```cpp
  range_start_ = 0;
  range_end_ = 0;
  adc_route_index_ = -1;
  next_routing_waypoint_index_ = 0;
  UpdateRoutingRange(adc_route_index_);
```

最后，将routing_request中的waypoint信息存储进routing_waypoint_index_中，这样routing信息的转换也就全部结束了。

```cpp
routing_waypoint_index_.clear();
  const auto &request_waypoints = routing.routing_request().waypoint();
  if (request_waypoints.empty()) {
    AERROR << "Invalid routing: no request waypoints.";
    return false;
  }
  int i = 0;
  for (size_t j = 0; j < route_indices_.size(); ++j) {
    while (i < request_waypoints.size() &&
           RouteSegments::WithinLaneSegment(route_indices_[j].segment,
                                            request_waypoints.Get(i))) {
      routing_waypoint_index_.emplace_back(
          LaneWaypoint(route_indices_[j].segment.lane,
                       request_waypoints.Get(i).s()),
          j);
      ++i;
    }
  }
```

其中，存储routing结果的几个较重要的变量定义如下：

```cpp
  struct RouteIndex {
    LaneSegment segment;
    std::array<int, 3> index;
  };
  std::vector<RouteIndex> route_indices_;

  // routing ids in range
  std::unordered_set<std::string> range_lane_ids_;
  std::unordered_set<std::string> all_lane_ids_;

  /**
   * The routing request waypoints
   */
  struct WaypointIndex {
    LaneWaypoint waypoint;
    int index;
    WaypointIndex(const LaneWaypoint &waypoint, int index)
        : waypoint(waypoint), index(index) {}
  };

  std::vector<WaypointIndex> routing_waypoint_index_;
```

**2. PncMap::GetRouteSegments()**

该函数为pnc_map的第二个步骤，也是**其主要功能**，即根据第一步骤转换的routing信息及自车位置来构造route_segments，这个我们从该函数的入参也可以很清晰的看出来。

```cpp
bool PncMap::GetRouteSegments(const VehicleState &vehicle_state,
                              const double backward_length,
                              const double forward_length,
                              std::list<RouteSegments> *const route_segments)
```

这个函数比较复杂，我分成几个子任务来进行讲解。

- **UpdateVehicleState()**

这里的更新VehicleState并不是更新车辆的位置、速度这些状态，而是根据自车状态，从routing中查找到最近的道路点**adc_waypoint**，并根据adc_waypoint来在route_indices_中查找到当前行驶至了routing的哪条LaneSegment上，并将index赋给**adc_route_index_**（由GetWaypointIndex()函数计算得出）。另外，计算自车下一个必经的waypoint点，并赋给**next_routing_waypoint_index_**（由UpdateNextRoutingWaypointIndex()函数计算得出）。具体的计算代码因为比较长这里也就不贴出了，里面的逻辑还是比较清晰的，读者可以自行查看下，后续需要用到的变量如下所示：

```cpp
  /**
   * The next routing request waypoint index in routing_waypoint_index_
   */
  std::size_t next_routing_waypoint_index_ = 0;  
  /**
   * A three element index: {road_index, passage_index, lane_index}
   */
  int adc_route_index_ = -1;
  /**
   * The waypoint of the autonomous driving car
   */
  LaneWaypoint adc_waypoint_;
```

- **GetNeighborPassages()**

在上一步更新车辆状态中，根据路由查询响应以及车辆状态可以得到当前车辆在routing中的位置adc_waypoint_，以及下一个必经查询点的索引next_routing_waypoint_index_，接下来便要计算当前车道的所有相邻passage的信息。从该函数的入参可以看到，输入为自车所在的road信息以及起始的passage信息。

```cpp
std::vector<int> PncMap::GetNeighborPassages(const routing::RoadSegment &road,
                                             int start_passage) const
```

相邻车道的判断有几个依据：

1. 如果当前车道是FORWARD直行车道，无需变道，则返回当前车道
2. 如果当前车道即将退出（can_exit为true），则表示即将驶入下一个passage，则返回当前车道
3. 如果下一个waypoint在当前车道上，则必须到达下一个毕竟waypoint后才能变道，因此返回当前车道
4. 如果当前车道为左转或右转车道，则从hdmap中查询当前车道对应左侧或者右侧的所有车道，然后去和当前RoadSegment.passage()去做对比，找到两者共同包含的车道，就是最终的邻接车道。

这块的逻辑比较简单，代码也就不贴了，读者可以自行查看。这里我想单独提一下**PassageToSegments()**这个函数，我前面有说过passage表达的数据内容和RouteSegments其实是类似的，从该函数的转换中我们可以看出，RouteSegments就是将Passage中的一条条lane，存进其数组里，并记录起始终止的s值。

```cpp
bool PncMap::PassageToSegments(routing::Passage passage,
                               RouteSegments *segments) const {
  CHECK_NOTNULL(segments);
  segments->clear();
  for (const auto &lane : passage.segment()) {
    auto lane_ptr = hdmap_->GetLaneById(hdmap::MakeMapId(lane.id()));
    if (!lane_ptr) {
      AERROR << "Failed to find lane: " << lane.id();
      return false;
    }
    segments->emplace_back(lane_ptr, std::max(0.0, lane.start_s()),
                           std::min(lane_ptr->total_length(), lane.end_s()));
  }
  return !segments->empty();
}
```

- **route_segments生成**

pnc_map的最后一步就是生成route_segments。首先对上一步生成的邻近passage进行遍历，转为RouteSegments结构后，计算其sl坐标。接下来，判断每一个相邻的passage能否从当前车道驶入，判断的依据为：

1. 如果adc_waypoint_在该passage上，则可以
2. 如果adc_waypoint_无法在该passage上计算出投影，即SL坐标，则不行； 如果能计算出SL坐标，但是L值过大，说明车辆横向距离太大，可能横跨了好几个车道，那么不可驶入
3. adc_waypoint对应的heading角与该passage对应的heading角，相差不能超过90度
4. 当前车辆所在车道和投影到passage中对应的LaneSegment所属车道必须是相邻的，不能跨车道驶入(每次只能变道一次，无法连续变道)。

进行完合法性检查后，满足条件的segments再向前扩展forward_length，向后扩展backward_length，这里面牵涉的细节比较多，本篇文章就不再展开讨论了，之后应该会单独再写一篇文章进行分析。

## 三、总结

这样，Apollo的pnc_map模块的大体框架就讲解完了。**总结来说，pnc_map的作用就是将routing的结果，进行一系列的操作处理，找到一个局部的（固定长度）、可供规划使用的、车辆能正常驶入的局部地图（RouteSegments），后续用于生成规划中的ReferenceLine结构。**

这篇文章我前前后后写了很长时间，这块的代码量比较庞大，对于Apollo的地图格式，规划代码框架，代码风格等等需要有较深的理解才能完全理解清楚，我在写文章时也经常会被绕进去。本篇文章建议收藏后，对照代码仔细阅读，刚开始缕清框架即可，细节可以慢慢去理顺，希望对大家有所帮助，大家有任何疑问或是文章中写错的地方，也欢迎在评论区提出指正。

最后，也欢迎读者关注我，后续会不定期分享规划控制算法~

参考资料：

[1] [pnc_map模块（规划与控制地图）_lzw0107的博客-CSDN博客](https://link.zhihu.com/?target=https%3A//blog.csdn.net/lzw0107/article/details/107814610%23t1)

[2] [解析百度 Apollo 之 Routing 模块](https://link.zhihu.com/?target=https%3A//www.h3399.cn/201902/658516.html)