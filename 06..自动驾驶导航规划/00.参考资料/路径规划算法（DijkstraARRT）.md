- [（二）路径规划算法（Dijkstra/A*/RRT） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/346049418)

![img](https://pic4.zhimg.com/80/v2-db8d7ee2af0ae6e833435bb98ce1a39f_720w.jpg)

## 1 BFS宽度优先遍历

为了方便讲解，我们使用0-1的格子地图，即0代表该格子可通行，1代表该格子存在障碍物，不可通行。我们先考虑场景中没有障碍物的情况，如下图所示：

![img](https://pic2.zhimg.com/80/v2-a584673c41f3431dfce1a8c8bc57eec9_720w.jpg)

0-1格子地图，红色代表起点，绿点代表终点

宽度优先遍历的思想为：**从起始点开始，一直朝着四面八方“扩散搜索”，直到搜索到目标点为止。**下图为运行BFS算法后的寻径效果图，每一个格子中的数目代表该格子是在第几步的时候被搜索到的：

![img](https://pic1.zhimg.com/80/v2-73ce6fb65d0f8c2c934eed7d46d04648_720w.jpg)

BFS寻径过程图，格中数字代表搜索step数

我们将图中相同数字的格子连接起来，可以看出：**BFS是以起点作为中心，朝着西面八方散开搜索，直到当前的搜索边界“碰到”了目标点后停止扩散。**在完成了扩散搜索后，我们如何找到从起点到目标点的路径呢？其实已经很简单了，我们只需要在路径中找到 起点-1-2-3-4-5-终点 的这样一条路径即可，最终路径图如下所示：

![img](https://pic4.zhimg.com/80/v2-bb3f77587569e0670fb3677fb7ced60b_720w.jpg)

最终路径图，1代表同行路径

那么我们如何实现“扩散搜索”和“寻找路径”呢？下面我们挑算法的核心代码进行讲解和分析，BFS的完整代码可以查看我的Github：

[https://github.com/HarderThenHarder/autoPlanningAlgorithm/blob/master/GridWorldAlgorithm/AlgorithmLib/algorithms.pygithub.com/HarderThenHarder/autoPlanningAlgorithm/blob/master/GridWorldAlgorithm/AlgorithmLib/algorithms.py](https://link.zhihu.com/?target=https%3A//github.com/HarderThenHarder/autoPlanningAlgorithm/blob/master/GridWorldAlgorithm/AlgorithmLib/algorithms.py)

宽度优先遍历（BFS）基本都由队列 queue 来实现，其核心思想是：依次访问一个节点和该节点的所有邻居节点，再去访问邻居节点和邻居节点的邻居节点，慢慢往外扩散，从而实现我们之前所提到的“扩散搜索”的目的。例如我们上面贴出的BFS寻径图，所有值为1的节点都是起点（红色）的邻居，因此它们会被优先访问；当所有的1被访问完后，我们接着访问所有值为1的格子的邻居们——也就是图中所有值为2的格子，这样依次往外扩散，完成搜索。下面我们来看看python代码如何实现：

```text
queue, tmp_queue = [start_node], []    # 将起点加入队列 queue 中，tmp_queue 不用管
    start_node.visited = True              # 设置首节点已经被访问过了
    step = 0

    while queue:
        current_node = queue.pop(0)        # 从队列首部取出一个节点
        current_node.step = step
        current_node.visited = True        # 访问过的节点一定要重置已被访问变量，不然会一直循环

        if current_node == target_node:    # 如果我们找到目标点后就不再继续搜索了
            break

        for index in current_node.neighbours_index:  # 把当前节点的所有邻居都加到队列中
            n = world.get_node(index) 
            if not n.visited:              # 只有该邻居节点没被访问的时候才进行访问
                tmp_queue.append(n)
                n.come_from_index = current_node.get_index()  # 设置该邻居节点的父节点为当前节点

        if not len(queue):    # 这部分是为了可视化一层一层扩散搜索的效果代码，不用管它
            step += 1
            queue.extend(tmp_queue)
            tmp_queue.clear()
```

上面代码中的 tmp_queue 和 最下层的 if not len(queue): 部分代码是我为了做出一层层扩散搜索可视化效果的代码，不用理解它。以上部分的核心代码在于：

```text
while queue:
    current_node = queue.pop(0)

     if current_node == target_node:  # 如果我们找到目标点后就不再继续搜索了
            break

     for index in current_node.neighbours_index:  # 把当前节点的所有邻居都加到队列中
            n = world.get_node(index) 
            if not n.visited:              # 只有该邻居节点没被访问的时候才进行访问
                tmp_queue.append(n)
                n.come_from_index = current_node.get_index()
```

## 2 迪杰斯特拉算法（Dijsktra）

在BFS算法中我们考虑了如何找到一条从起始点到目标点的路径，但是我们没有考虑路径权重和障碍物的情况。路径权重是指我们在通过这条路径的时候会耗费多少的代价（cost），比方说某一个格子对应的地形是一个山脉，如果我们要从这个格子通行的话就会耗费较大的代价（cost），当存在路径代价的情况下，我们使用BFS就很难解决了，因为我并没有考虑路径的代价因素。其实，BFS搜素中，所有被访问过的节点就不会再被访问了，而引入了路径代价后，某些之前访问过的节点可能现在有了更低的路径代价，例如：

![img](https://pic2.zhimg.com/80/v2-74942e8273e8b7635f2d4ff7da9c70dd_720w.jpg)

当我们第一次访问 N1 的邻居节点N3时，计算出 N1→N3 的距离为30，但当我们访问了 N2 后通过 N2 去访问 N2 的邻居节点 N3 时，发现 N1→N2→N3这条路径的距离只有20，比第一次访问的30要小，但是 BFS 算法规定我们一个节点只能访问一次。因此，我们只用改一改这里的逻辑：**当该节点是第一次被访问 or 该节点能够计算出更短的同行代价的时候，我们去访问该节点就行了**。最后的效果如下：

![img](https://pic4.zhimg.com/80/v2-631185429816b9ba80564361c894f3a7_720w.jpg)

地图权重矩阵

![img](https://pic3.zhimg.com/80/v2-c7b61a154f53f73eebe3e0b6bb9d294a_720w.jpg)

代码上我们采用优先队列（PriorityQueue）来实现，优先队列是一种特殊的队列结构，除了队列的基本先进先出（FIFO）的特性外，优先队列还会考虑优先级，**在满足相同优先级的对象之间才会考虑FIFO**，否则会先 pop 出优先级越高（值越小）的对象。

```text
from queue import PriorityQueue

    world.load_weights_map(weights_map)

    protect_index = 0   # 用路径长短作为权重，当路径长短相同时，PriorityQueue 会顺位比较两个 Node 对象，会报错，
                        # 因此在 Node 之前插入一个独一无二的 Index

    pqueue = PriorityQueue()
    pqueue.put((0, protect_index, start_node))
    protect_index += 1
    cost_so_far = {start_node: 0}

    while not pqueue.empty():
        current_node = pqueue.get()[2]

        if current_node == target_node:
            break

        for index in current_node.neighbours_index:
            n = world.get_node(index)
            new_cost = cost_so_far[current_node] + n.weight

            if n not in cost_so_far or new_cost < cost_so_far[n]:
                cost_so_far[n] = new_cost
                """ 将距离作为权重放入优先队列，距离越小权重越高，越先被队列弹出 """
                pqueue.put((new_cost, protect_index, n))
                protect_index += 1
                n.come_from_index = current_node.get_index()
```

上述代码中主要变更的关键部分在于 for 循环遍历节点邻居的部分：

```text
for index in current_node.neighbours_index:
            n = world.get_node(index)
            new_cost = cost_so_far[current_node] + n.weight    # 计算加入该点后通往邻居节点的路径cost

            """  如果该节点第一次被访问，或能产生更短的路径cost """
            if n not in cost_so_far or new_cost < cost_so_far[n]:
                cost_so_far[n] = new_cost

                """ 将cost作为权重放入优先队列，cost越小权重越高，越先被队列弹出 """
                pqueue.put((new_cost, protect_index, n))
                protect_index += 1
                n.come_from_index = current_node.get_index()
```

## 3 A\* 算法

迪杰斯特拉很好的解决了带权路径的求解，但在搜索的时候是以“从起点开始能够消耗最少的同行路径”作为搜索导向的。但在很多时候可能我们的目标点并一定存在在那些路径cost小的地方，如下图所示：

![img](https://pic3.zhimg.com/80/v2-9d9c619cb53c1c2aacf9e79a59fbc45a_720w.jpg)

如果使用迪杰斯特拉算法进行路径搜寻的话，会优先去搜寻那些平坦的道路（路径代价小），但不会朝着我们的目标点方向尝试进行搜索，但在实际情况下，我们期望算法在搜索时能够兼顾“已消耗代价”和“目标点信息”两者的，这就是我们常说的**启发式搜索**，即在搜索的时候能够受到一些外界信息的引导（例如目标点信息）。

回顾我们之前的迪杰斯特拉算法，我们在使用优先队列的时候只考虑了当前已走过的路径最小（即该点到起点的距离），现在我们期望全局距离最小，那我们只需要再加入**该点到目标点**的剩余距离估计就好了，即：

Priority=cost(start,current)+cost(current,target)Priority=cost(start,current)+cost(current,target)

只不过，在 A* 中我们使用曼哈顿距离来衡量当前点到目标点之间的距离，即：

```text
cost(current, target) = |target.x - current.x| + |target.y - current.y|
```

代码修改如下：

```text
from queue import PriorityQueue

    def heuristic(current_node, target_node):
        """
        启发式搜索项，用于估算当前点到目标点还剩多少距离，此处用曼哈顿距离。
        :return: 曼哈顿距离
        """
        h_c, w_c = current_node.get_index()
        h_t, w_t = target_node.get_index()
        return abs(h_c - h_t) + abs(w_c - w_t)

    pqueue = PriorityQueue()
    protect_index = 0
    pqueue.put((0, protect_index, start_node))
    protect_index += 1

    cost_so_far = {start_node: 0}

    while not pqueue.empty():
        current_node = pqueue.get()[2]

        if current_node == target_node:
            break

        for n_index in current_node.neighbours_index:
            n = world.get_node(n_index)
            new_cost = cost_so_far[current_node] + n.weight
            if n not in cost_so_far or new_cost < cost_so_far[n]:
                cost_so_far[n] = new_cost
                """ 用当前已走过的距离 + 剩余到目标点的距离作为该 Node 的权重 """
                priority = new_cost + heuristic(n, target_node)    
                pqueue.put((priority, protect_index, n))
                protect_index += 1
                n.come_from_index = current_node.get_index()
```

核心代码在于计算优先级权重的这一行：

```text
""" 用当前已走过的距离 + 剩余到目标点的距离作为该 Node 的权重 """
priority = new_cost + heuristic(n, target_node)
```

## 4 RRT 算法

当地图过大的时候，我们使用迪杰斯特拉或是 A* 算法效果就不那么理想了。快速扩展随机树（RRT）算法是非常使用的一种路径搜索算法，该算法能够在地图中快速展开生成一棵树，最后返回一条从起始点到终点的一条可行路径。该章节参考了其他知乎答主的优秀回答：

[小明工坊：【机器人路径规划】快速扩展随机树(RRT)算法176 赞同 · 22 评论文章

![img](https://pic3.zhimg.com/v2-021a13b4f4b1ae2499d05cc62b3b4d7e_ipico.jpg)



在该章节中我们尝试模拟**智能机器人在房间里寻路的问题**，假设给定指令从一个点（卧室）到另一个点（厨房），机器人该如何规划自己的路径，找到一条从起点到终点的通路。最后的效果演示如下：

![img](https://pic2.zhimg.com/v2-cbd497b9313b7fb64a2aa762532ee459_b.jpg)



图中**两个红色圆点**（主卧和厨房/男孩卧）分别为**起点**和**目标点**，可以看到，在前两个场景（终点为厨房，男孩卧门口）时，算法能快速的规划并找到一条通行路径；但在场景3（终点为男孩卧角落）的时候，算法无法规划出一条同行路径。那么为什么会出现这样的问题，以及怎样去解决这些问题呢，接下来我们就来讲解一下 RRT 算法的原理吧。

上述代码 Github 链接：

[https://github.com/HarderThenHarder/autoPlanningAlgorithm/tree/master/ImageWorldAlgorithmgithub.com/HarderThenHarder/autoPlanningAlgorithm/tree/master/ImageWorldAlgorithm](https://link.zhihu.com/?target=https%3A//github.com/HarderThenHarder/autoPlanningAlgorithm/tree/master/ImageWorldAlgorithm)

RRT 和 A* 一样，同样是一种**启发式**的算法，在搜索的过程中会用到**目标点**的位置信息。RRT 算法中一共分为 4 个主要步骤：

1. 采样
2. 扩树
3. 合法性判断
4. 算法终止条件判断

> 1. 采样

采样步骤是为了选择一个**临时目标点**，临时目标点决定了下一步树的扩展方向。我们之前提到，RRT 也是一种启发式的算法，其会用到目标点的位置信息，因此我们在这里完全可以将树的扩展方向设定为往目标点的扩展方向，但是，这样会存在一个问题：如果目标点和当前点之前存在不和同行的区域会导致无意义的搜索，例如下面这种情况：

![img](https://pic4.zhimg.com/80/v2-0b2d66a92172957f47b2392e28572f1f_720w.jpg)

如果我们只向着目标点的方向去做扩展，那么我们会一直卡死在墙角处，无法绕出卧室。因此，我们在选择临时目标点的时候，**应该以一定概率ϵϵ选择目标点为临时目标**，**还有一定概率1−ϵ1−ϵ在地图中随机选择一个点作为临时目标**。

> 2. 扩树

在我们选择了临时目标点后，我们就要对整棵树添加一个新的子节点了。我们选择整棵树所有的节点中离临时目标点最近的节点，**按照我们的预设步长扩展出一个新的节点即可**，如下所示：

![img](https://pic3.zhimg.com/80/v2-a07d54acf2af2d3adbfdab2af05b2fba_720w.jpg)

> 3. 合法性判断

在我们扩树的时候，有可能出现非法的情况，比如新扩展的叶子节点位于障碍物内或跨过了障碍物，我们需要对这种情况做出判断，如下所示：

![img](https://pic3.zhimg.com/80/v2-f729a667e860bc90c7bab5676034f41e_720w.jpg)

当存在非法扩建点的时候，我们需要舍弃这个生成点，重复1、2步骤，直到一个合法点生成，将该合法点添加入 RRT 树中。

> 4. 算法终止条件判断

当新生成的扩展点离最终目标点小于一定距离阈值后，我们停止整个算法循环：

![img](https://pic3.zhimg.com/80/v2-20915d6e00cbeb3fb8565153b2c45586_720w.jpg)

最终，我们从最后一个点回溯到起始点，形成算法最后的规划路径：

![img](https://pic3.zhimg.com/80/v2-cd79654e1e68456a1362e59839f97ba6_720w.jpg)

RRT 算法的核心代码如下，主要为上述 4 个步骤的功能实现：

```text
while try_num < max_iterations:

        """ 1. 采样：Sample 一个临时目标点 """
        if random.random() < epsilon:
            sample_point = np.mat(np.random.randint(0, map_array.shape[0], size=(1, 2)))  # 在地图内随机 Sample 一个点
        else:
            sample_point = target_pos

        """ 2. 扩树：在 RRT 树上添加一个叶节点 """
        straight_distances = tbx.straight_distance(rrt_tree[:, :2], sample_point)
        closest_index = np.argmin(straight_distances, 0)[0, 0]
        closest_pos = rrt_tree[closest_index, :2]

        theta = math.atan2(sample_point[0, 1] - closest_pos[0, 1], sample_point[0, 0] - closest_pos[0, 0])
        new_node_pos = closest_pos + step_size * np.mat([math.cos(theta), math.sin(theta)])
        new_node_pos = np.around(new_node_pos)

        """ 3. 判断：检查扩展节点是否合法，处于障碍物中或是已经在树节点上 """
        # 路径是否处在障碍物上
        if not tbx.check_path(closest_pos, new_node_pos, map_array):
            try_num += 1
            continue

        # 新节点是否已经存在于RRT树上
        min_distance = min(tbx.straight_distance(rrt_tree[:, :2], new_node_pos))[0, 0]
        if min_distance < threshold:
            try_num += 1
            continue

        # 通过合法判断，将新节点添加入RRT树中
        new_node = np.hstack((new_node_pos, [[closest_index]]))
        rrt_tree = np.vstack((rrt_tree, new_node))

        """ 4. 判断当前点是否已经在 target 目标点周围 """
        if tbx.straight_distance(new_node_pos, target_pos)[0, 0] < threshold:
            return True, rrt_tree

    return False, rrt_tree
```