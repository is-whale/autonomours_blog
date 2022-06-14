- [Apollo Planning决策规划代码详细解析 (1)：Scenario选择 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/494813220)

**Scenario是apollo决策规划算法中的重要概念，apollo可以应对自动驾驶所面临的不同道路场景，都是通过Scenario统一注册与管理；Scenario通过一个有限状态机来选择不同场景。**

**本文重点讲解Apollo代码中怎样配置Scenario以及选择当前Scenario，Scenario决策是Apollo规划算法的第一步，本文会对代码进行详细解析，也会梳理整个决策流程。码字不易，喜欢的朋友们麻烦点个关注与赞。**

**Apollo Planning决策规划代码将在以下系列做详细解析：**

[Apollo Planning决策规划代码详细解析 (1)：Scenario选择 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/494813220)

[自动驾驶Player：Apollo Planning决策规划代码详细解析 (2)：Scenario执行](https://zhuanlan.zhihu.com/p/494816954)

[自动驾驶Player：Apollo Planning决策规划代码详细解析 (3)：stage执行](https://zhuanlan.zhihu.com/p/495902084)

[自动驾驶Player：Apollo Planning决策规划代码详细解析 (4)：Stage逻辑详解](https://zhuanlan.zhihu.com/p/495905987)

[自动驾驶Player：Apollo Planning决策规划代码详细解析 (5)：规划算法流程介绍](https://zhuanlan.zhihu.com/p/496018011)

[自动驾驶Player：Apollo Planning决策规划代码详细解析 (6):LaneChangeDecider](https://zhuanlan.zhihu.com/p/496498679)

[自动驾驶Player：Apollo Planning决策规划代码详细解析 (7): PathReuseDecider](https://zhuanlan.zhihu.com/p/497489782)

[自动驾驶Player：Apollo Planning决策规划代码详细解析 (8): PathLaneBorrowDecider](https://zhuanlan.zhihu.com/p/497503888)

[自动驾驶Player：Apollo Planning决策规划代码详细解析 (9): PathBoundsDecider](https://zhuanlan.zhihu.com/p/497513085)

[自动驾驶Player：Apollo Planning决策规划代码详细解析 (11): PathAssessmentDecider](https://zhuanlan.zhihu.com/p/503287245)

[自动驾驶Player：Apollo Planning决策规划代码详细解析 (12): PathDecider](https://zhuanlan.zhihu.com/p/503397091)

[Apollo Planning决策规划算法代码详细解析 (13): RuleBasedStopDecider](https://link.zhihu.com/?target=https%3A//blog.csdn.net/nn243823163/article/details/124506678)

[Apollo Planning决策规划算法代码详细解析 (14):SPEED_BOUNDS_PRIORI_DECIDER](https://link.zhihu.com/?target=https%3A//blog.csdn.net/nn243823163/article/details/124545583)

[Apollo Planning决策规划算法代码详细解析 (15): 速度动态规划SPEED_HEURISTIC_OPTIMIZER 上](https://link.zhihu.com/?target=https%3A//blog.csdn.net/nn243823163/article/details/124619461)

[Apollo Planning决策规划算法代码详细解析 (16):SPEED_HEURISTIC_OPTIMIZER 速度动态规划中](https://link.zhihu.com/?target=https%3A//blog.csdn.net/nn243823163/article/details/124704179)

[Apollo Planning决策规划算法代码解析 (17):SPEED_HEURISTIC_OPTIMIZER 速度动态规划下](https://link.zhihu.com/?target=https%3A//blog.csdn.net/nn243823163/article/details/124772521%3Fcsdn_share_tail%3D%7B%22type%22%3A%22blog%22%2C%22rType%22%3A%22article%22%2C%22rId%22%3A%22124772521%22%2C%22source%22%3A%22nn243823163%22%7D%26ctrtid%3DhfHXY)



**在本文你将学到下面这些内容：**

- 规划器planer的种类；
- 规划器planer的主要函数及逻辑；
- 场景管理类ScenarioManager的运行机制；
- 场景注册方法；
- 场景决策流程，如何选择当前场景
- 详细的apollo决策规划代码分析

**代码具体过程如下：**

0、规划算法的入口

- （1）规划模块的入口函数是PlanningComponent的Proc。
- （2）以规划模式OnLanePlanning，执行RunOnce。在RunOnce中**先执行交通规则**，再规划轨迹。规划轨迹的函数是Plan。

1、Scenario的判断在Planer中进行，目前Apollo共有下面这些planer，其中最常用的就是EM规划器，即PublicRoadPlanner，本系列主要介绍PublicRoadPlanner这个Planer。

![img](https://pic1.zhimg.com/80/v2-5358dcab82a13e4c78437cde270b1cc4_720w.jpg)

2、Apollo会根据配置调用PublicRoadPlanner这个planer，关于配置方法，之后会在另外一篇博文进行更新。PublicRoadPlanner主要有init()与plan()两个重要的函数，inti()是规划器的初始化，plan就是具体的规划过程。

```cpp
class PublicRoadPlanner : public PlannerWithReferenceLine {
 public:
  /**
   * @brief Constructor
   */
  PublicRoadPlanner() = delete;
 
  explicit PublicRoadPlanner(
      const std::shared_ptr<DependencyInjector>& injector)
      : PlannerWithReferenceLine(injector) {}
 
  /**
   * @brief Destructor
   */
  virtual ~PublicRoadPlanner() = default;
 
  void Stop() override {}
 
  std::string Name() override { return "PUBLIC_ROAD"; }
 
  common::Status Init(const PlanningConfig& config) override;
 
  /**
   * @brief Override function Plan in parent class Planner.
   * @param planning_init_point The trajectory point where planning starts.
   * @param frame Current planning frame.
   * @return OK if planning succeeds; error otherwise.
   */
  common::Status Plan(const common::TrajectoryPoint& planning_init_point,
                      Frame* frame,
                      ADCTrajectory* ptr_computed_trajectory) override;
};
```

3、scenario的选择在Plan() 函数的update阶段，主要用的是ScenarioManager类的updata函数。ScenarioManager并不属于某个特定的planer，这个类只针对于scenario，每个planer都可以调用它来管理场景。下面代码片段是PublicRoadPlanner的Plan()函数。

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

4、ScenarioManager会实例化一个全局的scenario_manager_对象来进行场景管理，在PublicRoadPlanner初始化时会调用配置文件里的参数来建立这个对象。

```cpp
Status PublicRoadPlanner::Init(const PlanningConfig& config) {
  config_ = config;
  scenario_manager_.Init(config);
  return Status::OK();
}
```

调用ScenarioManager类的init()函数，并且根据当前planer的配置来注册场景。

```cpp
bool ScenarioManager::Init(
    const std::set<ScenarioConfig::ScenarioType>& supported_scenarios) {
  // 注册场景
  RegisterScenarios();
  default_scenario_type_ = ScenarioConfig::LANE_FOLLOW;
  supported_scenarios_ = supported_scenarios;
  // 创建场景，默认为lane_follow
  current_scenario_ = CreateScenario(default_scenario_type_);
  return true;
}
```

目前PublicRoadPlanner支持下面这些场景

```protobuf
// 还是在"/conf/planning_config.pb.txt"中
standard_planning_config {
  planner_type: PUBLIC_ROAD
  planner_type: OPEN_SPACE
  planner_public_road_config {
     // 支持的场景
     scenario_type: LANE_FOLLOW  // 车道线保持
     scenario_type: SIDE_PASS    // 超车
     scenario_type: STOP_SIGN_UNPROTECTED  // 停止
     scenario_type: TRAFFIC_LIGHT_PROTECTED    // 红绿灯
     scenario_type: TRAFFIC_LIGHT_UNPROTECTED_LEFT_TURN  // 红绿灯左转
     scenario_type: TRAFFIC_LIGHT_UNPROTECTED_RIGHT_TURN // 红绿灯右转
     scenario_type: VALET_PARKING  // 代客泊车
  }
```

5、ScenarioManager类的Update()函数，用来决策当前处在什么场景。如果进入了新的场景，会创建一个新的对象来进行之后的规划逻辑。

```cpp
void ScenarioManager::Update(const common::TrajectoryPoint& ego_point,
                             const Frame& frame) {
  ACHECK(!frame.reference_line_info().empty());
 
  Observe(frame);
 
  ScenarioDispatch(frame);
}
```

场景决策逻辑在ScenarioDispatch(frame)当中，会根据配置选择基于规则还是基于学习的决策方法。

```cpp
void ScenarioManager::ScenarioDispatch(const Frame& frame) {
  ACHECK(!frame.reference_line_info().empty());
  ScenarioConfig::ScenarioType scenario_type;
 
  int history_points_len = 0;
  if (injector_->learning_based_data() &&
      injector_->learning_based_data()->GetLatestLearningDataFrame()) {
    history_points_len = injector_->learning_based_data()
                                  ->GetLatestLearningDataFrame()
                                  ->adc_trajectory_point_size();
  }
  if ((planning_config_.learning_mode() == PlanningConfig::E2E ||
       planning_config_.learning_mode() == PlanningConfig::E2E_TEST) &&
      history_points_len >= FLAGS_min_past_history_points_len) {
    scenario_type = ScenarioDispatchLearning();
  } else {
    scenario_type = ScenarioDispatchNonLearning(frame);
  }
 
  ADEBUG << "select scenario: "
         << ScenarioConfig::ScenarioType_Name(scenario_type);
 
  // update PlanningContext
  UpdatePlanningContext(frame, scenario_type);
 
  if (current_scenario_->scenario_type() != scenario_type) {
    current_scenario_ = CreateScenario(scenario_type);
  }
}
```

6、ScenarioDispatchNonLearning()函数默认从lanefollow场景开始判断，首先根据驾驶员的意图来安排场景，如果不是默认的lanefollow场景，直接输出当前场景；如果是lanefollow场景，会依次判断是否属于别的场景；即剩余场景之间的跳转必须经过lanefollow这个场景。

```cpp
ScenarioConfig::ScenarioType ScenarioManager::ScenarioDispatchNonLearning(
    const Frame& frame) {
  
  // default: LANE_FOLLOW
  ScenarioConfig::ScenarioType scenario_type = default_scenario_type_;
  
  // Pad Msg scenario
  scenario_type = SelectPadMsgScenario(frame);
 
  const auto vehicle_state_provider = injector_->vehicle_state();
  common::VehicleState vehicle_state = vehicle_state_provider->vehicle_state();
  const common::PointENU& target_point =
  frame.local_view().routing->routing_request().dead_end_info().target_point();
  const common::VehicleState& car_position = frame.vehicle_state();
  if (scenario_type == default_scenario_type_) {
    // check current_scenario (not switchable)
    switch (current_scenario_->scenario_type()) {
      case ScenarioConfig::LANE_FOLLOW:
      case ScenarioConfig::PULL_OVER:
        break;
      case ScenarioConfig::BARE_INTERSECTION_UNPROTECTED:
      case ScenarioConfig::EMERGENCY_PULL_OVER:
      case ScenarioConfig::PARK_AND_GO:
      case ScenarioConfig::STOP_SIGN_PROTECTED:
      case ScenarioConfig::STOP_SIGN_UNPROTECTED:
      case ScenarioConfig::TRAFFIC_LIGHT_PROTECTED:
      case ScenarioConfig::TRAFFIC_LIGHT_UNPROTECTED_LEFT_TURN:
      case ScenarioConfig::TRAFFIC_LIGHT_UNPROTECTED_RIGHT_TURN:
      case ScenarioConfig::VALET_PARKING:
      case ScenarioConfig::DEADEND_TURNAROUND:
        // transfer dead_end to lane follow, should enhance transfer logic
        if (JudgeReachTargetPoint(car_position, target_point)) {
          scenario_type = ScenarioConfig::LANE_FOLLOW;
          reach_target_pose_ = true;
        }
      case ScenarioConfig::YIELD_SIGN:
        // must continue until finish
        if (current_scenario_->GetStatus() !=
            Scenario::ScenarioStatus::STATUS_DONE) {
          scenario_type = current_scenario_->scenario_type();
        }
        break;
      default:
        break;
    }
  }
  
  // ParkAndGo / starting scenario
  if (scenario_type == default_scenario_type_) {
    if (FLAGS_enable_scenario_park_and_go && !reach_target_pose_) {
      scenario_type = SelectParkAndGoScenario(frame);
    }
  }
 
  
  // intersection scenarios
  if (scenario_type == default_scenario_type_) {
    scenario_type = SelectInterceptionScenario(frame);
  }
 
  
  // pull-over scenario
  if (scenario_type == default_scenario_type_) {
    if (FLAGS_enable_scenario_pull_over) {
      scenario_type = SelectPullOverScenario(frame);
    }
  }
 
  
  // VALET_PARKING scenario
  if (scenario_type == default_scenario_type_) {
    scenario_type = SelectValetParkingScenario(frame);
  }
  
  // dead end
  if (scenario_type == default_scenario_type_) {
    scenario_type = SelectDeadEndScenario(frame);
  }
  
  return scenario_type;
}
```

7、在场景判断时，首先调用函数SelectPadMsgScenario()，根据驾驶员意图来安排场景.

```cpp
ScenarioConfig::ScenarioType ScenarioManager::SelectPadMsgScenario(
    const Frame& frame) {
  const auto& pad_msg_driving_action = frame.GetPadMsgDrivingAction();
  switch (pad_msg_driving_action) {
    case DrivingAction::PULL_OVER:
      if (FLAGS_enable_scenario_emergency_pull_over) {
        return ScenarioConfig::EMERGENCY_PULL_OVER;
      }
      break;
    case DrivingAction::STOP:
      if (FLAGS_enable_scenario_emergency_stop) {
        return ScenarioConfig::EMERGENCY_STOP;
      }
      break;
    case DrivingAction::RESUME_CRUISE:
      if (current_scenario_->scenario_type() ==
              ScenarioConfig::EMERGENCY_PULL_OVER ||
          current_scenario_->scenario_type() ==
              ScenarioConfig::EMERGENCY_STOP) {
        return ScenarioConfig::PARK_AND_GO;
      }
      break;
    default:
      break;
  }
  return default_scenario_type_;
}
```

8、可以看到，除了驾驶员行为相关的两个场景外，每次切换场景必须是从默认场景(LANE_FOLLOW)开始，即每次场景切换之后都会回到默认场景。

![img](https://pic3.zhimg.com/80/v2-62445e9b1ab89b9b57a24fa577a96eb2_720w.jpg)

9、以上即为apollo场景决策逻辑。后续文章会讲场景选择之后，怎么进行下一步的规划算法。

**算法详细介绍文章：**

[自动驾驶Player：自动驾驶算法详解(4): 横向LQR、纵向PID控制进行轨迹跟踪以及python实现](https://zhuanlan.zhihu.com/p/509252743)

[自动驾驶Player：自动驾驶算法详解(3) : LQR算法进行轨迹跟踪，lqr_speed_steering_control( )的python实现](https://zhuanlan.zhihu.com/p/504299366)

**apollo驾驶仿真技术文章：**

[自动驾驶Player：Apollo规划决策算法仿真调试(1)：使用Vscode断点调试apollo的方法](https://zhuanlan.zhihu.com/p/506275430)

[自动驾驶Player：Apollo规划决策算法仿真调试(2)：使用bazel 编译自定义代码模块](https://zhuanlan.zhihu.com/p/508512439)

[自动驾驶Player：Apollo规划决策算法仿真调试(3)：ReferenceLineProvider参考线生成流程](https://zhuanlan.zhihu.com/p/511392608)

[自动驾驶Player：Apollo规划决策算法仿真调试(4):动态障碍物绕行](https://zhuanlan.zhihu.com/p/511997496)