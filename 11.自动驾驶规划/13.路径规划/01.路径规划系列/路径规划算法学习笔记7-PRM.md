- [路径规划算法学习笔记7-PRM - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/146885104)

**一、原理：**

Probabilistic Road Map, 分为两个过程Learning phase和Query phase.

**Learning phase:**

1. Sample N points in C-space.

\2. Delete points that are not collision-free.

\3. Connect to nearest points and get collision-free segments.

\4. Delete segments that are not collision free.

![img](https://pic3.zhimg.com/80/v2-bc2da4be64c931848564768a617edae2_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-2277c53ea9a53d7c3eb23ab50aa8b8b6_720w.jpg)

**Query phase:**

1. Search on the road map to find a path from the start to the goal(using Dijkstra's algorithm or the A* algorithm).
2. Road map is now similar with the grid map(or a simplified grid map).

![img](https://pic3.zhimg.com/80/v2-d623d75490762c78fb83010f7c1c88ce_720w.jpg)

**二、优缺点：**

优势：

概率完备；不用在整个环境搜索，简化了环境，相对于A*更加高效

劣势：

Required to solve 2 point boundary value problem.

Build graph over state space but no particular focus on generating a path.

Not efficient.

------

**改进方法：Lazy collision-checking**

**一、基本思想：**

Collision-checking process is time-consuming, especially in complex or high-dimensional environments. So sample points and generates segments without considering the collision.

**二、流程：**

1. Find a path on the road map generated without collision-checking.

![img](https://pic1.zhimg.com/80/v2-aa65595ac9c02e71225225bfe15aa934_720w.jpg)



\2. Delete the corresponding edges and nodes if the path is not collision free.

![img](https://pic4.zhimg.com/80/v2-c0c151ff86a61cf72a888f47f60e231f_720w.jpg)

\3. Restart path finding.

![img](https://pic3.zhimg.com/80/v2-9436fcaa45e16f294188d7c6bebe679a_720w.jpg)