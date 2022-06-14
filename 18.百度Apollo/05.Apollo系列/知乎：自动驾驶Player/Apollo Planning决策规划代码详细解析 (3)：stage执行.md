- [Apollo Planning决策规划代码详细解析 (3)：stage执行 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/495902084)

**正文如下：**

通过之前章节的介绍，在经过Scenario的决策与执行之后，Apollo已经可以确定目前处于对应场景下的对应stage，接下来就进入stage类的内部，通过Process() 函数来进行具体的规划过程。本章节将以 LANE_FOLLOW 场景的 LANE_FOLLOW_DEFAULT_STAGE 为例，讲解stage的执行流程。

码字不易，喜欢的朋友们麻烦点个关注与赞。

**在本文你将学到下面这些内容：**

- stage模块的注册与管理机制；
- stage模块调用的接口与顺序；
- NOP功能最常用stage：LANE_FOLLOW_DEFAULT_STAGE；
- LANE_FOLLOW_DEFAULT_STAGE中对变道场景的处理；
- LANE_FOLLOW_DEFAULT_STAGE的执行过程；
- LANE_FOLLOW_DEFAULT_STAGE的规划逻辑。

**代码具体过程如下：**

1、前文提到在planning模块执行的过程中，会根据配置来依次实例化每个Scenario当前所处的stage，LANE_FOLLOW这个Scenario只有LANE_FOLLOW_DEFAULT_STAGE一个stage，所以它的 CreateStage() 函数如下：

```cpp
std::unique_ptr<Stage> LaneFollowScenario::CreateStage(
    const ScenarioConfig::StageConfig& stage_config,
    const std::shared_ptr<DependencyInjector>& injector) {
  if (stage_config.stage_type() != ScenarioConfig::LANE_FOLLOW_DEFAULT_STAGE) {
    AERROR << "Follow lane does not support stage type: "
           << ScenarioConfig::StageType_Name(stage_config.stage_type());
    return nullptr;
  }
  return std::unique_ptr<Stage>(new LaneFollowStage(stage_config, injector));
}
```

其它的场景可能有多个stage，就需要先注册所有stage，然后再根据type创建当前stage：

```cpp
void ParkAndGoScenario::RegisterStages() {
  if (!s_stage_factory_.Empty()) {
    s_stage_factory_.Clear();
  }
  s_stage_factory_.Register(
      ScenarioConfig::PARK_AND_GO_CHECK,
      [](const ScenarioConfig::StageConfig& config,
         const std::shared_ptr<DependencyInjector>& injector) -> Stage* {
        return new ParkAndGoStageCheck(config, injector);
      });
  s_stage_factory_.Register(
      ScenarioConfig::PARK_AND_GO_ADJUST,
      [](const ScenarioConfig::StageConfig& config,
         const std::shared_ptr<DependencyInjector>& injector) -> Stage* {
        return new ParkAndGoStageAdjust(config, injector);
      });
  s_stage_factory_.Register(
      ScenarioConfig::PARK_AND_GO_PRE_CRUISE,
      [](const ScenarioConfig::StageConfig& config,
         const std::shared_ptr<DependencyInjector>& injector) -> Stage* {
        return new ParkAndGoStagePreCruise(config, injector);
      });
  s_stage_factory_.Register(
      ScenarioConfig::PARK_AND_GO_CRUISE,
      [](const ScenarioConfig::StageConfig& config,
         const std::shared_ptr<DependencyInjector>& injector) -> Stage* {
        return new ParkAndGoStageCruise(config, injector);
      });
}
 
std::unique_ptr<Stage> ParkAndGoScenario::CreateStage(
    const ScenarioConfig::StageConfig& stage_config,
    const std::shared_ptr<DependencyInjector>& injector) {
  if (s_stage_factory_.Empty()) {
    RegisterStages();
  }
  auto ptr = s_stage_factory_.CreateObjectOrNull(stage_config.stage_type(),
                                                 stage_config, injector);
  if (ptr) {
    ptr->SetContext(&context_);
  }
  return ptr;
}
```

2、当前stage创建好之后，在Scenario的Process()函数处会调用当前stage的Process()函数，完成当前stage具体的规划任务。

