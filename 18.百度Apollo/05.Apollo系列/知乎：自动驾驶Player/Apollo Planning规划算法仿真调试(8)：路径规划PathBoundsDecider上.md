- [Apollo Planning规划算法仿真调试(8)：路径规划PathBoundsDecider上 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/519439428)

**前言：**

PathBoundsDecider 是Apollo Planning规划算法的第四个task，PathBoundsDecider根据lane borrow决策器的输出、本车道以及相邻车道的宽度、障碍物的左右边界，来计算path 的boundary，从而将path 搜索的边界缩小，将复杂问题转化为凸空间的搜索问题，方便后续使用QP算法求解。它的作用主要是：

- 根据借道信息、道路宽度生成FallbackPathBound
- 根据借道信息、道路宽度生成、障碍物边界生成PullOverPathBound、LaneChangePathBound、RegularPathBound中的一种

PathBound的生成对于后续的路径规划，以及规划算法的整体表现，是沿着当前道路行驶还是借道超车，有着决定性的作用。所以本文将深入PathBound内部，以GenerateRegularPathBound为例，详细解析与调试这个task，从而帮助大家来深入了解这个task。

调试所使用场景如下：

![img](https://pic4.zhimg.com/80/v2-b512c2049c881fdf1c6628d4df5caf9f_720w.jpg)

1、首先判断当前场景是否在laneborrow状态：

```cpp
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
```

主要是根据 path_decider_status.decided_side_pass_direction() 读到的可以借道的方向更新lane_borrow_info_list，更新后如下：

![img](https://pic4.zhimg.com/80/v2-d17049b8c75eb8513185673802241e23_720w.png)

2、接下来便是对lane_borrow_info_list进行遍历，根据lane_borrow_info_list的数量与方向，生成对应的PathBound，生成的PathBound便是之后路径优化的边界

```cpp
for (const auto& lane_borrow_info : lane_borrow_info_list) {
    PathBound regular_path_bound;
    std::string blocking_obstacle_id = "";
    std::string borrow_lane_type = "";
 ......
 ......
 ......
 // RecordDebugInfo(regular_path_bound, "", reference_line_info);
    candidate_path_boundaries.back().set_label(
        absl::StrCat("regular/", path_label, "/", borrow_lane_type));
    candidate_path_boundaries.back().set_blocking_obstacle_id(
        blocking_obstacle_id);
 }
```

3、GenerateRegularPathBound()

GenerateRegularPathBound() 是生成路径边界的主要逻辑承载函数。它的主要逻辑如下：

- 1、用numeric 的最大最小值初始化path bound
- 2、用当前车道宽度计算path bound，同时考虑lane borrow，如果借道将目标车道的宽度叠加
- 3、在换道结束前，用ADC坐标到目标道路边界的距离，修正path bound
- 4、 根据path上的障碍物修正path_bound，遍历path上每个点，并在每个点上遍历障碍物；如果目标在左边将left_bound 设置为目标右边界； 如果目标在右边，将right_bound设置为目标的左边界

4、GetBoundaryFromLanesAndADC()

GetBoundaryFromLanesAndADC的作用是用当前车道宽度计算path bound，同时考虑lane borrow，如果借道将目标车道的宽度叠加。

计算方法之前做过分享：

![img](https://pic2.zhimg.com/80/v2-90d35202f0db0e3cf70965470933053d_720w.jpg)

（1）根据车道生成左右的lane_bound，从地图中获得；根据自车状态生成adc_bound，

adc_bound = adc_l_to_lane_center_ + ADC_speed_buffer + adc_half_width + ADC_buffer

上式中的各项：

adc_l_to_lane_center_ - 自车

adc_half_width - 车宽

adc_buffer - 0.5

ADCSpeedBuffer表示横向的瞬时位移， 公式如下：

![img](https://pic3.zhimg.com/80/v2-1c5add0cc9a1c96e73736700bce6a7ca_720w.jpg)

其中kMaxLatAcc = 1.5

（2）根据ADC_bound 与lane_bound 生成基本的bound

左侧当前s对应的bound取MAX(left_lane_bound,left_adc_bound), 即取最左边的

右侧当前s对应的bound取MIN(right_lane_bound,right_adc_bound)，即取最右边的

（3）path_bounds由一系列采样点组成，数据结构如下，这组数据里共有0～199个200个采样点，每两个点的间隔是0.5m；每个点由3个变量组成，分别是根据参考线建立的s-l坐标系的s坐标，右边界的l取值，左边界的s取值：

![img](https://pic3.zhimg.com/80/v2-513ef7414c7fdf0f5f6fda0456d445ba_720w.jpg)

（4）由于车道中心线和参考线不一定是重合的，所以以参考线为基准，左右边界的宽度如下，其中offset_to_lane_center是参考线到车道中心线的距离：

![img](https://pic2.zhimg.com/80/v2-3232858839138cf9e4f1e3d1ffce9c7d_720w.jpg)

（5）经过修正后，以车道中线为基准，车道的左右边界值如下：

![img](https://pic3.zhimg.com/80/v2-623d49dd0f137d851d9bc31438006852_720w.jpg)

修正代码如下：

reference_line.GetOffsetToMap(curr_s, &offset_to_lane_center);
curr_lane_left_width += offset_to_lane_center;
curr_lane_right_width -= offset_to_lane_center;
past_lane_left_width = curr_lane_left_width;
past_lane_right_width = curr_lane_right_width;

(6) 经过adc*bound 修正后的path_bound:*



![img](https://pic2.zhimg.com/80/v2-f00115e1cce4023d1d61c4fe3787a751_720w.jpg)



(7) 对于path*bound上的每个点，都要检查自车是否被*path*bound block，如果blocke则记录下pathblockedidx对应pathbound上点的index，并且停止后续path*_bound的生成；

```cpp
 // 4. Update the boundary.
 if (!UpdatePathBoundaryWithBuffer(i, curr_left_bound, curr_right_bound,
                                      path_bound, is_left_lane_boundary,
                                      is_right_lane_boundary)) {
      path_blocked_idx = static_cast<int>(i);
 }
 if (path_blocked_idx != -1) {
 break;
 }
```

(8) 对于lane_borrow 对应的*path*_bound，会有相应的逻辑作处理，将旁边车道的宽度考虑进来，最后生成的*path*_bound 如下：

```cpp
 double ADC_speed_buffer = (adc_frenet_ld_ > 0 ? 1.0 : -1.0) *
                              adc_frenet_ld_ * adc_frenet_ld_ /
                              kMaxLateralAccelerations / 2.0;


 double curr_left_bound_lane =
        curr_lane_left_width + (lane_borrow_info == LaneBorrowInfo::LEFT_BORROW
 ? curr_neighbor_lane_width
 : 0.0);
```

![img](https://pic4.zhimg.com/80/v2-2492fd3cefe161c3c3b6ff982bd67b2f_720w.jpg)

5、下篇文章介绍如何根据障碍物来修pathbound