- [路径规划（一）：使用Matlab快速绘制栅格地图_Kevin的学习站的博客-CSDN博客_matlab 栅格图](https://blog.csdn.net/qq_44705488/article/details/121239045?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-1-121239045-blog-114262417.pc_relevant_multi_platform_whitelistv3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-1-121239045-blog-114262417.pc_relevant_multi_platform_whitelistv3&utm_relevant_index=2)

## 一、[Matlab](https://so.csdn.net/so/search?q=Matlab&spm=1001.2101.3001.7020)快速绘制栅格地图

> 声明：本文是学习古月居~[基于栅格地图的机器人路径规划算法指南 • 黎万洪 ](https://class.guyuehome.com/detail/p_6098db8ce4b071a81eb8befa/6)后写的笔记，好记性不如烂笔头！方便自己日后的巩固与复习，这个教程讲的很高，值得推荐！

### 1、几种常用的地图形式：

#### 1.1、尺度地图：

具有真实的物理尺度，如：栅格地图、特征地图、点云地图，常用于地图构建、定位、SLAM、小规模[路径规划](https://so.csdn.net/so/search?q=路径规划&spm=1001.2101.3001.7020)。

![img](https://img-blog.csdnimg.cn/img_convert/da4484d83002c17446fea9b84e122d9f.png#pic_center)

#### 1.2、拓扑地图：

不具备真实的物理尺度，只表示不同地点的连接关系和距离，如铁路网。

![img](https://img-blog.csdnimg.cn/img_convert/8de71835a5af06f4d40a05efb4686e93.png#pic_center)

#### 1.3、语义地图：

加标签的尺度地图，常用于人机交互。

![img](https://img-blog.csdnimg.cn/img_convert/15f2ada34ab8832053333b7a6b5692c9.png#pic_center)

### 2、栅格地图用于路径规划的优势：

> ①、可以将任意形状轮廓的地图，用足够精细的栅格进行绘制；
>
> ②、每一个栅格，可以通过不同颜色表示不同含义，如黑色代表障碍物、黄色代表起点、红色代表终点；
>
> ③、基于栅格地图进行路径规划有横、纵、斜三个规划方向。对应室内低速机器人可以完全按照路径行走；对于中高速机器人，可以考虑将路径进行平滑处理，以适用于非完全约束系统。

### 3、matlab绘制栅格地图的核心函数及思想：

#### 3.1、colormap函数：

> 为栅格地图创建自定义颜色。如黄色栅格代表起点、红色为终点。

#### 3.2、sub2ind函数：

将行列索引转为线性索引。对于右图栅格地图，10行1列，行从左上角自上而下排序，列从左上角自左向右排序。

![img](https://img-blog.csdnimg.cn/img_convert/ae98b3e171f42117ed04d3c431f0ae5c.png#pic_center)

#### 3.3、ind2sub函数：

> 与sub2ind相反，是将线性索引转为行列索引。

#### 3.4、为了在栅格地图呈现随机障碍物的效果，可以设置障碍物出现频率数值，根据该数据在所有栅格中生成[随机数](https://so.csdn.net/so/search?q=随机数&spm=1001.2101.3001.7020)，从而确定障碍物栅格。

#### 3.5、image函数：

利用colormap建立的颜色图，将数组信息显示为图像。

### 4、具体例子：

### 4.1、利用Matlab快速绘制栅格地图matlab代码：

```cpp
clc
clear
close all

%% 构建颜色MAP图
cmap = [1 1 1; ...       % 1-白色-空地
    0 0 0; ...           % 2-黑色-静态障碍
    1 0 0; ...           % 3-红色-动态障碍
    1 1 0;...            % 4-黄色-起始点 
    1 0 1;...            % 5-品红-目标点
    0 1 0; ...           % 6-绿色-到目标点的规划路径   
    0 1 1];              % 7-青色-动态规划的路径

% 构建颜色MAP图
colormap(cmap);

%% 构建栅格地图场景
% 栅格界面大小:行数和列数
rows = 10;
cols = 10; 

% 定义栅格地图全域，并初始化空白区域
field = ones(rows, cols);

% 障碍物区域
obsRate = 0.3;
obsNum = floor(rows*cols*obsRate);
obsIndex = randi([1,rows*cols],obsNum,1);
field(obsIndex) = 2;

% 起始点和目标点
startPos = 2;
goalPos = rows*cols-2;
field(startPos) = 4;
field(goalPos) = 5;

%% 画栅格图
image(1.5,1.5,field);
grid on;
set(gca,'gridline','-','gridcolor','k','linewidth',2,'GridAlpha',0.5);
set(gca,'xtick',1:cols+1,'ytick',1:rows+1);
axis image;
```

### 4.2、运行结构：

![img](https://img-blog.csdnimg.cn/img_convert/9c59342c1cfa4e8a7295187ce6974080.png#pic_center)

### 4.3、具体分析

1、

```cpp
clc
clear
close all
```

> clc：清除命令窗口的内容，对工作环境中的全部变量无任何影响
> close：关闭当前的Figure窗口
> close all:关闭所有的Figure窗口
> clear：清除工作空间的所有变量
> clear all：清除工作空间的所有变量，函数，和MEX文件

2、自定义构建颜色MAP图

```cpp
%% 构建颜色MAP图
cmap = [1 1 1; ...       % 1-白色-空地
    0 0 0; ...           % 2-黑色-静态障碍
    1 0 0; ...           % 3-红色-动态障碍
    1 1 0;...            % 4-黄色-起始点 
    1 0 1;...            % 5-品红-目标点
    0 1 0; ...           % 6-绿色-到目标点的规划路径   
    0 1 1];              % 7-青色-动态规划的路径
```

运行后会建立对应变量的MAP图值

![img](https://img-blog.csdnimg.cn/img_convert/fb8a170436e5352420b1eaf6e74ab64c.png#pic_center)

3、colormap构建颜色MAP图

```cpp
% 构建颜色MAP图
colormap(cmap);
```

4、构建栅格地图场景：大小

```cpp
%% 构建栅格地图场景
% 栅格界面大小:行数和列数
rows = 10;
cols = 10; 
```

5、定义栅格地图全域，并初始化空白区域

```cpp
% 定义栅格地图全域，并初始化空白区域
field = ones(rows, cols);
```

将栅格图全设置为白色

![img](https://img-blog.csdnimg.cn/img_convert/765ff985b6d7f185de2f047f063ce9e8.png#pic_center)

6、障碍物区域

```cpp
% 障碍物区域
obsRate = 0.3;
obsNum = floor(rows*cols*obsRate);
obsIndex = randi([1,rows*cols],obsNum,1);
field(obsIndex) = 2;
```

> obsRate = 0.3;%障碍物占比率
>
> obsNum = floor(rows*cols*obsRate);%计算障碍物个数；
>
> obsIndex = randi([1,rows*cols],obsNum,1);%随机设置障碍物

得到障碍物的区域

![img](https://img-blog.csdnimg.cn/img_convert/e8006b7e2f7e8fa96c96056917f955d4.png#pic_center)

7、起始点和目标点

起始点和目标点要定义在障碍物后面，不然可能会和障碍物冲突

```cpp
% 起始点和目标点
startPos = 2;
goalPos = rows*cols-2;
field(startPos) = 4;
field(goalPos) = 5;
```

> startPos = 2; &起点
> goalPos = rows*cols-2;%终点
> field(startPos) = 4; %起点为黄色
> field(goalPos) = 5; %终点为红色

8、画栅格图

```cpp
%% 画栅格图
image(1.5,1.5,field);
grid on;set(gca,'gridline','-','gridcolor','k','linewidth',2,'GridAlpha',0.5);
set(gca,'xtick',1:cols+1,'ytick',1:rows+1);
axis image;
```

> image(1.5,1.5,field);%画出颜色图
>
> ![img](https://img-blog.csdnimg.cn/img_convert/56d24793ff89785c95d20ecc5a432a32.png#pic_center)
>
> grid on;% 画出网格
>
> ![img](https://img-blog.csdnimg.cn/img_convert/e47ac016fd44492d3247c758e7cab077.png#pic_center)
>
> set(gca,‘gridline’,’-’,‘gridcolor’,‘k’,‘linewidth’,2,‘GridAlpha’,0.5); % 设置网格为：网格线实线、颜色黑色、线宽2、透明度0.5；
>
> ![img](https://img-blog.csdnimg.cn/img_convert/d3def0a2a5e6df54b0c0bd41a793ba86.png#pic_center)
>
> set(gca,‘xtick’,1:cols+1,‘ytick’,1:rows+1); % 设置长宽高为一样~正方形
> axis image;

![img](https://img-blog.csdnimg.cn/img_convert/f1cdac2831dd14f1d5fcfa5d6fd1212c.png#pic_center)