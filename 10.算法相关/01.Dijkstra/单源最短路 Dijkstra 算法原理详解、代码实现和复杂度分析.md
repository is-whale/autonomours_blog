- [单源最短路 Dijkstra 算法原理详解、代码实现和复杂度分析_Alan-zzx的博客-CSDN博客_dijkstra算法时间复杂度](https://blog.csdn.net/m0_37264516/article/details/86766513)

## 图的最短路问题

图 (Graph) 是用途最广泛的[数据结构](https://so.csdn.net/so/search?q=数据结构&spm=1001.2101.3001.7020)之一，类似 Google 地图、百度地图等地图软件中寻找两地之间的路径，就需要将地图抽象成一张图，从图中计算得到合适的路径。

图分为很多种，例如：无向图，有向图，有向加权图等等，最简单的搜索算法深度优先和[广度优先搜索](https://so.csdn.net/so/search?q=广度优先搜索&spm=1001.2101.3001.7020)都是用于无向图或有向图查找路径的，而针对加权的图的最短路，这两个方法就不管用了。

最短路的算法有很多种，我们今天要了解的是 Dijkstra 算法，该算法是由荷兰科学家在 1959 年提出的，是一个求单源最短路的算法，也就是有向图中从一个点到任意点的[最短路径](https://so.csdn.net/so/search?q=最短路径&spm=1001.2101.3001.7020)。

## Dijkstra 算法核心思想

在一个有向加权图中，求一个点到其他任意点的最短路径，首先需要有一个数组来表示当前点到其他点的最短路径长度。
![vertexes数组](https://img-blog.csdnimg.cn/20190204111319679.?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3MjY0NTE2,size_16,color_FFFFFF,t_70)

核心思路是从顶点 A 往外延伸，不断更新 A 到其他点的距离，我们称这个操作为**松弛**。

## [Dijkstra算法](https://so.csdn.net/so/search?q=Dijkstra算法&spm=1001.2101.3001.7020)实践详解

例如我们现在有如下有向加权图：

![示例图](https://img-blog.csdnimg.cn/20190205190816530.)

计算点 A 到其他顶点的最短距离。

同样的，我们创建 vertexes 数组来临时保存点 A 到其他顶点的最短路距离。在算法计算过程中，从点 A 开始往其他顶点扩散遍历。同时以当前遍历到的边结合 vertexes 数组中的值，不断更新 vertexes 数组中的值。

首先我们以邻接表来表示这个图：

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

    private class Vertex {// 用于算法实现中，记录第一个节点到这个节点的距离
        public int id;
        public int dist;
        public Vertex(int id; int dist) {
            this.id = id;
            this.dist = dist;
        }
    }


    private LinkedList<Edge> adj[];// 邻接表
    private int v;// 顶点数
    
    public Graph(int v) {
        this.v = v;
        this.adj = new LinkedList[v];
        for(int i; i<v; i++) {
            this.adj[i] = new LinkedList<Edge>();
        }
    }
    
    public void addEdge(int s, int t, int w) {
        this.adj[s].add(new Edge(s, t, w));
    }
}
```

简单的说明一下这个类，它可以用来表示一张图，类中分别有两个成员变量表示邻接表和顶点数，其中，Edge 类表示一条边，结合邻接表可以将图的信息记录，Vertex 类则是记录起始节点到当前节点的最短距离。

在算法实现过程中，我们会使用一个优先队列，将离起始节点最近的节点优先出队，利用这个节点能到达的节点的距离与当前记录在 vertexes 数组中的最短距离比较，进行更新最短距离（松弛）。文字说的不太清楚，结合代码会更容易理解。

注意：由于 Java 类库中没有提供可更新节点信息的优先队列，因此我们需要手动实现一个优先队列，思路是使用一个小顶堆来实现。

优先队列代码：

```java
// 一个可更新数据的优先队列，即小顶堆，根据dist构建
private class PriorityQueue {
    private Vertex[] nodes;// 优先队列中的数组
    private int count;// 队列中的节点个数

    public PriorityQueue(int v) {
        this.nodes = new Vertex[v + 1];
        this.count = 0;
    }

    /**
     * 出队
     * 
     * @return
     */
    public Vertex poll() {
        Vertex v = this.nodes[1];
        this.nodes[1] = this.nodes[count--];
        heapify();// 堆化
        return v;
    }

    /**
     * 入队
     * 
     * @param vertex
     */
    public void add(Vertex vertex) {
        nodes[++this.count] = vertex;
        int i = this.count;
        while(i/2 > 0 && nodes[i/2].dist > nodes[i].dist) {
            swap(i/2, i);
            i = i/2;
        }
    }

    /**
     * 更新队列中某个节点
     * 
     * @param vertex
     */
    public void update(Vertex vertex) {
        for (int i = 1; i < count; i++) {
            if (nodes[i].id == vertex.id) {
                nodes[i] = vertex;
            }
        }
    }

    /**
     * 判空
     * 
     * @return
     */
    public boolean isEmpty() {
        if (this.count != 0) {
            return false;
        } else {
            return true;
        }
    }

    // 堆化
    private void heapify() {
        int minPos = 1;
        int temp = 1;
        while (true) {
            int left = temp * 2;
            int right = temp * 2 + 1;
            if (left <= count && nodes[left].dist < nodes[temp].dist) {
                minPos = left;
            }
            if (right <= count && nodes[right].dist < nodes[minPos].dist) {
                minPos = right;
            }
            if (minPos == temp) {
                break;
            }
            swap(temp, minPos);
            temp = minPos;
        }
    }

    // 交换两个值
    private void swap(int temp, int minPos) {
        Vertex t = nodes[temp];
        nodes[temp] = nodes[minPos];
        nodes[minPos] = t;
    }
}
```

现在，我们已经把所有的准备工作都做完了，剩下的只需要利用当前的条件来实现 Dijkstra 算法。

![示例图](https://img-blog.csdnimg.cn/20190205190816530.)

从上图中顶点 1 开始遍历，顶点 1 可以连通到顶点 2 和 3，将顶点 2 和 3 入队，并同时在 vertexes 数组更新到达顶点 2 和 3 的距离。

接着出队为顶点 2，顶点 2 能连通的是顶点 3 和 4，此时，我们可以得到顶点 1 到 3 的距离是 1+9=10，而 vertexes 数组中记录的是 12，因此需要更新 vertexes 数组和队列中节点 3 的 dist 值。

```java
/**
 * 计算从顶点 s 到 t 的最短路
 * @param s
 * @param t
 */
public void dijkstra(int s, int t) {
    int[] pre = new int[this.v+1];// 用于还原最短路的路径
    Vertex[] vertexes = new Vertex[this.v+1];
    for(int i=0; i<this.v; i++) {//初始化 vertexes 数组
        vertexes[i] = new Vertex(i, Integer.MAX_VALUE);
    }
    
    PriorityQueue queue = new PriorityQueue(this.v+1);//小顶堆
    boolean[] inqueue = new boolean[this.v+1];//标记节点是否已经入队
    
    vertexes[s].dist = 0;
    queue.add(vertexes[s]);
    inqueue[s] = true;
    
    while(!queue.isEmpty()) {
        Vertex minVertex = queue.poll();
        if(minVertex.id == t){
            break;
        }
        
        for(int i=0; i<this.adj[minVertex.id].size(); i++){//遍历节点连通的其他节点
            Edge e = this.adj[minVertex.id].get(i);//取出节点 minVertex.id 相连的节点
            Vertex nextVertex = vertexes[e.tid];//初始节点到下一个节点的距离
            if(minVertex.dist + e.w < nextVertex.dist) {//上一步计算得到的距离和当前计算出的距离比较
                nextVertex.dist = minVertex.dist + e.w;//更新距离
                pre[nextVertex.id] = minVertex.id;//记录路径
                if(inqueue[nextVertex.id]){//若节点已经在队列中，则更新值，若不在则入队
                    queue.update(nextVertex);
                } else {
                    queue.add(nextVertex);
                    inqueue[nextVertex.id] = true;
                }
            }
        }
    }
    System.out.print(s);
    print(s, t, pre);
}

private void print(int s, int t, int[] pre) {
    if(s == t) return;
    print(s, pre[t], pre);
    System.out.print("->"+t);
}
```

测试代码：

```java
public static void main(String[] args) {
    Graph g = new Graph(6);
    g.addEdge(1, 2, 1);
    g.addEdge(1, 3, 12);
    g.addEdge(2, 4, 3);
    g.addEdge(2, 3, 9);
    g.addEdge(4, 3, 4);
    g.addEdge(4, 5, 13);
    g.addEdge(3, 5, 5);
    g.addEdge(5, 6, 4);
    g.addEdge(4, 6, 15);
    g.dijkstra(1, 6);
}
/*output:
1->2->4->3->5->6
*/
```

## Dijkstra 算法复杂度

Dijkstra 算法的核心逻辑已经讲完了，我们现在来考虑一下算法的复杂度情况。

在核心代码部分，最复杂的是 while 循环和 for 循环嵌套的部分，while 循环最多循环 v 次（v 为顶点个数），for 循环执行次数与边的数目有关，假设顶点数 v 的最大边数是 e。

for 循环中往优先队列中添加删除数据的复杂度为`O(log v)`。

综合上述两部分，最终 Dijkstra 算法的[时间复杂度](https://so.csdn.net/so/search?q=时间复杂度&spm=1001.2101.3001.7020)是`O(e·logv)`

## 拓展

在实际场景中，如：地图中，我们要查找两点之间的路径，可以使用 Dijkstra 算法计算得到，但是如果两点之间特别远，岔路口、道路特别多，此时再使用 Dijkstra 会非常耗时，那么有什么优化的方案呢？

**实际开发中，有个原则，就是需要对解决方案进行权衡取舍，也就是没有必要得出最优解，为了兼顾效率，得到一个可行的次优解也是 OK 的。**

1. 若地图很大，在查找两点之间的距离时，可以不用遍历所有的路径，先划分出一个区域，包括了两点，再从这个区域中查找。
2. 两点距离非常远时，第一点就不可用了，例如，我们要找出北京到上海的路径，首先规划大的出行路线，比如通过那几条告诉可以大致从北京到达上海，接着再分别从北京和上海两个区域查找路线。