```cpp
Scenario::ScenarioStatus Scenario::Process(
    const common::TrajectoryPoint& planning_init_point, Frame* frame) {
  // stage类型unknow
  if (current_stage_ == nullptr) {
    AWARN << "Current stage is a null pointer.";
    return STATUS_UNKNOWN;
  }
  // stage全部执行完成
  if (current_stage_->stage_type() == ScenarioConfig::NO_STAGE) {
    scenario_status_ = STATUS_DONE;
    return scenario_status_;
  }
  // 当前处于某一stage，调用这个stage的Process()函数，处理具体规划逻辑
  auto ret = current_stage_->Process(planning_init_point, frame);
  // 读取当前stage完成的状态，并进行处理
  switch (ret) {
    case Stage::ERROR: {
      AERROR << "Stage '" << current_stage_->Name() << "' returns error";
      scenario_status_ = STATUS_UNKNOWN;
      break;
    }
    case Stage::RUNNING: {
      scenario_status_ = STATUS_PROCESSING;
      break;
    }
    case Stage::FINISHED: {
      auto next_stage = current_stage_->NextStage();
      if (next_stage != current_stage_->stage_type()) {
        AINFO << "switch stage from " << current_stage_->Name() << " to "
              << ScenarioConfig::StageType_Name(next_stage);
        if (next_stage == ScenarioConfig::NO_STAGE) {
          scenario_status_ = STATUS_DONE;
          return scenario_status_;
        }
        if (stage_config_map_.find(next_stage) == stage_config_map_.end()) {
          AERROR << "Failed to find config for stage: " << next_stage;
          scenario_status_ = STATUS_UNKNOWN;
          return scenario_status_;
        }
        current_stage_ = CreateStage(*stage_config_map_[next_stage], injector_);
        if (current_stage_ == nullptr) {
          AWARN << "Current stage is a null pointer.";
          return STATUS_UNKNOWN;
        }
      }
      if (current_stage_ != nullptr &&
          current_stage_->stage_type() != ScenarioConfig::NO_STAGE) {
        scenario_status_ = STATUS_PROCESSING;
      } else {
        scenario_status_ = STATUS_DONE;
      }
      break;
    }
    default: {
      AWARN << "Unexpected Stage return value: " << ret;
      scenario_status_ = STATUS_UNKNOWN;
    }
  }
  return scenario_status_;
}
```

3、在当前stage，即LANE_FOLLOW_DEFAULT_STAGE中，调用Process()函数，执行具体的固化任务。该函数的定义如下，关键步骤加了备注，稍后会详细讲解这部分代码。

```cpp
Stage::StageStatus LaneFollowStage::Process(
    const TrajectoryPoint& planning_start_point, Frame* frame) {
  bool has_drivable_reference_line = false;
 
  ADEBUG << "Number of reference lines:\t"
         << frame->mutable_reference_line_info()->size();
 
  unsigned int count = 0;
 
  // 遍历所有的参考线，直到找到可用来规划的参考线后退出
  for (auto& reference_line_info : *frame->mutable_reference_line_info()) {
    // TODO(SHU): need refactor
    if (count++ == frame->mutable_reference_line_info()->size()) {
      break;
    }
    ADEBUG << "No: [" << count << "] Reference Line.";
    ADEBUG << "IsChangeLanePath: " << reference_line_info.IsChangeLanePath();
    
    // 找到可用来规划的参考线，退出循环
    if (has_drivable_reference_line) {
      reference_line_info.SetDrivable(false);
      break;
    }
 
    // 执行具体规划任务
    auto cur_status =
        PlanOnReferenceLine(planning_start_point, frame, &reference_line_info);
    // 判断规划结果是否OK
    if (cur_status.ok()) {
      // 如果发生lanechange，判断reference_line的cost
      if (reference_line_info.IsChangeLanePath()) {
        ADEBUG << "reference line is lane change ref.";
        ADEBUG << "FLAGS_enable_smarter_lane_change: "
               << FLAGS_enable_smarter_lane_change;
        if (reference_line_info.Cost() < kStraightForwardLineCost &&
            (LaneChangeDecider::IsClearToChangeLane(&reference_line_info) ||
             FLAGS_enable_smarter_lane_change)) {
          // If the path and speed optimization succeed on target lane while
          // under smart lane-change or IsClearToChangeLane under older version
          has_drivable_reference_line = true;
          reference_line_info.SetDrivable(true);
          LaneChangeDecider::UpdatePreparationDistance(
              true, frame, &reference_line_info, injector_->planning_context());
          ADEBUG << "\tclear for lane change";
        } else {
          LaneChangeDecider::UpdatePreparationDistance(
              false, frame, &reference_line_info,
              injector_->planning_context());
          reference_line_info.SetDrivable(false);
          ADEBUG << "\tlane change failed";
        }
      } 
      // 如果没有lanechange，stage执行结果为OK，则has_drivable_reference_line置位true
      else {
        ADEBUG << "reference line is NOT lane change ref.";
        has_drivable_reference_line = true;
      }
    } else {
      reference_line_info.SetDrivable(false);
    }
  }
  
  // 根据has_drivable_reference_line这个标志位的结果，返回stage执行的结果
  return has_drivable_reference_line ? StageStatus::RUNNING
                                     : StageStatus::ERROR;
}
```

通过以上代码看到，当变道时针对目标车道进行规划，如果规划成功后，还需要判断目标车道的变道cost，如果cost太高，那么就会舍弃掉这条目标车道的reference_line, 此时放弃变道的规划，继续循环使用原车道的reference_line进行规划。

