- [Apollo 7.0 规划算法框架解析 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/462665362)

由于Apollo的planning整体代码都相当庞大，一开始还是理清其框架，再分算法块逐个击破这样效果更好。这篇文章就想带领读者理清planning的整体框架，梳理数据流，以主要场景为例，一直分析到task (apollo planning 的主要算法所在处)逻辑前的准备工作、输入如何构造的，之后再深入看task内部的细节也更容易理解。**本篇文章作者花费了大量时间来梳理流程图，以便读者能够更易理解planning的整体框架，抓住重点不被细节带跑偏，建议大家点赞收藏后常拿出来翻翻，有任何错误或疑问也都欢迎在评论区留言。**

## **一、planning 的输入输出**

读懂一个模块，首先必然是了解其的上下游，即输入输出是什么。熟悉Apollo CyberRT框架的小伙伴都知道在该框架下，输入输出由Reader和Writer构成，并定义在每个模块的component文件中。除此之外，CyberRT框架定义了两种模式，分别为消息触发和时间触发，而planning中采用的为消息触发，因此必须接到特定的上游消息后，才会进入内部主逻辑，而消息触发的上游消息，定义为component中Process()函数的入参。

```cpp
  std::shared_ptr<cyber::Reader<perception::TrafficLightDetection>>
      traffic_light_reader_;
  std::shared_ptr<cyber::Reader<routing::RoutingResponse>> routing_reader_;
  std::shared_ptr<cyber::Reader<planning::PadMessage>> pad_msg_reader_;
  std::shared_ptr<cyber::Reader<relative_map::MapMsg>> relative_map_reader_;
  std::shared_ptr<cyber::Reader<storytelling::Stories>> story_telling_reader_;

  std::shared_ptr<cyber::Writer<ADCTrajectory>> planning_writer_;
  std::shared_ptr<cyber::Writer<routing::RoutingRequest>> rerouting_writer_;
  std::shared_ptr<cyber::Writer<PlanningLearningData>>
      planning_learning_data_writer_;
```



```cpp
bool Proc(const std::shared_ptr<prediction::PredictionObstacles>& prediction_obstacles,
 const std::shared_ptr<canbus::Chassis>& chassis,
 const std::shared_ptr<localization::LocalizationEstimate>& localization_estimate) override;
```

因此总结来看，planning的上下游关系总结为下图：

