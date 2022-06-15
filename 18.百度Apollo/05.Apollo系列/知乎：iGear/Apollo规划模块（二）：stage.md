- [Apollo规划模块（二）：stage - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/421814639)

在planning模块确定scenarios之后就会进入stage阶段，stage是plannin衔接scenarios与后具体执行器task的中间阶段。

以最简单的lane_follow为例，在scenarios中会调用[http://lane_follow_scenarios.cc](https://link.zhihu.com/?target=http%3A//lane_follow_scenarios.cc)的中的 LaneFollowScenario::CreateStage创建LaneFollowStage，而具体的LaneFollowStage的具体实现则在lane_follow_stage中。

![img](https://pic4.zhimg.com/80/v2-98321e2e60bfd61f0c0d86fcb90e959b_720w.jpg)图1 lane_follow

执行具体stage的逻辑的入口则在[http://scenario.cc](https://link.zhihu.com/?target=http%3A//scenario.cc)中的Process中，根据最后一行的current_stage_->Process() 会调用到lane_follow_stage中的LaneFollowStage::Process函数，并返回目前stage的状态。

![img](https://pic4.zhimg.com/80/v2-96c9922f286af3c20bcf6f6ae93f65b7_720w.jpg)



在配置文件中我们看见，LANE_FOLLOW只注册了一个stage，这个stage中注册了一系列的task。而apollo的stage会在LaneFollowStage::Process函数中的LaneFollowStage::PlanOnReferenceLine中对所有注册的Task执行task->Execute()。

![img](https://pic4.zhimg.com/80/v2-5f0cc4db82825092a03b588e8b547ad7_720w.jpg)

上面提到的LANE_FOLLOW是一个只包含一个stage的场景，下面再以一个多stage的场景举例。

首先在配置文件方面。BARE_INTERSECTION_UNPROTECTED注册了APPROACH与INTERSECTION_CRUISE两个阶段。每个阶段分别注册了不同的TASK。

```yaml
scenario_type: BARE_INTERSECTION_UNPROTECTED
bare_intersection_unprotected_config: {
  start_bare_intersection_scenario_distance: 25.0
  enable_explicit_stop: false
  min_pass_s_distance: 3.0
  approach_cruise_speed: 6.7056  # 15 mph
  stop_distance: 0.5
  stop_timeout_sec: 8.0
  creep_timeout_sec: 10.0
}
stage_type: BARE_INTERSECTION_UNPROTECTED_APPROACH
stage_type: BARE_INTERSECTION_UNPROTECTED_INTERSECTION_CRUISE

stage_config: {
  stage_type: BARE_INTERSECTION_UNPROTECTED_APPROACH
  enabled: true
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
  task_type: PIECEWISE_JERK_NONLINEAR_SPEED_OPTIMIZER
  ...
  }
 
stage_config: {
  stage_type: BARE_INTERSECTION_UNPROTECTED_INTERSECTION_CRUISE
  enabled: true
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
  task_type: PIECEWISE_JERK_NONLINEAR_SPEED_OPTIMIZER
  ...
  }
```

在scenarios的具体实现中，apollo分别在bare_instersection中创建了>BareIntersectionUnprotectedScenario（bare_intersection_unprotected_scenario）类， BareIntersectionUnprotectedStageApproach（stage_approach）类与BareIntersectionUnprotectedStageIntersectionCruise（stage_intersection_cruise）类。其中注册的BARE_INTERSECTION_UNPROTECTED_APPROACH与BARE_INTERSECTION_UNPROTECTED_INTERSECTION_CRUISE这两个阶段分别对应了BareIntersectionUnprotectedStageApproach类与BareIntersectionUnprotectedStageIntersectionCruise类。这两个阶段会在BareIntersectionUnprotectedScenario::RegisterStages函数中被创建。

```python
void BareIntersectionUnprotectedScenario::RegisterStages() {
  if (!s_stage_factory_.Empty()) {
    s_stage_factory_.Clear();
  }
  s_stage_factory_.Register(
      ScenarioConfig::BARE_INTERSECTION_UNPROTECTED_APPROACH,
      [](const ScenarioConfig::StageConfig& config,
         const std::shared_ptr<DependencyInjector>& injector) -> Stage* {
        return new BareIntersectionUnprotectedStageApproach(config, injector);
      });
  s_stage_factory_.Register(
      ScenarioConfig::BARE_INTERSECTION_UNPROTECTED_INTERSECTION_CRUISE,
      [](const ScenarioConfig::StageConfig& config,
         const std::shared_ptr<DependencyInjector>& injector) -> Stage* {
        return new BareIntersectionUnprotectedStageIntersectionCruise(config,
                                                                      injector);
      });
}
```

每个stage具体实现和LANE_FOLLOW一样是stage::Process执行。最终会返回这个stage的状态ERROR，READY，RUNNING与FINISHED。