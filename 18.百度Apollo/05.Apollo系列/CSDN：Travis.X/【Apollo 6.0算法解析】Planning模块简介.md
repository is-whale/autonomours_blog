- [【Apollo 6.0算法解析】Planning模块简介_Travis.X的博客-CSDN博客_apollo planning](https://blog.csdn.net/Travis_X/article/details/121859151)

# 前言

[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) 系统各个模块之间的通信框架如下图所示。
![在这里插入图片描述](https://img-blog.csdnimg.cn/02de19ac75e74fd2afb367648d010c4a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
橘色的实线为数据流动线，黑色实线为控制逻辑线。

Planning 模块的上游是Perception 、 Prediction、Localization 和 Routing 模块，下游是 Control 模块。它的作用是根据感知和预测的结果，根据当前车辆信息和路况信息**在有限时间内规划出一条平滑的且安全可行的行驶轨迹**，再交由控制模块去执行。若Planning 模块规划的轨迹不可行（比如道路施工、错过路口）就会触发 Routing 模块重新规划线路，因此 Planning 和 Routing 两个模块的数据流是双向的。

本文主要是简单介绍下 Apollo 中的 Planning模块，包括 Apollo 使用到的决策规划算法（详细的算法讲解和代码解析会之后的文章进行更新）。

------

# 一、Planning模块规划器简介

目前，Apollo Planning 模块提供以下五种规划器。

| 名称                | 加入版本 | 类型        | 说明                                          |
| ------------------- | -------- | ----------- | --------------------------------------------- |
| RTK Replay Planner  | 1.0      | RTK         | 根据录制的轨迹来规划行车路线。                |
| Public Road Planner | 1.5      | PUBLIC_ROAD | 实现了EM算法的规划器，这是目前的默认Planner。 |
| Lattice Planner     | 2.5      | LATTICE     | 基于状态网格的轨迹规划器。                    |
| Navi Planner        | 3.0      | NAVI        | 基于实时相对地图的规划器。                    |
| Open Space Planner  | 3.5      | OPEN_SPACE  | 用于开放空间的轨迹规划器。                    |

------

## 1.1 RTK Replay Planner

RTK Replay Planner 是基于录制的轨迹进行循迹的 Planner，是 Apollo 比较原始的一种 Planner。需要事先录制好设定的轨迹再来规划行车路线。

录制的轨迹文件格式可以参考 modules/planning/data/garage.csv。

轨迹中包含以下信息：

| 名称                  | 说明       |
| --------------------- | ---------- |
| x,y,z                 | 车辆位置   |
| speed                 | 车速       |
| acceleration          | 加速度     |
| curvature             | 曲率       |
| curvature_change_rate | 曲率变化率 |
| time                  | 时间戳     |
| theta                 | 航向角     |
| gear                  | 档位       |
| s                     | 路程       |
| throttle              | 油门       |
| brake                 | 刹车       |
| steering              | 方向盘转角 |

部分代码解析和流程可以参考[百度Apollo5.0规划模块代码学习（一）RTK规划器](https://blog.csdn.net/wwyklnh/article/details/98311706)这篇文章，在此不过多叙述。

------

## 1.2 Public Road Planner

**Public Road Planner是目前默认的Planner**，它实现了 EM Planner（Expectation Maximization）算法。

EM Planner是**基于轻决策**的规划算法。**与其他基于重决策的算法相比，EM Planner的优势在于能够处理许多复杂的场景**（例如多障碍物的场景）。当基于重决策的方法试图预先确定如何处理每个障碍物时，困难是显而易见的：（1）很难理解和预测障碍物如何与主车相互作用，因此它们的跟随运动难以描述，因此很难被任何规则考虑；（2）当多个障碍物阻塞道路时，无法找到满足所有预定决策的轨迹概率大大降低，从而导致规划的失败。

Planning大概流程是这样：先由 Routing模块进行全局规划得到参考线 Reference_line，然后 Planning模块在此基础上进行局部轨迹规划，EM Planner将路径和速度进行分层规划，并在 SL 和 ST坐标系下，使用动态规划（DP）进行路径和速度的决策和粗规划，然后再使用二次规划（QP）进行平滑处理。

### Apollo EM Planner的整体架构

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201020113008293.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RyYXZpc19Y,size_16,color_FFFFFF,t_70#pic_center)
动态规划：基于 Reference Line 和车道边界，将道路进行切片和撒点采样并逐级升级目标函数，最终得到一条可行路径。

二次规划：对路径和速度进行平滑处理，以平方项的形式进行量化，将其转化为二次规划问题。

详情可以参考[[论文解读\]Baidu Apollo EM Motion Planner](https://blog.csdn.net/Travis_X/article/details/109174898)

------

## 1.3 Lattice Planner

![在这里插入图片描述](https://img-blog.csdnimg.cn/1b4dd6daf06a40c6873010c3a3bdd997.png#pic_center)
Lattice Planner 用 Frenet 坐标系来表示汽车的状态，在Frenet坐标系下同时进行横向和纵向采样，再进行二维合成生成足够多的轨迹，然后计算每条轨迹的代价，再循环检测过程中每次挑选出代价最低的轨迹，对其进行物理限制检测和碰撞检测。如果挑出来的轨迹不能同时通过这两个检测，就将其筛除，考察下一条cost最低的轨迹。

------

## 1.4 Navi Planner

Navi Planner 是基于实时相对地图的规划器。目前资料比较少，待补充…

------

## 1.5 Open Space Planner

![在这里插入图片描述](https://img-blog.csdnimg.cn/c1dd8bfcb28b4c7caa989d3a70be7dc5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
该算法从两个不同的来源获取输入：

- 感知数据，包括但不限于障碍。
- 通过高清地图获得感兴趣区域(ROI)。

算法本身包括两个阶段：**基于规划的搜索**和**优化**。

基于规划的搜索是基于车辆的运动学模型运用Hybrid A*算法生成一条原始轨迹，如下图红线所示，再通过优化的方法生成绿色的平滑轨迹。
![在这里插入图片描述](https://img-blog.csdnimg.cn/93f9a810d3214b0e8c2a9663e89c0fcd.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
轨迹优化的作用是生成安全平滑的轨迹，以获得更好的乘坐舒适性体验，也使控制模块更容易跟踪。
![在这里插入图片描述](https://img-blog.csdnimg.cn/45aaf827b9494c7bb46c3802bae78bd8.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
![在这里插入图片描述](https://img-blog.csdnimg.cn/9a61053f978a4468b53e5e0f307b669c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

------

# 参考

【1】[解析百度Apollo之决策规划模块](https://paul.pub/apollo-planning/)
【2】[apollo介绍之planning模块(四)](https://zhuanlan.zhihu.com/p/61982682)
【3】[Dig-into-Apollo](https://gitee.com/drshawn/Dig-into-Apollo/tree/master/planning#introduction)
【4】[技术文档丨开发空间规划算法](https://mp.weixin.qq.com/s/eMaDPZp5pdq0TpgV_3PEKQ)