- [Apollo Planning决策规划代码详细解析 (8): PathLaneBorrowDecider - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/497503888)

**PathLaneBorrowDecider 是第3个task，PathLaneBorrowDecider会判断已处于借道场景下判断是否退出避让；判断未处于借道场景下判断是否具备借道能力。**

一、概述

PathLaneBorrowDecider 是lanefollow 场景下，所调用的第 3 个 task，它的作用主要是换道时：

- 已处于借道场景下判断是否退出避让；
- 未处于借道场景下判断是否具备借道能力。

二、PathLaneBorrowDecider的具体逻辑如下：

1、PublicRoadPlanner 的 LaneFollowStage 配置了以下几个task 来实现具体的规划逻辑，PathLaneBorrowDecider 是第3个task，PathLaneBorrowDecider只是判断是否满足借道条件，具体的轨迹是否借道，是由后面的task决定；

```protobuf
scenario_type: LANE_FOLLOW
stage_type: LANE_FOLLOW_DEFAULT_STAGE
stage_config: {
  stage_type: LANE_FOLLOW_DEFAULT_STAGE
  enabled: true
  task_type: LANE_CHANGE_DECIDER
  task_type: PATH_REUSE_DECIDER
  task_type: PATH_LANE_BORROW_DECIDER
  task_type: PATH_BOUNDS_DECIDER
  task_type: PIECEWISE_JERK_PATH_OPTIMIZER
  task_type: PATH_ASSESSMENT_DECIDER
  task_type: PATH_DECIDER
  task_type: RULE_BASED_STOP_DECIDER
  task_type: ST_BOUNDS_DECIDER
  task_type: SPEED_BOUNDS_PRIORI_DECIDER
  task_type: SPEED_HEURISTIC_OPTIMIZER
  task_type: SPEED_DECIDER
  task_type: SPEED_BOUNDS_FINAL_DECIDER
  # task_type: PIECEWISE_JERK_SPEED_OPTIMIZER
  task_type: PIECEWISE_JERK_NONLINEAR_SPEED_OPTIMIZER
  task_type: RSS_DECIDER
}
```

2、借道决策器的主要功能为判断当前车辆是否具备借道能力，其实现在类PathLaneBorrowDecider的成员函数process()中。process()函数的功能一共分为三部分：检查输入、如果路径复用则跳过借道决策、判断当前街道状态。

```cpp
Status PathLaneBorrowDecider::Process(
    Frame* const frame, ReferenceLineInfo* const reference_line_info) {
  // Sanity checks.
  CHECK_NOTNULL(frame);
  CHECK_NOTNULL(reference_line_info);
 
  // skip path_lane_borrow_decider if reused path
  if (FLAGS_enable_skip_path_tasks && reference_line_info->path_reusable()) {
    // for debug
    AINFO << "skip due to reusing path";
    return Status::OK();
  }
 
  // By default, don't borrow any lane.
  reference_line_info->set_is_path_lane_borrow(false);
  // Check if lane-borrowing is needed, if so, borrow lane.
  if (Decider::config_.path_lane_borrow_decider_config()
          .allow_lane_borrowing() &&
      // 判断是否需要借道，如果需要借道，将当前reference_line的借道属性置位TRUE
      IsNecessaryToBorrowLane(*frame, *reference_line_info)) {
    reference_line_info->set_is_path_lane_borrow(true);
  }
```

3、PathLaneBorrowDecider 的输出结果存在mutable_path_decider_status当中，它是通过proto文件生成的结构体，结构体定义在planning_status.ptoto中

```protobuf
message PathDeciderStatus {
  enum LaneBorrowDirection {
    LEFT_BORROW = 1;   // borrow left neighbor lane
    RIGHT_BORROW = 2;  // borrow right neighbor lane
  }
  optional int32 front_static_obstacle_cycle_counter = 1 [default = 0];
  optional int32 able_to_use_self_lane_counter = 2 [default = 0];
  optional bool is_in_path_lane_borrow_scenario = 3 [default = false];
  optional string front_static_obstacle_id = 4 [default = ""];
  repeated LaneBorrowDirection decided_side_pass_direction = 5;
}
```

输出：

