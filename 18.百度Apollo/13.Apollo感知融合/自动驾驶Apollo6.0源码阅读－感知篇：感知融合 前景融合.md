- [自动驾驶Apollo6.0源码阅读－感知篇：感知融合 前景融合_洪山橋嬉皮的博客-CSDN博客_自动驾驶源码](https://blog.csdn.net/ControlLearner/article/details/122973738)

# ProbabilisticFusion::FuseForegroundTrack

![在这里插入图片描述](https://img-blog.csdnimg.cn/ff02c057903f4e2aa77e1a56fda48d2d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

## matcher_->Associate

[自动驾驶Apollo6.0源码阅读－感知篇：感知融合-数据关联](https://blog.csdn.net/ControlLearner/article/details/122973909)

## this->UpdateAssignedTracks

匹配上的结果做更新，使用探测更新tracker，tracker类是`pdf_tracker`

```cpp
void ProbabilisticFusion::UpdateAssignedTracks(
    const SensorFramePtr& frame,
    const std::vector<TrackMeasurmentPair>& assignments) {
  TrackerOptions options;
  options.match_distance = 0;
  for (size_t i = 0; i < assignments.size(); ++i) {
    size_t track_ind = assignments[i].first;
    size_t obj_ind = assignments[i].second;
    //pbf_tracker,观测更新tracker
    trackers_[track_ind]->UpdateWithMeasurement(
        options, frame->GetForegroundObjects()[obj_ind], frame->GetTimestamp());
  }
}
```

tracker更新的函数中会更新四个部分，existence、motion、[shape](https://so.csdn.net/so/search?q=shape&spm=1001.2101.3001.7020)、type和tracker的信息，前四个fusion的配置参数在`modules/perception/proto/pbf_tracker_config.proto`，就是init()中的默认值。

```cpp
// 观测更新tracker
void PbfTracker::UpdateWithMeasurement(const TrackerOptions& options,const SensorObjectPtr measurement,double target_timestamp) {
  std::string sensor_id = measurement->GetSensorId();
  ADEBUG << "fusion_updating..." << track_->GetTrackId() << " with " << sensor_id << "..." << measurement->GetBaseObject()->track_id << "@" << FORMAT_TIMESTAMP(measurement->GetTimestamp());
  // options.match_distance = 0
  // @liuxinyu: DstExitenceFusion 
  // 证据推理（DS theory）更新Tracker的存在性
  existence_fusion_->UpdateWithMeasurement(measurement,target_timestamp,options.match_distance);
  // NOTE(@liuxinyu):KalmanMotionFusion
  // 鲁棒卡尔曼滤波更新tracker的运动属性（非标准CA运动模型）
  motion_fusion_->UpdateWithMeasurement(measurement, target_timestamp);
  // NOTE(@liuxinyu): PbfShapeFusion
  // 更新tracker的形状
  shape_fusion_->UpdateWithMeasurement(measurement, target_timestamp);
   // @liuxinyu: DstTypeFusion
  // 证据推理（DS theory）更新Tracker的属性
  type_fusion_->UpdateWithMeasurement(measurement, target_timestamp);

  track_->UpdateWithSensorObject(measurement);
}
```

`shape_fusion_`：

```bash
// @liuxinyu: 
// 1.优先使用Lidar的形状，最近的历史关联的观测中若有lidar,radar和camera观测都不会更新
// 2.其次优先camera的形状，间隔更新时间比较小的话，radar观测仅仅更新中心，形状用历史camera更新
// 3.最后最近的历史观测中lidar和camera都没有的话，才使用radar的观测更新
```

主要是`DS theory`和更新`tracker`的属性。后续展开。

## this->UpdateUnassignedTracks

同上UpdateAssignedTracks一样，对未匹配探测的航迹tracker，更新同样的参数：

```cpp
// 没有匹配探测时更新tracker
void PbfTracker::UpdateWithoutMeasurement(const TrackerOptions& options,const std::string& sensor_id,double measurement_timestamp, double target_timestamp) {
  existence_fusion_->UpdateWithoutMeasurement(sensor_id,measurement_timestamp,                             target_timestamp,options.match_distance);
  motion_fusion_->UpdateWithoutMeasurement(sensor_id, measurement_timestamp,target_timestamp);
  shape_fusion_->UpdateWithoutMeasurement(sensor_id, measurement_timestamp,target_timestamp);
  type_fusion_->UpdateWithoutMeasurement(sensor_id, measurement_timestamp,target_timestamp,options.match_distance);
  track_->UpdateWithoutSensorObject(sensor_id, measurement_timestamp);
}
```

## this->CreateNewTracks

对没有匹配到tracker的探测object，新建航迹track，主要是最后的两个Init函数。可以详细看下Track和BaseTracker两个类。

```cpp
void ProbabilisticFusion::CreateNewTracks(
    const SensorFramePtr& frame,
    const std::vector<size_t>& unassigned_obj_inds) {
  for (size_t i = 0; i < unassigned_obj_inds.size(); ++i) {
    size_t obj_ind = unassigned_obj_inds[i];
    bool prohibition_sensor_flag = false;
    // @liuxinyu: 如果是prohibition_sensor，则跳过
    std::for_each(params_.prohibition_sensors.begin(),
                  params_.prohibition_sensors.end(),
                  [&](std::string sensor_name) {
                    if (sensor_name == frame->GetSensorId())
                      prohibition_sensor_flag = true;
                  });
    if (prohibition_sensor_flag) {
      continue;
    }
    // NOTE(@liuxinyu): 新建track,并初始化，添加到scenes_中
    TrackPtr track = TrackPool::Instance().Get();
    track->Initialize(frame->GetForegroundObjects()[obj_ind]);
    scenes_->AddForegroundTrack(track);

    // NOTE(@liuxinyu): pbfTracker：新建tracker，track初始化tracker，tracker插入到航迹集合trackers_中
    if (params_.tracker_method == "PbfTracker") {
      std::shared_ptr<BaseTracker> tracker;
      tracker.reset(new PbfTracker());
      tracker->Init(track, frame->GetForegroundObjects()[obj_ind]);
      trackers_.emplace_back(tracker);
    }
  }
}
```

# 参考资料

**Fusion**
[Apollo perception源码阅读 | fusion](https://blog.csdn.net/weixin_43152152/article/details/114836882?ops_request_misc=%7B%22request%5Fid%22%3A%22164491584816780269835017%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=164491584816780269835017&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-114836882.first_rank_v2_pc_rank_v29&utm_term=CollectFusedObjects&spm=1018.2226.3001.4187)
[自动驾驶 Apollo 源码分析系列，感知篇(八)：感知融合代码的基本流程](https://frank909.blog.csdn.net/article/details/118101596?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-4.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-4.pc_relevant_default&utm_relevant_index=8)
**关联匹配**
[Apollo perception源码阅读 | fusion](https://blog.csdn.net/weixin_43152152/article/details/115175675)

[Apollo 5.0源码学习笔记（三）| 感知模块 | 融合模块 | 数据关联](https://blog.csdn.net/zhanghm1995/article/details/104910923/?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-4&spm=1001.2101.3001.4242)
**匈牙利匹配算法**
[Apollo perception源码阅读 | fusion之匈牙利算法](https://blog.csdn.net/weixin_43152152/article/details/114235820)
**卡尔曼滤波**
[Apollo perception源码阅读 | fusion之kalman](https://blog.csdn.net/weixin_43152152/article/details/115308664)
**证据推理**
[Apollo perception源码阅读 | fusion之证据推理](https://blog.csdn.net/weixin_43152152/article/details/115648285)