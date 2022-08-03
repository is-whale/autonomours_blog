- [自动驾驶（八十）---------Apollo之导航模块_一实相印的博客-CSDN博客](https://blog.csdn.net/zhouyy858/article/details/111517494)

说[apollo](https://so.csdn.net/so/search?q=apollo&spm=1001.2101.3001.7020)的导航(Routing)模块，Routing模块给自动驾驶系统提供全局的路径导航功能，类似于百度地图/高德地图的导航。

## 1. Routing的概念

   在收到路由请求之后，为了完成导航功能，Routing模块使用了专用的路由地图routing_map。最终输出的是自动驾驶车辆在从出发点到目的地的过程中经过的所有路段。

   首先，我需要有地图，这里的地图需要的是一个拓扑结构的图，需要有车道级别的连接信息，百度高精地图就是做这个工作。然后我们定义起始点，并通过Routing找到一条最优路径。

   另外，分析Routing模块之前，我们只需要能够解决以下几个问题，就算是把routing模块掌握清楚了。

1. 如何从A点到B点？
2. 如何规避某些点？ - 查找的时候发现是黑名单里的节点，则选择跳过
3. 如何途径某些点？- 采用分段的形式，逐段导航（改进版的算法是不给定点的顺序，自动规划最优的线路）
4. 如何设置固定线路，而且不会变？最后routing输出的结果是什么？

## 2. Routing流程

1. 加载routing_map和起始点的位置，并找到起始点最近的lian这步没什么好解释的，相信都能理解。

2. 这一步需要考虑黑名单的问题，即在全局地图中，有一些车道有一段路不能走，或者有些点不能走，一般的考虑方式是把这些点和线段删掉，重新规划全局规划，也可以按照apollo的方式把这段路切开，分成几个subline，突然遇到不能走的地方，在这一段按照subline重新规划这一条段，一般都是在黑名单一定区域内重新导航，这样就不用全局重新规划了。参考这一段apollo代码：

```cpp
SubTopoGraph::SubTopoGraph(
  const std::unordered_map<const TopoNode*, std::vector<NodeSRange>>&black_map) {
  std::vector<NodeSRange> valid_range;
  for (const auto& map_iter : black_map) {
    valid_range.clear();
    GetSortedValidRange(map_iter.first, map_iter.second, &valid_range);
    // 生成子节点
    InitSubNodeByValidRange(map_iter.first, valid_range);
  }
  for (const auto& map_iter : black_map) {
    // 生成子边
    InitSubEdge(map_iter.first);
  }
  for (const auto& map_iter : black_map) {
    AddPotentialEdge(map_iter.first);
  }
}
```

3. Astar算法搜索路径。A*算法之前有文章专门介绍，[传送](https://blog.csdn.net/zhouyy858/article/details/98958229)，这里就不介绍算法的思想了，主要从代码的角度看一看apollo的实现过程：

```php
bool AStarStrategy::Search(const TopoGraph* graph,const SubTopoGraph* sub_graph,
                           const TopoNode* src_node, const TopoNode* dest_node,
                           std::vector<NodeWithRange>* const result_nodes) {
  Clear();
  AINFO << "Start A* search algorithm.";
  //优先级队列openlist,用priority_queue实现.std::priority_queue是一种容器适配器，
  //它提供常数时间的最大元素查找功能，亦即其栈顶元素top永远输出队列中的最大元素。
  //但SearchNode内部重载了<运算符，对小于操作作了相反的定义
  //因此std::priority_queue<SearchNode>的栈顶元素永远输出队列中的最小元素。
  std::priority_queue<SearchNode> open_set_detail;
  //将起点设置为检查节点
  SearchNode src_search_node(src_node);
  //起点的f值
  src_search_node.f = HeuristicCost(src_node, dest_node);
  //把起点节点存入openli
  open_set_detail.push(src_search_node);
  //把起点加入到open_set
  open_set_.insert(src_node);
  g_score_[src_node] = 0.0;
  enter_s_[src_node] = src_node->StartS();
 
  SearchNode current_node;
  std::unordered_set<const TopoEdge*> next_edge_set;
  std::unordered_set<const TopoEdge*> sub_edge_set;
  //进入算法主循环,只要openlist不为空,就一直循环查找
  while (!open_set_detail.empty()) {
    current_node = open_set_detail.top();
    const auto* from_node = current_node.topo_node;
    if (current_node.topo_node == dest_node) {
      if (!Reconstruct(came_from_, from_node, result_nodes)) {
        AERROR << "Failed to reconstruct route.";
        return false;
      }
      return true;
    }
    open_set_.erase(from_node);
    open_set_detail.pop();
	//判断当前节点是否被检查过
    if (closed_set_.count(from_node) != 0) {
      // if showed before, just skip...
      continue;
    }
    //当前点加入openlist
    closed_set_.emplace(from_node);
 
    // if residual_s is less than FLAGS_min_length_for_lane_change, only move
    // forward
    //先通过判断用不用变道,再获取当前节点的所有相邻边
    //GetResidualS 为当前节点到终点的剩余距离s
    //若s<min_length则不变道,若s > min_length 则变道
    const auto& neighbor_edges =
        (GetResidualS(from_node) > FLAGS_min_length_for_lane_change &&
         change_lane_enabled_)
            ? from_node->OutToAllEdge()
            : from_node->OutToSucEdge();
    double tentative_g_score = 0.0;
    next_edge_set.clear();
    //从上面相邻边neighbor_edges中获取其内部包含的边,将所有相邻边全部加入集合:next_edge_set
    for (const auto* edge : neighbor_edges) {
      sub_edge_set.clear();
      sub_graph->GetSubInEdgesIntoSubGraph(edge, &sub_edge_set);
      next_edge_set.insert(sub_edge_set.begin(), sub_edge_set.end());
    }
	//所有相邻边的目标节点就是我们要逐一检查的相邻节点,
	//对相邻节点逐一检查,寻找总代价最小的节点,该节点就是下次扩展的节点
    for (const auto* edge : next_edge_set) {
      const auto* to_node = edge->ToNode();
      //判断是不是已经在closelist中
      if (closed_set_.count(to_node) == 1) {
        continue;
      }
      //若当前边到相邻节点的距离小于min_length,则不能通过变道到达相邻节点,就直接忽略
      if (GetResidualS(edge, to_node) < FLAGS_min_length_for_lane_change) {
        continue;
      }
      //更新当前节点的移动代价g
      tentative_g_score =
          g_score_[current_node.topo_node] + GetCostToNeighbor(edge);
      //如果边的类型不是向前而是向左向右,表示变道,需要更新移动代价g的计算方式
      if (edge->Type() != TopoEdgeType::TET_FORWARD) {
        tentative_g_score -=
            (edge->FromNode()->Cost() + edge->ToNode()->Cost()) / 2;
      }
      //总代价f = g + h
      double f = tentative_g_score + HeuristicCost(to_node, dest_node);
      //判断当前节点到相邻节点的代价是不是比原来的代价小,如果小则更新代价,否则忽略
      if (open_set_.count(to_node) != 0 &&
          f >= g_score_[to_node]) {
        continue;
      }
      // if to_node is reached by forward, reset enter_s to start_s
      //如果是以向前的方向到达相邻节点,则更新节点的进入距离enter_s
      if (edge->Type() == TopoEdgeType::TET_FORWARD) {
        enter_s_[to_node] = to_node->StartS();
      } else {
        // else, add enter_s with FLAGS_min_length_for_lane_change
        //若是以向左或者向右的方式到达相邻界定啊,则将相邻及诶单的进入距离更新为与当前节点长度的比值
        double to_node_enter_s =
            (enter_s_[from_node] + FLAGS_min_length_for_lane_change) /
            from_node->Length() * to_node->Length();
        // enter s could be larger than end_s but should be less than length
        to_node_enter_s = std::min(to_node_enter_s, to_node->Length());
        // if enter_s is larger than end_s and to_node is dest_node
        if (to_node_enter_s > to_node->EndS() && to_node == dest_node) {
          continue;
        }
        enter_s_[to_node] = to_node_enter_s;
      }
	 //更新从起始点移动到当前节点的移动代价
      g_score_[to_node] = f;
      //将代价最小的相邻节点设置为下一个检查点
      SearchNode next_node(to_node);
      next_node.f = f;
      //把下一个待检查节点加入到openlist
      open_set_detail.push(next_node);
      //设置当前节点的父节点
      came_from_[to_node] = from_node;
      //若相邻及节点不在openlist中,则将其加入到open_set中,便于后续考察
      if (open_set_.count(to_node) == 0) {
        open_set_.insert(to_node);
      }
    }
  }
  //整个循环结束后仍未正确返回,则表明搜索失败
  AERROR << "Failed to find goal lane with id: " << dest_node->LaneId();
  return false;
}
```

   