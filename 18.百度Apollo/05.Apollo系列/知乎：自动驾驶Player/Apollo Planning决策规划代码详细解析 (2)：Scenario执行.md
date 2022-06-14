- [Apollo Planning决策规划代码详细解析 (2)：Scenario执行 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/494816954)

**在本文你将学到下面这些内容：**

- Scenario的Process()函数的作用于调用方式；
- Scenario对象的初始化方法；
- 如何给每个Scenario设置对应的stage；
- Apollo的stage概念；
- 如何确定在当前场景下，程序处于哪个stage当中；
- Apollo规划算法的运行机制

**代码具体过程如下：**

1、上一章节讲解了当前Scenario如何决策处于哪个场景，当获取到当前场景后，apollo在planer中调用当前Scenario对象的Process()函数，来执行这个场景对应的逻辑，在Scenario这一层的逻辑主要是确认当前处于哪个stage当中。

planer中对Scenario的Process()函数调用如下：

```cpp
Status PublicRoadPlanner::Plan(const TrajectoryPoint& planning_start_point,
                               Frame* frame,
                               ADCTrajectory* ptr_computed_trajectory) {
  // 决策当前应该执行哪个场景
  scenario_manager_.Update(planning_start_point, *frame);
  // 获取当前场景
  scenario_ = scenario_manager_.mutable_scenario();
  // 处理当前场景
  auto result = scenario_->Process(planning_start_point, frame);
  // 打印debug信息
  if (FLAGS_enable_record_debug) {
    auto scenario_debug = ptr_computed_trajectory->mutable_debug()
                              ->mutable_planning_data()
                              ->mutable_scenario();
    scenario_debug->set_scenario_type(scenario_->scenario_type());
    scenario_debug->set_stage_type(scenario_->GetStage());
    scenario_debug->set_msg(scenario_->GetMsg());
  }
  // 场景处理成功
  if (result == scenario::Scenario::STATUS_DONE) {
    // only updates scenario manager when previous scenario's status is
    // STATUS_DONE
    scenario_manager_.Update(planning_start_point, *frame);
  } 
  // 场景处理失败
  else if (result == scenario::Scenario::STATUS_UNKNOWN) {
    return Status(common::PLANNING_ERROR, "scenario returned unknown");
  }
  return Status::OK();
}
```

2、在实例化Scenario后会调用它的LoadConfig()与void Scenario::Init() 两个函数，读取配置文件，并且初始化当前对象，注册配置文件中定义的该场景拥有的stage。

```cpp
// 读取配置文件
bool Scenario::LoadConfig(const std::string& config_file,
                          ScenarioConfig* config) {
  return apollo::cyber::common::GetProtoFromFile(config_file, config);
}
 
// Scenario初始化
void Scenario::Init() {
  ACHECK(!config_.stage_type().empty());
 
  // set scenario_type in PlanningContext
  auto* scenario = injector_->planning_context()
                       ->mutable_planning_status()
                       ->mutable_scenario();
  scenario->Clear();
  scenario->set_scenario_type(scenario_type());
 
  for (const auto& stage_config : config_.stage_config()) {
    stage_config_map_[stage_config.stage_type()] = &stage_config;
  }
  for (int i = 0; i < config_.stage_type_size(); ++i) {
    auto stage_type = config_.stage_type(i);
    ACHECK(common::util::ContainsKey(stage_config_map_, stage_type))
        << "stage type : " << ScenarioConfig::StageType_Name(stage_type)
        << " has no config";
  }
  ADEBUG << "init stage "
         << ScenarioConfig::StageType_Name(config_.stage_type(0));
  // 初始化后分配该场景下的默认stage
  current_stage_ =
      CreateStage(*stage_config_map_[config_.stage_type(0)], injector_);
}
```

3、场景的执行在"[scenario.cc](https://link.zhihu.com/?target=http%3A//scenario.cc/)"和对应的场景目录中，在apollo中每个场景由一个或者多个阶段(stage)注册而来，每个阶段stage又由不同的任务(task)组成。执行一个场景，就是顺序执行不同阶段的不同任务。在每一个规划周期，所谓决策的其中一个作用就是定位到当前所在的scenario以及stage，并且按顺序执行完当前stage中注册的所有task。

![img](https://pic4.zhimg.com/80/v2-c02c93248c071a8038a6c29c1741e30b_720w.jpg)

4、下面我们来看一个具体的多个stage组成scenario的例子，以scenario_type: SIDE_PASS为例，Scenario对应的stage和task在"planning/conf/scenario"中进行配置。

```protobuf
// Scenario对应的Stage
scenario_type: SIDE_PASS
stage_type: SIDE_PASS_APPROACH_OBSTACLE
stage_type: SIDE_PASS_GENERATE_PATH
stage_type: SIDE_PASS_STOP_ON_WAITPOINT
stage_type: SIDE_PASS_DETECT_SAFETY
stage_type: SIDE_PASS_PASS_OBSTACLE
stage_type: SIDE_PASS_BACKUP
 
// Stage对应的Task
stage_type: SIDE_PASS_APPROACH_OBSTACLE
enabled: true
task_type: DP_POLY_PATH_OPTIMIZER
task_type: PATH_DECIDER
task_type: SPEED_BOUNDS_PRIORI_DECIDER
task_type: DP_ST_SPEED_OPTIMIZER
task_type: SPEED_DECIDER
task_type: SPEED_BOUNDS_FINAL_DECIDER
task_type: QP_SPLINE_ST_SPEED_OPTIMIZER
 
// 以此类推
```

目前常用的lane follow这个 scenario_type只定义了1个stage：

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
 
  task_config: {
    task_type: LANE_CHANGE_DECIDER
    lane_change_decider_config {
      enable_lane_change_urgency_check: true
    }
  }
 
  task_config: {
    task_type: PATH_REUSE_DECIDER
    path_reuse_decider_config {
      reuse_path: false
    }
  }
 
  task_config: {
    task_type: PATH_LANE_BORROW_DECIDER
    path_lane_borrow_decider_config {
      allow_lane_borrowing: true
    }
  }
  
}
```

5、由于Scenario的stage都是顺序执行，只需要判断这一阶段是否结束，然后转到下一个阶段就可以了。具体的实现在Scenario::Process() ：

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

以lane follow这个scenario的调试过程为例，可以看到此时就处在了LANE_FOLLOW_DEFAULT_STAGE 当中，而LANE_FOLLOW_DEFAULT_STAGE注册的task正是配置文件中定义的任务列表：

![img](https://pic2.zhimg.com/80/v2-a8cfb916ed63bf0ffe712a1dc75f49a1_720w.jpg)

6、以上即为apollo中Scenario处理的过程，即根据配置文件顺序执行stage，并判断目前处于哪个stage。当确认好stage后调用该stage对应的Process()函数，来进行具体的规划任务。