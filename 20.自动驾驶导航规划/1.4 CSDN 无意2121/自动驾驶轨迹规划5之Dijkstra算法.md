- [【自动驾驶轨迹规划5之Dijkstra算法】_无意2121的博客-CSDN博客](https://blog.csdn.net/weixin_65089713/article/details/123924900)

# 1 [Dijkstra算法](https://so.csdn.net/so/search?q=Dijkstra算法&spm=1001.2101.3001.7020)的简介

Dijkstra算法是由荷兰计算机科学家 Dijkstra 于1959年提出的。是从一个顶点到其余各顶点的[最短路径](https://so.csdn.net/so/search?q=最短路径&spm=1001.2101.3001.7020)算法，解决的是**有权图**中最短路径问题。目前可以应用于机器人**自主导航、自动驾驶**。

# 2 Dijkstra算法原理及流程

## 2.1 基本原理

迪杰斯特拉算法主要特点是从起点开始，采用**贪心算法**的策略，每次**遍历**搜索到起点距离**最近且未访问过**的顶点的邻接节点，每一次将距离最近且未访问过的顶点的邻接节点设置为**已访问**，将这一次设置为已访问的节点设为**中间点**，去更新其他节点**通过这个中间点**到达起点的距离，通过不断迭代，直到扩展到终点为止。

## 2.2 算法流程图

### 2.2.1 变量举例介绍

father(3)=2 代表节点 3 在最短路径中的上一个节点是节点 2

father(3)=0 代表节点 3 还未被确定到节点 1 的最短路径也就是设置为未访问

节点 j 在遍历所有还未确定距离节点 1 最近路径的节点

节点 i 为刚刚确定了最短路径的节点，需要为节点 j 与节点 1 之间建立中间点

min 在不断更新，最终确定下来是为访问节点离节点 1 最近的距离

k 在不断更新，最终确定下来是为访问节点离节点 1 最近的节点

### 2.2.2 下面是算法流程图



![img](https://img-blog.csdnimg.cn/4b7aeee1209e4e41a52abc3ab479efb3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5peg5oSPMjEyMQ==,size_20,color_FFFFFF,t_70,g_se,x_16)

# 

# 3 算法测试地图

参考 [【自动驾驶轨迹规划自学笔记3】_无意2121的博客-CSDN博客](https://blog.csdn.net/weixin_65089713/article/details/123843548)

在那篇文章中还有更多的地图结构介绍，对Dijkstra算法的测试可以利用那篇文章的各种地图结构

本篇文章测试地图如下

![img](https://img-blog.csdnimg.cn/daf5128b55c347ff9f49a945e73c474d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5peg5oSPMjEyMQ==,size_9,color_FFFFFF,t_70,g_se,x_16)



将上面的图片转化为栅格地图的matlab代码如下

```ruby
clc
clear all
Img=imread('C:\Autodesk\WI\ceshi.jpg');
Img = flipud(Img);%y坐标调换，由于读入图像时上下会反转
I = rgb2gray(Img);
a=50;b=50;% 设置网格格数 a表示横轴 b表示纵轴
length=1;%网格边长   
B = imresize(I,[a/length b/length]);%对图像进行矩阵化
J=floor(B/(255-60));
%不接近白色的都变为黑色，即变成障碍物，这里的60是可以调整的范围，不同的图像有不同的处理方法
hold on;grid on;%添加网格线
axis([0,a,0,b]);
% gca表示当前绘图区域
% xtick表示x轴坐标刻度，刻度为0到a，步进为1
set(gca,'xtick',0:1:a,'ytick',0:1:b);
axis image xy
% 障碍物填充为黑色
for i=1:a/length-1
    for j=1:b/length-1
        if(J(i,j)==0)
            y=[i,i,i+1,i+1]*length;
            x=[j,j+1,j+1,j]*length;
            h=fill(x,y,'k');
            hold on
        end
    end
end
```

![img](https://img-blog.csdnimg.cn/f77657cb35564f60a59246665d9b7a3b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5peg5oSPMjEyMQ==,size_14,color_FFFFFF,t_70,g_se,x_16)

# 4 Dijkstra算法matlab实现

## 4.1 完善地图结构

将地图的周围一圈设置成障碍物，使得路径规划在地图内，因此需要将整个地图往x轴正方向移动一格，往y轴正方向移动一格，matlab代码如下

```ruby
%J(i,j)=0代表此处是障碍物，J(i,j)=1代表此无障碍物
%将地图整体向x轴正方向移动一格
for i=1:a/length-1
    for j=b/length-1:-1:1
        J(i,j+1)=J(i,j);
    end
end
%将地图整体向y轴正方向移动一格
for i=a/length-1:-1:1
    for j=2:b/length
        J(i+1,j)=J(i,j);
    end
end
%周围一圈设置成障碍
j=1;
for i=1:a/length+1
        J(i,j)=0;
end
j=a/length+1;
for i=1:a/length+1
        J(i,j)=0;
end
i=1;
for j=1:b/length+1
        J(i,j)=0;
end
i=b/length+1;
for j=1:b/length+1
        J(i,j)=0;
end
```

## 4.2 邻接矩阵

将障碍物信息存放到邻接矩阵这种数据结构下，令有障碍物的距离为0，无障碍物的距离就是原本的距离，代码如下

```bash
%将格子之间的距离放到邻接矩阵这种数据结构下
A=zeros((a/length+1)*(a/length+1),(a/length+1)*(a/length+1));#初始化全有障碍物
for i=2:a/length      #遍历出发节点
    for j=2:b/length  #遍历到达节点
        if J(i,j)==1  #出发节点不是障碍物
            if J(i-1,j)==1  #到达节点不是障碍物
                A((i-1)*(a/length+1)+j,(i-1-1)*(a/length+1)+j)=1;#将原本二维坐标
行行相连才能放入邻接矩阵
                A((i-1-1)*(a/length+1)+j,(i-1)*(a/length+1)+j)=1; 
            end
            if J(i+1,j)==1
                A((i-1)*(a/length+1)+j,(i+1-1)*(a/length+1)+j)=1;
                A((i+1-1)*(a/length+1)+j,(i-1)*(a/length+1)+j)=1;
            end
            if J(i,j-1)==1
                A((i-1)*(a/length+1)+j,(i-1)*(a/length+1)+j-1)=1;
                A((i-1)*(a/length+1)+j-1,(i-1)*(a/length+1)+j)=1;
            end
            if J(i,j+1)==1
                A((i-1)*(a/length+1)+j,(i-1)*(a/length+1)+j+1)=1;
                A((i-1)*(a/length+1)+j+1,(i-1)*(a/length+1)+j)=1;
            end
        end
    end
end
```

## 4.3 Dijkstra算法

Dijkstra算法实现起点到目标点的全局最优路径规划，在此过程中需要记录最短路径通过的节点，代码如下

```erlang
X=1;Y=1;%起点坐标
XX=X*(a/length+1)+Y+1;%起点在邻接矩阵中的顺序
X1=47;Y1=22;%终点坐标
%画出起点和终点
y=[Y,Y,Y+1,Y+1]*length;
x=[X,X+1,X+1,X]*length;
h=fill(x,y,'y'); 
hold on;
y=[X1,X1+1,X1+1,X1]*length;
x=[Y1,Y1,Y1+1,Y1+1]*length;
h=fill(x,y,'g');
hold on;
t=size(A);
t1=t(1);%获取总共节点个数
B=A(XX,:);%B矩阵代表中间节点走向任意一个节点的距离
C=[zeros(1,XX-1),1,zeros(1,t1-1-(XX-1))];%将C矩阵初始化，C矩阵代表是否已被访问
k=XX;%k代表中间点即上一轮设置为已访问的节点
k2=XX;%k2代表父节点
min=100000;
min2=0;
D(XX)=0;%链表起点指向0
while k~=X1*(a/length+1)+Y1+1   %不到终点不停止循环
        min=100000;
        for i=1:t1 
            if C(i)==0 %遍历未访问的节点
                if (((min2+B(i))<A(XX,i))||(A(XX,i)==0))&&(B(i)~=0)
                    %从中间点去往节点i必须无障碍物
                   A(XX,i)=min2+B(i);D(i)=k2;%更新距离
                end
                if (A(XX,i)<=min)&&(A(XX,i)~=0) %不断更新距离起点最近的节点k
                    min=A(XX,i);
                    k=i;
                end
            end
        end
    B=A(k,:);%循环一遍后节点k作为中间点
    C(k)=1;%设置节点k已访问
    k2=k;
    min2=min;%min2代表中间点到起点的距离
end
```

## 4.4 回溯

在记录了最短路径的节点后，这是用到了链表的结构，通过回溯得到最短路径通过的节点，并将最短路径标红显示，代码如下

```erlang
%回溯将最短路径节点顺序存在jg矩阵中
i=k;
jg=[k];
while D(i)
    jg=[D(i),jg];%存储最短路径通过的节点
    i=D(i); %通过链表回溯
end
%填充最短路径为红色
for i=1:size(jg,2)
    i1=fix((jg(i)-1)/(a/length+1));%将顺序排列的节点转换为原来的二维坐标
    j1=mod(jg(i)-1,(a/length+1))+1-1;
    y=[i1,i1,i1+1,i1+1]*length;
    x=[j1,j1+1,j1+1,j1]*length;
    h=fill(x,y,'r');  %将最短路径通过的节点填充为红色
    pause(0.03);
    hold on
end
```

# 5 完整版Dijkstra算法

## 5.1 动画

![img](https://img-blog.csdnimg.cn/16afc0bdcf8f4150bc5d5402303bdc54.gif)

 ![img](https://img-blog.csdnimg.cn/c88853221c2a47468cac212e6a5c5795.gif)

 ![img](https://img-blog.csdnimg.cn/613839b1b87f49579b71867e07ccd98d.gif)



## 5.2 完整源程序

**图像的处理**可以修改，起点终点也可以自行调整，动图速度也可以调整

```ruby
clc
clear all
Img=imread('C:\Autodesk\WI\ceshi.jpg');
Img = flipud(Img);%y坐标调换，由于读入图像时上下会反转
I = rgb2gray(Img);
a=50;b=50;% 设置网格格数 a表示横轴 b表示纵轴
length=1;%网格边长   
B = imresize(I,[a/length b/length]);%对图像进行矩阵化
J=floor(B/(255-60));
%不接近白色的都变为黑色，即变成障碍物，这里的60是可以调整的范围，不同的图像有不同的处理方法
hold on;grid on;%添加网格线
axis([0,a,0,b]);
% gca表示当前绘图区域
% xtick表示x轴坐标刻度，刻度为0到a，步进为1
set(gca,'xtick',0:1:a,'ytick',0:1:b);
axis image xy
% 障碍物填充为黑色
for i=1:a/length-1
    for j=1:b/length-1
        if(J(i,j)==0)
            y=[i,i,i+1,i+1]*length;
            x=[j,j+1,j+1,j]*length;
            h=fill(x,y,'k');
            hold on
        end
    end
end
%J(i,j)=0代表此处是障碍物，J(i,j)=1代表此无障碍物
%将地图整体向x轴正方向移动一格
for i=1:a/length-1
    for j=b/length-1:-1:1
        J(i,j+1)=J(i,j);
    end
end
%将地图整体向y轴正方向移动一格
for i=a/length-1:-1:1
    for j=2:b/length
        J(i+1,j)=J(i,j);
    end
end
%周围一圈设置成障碍
j=1;
for i=1:a/length+1
        J(i,j)=0;
end
j=a/length+1;
for i=1:a/length+1
        J(i,j)=0;
end
i=1;
for j=1:b/length+1
        J(i,j)=0;
end
i=b/length+1;
for j=1:b/length+1
        J(i,j)=0;
end
%将格子之间的距离放到邻接矩阵这种数据结构下
A=zeros((a/length+1)*(a/length+1),(a/length+1)*(a/length+1));#初始化全有障碍物
for i=2:a/length      #遍历出发节点
    for j=2:b/length  #遍历到达节点
        if J(i,j)==1  #出发节点不是障碍物
            if J(i-1,j)==1  #到达节点不是障碍物
                A((i-1)*(a/length+1)+j,(i-1-1)*(a/length+1)+j)=1;#将原本二维坐标
行行相连才能放入邻接矩阵
                A((i-1-1)*(a/length+1)+j,(i-1)*(a/length+1)+j)=1; 
            end
            if J(i+1,j)==1
                A((i-1)*(a/length+1)+j,(i+1-1)*(a/length+1)+j)=1;
                A((i+1-1)*(a/length+1)+j,(i-1)*(a/length+1)+j)=1;
            end
            if J(i,j-1)==1
                A((i-1)*(a/length+1)+j,(i-1)*(a/length+1)+j-1)=1;
                A((i-1)*(a/length+1)+j-1,(i-1)*(a/length+1)+j)=1;
            end
            if J(i,j+1)==1
                A((i-1)*(a/length+1)+j,(i-1)*(a/length+1)+j+1)=1;
                A((i-1)*(a/length+1)+j+1,(i-1)*(a/length+1)+j)=1;
            end
        end
    end
end
X=1;Y=1;%起点坐标
XX=X*(a/length+1)+Y+1;%起点在邻接矩阵中的顺序
X1=47;Y1=22;%终点坐标
%画出起点和终点
y=[Y,Y,Y+1,Y+1]*length;
x=[X,X+1,X+1,X]*length;
h=fill(x,y,'y'); 
hold on;
y=[X1,X1+1,X1+1,X1]*length;
x=[Y1,Y1,Y1+1,Y1+1]*length;
h=fill(x,y,'g');
hold on;
t=size(A);
t1=t(1);%获取总共节点个数
B=A(XX,:);%B矩阵代表中间节点走向任意一个节点的距离
C=[zeros(1,XX-1),1,zeros(1,t1-1-(XX-1))];%将C矩阵初始化，C矩阵代表是否已被访问
k=XX;%k代表中间点即上一轮设置为已访问的节点
k2=XX;%k2代表父节点
min=100000;
min2=0;
D(XX)=0;%链表起点指向0
while k~=X1*(a/length+1)+Y1+1   %不到终点不停止循环
        min=100000;
        for i=1:t1 
            if C(i)==0 %遍历未访问的节点
                if (((min2+B(i))<A(XX,i))||(A(XX,i)==0))&&(B(i)~=0)
                    %从中间点去往节点i必须无障碍物
                   A(XX,i)=min2+B(i);D(i)=k2;%更新距离
                end
                if (A(XX,i)<=min)&&(A(XX,i)~=0) %不断更新距离起点最近的节点k
                    min=A(XX,i);
                    k=i;
                end
            end
        end
    B=A(k,:);%循环一遍后节点k作为中间点
    C(k)=1;%设置节点k已访问
    k2=k;
    min2=min;%min2代表中间点到起点的距离
end
%回溯将最短路径节点顺序存在jg矩阵中
i=k;
jg=[k];
while D(i)
    jg=[D(i),jg];%存储最短路径通过的节点
    i=D(i); %通过链表回溯
end
%填充最短路径为红色
for i=1:size(jg,2)
    i1=fix((jg(i)-1)/(a/length+1));%将顺序排列的节点转换为原来的二维坐标
    j1=mod(jg(i)-1,(a/length+1))+1-1;
    y=[i1,i1,i1+1,i1+1]*length;
    x=[j1,j1+1,j1+1,j1]*length;
    h=fill(x,y,'r');  %将最短路径通过的节点填充为红色
    pause(0.03);
    hold on
end
```