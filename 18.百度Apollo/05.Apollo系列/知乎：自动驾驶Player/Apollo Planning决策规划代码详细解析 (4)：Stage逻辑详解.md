- [Apollo Planning决策规划代码详细解析 (4)：Stage逻辑详解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/495905987)

**在本文你将学到下面这些内容：**

- Stage中如何对task进行注册；
- Stage中如何调用task执行规划任务；
- LaneFollowStage 包含哪些task；
- LaneFollowStage 的详细运行逻辑；
- PlanOnReferenceLine() 规划算法的运行逻辑

**代码具体过程如下：**

1、之前的章节讲到，Apollo在每个palnning周期首先决策当前处于哪个场景Scenario下面的哪个状态stage当中，当确认好stage之后，便会调用这个stage的Process() 函数来执行具体的规划逻辑。stage::Process() 主要逻辑是根据配置文件，来依次执行每一个注册的task的Execute() 函数，从而把具体的规划任务分散到每个task当中。stage并不属于某个特定的Scenario，task也不属于某个特定的stage，这些任务的组合通过配置文件进行配置，这样便保证了整个规划模块的解耦与可扩展性。

![img](https://pic3.zhimg.com/80/v2-be4cf4f219aa58b16acef4f40b65eb8a_720w.jpg)

2、接下来将介绍最常用的LaneFollowStage，task的注册发生在 LaneFollowStage 对象建立阶段，LaneFollowStage的构造函数继承了Stage类的构造函数，在建立对象时读取配置文件，并对当前stage包含的task进行注册。

LaneFollowStage的构造函数对Stage的构造函数进行继承：

```cpp
LaneFollowStage::LaneFollowStage(
    const ScenarioConfig::StageConfig& config,
    const std::shared_ptr<DependencyInjector>& injector)
    : Stage(config, injector) {}
```

Stage的构造函数中读取配置文件，并且注册每个task，并保存至全局变量task_list_：

```cpp
Stage::Stage(const ScenarioConfig::StageConfig& config,
             const std::shared_ptr<DependencyInjector>& injector)
    : config_(config), injector_(injector) {
  // set stage_type in PlanningContext
  injector->planning_context()
      ->mutable_planning_status()
      ->mutable_scenario()
      ->set_stage_type(stage_type());
 
  name_ = ScenarioConfig::StageType_Name(config_.stage_type());
  next_stage_ = config_.stage_type();
  std::unordered_map<TaskConfig::TaskType, const TaskConfig*, std::hash<int>>
      config_map;
  for (const auto& task_config : config_.task_config()) {
    config_map[task_config.task_type()] = &task_config;
  }
  for (int i = 0; i < config_.task_type_size(); ++i) {
    auto task_type = config_.task_type(i);
    ACHECK(config_map.find(task_type) != config_map.end())
        << "Task: " << TaskConfig::TaskType_Name(task_type)
        << " used but not configured";
    auto iter = tasks_.find(task_type);
    if (iter == tasks_.end()) {
      auto ptr = TaskFactory::CreateTask(*config_map[task_type], injector_);
      task_list_.push_back(ptr.get());
      tasks_[task_type] = std::move(ptr);
    } else {
      task_list_.push_back(iter->second.get());
    }
  }
}
```

3、LaneFollowStage 的Process() 函数执行主要的规划逻辑，在函数内部会对变道的效率来进行判断从而选择是否按照变道进行规划，或者保持本车道运行，关于变道判断逻辑上一章节已经分析。在LaneFollowStage 的Process() 函数中，另外一个重要的函数是PlanOnReferenceLine() , 在这个函数中实现了对每个task的依次调用，关于这个函数的定义及备注如下：

```cpp
Status LaneFollowStage::PlanOnReferenceLine(
    const TrajectoryPoint& planning_start_point, Frame* frame,
    ReferenceLineInfo* reference_line_info) {
  // 判断是否有lanechange意图，如果有计算cost
  if (!reference_line_info->IsChangeLanePath()) {
    reference_line_info->AddCost(kStraightForwardLineCost);
  }
  ADEBUG << "planning start point:" << planning_start_point.DebugString();
  ADEBUG << "Current reference_line_info is IsChangeLanePath: "
         << reference_line_info->IsChangeLanePath();
  // 遍历每个task，即把注册的task运行一遍
  auto ret = Status::OK();
  for (auto* task : task_list_) {
    // 记录起始时间戳
    const double start_timestamp = Clock::NowInSeconds();
    // 执行每个task的具体逻辑
    ret = task->Execute(frame, reference_line_info);
    // 记录当前task的结束时间
    const double end_timestamp = Clock::NowInSeconds();
    // 计算当前task的运行时间
    const double time_diff_ms = (end_timestamp - start_timestamp) * 1000;
    ADEBUG << "after task[" << task->Name()
           << "]:" << reference_line_info->PathSpeedDebugString();
    ADEBUG << task->Name() << " time spend: " << time_diff_ms << " ms.";
    RecordDebugInfo(reference_line_info, task->Name(), time_diff_ms);
    // 如果task执行失败，退出task执行序列，并且记录失败信息
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
    // 如果task执行失败，则使用备用的规划轨迹
    PlanFallbackTrajectory(planning_start_point, frame, reference_line_info);
  }
  
  // 对规划的轨迹进行合成，如果合成失败，返回失败状态
  DiscretizedTrajectory trajectory;
  if (!reference_line_info->CombinePathAndSpeedProfile(
          planning_start_point.relative_time(),
          planning_start_point.path_point().s(), &trajectory)) {
    const std::string msg = "Fail to aggregate planning trajectory.";
    AERROR << msg;
    return Status(ErrorCode::PLANNING_ERROR, msg);
  }
 
  // determine if there is a destination on reference line.
  // 对目的终点进行处理
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
 
  // 对规划的轨迹进行检查，如果检查失败，返回失败状态
  if (FLAGS_enable_trajectory_check) {
    if (ConstraintChecker::ValidTrajectory(trajectory) !=
        ConstraintChecker::Result::VALID) {
      const std::string msg = "Current planning trajectory is not valid.";
      AERROR << msg;
      return Status(ErrorCode::PLANNING_ERROR, msg);
    }
  }
  
  // 存放规划的结果
  reference_line_info->SetTrajectory(trajectory);
  reference_line_info->SetDrivable(true);
  return Status::OK();
}
```

4、LaneFollowStage 注册了以下这些task，都会被顺序执行，这些task封装了具体的规划逻辑，后续的章节会介绍具体的规划算法。

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

5、task的被调用流程图如下：

![img](https://pic4.zhimg.com/80/v2-925d7f3b7613fe5e0834f576bfc1dccf_720w.jpg)