- [Apollo规划模块-PncMap - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/489756011)

## 1.概述

PncMap是Planning and Control Map的缩写，即用于规划控制使用的地图，Planning模块收到Routing发送的导航轨迹信息后，将其转为可以用于规划控制地图。PncMap功能实现位于Planning模块的reference_line_provider中，为参考线的生成提供基础数据。

## 2.数据结构信息

PncMap模块生成可用于规划控制使用的地图，因此需要包含完整的道路信息，使用的有高精地图元素信息，使用routing模块发送的导航轨迹信息，将其进行转化，因此也使用routing发送的元素信息，将两者进行对比：

![img](https://pic4.zhimg.com/80/v2-86819f11f107071aebda3d0047df3853_720w.jpg)

## 3.PncMap主要实现的功能

PncMap主要是将routing发送的规划轨迹转变为能够生成referenceline的中间数据，分为两步：

### 3.1 转化routing数据

![img](https://pic1.zhimg.com/80/v2-273b6489c0c109be18f8aeeebf3b9884_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-19e39c55e70e15b5737f5f551746cf37_720w.jpg)

图中左上角蓝色方框为一个road的示例，包含road_index,passage_index,lane_index，左下角在生成的RoutingResponse上进行车道线编号。

### 3.2 匹配请求点位置信息

将routing中的request点信息转化后的索引数据中进行匹配，找到具体LaneSegment信息：

![img](https://pic4.zhimg.com/80/v2-5787d186498201e72f46f7eedbde0e93_720w.jpg)

匹配得到的routing_waypoint_index_结果为：

```text
[{(LaneWaypoint(lane: lane2, s: xx) ,index: 0};
 {(LaneWaypoint(lane: lane24, s: xx), index: 13)}]
```

### 3.3 更新车辆状态信息

根据传入的车辆状态信息，更新当前所在LaneSegment位置以及下一必须经过点的LaneSegment位置信息：

![img](https://pic1.zhimg.com/80/v2-780018cb8da69ca04b0866f60e1cdec4_720w.jpg)

### 3.4 计算所有可能经过的相邻passage信息

根据上一步得到的本车所处index信息，计算当前位置可能经过的所有相邻可通行区域信息，作为将要生成的reference_line原始信息：

![img](https://pic3.zhimg.com/80/v2-07f18e90cf4927c9a2c5f6c5a7b7ddca_720w.jpg)

计算相邻可通行区域信息：

![img](https://pic3.zhimg.com/80/v2-b538e8aa3ad9fd031aebf7f141159536_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-eaf1b6e6a7bafbd1966e06d43d48382f_720w.jpg)

### 3.5 判断相邻passage是否可以驶入，生成route_segments

判断当前road索引范围内所有可以换道通行的passage中，是否可以真正驶入，因为相邻passage可能是反向的，也有可能相邻比较远（通过连续两个或以上换道才能驶入），将其排除：

![img](https://pic1.zhimg.com/80/v2-1a6e583c1fef9ee754db8ba39432272c_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-c75b1eeb6ea1ed1857afda9919caa121_720w.jpg)

至此，根据每一次车辆位置状态更新值，都可以获得当前车辆位置依据发送RoutingResponse计算得到用于规划控制使用的前后预瞄距离范围内的车道索引，用于生成reference_line。