4、LANE_FOLLOW_DEFAULT_STAGE的规划逻辑在 PlanOnReferenceLine() 函数，这个函数的实现如下，关键步骤加了备注：

```cpp
Status LaneFollowStage::PlanOnReferenceLine(
    const TrajectoryPoint& planning_start_point, Frame* frame,
    ReferenceLineInfo* reference_line_info) {
  if (!reference_line_info->IsChangeLanePath()) {
    reference_line_info->AddCost(kStraightForwardLineCost);
  }
  ADEBUG << "planning start point:" << planning_start_point.DebugString();
  ADEBUG << "Current reference_line_info is IsChangeLanePath: "
         << reference_line_info->IsChangeLanePath();
 
  auto ret = Status::OK();
  for (auto* task : task_list_) {
    const double start_timestamp = Clock::NowInSeconds();
 
    ret = task->Execute(frame, reference_line_info);
 
    const double end_timestamp = Clock::NowInSeconds();
    const double time_diff_ms = (end_timestamp - start_timestamp) * 1000;
    ADEBUG << "after task[" << task->Name()
           << "]:" << reference_line_info->PathSpeedDebugString();
    ADEBUG << task->Name() << " time spend: " << time_diff_ms << " ms.";
    RecordDebugInfo(reference_line_info, task->Name(), time_diff_ms);
 
    if (!ret.ok()) {
      AERROR << "Failed to run tasks[" << task->Name()
             << "], Error message: " << ret.error_message();
      break;
    }
 
    // TODO(SHU): disable reference line order changes for now
    // updated reference_line_info, because it is changed in
    // lane_change_decider by PrioritizeChangeLane().
    // reference_line_info = &frame->mutable_reference_line_info()->front();
    // ADEBUG << "Current reference_line_info is IsChangeLanePath: "
    //        << reference_line_info->IsChangeLanePath();
  }
 
  RecordObstacleDebugInfo(reference_line_info);
 
  // check path and speed results for path or speed fallback
  reference_line_info->set_trajectory_type(ADCTrajectory::NORMAL);
  if (!ret.ok()) {
    PlanFallbackTrajectory(planning_start_point, frame, reference_line_info);
  }
 
  DiscretizedTrajectory trajectory;
  if (!reference_line_info->CombinePathAndSpeedProfile(
          planning_start_point.relative_time(),
          planning_start_point.path_point().s(), &trajectory)) {
    const std::string msg = "Fail to aggregate planning trajectory.";
    AERROR << msg;
    return Status(ErrorCode::PLANNING_ERROR, msg);
  }
 
  // determine if there is a destination on reference line.
  double dest_stop_s = -1.0;
  for (const auto* obstacle :
       reference_line_info->path_decision()->obstacles().Items()) {
    if (obstacle->LongitudinalDecision().has_stop() &&
        obstacle->LongitudinalDecision().stop().reason_code() ==
            STOP_REASON_DESTINATION) {
      SLPoint dest_sl = GetStopSL(obstacle->LongitudinalDecision().stop(),
                                  reference_line_info->reference_line());
      dest_stop_s = dest_sl.s();
    }
  }
 
  for (const auto* obstacle :
       reference_line_info->path_decision()->obstacles().Items()) {
    if (obstacle->IsVirtual()) {
      continue;
    }
    if (!obstacle->IsStatic()) {
      continue;
    }
    if (obstacle->LongitudinalDecision().has_stop()) {
      bool add_stop_obstacle_cost = false;
      if (dest_stop_s < 0.0) {
        add_stop_obstacle_cost = true;
      } else {
        SLPoint stop_sl = GetStopSL(obstacle->LongitudinalDecision().stop(),
                                    reference_line_info->reference_line());
        if (stop_sl.s() < dest_stop_s) {
          add_stop_obstacle_cost = true;
        }
      }
      if (add_stop_obstacle_cost) {
        static constexpr double kReferenceLineStaticObsCost = 1e3;
        reference_line_info->AddCost(kReferenceLineStaticObsCost);
      }
    }
  }
 
  if (FLAGS_enable_trajectory_check) {
    if (ConstraintChecker::ValidTrajectory(trajectory) !=
        ConstraintChecker::Result::VALID) {
      const std::string msg = "Current planning trajectory is not valid.";
      AERROR << msg;
      return Status(ErrorCode::PLANNING_ERROR, msg);
    }
  }
 
  reference_line_info->SetTrajectory(trajectory);
  reference_line_info->SetDrivable(true);
  return Status::OK();
}
```

5、本章节主要以LANE_FOLLOW_DEFAULT_STAGE为例，介绍了apollo中stage这个模块的运行机制，LANE_FOLLOW_DEFAULT_STAGE是NOP功能下最常用到的一个stage，在下一章节会详细介绍这个stage的逻辑。