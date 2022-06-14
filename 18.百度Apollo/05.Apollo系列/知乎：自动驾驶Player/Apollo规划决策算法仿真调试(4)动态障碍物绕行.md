- [Apollo规划决策算法仿真调试(4):动态障碍物绕行 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/511997496)

**前言**：

最近很多粉丝问在apollo规划算法planning模块中，如何才能对运动的障碍物进行绕行。动态障碍物的绕行在量产项目中是一个很大的话题，需要很复杂的逻辑与场景去解决。

这里先不讨论量产解法，提供一种在apollo的仿真环境中动态障碍物绕行的解决思路。先上一个动态障碍物绕行视频：

<iframe title="video" src="https://video.zhihu.com/video/1507309076681326592?player=%7B%22autoplay%22%3Afalse%2C%22shouldShowPageFullScreenButton%22%3Atrue%7D" allowfullscreen="" frameborder="0" class="css-uwwqev" style="width: 688px; height: 387px;"></iframe>

apollo规划算法动态障碍物绕行

1、动态障碍物绕行分析：

![img](https://pic4.zhimg.com/80/v2-0daa21bf28327f5bc70ada2097ab599b_720w.jpg)

2、PathLaneBorrowDecider分析

需要进入借道场景才可以触发绕行功能。

3、PathBoundsDecider分析：

可以看到经过PathBoundsDecider计算后，总共形成3个pathBoundary，分别是fallback、regular、right-forward

![img](https://pic3.zhimg.com/80/v2-9ae1121b998f10bff92fa6b3839ee7be_720w.jpg)

相关代码如下：

```text
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
```

4、PathAssessmentDecider分析

PathAssessmentDecider中进行路径选择，排序前：

![img](https://pic3.zhimg.com/80/v2-c01df3e1b813a0db98a0458ea19def5e_720w.jpg)

排序后：

![img](https://pic2.zhimg.com/80/v2-29a66eb73c0af7bdb44e59be9152f015_720w.jpg)

5、最终选择的路径

可以看到，最终选择的路径是向右借道的路径。

路径如下：

![img](https://pic3.zhimg.com/80/v2-420e9c3c3048e9afdd38ec53707c69c6_720w.jpg)

6、最终结果如下：

![img](https://pic1.zhimg.com/80/v2-6dc9eba182b7fdd4fa547b52569ae9a8_720w.jpg)

