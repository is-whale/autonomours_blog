- [Apollo Planning决策规划代码详细解析 (9): PathBoundsDecider - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/497513085)

**PathBoundsDecider 是第四个task，PathBoundsDecider根据lane borrow决策器的输出、本车道以及相邻车道的宽度、障碍物的左右边界，来计算path 的boundary，从而将path 搜索的边界缩小，将复杂问题转化为凸空间的搜索问题，方便后续使用QP算法求解。**

一、概述

PathBoundsDecider 是lanefollow 场景下，所调用的第 4 个 task，它的作用主要是：

- 根据借道信息、道路宽度生成FallbackPathBound
- 根据借道信息、道路宽度生成、障碍物边界生成PullOverPathBound、LaneChangePathBound、RegularPathBound中的一种

二、PathBoundsDecider的具体逻辑如下：

1、PublicRoadPlanner 的 LaneFollowStage 配置了以下几个task 来实现具体的规划逻辑，PathBoundsDecider 是第四个task，PathBoundsDecider根据lane borrow决策器的输出、本车道以及相邻车道的宽度、障碍物的左右边界，来计算path 的boundary，从而将path 搜索的边界缩小，将复杂问题转化为凸空间的搜索问题，方便后续使用QP算法求解；

PathBoundsDecider生成的每个bound，后续都会生成对应的轨迹

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

PathBoundsDecider被调用流程与其他task类似：

