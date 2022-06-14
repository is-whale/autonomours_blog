- [【Apollo 6.0项目实战】Dreamview的使用_Travis.X的博客-CSDN博客_dreamview](https://blog.csdn.net/Travis_X/article/details/120949045)

# 前言

![在这里插入图片描述](https://img-blog.csdnimg.cn/073df8cd13d740b7926a5c5d0d04f5fe.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

DreamView 是一个网络应用程序，提供如下的功能：

1. 可视化显示当前自动驾驶汽车模块的输出信息，例如，规划路径、车辆定位、车架信息等；
2. 为用户提供人机交互接口以监测车辆硬件状态，对模块进行开关操作，启动自动驾驶车辆等；
3. 提供调试功能，例如PnC功能工具的有效跟踪模块输出。

------

# 一、界面布局和特性

该应用程序的界面被划分为多个区域：标题、侧边栏、主视图和工具视图。

## 标题

标题可以下拉4个列表，可以像下面的图片所示进行操作：
![在这里插入图片描述](https://img-blog.csdnimg.cn/4254eeb65f224b50b474034fbf7f3364.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
附注：导航模块是在阿波罗 2.5 版本引入的满足目标测试的特性。

------

# 二、侧边栏和工具视图

侧边栏控制着显示在工具视图中的模块
![在这里插入图片描述](https://img-blog.csdnimg.cn/b5b172d4a8b446098244d3ecad45e8ac.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 1. Tasks

在DreamView中用户可以操作的任务有：
![在这里插入图片描述](https://img-blog.csdnimg.cn/9dd0863cd55d452c9cf31bc59c2ac4c1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

- Quick Start: 当前选择的模式支持的指令。通常情况下，

  setup: 开启所有模块

  reset all: 关闭所有模块

  start auto: 开始车辆的[自动驾驶](https://so.csdn.net/so/search?q=自动驾驶&spm=1001.2101.3001.7020)

- Others: 工具经常使用的开关和按钮

- Module Delay: 从模块中输出的两次事件的时间延迟

- Console: 从[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020)平台输出的监视器信息

## 2. Module Controller

监视硬件状态和对模块进行开关操作
![在这里插入图片描述](https://img-blog.csdnimg.cn/8b9e3685ae3f4d048c0286c7b4b21265.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 3.Layer Menu

显式控制元素是否显示的开关
![在这里插入图片描述](https://img-blog.csdnimg.cn/9c3b0d03b5004c0d80fea7f01c7a0d6c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 4.Route Editing

在向Routing模块发送寻路信息请求前可以编辑路径信息的[可视化](https://so.csdn.net/so/search?q=可视化&spm=1001.2101.3001.7020)工具
![在这里插入图片描述](https://img-blog.csdnimg.cn/24b6dc61fe674d26b5b6de0afaebcccd.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 5.Data Recorder

![在这里插入图片描述](https://img-blog.csdnimg.cn/d7f4718c0f174dfcb0c0f03726944b39.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 6.Default Routing

预先定义的路径或者路径点，该路径点所在的兴趣点（POI）。
![在这里插入图片描述](https://img-blog.csdnimg.cn/3a6a7b600a0044f292aa173c6acc833c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
如果打开了路径编辑模式，路径点可被显式的在地图上添加。

如果只选择了一个点，那么寻找路请求的起点是自动驾驶车辆的当前点。点击一个目的地的POI会向服务器发送一次寻路请求。路径点中的第一个点。

查看地图目录下的default_end_way_point.txt文件可以编译POI信息。例如，如果选择的地图模式为“Demo”，则modules/map/data/demo可以在目录下查看对应的default_end_way_point.txt文件。

------

# 三、主视图

主视图在web页面中以动画的方式展示3D计算机图形
![在这里插入图片描述](https://img-blog.csdnimg.cn/5ae8acfd708a441a837da26e0d2f8b85.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
下表列举了主视图中各个元素：
![在这里插入图片描述](https://img-blog.csdnimg.cn/da7aa1d1be404f48b7e41d38ce8ef113.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_14,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 障碍物

![在这里插入图片描述](https://img-blog.csdnimg.cn/7b9047ef1aea426695fc2dfec670bbef.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_17,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 规划决策

### 探栅栏区

决策栅栏区显示了Planning模块对车辆障碍物做出的决策。每种类型的决策会表示为不同的颜色和图标，如下图所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/fceb9351468347e9a2ee5a7bb4ec8bed.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_13,color_FFFFFF,t_70,g_se,x_16#pic_center)

线路变更是一个特殊的决策，因此不显示决策栅栏区，而是将路线变更的图标显示在车辆上。

![在这里插入图片描述](https://img-blog.csdnimg.cn/e20eb71f87714a2688f0e5ae3c3a697b.png#pic_center)
在优先通行的规则下，当在交叉路口的停车标志处做出让行决策时，被让行的物体在头顶会显示让行图标

![在这里插入图片描述](https://img-blog.csdnimg.cn/135c47a54fef4735a45de30d27edcf8d.png#pic_center)

### 停止原因

如果显示了停止决策栅栏区，则停止原因展示在停止图标的右侧。可能的停止原因和对应的图标为：
![在这里插入图片描述](https://img-blog.csdnimg.cn/f49a8676fed5419bae65d95b6664d762.png#pic_center)

## 视图

可以在主视图中展示多种从Layer Menu选择的视图模式：

![在这里插入图片描述](https://img-blog.csdnimg.cn/b64c4b81f97246ea906787f5459160c7.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 四、快捷键

| 快捷键 | 描述                        |
| ------ | --------------------------- |
| 1      | 切换到Task面板              |
| 2      | 切换到Module Controller面板 |
| 3      | 切换到Layer Menu 面板       |
| 4      | 切换到Route Editing面板     |
| 5      | 切换到Data Recorder面板     |
| 6      | 切换到Audio Capture面板     |
| 7      | 切换到Default Routing面板   |
| v      | 旋转视角选项                |

------

# 五、PnC Monitor

查看监视器：
构建 Apollo 并在Web 浏览器上运行 Dreamview
从“Others”面板打开“PNC Monitor”。
在右侧，应该能够查看计划、控制、延迟图，如下所示
![在这里插入图片描述](https://img-blog.csdnimg.cn/d9e83e75800649ad8adf51cb30d618e0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## Planning/Control Graphs

监视器中的Planning/Control Graphs绘制了各种图表来反映其模块的内部状态。

### 规划模块的自定义图表

[Planning_internal.proto](https://github.com/ApolloAuto/apollo/blob/master/modules/planning/proto/planning_internal.proto#L180)是一个存放调试信息的protobuf，由dreamview server 处理后发送到dreamview client 以帮助工程师调试。对于想要为新的规划算法绘制自己的图形的用户：

1、填写[Planning_internal.proto](https://github.com/ApolloAuto/apollo/blob/master/modules/planning/proto/planning_internal.proto#L180)中定义的“图表”的信息。

2、X/Y 轴： [chart.proto](https://github.com/ApolloAuto/apollo/blob/master/modules/dreamview/proto/chart.proto) 具有您可以为轴设置的“选项”，其中包括

- min/max: 最小/大刻度
- label_string：轴标签
- Legend_display：显示或隐藏图表图例。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/93eb5303269f4eca8301e162a4edbd80.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

3、数据集：

- 类型：每个图形可以有多个在 [chart.proto](https://github.com/ApolloAuto/apollo/blob/master/modules/dreamview/proto/chart.proto)中定义的线、多边形和/或汽车标记：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/2412d0b6b0f34a53b03559e82a39e6ba.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center)
- 标签：每个数据集必须有一个唯一的“标签”给每个图表，以帮助 Dreamview 识别要更新的数据集。
- 属性：对于多边形和线，可以设置样式。Dreamview 使用Chartjs.org制作图表。下面是常见的：

| 名称        | 描述             | 列子                    |
| ----------- | ---------------- | ----------------------- |
| color       | 线的颜色         | RGBA(27, 249, 105, 0.5) |
| borderWidth | 线宽             | 2                       |
| pointRadius | 点的半径         | 1                       |
| fill        | 是否填充线下区域 | false                   |
| showLine    | 是否画线         | true                    |

有关更多属性，请参阅https://www.chartjs.org/docs/latest/charts/line.html。

- 示例：您可以查看 [on_lane_planning.cc](https://github.com/ApolloAuto/apollo/blob/master/modules/planning/on_lane_planning.cc#L562)以获取代码示例。

### 其他规划路径

对于想要在dreamview 3D 场景中渲染额外路径的用户，将所需的路径添加到 [planning_internal.proto](https://github.com/ApolloAuto/apollo/blob/master/modules/planning/proto/planning_internal.proto#L164)中的“path”字段。当 PnC 监视器打开时，将呈现这些路径：
![在这里插入图片描述](https://img-blog.csdnimg.cn/1c831409ebda4bb3995c5f9f85cb8607.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
Dreamview 为前四条路径预定义了样式：

| 特性    | 路径1    | 路径1    | 路径1    | 路径1    |
| ------- | -------- | -------- | -------- | -------- |
| width   | 0.8      | 0.15     | 0.4      | 0.65     |
| color   | 0x01D1C1 | 0x36A2EB | 0x8DFCB4 | 0xD85656 |
| opacity | 0.65     | 1        | 0.7      | 0.8      |
| zOffset | 4        | 7        | 6        | 5        |

如果要渲染的路径超过四条或想要更改样式，请编辑 [dreamview/frtonend/dist/parameters.json](https://github.com/ApolloAuto/apollo/blob/master/modules/dreamview/frontend/dist/parameters.json)中的 Planning.pathProperties 值 。

## Latency graph

该图显示了模块接收传感器输入数据与发布该数据的时间差。
![在这里插入图片描述](https://img-blog.csdnimg.cn/a6a153a46fa344179c9854c88456d135.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

延迟图可用于跟踪每个人面临的延迟。图表的颜色不同以帮助区分模块，并包含一个键以便更好地理解。该图绘制为以毫秒为单位测量的延迟与以秒为单位的时间戳测量，如下图所示。

![在这里插入图片描述](https://img-blog.csdnimg.cn/40e7b31caf1f4c58af31baa0e05941dd.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)