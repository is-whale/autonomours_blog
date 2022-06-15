- [Apollo 感知模块（一）：Radar Perception - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/415416588)

## Radar Perception 简介

雷达感知（Radar Perception）的主要内容，是整个感知模块中较为简单的部分，也是最好入手的部分，代码逻辑清晰简洁。其中包含了整个感知体系里最经典的处理逻辑，也就是主要的pipline，preprocess -> detect -> track。

文章的后续部分我们将从每个模块递进，看看雷达感知究竟做了点什么。

首先，我们从代码的目录结构开始进行阅读

## 1.代码目录结构

![img](https://pic2.zhimg.com/80/v2-8ed35f04927f1ccde110b92e4f5e6875_720w.jpg)图1 目录结构


app中主要存放了Radar检测的主逻辑，common里存放的是一些工具，lib里存了主逻辑下各子模块的实现。

## 2.入口及查看

该模块的启动是通过融合模块的dag文件而启动的，在**Apollo/modules/perception/production/launch**并没有单独启动radar的launch文件或者单独启动的dag文件。其具体路径为：**Apollo/modules/perception/production/dag/dag_streaming_perception.dag**

可以看到启动的两个分别是前雷达的detect和后雷达的detect，使用的是同一个雷达检测入口RadarDetectionComponent.cc, 唯独的区别是读取的参数文件不同

![img](https://pic3.zhimg.com/80/v2-820d8b90e4ebdaf95dde86546a86a492_720w.jpg)图2 启动配置文件

参数文件对比：

![img](https://pic2.zhimg.com/80/v2-e04fd98856d404d8262ff779664ed589_720w.jpg)图3 雷达参数文件

可以看到主要的区别就是前雷达推理距离是200米，后雷达是120米，处理的pipeline也分为了前、后两种，但是具体查看其配置文件发现是一模一样的。

![img](https://pic3.zhimg.com/80/v2-dbf61391cf7a9cf0340491eae1ddad26_720w.jpg)图4 pipeline配置文件

### 2.1 RadarDetectionComponent

在融合感知模块，可以找到RadarDetectionComponent，我们现在便进入这一模块进行查看：

- ***RadarDetectionComponent::Init()***

初始化参数，参数列表见图3

- ***RadarDetectionComponent::InternalProc()***

**输入**：从ContiRadar（大陆雷达）获取的原始radar信息

**输出**：SensorFrameMessage ，即为最终的radar感知结果

其核心流程主要分为两步， 雷达数据前处理流程Preprocess，及后续的感知流程Perceive

```cpp
bool RadarDetectionComponent::InternalProc( 
 const std::shared_ptr<ContiRadar>& in_message, 
 std::shared_ptr<SensorFrameMessage> out_message) { 
  pipeline(radar_info_.name); 
  ContiRadar raw_obstacles = *in_message; 
  { 
    std::unique_lock<std::mutex> lock(_mutex); 
    ++seq_num_; 
  } 
  double timestamp = in_message->header().timestamp_sec(); 
  const double cur_time = Clock::NowInSeconds(); 
  const double start_latency = (cur_time - timestamp) * 1e3; 
  AINFO << "FRAME_STATISTICS:Radar:Start:msg_time[" << timestamp 
        << "]:cur_time[" << cur_time << "]:cur_latency[" << start_latency 
        << "]"; 
  PERF_BLOCK_START(); 
  radar::PreprocessorOptions preprocessor_options; 
  ContiRadar corrected_obstacles; 
  //预处理的具体流程 
  radar_preprocessor_->Preprocess(raw_obstacles, preprocessor_options, 
                                  &corrected_obstacles); 
  PERF_BLOCK_END_WITH_INDICATOR(radar_info_.name, "radar_preprocessor"); 
  timestamp = corrected_obstacles.header().timestamp_sec(); 
 
  out_message->timestamp_ = timestamp; 
  out_message->seq_num_ = seq_num_; 
  out_message->process_stage_ = ProcessStage::LONG_RANGE_RADAR_DETECTION; 
  out_message->sensor_id_ = radar_info_.name; 
 
 
  //初始化一系列的参数，如radar2world转换矩阵，rader2egovechile转换矩阵，自车线速度，角速度 
  radar::RadarPerceptionOptions options; 
  options.sensor_name = radar_info_.name; 
 
  Eigen::Affine3d radar_trans; 
  //radar2world转换矩阵 
  if (!radar2world_trans_.GetSensor2worldTrans(timestamp, &radar_trans)) { 
    out_message->error_code_ = apollo::common::ErrorCode::PERCEPTION_ERROR_TF; 
    AERROR << "Failed to get pose at time: " << timestamp; 
    return true; 
  } 
  Eigen::Affine3d radar2novatel_trans; 
  //radar2egovechile (自车）转换矩阵 
  if (!radar2novatel_trans_.GetTrans(timestamp, &radar2novatel_trans, "novatel", 
                                     tf_child_frame_id_)) { 
    out_message->error_code_ = apollo::common::ErrorCode::PERCEPTION_ERROR_TF; 
    AERROR << "Failed to get radar2novatel trans at time: " << timestamp; 
    return true; 
  } 
  PERF_BLOCK_END_WITH_INDICATOR(radar_info_.name, "GetSensor2worldTrans"); 
  Eigen::Matrix4d radar2world_pose = radar_trans.matrix(); 
  options.detector_options.radar2world_pose = &radar2world_pose; 
  Eigen::Matrix4d radar2novatel_trans_m = radar2novatel_trans.matrix(); 
  options.detector_options.radar2novatel_trans = &radar2novatel_trans_m; 
  //获取自车车速 
  if (!GetCarLocalizationSpeed(timestamp, 
                               &(options.detector_options.car_linear_speed), 
                               &(options.detector_options.car_angular_speed))) { 
    AERROR << "Failed to call get_car_speed. [timestamp: " << timestamp; 
    // return false; 
  } 
  PERF_BLOCK_END_WITH_INDICATOR(radar_info_.name, "GetCarSpeed"); 
 
  //Radar2world的矩阵偏移 
  base::PointD position; 
  position.x = radar_trans(0, 3); 
  position.y = radar_trans(1, 3); 
  position.z = radar_trans(2, 3); 
  options.roi_filter_options.roi.reset(new base::HdmapStruct()); 
  if (FLAGS_obs_enable_hdmap_input) { 
    hdmap_input_->GetRoiHDMapStruct(position, radar_forward_distance_, 
                                    options.roi_filter_options.roi); 
  } 
  PERF_BLOCK_END_WITH_INDICATOR(radar_info_.name, "GetRoiHDMapStruct"); 
 
  // 感知主要流程 
  std::vector<base::ObjectPtr> radar_objects; 
  if (!radar_perception_->Perceive(corrected_obstacles, options, 
                                   &radar_objects)) { 
    out_message->error_code_ = 
        apollo::common::ErrorCode::PERCEPTION_ERROR_PROCESS; 
    AERROR << "RadarDetector Proc failed."; 
    return true; 
  } 
  out_message->frame_.reset(new base::Frame()); 
  out_message->frame_->sensor_info = radar_info_; 
  out_message->frame_->timestamp = timestamp; 
  out_message->frame_->sensor2world_pose = radar_trans; 
  out_message->frame_->objects = radar_objects; 
 
  const double end_timestamp = Clock::NowInSeconds(); 
  const double end_latency = 
      (end_timestamp - in_message->header().timestamp_sec()) * 1e3; 
  AINFO << "FRAME_STATISTICS:Radar:End:msg_time[" 
        << in_message->header().timestamp_sec() << "]:cur_time[" 
        << end_timestamp << "]:cur_latency[" << end_latency << "]"; 
  PERF_BLOCK_END_WITH_INDICATOR(radar_info_.name, "radar_perception"); 
 
  return true; 
} 
```

## 3.预处理

**Apollo/modules/perception/radar/lib/preprocessor/conti_ars_preprocessor/[http://conti_ars_preprocessor.cc](https://link.zhihu.com/?target=http%3A//conti_ars_preprocessor.cc)**

- **ContiArsPreprocessor::Preprocess()**

雷达预处理主要包括了以下三个主要步骤：

1. 跳过不在时间范围内的目标
2. 重新分配ID
3. 修正时间戳

```cpp
bool ContiArsPreprocessor::Preprocess( 
    const drivers::ContiRadar& raw_obstacles, 
    const PreprocessorOptions& options, 
    drivers::ContiRadar* corrected_obstacles) { 
  PERF_FUNCTION(); 
  SkipObjects(raw_obstacles, corrected_obstacles); 
  ExpandIds(corrected_obstacles); 
  CorrectTime(corrected_obstacles); 
  return true; 
} 
```

## 4. 感知主流程

**Apollo/modules/perception/radar/app/[http://radar_obstacle_perception.cc](https://link.zhihu.com/?target=http%3A//radar_obstacle_perception.cc)**

- RadarObstaclePerception::Perceive()

接下来我们便进入了Radar Perception的核心处理逻辑，其包含了 Detect -> ROI Filter -> Track 这三个核心部分

```cpp
bool RadarObstaclePerception::Perceive( 
    const drivers::ContiRadar& corrected_obstacles, 
    const RadarPerceptionOptions& options, 
    std::vector<base::ObjectPtr>* objects) { 
  PERF_FUNCTION(); 
  const std::string& sensor_name = options.sensor_name; 
  PERF_BLOCK_START(); 
  base::FramePtr detect_frame_ptr(new base::Frame()); 
 
  if (!detector_->Detect(corrected_obstacles, options.detector_options, 
                         detect_frame_ptr)) { 
    AERROR << "radar detect error"; 
    return false; 
  } 
  ADEBUG << "Detected frame objects number: " 
         << detect_frame_ptr->objects.size(); 
  PERF_BLOCK_END_WITH_INDICATOR(sensor_name, "detector"); 
  if (!roi_filter_->RoiFilter(options.roi_filter_options, detect_frame_ptr)) { 
    ADEBUG << "All radar objects were filtered out"; 
  } 
  ADEBUG << "RoiFiltered frame objects number: " 
         << detect_frame_ptr->objects.size(); 
  PERF_BLOCK_END_WITH_INDICATOR(sensor_name, "roi_filter"); 
 
  base::FramePtr tracker_frame_ptr(new base::Frame); 
  if (!tracker_->Track(*detect_frame_ptr, options.track_options, 
                       tracker_frame_ptr)) { 
    AERROR << "radar track error"; 
    return false; 
  } 
  ADEBUG << "tracked frame objects number: " 
         << tracker_frame_ptr->objects.size(); 
  PERF_BLOCK_END_WITH_INDICATOR(sensor_name, "tracker"); 
 
  *objects = tracker_frame_ptr->objects; 
 
  return true; 
} 
```

### 4.1 Detect

**Apollo/modules/perception/radar/lib/detector/conti_ars_detector/[http://conti_ars_detector.cc](https://link.zhihu.com/?target=http%3A//conti_ars_detector.cc)**

在检测这个部分，Apollo直接使用了conti雷达的检测结果，输出了obstacle粒度的目标，这一部分的核心则在于conti雷达数据的一些后处理，其中包括了

- radar2world和radar2egovechile的坐标系转换，之前已在外层函数***RadarDetectionComponent::InternalProc()\***中实现，这里直接拿来用，具体可查看2.1代码注释
- 相对速度和绝对速度的转换
- 运动状态、类别等属性的赋值，从conti的输出格式转化为apollo的通用障碍物格式

### **4.2 RoiFilter**

**Apollo/modules/perception/radar/lib/roi_filter/hdmap_radar_roi_filter/[http://hdmap_radar_roi_filter.cc](https://link.zhihu.com/?target=http%3A//hdmap_radar_roi_filter.cc)**

这里就像autoware的weighted-map一样，通过引入高精地图模块，帮助radar区分哪里是道路，哪里是非道路，因此可以过滤掉那些不感兴趣的目标，如人行道上的物体。

```cpp
bool HdmapRadarRoiFilter::RoiFilter(const RoiFilterOptions& options, 
                                    base::FramePtr radar_frame) { 
  std::vector<base::ObjectPtr> origin_objects = radar_frame->objects; 
  return common::ObjectInRoiCheck(options.roi, origin_objects, 
                                  &radar_frame->objects); 
} 
 
```

### **4.3 Track**

**Apollo/modules/perception/radar/lib/tracker/conti_ars_tracker/[http://conti_ars_tracker.cc](https://link.zhihu.com/?target=http%3A//conti_ars_tracker.cc)**

跟踪采用了apollo经典的关联，匹配，更新的三个过程。我们接下来主要讲一下这个部分，可以帮助理解更加复杂的Lidar Track 和 Fusion Track。

**ContiArsTracker::TrackObjects()**

```cpp
bool ContiArsTracker::Track(const base::Frame &detected_frame, 
                            const TrackerOptions &options, 
                            base::FramePtr tracked_frame) { 
  //进入Track核心处理逻辑 
  TrackObjects(detected_frame); 
  //再对track输出的结果做一层重组和封装 
  CollectTrackedFrame(tracked_frame); 
  return true; 
} 
 
void ContiArsTracker::TrackObjects(const base::Frame &radar_frame) { 
  std::vector<TrackObjectPair> assignments; 
  std::vector<size_t> unassigned_tracks; 
  std::vector<size_t> unassigned_objects; 
  TrackObjectMatcherOptions matcher_options; 
  const auto &radar_tracks = track_manager_->GetTracks(); 
  //匹配 
  matcher_->Match(radar_tracks, radar_frame, matcher_options, &assignments, 
                  &unassigned_tracks, &unassigned_objects); 
  //更新已经匹配的track 
  UpdateAssignedTracks(radar_frame, assignments); 
  UpdateUnassignedTracks(radar_frame, unassigned_tracks); 
  //删除尚未匹配的track 
  DeleteLostTracks(); 
  //创建新的track 
  CreateNewTracks(radar_frame, unassigned_objects); 
} 
```

### **4.3.1 CreateNewTracks**

我们需要首先理解的是，从percieve模块开始的时候，就是一帧一帧的Radar数据送入的，所以此时我们拿到的也是一帧雷达数据（经过很多重新封装的过程之后的）。

第一帧雷达数据进入后，由于此时还没有track，所以我们首先跳过了前三步，进入了新建track的这个函数。其代码逻辑就是将尚未被分配到track的物体，依次新建一个track。track_manager_的addtrack方法涉及到了如何把物体信息转换到track信息中

```cpp
void ContiArsTracker::CreateNewTracks( 
    const base::Frame &radar_frame, 
    const std::vector<size_t> &unassigned_objects) { 
  for (size_t i = 0; i < unassigned_objects.size(); ++i) { 
    RadarTrackPtr radar_track; 
    radar_track.reset(new RadarTrack(radar_frame.objects[unassigned_objects[i]], 
                                     radar_frame.timestamp)); 
    track_manager_->AddTrack(radar_track); 
  } 
} 
```

### **4.3.2 matcher_->Match**

假设第一帧数据提供了5个物体，也在上一步里新建了5个track，那么此时第一帧数据处理完毕，等待第二帧数据进来，也就进入了匹配的过程。匹配的过程分为两步：

```cpp
bool HMMatcher::Match(const std::vector<RadarTrackPtr> &radar_tracks, 
                      const base::Frame &radar_frame, 
                      const TrackObjectMatcherOptions &options, 
                      std::vector<TrackObjectPair> *assignments, 
                      std::vector<size_t> *unassigned_tracks, 
                      std::vector<size_t> *unassigned_objects) { 
 IDMatch(radar_tracks, radar_frame, assignments, unassigned_tracks, 
          unassigned_objects); 
 TrackObjectPropertyMatch(radar_tracks, radar_frame, assignments, 
                           unassigned_tracks, unassigned_objects); 
  return true; 
} 
```

第一步，IDMatch。先检查track中物体的id和此时这帧传感器数据的物体id是否一样，如果一样，那么该物体就与该track匹配上了，并进栈到assiment_track中（匹配过的track）。那这一步，其实是默认利用了conti雷达对物体的追踪，从而完成了ID匹配。

第二步，TrackObjectPropertyMatch。这一步本质上的目的其实是，在雷达自身匹配的同时，也利用一些雷达数据的性质，扩充一点额外的匹配。具体的匹配方法是，使用一个二维矩阵来计算radar物体和track物体的关联值。关联值是通过两者的距离来计算的，也很好理解，此时观测到离轨迹中保存物体最近的那个就属于这个轨迹。

radar通过对关联值的距离计算了两次，然后取了平均值。分别使用了上一帧和当前帧的速度做预测。

```cpp
double distance_forward = DistanceBetweenObs( 
          track_object, track_timestamp, frame_object, frame_timestamp); 
      double distance_backward = DistanceBetweenObs( 
          frame_object, frame_timestamp, track_object, track_timestamp); 
      association_mat->at(i).at(j) = 
          0.5 * distance_forward + 0.5 * distance_backward; 
 
```

在刚开始的时候我一直困惑匹配这个过程是在做什么，但现在理解下来，就是将实时的传感器数据放入对应的Track中，并用assignment作为保存，之后就可以对轨迹本身进行重新演算，得到轨迹的速度等性质; 至于unassigned_tracks 和 unassigned_obj,这些会有后续的处理逻辑来决定是否丢弃。

### **4.3.3 UpdateAssignedTracks && UpdateUNAssignedTracks**

这里的更新track，我们可以理解为上一步已经把最新一帧的传感器数据放入了它属于的某个物体轨迹中，这一步就要对轨迹一些性质进行有效更新。

进一步阅读代码我们可以发现，这里并没有使用卡尔曼滤波器，而是直接使用Radar的观测量做了更新。

对每一个匹配到的结果，把里面的观测量拿出来用于更新轨迹，里面涉及到的主要就是物体中心，物体速度，以及相应的不确定性。

```cpp
void RadarTrack::UpdataObsRadar(const base::ObjectPtr& obs_radar, 
                                const double timestamp) { 
  *obs_radar_ = *obs_radar; 
  *obs_ = *obs_radar; 
  double time_diff = timestamp - timestamp_; 
  if (s_use_filter_) { 
    Eigen::VectorXd state; 
    state = filter_->UpdateWithObject(*obs_radar_, time_diff); 
    obs_->center(0) = static_cast<float>(state(0)); 
    obs_->center(1) = static_cast<float>(state(1)); 
    obs_->velocity(0) = static_cast<float>(state(2)); 
    obs_->velocity(1) = static_cast<float>(state(3)); 
    Eigen::Matrix4d covariance_matrix = filter_->GetCovarianceMatrix(); 
    obs_->center_uncertainty(0) = static_cast<float>(covariance_matrix(0, 0)); 
    obs_->center_uncertainty(1) = static_cast<float>(covariance_matrix(1, 1)); 
    obs_->velocity_uncertainty(0) = static_cast<float>(covariance_matrix(2, 2)); 
    obs_->velocity_uncertainty(1) = static_cast<float>(covariance_matrix(3, 3)); 
  } 
  tracking_time_ += time_diff; 
  timestamp_ = timestamp; 
  ++tracked_times_; 
} 
 
```

对于unassignedTrack，如果数组中已经没有object，则直接设为dead；如果还有object，但是已经有一段时间没更新过了，也设为dead

```cpp
oid ContiArsTracker::UpdateUnassignedTracks( 
    const base::Frame &radar_frame, 
    const std::vector<size_t> &unassigned_tracks) { 
  double timestamp = radar_frame.timestamp; 
  auto &radar_tracks = track_manager_->mutable_tracks(); 
  for (size_t i = 0; i < unassigned_tracks.size(); ++i) { 
    if (radar_tracks[unassigned_tracks[i]]->GetObs() != nullptr) { 
      double radar_time = radar_tracks[unassigned_tracks[i]]->GetTimestamp(); 
      double time_diff = fabs(timestamp - radar_time); 
      if (time_diff > s_tracking_time_win_) { 
        radar_tracks[unassigned_tracks[i]]->SetDead(); 
      } 
    } else { 
      radar_tracks[unassigned_tracks[i]]->SetDead(); 
    } 
  } 
} 
```

### **4.3.4 DeleteLostTracks();**

删除不用的track，逻辑很简单，check所有的track，如果有dead的，则删掉。

```cpp
int RadarTrackManager::RemoveLostTracks() { 
  size_t track_count = 0; 
  for (size_t i = 0; i < tracks_.size(); ++i) { 
    if (!tracks_[i]->IsDead()) { 
      if (i != track_count) { 
        tracks_[track_count] = tracks_[i]; 
      } 
      ++track_count; 
    } 
  } 
  int removed_count = static_cast<int>(tracks_.size() - track_count); 
  ADEBUG << "Remove " << removed_count << " tracks"; 
  tracks_.resize(track_count); 
  return static_cast<int>(track_count); 
} 
 
```

## 总结

至此，Apollo感知模块的雷达感知（Radar Perception）部分已介绍完毕了。后续我们会陆续推出一系列Apollo定位、感知、预测、规划、控制及监控等几大模块的系列文章，先从感知模块开启，敬请期待~