![img](https://pic1.zhimg.com/80/v2-c5620e3995f61c3787588cdb3936d19c_720w.jpg)

这里再重复一下，planning的输入分为Reader和Process()入参的原因在于，planning依赖于Process()的三个上游输入，只有同时接到这三个输入，才会触发planning的主逻辑，即是planning正常启动的必要条件。而Reader则不是，其中部分上游还依赖于配置参数是否打开，具体可以查看Apollo的源码。

planning的输出就比较简单了，主要是给控制的ADCTrajectory数据，包含了一条带时间、速度的轨迹点集，具体的格式定义可以查看对应的proto文件。

## 二、planning 整体框架

![img](https://pic3.zhimg.com/80/v2-cae9bfc7d17aa2bce812aefd5f5da166_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-8e3726d179eb3033f0bc9b88b3777e37_720w.jpg)

上面两张流程图是我整理的Apollo规划算法的框架，可以整体框架和之前并无太大变化。主框架分为两个线程，子线程ReferenceLineProvider以20HZ的频率运行，用于计算planning中最重要的数据结构reference_line；主线程上还是基于场景划分的思路，多数场景下还是采用基于ReferenceLine的规划算法，对于泊车相关场景，则利用open space算法。目前Apollo的场景划分为了16种，在proto文件中可以查看到。在Apollo 7.0中，新增了deadend_turnaround场景，用于无人车遇到断头路时，采用openspace的方法进行调头的轨迹规划，后续我会详细看一下里面的算法实现细节。

![img](https://pic4.zhimg.com/80/v2-4aebcdf5edb4d54bb93e7422e92aeaeb_720w.jpg)

## 三、planning中的场景管理

Apollo中的规划里的一个重要思想就是基于场景划分来调用不同的task处理，而其中如何进行场景分配便是实现该思想的核心。从上面的配置参数可以看到目前Apollo设定了16种场景，而场景下的具体切换逻辑也比较复杂，scenario_manager的具体流程逻辑如下图所示：

![img](https://pic3.zhimg.com/80/v2-8a3e5f36e793db79f922133dfcd360c6_720w.jpg)

在上图红框中的场景判断中，我只画了第一步的场景判断，红框内的5种场景为大类，剩余的场景在这5大类中再细分做出判断。另外需要注意的是，红框内的5种场景是有优先级顺序的，即如果判断为某种场景后，后续的场景也就不再判断。下面从这5种场景出发，介绍一下Apollo中的场景判断条件，以及每个大类场景下包含哪些细分的场景小类。

### **1. ParkAndGo**

```cpp
  // ParkAndGo / starting scenario
  if (scenario_type == default_scenario_type_) {
    if (FLAGS_enable_scenario_park_and_go && !reach_target_pose_) {
      scenario_type = SelectParkAndGoScenario(frame);
    }
  }
```

该场景的判断条件为车辆是否静止，并且距离终点10m以上，并且当前车辆已经off_lane或者不在城市道路上，在该场景下采用的是open_space相关的算法。个人感觉该场景在驶离目标车道并正常规划失败导致的停车时会触发，利用open_space方法使其重新回到正常道路上，因此也是场景判断中首先需要check的。

```cpp
const double adc_distance_to_dest = dest_sl.s() - adc_front_edge_s;
  // if vehicle is static, far enough to destination and (off-lane or not on
  // city_driving lane)
  if (std::fabs(adc_speed) < max_abs_speed_when_stopped &&
      adc_distance_to_dest > scenario_config.min_dist_to_dest() &&
      (HDMapUtil::BaseMap().GetNearestLaneWithHeading(
           adc_point, 2.0, vehicle_state.heading(), M_PI / 3.0, &lane, &s,
           &l) != 0 ||
       lane->lane().type() != hdmap::Lane::CITY_DRIVING)) {
    park_and_go = true;
  }
```

### **2. Intersection**

在该场景下，又可细分四个场景。首先，根据先前计算的地图中第一个遇到的overlap来确定大类型，是包含交通标识的交叉口，还是其他交叉口。其次，若是包含交通标识的交叉口，还细分为stop_sign、traffic_light以及yield_sign，具体结构图如下所示。

![img](https://pic2.zhimg.com/80/v2-ee9d5458c4dea194ebbf99d39e281741_720w.jpg)

### **3. PullOver**

PullOver场景即靠边停车，需要满足以下条件才可切换到该场景：

- 不在change_line的时候，即reference_line只有一条
- 当前位置距离终点在一定范围内并且满足pullover可以执行的最短距离
- 地图中能够找到pullover的位置
- 终点的位置不在交叉路口附近
- 能查找到最右边车道的lane_type，并且该车道允许pullover
- 只有从lane_follow场景下才能切换到pullover

大体逻辑如上述所示，具体的参数设置可以查看代码。

### **4. ValetParking**

ValetParking场景即代客泊车，判断逻辑如下：

- 从routing中得到target_parking_spot_id
- 从地图中搜索是否存在path能够抵达该parking_spot
- 查询当前位置至parking_spot的距离，满足条件即可切换至该场景

### **5. DeadEnd**

Apollo 7.0中新增的断头路场景，增加了"三点掉头"功能，增加了驶入驶出的能力，扩展了城市路网运营边界。"三点掉头"功能基于open space planner框架，包含以下几个部分：断头路场景转换、开放空间ROI构建、掉头轨迹规划。下列图片展现了从进入DeadEnd到驶离DeadEnd的整个过程，后续我也会详细了解该算法实现逻辑。

![img](https://pic2.zhimg.com/80/v2-395b30b5f9e19e0a57ba1b9855f8dbc9_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-8203b1245216371045619a51b5006a8c_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-2aa9e9d6866c5b428f23733a32af8e3e_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-5fd386761c77b752f039780fb38dc246_720w.jpg)

上面就是Apollo中的场景切换及管理逻辑。在每个场景scenario下，还分为一个或多个stage，而每个stage下面，又划分了多个task来完成相应的规划任务。以用到最多的lane_follow场景为例，它就包含了一个stage——lane_follow_default_stage，而在这个stage下包含了多个task，如下图所示：

![img](https://pic3.zhimg.com/80/v2-cb9f895bdd57b0c8208cb23ae676e8b2_720w.jpg)

仔细查看lane_follow场景下的task，我们可以看出Apollo的规划思路也是横纵向解耦，先规划path，再规划speed。具体的，对于path来说，先做出是否需要lane_change或者lane_borrow的决策，再根据决策状态来生成凸空间，最终基于reference_line及凸空间求解一个二次优化问题，从而得到优化后的path。对于speed来说，是基于ST图进行DP+QP的优化方法，先利用DP来找到一个cost值最小的可行解，再利用QP对可行解进行平滑，得到最终平滑后的ST图点集。最终，基于s值对path和speed进行融合，得到一条平滑的轨迹。

## 四、planning中重要的数据结构

在具体深入到Apollo的规划算法之前，首先需要讲清楚算法里贯穿始终的几个重要数据结构。只有搞懂了这些数据结构，才能理清楚具体算法的输入输出，后续看代码也会事半功倍。

### LocalView结构体

LocalView这个结构体中包含了planning的所有上游输入数据：包含了预测障碍物及其轨迹、底盘信息、定位信息、交通灯信息、导航信息、相对地图等，一帧规划中所会用到的所有外部输入都被包含进了该结构体中，其会在component的Process()函数的一开始便进行赋值。

```cpp
/**
 * @struct local_view
 * @brief LocalView contains all necessary data as planning input
 */

struct LocalView {
  std::shared_ptr<prediction::PredictionObstacles> prediction_obstacles;
  std::shared_ptr<canbus::Chassis> chassis;
  std::shared_ptr<localization::LocalizationEstimate> localization_estimate;
  std::shared_ptr<perception::TrafficLightDetection> traffic_light;
  std::shared_ptr<routing::RoutingResponse> routing;
  std::shared_ptr<relative_map::MapMsg> relative_map;
  std::shared_ptr<PadMessage> pad_msg;
  std::shared_ptr<storytelling::Stories> stories;
};
```

### ReferenceLineInfo类

ReferenceLineInfo类可以说是planning当中最重要的类，其包含了ReferenceLine类，为车辆行驶的参考线；还包含了blocking_obstacles_，即在该条参考线上阻塞车道的障碍物；还有优化出的path_data、speed_data以及最终融合后的轨迹discretized_trajectory。在以参考线为主的规划算法中，该类包含了所有算法中用到的信息及结果，后续也会着重讲到。

```cpp
private:
  static std::unordered_map<std::string, bool> junction_right_of_way_map_;
  const common::VehicleState vehicle_state_;
  const common::TrajectoryPoint adc_planning_point_;
  ReferenceLine reference_line_;

  /**
   * @brief this is the number that measures the goodness of this reference
   * line. The lower the better.
   */
  double cost_ = 0.0;
  bool is_drivable_ = true;
  PathDecision path_decision_;
  Obstacle* blocking_obstacle_;
  std::vector<PathBoundary> candidate_path_boundaries_;
  std::vector<PathData> candidate_path_data_;
  PathData path_data_;
  PathData fallback_path_data_;
  SpeedData speed_data_;
  DiscretizedTrajectory discretized_trajectory_;
  RSSInfo rss_info_;

  /**
   * @brief SL boundary of stitching point (starting point of plan trajectory)
   * relative to the reference line
   */
  SLBoundary adc_sl_boundary_;
```

### ReferenceLine类

ReferenceLine类即为规划的参考线，其中包含了地图给出的原始中心线map_path，以及后续优化的基准参考点，一系列的reference_points。

```cpp
 private:
  struct SpeedLimit {
    double start_s = 0.0;
    double end_s = 0.0;
    double speed_limit = 0.0;  // unit m/s
    SpeedLimit() = default;
    SpeedLimit(double _start_s, double _end_s, double _speed_limit)
        : start_s(_start_s), end_s(_end_s), speed_limit(_speed_limit) {}
  };
  /**
   * This speed limit overrides the lane speed limit
   **/
  std::vector<SpeedLimit> speed_limit_;
  std::vector<ReferencePoint> reference_points_;
  hdmap::Path map_path_;
  uint32_t priority_ = 0;
```

### Frame类

Frame类包含了planning一次循环中所用到的所有信息，可以说这个数据结构贯穿了planning的主逻辑，上述讲到的所有结构体也都被包含在了这个类中，我们可以看下里面包含的变量。有LocalView、地图信息hd_map、规划初始点、车辆状态信息、ReferenceLineInfo信息、障碍物信息、交通灯信息等等，它也是后续planner基类的入参，所有的输入信息、输出信息都包含其中。

```cpp
 private:
  static DrivingAction pad_msg_driving_action_;
  uint32_t sequence_num_ = 0;
  LocalView local_view_;
  const hdmap::HDMap *hdmap_ = nullptr;
  common::TrajectoryPoint planning_start_point_;
  common::VehicleState vehicle_state_;
  std::list<ReferenceLineInfo> reference_line_info_;
  bool is_near_destination_ = false;

  /**
   * the reference line info that the vehicle finally choose to drive on
   **/
  const ReferenceLineInfo *drive_reference_line_info_ = nullptr;
  ThreadSafeIndexedObstacles obstacles_;
  std::unordered_map<std::string, const perception::TrafficLight *>
      traffic_lights_;
  // current frame published trajectory
  ADCTrajectory current_frame_planned_trajectory_;
  // current frame path for future possible speed fallback
  DiscretizedPath current_frame_planned_path_;
  const ReferenceLineProvider *reference_line_provider_ = nullptr;
  OpenSpaceInfo open_space_info_;
  std::vector<routing::LaneWaypoint> future_route_waypoints_;
  common::monitor::MonitorLogBuffer monitor_logger_buffer_;
```

## 五、ReferenceLineProvider

有了上面的知识铺垫，我们知道planning中的主要逻辑都蕴含在task中，而在进入到task处理之前，有许多的准备工作需要做，许多的数据需要处理，才能让task中更加方便合理地只处理所赋予的一个个小功能。从文章一开始我整理的planning流程图中我们可以看到，具体进入planner的Plan()函数之前，其中最重要的一个步骤就是子进程中对于reference_line的构造，可以说后续planning的算法逻辑全都是基于这些reference_line来完成的，这一节我们就来看一下参考线是如何生成的。

![img](https://pic1.zhimg.com/80/v2-d25e99ba00c8b106933a27e40f735ce4_720w.jpg)

上图是我整理出来的子进程ReferenceLineProvider中所完成的所有逻辑，主要的操作都集中在CreateReferenceLine这个函数中，下面我们分步骤来细读一下这个函数。

### **pnc_map**

pnc_map即规划控制地图，在planning中属于相对独立的一块功能，其主要目的在于将routing给到的原始地图车道信息转换成规划可以用的ReferenceLine信息。其中的逻辑比较复杂，我之前也写过一篇文章来讲解，里面的具体逻辑可以看下下面这篇文章。

[steve：Apollo 6.0 pnc_map解析37 赞同 · 6 评论文章![img](https://pic1.zhimg.com/v2-3a39e1a41bfeae46ee3ff0e56a6905c4_180x120.jpg)](https://zhuanlan.zhihu.com/p/419350318)

简单来说，实现pnc_map的功能分为两步

1. 处理routing结果，将routing数据处理并存储在map相关的数据结构中，供后续使用
2. 根据routing结果及当前车辆位置，计算可行驶的passage信息，并转换成RouteSegments结构

其实从后续的SmoothRouteSegment函数中，我们可以看到RouteSegments结构是可以转换至ReferenceLine结构的。因此我们需要知道的是，pnc_map的输出为RouteSegments，而这与planning中的ReferenceLine已无太多区别。

![img](https://pic4.zhimg.com/80/v2-3bac9064ebefa21e26f6a7241bed578f_720w.png)

### 参考线拼接及平滑

拿到pnc_map的结果之后，下一步就要对原始的地图中心线信息进行平滑。这里分为两块逻辑，如果当前routing为新routing或者**未启用**reference_line_stitching功能的话，就走流程图中的左边分支，进行SmoothRouteSegments和Shrink操作；若不是，则走流程图中的右侧分支，进行ExtendReferenceLine操作，其中也包含了对参考线的平滑，下面我来分别讲下这两者的逻辑。

```cpp
bool ReferenceLineProvider::ExtendReferenceLine(const VehicleState &state,
                                                RouteSegments *segments,
                                                ReferenceLine *reference_line)
```

参考线的拼接函数为ExtendReferenceLine，其主要功能在于每一帧的规划大多数情况下参考线的很大一部分区域是可以复用的，这样只需要对这帧延长的部分加入进上一帧的参考线即可。拼接的好处在于一是可以减少许多的重复计算，参考线的平滑的优化变量越多就越耗时，只对延长部分进行平滑操作显然节省了大量计算时间；另一方面在于保证参考线的稳定性，作为后续优化算法的重要基础，参考线前后帧的稳定性非常重要，拼接可以减少后续轨迹层的跳变。

ExtendReferenceLine的主要步骤如下：

1. 判断新的route_segments与上一帧的route_segments是否包含了共同点，若不包含则无法拼接，平滑新的route_segments后返回
2. 用当前位置在上一帧route_segments中查询投影点，若未查到则平滑后返回
3. 查看上一帧route_segments的长度是否满足需求，若满足则无需extend，平滑后返回
4. 调用pnc_map下的ExtendSegments()函数，对route_segments查找前继及后继车道，从而实现道路段拓展。这块是参考线拼接的核心算法，里面的逻辑也比较清晰，这里由于篇幅原因就不展开细讲了，感兴趣的读者可以自行查看源码，有任何问题也欢迎在评论区留言
5. 如果扩展后的route_segments的最后一个点还在上一帧的route_segments上，则直接将上一帧的route_segments返回，此时已无法再扩展道路段
6. SmoothPrefixedReferenceLine()，平滑前置被强制锁定的reference_line，即上一帧重复的点无需再重复平滑了
7. reference_line->Stitch(*prev_ref)，对reference_line进行拼接
8. shifted_segments.Stitch(*prev_segment)，对这一帧的route_segments进行拼接
9. Shrink收缩，主要针对U型弯等曲率过大的弯道，收缩至角度与当前路点航向角的差在5/6 PI之内

若不启用参考线拼接或者来了一帧新的routing结果，则直接进行参考线平滑操作及Shrink收缩即可。

参考线的平滑我之前也写过一篇文章，感兴趣的读者可以看一下，里面具体的逻辑我都写的比较清楚了。参考线拼接我上面只是将大致步骤提炼了一下，没有很深入地去讲里面的原理，这块等我后续看通透了我再单独写篇文章讲解下。需要说明一下的是，这里的算法叫参考线拼接，与我之前写的一篇轨迹拼接是不一样的，需要注意一下两者的区别。个人理解的话，参考线拼接为了节省算力、提升规划算法的稳定性；轨迹拼接则是提升控制模块跟踪的平滑性。

[steve：Apollo 6.0 参考线平滑算法解析54 赞同 · 18 评论文章![img](https://pic1.zhimg.com/v2-3a39e1a41bfeae46ee3ff0e56a6905c4_180x120.jpg)](https://zhuanlan.zhihu.com/p/371585754)

最终，我们可以看到ReferenceLineProvider的最终输出为reference_lines及segments

```cpp
    std::list<ReferenceLine> reference_lines;
    std::list<hdmap::RouteSegments> segments;
    if (!CreateReferenceLine(&reference_lines, &segments)) {
      is_reference_line_updated_ = false;
      AERROR << "Fail to get reference line";
      continue;
    }
    UpdateReferenceLine(reference_lines, segments);
```

## 六、核心算法的输入——Frame

讲解完了ReferenceLineProvider这个类，我们获得了一系列的reference_line信息，而仅有这个信息是远远不够的。我们以planning中的PublicRoadPlanner为例，可以看到到这一步的输入包括规划初始点、Frame。

```cpp
Status PublicRoadPlanner::Plan(const TrajectoryPoint& planning_start_point,
                               Frame* frame,
                               ADCTrajectory* ptr_computed_trajectory)
```

前面我也提到过，Frame这个结构体包含了planning一帧计算中所需要用到的全部信息，同时Frame也承担了贯穿planning整体逻辑中数据流传递的作用，因此我们可以看到Frame的入参形式为指针传参，因为每一步的修改都由它传递给下一步骤。这一节我们来分析下在进入后续task逻辑前，Frame包含了哪些信息。

Frame中所蕴含的给后续的输入信息其实基本都在InitFrame()这个函数中

```cpp
Status OnLanePlanning::InitFrame(const uint32_t sequence_num,
                                 const TrajectoryPoint& planning_start_point,
                                 const VehicleState& vehicle_state) {
  frame_.reset(new Frame(sequence_num, local_view_, planning_start_point,
                         vehicle_state, reference_line_provider_.get()));
```

一进入InitFrame()，首先调用Frame的构造函数，new出一个新对象。该构造函数包括每一帧累加的sequence_num，planning的上游输入信息local_view，规划起始点，自车状态，前面计算的reference_line_provider里包含的所有信息。

接下来，就是取出reference_line_provider里计算好的reference_lines及segments信息。

```cpp
  std::list<ReferenceLine> reference_lines;
  std::list<hdmap::RouteSegments> segments;
  if (!reference_line_provider_->GetReferenceLines(&reference_lines,
                                                   &segments)) {
    const std::string msg = "Failed to create reference line";
    AERROR << msg;
    return Status(ErrorCode::PLANNING_ERROR, msg);
  }
```

最后，便是调用Frame类里的Init()函数进行初始化，让我们深入看下该函数下都进行了哪些操作。

```cpp
Status Frame::Init(
    const common::VehicleStateProvider *vehicle_state_provider,
    const std::list<ReferenceLine> &reference_lines,
    const std::list<hdmap::RouteSegments> &segments,
    const std::vector<routing::LaneWaypoint> &future_route_waypoints,
    const EgoInfo *ego_info) {
  // TODO(QiL): refactor this to avoid redundant nullptr checks in scenarios.
  auto status = InitFrameData(vehicle_state_provider, ego_info);
  if (!status.ok()) {
    AERROR << "failed to init frame:" << status.ToString();
    return status;
  }
  if (!CreateReferenceLineInfo(reference_lines, segments)) {
    const std::string msg = "Failed to init reference line info.";
    AERROR << msg;
    return Status(ErrorCode::PLANNING_ERROR, msg);
  }
  future_route_waypoints_ = future_route_waypoints;
  return Status::OK();
}
```

首先是InitFrameData()，里面对hdmap_、vehicle_state_、obstacles、traffic_lights进行赋值。其次，构建ReferenceLineInfo结构，我之前提到过ReferenceLineInfo结构里包含了reference_lines信息，还包含了包括vehicle_state_、obstacles在内的等等信息。到这里，也就构建完了Frame结构与ReferenceLineInfo结构。

总结来看，Frame结构在进入task逻辑之前主要包含了以下信息：

1. local_view_
2. hdmap_
3. planning_start_point_
4. vehicle_state_
5. reference_line_info_
6. obstacles_
7. traffic_lights_
8. reference_line_provider_

而这些结构中具体包含了什么，又是怎么计算的，相信我在前面已经讲的比较清楚了，还有任何疑问也欢迎在评论区留言。其中关于obstacles这个结构，由于牵涉到具体的算法逻辑内部，这篇文章就不细谈了，之后讲算法逻辑时，会着重介绍一下这个类，各位只要在frame中，obstacles为固定障碍物id配上预测给的障碍物信息，就足够了。而obstacle这个类下的具体方法，后续在task逻辑内部才会被调用。

## 七、总结

至此，Apollo 7.0 planning的核心框架及核心算法的输入都已经解释清楚了，总体来看Apollo规划的整体思路非常清晰，但是细节部分真的需要花费大量时间来理解，我觉得如果没能将这套算法部署到实车上跑过的话，很多算法可能真的无法很好地理解。我个人也陆陆续续看这套代码有两年多了，始终觉得很多细节都没有深入理解透，代码光看是肯定不行的，如果没有条件在实车，或者是仿真里实际运行，解决相应场景下遇到的问题的话，是始终不能转变为自己的知识的，希望与大家共勉吧。