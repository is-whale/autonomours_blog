- [Apollo规划决策算法仿真调试(3)：ReferenceLineProvider参考线生成流程 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/511392608)

前言：

在Apollo规划决策算法中，即planning模块中，参考线ReferenceLine 的作用至关重要，ReferenceLine 是整个规划算法的基础，规划的目的就是使车辆按照ReferenceLine行驶。

目前关于ReferenceLine 的平滑方法介绍很多，但很少介绍ReferenceLine 在整个apollo的planning模块中，是在哪一步进行生成，它的整个运行机制与流程是怎样的。

本文将使用vscode与dreamview 联合调试的方法，来展示ReferenceLine 在apollo的planning模块中是如何一步步生成。

![img](https://pic3.zhimg.com/80/v2-2b7abecd3b25fd1091030b1556ce013a_720w.jpg)

1、Apollo在onlanePlanning中进行ReferenceLine 的生成，整体的调度过程如下：

下图是一次调试时vscode中堆栈区的截图，可以看到在planning模块中，参考线ReferenceLine 的生成路径如下：

OnLanePlanning::RunOnce( ) -->> InitFrame(frame_num, stitching_trajectory.back(), vehicle_state) -->>

GetReferenceLines(&reference_lines, &segments)) -->> CreateReferenceLine(reference_lines, segments) -->>

CreateRouteSegments(vehicle_state, segments) -->> GetRouteSegments(vehicle_state, segments) ......

从本文后续的代码分析可以看出，RouteSegments 对于参考线ReferenceLine 的生成至关重要，有几个RouteSegments 决定有几条参考线的生成。

![img](https://pic4.zhimg.com/80/v2-fcf2b9fdc39c9fdec1f8e0515438ac53_720w.png)

2、OnLanePlanning::RunOnce( ) 是执行规划认为的主函数，在调用具体的规划器planner进行轨迹规划任务前，会首先初始化frame，其中在初始化frame时，进行了参考线的调用，即GetReferenceLines()。

OnLanePlanning::RunOnce( ) 中 InitFrame( ) 的相关代码：

```cpp
  injector_->ego_info()->Update(stitching_trajectory.back(), vehicle_state);
 const uint32_t frame_num = static_cast<uint32_t>(seq_num_++);
  status = InitFrame(frame_num, stitching_trajectory.back(), vehicle_state);


 if (status.ok()) {
    injector_->ego_info()->CalculateFrontObstacleClearDistance(
        frame_->obstacles());
 }
```

在 InitFrame( ) 中 GetReferenceLines( ）的相关代码如下：

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

3、ReferenceLineProvider 中获取参考线ReferenceLine：

从调试过程中变量分析，在GetReferenceLines(&reference_lines, &segments)) 中，主要是调用CreateReferenceLine(reference_lines, segments) 来生成参考线，二者都属于ReferenceLineProvider 类中的方法。

调试过程中，关键变量取值如下：

![img](https://pic3.zhimg.com/80/v2-b529630b8a0281eca3175adce5d14bfe_720w.jpg)

代码如下：

```cpp
bool ReferenceLineProvider::GetReferenceLines(
    std::list<ReferenceLine> *reference_lines,
    std::list<hdmap::RouteSegments> *segments) {
 CHECK_NOTNULL(reference_lines);
 CHECK_NOTNULL(segments);


 if (FLAGS_use_navigation_mode) {
 double start_time = Clock::NowInSeconds();
 bool result = GetReferenceLinesFromRelativeMap(reference_lines, segments);
 if (!result) {
      AERROR << "Failed to get reference line from relative map";
 }
 double end_time = Clock::NowInSeconds();
    last_calculation_time_ = end_time - start_time;
 return result;
 }


 if (FLAGS_enable_reference_line_provider_thread) {
    std::lock_guard<std::mutex> lock(reference_lines_mutex_);
 if (!reference_lines_.empty()) {
      reference_lines->assign(reference_lines_.begin(), reference_lines_.end());
      segments->assign(route_segments_.begin(), route_segments_.end());
 return true;
 }
 } else {
 double start_time = Clock::NowInSeconds();
 if (CreateReferenceLine(reference_lines, segments)) {
 UpdateReferenceLine(*reference_lines, *segments);
 double end_time = Clock::NowInSeconds();
      last_calculation_time_ = end_time - start_time;
 return true;
 }
 }


  AWARN << "Reference line is NOT ready.";
 if (reference_line_history_.empty()) {
    AERROR << "Failed to use reference line latest history";
 return false;
 }


  reference_lines->assign(reference_line_history_.back().begin(),
                          reference_line_history_.back().end());
  segments->assign(route_segments_history_.back().begin(),
                   route_segments_history_.back().end());
  AWARN << "Use reference line from history!";
 return true;
}
```

4、GetReferenceLines( ) 中参考线ReferenceLine 的生成逻辑

在GetReferenceLines( ) 中可以看到 ReferenceLine 是通过遍历segments 来进行生成的，所以segment 的数量以及位置，便决定了参考线ReferenceLine 的数量与位置；对于每个segments ，通过调用参考线平滑方法SmoothRouteSegment(*iter, &reference_lines->back()) 来生成参考线。

```cpp
if (!CreateRouteSegments(vehicle_state, segments)) {
    AERROR << "Failed to create reference line from routing";
 return false;
 }
 if (is_new_routing || !FLAGS_enable_reference_line_stitching) {
 for (auto iter = segments->begin(); iter != segments->end();) {
      reference_lines->emplace_back();
 if (!SmoothRouteSegment(*iter, &reference_lines->back())) {
        AERROR << "Failed to create reference line from route segments";
        reference_lines->pop_back();
        iter = segments->erase(iter);
 } else {
        common::SLPoint sl;
 if (!reference_lines->back().XYToSL(vehicle_state, &sl)) {
          AWARN << "Failed to project point: {" << vehicle_state.x() << ","
 << vehicle_state.y() << "} to stitched reference line";
 }
 Shrink(sl, &reference_lines->back(), &(*iter));
 ++iter;
 }
 }
 return true;
```

5、ReferenceLineProvider::CreateRouteSegments()逻辑

可以看到对于参考线至关重要的RouteSegments，是根据地图提供的pnc_map 来进行生成，其中又可以通过配置FLAGS_prioritize_change_lane 来确定是否变道优先。

代码如下：

```cpp
bool ReferenceLineProvider::CreateRouteSegments(
 const common::VehicleState &vehicle_state,
    std::list<hdmap::RouteSegments> *segments) {
 {
    std::lock_guard<std::mutex> lock(pnc_map_mutex_);
 if (!pnc_map_->GetRouteSegments(vehicle_state, segments)) {
      AERROR << "Failed to extract segments from routing";
 return false;
 }
 }


 if (FLAGS_prioritize_change_lane) {
 PrioritzeChangeLane(segments);
 }
 return !segments->empty();
}
```

6、从调试结果中看到，生成了两个RouteSegments，所以也会生成两条参考线

![img](https://pic3.zhimg.com/80/v2-72a83b700f576bb85d2e600ae3a5f8be_720w.jpg)

7、关于RouteSegments 生成的具体方法，将在之后的文章中进行介绍。