![img](https://pic4.zhimg.com/80/v2-9390b3414f1018ce2ce73ff87feb2ebb_720w.jpg)

2、PathBoundsDecider计算path上可行使区域边界，其输出是对纵向s等间距采样，横向s对应的左右边界，以此来作为下一步路径搜索的边界约束；与其他task一样，PathBoundsDecider的主要逻辑在Process() 方法中。

在Process() 方法中分四种情况对pathBound进行计算，按照处理顺序分别是fallback、pullover、lanechange、regular，不同的boundary对应不同的应用场景，其中fallback对应的path bound一定会生成，其余3个只有一个被激活，即按照顺序一旦有有效的boundary生成，就结束该task。

Process() 方法的代码及详细备注如下，首先看下整体代码，下文会对其中的模块做详细介绍：

```cpp
Status PathBoundsDecider::Process(
    Frame* const frame, ReferenceLineInfo* const reference_line_info) {
  // Sanity checks.
  CHECK_NOTNULL(frame);
  CHECK_NOTNULL(reference_line_info);
 
  // 如果道路重用置位，则跳过PathBoundsDecider
  // Skip the path boundary decision if reusing the path.
  if (FLAGS_enable_skip_path_tasks && reference_line_info->path_reusable()) {
    return Status::OK();
  }
 
  std::vector<PathBoundary> candidate_path_boundaries;
  // const TaskConfig& config = Decider::config_;
 
  // Initialization.
  // 用规划起始点自车道的lane width去初始化 path boundary
  // 如果无法获取规划起始点的道路宽度，则用默认值目前是5m，来初始化
  InitPathBoundsDecider(*frame, *reference_line_info);
 
  // Generate the fallback path boundary.
  // 生成fallback_path_bound，无论何种场景都会生成fallback_path_bound
  // 生成fallback_path_bound时，会考虑借道场景，向哪个方向借道，对应方向的path_bound会叠加这个方向相邻车道宽度
  PathBound fallback_path_bound;
  Status ret =
      // 生成fallback_path_bound，
      // 首先计算当前位置到本车道两侧车道线的距离；
      // 然后判断是否借道，并根据借道方向叠加相邻车道的车道宽度
      // 带有adc的变量表示与自车相关的信息
      GenerateFallbackPathBound(*reference_line_info, &fallback_path_bound);
  if (!ret.ok()) {
    ADEBUG << "Cannot generate a fallback path bound.";
    return Status(ErrorCode::PLANNING_ERROR, ret.error_message());
  }
  if (fallback_path_bound.empty()) {
    const std::string msg = "Failed to get a valid fallback path boundary";
    AERROR << msg;
    return Status(ErrorCode::PLANNING_ERROR, msg);
  }
  if (!fallback_path_bound.empty()) {
    CHECK_LE(adc_frenet_l_, std::get<2>(fallback_path_bound[0]));
    CHECK_GE(adc_frenet_l_, std::get<1>(fallback_path_bound[0]));
  }
  // Update the fallback path boundary into the reference_line_info.
  // 将fallback_path_bound存入到candidate_path_boundaries
  std::vector<std::pair<double, double>> fallback_path_bound_pair;
  for (size_t i = 0; i < fallback_path_bound.size(); ++i) {
    fallback_path_bound_pair.emplace_back(std::get<1>(fallback_path_bound[i]),
                                          std::get<2>(fallback_path_bound[i]));
  }
  candidate_path_boundaries.emplace_back(std::get<0>(fallback_path_bound[0]),
                                         kPathBoundsDeciderResolution,
                                         fallback_path_bound_pair);
  candidate_path_boundaries.back().set_label("fallback");
 
  // 根据场景生成另外一条path_bound
  // 依次判断是否处于pull_over、lane_change、regular；当处于其中一个场景，计算对应的path_bound并返回结果；
  // 即以上3种场景，只会选一种生成对应的根据场景生成另外一条path_bound
 
  // 判断是否为pull_over_status
  // If pull-over is requested, generate pull-over path boundary.
  auto* pull_over_status = injector_->planning_context()
                               ->mutable_planning_status()
                               ->mutable_pull_over();
  const bool plan_pull_over_path = pull_over_status->plan_pull_over_path();
  if (plan_pull_over_path) {
    PathBound pull_over_path_bound;
    Status ret = GeneratePullOverPathBound(*frame, *reference_line_info,
                                           &pull_over_path_bound);
    if (!ret.ok()) {
      AWARN << "Cannot generate a pullover path bound, do regular planning.";
    } else {
      ACHECK(!pull_over_path_bound.empty());
      CHECK_LE(adc_frenet_l_, std::get<2>(pull_over_path_bound[0]));
      CHECK_GE(adc_frenet_l_, std::get<1>(pull_over_path_bound[0]));
 
      // Update the fallback path boundary into the reference_line_info.
      std::vector<std::pair<double, double>> pull_over_path_bound_pair;
      for (size_t i = 0; i < pull_over_path_bound.size(); ++i) {
        pull_over_path_bound_pair.emplace_back(
            std::get<1>(pull_over_path_bound[i]),
            std::get<2>(pull_over_path_bound[i]));
      }
      candidate_path_boundaries.emplace_back(
          std::get<0>(pull_over_path_bound[0]), kPathBoundsDeciderResolution,
          pull_over_path_bound_pair);
      candidate_path_boundaries.back().set_label("regular/pullover");
 
      reference_line_info->SetCandidatePathBoundaries(
          std::move(candidate_path_boundaries));
      ADEBUG << "Completed pullover and fallback path boundaries generation.";
 
      // set debug info in planning_data
      auto* pull_over_debug = reference_line_info->mutable_debug()
                                  ->mutable_planning_data()
                                  ->mutable_pull_over();
      pull_over_debug->mutable_position()->CopyFrom(
          pull_over_status->position());
      pull_over_debug->set_theta(pull_over_status->theta());
      pull_over_debug->set_length_front(pull_over_status->length_front());
      pull_over_debug->set_length_back(pull_over_status->length_back());
      pull_over_debug->set_width_left(pull_over_status->width_left());
      pull_over_debug->set_width_right(pull_over_status->width_right());
 
      return Status::OK();
    }
  }
 
  // 判断是否满足lane_change场景
  // 首先FLAGS_enable_smarter_lane_change 要置位
  // 通过判断当前reference_line 是否为目标车道，来进行判断
  // If it's a lane-change reference-line, generate lane-change path boundary.
  if (FLAGS_enable_smarter_lane_change &&
      reference_line_info->IsChangeLanePath()) {
    PathBound lanechange_path_bound;
    // 当前reference_line 如果是目标path，则计算lanechange_path_bound
    // 首先根据借道与否，用道路宽度来生成基本的path bound
    // 然后遍历path上的每个点，并且判断这个点上的障碍物，利用障碍物的边界来修正path bound
    // 如果目标在左边将left_bound 设置为目标右边界
    // 如果目标在右边，将right_bound设置为目标的左边界
    Status ret = GenerateLaneChangePathBound(*reference_line_info,
                                             &lanechange_path_bound);
    if (!ret.ok()) {
      ADEBUG << "Cannot generate a lane-change path bound.";
      return Status(ErrorCode::PLANNING_ERROR, ret.error_message());
    }
    if (lanechange_path_bound.empty()) {
      const std::string msg = "Failed to get a valid fallback path boundary";
      AERROR << msg;
      return Status(ErrorCode::PLANNING_ERROR, msg);
    }
 
    // disable this change when not extending lane bounds to include adc
    if (config_.path_bounds_decider_config()
            .is_extend_lane_bounds_to_include_adc()) {
      CHECK_LE(adc_frenet_l_, std::get<2>(lanechange_path_bound[0]));
      CHECK_GE(adc_frenet_l_, std::get<1>(lanechange_path_bound[0]));
    }
    // Update the fallback path boundary into the reference_line_info.
    std::vector<std::pair<double, double>> lanechange_path_bound_pair;
    for (size_t i = 0; i < lanechange_path_bound.size(); ++i) {
      lanechange_path_bound_pair.emplace_back(
          std::get<1>(lanechange_path_bound[i]),
          std::get<2>(lanechange_path_bound[i]));
    }
    candidate_path_boundaries.emplace_back(
        std::get<0>(lanechange_path_bound[0]), kPathBoundsDeciderResolution,
        lanechange_path_bound_pair);
    candidate_path_boundaries.back().set_label("regular/lanechange");
    RecordDebugInfo(lanechange_path_bound, "", reference_line_info);
    reference_line_info->SetCandidatePathBoundaries(
        std::move(candidate_path_boundaries));
    ADEBUG << "Completed lanechange and fallback path boundaries generation.";
    return Status::OK();
  }
 
  // Generate regular path boundaries.
  // 生成regular path boundaries
  std::vector<LaneBorrowInfo> lane_borrow_info_list;
  lane_borrow_info_list.push_back(LaneBorrowInfo::NO_BORROW);
 
  // 判断是否进行借道，以及借道的方向
  if (reference_line_info->is_path_lane_borrow()) {
    const auto& path_decider_status =
        injector_->planning_context()->planning_status().path_decider();
    for (const auto& lane_borrow_direction :
         path_decider_status.decided_side_pass_direction()) {
      if (lane_borrow_direction == PathDeciderStatus::LEFT_BORROW) {
        lane_borrow_info_list.push_back(LaneBorrowInfo::LEFT_BORROW);
      } else if (lane_borrow_direction == PathDeciderStatus::RIGHT_BORROW) {
        lane_borrow_info_list.push_back(LaneBorrowInfo::RIGHT_BORROW);
      }
    }
  }
 
  // Try every possible lane-borrow option:
  // PathBound regular_self_path_bound;
  // bool exist_self_path_bound = false;
  for (const auto& lane_borrow_info : lane_borrow_info_list) {
    PathBound regular_path_bound;
    std::string blocking_obstacle_id = "";
    std::string borrow_lane_type = "";
    // 根据借道信息，以及path上障碍物来生成path bound
    // 首先根据借道与否，用道路宽度来生成基本的path bound
    // 然后遍历path上的每个点，并且判断这个点上的障碍物，利用障碍物的边界来修正path bound
    // 如果目标在左边将left_bound 设置为目标右边界
    // 如果目标在右边，将right_bound设置为目标的左边界
    Status ret = GenerateRegularPathBound(
        *reference_line_info, lane_borrow_info, &regular_path_bound,
        &blocking_obstacle_id, &borrow_lane_type);
    if (!ret.ok()) {
      continue;
    }
    if (regular_path_bound.empty()) {
      continue;
    }
    // disable this change when not extending lane bounds to include adc
    if (config_.path_bounds_decider_config()
            .is_extend_lane_bounds_to_include_adc()) {
      CHECK_LE(adc_frenet_l_, std::get<2>(regular_path_bound[0]));
      CHECK_GE(adc_frenet_l_, std::get<1>(regular_path_bound[0]));
    }
 
    // Update the path boundary into the reference_line_info.
    std::vector<std::pair<double, double>> regular_path_bound_pair;
    for (size_t i = 0; i < regular_path_bound.size(); ++i) {
      regular_path_bound_pair.emplace_back(std::get<1>(regular_path_bound[i]),
                                           std::get<2>(regular_path_bound[i]));
    }
    candidate_path_boundaries.emplace_back(std::get<0>(regular_path_bound[0]),
                                           kPathBoundsDeciderResolution,
                                           regular_path_bound_pair);
    std::string path_label = "";
    switch (lane_borrow_info) {
      case LaneBorrowInfo::LEFT_BORROW:
        path_label = "left";
        break;
      case LaneBorrowInfo::RIGHT_BORROW:
        path_label = "right";
        break;
      default:
        path_label = "self";
        // exist_self_path_bound = true;
        // regular_self_path_bound = regular_path_bound;
        break;
    }
    // RecordDebugInfo(regular_path_bound, "", reference_line_info);
    candidate_path_boundaries.back().set_label(
        absl::StrCat("regular/", path_label, "/", borrow_lane_type));
    candidate_path_boundaries.back().set_blocking_obstacle_id(
        blocking_obstacle_id);
  }
 
  // Remove redundant boundaries.
  // RemoveRedundantPathBoundaries(&candidate_path_boundaries);
 
  // Success
  reference_line_info->SetCandidatePathBoundaries(
      std::move(candidate_path_boundaries));
  ADEBUG << "Completed regular and fallback path boundaries generation.";
  return Status::OK();
}
```

3、关于ADC_bound 与lane_bound的计算

![img](https://pic2.zhimg.com/80/v2-6cfb00703dda56cc3b44e51bb3d9e331_720w.jpg)

（1）根据车道生成左右的lane_bound，从地图中获得；根据自车状态生成adc_bound，

adc_bound = adc_l_to_lane_center_ + ADC_speed_buffer + adc_half_width + ADC_buffer

上式中的各项：

adc_l_to_lane_center_ - 自车

adc_half_width - 车宽

adc_buffer - 0.5

ADCSpeedBuffer表示横向的瞬时位移， 公式如下：

![img](https://pic1.zhimg.com/80/v2-6effabee9fa2c90900808de75def380c_720w.jpg)

其中kMaxLatAcc = 1.5

（2）根据ADC_bound 与lane_bound 生成基本的bound

左侧当前s对应的bound取MAX(left_lane_bound,left_adc_bound), 即取最左边的

右侧当前s对应的bound取MIN(right_lane_bound,right_adc_bound)，即取最右边的

4、InitPathBoundsDecider()

在Process()过程中，首先会初始化bounds，用规划起始点自车道的lane width去初始化 path boundary，如果无法获取规划起始点的道路宽度，则用默认值目前是5m，来初始化。

path_bounds由一系列采样点组成，数据结构如下，这组数据里共有0～199个200个采样点，每两个点的间隔是0.5m；每个点由3个变量组成，分别是根据参考线建立的s-l坐标系的s坐标，右边界的l取值，左边界的s取值：

![img](https://pic3.zhimg.com/80/v2-513ef7414c7fdf0f5f6fda0456d445ba_720w.jpg)



5、GenerateFallbackPathBound()

无论当前处于何种场景，都会调用GenerateFallbackPathBound() 来生成备用的path bound，在计算FallbackPathBound时不考虑障碍物边界，仅使用道路边界，并考虑借道信息来进行计算。

```cpp
// Generate the fallback path boundary.
  // 生成fallback_path_bound，无论何种场景都会生成fallback_path_bound
  // 生成fallback_path_bound时，会考虑借道场景，向哪个方向借道，对应方向的path_bound会叠加这个方向相邻车道宽度
  PathBound fallback_path_bound;
  Status ret =
      // 生成fallback_path_bound，
      // 首先计算当前位置到本车道两侧车道线的距离；
      // 然后判断是否借道，并根据借道方向叠加相邻车道的车道宽度
      // 带有adc的变量表示与自车相关的信息
      GenerateFallbackPathBound(*reference_line_info, &fallback_path_bound);
  if (!ret.ok()) {
    ADEBUG << "Cannot generate a fallback path bound.";
    return Status(ErrorCode::PLANNING_ERROR, ret.error_message());
  }
  if (fallback_path_bound.empty()) {
    const std::string msg = "Failed to get a valid fallback path boundary";
    AERROR << msg;
    return Status(ErrorCode::PLANNING_ERROR, msg);
  }
  if (!fallback_path_bound.empty()) {
    CHECK_LE(adc_frenet_l_, std::get<2>(fallback_path_bound[0]));
    CHECK_GE(adc_frenet_l_, std::get<1>(fallback_path_bound[0]));
  }
  // Update the fallback path boundary into the reference_line_info.
  // 将fallback_path_bound存入到candidate_path_boundaries
  std::vector<std::pair<double, double>> fallback_path_bound_pair;
  for (size_t i = 0; i < fallback_path_bound.size(); ++i) {
    fallback_path_bound_pair.emplace_back(std::get<1>(fallback_path_bound[i]),
                                          std::get<2>(fallback_path_bound[i]));
  }
  candidate_path_boundaries.emplace_back(std::get<0>(fallback_path_bound[0]),
                                         kPathBoundsDeciderResolution,
                                         fallback_path_bound_pair);
  candidate_path_boundaries.back().set_label("fallback");
```

6、障碍物边界生成

考虑如下场景：

![img](https://pic1.zhimg.com/80/v2-5aa223124321dc020704283be0f4ee48_720w.jpg)

（1）首先筛选障碍物，障碍物筛选规则如下，需要符合以下所有的条件，才加到obs_list中：

- 不是虚拟障碍物
- 不是可忽略的障碍物（其他decider中添加的ignore decision）
- 静态障碍物或速度小于FLAGS_static_obstacle_speed_threshold（0.5m/s）
- 在自车的前方

（2）将每个障碍物分解成两个ObstacleEdge，起点一个终点一个，记录下s，start_l,end_l,最后按s排序

![img](https://pic1.zhimg.com/80/v2-99bccb07fd1960c9522699845ee9d5cc_720w.jpg)

对于上图场景中id为2的obstacle，sort后得到的内部数据结构为：

![img](https://pic3.zhimg.com/80/v2-dd91d8dad93b41a183c1f0c2037e2cda_720w.jpg)

7、根据场景生成另外一条path_bound

依次判断是否处于pull_over、lane_change、regular；当处于其中一个场景，计算对应的path_bound并返回结果；即以上3种场景，只会选一种生成对应的根据场景生成另外一条path_bound。

这3种path bound均考虑了障碍物的边界，用障碍物的边界来修正path bound：

- 首先根据借道与否，用道路宽度来生成基本的path bound
- 然后遍历path上的每个点，并且判断这个点上的障碍物，利用障碍物的边界来修正path bound
- 如果目标在左边将left_bound 设置为目标右边界
- 如果目标在右边，将right_bound设置为目标的左边界

下面以GenerateLaneChangePathBound 进行说明：

```cpp
Status PathBoundsDecider::GenerateLaneChangePathBound(
    const ReferenceLineInfo& reference_line_info,
    std::vector<std::tuple<double, double, double>>* const path_bound) {
  // 1. Initialize the path boundaries to be an indefinitely large area.
  // 1、用numeric 的最大最小值初始化path bound
  if (!InitPathBoundary(reference_line_info, path_bound)) {
    const std::string msg = "Failed to initialize path boundaries.";
    AERROR << msg;
    return Status(ErrorCode::PLANNING_ERROR, msg);
  }
  // PathBoundsDebugString(*path_bound);
 
  // 2、用当前车道宽度计算path bound，同时考虑lane borrow，如果借道将目标车道的宽度叠加
  // 2. Decide a rough boundary based on lane info and ADC's position
  std::string dummy_borrow_lane_type;
  if (!GetBoundaryFromLanesAndADC(reference_line_info,
                                  LaneBorrowInfo::NO_BORROW, 0.1, path_bound,
                                  &dummy_borrow_lane_type, true)) {
    const std::string msg =
        "Failed to decide a rough boundary based on "
        "road information.";
    AERROR << msg;
    return Status(ErrorCode::PLANNING_ERROR, msg);
  }
  // PathBoundsDebugString(*path_bound);
 
  // 3、在换道结束前，用ADC坐标到目标道路边界的距离，修正path bound
  // 3. Remove the S-length of target lane out of the path-bound.
  GetBoundaryFromLaneChangeForbiddenZone(reference_line_info, path_bound);
 
  PathBound temp_path_bound = *path_bound;
  std::string blocking_obstacle_id;
  // 根据path上的障碍物修正path_bound，遍历path上每个点，并在每个点上遍历障碍物；
  // 如果目标在左边将left_bound 设置为目标右边界
  // 如果目标在右边，将right_bound设置为目标的左边界
  if (!GetBoundaryFromStaticObstacles(reference_line_info.path_decision(),
                                      path_bound, &blocking_obstacle_id)) {
    const std::string msg =
        "Failed to decide fine tune the boundaries after "
        "taking into consideration all static obstacles.";
    AERROR << msg;
    return Status(ErrorCode::PLANNING_ERROR, msg);
  }
  // Append some extra path bound points to avoid zero-length path data.
  int counter = 0;
  while (!blocking_obstacle_id.empty() &&
         path_bound->size() < temp_path_bound.size() &&
         counter < kNumExtraTailBoundPoint) {
    path_bound->push_back(temp_path_bound[path_bound->size()]);
    counter++;
  }
 
  ADEBUG << "Completed generating path boundaries.";
  return Status::OK();
}
```