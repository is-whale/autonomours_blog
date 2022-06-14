- [Apollo Planning决策规划代码详细解析 (6):LaneChangeDecider - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/496498679)

**LaneChangeDecider 是lanefollow 场景下，所调用的第一个task，它的作用主要有两点：**

- **判断当前是否进行变道，以及变道的状态，并将结果存在变量lane_change_status中；**
- **变道过程中将目标车道的reference line放置到首位，变道结束后将当前新车道的reference line放置到首位**

一、概述

LaneChangeDecider 是lanefollow 场景下，所调用的第一个task，它的作用主要有两点：

- 判断当前是否进行变道，以及变道的状态，并将结果存在变量lane_change_status中；
- 变道过程中将目标车道的reference line放置到首位，变道结束后将当前新车道的reference line放置到首位

二、LaneChangeDecider的流程图如下：

![img](https://pic2.zhimg.com/80/v2-2d9117b12304920f53e861568787cf4d_720w.jpg)

三、LaneChangeDecider的具体逻辑如下：

1、PublicRoadPlanner 的 LaneFollowStage 配置了以下几个task 来实现具体的规划逻辑，LaneChangeDecider是第一个task：

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

2、前文讲到在stage阶段会依次调用每个 task 的 Execute() 函数，LaneChangeDecider继承自 Decider 类，Decider继承自基类 task 类，并且override了Execute() 方法；

```cpp
class Decider : public Task {
 public:
  explicit Decider(const TaskConfig& config);
  Decider(const TaskConfig& config,
          const std::shared_ptr<DependencyInjector>& injector);
  virtual ~Decider() = default;
  
  // Execute 方法有override关键字，需要被重写
  apollo::common::Status Execute(
      Frame* frame, ReferenceLineInfo* reference_line_info) override;
 
  apollo::common::Status Execute(Frame* frame) override;
 
 protected:
  virtual apollo::common::Status Process(
      Frame* frame, ReferenceLineInfo* reference_line_info) {
    return apollo::common::Status::OK();
  }
 
  virtual apollo::common::Status Process(Frame* frame) {
    return apollo::common::Status::OK();
  }
};
```

重写Execute() 的代码在 [decider.cc is coming soon](https://link.zhihu.com/?target=http%3A//decider.cc/) 文件中，即调用当前类的 Process() 方法：

```cpp
apollo::common::Status Decider::Execute(
    Frame* frame, ReferenceLineInfo* reference_line_info) {
  Task::Execute(frame, reference_line_info);
  return Process(frame, reference_line_info);
}
```

2、由以上分析可知，LaneChangeDecider 的主要决策逻辑在Process() 方法中，Process() 的代码及注释如下，先上整体代码，再详细讲解其中的每个模块：

```cpp
Status LaneChangeDecider::Process(
    Frame* frame, ReferenceLineInfo* const current_reference_line_info) {
  // Sanity checks.
  CHECK_NOTNULL(frame);
  
  // 读取配置文件
  const auto& lane_change_decider_config = config_.lane_change_decider_config();
  
  // 从frame 中读取reference_line_info，并检查
  std::list<ReferenceLineInfo>* reference_line_info =
      frame->mutable_reference_line_info();
  if (reference_line_info->empty()) {
    const std::string msg = "Reference lines empty.";
    AERROR << msg;
    return Status(ErrorCode::PLANNING_ERROR, msg);
  }
  
  // 如果配置reckless_change_lane为TRUE，则将变道的目标车道放置为reference line的首位，并返回OK；
  // 需要同时打开配置enable_prioritize_change_lane，才可以调整reference line
  // 默认配置中reckless_change_lane 是关闭的，所以不会执行这个逻辑
  if (lane_change_decider_config.reckless_change_lane()) {
    PrioritizeChangeLane(true, reference_line_info);
    return Status::OK();
  }
 
  // 将变道的状态存储在lane_change_status 这个变量中
  auto* prev_status = injector_->planning_context()
                          ->mutable_planning_status()
                          ->mutable_change_lane();
  double now = Clock::NowInSeconds();
 
  prev_status->set_is_clear_to_change_lane(false);
  if (current_reference_line_info->IsChangeLanePath()) {
    prev_status->set_is_clear_to_change_lane(
        IsClearToChangeLane(current_reference_line_info));
  }
 
  if (!prev_status->has_status()) {
    UpdateStatus(now, ChangeLaneStatus::CHANGE_LANE_FINISHED,
                 GetCurrentPathId(*reference_line_info));
    prev_status->set_last_succeed_timestamp(now);
    return Status::OK();
  }
 
  // 根据reference line的数量判断是否处于变道场景中，size() > 1则处于变道过程中，需要判断变道的状态
  bool has_change_lane = reference_line_info->size() > 1;
  ADEBUG << "has_change_lane: " << has_change_lane;
  // 只有一条reference line，没有进行变道
  if (!has_change_lane) {
    // 根据当前唯一的reference line，获得当前道路lane的ID
    const auto& path_id = reference_line_info->front().Lanes().Id();
    if (prev_status->status() == ChangeLaneStatus::CHANGE_LANE_FINISHED) {
    } 
    // 上一时刻在变道中，这一时刻只有一条reference line，说明变道成功
    else if (prev_status->status() == ChangeLaneStatus::IN_CHANGE_LANE) {
      // 将变道的状态存储在lane_change_status 这个变量中，
      // 存入当前时刻，变道完成状态，以及当前道路的ID
      UpdateStatus(now, ChangeLaneStatus::CHANGE_LANE_FINISHED, path_id);
    } else if (prev_status->status() == ChangeLaneStatus::CHANGE_LANE_FAILED) {
    } else {
      const std::string msg =
          absl::StrCat("Unknown state: ", prev_status->ShortDebugString());
      AERROR << msg;
      return Status(ErrorCode::PLANNING_ERROR, msg);
    }
    // 返回LaneChangeDecider::Process 的状态为OK
    return Status::OK();
  } 
  // 有多条reference line，说明处在变道中
  else {  // has change lane in reference lines.
    // 获取自车当前所在车道的ID
    auto current_path_id = GetCurrentPathId(*reference_line_info);
    // 如果当前所在车道为空，则返回error状态
    if (current_path_id.empty()) {
      const std::string msg = "The vehicle is not on any reference line";
      AERROR << msg;
      return Status(ErrorCode::PLANNING_ERROR, msg);
    }
    // 如果上一时刻处在变道中，根据上一时刻自车所处道路ID与当前时刻所处道路ID对比，来确认变道状态
    if (prev_status->status() == ChangeLaneStatus::IN_CHANGE_LANE) {
      // ID相同则说明变道还在进行中，
      // 同时调用PrioritizeChangeLane(),将目标车道的reference line放在首位
      if (prev_status->path_id() == current_path_id) {
        PrioritizeChangeLane(true, reference_line_info);
      } else {
        // RemoveChangeLane(reference_line_info);
        // ID不同则说明变道已经成功，
        // 则调用PrioritizeChangeLane(),将变道前所在车道的reference line 删掉
        PrioritizeChangeLane(false, reference_line_info);
        ADEBUG << "removed change lane.";
        UpdateStatus(now, ChangeLaneStatus::CHANGE_LANE_FINISHED,
                     current_path_id);
      }
      return Status::OK();
    } else if (prev_status->status() == ChangeLaneStatus::CHANGE_LANE_FAILED) {
      // TODO(SHU): add an optimization_failure counter to enter
      // change_lane_failed status
      if (now - prev_status->timestamp() <
          lane_change_decider_config.change_lane_fail_freeze_time()) {
        // RemoveChangeLane(reference_line_info);
        PrioritizeChangeLane(false, reference_line_info);
        ADEBUG << "freezed after failed";
      } else {
        UpdateStatus(now, ChangeLaneStatus::IN_CHANGE_LANE, current_path_id);
        ADEBUG << "change lane again after failed";
      }
      return Status::OK();
    } else if (prev_status->status() ==
               ChangeLaneStatus::CHANGE_LANE_FINISHED) {
      if (now - prev_status->timestamp() <
          lane_change_decider_config.change_lane_success_freeze_time()) {
        // RemoveChangeLane(reference_line_info);
        PrioritizeChangeLane(false, reference_line_info);
        ADEBUG << "freezed after completed lane change";
      } else {
        PrioritizeChangeLane(true, reference_line_info);
        UpdateStatus(now, ChangeLaneStatus::IN_CHANGE_LANE, current_path_id);
        ADEBUG << "change lane again after success";
      }
    } else {
      const std::string msg =
          absl::StrCat("Unknown state: ", prev_status->ShortDebugString());
      AERROR << msg;
      return Status(ErrorCode::PLANNING_ERROR, msg);
    }
  }
  return Status::OK();
}
```

3、其中lane_change_decider_config 配置文件很关键，决定了整个函数的流程走向，它定义在以下两个文件中：

![img](https://pic4.zhimg.com/80/v2-2f9a3f4f8896af41211dc82bb2138543_720w.jpg)

```protobuf
 lane_change_decider_config {
    enable_lane_change_urgency_check: false
    enable_prioritize_change_lane: false
    enable_remove_change_lane: false
    reckless_change_lane: false
    change_lane_success_freeze_time: 1.5
    change_lane_fail_freeze_time: 1.0
  }
 
  lane_change_decider_config {
      enable_lane_change_urgency_check: true
  }
```

4、判断是否为可变车道时调用了 IsChangeLanePath()，它的逻辑也很简单， 如果自车在当前ReferenceLine 的车道segment上，则为FALSE；如果自车不在当前ReferenceLine 的车道segment上，则为TRUE。

```cpp
bool ReferenceLineInfo::IsChangeLanePath() const {
  return !Lanes().IsOnSegment();
}
```

5、更新变道状态时用到了 UpdateStatus() 函数，它的定义如下：

```cpp
void LaneChangeDecider::UpdateStatus(double timestamp,
                                     ChangeLaneStatus::Status status_code,
                                     const std::string& path_id) {
  auto* lane_change_status = injector_->planning_context()
                                 ->mutable_planning_status()
                                 ->mutable_change_lane();
  lane_change_status->set_timestamp(timestamp);
  lane_change_status->set_path_id(path_id);
  lane_change_status->set_status(status_code);
}
```

6、在调整参考线的顺序时，使用了PrioritizeChangeLane() 函数，它的调整参考线顺序的功能，需要配置enable_prioritize_change_lane为True，这个函数的完整代码及注释如下：

```cpp
void LaneChangeDecider::PrioritizeChangeLane(
    const bool is_prioritize_change_lane,
    std::list<ReferenceLineInfo>* reference_line_info) const {
  if (reference_line_info->empty()) {
    AERROR << "Reference line info empty";
    return;
  }
 
  const auto& lane_change_decider_config = config_.lane_change_decider_config();
  
  // TODO(SHU): disable the reference line order change for now
  // 如果没有配置变道优先，则退出该函数
  if (!lane_change_decider_config.enable_prioritize_change_lane()) {
    return;
  }
  auto iter = reference_line_info->begin();
  while (iter != reference_line_info->end()) {
    ADEBUG << "iter->IsChangeLanePath(): " << iter->IsChangeLanePath();
    /* is_prioritize_change_lane == true: prioritize change_lane_reference_line
       is_prioritize_change_lane == false: prioritize
       non_change_lane_reference_line */
 
    // 0、is_prioritize_change_lane 根据参考线数量置位True 或 False
    // 1、如果is_prioritize_change_lane为True
    // 首先获取第一条参考线的迭代器，然后遍历所有的参考线，
    // 如果当前的参考线为允许变道参考线，则将第一条参考线更换为当前迭代器所指向的参考线,
    // 注意，可变车道为按迭代器的顺序求取，一旦发现可变车道，即推出循环。
    // 
    // 2、如果is_prioritize_change_lane 为False，
    // 找到第一条不可变道的参考线，将第一条参考线更新为当前不可变道的参考线
    if ((is_prioritize_change_lane && iter->IsChangeLanePath()) ||
        (!is_prioritize_change_lane && !iter->IsChangeLanePath())) {
      ADEBUG << "is_prioritize_change_lane: " << is_prioritize_change_lane;
      ADEBUG << "iter->IsChangeLanePath(): " << iter->IsChangeLanePath();
      break;
    }
    ++iter;
  }
  reference_line_info->splice(reference_line_info->begin(),
                              *reference_line_info, iter);
  ADEBUG << "reference_line_info->IsChangeLanePath(): "
         << reference_line_info->begin()->IsChangeLanePath();
}
```

7、 IsClearToChangeLane() 判断当前的参考线是否变道安全，并将结果写入lane_change_status 这个变量中

对IsClearToChangeLane() 的调用：

```cpp
prev_status->set_is_clear_to_change_lane(false);
  if (current_reference_line_info->IsChangeLanePath()) {
    prev_status->set_is_clear_to_change_lane(
        IsClearToChangeLane(current_reference_line_info));
  }
```

IsClearToChangeLane() 遍历了当前参考线上所有目标，并根据目标的行驶方向设置安全距离，通过安全距离判断是否变道安全，代码及注释如下：

```cpp
bool LaneChangeDecider::IsClearToChangeLane(
    ReferenceLineInfo* reference_line_info) {
  // 或得当前参考线的s坐标的最大最小值，以及自车速度
  double ego_start_s = reference_line_info->AdcSlBoundary().start_s();
  double ego_end_s = reference_line_info->AdcSlBoundary().end_s();
  double ego_v =
      std::abs(reference_line_info->vehicle_state().linear_velocity());
 
  // 遍历每个目标
  for (const auto* obstacle :
       reference_line_info->path_decision()->obstacles().Items()) {
    // 跳过静止与虚拟目标
    if (obstacle->IsVirtual() || obstacle->IsStatic()) {
      ADEBUG << "skip one virtual or static obstacle";
      continue;
    }
    // 初始化
    double start_s = std::numeric_limits<double>::max();
    double end_s = -std::numeric_limits<double>::max();
    double start_l = std::numeric_limits<double>::max();
    double end_l = -std::numeric_limits<double>::max();
 
    // 遍历当前目标的预测轨迹点集，或得预测轨迹的边界点
    for (const auto& p : obstacle->PerceptionPolygon().points()) {
      SLPoint sl_point;
      reference_line_info->reference_line().XYToSL(p, &sl_point);
      start_s = std::fmin(start_s, sl_point.s());
      end_s = std::fmax(end_s, sl_point.s());
 
      start_l = std::fmin(start_l, sl_point.l());
      end_l = std::fmax(end_l, sl_point.l());
    }
 
    // 如果目标距离当前参考线距离太远， 则跳过该目标
    if (reference_line_info->IsChangeLanePath()) {
      static constexpr double kLateralShift = 2.5;
      if (end_l < -kLateralShift || start_l > kLateralShift) {
        continue;
      }
    }
 
    // Raw estimation on whether same direction with ADC or not based on
    // prediction trajectory
    // 判断车与障碍物是否同一方向行驶，同时设置安全距离。之后判断距离障碍物距离是否安全
    // 根据航向角判断是否为相同方向
    bool same_direction = true;
    if (obstacle->HasTrajectory()) {
      double obstacle_moving_direction =
          obstacle->Trajectory().trajectory_point(0).path_point().theta();
      const auto& vehicle_state = reference_line_info->vehicle_state();
      double vehicle_moving_direction = vehicle_state.heading();
      if (vehicle_state.gear() == canbus::Chassis::GEAR_REVERSE) {
        vehicle_moving_direction =
            common::math::NormalizeAngle(vehicle_moving_direction + M_PI);
      }
      double heading_difference = std::abs(common::math::NormalizeAngle(
          obstacle_moving_direction - vehicle_moving_direction));
      same_direction = heading_difference < (M_PI / 2.0);
    }
 
    // TODO(All) move to confs
    static constexpr double kSafeTimeOnSameDirection = 3.0;
    static constexpr double kSafeTimeOnOppositeDirection = 5.0;
    static constexpr double kForwardMinSafeDistanceOnSameDirection = 10.0;
    static constexpr double kBackwardMinSafeDistanceOnSameDirection = 10.0;
    static constexpr double kForwardMinSafeDistanceOnOppositeDirection = 50.0;
    static constexpr double kBackwardMinSafeDistanceOnOppositeDirection = 1.0;
    static constexpr double kDistanceBuffer = 0.5;
 
    double kForwardSafeDistance = 0.0;
    double kBackwardSafeDistance = 0.0;
    // 根据方向设置安全距离
    if (same_direction) {
      kForwardSafeDistance =
          std::fmax(kForwardMinSafeDistanceOnSameDirection,
                    (ego_v - obstacle->speed()) * kSafeTimeOnSameDirection);
      kBackwardSafeDistance =
          std::fmax(kBackwardMinSafeDistanceOnSameDirection,
                    (obstacle->speed() - ego_v) * kSafeTimeOnSameDirection);
    } else {
      kForwardSafeDistance =
          std::fmax(kForwardMinSafeDistanceOnOppositeDirection,
                    (ego_v + obstacle->speed()) * kSafeTimeOnOppositeDirection);
      kBackwardSafeDistance = kBackwardMinSafeDistanceOnOppositeDirection;
    }
    
    // 判断障碍物是否满足安全距离
    if (HysteresisFilter(ego_start_s - end_s, kBackwardSafeDistance,
                         kDistanceBuffer, obstacle->IsLaneChangeBlocking()) &&
        HysteresisFilter(start_s - ego_end_s, kForwardSafeDistance,
                         kDistanceBuffer, obstacle->IsLaneChangeBlocking())) {
      reference_line_info->path_decision()
          ->Find(obstacle->Id())
          ->SetLaneChangeBlocking(true);
      ADEBUG << "Lane Change is blocked by obstacle" << obstacle->Id();
      return false;
    } else {
      reference_line_info->path_decision()
          ->Find(obstacle->Id())
          ->SetLaneChangeBlocking(false);
    }
  }
  return true;
}
```

8、此外，LaneChangeDecider还定义了IsPerceptionBlocked() 和 UpdatePreparationDistance() 两个public 方法，但是并未在Process() 中使用

IsPerceptionBlocked()：

```cpp
bool LaneChangeDecider::IsPerceptionBlocked(
    const ReferenceLineInfo& reference_line_info,
    const double search_beam_length, const double search_beam_radius_intensity,
    const double search_range, const double is_block_angle_threshold) {
  const auto& vehicle_state = reference_line_info.vehicle_state();
  const common::math::Vec2d adv_pos(vehicle_state.x(), vehicle_state.y());
  const double adv_heading = vehicle_state.heading();
 
  double left_most_angle =
      common::math::NormalizeAngle(adv_heading + 0.5 * search_range);
  double right_most_angle =
      common::math::NormalizeAngle(adv_heading - 0.5 * search_range);
  bool right_most_found = false;
 
  for (auto* obstacle :
       reference_line_info.path_decision().obstacles().Items()) {
    if (obstacle->IsVirtual()) {
      ADEBUG << "skip one virtual obstacle";
      continue;
    }
    const auto& obstacle_polygon = obstacle->PerceptionPolygon();
    for (double search_angle = 0.0; search_angle < search_range;
         search_angle += search_beam_radius_intensity) {
      common::math::Vec2d search_beam_end(search_beam_length, 0.0);
      const double beam_heading = common::math::NormalizeAngle(
          adv_heading - 0.5 * search_range + search_angle);
      search_beam_end.SelfRotate(beam_heading);
      search_beam_end += adv_pos;
      common::math::LineSegment2d search_beam(adv_pos, search_beam_end);
 
      if (!right_most_found && obstacle_polygon.HasOverlap(search_beam)) {
        right_most_found = true;
        right_most_angle = beam_heading;
      }
 
      if (right_most_found && !obstacle_polygon.HasOverlap(search_beam)) {
        left_most_angle = beam_heading - search_angle;
        break;
      }
    }
    if (!right_most_found) {
      // obstacle is not in search range
      continue;
    }
    if (std::fabs(common::math::NormalizeAngle(
            left_most_angle - right_most_angle)) > is_block_angle_threshold) {
      return true;
    }
  }
 
  return false;
}
```

UpdatePreparationDistance():

```cpp
void LaneChangeDecider::UpdatePreparationDistance(
    const bool is_opt_succeed, const Frame* frame,
    const ReferenceLineInfo* const reference_line_info,
    PlanningContext* planning_context) {
  auto* lane_change_status =
      planning_context->mutable_planning_status()->mutable_change_lane();
  ADEBUG << "Current time: " << lane_change_status->timestamp();
  ADEBUG << "Lane Change Status: " << lane_change_status->status();
  // If lane change planning succeeded, update and return
  if (is_opt_succeed) {
    lane_change_status->set_last_succeed_timestamp(
        Clock::NowInSeconds());
    lane_change_status->set_is_current_opt_succeed(true);
    return;
  }
  // If path optimizer or speed optimizer failed, report the status
  lane_change_status->set_is_current_opt_succeed(false);
  // If the planner just succeed recently, let's be more patient and try again
  if (Clock::NowInSeconds() -
          lane_change_status->last_succeed_timestamp() <
      FLAGS_allowed_lane_change_failure_time) {
    return;
  }
  // Get ADC's current s and the lane-change start distance s
  const ReferenceLine& reference_line = reference_line_info->reference_line();
  const common::TrajectoryPoint& planning_start_point =
      frame->PlanningStartPoint();
  auto adc_sl_info = reference_line.ToFrenetFrame(planning_start_point);
  if (!lane_change_status->exist_lane_change_start_position()) {
    return;
  }
  common::SLPoint point_sl;
  reference_line.XYToSL(lane_change_status->lane_change_start_position(),
                        &point_sl);
  ADEBUG << "Current ADC s: " << adc_sl_info.first[0];
  ADEBUG << "Change lane point s: " << point_sl.s();
  // If the remaining lane-change preparation distance is too small,
  // refresh the preparation distance
  if (adc_sl_info.first[0] + FLAGS_min_lane_change_prepare_length >
      point_sl.s()) {
    lane_change_status->set_exist_lane_change_start_position(false);
    ADEBUG << "Refresh the lane-change preparation distance";
  }
}
```