![img](https://pic3.zhimg.com/80/v2-770e567a610cc4bac8fe8f3bffdbb00a_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-0fc0affb13b1537ab985cd46cc4832d9_720w.jpg)

4、判断是否借道IsNecessaryToBorrowLane（）

借道判断主要通过核心函数IsNecessaryToBorrowLane()判断是否借道，主要涉及一些rules，包括距离信号交叉口的距离，与静态障碍物的距离，是否是单行道，是否所在车道左右车道线是虚线等规则。主要有两个功能：

（1）已处于借道场景下判断是否退出避让；

（2）未处于借道场景下判断是否具备借道能力。

```cpp
bool PathLaneBorrowDecider::IsNecessaryToBorrowLane(
    const Frame& frame, const ReferenceLineInfo& reference_line_info) {
  auto* mutable_path_decider_status = injector_->planning_context()
                                          ->mutable_planning_status()
                                          ->mutable_path_decider();
  // 如果当前处于借道场景中
  if (mutable_path_decider_status->is_in_path_lane_borrow_scenario()) {
    // If originally borrowing neighbor lane:
    // 根据数值优化求解轨迹后的信息计算是否退出借道场景（如：避让输出轨迹无解时退出借道），滤波周期为6
    if (mutable_path_decider_status->able_to_use_self_lane_counter() >= 6) {
      // If have been able to use self-lane for some time, then switch to
      // non-lane-borrowing.
      mutable_path_decider_status->set_is_in_path_lane_borrow_scenario(false);
      mutable_path_decider_status->clear_decided_side_pass_direction();
      AINFO << "Switch from LANE-BORROW path to SELF-LANE path.";
    }
  }
  // 如果当前不处于借道场景中
   else {
    // If originally not borrowing neighbor lane:
    ADEBUG << "Blocking obstacle ID["
           << mutable_path_decider_status->front_static_obstacle_id() << "]";
    // ADC requirements check for lane-borrowing:
    // 当下面这些条件必须全部满足，才能借道:
    // 只有一条参考线，才能借道
    // 起点速度小于最大借道允许速度
    // 阻塞障碍物必须远离路口
    // 阻塞障碍物会一直存在
    // 阻塞障碍物与终点位置满足要求
    // 为可侧面通过的障碍物
    if (!HasSingleReferenceLine(frame)) {
      return false;
    }
    if (!IsWithinSidePassingSpeedADC(frame)) {
      return false;
    }
 
    // Obstacle condition check for lane-borrowing:
    if (!IsBlockingObstacleFarFromIntersection(reference_line_info)) {
      return false;
    }
    if (!IsLongTermBlockingObstacle()) {
      return false;
    }
    if (!IsBlockingObstacleWithinDestination(reference_line_info)) {
      return false;
    }
    if (!IsSidePassableObstacle(reference_line_info)) {
      return false;
    }
 
    // switch to lane-borrowing
    // set side-pass direction
    // 在无避让方向时重新计算避让方向，若左、右借道空间均不满足则不借道，is_in_path_lane_borrow_scenario_标志为false；
    // 左借道条件满足左借道、右借道条件满足右借道，is_in_path_lane_borrow_scenario_为true。
    const auto& path_decider_status =
        injector_->planning_context()->planning_status().path_decider();
    if (path_decider_status.decided_side_pass_direction().empty()) {
      // first time init decided_side_pass_direction
      bool left_borrowable;
      bool right_borrowable;
      CheckLaneBorrow(reference_line_info, &left_borrowable, &right_borrowable);
      if (!left_borrowable && !right_borrowable) {
        mutable_path_decider_status->set_is_in_path_lane_borrow_scenario(false);
        return false;
      } else {
        mutable_path_decider_status->set_is_in_path_lane_borrow_scenario(true);
        if (left_borrowable) {
          mutable_path_decider_status->add_decided_side_pass_direction(
              PathDeciderStatus::LEFT_BORROW);
        }
        if (right_borrowable) {
          mutable_path_decider_status->add_decided_side_pass_direction(
              PathDeciderStatus::RIGHT_BORROW);
        }
      }
    }
 
    AINFO << "Switch from SELF-LANE path to LANE-BORROW path.";
  }
  return mutable_path_decider_status->is_in_path_lane_borrow_scenario();
}
```

需要满足下面条件才能判断是否可以借道：

- 只有一条参考线，才能借道
- 起点速度小于最大借道允许速度
- 阻塞障碍物必须远离路口
- 阻塞障碍物会一直存在
- 阻塞障碍物与终点位置满足要求
- 为可侧面通过的障碍物

5、CheckLaneBorrow()

主要根据前方道路的线型判断是否可以借道；在此函数中2m间隔一个点遍历车前100m参考线或全部参考线，如果车道线类型为黄实线、白实线则不借道。

```cpp
void PathLaneBorrowDecider::CheckLaneBorrow(
    const ReferenceLineInfo& reference_line_info,
    bool* left_neighbor_lane_borrowable, bool* right_neighbor_lane_borrowable) {
  const ReferenceLine& reference_line = reference_line_info.reference_line();
 
  *left_neighbor_lane_borrowable = true;
  *right_neighbor_lane_borrowable = true;
 
  static constexpr double kLookforwardDistance = 100.0;
  double check_s = reference_line_info.AdcSlBoundary().end_s();
  const double lookforward_distance =
      std::min(check_s + kLookforwardDistance, reference_line.Length());
  while (check_s < lookforward_distance) {
    auto ref_point = reference_line.GetNearestReferencePoint(check_s);
    if (ref_point.lane_waypoints().empty()) {
      *left_neighbor_lane_borrowable = false;
      *right_neighbor_lane_borrowable = false;
      return;
    }
 
    const auto waypoint = ref_point.lane_waypoints().front();
    hdmap::LaneBoundaryType::Type lane_boundary_type =
        hdmap::LaneBoundaryType::UNKNOWN;
 
    if (*left_neighbor_lane_borrowable) {
      lane_boundary_type = hdmap::LeftBoundaryType(waypoint);
      if (lane_boundary_type == hdmap::LaneBoundaryType::SOLID_YELLOW ||
          lane_boundary_type == hdmap::LaneBoundaryType::SOLID_WHITE) {
        *left_neighbor_lane_borrowable = false;
      }
      ADEBUG << "s[" << check_s << "] left_lane_boundary_type["
             << LaneBoundaryType_Type_Name(lane_boundary_type) << "]";
    }
    if (*right_neighbor_lane_borrowable) {
      lane_boundary_type = hdmap::RightBoundaryType(waypoint);
      if (lane_boundary_type == hdmap::LaneBoundaryType::SOLID_YELLOW ||
          lane_boundary_type == hdmap::LaneBoundaryType::SOLID_WHITE) {
        *right_neighbor_lane_borrowable = false;
      }
      ADEBUG << "s[" << check_s << "] right_neighbor_lane_borrowable["
             << LaneBoundaryType_Type_Name(lane_boundary_type) << "]";
    }
    check_s += 2.0;
  }
}
```

6、 在IsNonmovableObstacle() 中主要对前方障碍物进行判断，利用预测以及参考线的信息来进行判断：

- 目标太远不借道
- 目标停止借道
- 目标

```cpp
bool IsNonmovableObstacle(const ReferenceLineInfo& reference_line_info,
                          const Obstacle& obstacle) {
  // Obstacle is far away.
  const SLBoundary& adc_sl_boundary = reference_line_info.AdcSlBoundary();
  if (obstacle.PerceptionSLBoundary().start_s() >
      adc_sl_boundary.end_s() + kAdcDistanceThreshold) {
    ADEBUG << " - It is too far ahead and we are not so sure of its status.";
    return false;
  }
 
  // Obstacle is parked obstacle.
  if (IsParkedVehicle(reference_line_info.reference_line(), &obstacle)) {
    ADEBUG << "It is Parked and NON-MOVABLE.";
    return true;
  }
 
  // Obstacle is blocked by others too.
  for (const auto* other_obstacle :
       reference_line_info.path_decision().obstacles().Items()) {
    if (other_obstacle->Id() == obstacle.Id()) {
      continue;
    }
    if (other_obstacle->IsVirtual()) {
      continue;
    }
    if (other_obstacle->PerceptionSLBoundary().start_l() >
            obstacle.PerceptionSLBoundary().end_l() ||
        other_obstacle->PerceptionSLBoundary().end_l() <
            obstacle.PerceptionSLBoundary().start_l()) {
      // not blocking the backside vehicle
      continue;
    }
    double delta_s = other_obstacle->PerceptionSLBoundary().start_s() -
                     obstacle.PerceptionSLBoundary().end_s();
    if (delta_s < 0.0 || delta_s > kObstaclesDistanceThreshold) {
      continue;
    }
 
    // TODO(All): Fix the segmentation bug for large vehicles, otherwise
    // the follow line will be problematic.
    ADEBUG << " - It is blocked by others, and will move later.";
    return false;
  }
 
  ADEBUG << "IT IS NON-MOVABLE!";
  return true;
}
```