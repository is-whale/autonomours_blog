- [Apollo规划模块详解（七）：算法实现-path bounds decider 上篇 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/444687011)

## 引

该task负责计算道路上可行使区域边界，其输出是对纵向s等间距采样，横向s对应的左右边界，以此来作为下一步结果的边界约束；

在一个函数中分四种情况对pathBound进行计算，按照处理顺序分别是fallback、pullover、lanechange、regular，不同的boundary对应不同的应用场景，其中fallback对应的path bound一定会生成，其余3个只有一个被激活，即按照顺序一旦有有效的boundary生成，就结束该task。

## 1. fallback boundary生成

fallback boundary通过函数GenerateFallbackPathBound（）生成，主要分为两步：

1）通过函数InitPathBoundary（）沿着参考线初始化一个横向无穷大的区域，该区域的纵向距离取决于车速和参考线的长度，fallback boundary的初始值就是基于这个区域，纵向间隔为0.5米生成的一系列边界点；

![img](https://pic2.zhimg.com/80/v2-4380ec5a9dc99b9a931adee2a9f3a7b9_720w.jpg)

2）调用函数GetBoundaryFromLanesAndADC（）遍历（1）中生成的边界点根据自车状态及道路信息生成boundary，其中入参里的是否借道设置为不借道，ADC_buffer固定设置为0.5米，根据中心线各个S上的宽度生成。

2-1）获取每一点boundary处对应的车道宽度及道路宽度，然后比较左右车道及道路宽度的值，判断车道边界是否就是道路边界，最后根据相对地图的偏移量计算当前边界点所对应的车道宽度；

计算结果为 车道的左右宽度curr_lane_left_width， curr_lane_right_width

2-2） 根据借道类型获取左右相邻车道，生成fallback boundary时，不借道，所以这里就先不对这块展开介绍；

2-3） 根据横向刹停距离以及基于车道的左右边界值计算左右边界

首先看一下计算时边界值时需要用到的几个变量

a） ADC_speed_buffer

我对ADC_speed_buffer的理解是按照最大横向加速度车辆横向速度降为0所需要的横向距离；

![img](https://pic1.zhimg.com/80/v2-fb5bac038914c924c29c252aa514ee48_720w.png)

b） curr_left_bound_lane、curr_right_bound_lane基于车道边界的左右边界，如果需要借道，则为当前车道宽度加上相邻车道宽度；

![img](https://pic1.zhimg.com/80/v2-d8712016ab0b0b0b02db3759538c2980_720w.jpg)

c）curr_left_bound_adc、curr_right_bound_adc，基于自车信息的左右边界，根据ADC_speed_buffer，ADC_buffer（0.5，可以认为是横向安全区间）,半车宽，adc_l_to_lane_center_（自车相对于车道中心的偏移量）计算

![img](https://pic2.zhimg.com/80/v2-71edfcac56cb291f8fc28f9c428bbd75_720w.png)

![img](https://pic3.zhimg.com/80/v2-51eb270d4ff2c1a3d49cbde24b9a2002_720w.png)

接下来根据配置文件中的is_extend_lane_bounds_to_include_adc（计算的边界值是否需要考虑自车状态）以及上述三组变量计算左右边界值

如果需要考虑自车状态，则在计算时边界值为同时考虑 基于自车信息的左右边界和基于车道边界的左右边界，取两者的边界较大值；若不需要考虑自车状态，则只需以基于车道边界的左右边界作为边界值。

另外需要注意的是，得到的边界值curr_left_bound、 curr_right_bound是基于参考线的，因此需要考虑参考线相对map的偏移量offset_to_map。

2-4）调用函数UpdatePathBoundaryWithBuffer（）根据上述得到的边界值curr_left_bound、 curr_right_bound，以及车道边界是否是道路边界的判定结果is_left_lane_boundary，is_right_lane_boundary更新boundary

a）计算边界缓冲系数left_adc_buffer_coeff、right_adc_buffer_coeff

这里的缓冲值的实际意义就是在上述boundary的基础上左右收缩半个车宽。

![img](https://pic1.zhimg.com/80/v2-db08b5687aadd1a17707f971d2740f98_720w.jpg)

b）判断道路是否被截断

如果计算出来的右边界大于左边界，则道路被截断

c）从boundary中删除被截断之后的边界点

![img](https://pic3.zhimg.com/80/v2-e65d358b73fde4ab740e0d0d9fd936a2_720w.jpg)

fallback boundary是一定要会生成的，因此需要确保有有效的fallback boundary生成，并将最后生成的有效boundary存入candidate_path_boundaries中

![img](https://pic1.zhimg.com/80/v2-764348652e5bbb03254c741f18b56fc4_720w.jpg)

下篇文章中我们会继续介绍Pullover boundary 的生成