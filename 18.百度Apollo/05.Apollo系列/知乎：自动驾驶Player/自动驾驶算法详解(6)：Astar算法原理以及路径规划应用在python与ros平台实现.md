- [自动驾驶算法详解(6)：Astar算法原理以及路径规划应用在python与ros平台实现 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/522806083)

**一、算法原理**

Astar算法流程图如下:

![img](https://pic1.zhimg.com/80/v2-7234e81554bc86f85b52e2f4d1e0723c_720w.jpg)

Astar算法是一种图形搜索算法，常用于路径规划。它是个以广度优先搜索为基础，集Dijkstra算法与最佳优先(best fit)算法特点于一身的一种算法。

Astar算法从起点开始向周围扩张，通过估计函数不断计算周围相邻点的cost，并选择cost最小的节点作为下一次进行扩张的节点，按此规则一直循环直到搜索到终点。

其中每个扩展节点对应的估值函数记为：

f(n) = g(n) + h(n)

g(n) 表示当前节点的实际代价函数；

h(n) 表示当前节点至目标节点的估值函数，常用的估值方法有欧式距离、曼哈顿距离等；

**二、代码实现及注释**

```python
# init astar plannet
resolution = grid_size 
rr = robot_radius
min_x, min_y = 0, 0
max_x, max_y = 0, 0
obstacle_map = None
x_width, y_width = 0, 0
motion = get_motion_model()

### calc_obstacle_map
min_x = round(min(ox))
min_y = round(min(oy))
max_x = round(max(ox))
max_y = round(max(oy))
# cal map width
x_width = round((max_x - min_x) / resolution)
y_width = round((max_y - min_y) / resolution)
# generate map with obstacle
obstacle_map = [[False for _ in range(y_width)]
 for _ in range(x_width)]
for ix in range(x_width):
    x = calc_grid_position(ix, min_x)
 for iy in range(y_width):
        y = calc_grid_position(iy, min_y)
 for iox, ioy in zip(ox, oy):
            d = math.hypot(iox - x, ioy - y)
 if d <= rr:
                obstacle_map[ix][iy] = True
 break 

### start planning                
# define node
start_node = Node(calc_xy_index(sx, min_x),
                       calc_xy_index(sy, min_y), 0.0, -1)
goal_node = Node(calc_xy_index(gx, min_x),
                      calc_xy_index(gy, min_y), 0.0, -1)
# define 
open_set, closed_set = dict(), dict()
open_set[calc_grid_index(start_node)] = start_node

search_index = 0
while 1 :
 if len(open_set) == 0:
 print("Open set is empty..")
 break

    c_id = min(
        open_set,
        key=lambda o: open_set[o].cost + calc_heuristic(goal_node,open_set[o]))
    current = open_set[c_id]

 if current.x == goal_node.x and current.y == goal_node.y:
 print("Find goal")
        goal_node.parent_index = current.parent_index
        goal_node.cost = current.cost
 break

 # Remove the item from the open set
 del open_set[c_id]

 # Add it to the closed set
    closed_set[c_id] = current

 # expand_grid search grid based on motion model
 for i, _ in enumerate(motion):
        node = Node(current.x + motion[i][0],
                         current.y + motion[i][1],
                         current.cost + motion[i][2], c_id)
        n_id = calc_grid_index(node)

 # If the node is not safe, do nothing
 if not verify_node(node):
 continue

 if n_id in closed_set:
 continue

 if n_id not in open_set:
            open_set[n_id] = node  # discovered a new node
 else:
 if open_set[n_id].cost > node.cost:
 # This path is the best until now. record it
                open_set[n_id] = node
 
    search_index = search_index + 1

rx, ry = calc_final_path(goal_node, closed_set)
```

**三、结果分析**

在代码中可以设置启发函数的权重w，可以看到不同的w对搜索的速度与结果有直接影响

1、w = 10 的搜索结果，可以看到最终的搜索步数为43

![img](https://pic1.zhimg.com/80/v2-180b0a821bbb05e0082dddd0443edc58_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-2c84774d4aff12b2f72b2806f08ad8a6_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-d61e18cfb9438dbf8e4495c76caead4e_720w.jpg)

2、 w = 0 的搜索结果，可以看到最终的搜索步数为146

![img](https://pic4.zhimg.com/80/v2-413b4164d17449ddedff612e53551d83_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-8aa785aeca5c1ff23d45a6e39a9e531b_720w.jpg)

3、 w = 2 的搜索结果，可以看到最终的搜索步数为88

![img](https://pic2.zhimg.com/80/v2-b7cc766ab5a2b1cf09f87e1467eee329_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-e858d3f01bb0ed07fdd26e4cdf9e79e2_720w.jpg)