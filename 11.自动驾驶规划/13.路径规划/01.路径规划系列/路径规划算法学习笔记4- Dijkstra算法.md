- [路径规划算法学习笔记4- Dijkstra算法 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/146740532)

## 1 主要思想

每次找到离源点最近的一个顶点，然后以该顶点为中心进行扩展，最终得到源点到其余所有点的最短路径。基本步骤如下：

a. 将所有的顶点分为两部分：已知最短路程的顶点集合P和未知最短路径的顶点集合Q。最开始，已知最短路径的顶点集合P中只有源点一个顶点。我们这里用一个book数组来记录哪些点在集合P中。例如对于某个顶点i,如果book[i]为1则表示这个顶点在集合P中，如果book[i]为0则表示这个顶点在集合Q中。

b. 设置源点s到自己的最短路径为0即dis[s]=0。若存在有源点直接到达的顶点i, 则把dis[i]设为e[s][i]。同时把所有其他（源点不能直接到达的）顶点的最短路径设为 ∞ 。

c. 在集合Q的所有顶点中选择一个离源点s最近的顶点u(即dis[u]最小)加入到集合P。并考察所有以点u为起点的边，对每一条边进行松弛操作。例如存在一条从u到v的边，那么可以通过将边u->v添加到尾部来扩展一条从s到v的路径，这条路径的长度是dis[u]+e[u][v]。如果这个值比目前已知的dis[v]的值要小，我们可以用新值来替代当前dis[v]中的值。

d. 重复第三步，如果集合Q为空，算法结束。最终dis数组中的值就是源点到所有顶点的最短路径。

![img](https://pic4.zhimg.com/80/v2-76507a2b65330d5e7163c04739e99cef_720w.jpg)

## **2. 图示：**

![img](https://pic4.zhimg.com/80/v2-75e3c2c7dfc62ebb1e0aa5b2d548b297_720w.jpg)

## **3. 代码：**

![img](https://pic4.zhimg.com/80/v2-1fe8117b81ca392fe4c9a25082ca507b_720w.jpg)

```c
#include <stdio.h>
int main()
{
    int e[10][10], dis[10], book[10], i, j, n, m, t1, t2, t3, u, v, min;
    int inf = 99999999; // 用inf存储一个我们认为的正无穷值
    //读入n和m,n表示顶点个数，m表示边的条数
    scanf("%d %d", &n, &m);

    //初始化
    for(i=1;i<=n;i++)
        for(j=1;j<=n;j++)
            if(i==j) e[i][j] = 0;
                else e[i][j] = inf;
    
    //读入边
    for(i=1;i<=m;i++)
    {
        scanf("%d %d %d", &t1, &t2, &t3);
        e[t1][t2] = t3;
    }

    //初始化dis数组，这里是1号顶点到其余各个顶点的初始路程
    for (i=1;i<=n;i++)
        dis[i] = e[1][i];

    //book数组初始化
    for(i=1;i<=n;i++)
        book[i] = 0;
    book[1] = 1;

    //Dijkstra算法核心语句
    for(i=1;i<=n-1;i++)
    {
        //找到离1号顶点最近的顶点
        min = inf;
        for(j=1;j<=n;j++)
        {
            if(book[j]==0 && dis[j]<inf)
            {
                min = dis[j];
                u = j;
            }
        }
        book[u] = 1;
        for(v=1;v<=n;v++)
        {
            if(e[u][v]<inf)
            {
                if(dis[v]>dis[u]+e[u][v])
                    dis[v] = dis[u]+e[u][v];
            }
        }
    }

    //输出最终的结果
    for(i=1;i<=n;i++)
        printf("%d", dis[i]);
    
    getchar();
    getchar();
    return 0;
}
```

可以输入以下数据进行验证。第一行两个整数n和m。n表示顶点个数（顶点编号为1~n），m表示边的条数。接下来m行，每行有3个数xyz,表示顶点x到顶点y边的权值为z。

```text
6 9 
1 2 1
1 3 12
2 3 9
2 4 3
3 5 5
4 3 4
4 5 13
4 6 15
5 6 4
```

运行结果是：

```text
0 1 8 4 13 17
```