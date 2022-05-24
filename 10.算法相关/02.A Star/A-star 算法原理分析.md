- [A-star 算法原理分析_Alan-zzx的博客-CSDN博客_a star算法](https://blog.csdn.net/m0_37264516/article/details/88045568)
- [A*搜索算法(A-Star Search)简介及保姆级代码解读_高能阿博特的博客-CSDN博客_a*搜索](https://blog.csdn.net/weixin_58399148/article/details/121347500)
- [使用A Star 算法实现自动寻路详解 - 我玩亚索我会C - 博客园 (cnblogs.com)](https://www.cnblogs.com/JDCODE/p/14973935.html)

## 搜索算法

图论中，应用最广泛的就是搜索算法了，比如，[深度优先搜索](https://so.csdn.net/so/search?q=深度优先搜索&spm=1001.2101.3001.7020)、广度优先搜索等。在介绍 Dijkstra 算法那篇中，除了深度优先、广度优先这种暴力搜索算法，还有一些最短路算法也可以求得最短路径，并且效率比深度、广度优先搜索高。

在一些大型网络游戏中，往往存在一种场景，游戏中的你需要从当前位置走到目的地，而你只需要在地图中找到目标位置，点击即可自动寻路走过去，那么这个自动寻路的功能是怎么实现的呢？

在实际场景中，游戏地图通常会很大，而且其中的路，障碍物等都不是很规则，并不能简单的将它们抽象成节点和线段。在寻路的过程中需要绕过所有障碍物，并且找到一条不会绕路的路径。似乎最短路是最符合要求的，但是如果地图很大，节点很多很多，那么求最短路的算法执行效率还是太低了。

不管是游戏中的寻路还是我们日常生活中地图软件的路线规划，其实都不需要找到那条最短路，而是在权衡效率和路线质量的情况下，找到一个次优解即可，A* 算法就是可以平衡效率和路线质量，找到次优路线的搜索算法。

## A* 算法

A* 算法实际上是对 Dijkstra 算法的优化和改造后得到的。Dijkstra 算法详见：
[单源最短路 Dijkstra 算法原理详解、代码实现和复杂度分析](https://blog.csdn.net/m0_37264516/article/details/86766513).

Dijkstra 算法的核心思想是每次都获取离起始点最近的节点，再向外扩展。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190228201204472.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3MjY0NTE2,size_16,color_FFFFFF,t_70)

如图，从起始点 1 开始，目的地为节点 5。如果采用 Dijkstra 算法，首先一定会走节点 6，7，8。但实际上走节点 6，7，8 是在绕路了，这肯定不是最佳路径，即一开始有点跑偏了。考虑一下如何避免一开始跑偏这个问题呢？

在 Dijkstra 算法中，我们创建了优先队列，从优先队列中取出节点，再从这个节点扩散到其他节点（有点类似[广度优先搜索](https://so.csdn.net/so/search?q=广度优先搜索&spm=1001.2101.3001.7020)），优先队列的优先依据是根据起始点到队列中节点最近的一个。即队列中的节点总是离起始点最近的先出队，这样就很好理解为什么一开始会跑偏了。

当我们走到某个节点时，已知的是起始点到该节点的距离 g(i)，Dijkstra 算法判断依据就只有这一个，**那么我们是否可以再增加一个判断量来减少跑偏的概率。如果能计算得到当前节点到终点的距离，结合 g(i) 可以实现防止过度跑偏**。但是这个 距离是未知的，我们可以计算一个估值，那么如何计算出它的估值呢？

在地图中，我们虽然不知道中间某个点距离终点的最短路，但是我们可以**将地图置于坐标轴中**，通过计算节点之间的**欧几里得距离**得到两点之间的直线距离，这便可以作为当前节点离终点距离的估值，欧几里得距离计算需要开平方等复杂的操作，因此我们通过计算简单的**曼哈顿距离**来代替欧几里得距离，曼哈顿距离就是计算两点之间横纵坐标的距离之和，只涉及到加减法和符号转换。这里得到的距离记为 `h(i)`，称作**启发函数**。

如图，绿色线为欧几里得距离，其他为曼哈顿距离。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190228212806584.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3MjY0NTE2,size_16,color_FFFFFF,t_70)

到这里为止，我们就可以得到最终的**估价函数**啦，优先队列中以估价函数值最低的优先出队。

```
f(i) = g(i) + h(i)
```

## A* 算法的代码实现

现在，我们要实现 A* 算法的代码，那么首先就需要抽象出节点等的数据结构，与 Dijkstra 算法相似，但加入了横纵坐标值：

```java
public class Graph {

    private class Edge {// 表示边
        public int sid;// 边的起始节点
        public int tid;// 边的结束节点
        public int w;// 边的权重
        
        public Edge(int s, int t, int w) {
            this.sid = s;
            this.tid = t;
            this.w = w;
        }
    }

    private class Vertex {
        public int id; // 顶点编号 ID
        public int dist; // 从起始顶点，到这个顶点的距离，也就是 g(i)
        public int f; // 新增：f(i)=g(i)+h(i)
        public int x, y; // 新增：顶点在地图中的坐标（x, y）
        public Vertex(int id, int x, int y) {
          this.id = id;
          this.x = x;
          this.y = y;
          this.f = Integer.MAX_VALUE;
          this.dist = Integer.MAX_VALUE;
        }
    }


    private LinkedList<Edge> adj[];// 邻接表
    private Vertex[] vertexes;//记录所有顶点的坐标
    private int v;// 顶点数
    
    public Graph(int v) {
        this.v = v;
        Vertex[] vertexes = new Vertex[this.v];
        this.adj = new LinkedList[v];
        for(int i; i<v; i++) {
            this.adj[i] = new LinkedList<Edge>();
        }
    }
    
    public void addEdge(int s, int t, int w) {
        this.adj[s].add(new Edge(s, t, w));
    }
    
    //添加顶点坐标
    public void addVetex(int id, int x, int y) {
        vertexes[id] = new Vertex(id, x, y)
    }
}
```

以上就是 A* 算法的图的定义，与 Dijkstra 算法的图区别在于：

1. 图类中增加成员变量`Vertex[] vertexes`记录所有节点的坐标
2. 构造函数中进行初始化`Vertex[] vertexes`
3. 新增添加节点坐标的方法`addVetex(int id, int x, int y)`

在具体算法实现中，与 Dijkstra 的区别主要在以下几点：

1. 优先队列的构建方式不同，A* 算法是使用代码中`public int f;`这个值来构建的，而 Dijkstra 算法是用 dist 值构建的
2. A* 算法除了需要更新 dist 值，还需要更新 f 值。
3. A* 算法循环结束的条件是只要遍历到终点即退出循环，因此 A* 算法最终并不一定能得到最短路径。

```java
public void AStar(Graph g, int s, int t) { // 从顶点 s 到顶点 t 的路径
    int[] predecessor = new int[this.v]; // 用来还原路径
    
    // 按照 vertex 的 f 值构建的小顶堆，而不是按照 dist
    PriorityQueue queue = new PriorityQueue(this.v);
    
    boolean[] inqueue = new boolean[this.v]; // 标记是否已经走过
    
    // 起始节点初始化
    vertexes[s].dist = 0;
    vertexes[s].f = 0;
    queue.add(vertexes[s]);
    inqueue[s] = true;
    
    while (!queue.isEmpty()) {
        Vertex minVertex = queue.poll(); // 取堆顶元素并删除
        for (int i=0; i<g.adj[minVertex.id].size(); i++) {
            Edge e = g.adj[minVertex.id].get(i); // 取出一条 minVetex 相连的边
            Vertex nextVertex = vertexes[e.tid]; // 下一个节点
            if (minVertex.dist + e.w < nextVertex.dist) { // 更新 next 的 dist,f
            
                nextVertex.dist = minVertex.dist + e.w;// 更新 dist
                nextVertex.f = nextVertex.dist+hManhattan(nextVertex, vertexes[t]);// 更新 f 值
                
                // 记录路径
                predecessor[nextVertex.id] = minVertex.id;
                
                //更新队列中的节点信息
                if (inqueue[nextVertex.id] == true) {
                    queue.update(nextVertex);
                } else {
                    queue.add(nextVertex);
                    inqueue[nextVertex.id] = true;
                }
            }
            if (nextVertex.id == t) { // 只要到达终点就退出循环
                queue.clear(); // 清空队列，否则无法退出 while 循环
                break; 
            }
        }
    }
    // 输出路径
    System.out.print(s);
    print(s, t, predecessor);
}

/**
 * 计算曼哈顿距离
 *
 */
int hManhattan(Vertex v1, Vertex v2) {
  return Math.abs(v1.x - v2.x) + Math.abs(v1.y - v2.y);
}

private void print(int s, int t, int[] pre) {
    if(s == t) return;
    print(s, pre[t], pre);
    System.out.print("->"+t);
}
```

## A* 算法实践

在了解 A* 算法的原理后，我们可以尝试着解决游戏中寻路的问题，游戏中的地图，通路弯弯曲曲，障碍物不规则，似乎无法抽象成点与线，这时我们可以将地图分割成一个个极小的方块，从起点开始向其他三个方向走，当然，障碍物所在的方块不能走。方块之间的间距为 1。

这样我们就可以将一个游戏地图抽象成边权值为 1 的有向图，就可以使用 A* 算法来寻路了。

## 拓展

A* 算法属于**启发式搜索算法（Heuristically Search Algorithm）**的一种，其他属于启发式搜索的算法还有：IDA* 算法、蚁群算法、遗传算法、模拟退火算法等等。