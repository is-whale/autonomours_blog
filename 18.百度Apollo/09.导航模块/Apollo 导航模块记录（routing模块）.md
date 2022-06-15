- [Apollo 导航模块记录（routing模块） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/459954010)

## 1. 概述

Apollo的routing模块读取高精地图原始信息，用于根据输入的RoutingRequest信息在base_map中选取匹配最近的点作为导航轨迹的**起点和终点** ，读取依据base_map生成的routing_map作为生成**topo_graph** 的，然后通过AStart算法在拓扑图中搜索连接起始点的最优路径RoutingResponse，作为输出发送出去。

## 2. 输入信息

routing模块的信息输入包括两个固定信息：高精地图原始信息（base_map）和生成的拓扑图（routing_map），一个外部输入的起始点请求信息（RoutingRequest）。

base_map信息和routing_map信息的读取在[http://routing.cc](https://link.zhihu.com/?target=http%3A//routing.cc)的Init()函数中完成代码如下：

![img](https://pic2.zhimg.com/80/v2-21a84031edb86ed57376bbb54dc880dd_720w.jpg)

RoutingRequest信息是其他模块发送的，RoutingComponent通过继承Component组件，实现Routing模块事件触发，代码如下：

![img](https://pic1.zhimg.com/80/v2-0a9e55341a5c6481a6931172f0e17c20_720w.jpg)

## 3. 处理输入信息

### 3.1从地图中选取最佳匹配点

选择匹配点的函数在[http://routing.cc](https://link.zhihu.com/?target=http%3A//routing.cc)的FillLaneInfoMissing()函数中，代码如下：

![img](https://pic1.zhimg.com/80/v2-3ad521699b7ed530b50a76939352a200_720w.jpg)

搜索过程中，存在因车道重叠而增加的lane信息，将该部分车道信息也增加如修正后的请求信息（fixed_requests）中，到此完成输入请求点信息的修正，用于后续进行路径搜索：

![img](https://pic2.zhimg.com/80/v2-a59708d6408271df5d6521a45c2f33a1_720w.jpg)

### 3.2搜索导航路径信息

routing模块的路径搜索功是通过AStart算法完成的，在[http://a_start_strategy.cc](https://link.zhihu.com/?target=http%3A//a_start_strategy.cc)的Search函数中实现，输入修正后的起点、终点、读取的拓扑地图以及根据起点终点生成的子拓扑图，得到起点到达终点的点集：

![img](https://pic3.zhimg.com/80/v2-c1cb0d0a4c729a11799c7d12abd4e462_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-22309d887dd42d9d74ceb324f9be13b7_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-8e2faf1d063ab61de2ec5ee1a4b83ca6_720w.jpg)

完成从起点到达终点的路径搜索后，还需要从中规划出一条完整的轨迹，然后生成可通行区域和Road。

**1）规划起始点到达终点路径**

在Reconstruct()函数中，从终点到起点进行反向搜索，获取轨迹点：

![img](https://pic3.zhimg.com/80/v2-1b907d496040f44390ec5b4eb44a347e_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-4d972f7fcf3a13c07c973c4f40b72d31_720w.jpg)

**2）提取基础可通行区域**

将重组得到的一条完整轨迹按照是否需要换道，生成基础可通行区域：

![img](https://pic3.zhimg.com/80/v2-47de31aae5699037694d57dec875a0d2_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-b8e024cbbc0084ec7317869ac2606728_720w.jpg)

红色的laneID为具有换道属性节点，依据这些特殊节点，将可通行路径进行划分，可通行路径的换道属性为图中标注的属性。

**3) 对提取的可通行区域进行扩展**

AStart算法搜索得到的是一条唯一可通行路径，路径上存在可以通过换道到达相同目的地的路径，因此需要对得到的路径进行向前、向后两个方向的扩展：

![img](https://pic1.zhimg.com/80/v2-c7c89fc660868c7f4845ccdcc4e65c84_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-7c6f075fe732b5282ac1f7d97be34fc9_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-be412d8f8a8d95eb5580c7530791e62f_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-7ee511ba6b3e9b2d1675f59b0ffe5cfd_720w.jpg)



红色laneID为向前搜索新增的节点，橙色laneID为向后搜索新增的节点。

## 4.生成RoadSegments

遍历扩展得到的可通行区域，依据是否可以通过换道到到达该节点，对扩展的可性行进行道路段构建：

![img](https://pic4.zhimg.com/80/v2-02afe931f18a2475ec826af0b64f1983_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-afea3f562a7de73280b6daa513e5c356_720w.jpg)



生成的Response中，包含road，passage，segment信息，为一条指引从起点到达终点的行驶路径的**节点索引**，roadID为第一个节点对应的道路ID，不包含车道中心线每个位置点信息，所以不能直接用于Planning模块。

将生成的Rosponse信息发送出去，Planning模块接受导航信息，然后生成PNC_Map，指引生成参考线轨迹，用于规划控制。