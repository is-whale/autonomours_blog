- [基础路径规划算法(Dijikstra、A*、D*)总结 - 林学徒 - 博客园 (cnblogs.com)](https://www.cnblogs.com/MyStringIsNotNull/p/16273574.html)

## 引言

在一张固定地图上选择一条路径，当存在多条可选的路径之时，需要选择代价最小的那条路径。我们称这类问题为最短路径的选择问题。解决这个问题最经典的算法为Dijikstra算法，其通过贪心选择的步骤从源点出发逐步逼近目标点，从而得到起始点与目标点的最短路径。A*算法是在Dijikstra算法上做了改进，使其能够在 **开阔空间(也就是四通八达或具有少量障碍物的方格路，可以近似看成各边权重均相等的完全图)** 上具有比Dijikstra算法有更好的搜索效率。 但Dijikstra算法和A*算法无法很好的适用动态路网的情况，D*算法却能够很好的适应。Dijikstra算法和A*算法在动态路网中进行重新规划所花费的代价比D*算法高，且效率比D*的低。

***ps:*** 动态路网是指在沿着最优路径前进时，突然出现既定路径中断(地图上的路径变更等)需要进行重新规划的情况。

## Dijikstra算法

Dijikstra算法是单向最短路径的查找算法，其要求对应的图必须无负权。其适用有向图和无向图的情况(一般会将无向图的每条边看成两条反向的有向边)。Dijikstra算法是一个贪心算法，每一步都是基于贪心选择策略来进行。

**算法描述：** 对于具有V个顶点和U条边的图 E=(U,V),算法会将图的顶点V分为两部分，一部分是已遍历过且已找到从源点source到目标点的最短路径的节点集合S。一部分是未遍历过的节点集合K。每次遍历过程都会从未找到最短路径的节点集合K中取出距离源点路径最短的节点n。然后遍历该节点的连通节点i，若该节点的连通节点I不存在于集合S中，且 dist[source][i] > dist[source][n] + graph[n][i]; 则更新dist[source][i] = dist[source][n] + graph[n][i]，并标注节点i最短路径中，其上一个节点为n，即path[i] = n，且将节点n从集合K中删除，并将其放入集合S中。然后进入下一次循环，直至将目标节点放入集合S中，或遍历了图中的全部节点。

```java
function dijikstra(graph,source,target):
    initDist(dist,graph.nodeSize,graph.nodeSize,Integer.MAX_VALUE);
    dist[source][source] <- 0;
    initPath(path,pathNodeSelfIndex);

    queue.push(source);

    while(!queue.isEmpty()):
        // 得到队列中当前dist值最小的节点
        node <- queue.pop(sorted by dist[source][obj]);

        S.push(node);
        if node == target:
            break;

        for neighborNode in node:
            if !S.contains(neighborNode) and dist[source][neighborNode] > dist[source][node] + graph[node][neighborNode]:
                dist[source][neighborNode] = dist[source][node] + graph[node][neighborNode];
                path[neighborNode] = node;
                queue.push(new SearchNode(neighborNode,dist[source][neighborNode]));
    
    return path,dist;
```

**实现:**

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.util.*;

/**
 * @author 学徒
 */
public class Dijikstra {

    /**
     * 图最短路径查找结果
     */
    @AllArgsConstructor
    @NoArgsConstructor
    @Data
    public class SearchResult {
        /**
         * 最短路径查找的路径大小结果存储
         */
        private int[][] dist;

        /**
         * 最短路径查找的路径节点过程结果存储
         */
        private int[] path;

        /**
         * 源节点
         */
        private int source;

        /**
         * 目标节点
         */
        private int target;

        /**
         * 打印出最短路径查找结果
         */
        public void print() {
            System.out.print("路径查找:" + source + "->" + target + ":");
            int temp = target;
            while (path[temp] != temp) {
                System.out.print(temp + "<-");
                temp = path[temp];
            }
            System.out.println(temp);
            System.out.println("路径长度:" + (Objects.equals(dist[source][target], Integer.MAX_VALUE) ? "无限长，节点不可达" : dist[source][target]));
        }
    }


    /**
     * 实现dijikstra算法
     *
     * @param graph  由领接矩阵表示的图，值表示边权重
     * @param source 查找的起始点
     * @param target 查找的目标点
     * @return 查询的结果
     */
    public SearchResult search(int[][] graph, int source, int target) {
        if (Objects.isNull(graph) || graph.length == 0 || graph[0].length != graph.length) {
            throw new IllegalArgumentException("图存在节点数目异常问题");
        }

        if (source < 0 || target < 0 || source > graph.length - 1 || target > graph.length - 1) {
            throw new IllegalArgumentException("查找点参数存在异常");
        }

        // 记录两点之间路径的最短距离
        int[][] dist = new int[graph.length][graph.length];
        for (int[] temp : dist) {
            Arrays.fill(temp, Integer.MAX_VALUE);
        }
        dist[source][source] = 0;

        // 用于暂时存储最短路径节点编号，且维护当前最短距离的最小值节点编号
        Queue<Integer> queue = new PriorityQueue<>(Comparator.comparing((obj) -> dist[source][obj]));
        // 用于存储已经查找到最短路径的节点的编号
        Set<Integer> set = new HashSet<>();
        // 用于记录最短路径中某个节点的上一个节点编号
        int[] path = new int[graph.length];
        for (int i = 0; i < graph.length; i++) {
            path[i] = i;
        }

        queue.add(source);

        while (!queue.isEmpty()) {
            Integer node = queue.poll();

            set.add(node);
            // 查找到目标节点时，
            if (node == target) {
                break;
            }

            for (int neighborNode : getNeighborNodes(graph, node)) {
                int distance = dist[source][node] + graph[node][neighborNode];
                if (set.contains(neighborNode) || dist[source][neighborNode] < distance) {
                    continue;
                }

                dist[source][neighborNode] = distance;
                path[neighborNode] = node;
                queue.add(neighborNode);

            }

        }

        return new SearchResult(dist, path, source, target);
    }

    /**
     * 用于得到某个图中某个节点的连通节点的节点编号
     *
     * @param nodeNumber 要查找连通节点的编号
     * @return 连通节点的列表
     */
    private List<Integer> getNeighborNodes(int[][] graph, int nodeNumber) {
        if (Objects.isNull(graph) || nodeNumber > graph.length - 1 || nodeNumber < 0) {
            return new ArrayList<>();
        }
        List<Integer> result = new LinkedList<>();
        for (int temp = 0; temp < graph.length; temp++) {
            if (Objects.equals(graph[nodeNumber][temp], Integer.MAX_VALUE) || Objects.equals(nodeNumber, temp)) {
                continue;
            }
            result.add(temp);
        }
        return result;
    }
}
```

**分析：** Dijikstra算法的时间复杂度为O(n2n2)，空间复杂度为O(n2n2)。其中n为图中节点的数目。

**总结：** Dijikstra算法能够很好的实现起始点到目标点之间最短距离的搜索。但是其要求图中不能有负权边。且当图为开放空间，甚至是各边权重均相等的条件下，效率会较差。

## A*算法

Dijikstra算法在开阔空间中查找源点到目标点的最短路径之时，其执行过程类似于以源点为中心，源点到目标点的距离为半径，进行广度搜索的状态。也就是从源点一层一层的向外扩的方式进行查找，直至找到目标点停止。出现这个问题的**根本原因在于每次从未遍历过的节点集合K中取出要进行访问的节点时，只考虑距离源节点最近的节点，且与源节点之间距离最短的节点数目较多，没有考虑取出的节点与目标节点之间的距离的问题。** 而使得每次取出的节点都无法很好的逼近目标节点，只是“盲目”的在图中向目标节点搜索。A*算法在Dijikstra算法的基础上进行改进，其引入了一个评估函数。f(x) = g(x) + h(x)。**其中g(x)是从起点到当前节点x的实际距离量度，也就是在Dijikstra算法中，源点到当前节点的最短距离dist[source][x]。h(x)是从节点x到终点的最小距离估计，h(x)可以从欧几里得距离或者曼哈顿距离中选取，也可以是其它的距离度量。** ，因此我们可知，f(x)综合考虑了节点x与源节点以及目标节点之间的距离关系。A*算法相较于Dijikstra算法，在其它步骤上都没有任何变化，只是**在每次从未访问过的节点集合K中取出节点进行访问时，判断的依据从原先的与源节点距离最短的节点变为f(x)中值最小的那个节点。**

```java
function AStar(graph,source,target):
    initDist(dist,graph.nodeSize,graph.nodeSize,Integer.MAX_VALUE);
    dist[source][source] <- 0;

    queue.push(new SearchNode(source));

    while(!queue.isEmpty()):
        // 得到队列中当前dist值最小的节点
        node <- queue.pop(sorted by obj.f(target,dist[source][obj]));

        S.push(node.nodeNumber);
        if node.nodeNumber == target:
            break;

        for neighborNode in getNeighbors(node.nodeNumber):
            if !S.contains(neighborNode) and dist[source][neighborNode] > dist[source][node.nodeNumber] + graph[node.nodeNumber][neighborNode]:
                dist[source][neighborNode] = dist[source][node.nodeNumber] + graph[node.nodeNumber][neighborNode];
                path[neighborNode] = node.nodeNumber;
                queue.push(new SearchNode(neighborNode));
    
    return path,dist;

struct SearchNode{
    int nodeNumber;

    function f(target,distance);
}
```

**实现:**

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.util.*;

/**
 * @author 学徒
 */
public class AStar {

    /**
     * 用于辅助实现最短路径的查找节点
     */
    @AllArgsConstructor
    @NoArgsConstructor
    @Data
    private class SearchNode {
        /**
         * 节点编号
         */
        private int nodeNumber;

        /**
         * 用于得到评估函数的值
         *
         * @param distance         从源节点到当前节点的最短距离
         * @param targetNodeNumber 目标节点编号
         * @return 评估函数的值
         */
        public int f(int distance, int targetNodeNumber) {
            int gx = distance;
            // 这里评估与目标节点的距离为编号差
            int hx = Math.abs(nodeNumber - targetNodeNumber);
            return gx + hx;
        }


    }

    /**
     * 图最短路径查找结果
     */
    @AllArgsConstructor
    @NoArgsConstructor
    @Data
    public class SearchResult {
        /**
         * 最短路径查找的路径大小结果存储
         */
        private int[][] dist;

        /**
         * 最短路径查找的路径节点过程结果存储
         */
        private int[] path;

        /**
         * 源节点
         */
        private int source;

        /**
         * 目标节点
         */
        private int target;

        /**
         * 打印出最短路径查找结果
         */
        public void print() {
            System.out.print("路径查找:" + source + "->" + target + ":");
            int temp = target;
            while (path[temp] != temp) {
                System.out.print(temp + "<-");
                temp = path[temp];
            }
            System.out.println(temp);
            System.out.println("路径长度:" + (Objects.equals(dist[source][target], Integer.MAX_VALUE) ? "无限长，节点不可达" : dist[source][target]));
        }
    }


    /**
     * 实现dijikstra算法
     *
     * @param graph  由领接矩阵表示的图，值表示边权重
     * @param source 查找的起始点
     * @param target 查找的目标点
     * @return 查询的结果
     */
    public SearchResult search(int[][] graph, int source, int target) {
        if (Objects.isNull(graph) || graph.length == 0 || graph[0].length != graph.length) {
            throw new IllegalArgumentException("图存在节点数目异常问题");
        }

        if (source < 0 || target < 0 || source > graph.length - 1 || target > graph.length - 1) {
            throw new IllegalArgumentException("查找点参数存在异常");
        }

        // 记录两点之间路径的最短距离
        int[][] dist = new int[graph.length][graph.length];
        for (int[] temp : dist) {
            Arrays.fill(temp, Integer.MAX_VALUE);
        }
        dist[source][source] = 0;

        // 用于暂时存储最短路径节点，且维护当前f(x)的最小值节点
        Queue<SearchNode> queue = new PriorityQueue<>(Comparator.comparing((obj) -> obj.f(dist[source][obj.nodeNumber], target)));
        // 用于存储已经查找到最短路径的节点的编号
        Set<Integer> set = new HashSet<>();
        // 用于记录最短路径中某个节点的上一个节点编号
        int[] path = new int[graph.length];
        for (int i = 0; i < graph.length; i++) {
            path[i] = i;
        }

        queue.add(new SearchNode(source));

        while (!queue.isEmpty()) {
            SearchNode node = queue.poll();

            set.add(node.nodeNumber);
            // 查找到目标节点时，
            if (node.nodeNumber == target) {
                break;
            }

            for (int neighborNode : getNeighborNodes(graph, node.nodeNumber)) {
                int distance = dist[source][node.nodeNumber] + graph[node.nodeNumber][neighborNode];
                if (set.contains(neighborNode) || dist[source][neighborNode] < distance) {
                    continue;
                }

                dist[source][neighborNode] = distance;
                path[neighborNode] = node.nodeNumber;
                queue.add(new SearchNode(neighborNode));

            }

        }

        return new SearchResult(dist, path, source, target);
    }

    /**
     * 用于得到某个图中某个节点的连通节点的节点编号
     *
     * @param nodeNumber 要查找连通节点的编号
     * @return 连通节点的列表
     */
    private List<Integer> getNeighborNodes(int[][] graph, int nodeNumber) {
        if (Objects.isNull(graph) || nodeNumber > graph.length - 1 || nodeNumber < 0) {
            return new ArrayList<>();
        }
        List<Integer> result = new LinkedList<>();
        for (int temp = 0; temp < graph.length; temp++) {
            if (Objects.equals(graph[nodeNumber][temp], Integer.MAX_VALUE) || Objects.equals(nodeNumber, temp)) {
                continue;
            }
            result.add(temp);
        }
        return result;
    }
}
```

**分析：** A*算法不一定能够得到最优解。当h*(x)表示节点x到达目标节点的真实代价。那么存在如下四种情况：

1. 如果h(x) = 0,这种情况下，A*算法变为了Dijikstra算法
2. 如果h(x) < h*(x),这种情况下，搜索的点数多，搜索范围大，效率低。但能得到最优解
3. 如果h(x) = h*(x)，此时的搜索效率是最高的。
4. 如果h(x) > h*(x)，搜索的点数少，搜索范围小，效率高，但不能保证得到最优解。

**总结：** A*算法是一种启发式搜索算法，h(x)作为搜索代价的启发函数，其影响着搜索的精确度和效率。对于A*算法，其能够适应静态路网的情况，而对于动态路网的情况，其不能很好的适应。

## D*算法

D*算法是一种增量式的路径搜索算法，**适合面对周围环境未知或者周围环境存在动态变化的场景，但也能兼容静态环境**，与A*算法不同的是，A*算法从起点向目标点进行搜索，而D*算法是从目标点向起始点进行反向搜索。

***ps:*** 启发式搜索是利用启发函数来对搜索进行指导，从而实现高效的搜索。增量搜索是对以前的搜索结果信息进行再利用来实现高效搜索，大大减少搜索范围和时间。D*算法采用反向搜索的目的在于后期需要重新规划路径的时候，能够用到先前搜索到的最短路径信息，减少搜索量。因为以目标向起始点进行搜索得到的最短路径图，是以目标点为中心辐射出的最短路径图，图上目标点到各点之间都是最短路径，为此其在既定路径上遇到问题需要重新路径规划的时候，可以很好的利用原先得到的信息。而以起始点向目标点搜索得到的最短路径图，其是以起始点为中心辐射出的最短路径图，当沿着既定路径前行遇到障碍物之后，需要重新进行路径规划之时，没有办法很好的利用原先搜索得到的信息。

**算法描述：**

***D\*算法有几个重要的概念：***

1. G：表示进行路径搜索的目标点
2. c(x,y)：从节点x移动到节点y的代价
3. t(x)：节点的状态。每个节点(作者论文中称为state)都有一个状态，其中总共有三种可能NEW,OPEN,CLOSED。NEW表示从未加入到openList中的，也就是从未被遍历查找过的。OPEN表示节点在被查找中，节点在openList中。CLOSED表示从openList中被移出。OPEN和CLOSED状态会相互转化，当节点从openList中移出的时候，状态从OPEN变为CLOSED，当节点再次加入openList，进行 **降低节点自身h值或者传播当前节点的h值变更信息给邻居节点，让邻居节点进行h值修改变更** 操作时，状态从CLOSED变为OPEN
4. h(x)：表示地图上的点x到达目标点G的代价。由于D*算法是从目标点开始进行路径规划的，为此，初始化的时候，令h(G) = 0。此后，其代价的变动可能会在两个地方出现，一个是路径搜索的过程中，当节点x的邻居节点被执行搜索过程的时候，如果其能够让h(x)的代价更小，则更新h(x) = h(y) + cost(y,x)。一个是在路径搜索完成后执行路径搜索结果的过程中遇到障碍物之时，通过insert(x,y,val)函数改变障碍物节点y的代价h(y)
5. k(x)：可以理解为节点最小的h(x)值。随着节点遍历和搜索过程的进行，节点的h(x)值会不断的变动
6. b(x) = y：用于记录当前节点x的父节点，这里b(x) = y表示x节点的父节点为y节点。在搜索完成之后，能够根据b(x)的值，回溯追踪到目标节点
7. openList：存放节点状态为OPEN的节点。且其按照节点的k值从小到大进行排序

***D\*算法有三个重要的函数：***

1. process_state()：该函数是用来降低openlist表中的某个节点x(state)的h(x)值或者传播节点x的h(x)值变更信息给邻居节点，让邻居节点进行相应变更的同时进行路径搜索查找的
2. insert(x,val)：该函数是用来修改节点x的状态以及h(x)值和k(x)值的
3. modify_cost(x,y,val)：该函数是用来修改节点x和y之间的移动代价cost(x,y)，而且根据节点y的状态t(y)的情况，可能对节点y的h(y)值和状态t(y)进行修改的

***D\*算法的执行过程：***

1. 在算法搜索路径的起始阶段，其操作过程类似于Dijikstra算法，但是其是从目标点往起始点进行搜索的过程。首先将t(G) = OPEN，h(G) = 0，k(G) = 0。并将点G放入openList中，同时将其余各点的t(x) = NEW,k(x) = MAX_VALUE,h(x) = MAX_VALUE（实际上，当t(x)值为NEW时，h(x)和k(x)的值并不重要，但为了统一process_state函数的描述，这里用了MAX_VALUE表示最大值）。之后反复运行process_state()函数，用于搜索路径，直到起始点x的状态变为CLOSED或openList中已经没有节点。路径搜索结束，当存在路径时，可以根据b(x)不断回溯上一个节点，直到目标点
2. 搜索了最短路径之后，在根据b(x)进行路径执行的过程中，若当前点为x，且探测到要走的下一个节点y存在障碍，或者节点y本来存在障碍物，但是继续行进到x的时候，障碍物被移除。这时就要调用modify_cost(x,y,val)函数，将最新的cost(x,y)以及h(y)的值进行变更，并反复执行process_state()函数用于传播节点h值变更信息和路径搜索直到process_state()函数返回的openList中所有节点的k值中的最小k值kminkmin >= h(x) 或者openList没有任何节点时，表示重新路径规划完成，或者无法找到别的路径从x点规划到目标点G

***D\*算法的几个说明：***

1. 为何有了h值还需要k值：在静态环境下，按照h值进行排序然后执行算法是可以找到一条全局最优路径的(其相当于Dijkstra算法)。但是在动态环境下，假如某个节点变成了障碍物，变得不可达，此时其h值会被修改为∞∞，这时将该节点假如openList中进行重新规划时，该节点会被置于openList中的最后。也就是说，此时路径规划会从openList中剩余的处于OPEN状态的节点开始，一直扩张至全图都没有不可达节点之后，才会访问该节点。这显然并不合理，因为我们的目的就是要在节点状态动态变化的时候减少搜索的空间，提高搜索效率。而用最小的h值也就是k值在openList中进行排序，表示这里曾有一条捷径，那么就会优先在这附近进行搜索。
2. 为何重新进行路径规划的时候，当执行到 kminkmin >= h(y) 时停止：因为process_state()函数的功能是用邻居节点来减低自身节点的h值的，当所有处于OPEN状态的节点中的k值的最小值 kminkmin 也要大于等于h(y)时，表示不可能再通过process_state()函数的执行来降低h(y)值了，那么自然就没有再搜索的必要，且已经完成了路径的修正了。

**相关伪代码如下：**

```pseudocode
function process_state():
    x = openList.poll(sortedBy k);
    if(x == NULL):
        return -1;
    x.state = CLOSED;
    
    // 当h值大于k值时，表示当前该节点处于h值被修改为较大的状态(raise状态)
    // 为此查找邻居节点来得到减低自身的h值
    if(x.k < x.h):
        for each neighbor y of x:
            if(y.h < x.k and x.h > y.h + c(y,x)):
                x.b = y;
                x.h = y.h + c(y,x);

    // 该过程类似于dijikstra，用来传播信息当前节点h值变化的信息和降低邻居节点的h值
    if(x.k == x.h):
        for each neighbor y of x:
            if(y.state == NEW or 
            (y.b == x and y.h != x.h + c(x,y)) or
            (y.b != x and y.h > x.h + c(x,y))):
                y.b = x;
                insert(y,x.h + c(x,y))
    // k值和h值不相同，表示节点处于调整状态
    else:
        for each neighbor y of x:
            // 传播信息当前节点h值变化的信息和降低邻居节点的h值
            if(y.state == NEW or
            (y.b == x and y.h != x.h + c(x,y))):
                y.b = x;
                insert(y,x.h + c(x,y));
            else:
                // 邻居节点存在更短的路径
                // 调整当前节点并重新传播当前节点的h值变化信息给周围节点
                if(y.b != x and y.h > x.h + c(x,y)):
                    insert(x,x.h);
                // 传播邻居节点的信息，使其可以影响当前节点进而修改当前节点的h值和路径信息、
                // 因为这里存在比当前节点的h值更低的值
                else:
                    if(y.b != x and x.h > y.h + c(y,x) and
                    y.state == CLOSED and y.h > x.k):
                    insert(y,y.h);

    return openList.peek().k;

function insert(x,val):
    if(x.state == NEW):
        x.h = val;
        x.k = val;
        x.state = OPEN;
        openList.add(x);
    else if(x.state == OPEN):
        openList.remove(x);
        x.k = Math.min(x.k,val);
        openList.add(x);
    else if(x.state == CLOSED):
        x.k = Math.min(x.h,val);
        x.h = val;
        x.state = OPEN;
        openList.add(x);


function modify_cost(x,y,val):
    c(x,y) = val;
    if(y.state == CLOSED):
        insert(y,val);
    
    return openList.peek().k;

function replan(y):
    while True:
        if(result = process_state() >= y.h || result == -1){
            break;
        }
    // 返回值大于-1表示调整好路径，等于-1 表示没有路径可以从当前点到达目标点
    return result;

function run(start,end):
    init(graph);
    insert(end,0);
    //进行路径规划
    while True:
        min_k = process_state();
        if(start.state == CLOSED || min_k == -1):
            break;
    // 不能找到从起始点到目标点的最短路径
    if(min_k == -1):
        return;
    
    current = start.b;
    while True:
        //执行查找到的路径，直到上层节点无法通过，或者到达目标节点
        while (current != end and current.b.state == CLOSED):
            current = current.b;
            // 执行移动操作
            gogogo(current);
        
        if(current == end):
            // 将其移动到终点,结束路径执行过程
            gogogo(current);
            break;

        // 当前节点节点的上层节点不能移动了，因为某种原因(可能是障碍物)被人修改了执行代价,也就是被调用过该节点的modify_cost(current,current.b,newH)函数
        min_k = replan(current);
        // 重新规划路径后，无法找到一条有效的路
        if(min_k == -1):
            return;
```

实现

```java
import lombok.Data;
import lombok.extern.slf4j.Slf4j;

import java.util.*;

/**
 * @author 学徒
 */
@Slf4j
public class DGraph {

    /**
     * 用于映射节点标识和节点对象
     */
    private Map<Integer, DNode> nodeMap;

    /**
     * 用于映射两个节点之间的移动代价
     */
    private Map<String, Integer> costMap;

    /**
     * 用于映射节点的邻居节点
     */
    private Map<DNode, Set<DNode>> neighborsMap;


    public DGraph(int[][] graph) {
        if (Objects.isNull(graph) || graph.length == 0 || graph.length != graph[0].length) {
            throw new IllegalArgumentException("图参数出错");
        }
        neighborsMap = new HashMap<>(graph.length);
        nodeMap = new HashMap<>(graph.length);
        costMap = new HashMap<>(graph.length);
        init(graph);
    }

    /**
     * 用于完成图的初始化工作
     *
     * @param graph 图模型的邻接矩阵
     */
    private void init(int[][] graph) {
        final Integer UNGO = Integer.MAX_VALUE;
        // 用于初始化所有节点
        for (int i = 0; i < graph.length; i++) {
            nodeMap.put(i, new DNode(i));
        }
        // 用于初始化邻接节点列表和节点间的移动代价
        for (int i = 0; i < graph.length; i++) {
            for (int j = 0; j < graph[i].length; j++) {
                if (Objects.equals(UNGO, graph[i][j]) || Objects.equals(i, j)) {
                    continue;
                }
                DNode x = nodeMap.get(i);
                DNode y = nodeMap.get(j);
                // 此处调转y与x之间的位置是因为有向图中是从x指向y的，但DStar算法是从目标点向起始点进行规划的，为此需要调转方向
                costMap.put(generateCostKey(y, x), graph[i][j]);
                neighborsMap.computeIfAbsent(y, obj -> new HashSet<>()).add(x);

                //设置一个反方向的边，将其值代价设置得很大。目的是为了兼容有向图中某些节点之间没有双向边,只有单向边，
                //在有向图只有单向边的情况下，会得不到最优解
                neighborsMap.computeIfAbsent(x,obj -> new HashSet<>()).add(y);
            }
        }
    }

    /**
     * 图上的节点
     */
    @Data
    public class DNode {
        /**
         * 用于标识该节点
         */
        private int index;

        /**
         * 节点的h值
         */
        private long h;

        /**
         * 节点的k值
         */
        private long k;

        /**
         * 最短路径的回调节点
         */
        private DNode b;

        /**
         * 节点的状态
         */
        private STATE state;

        public DNode(int index) {
            this(index, STATE.NEW);
        }

        public DNode(int index, STATE state) {
            this.index = index;
            this.state = state;
        }

        @Override
        public int hashCode() {
            return this.index;
        }

        @Override
        public boolean equals(Object obj) {
            if (obj == this) {
                return true;
            }
            if (obj instanceof DNode) {
                DNode node = (DNode) obj;
                return node.index == this.index;
            }
            return false;
        }

    }


    /**
     * 节点的状态枚举值
     */
    public enum STATE {
        NEW,
        OPEN,
        CLOSED;
    }

    /**
     * 通过节点的唯一标识获取节点对象
     *
     * @param index 节点的唯一标识
     * @return 节点对象
     */
    public DNode getDNodeByIndex(int index) {
        return nodeMap.get(index);
    }

    /**
     * 通过x节点获取其对应的邻居节点
     *
     * @param x 查找邻居节点的相关节点
     * @return 邻居节点列表
     */
    public Set<DNode> getNeighbors(DNode x) {
        return neighborsMap.getOrDefault(x,new HashSet<>());
    }

    /**
     * 得到从x节点移动到y节点的移动代价
     *
     * @param x 起始点
     * @param y 目标点
     */
    public long getCost(DNode x, DNode y) {
        Integer val = costMap.get(generateCostKey(x, y));
        // 当不存在这样的路径的移动代价时，也就是不存在对应方向的路径，为此将其设置为最大值
        return Objects.isNull(val) ? Integer.MAX_VALUE : val;
    }

    /**
     * 修改节点x移动到节点y的移动代价
     *
     * @param x    起始点
     * @param y    目标点
     * @param cost 新的移动代价
     */
    public void setCost(DNode x, DNode y, int cost) {
        costMap.put(generateCostKey(x, y), cost);
    }

    /**
     * 用于生成存储两个节点的移动代价的键
     *
     * @param x 起始点
     * @param y 目标点
     * @return 从起始点移动到目标点的键
     */
    private String generateCostKey(DNode x, DNode y) {
        final String SEPARATOR = "~";
        return x.index + SEPARATOR + y.index;
    }
}




import lombok.extern.slf4j.Slf4j;

import java.util.Comparator;
import java.util.Objects;
import java.util.PriorityQueue;
import java.util.Queue;
import java.util.concurrent.TimeUnit;

/**
 * @author 学徒
 */
@Slf4j
public class DStar {
    /**
     * 开放列表
     */
    private Queue<DGraph.DNode> openList;

    /**
     * 地图
     */
    private DGraph graph;

    public DStar(int[][] graph) {
        this(new DGraph(graph));
    }

    private DStar(DGraph graph) {
        this.graph = graph;
        openList = new PriorityQueue<>(Comparator.comparingLong(DGraph.DNode::getK));
    }

    /**
     * 用于修改节点x的状态及其对应的h和k值
     *
     * @param x   节点x
     * @param val 新的h值
     */
    private void insert(DGraph.DNode x, long val) {
        if (Objects.equals(x.getState(), DGraph.STATE.NEW)) {
            x.setH(val);
            x.setK(val);
        } else if (Objects.equals(x.getState(), DGraph.STATE.OPEN)) {
            openList.remove(x);
            x.setK(Math.min(x.getK(), val));
        } else if (Objects.equals(x.getState(), DGraph.STATE.CLOSED)) {
            x.setK(Math.min(x.getH(), val));
            x.setH(val);
        }
        x.setState(DGraph.STATE.OPEN);
        openList.add(x);
    }

    /**
     * 用于处理对应的节点
     *
     * @return openList中所有节点的最小k值，当openList为空时，返回-1;
     */
    private long process_state() {
        DGraph.DNode x = openList.poll();
        if (Objects.isNull(x)) {
            return -1;
        }

        x.setState(DGraph.STATE.CLOSED);

        // 当h值大于k值时，表示当前该节点处于h值被修改为较大的状态(raise状态)
        // 为此查找邻居节点来得到减低自身的h值
        if (x.getK() < x.getH()) {
            for (DGraph.DNode y : graph.getNeighbors(x)) {
                if (y.getH() < x.getK() && x.getH() > y.getH() + graph.getCost(y, x)) {
                    x.setB(y);
                    x.setH(y.getH() + graph.getCost(y, x));
                }
            }
        }

        // 该过程类似于dijikstra，用来传播信息当前节点h值变化的信息和降低邻居节点的h值
        if (Objects.equals(x.getK(), x.getH())) {
            for (DGraph.DNode y : graph.getNeighbors(x)) {
                if (Objects.equals(y.getState(), DGraph.STATE.NEW)
                        || (Objects.equals(y.getB(), x) && !Objects.equals(y.getH(), x.getH() + graph.getCost(x, y)))
                        || (!Objects.equals(y.getB(), x) && y.getH() > x.getH() + graph.getCost(x, y))) {
                    y.setB(x);
                    insert(y, x.getH() + graph.getCost(x, y));
                }
            }
        } else {
            for (DGraph.DNode y : graph.getNeighbors(x)) {
                if (Objects.equals(y.getState(), DGraph.STATE.NEW)
                        || (Objects.equals(y.getB(), x) && !Objects.equals(y.getH(), x.getH() + graph.getCost(x, y)))) {
                    y.setB(x);
                    insert(y, x.getH() + graph.getCost(x, y));
                } else {
                    if (!Objects.equals(y.getB(), x) && y.getH() > x.getH() + graph.getCost(x, y)) {
                        insert(x, x.getH());
                    } else {
                        if (!Objects.equals(y.getB(), x)
                                && x.getH() > y.getH() + graph.getCost(y, x)
                                && Objects.equals(y.getState(), DGraph.STATE.CLOSED)
                                && y.getH() > x.getK()) {
                            insert(y, y.getH());
                        }
                    }
                }
            }
        }

        return Objects.isNull(x = openList.peek()) ? -1 : x.getK();
    }

    /**
     * 用于修改某两个节点之间的移动代价，且y节点为x节点的上一节点
     *
     * @param xIndex 节点1
     * @param xIndex 节点2
     * @param val    节点的移动代价
     * @return 当前新的最小k值
     */
    public long modify_cost(int xIndex, int yIndex, int val) {
        DGraph.DNode x = graph.getDNodeByIndex(xIndex);
        DGraph.DNode y = graph.getDNodeByIndex(yIndex);
        // 此处调转y与x之间的位置是因为有向图中是从x指向y的，但DStar算法是从目标点向起始点进行规划的，为此需要调转方向
        graph.setCost(y, x, val);

        if (Objects.equals(y.getState(), DGraph.STATE.CLOSED)) {
            insert(y, val);
        }
        DGraph.DNode node = null;
        return Objects.isNull(node = openList.peek()) ? -1 : node.getK();
    }

    /**
     * @param y 需要进行重新进行路径规划的点，就是可能是障碍点，或者移除了障碍之后的点
     * @return 返回值大于-1表示调整好路径，等于-1 表示没有路径可以从当前点到达目标点
     */
    private long replan(DGraph.DNode y) {
        long result = -1;
        while (true) {
            if ((result = process_state()) >= y.getH() || Objects.equals(result, -1L)) {
                break;
            }
        }
        return -1;
    }

    /**
     * 执行从start点到end点的最短路径执行过程
     *
     * @param start 起始节点
     * @param end   目标节点
     */
    public void run(int start, int end) {
        insert(graph.getDNodeByIndex(end), 0);
        // 执行路径规划
        long min_k = -1;
        while (true) {
            min_k = process_state();
            if (Objects.equals(graph.getDNodeByIndex(start).getState(), DGraph.STATE.CLOSED)
                    || Objects.equals(min_k, -1L)) {
                break;
            }
        }
        // 不能找到从起始点到目标点的最短路径
        if (Objects.equals(min_k, -1)) {
            log.info("无法找到执行路径");
            return;
        }

        // 用于调试使用
        printPath(start, end);

        DGraph.DNode current = graph.getDNodeByIndex(start);
        DGraph.DNode next = current.getB();
        while (true) {
            while (Objects.nonNull(next) && Objects.equals(next.getState(), DGraph.STATE.CLOSED)) {
                gogogo(next);
                current = next;
                next = next.getB();
            }
            if (Objects.isNull(next)) {
                break;
            }
            // 当前节点节点的上层节点不能移动了，因为某种原因(可能是障碍物)被人修改了执行代价,也就是被调用过该节点的modify_cost(current,current.b,newH)函数
            min_k = replan(current);

            // 重新规划路径后，无法找到一条有效的路
            if (Objects.equals(min_k, -1)) {
                log.info("无法找到有效执行路径");
                return;
            }

            log.info("重新规划后路径");
            // 用于调试使用
            printPath(current.getIndex(), end);
            next = current.getB();
        }
    }

    /**
     * 用于执行移动到指定节点操作
     *
     * @param node 需要移动到的对应节点
     */
    private void gogogo(DGraph.DNode node) {
        log.info("移动到节点：" + node.getIndex());
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (Exception e) {
            log.info(e.getMessage());
        }
    }

    /**
     * 用于打印路径，调试使用
     *
     * @param start 起始点
     * @param end   目标点
     */
    private void printPath(int start, int end) {
        DGraph.DNode s = graph.getDNodeByIndex(start);
        while (s.getIndex() != end) {
            log.info(s.getIndex() + "->");
            s = s.getB();
        }
        log.info(String.valueOf(end));
    }
}
```

**测试用代码：**

```java
/**
 * @author 学徒
 */
public class DStarTest {
    public static void main(String[] args) {
        int[][] graph = new int[][]{
                {0, 1, 12, Integer.MAX_VALUE, Integer.MAX_VALUE, Integer.MAX_VALUE},
                {Integer.MAX_VALUE, 0, 19, 3, Integer.MAX_VALUE, Integer.MAX_VALUE},
                {Integer.MAX_VALUE, Integer.MAX_VALUE, 0, Integer.MAX_VALUE, 5, Integer.MAX_VALUE},
                {Integer.MAX_VALUE, Integer.MAX_VALUE, 4, 0, 13, 15},
                {Integer.MAX_VALUE, Integer.MAX_VALUE, Integer.MAX_VALUE, Integer.MAX_VALUE, 0, 4},
                {Integer.MAX_VALUE, Integer.MAX_VALUE, Integer.MAX_VALUE, Integer.MAX_VALUE, Integer.MAX_VALUE, 0}
        };

        int source = 0, target = 5;
        DStar dStar = new DStar(graph);
        new Thread(() -> {
            try {
                Thread.sleep(2);
            }catch (Exception e){
                e.printStackTrace();
            }
            dStar.modify_cost(3, 2, 1000);
        }).start();
        dStar.run(source, target);
    }
}
```

**总结：** D*算法能过兼容静态环境。在动态变化的环境中，其校正最短路径的效率比重新执行静态路径规划的算法效率要高。

## 总结

对于路径规划，除了常见的Dijikstra算法，A*算法以及D*算法。在面对各种复杂的应用场景和前提条件时，存在着多种多样的其它算法，如D*Lite，LPA等。我们需要对这些算法的基本思路和适用场景有个基本的认识，才能够使高效的达成我们的目标。

以下是一个github链接，其包括了各种路径规划算法。

> [路径规划算法仓库链接](https://github.com/zhm-real/PathPlanning)
>
> [D*算法详细解析](https://blog.csdn.net/banzhuan133/article/details/100532206?spm=1001.2101.3001.6650.4&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-4.opensearchhbase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-4.opensearchhbase)