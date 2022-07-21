- [【TSP问题】TSP问题有关解法_轩轩是只橘猪猪的博客-CSDN博客_tsp问题](https://blog.csdn.net/qq_44705973/article/details/109629263?ops_request_misc=%7B%22request%5Fid%22%3A%22165838810116781818750425%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=165838810116781818750425&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-109629263-null-null.142^v33^new_blog_pos_by_title,185^v2^control&utm_term=TSP问题&spm=1018.2226.3001.4187)

TSP问题可以有很多种解决方法，比如动态规划、蛮力算法，贪心算法、近似算法、蚁群算法、遗传算法、分支限界法等等。之前课程作业也做过很多有关TSP问题的习题。所以想把这些都总结一下，方便以后作为复习。

# 近似算法

## 基本思想

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020111119365857.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0NzA1OTcz,size_16,color_FFFFFF,t_70#pic_center)

## 代码

```cpp
#include<iostream>
#include<vector>
#include<stack>
using namespace std;
class Graph
{
private:
	int n;//顶点个数
	vector< vector<int> > adj;//邻接矩阵
public:
	Graph(int n)
	{
		this->n = n;
		vector< vector<int> > tmp(n, vector<int>(n, -1));
		adj = tmp;
	}
	void input()//输入数据
	{
		for (int i = 0; i < n; i++)
		{
			for (int j = 0; j < n; j++)
			{
				int tmp;
				cin >> tmp;
				adj[i][j]=tmp;
			}
		}
	}
	void add_edge(int u, int v,int w)//增加一条边
	{
		adj[u][v] = w;
		adj[v][u] = w;
	}
	Graph get_tree(int u_id)//生成最小生成树
	{
		Graph res(n);
		const int INF = 1 << 30;//无穷大
		vector<int> vis(n, 0);//是否被访问过
		vector<int> lowcost(n, INF);//最小代价
		vector<int> parent(n,-1);//记录路径
		lowcost[u_id] = 0;
		for (int i = 0; i<n; i++)
		{
			
			int minlen = INF;
			int p = -1;
			for (int j = 0; j < n; j++)//寻找最小代价的边
			{
				if (lowcost[j]<minlen&&vis[j] == 0)
				{
					minlen = lowcost[j];
					p = j;
				}
			}
			//cout << i <<" " <<p<< endl;
			if (p == -1)    break;
			vis[p] = 1;//标记
			int pa = parent[p];
			if (pa != -1)
			{
				//cout << p << " " << pa << endl;
				res.add_edge(p, pa, 1);//往最小生成树里面加一条边
			}
			for (int j = 0; j<n; j++)
			{
				if (!vis[j] && adj[p][j]<lowcost[j])//更新最小代价和路径
				{
					lowcost[j] = adj[p][j];
					parent[j] = p;
				}
			}
		}
		return res;
	}
	vector<int> dfs(int u_id)//深度优先遍历
	{
		vector<int> vis(n, 0);
		vector<int> res;
		stack<int> s;//栈
		s.push(u_id);
		vis[u_id] = 1;
		while (!s.empty())
		{
			int x = s.top();//取栈顶的元素
			s.pop();
			res.push_back(x);
			for (int i = 0; i < n; i++)//遍历后继结点
			{
				if (adj[x][i]!=-1 && vis[i] == 0)
				{
					vis[i] = 1;//标记
					s.push(i);//入栈
				}
			}
		}
		return res;
	}
};
int main()
{
	int n;
	cin >> n;
	Graph graph(n);
	graph.input();
	Graph tree = graph.get_tree(0);//以0为起点
	vector<int> v = tree.dfs(0);//以0为起点
	for (int i = 0; i < v.size(); i++)
	{
		cout << v[i] << " ";
	}
	cout << endl;
	return 0;
}

/*
5
10000000 10 6 8  7
10 10000000 5 22 1
6  5 10000000 11 7
8 22 11 10000000 3
7 1  7  3 10000000
*/
```

# [贪心算法](https://so.csdn.net/so/search?q=贪心算法&spm=1001.2101.3001.7020)

## 基本思想

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201111193730589.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0NzA1OTcz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201111193743616.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0NzA1OTcz,size_16,color_FFFFFF,t_70#pic_center)

## 代码

```cpp
//贪心法求解TSP问题 
#include<iostream>
using namespace std;
int a[10000][10000];//记录邻接矩阵
int r[10000];//记录路径 
int TSP(int n,int b[],int u,int m,int l,int k)
{/*数组b记录点是否进入路径中，u为当前点编号，m为已进入路径点的个数，
    l为路径长度 ,k为开始点的编号 */
	r[m-1]=u;//当前点存入路径 
    if(m==n) 
	{//n个点都进入路径后，结束递归
	l=l+a[u][k]; //路径长度更新 
	return l;
    }
    else
    { 
	    int min=a[0][0];//将最短距离初始值设成一个比较大的数 
		int v; 
    	for(int i=0; i<n; i++)
        {//寻找剩余点中，距离当前点最近的点，并用变量v记录编号
		if(min>a[u][i]&&b[i]==0)
        {//如果某个点距离当前点更近，且不在路径中 
            v=i;//记录该点编号 
            min=a[u][i];//最短距离更新 
        }
        }
    m++;//路径中点的个数+1 
    b[v]=1;//进入路径的的点状态更新 
    l=l+min;//路径长度更新 
    return TSP(n,b,v,m,l,k);//v变为当前点，递归 
	} 
}
void newTSP(int n,int b[])
{/*贪心法寻找路径时，当起始点的选择不同时，得到的路径不同 ，长度也不同 ， 
   而且在有些无向图中，并不是任意一个起始点都能得到合法路径 ，此函数比较分别 
   以n个点为起始点用贪心法得到的n条路径的长度大小，最终选择长度最短的一条路径*/ 
	int min=a[0][0],t;//min默认为很大的数，t为当前路径长度
	int s;//记录最短路径的起始点 
	for(int i=0;i<n;i++)
	{// 寻找分别以n个点为起始点的n条路径
		for(int j=0;j<n;j++)
		    b[j]=0;//初始化每个点的状态，即初始时每个点都不在路径中
		b[i]=1;//初始点进入路径 
		t=TSP(n,b,i,1,0,i);//寻找路径
		if(t<min)
		{//如果当前路径比min的短 
			s=i;//记录起始点 
		    min=t;//更新min 
		}	
	}
	for(int j=0;j<n;j++)
		    b[j]=0;//初始化每个点的状态，即初始时每个点都不在路径中
	b[s]=1;//初始点进入路径
	t=TSP(n,b,s,1,0,s);//寻找最短路径
	for(int i=0;i<n;i++)
	cout<<r[i]<<" ";//输出路径 
}
int main()
{
	int n;//点的个数 
	cin>>n;
	int b[10000];//记录每个点是否进入路径中 
	for(int i=0;i<n;i++)
	{ 
		for(int j=0;j<n;j++)
	   cin>>a[i][j];//输入邻接矩阵 
	}   
    newTSP(n,b);//寻找路径，并输出0到n-1的一个排列 
    return 0;
}
```

# [分支限界法](https://so.csdn.net/so/search?q=分支限界法&spm=1001.2101.3001.7020)（与贪心算法结合）

## 基本思想

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201111193835274.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0NzA1OTcz,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201111193847396.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0NzA1OTcz,size_16,color_FFFFFF,t_70#pic_center)

## 代码

```cpp
#include <stdio.h>
#include <malloc.h> 

//配合贪心法prime，用于检查prime算法是否执行完 
bool prime_done(int n, int flag[])
{
	for(int i=0; i<n; i++)
		if(flag[i]==0) return false;
	return true;
}

//限界函数
int  prelen(int s,int n,int len,int flag[],int **two,int **a,int *routine,int k)
{
	//lb=（已经确定的路径的长度*2+首尾不在路径上的最小元素+其他不在路径上的点最小的两个长度）/2 
	int lb=0;
	lb+=2*(len+a[routine[k-1]][s]);
	if(k==n-1)	//如果是最后一个点直接首尾相连 
	{
		lb+=2*a[routine[0]][s];
	}
	else  
	{
		int min_0=100000,min_s=100000;	//分别记录首尾不在路径上的最小值 
		for(int i=0;i<n;i++)
		{
			if(flag[i]==0&&i!=s)	//不在路径上的点 也不是当前假设走的这个点 
			{
				lb+=two[i][0]+two[i][1];	//在路径上的点最小的两个长度
				if(a[0][i]<min_0) min_0=a[0][i];
				if(a[s][i]<min_s) min_s=a[s][i];
			}
		}
		lb+=min_0+min_s;
	}
	return (lb/2.0+0.5);	//还是向上取整 
}

//找这一层的合法的极小值
int find_min(int k,int n,int **PT,int *valid){
	int temp_min=10000,temp_min_len=100000;
    for(int i=0;i<n-k;i++)
		if(PT[i][1]<temp_min_len&&valid[i]==1) { temp_min=PT[i][0]; temp_min_len=PT[i][1]; }
	return temp_min;	//返回极小值的那个点 
}
    
//找所有可能的下一个点 
bool next(int k,int n,int **a,int *flag,int **two,int *routine,int r_len,int up,int down)
{
	int i;
	//是否走完全程，如果是，输出路径并结束 
	if(k==n)
	{
		for(i=0;i<n;i++) printf("%d ",routine[i]);	//输出路径
		return true; 
	}
	
	int **PT=NULL; 
	PT = (int**)malloc(sizeof(int*)*n-k);	//为二维数组分配n-k行,每一行是一种可能，因为已有k个点确定，下一个点的可能有n-k种 
	int temp=0;	//下一个可能点 

	//每个可能的下一个点都试一下 
	for (i = 0; i < n-k; ++i,temp++){	 
    	PT[i] = (int*)malloc(sizeof(int)*2);  	//为每行分配2个大小空间，前一个是下一个路径点，后一个是预估路径长度
		while(flag[temp]!=0) temp++; 	//	temp是顺次找的，如果当前temp已经在路径中直接顺着往后找 
		PT[i][0]=temp;
		PT[i][1]=prelen(temp,n,r_len,flag,two,a,routine,k); 
    }
    
    //找出合法的限界值
    int con=0,valid[n-k]; 
	for(i=0;i<n-k;i++)
		if(PT[i][1]>=down&&PT[i][1]<=up) { valid[i]=1; con=1; }		 //con表示存在合法的限界 
	if(con==0) {printf("return false\n");return false;} 	//con始终等于0表示没有合法限界，返回错误 

	//找这一层的合法的极小值往下算
	routine[k]=find_min(k,n,PT,valid);
	
	//加入路径 
	r_len+=a[routine[k-1]][routine[k]];
	flag[routine[k]]=1; k++;		
	
	while(next(k,n,a,flag,two,routine,r_len,up,down)==false)
	{
		//如果当前选择走到后面没有合法限界值了 
		valid[routine[k]]=0;	//之前取的这个点不能用了
		flag[routine[k]]=0;		//退掉这个点，没走过 
		k--; r_len-=a[routine[k-1]][routine[k]]; 	//路程也要减掉退回去的
		routine[k]=find_min(k,n,PT,valid);	//重新找 
	}
	
	//释放动态开辟的空间 
    for (i = 0; i < 2; ++i){ 
        free(PT[i]); 
    } 
    free(PT);
	return true;
}

int main()
{
	int n;	//图规模 
	scanf("%d",&n);
	//动态分配代价矩阵
	int **a = NULL; 	 
    int i, j; 
    a = (int**)malloc(sizeof(int*)*n);	//为二维数组分配n行 
    for (i = 0; i < n; ++i){	//为每行分配n个大小空间 
        a[i] = (int*)malloc(sizeof(int)*n); 
    } 
    //输入代价矩阵 
    for (i = 0; i < n; ++i){ 
        for (j = 0; j < n; ++j){ 
            scanf("%d",&a[i][j]);
        } 
    } 
    
    //为了不重复查找 先把每行最小的两个元素存在一个矩阵里 
    int **two = NULL;
    two = (int**)malloc(sizeof(int*)*n);	//为二维数组分配n行 
    for (i = 0; i < n; ++i){	//为每行分配2个大小空间 
        two[i] = (int*)malloc(sizeof(int)*2);
		two[i][0] =10000;	two[i][1] =10000;	//先初始化两个最小值是一个很大的数 
        for (j = 0; j < n; ++j){ 
            if(a[i][j]<two[i][0]) two[i][0]=a[i][j];	//最小的数放在每行第一位个 
            else if(a[i][j]<two[i][1]) two[i][1]=a[i][j];	//次小的数放在每行第二个 
        } 
    }  
    
    int flag[n]; 	//flag标记每个点是否走过，0为没走过，1是走过 
	for(i=1;i<n;i++) flag[i]=0;		//初始化flag
    flag[0]=1;	//第一个作为起点直接“走过”
    
    //确定上下界 
	int up=0,down=0;
	
	//确定上界采用贪心法的prime算法
	for(i=0;prime_done(n,flag)==false;)
	{
		int min,min_len=1000;	//记录到当前点最近的点的下标和距离 
		for(int j=0;j<n;j++)
		{
			if(flag[j]==0) 
				if(a[i][j]<min_len)
				{ min=j; min_len=a[i][j];}
		}
		i=min;	flag[i]=1;	//下一个点标记为“走过” 
		up+=min_len;
	}
	up+=a[i][0];	//最后还要回到原点
	
	//确定下界：把矩阵中每行最小的两个元素相加除2，向上取整
	for(i=0;i<n;i++) down+=two[i][0]+two[i][1];
	down=down/2.0+0.5;	//如果是偶数除2直接是整数，不存在取整；如果是奇数，得数是.5，加0.5实现向上取整 

	for(i=1;i<n;i++) flag[i]=0;		//重新初始化初始化flag
	int routine[n]; 	//存放结果路径的数组 
	routine[0]=0; flag[0]=1;	//第一个作为起点直接“走过”
	int r_len=0; 	//已经确定的路径长度 
	int k=1;	//已经确定了的点数

	//找所有可能的下一个点 
	if(next(k,n,a,flag,two,routine,r_len,up,down)==false)
	{
		printf("Error!\n");
		return 0;
	}
	 
	//释放动态开辟的空间 
    for (i = 0; i < 2; i++){ 
        free(two[i]); 
    } 
    free(two); 
    for (i = 0; i < n; ++i){ 
        free(a[i]); 
    } 
    free(a);
	 
    return 0; 
} 
/*
测试用例 
5
100000 5 61 34 12
5 100000 43 20 7
61 43 100000 8 21
34 20 8 100000 8
12 7 21 8 100000
*/
```

# [动态规划](https://so.csdn.net/so/search?q=动态规划&spm=1001.2101.3001.7020)算法（与回溯法相结合）

## 基本思想

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201111193917151.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0NzA1OTcz,size_16,color_FFFFFF,t_70#pic_center)

## 代码

```cpp
#include<iostream.h>
#include<fstream.h>
#include<stdlib.h>
#include<math.h>

#define n 6     //结点个数

void main()
{
	int i,j,k,min,temp;
	int b=(int)pow(2,n-1);
	int D[20][20];//原图的邻接矩阵
	
	fstream fin("TSPinput1.txt",ios::in);//打开输入文件
	fstream fout("TSPoutput.txt",ios::out);//打开输出文件

	//读入数据到邻接矩阵D中
	for(i=0;i<n;i++)
		for(j=0;j<n;j++)
			fin>>D[i][j];

	//申请二维数组F和M
	int ** F = new int* [n];//n行b列的二维数组，存放阶段最优值
	int ** M = new int* [n];//n行b列的二维数组，存放最优策略
	for(i=0;i<n;i++)
	{
		F[i] = new int[b];//每行有2的n-1次方列
		M[i] = new int[b];
	}

	//初始化F[][]和M[][]
	for(i=0;i<b;i++)
		for(j=0;j<n;j++)
		{
			F[j][i] = -1;
			M[j][i] = -1;
		}
	
	//给F的第0列赋初值
	for(i=0;i<n;i++)
		F[i][0] = D[i][0];
		
	//遍历并填表
	for(i=1;i<b-1;i++)//最后一列不在循环里计算
		for(j=1;j<n;j++)
		{
			//int p=(int)pow(2,j-1);
			//int res=p & i;
			if( ((int)pow(2,j-1) & i) == 0)//结点j不在i表示的集合中
			{
				min=65535;
				for(k=1;k<n;k++)
					if( (int)pow(2,k-1) & i )//非零表示结点k在集合中
					{
						temp = D[j][k] + F[k][i-(int)pow(2,k-1)]; 
						if(temp < min)
						{
							min = temp;
							F[j][i] = min;//保存阶段最优值
							M[j][i] = k;//保存最优决策
						}
					}
		
			}
		}
		
	//最后一列，即总最优值的计算
	min=65535;
	for(k=1;k<n;k++)
	{
		//b-1的二进制全1，表示集合{1,2,3,4,5}，从中去掉k结点即将k对应的二进制位置0
		temp = D[0][k] + F[k][b-1 - (int)pow(2,k-1)];
		if(temp < min)
		{
			min = temp;
			F[0][b-1] = min;//总最优解
			M[0][b-1] = k;
		}
	}
	fout<<"最短路径长度："<<F[0][b-1]<<endl;//最短路径长度


	//回溯查表M输出最短路径(编号0~n-1)
	fout<<"最短路径(编号0—n-1)："<<"0";
	for(i=b-1,j=0; i>0; )//i的二进制是5个1，表示集合{1,2,3,4,5}
	{
		j = M[j][i];//下一步去往哪个结点
		i = i - (int)pow(2,j-1);//从i中去掉j结点
		fout<<"->"<<j;
	}
	fout<<"->0"<<endl;

	//输出表格F到文件
	for(i=0;i<n;i++)
	{
		for(j=0;j<b;j++)
			fout<<F[i][j]<<" ";
		fout<<endl;
	}

}
```

# [遗传算法](https://so.csdn.net/so/search?q=遗传算法&spm=1001.2101.3001.7020)

思路在前面，就不再写了。

```cpp
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include "math.h"
#include "time.h"
#include <unistd.h>       //提供统一API接口 
#include <bits/stdc++.h>  //万能头文件，包含几乎可以用到的所有C++函数库 
#define Citynum 100     //城市数，编号是0~Citynum-1
#define Popnum 300        //种群个体数
#define Maxbound 10000000 //路径最大值上限
#define N 1000000         //需要根据实际求得的路径值修正
unsigned seed=(unsigned)time(0);
double Hash[Citynum+1];   //哈希表 
typedef struct CityPosition//城市点坐标 
{
    double x;
    double y;
}CityPosition;
CityPosition CityPos[100]={
	//根据城市规模设置相关参数 
    {11003.611100,42102.500000},{11108.611100,42373.888900},{11133.333300,42885.833300},
	{11155.833300,42712.500000},{11183.333300,42933.333300},{11297.500000,42853.333300},
	{11310.277800,42929.444400},{11416.666700,42983.333300},{11423.888900,43000.277800},
	{11438.333300,42057.222200},//10 
	{11461.111100,43252.777800},{11485.555600,43187.222200},{11475.555600,43187.222200},
	{11503.055600,42855.277800},{11511.388900,42106.388900},{11522.222200,42841.944400},
	{11569.444400,43136.666700},{11583.333300,43150.000000},{11595.000000,43148.055600},
	{11600.000000,43150.000000},//20
	{11690.555600,42686.666700},{11715.833300,41836.111100},{11745.277800,42884.444400},
	{11751.111100,42814.444400},{11770.277800,42651.944400},{11785.277800,42884.444400},
	{11822.777800,42673.611100},{11846.944400,42660.555600},{11963.055600,43290.555600},
	{11973.055600,43026.111100},//30 
	{12058.333300,42195.555600},{12149.444400,42477.500000},{13785.177800,42884.444400},
	{12286.944400,43355.555600},{12300.000000,42433.333300},{12355.833300,43156.388900},
	{12363.333300,43189.166700},{12372.777800,42711.388900},{12386.666700,43334.722200},
	{12421.666700,42895.555600},//40
	{13645.000000,42973.333300},{13149.444400,42477.500000},{13785.177800,43884.444400},
	{13286.944400,43355.555600},{13300.000000,42433.333300},{13355.833300,43156.388900},
	{13363.333300,43189.166700},{13372.777800,42711.388900},{13386.666700,43334.722200},
	{14421.666700,42895.555600},//50 
	{14003.611100,44102.500000},{14108.611100,42373.888900},{14133.333300,44885.833300},
	{14155.833300,44712.500000},{14183.333300,42933.333300},{14297.500000,44853.333300},
	{14310.277800,44929.444400},{14416.666700,42983.333300},{14423.888900,45000.277800},
	{14438.333300,44057.222200},//60 
	{11461.111100,45252.777800},{11485.555600,45187.222200},{11475.555600,45187.222200},
	{11503.055600,45855.277800},{11511.388900,45106.388900},{11522.222200,45841.944400},
	{11569.444400,45136.666700},{11583.333300,45150.000000},{11595.000000,45148.055600},
	{11600.000000,45150.000000},//70 
	{11690.555600,46686.666700},{11715.833300,46836.111100},{11745.277800,46884.444400},
	{11751.111100,46814.444400},{11770.277800,46651.944400},{11785.277800,46884.444400},
	{11822.777800,46673.611100},{11846.944400,46660.555600},{11963.055600,46290.555600},
	{11973.055600,46026.111100},//80 
	{12058.333300,47195.555600},{12149.444400,47477.500000},{13785.177800,47884.444400},
	{12286.944400,47355.555600},{12300.000000,47433.333300},{12355.833300,47156.388900},
	{12363.333300,47189.166700},{12372.777800,47711.388900},{12386.666700,47334.722200},
	{12421.666700,47895.555600},//90 
	{13645.000000,48973.333300},{13149.444400,48477.500000},{13785.177800,48884.444400},
	{13286.944400,48355.555600},{13300.000000,48433.333300},{13355.833300,48156.388900},
	{13363.333300,48189.166700},{13372.777800,48711.388900},{13386.666700,48334.722200},
	{14421.666700,48895.555600}//100
};
double CityDistance[Citynum][Citynum];//城市距离词典
typedef struct{
    int colony[Popnum][Citynum+1];//城市种群,默认出发城市编号为0，则城市编号的最后一个城市还应该为0
    double fitness[Popnum];//每个个体的适应值，即1/Distance[Popnum]
    double Distance[Popnum];//每个个体的总路径
    int BestRooting[Citynum+1];//最优城市路径序列
    double BestFitness;//最优路径适应值
    double WorstFitness;//最差路径适应值
    double SumFitness;//适应度之和 
    double priFitness;//平均适应度 
    double BestValue;//最优路径长度
    int BestNum;
}TSP,*PTSP;

/*计算城市距离词典CityDistance[i][j]*/
void CalculatDist(){
    int i,j;
    double temp1,temp2;
    for(i=0;i<Citynum;i++){
        for(j=0;j<=Citynum;j++){//最后一个城市还应该返回到出发节点
            temp1=CityPos[j].x-CityPos[i].x;
            temp2=CityPos[j].y-CityPos[i].y;
            CityDistance[i][j]=sqrt(temp1*temp1+temp2*temp2);
        }
    }
}
/*数组复制*/
void copy(int a[],int b[]){
    int i=0;
    for(i=0;i<Citynum+1;i++){
        a[i]=b[i];
    }
}
/*用来检查新生成的节点是否在当前群体中*/
//0号节点是默认出发节点和终止节点
bool check(TSP &city,int pop,int num,int k){
    int i;
    for(i=0;i<=num;i++){
        if(k==city.colony[pop][i])
            return true;//新生成节点存在于已经生成的路径中
    }
    return false;//新生成节点没有存在于已经生成的路径中
}
/*种群初始化，即为city.colony[i][j]赋值*/
void InitColony(TSP &city){
    int i,j,r;
    for(i=0;i<Popnum;i++){
        city.colony[i][0]=0;//默认出发城市为0 
        city.colony[i][Citynum]=0;//城市编号的最后一个城市也为0 
        city.BestValue=Maxbound;
        city.BestFitness=0;//适应值越大越好
        city.WorstFitness=100;
        city.SumFitness=0;
		city.priFitness=0; 
    }
    for(i=0;i<Popnum;i++){
        for(j=1;j<Citynum;j++){
            r=rand()%(Citynum-1)+1;//产生1～Citynum-1之间的随机数
            while(check(city,i,j,r))//随机产生城市序号，即为city.colony[i][j]赋值
            { r=rand()%(Citynum-1)+1;}
            city.colony[i][j]=r;
        }
    }
}
/*计算适应值,考虑应该在这里面把最优选出来*/ 
void CalFitness(TSP &city)
{
    int i,j;
    int start,end;
    int Best=0,Worst=0;
    city.SumFitness=0;
    for(i=0;i<Popnum;i++){//求每个个体的总路径，适应值
        city.Distance[i]=0;
        for(j=1;j<=Citynum;j++){
            start=city.colony[i][j-1];//与此城市相邻的上一个城市 
			end=city.colony[i][j];//此城市 
            city.Distance[i]=city.Distance[i]+CityDistance[start][end];//city.Distance[i]每个个体的总路径
        }
        city.fitness[i]=N/city.Distance[i];
        if(city.fitness[i]>city.fitness[Best])//选出最大的适应值，即选出所有个体中的最短路径
            Best=i;
        if(city.fitness[i]<city.fitness[Worst])//选出最小的适应值
            Worst=i;
        city.SumFitness=city.SumFitness+city.fitness[i];//总适应值就是将所有的适应值相加 
    }
    copy(city.BestRooting,city.colony[Best]);//将最优个体拷贝给city.BestRooting
    city.BestFitness=city.fitness[Best];
    city.WorstFitness=city.fitness[Worst];
    city.BestValue=city.Distance[Best];
    city.priFitness=city.SumFitness/Popnum;//计算平均适应度 
    city.BestNum=Best;
}
/*选择算子：轮盘赌法*/ 
void Select(TSP &city)
{
    int TempColony[Popnum][Citynum+1];
    int i,j,t;
    double s;
    double GaiLv[Popnum];
    double SelectP[Popnum+1];
    double avg;
    double sum=0;
    for(i=0;i<Popnum;i++)
    {sum+=city.fitness[i];}//算出总适应度 
    for(i=0;i<Popnum;i++)
    {GaiLv[i]=city.fitness[i]/sum;}//被选中的比例 
    SelectP[0]=0;
    for(i=0;i<Popnum;i++)
    {SelectP[i+1]=SelectP[i]+GaiLv[i]*RAND_MAX;}//选中的概率 
    memcpy(TempColony[0],city.colony[city.BestNum],sizeof(TempColony[0]));//memcpy函数用于把资源内存（src所指向的内存区域） 拷贝到目标内存（dest所指向的内存区域）
	for(t=1;t<Popnum;t++){
        double ran = rand() % RAND_MAX + 1;
        s= (double) ran / 100.0;
        for(i=1;i<Popnum;i++){
            if(SelectP[i]>=s)
                break;
        }
        memcpy(TempColony[t],city.colony[i-1],sizeof(TempColony[t]));
    }
    for(i=0;i<Popnum;i++){
        memcpy(city.colony[i],TempColony[i],sizeof(TempColony[i]));
    }
}
/*交叉：头尾不变，中间打乱顺序交叉*/
//交叉概率是pc
void Cross(TSP &city,double pc)
{
    int i,j,t,l;
    int a,b,ca,cb;
    int Temp1[Citynum+1],Temp2[Citynum+1];
    for(i=0;i<Popnum;i++){
        double s=((double)(rand()%RAND_MAX))/RAND_MAX;
        if(s<pc){
            cb=rand()%Popnum;
            ca=cb;
            if(ca==city.BestNum||cb==city.BestNum)//如果遇到最优则直接进行下次循环
                continue;
            l=rand()%50+1;//n/2
            a=rand()%(Citynum-l)+1;//1-37
            memset(Hash,0,sizeof(Hash));//作用是将某一块内存中的内容全部设置为指定的值,这个函数通常为新申请的内存做初始化工作。
            Temp1[0]=Temp1[Citynum]=0;
            for(j=1;j<=l;j++)//打乱顺序即随机，选出来的通过Hash标记为1
            {
                Temp1[j]=city.colony[cb][a+j-1]; //a+L=2~38 20~38
                Hash[Temp1[j]]=1;
            }
            for(t=1;t<Citynum;t++)
            {
                if(Hash[city.colony[ca][t]]==0)
                {
                    Temp1[j++]=city.colony[ca][t];
                    Hash[city.colony[ca][t]]=1;
                }
            }
            memcpy(city.colony[ca],Temp1,sizeof(Temp1));
        }
    }

}
/*变异*/
double GetFittness(int a[Citynum+1])
{
    int i,start,end;
    double Distance=0;
    for(i=0;i<Citynum;i++)
    {
        start=a[i];   end=a[i+1];
        Distance+=CityDistance[start][end];
    }
    return N/Distance;
}
/*对换变异*/
//变异概率是pm
void Mutation(TSP &city,double pm)
{
    int i,k,m;
    int Temp[Citynum+1];
    for(k=0;k<Popnum;k++)
    {
        double s=((double)(rand()%RAND_MAX))/RAND_MAX;//随机产生概率0~1间
        i=rand()%Popnum;//随机产生0~Popnum之间的数
        if(s<pm&&i!=city.BestNum)//i!=city.BestNum，即保证最优的个体不变异
        {
            int a,b,t;
            a=(rand()%(Citynum-1))+1;
            b=(rand()%(Citynum-1))+1;
            copy(Temp,city.colony[i]);
            if(a>b)//保证让b>=a
            {
                t=a;
                a=b;
                b=t;
            }
            for(m=a;m<(a+b)/2;m++)
            {
                t=Temp[m];
                Temp[m]=Temp[a+b-m];
                Temp[a+b-m]=t;
            }

            if(GetFittness(Temp)<GetFittness(city.colony[i]))
            {
                a=(rand()%(Citynum-1))+1;
                b=(rand()%(Citynum-1))+1;
                memcpy(Temp,city.colony[i],sizeof(Temp));
                if(a>b)
                {
                    t=a;
                    a=b;
                    b=t;
                }
                for(m=a;m<(a+b)/2;m++)
                {
                    t=Temp[m];
                    Temp[m]=Temp[a+b-m];
                    Temp[a+b-m]=t;
                }

                if(GetFittness(Temp)<GetFittness(city.colony[i]))
                {
                    a=(rand()%(Citynum-1))+1;
                    b=(rand()%(Citynum-1))+1;
                    memcpy(Temp,city.colony[i],sizeof(Temp));
                    if(a>b)
                    {
                        t=a;
                        a=b;
                        b=t;
                    }
                    for(m=a;m<(a+b)/2;m++)
                    {
                        t=Temp[m];
                        Temp[m]=Temp[a+b-m];
                        Temp[a+b-m]=t;
                    }
                }

            }
            memcpy(city.colony[i],Temp,sizeof(Temp));
        }
    }
}
void OutPut(TSP &city)
{
    int i,j;
    printf("最佳路径为:\n");
    for(i=0;i<=Citynum;i++){ 
    	if(i==Citynum) 
    	printf("%d",city.BestRooting[i]);
    	else
    	printf("%d-->",city.BestRooting[i]);
	} 
    printf("\n最佳路径值为：%f\n",(city.BestValue));
}
int main()
{
    TSP city;
    double pcross,pmutation;//交叉概率和变异概率
    double Sfitness=0;//适应度之和
	double Pfitness;//平均适应度 
    int MaxEpoc;//最大迭代次数
    int i;
    srand(seed);
    MaxEpoc=30000;
    pcross=0.85; pmutation=0.15;
    CalculatDist();//计算城市距离词典
    InitColony(city);//生成初始种群
    CalFitness(city);//计算适应值,考虑应该在这里面把最优选出来
	int begintime,endtime;//计算时间 
	double Ptime;
	begintime=clock();
    for(i=0;i<MaxEpoc;i++)
    {
	    Select(city);//选择(复制)：轮盘赌法
        Cross(city,pcross);//交叉
        Mutation(city,pmutation);//变异
        CalFitness(city);//计算适应值
        Sfitness=Sfitness+city.priFitness; 
    }
    endtime=clock();
    Ptime=float(endtime-begintime)/MaxEpoc;
    Pfitness=Sfitness/MaxEpoc;
	printf("最大适应度：%lf\n",city.BestFitness); 
	printf("最小适应度：%lf\n",city.WorstFitness); 
	printf("平均适应度：%lf\n",Pfitness); 
	printf("平均时间：%lfms\n",Ptime);
    OutPut(city);//输出
    return 0;
}
```

# 蚁群算法

```cpp
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<math.h>
#include<time.h>

#define CITY_NUM 20 //城市数量 
#define ANT_NUM 30  //蚁群数量 
#define TMAC 1000   //最大迭代次数 
#define ROU 0.3     //误差大小 
#define ALPHA 1     //信息素重要程度的参数 
#define BETA 4      //启发式因子重要程度的参数 
#define Q 100       //信息素残留参数 

double dis[100][100];   //距离 
double info[100][100];  //信息素矩阵 
const double mmax=1000;

double CityPos[30][2]={
	18200.0000,109550.0000,
	18200.0000,109583.3333,
	18206.3889,109581.9444,
	18207.5000,109477.7778,
	18215.8333,109700.5556,
	18216.6667,109700.0000,
	18220.5556,109510.2778,
	18223.8889,109552.2222,
	18229.7222,109528.3333,
	18233.3333,109533.3333,
	18266.6667,109566.6667,
	18266.6667,109666.6667,
	18271.3889,109705.8333,
	18278.3333,109730.2778,
	18279.4444,109675.2778,
	18281.1111,109480.8333,
	18281.3889,109684.1667,
	18283.3333,109400.0000,
	18283.8889,109569.7222,
	18283.8889,109705.5556,
	18325.8333,109528.3333,
	18327.2222,109256.3889,
	18327.7778,109247.5000,
	18332.5000,109490.2778,
	18333.3333,109450.0000,
	18335.2778,109323.0556,
	18336.1111,109731.3889,
	18344.7222,109452.2222,
	18347.2222,109638.8889,
	18347.7778,109203.3333
};

//返回指定范围内的随机整数 
int rnd(int n,int p){
    return n+(p-n)*rand()/(RAND_MAX+1);
} 

//返回指定范围内的随机浮点数 
double rnd(double d,double b){
    double dbTemp=rand()/((double)RAND_MAX+1.0);
	return d+dbTemp*(b-d);
}

struct Ant{
    int Path[CITY_NUM];//蚂蚁走的路径 
	double length;//路径总长度 
	int vis[CITY_NUM];//走过城市标记 
	int cur_cityno;//当前城市 
	int moved_cnt;//已走的数量  	
	//初始化 
	void Init(){
	    memset(vis,0,sizeof(vis));
		length=0;
		cur_cityno=rnd(0,CITY_NUM);//随机选择一个出发城市 
		Path[0]=cur_cityno;
		vis[cur_cityno]=1;
		moved_cnt=1;
	}
	//选择下一个城市 
	//返回值为城市编号 
	int chooseNextCity(){
	   int nSelectedCity=-1;//返回结果，先暂时把其设置为-1 
	   //计算当前城市和没去过的城市之间的信息素总和 
	   double dbTotal=0.0;
	   double prob[CITY_NUM];//保存各个城市被选中的概率 
	   for(int i=0;i<CITY_NUM;i++){ 
	        if(!vis[i]){
			    prob[i]=pow(info[cur_cityno][i],ALPHA)*pow(1.0/dis[cur_cityno][i],BETA);
				dbTotal+=prob[i];
			}
			else{
			   prob[i]=0;
			}
		}
		//进行轮盘选择 
		double dbTemp=0.0;
		if (dbTotal>0.0){//总的信息素值大于0 
		    dbTemp=rnd(0.0,dbTotal);
		    for (int i=0;i<CITY_NUM;i++){
			   if (!vis[i]){ 
			       dbTemp-=prob[i];
				   if (dbTemp<0.0){
				        nSelectedCity=i;
						break;
					}
				}
			}
		}
		//如果城市间的信息素非常小(小到比double能够表示的最小的数字还要小)      
	    //出现这种情况，就把第一个没去过的城市作为返回结果 
		if (nSelectedCity==-1){
		    for (int i=0;i<CITY_NUM;i++){
			    if(!vis[i]){//城市没去过 
				    nSelectedCity=i;
					break;
				}
		    }
		}
		return nSelectedCity;    
	}     
	
	//蚂蚁在城市间移动    
	void Move(){        
	    int nCityno=chooseNextCity();//选择下一个城市        
		Path[moved_cnt]=nCityno;//保存蚂蚁走的路径
		vis[nCityno]=1;//把这个城市设置成已经去过 
		cur_cityno=nCityno;//更新已走路径长度 
		length+=dis[Path[moved_cnt-1]][Path[moved_cnt]];
		moved_cnt++;
	}
	
	//蚂蚁进行搜索一次    
	void Search(){        
	    Init();        
		//如果蚂蚁去过的城市数量小于城市数量，就继续移动 
		while(moved_cnt<CITY_NUM){            
		    Move();        
		}        
		length+=dis[Path[CITY_NUM-1]][Path[0]];    
	}
};  

struct TSP{    
    Ant ants[ANT_NUM];    
	Ant ant_best;    
	//定义一群蚂蚁
	//保存最好结果的蚂蚁
	void Init(){        
	    //初始化为最大值        
		ant_best.length=mmax;        
		puts("cal dis");        
		//计算两两城市间距离        
		for(int i=0;i<CITY_NUM;i++){            
		    for(int j=0;j<CITY_NUM;j++){                
			    double temp1=CityPos[j][0]-CityPos[i][0];                
				double temp2=CityPos[j][1]-CityPos[i][1];                
				dis[i][j]=sqrt(temp1*temp1+temp2*temp2);            
			}        
		}        
		//初始化环境信息素        
		puts("init info");        
		for(int i=0;i<CITY_NUM;i++){            
		    for(int j=0;j<CITY_NUM;j++){                
			    info[i][j]=1.0;            
			}       
		}   
	}    
	
	//更新信息素,当前每条路上的信息素等于过去保留的信息素    
	//加上每个蚂蚁这次走过去剩下的信息素    
	void Updateinfo(){     
		double tmpinfo[CITY_NUM][CITY_NUM];        
		memset(tmpinfo,0,sizeof(tmpinfo));        
		int m=0;        
		int n=0;       
		//遍历每只蚂蚁        
		for(int i=0;i<ANT_NUM;i++){           
			for(int j=1;j<CITY_NUM;j++){                
			    m=ants[i].Path[j];                
				n=ants[i].Path[j-1];              
				tmpinfo[n][m]=tmpinfo[n][m]+Q/ants[i].length;                
				tmpinfo[m][n]=tmpinfo[n][m];            
			}            
			//最后城市和开始城市之间的信息素            
			n=ants[i].Path[0];            
			tmpinfo[n][m]=tmpinfo[n][m]+Q/ants[i].length;            
			tmpinfo[m][n]=tmpinfo[n][m];        
		}          
		
		//更新环境信息素        
		for(int i=0;i<CITY_NUM;i++){
		    for(int j=0;j<CITY_NUM;j++){
			   //最新的环境信息素 = 留存的信息素 + 新留下的信息素                
			   info[i][j]=info[i][j]*ROU+tmpinfo[i][j];
			}
		}
	}
	
	//寻找路径，迭代TMAC次 
	void Search(){ 
	    for(int i=0;i<TMAC;i++){
		    printf("current iteration times %d\n", i);
			for(int j=0;j<ANT_NUM;j++){                
			    ants[j].Search();
			}
			//保存最佳结果 
			for(int j=0;j<ANT_NUM;j++){
			    if(ant_best.length>ants[j].length){
				    ant_best=ants[j];
				}
			}
			//更新环境信息素 
			Updateinfo();
			printf("current minimum length %lf\n",ant_best.length);
		}
	}
};

int main(){
	TSP tsp;   //初始化蚁群 
	tsp.Init();//开始查找 
	tsp.Search();
	printf("\n利用蚁群求得30个城市最短路径为 : \n");
	for(int i=0;i<CITY_NUM;i++){
		if(i!=CITY_NUM-1){
			printf("%d-->",tsp.ant_best.Path[i]);
		}
	}
	return 0;
}
```