- [【Apollo 6.0项目实战】HD-Map模块_Travis.X的博客-CSDN博客_apollo hdmap](https://blog.csdn.net/Travis_X/article/details/121486163)

# 前言

环境：

- Ubuntu 20.04
- Apollo 6.0
- LGSVL仿真器

## [Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) 6.0软件框架

![在这里插入图片描述](https://img-blog.csdnimg.cn/66d527b5163d444abd699023053db962.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

- Perception——感知模块识别自动驾驶汽车周围的环境。感知模块内部包含两个重要的子模块：障碍物检测和交通灯检测。
- Prediction——预测模块用来预测与感知障碍物未来的运动轨迹。
- Routing——路由模块告诉自动驾驶汽车通过全局路径到达目的地。
- Planning——规划模块规划自动驾驶汽车要采取的时空轨迹。
- Control——控制模块通过产生油门、刹车和转向等控制命令来执行计划的时空轨迹。
- CanBus —— CanBus 是将控制命令传递给车辆硬件的接口。它还将机箱信息传递给软件系统。
- **HD-Map——该模块类似于库。它不是发布和订阅消息，而是经常用作查询引擎支持，以提供关于道路的特定结构化信息。**
- Localization——定位模块利用各种信息源（如 GPS、LiDAR 和 IMU）来估计自动驾驶汽车的位置。
- HMI ——Apollo 中的人机界面或 DreamView 是用于查看车辆状态、测试其他模块和实时控制车辆功能的模块。
- Monitor——车辆中所有模块的监控系统，包括硬件。
- Guardian——新的安全模块，用于干预监控检测到的失败和action center相应的功能。 执行操作中心功能并进行干预的新安全模块应监控检测故障。
- Storytelling——隔离和管理复杂场景的新模块，创建可触发多个模块操作的Story。所有其他模块都可以订阅此特定模块。

本文讲解的是如何通过[数据集](https://so.csdn.net/so/search?q=数据集&spm=1001.2101.3001.7020)制作简易的高精地图（RelativeMap 和 Routing 地图）。RelativeMap 地图的制作实际是为车辆的行驶绘制一条参引线，并把参引线的数据主题发布出去，使用该主题的模块是 relative_map，relative_map 模块把参引线和计算出的车道线信息封装成主题发布，planning 模块订阅该主题信息进行路径规划。Routing 地图用于 routing 模块， routing 模块关注起点到终点的长期路径，根据起点到终点之间的道路，选择一条最优路径。

------

# 一、获取数据集

可以通过仿真或者现实环境来录制获取数据集，使用以下指令进行录制

```cpp
cyber_recorder record -a
```

本文以demo_3.5.record为例，可以直接在https://github.com/ApolloAuto/apollo/releases/download/v3.5.0/demo_3.5.record进行下载，下载好后放到/apollo/data/bag/文件夹。

------

# 二、RelativeMap 地图制作

终端上执行 docker start apollo_dev 启动 apollo 容 器 （如果是第一次创建并启动容器，需要执行 ./docker/scripts/dev_start.sh ）， 再执行./docker/scripts/dev_into.sh 进入 docker 容器。

进入容器后执行

```cpp
./bazel-bin/modules/tools/navigator/record_extractor data/bag/demo_3.5.record 
```

此时在当前目录下会产生一个路径文件，命名为path_demo_3.5.record.txt，内容如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/fa507cb851404bf889f3af7d9206414f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

运行以下指令查看生成的路径

```cpp
./bazel-bin/modules/tools/navigator/viewer_raw path_demo_3.5.record.txt 
```

**注意**：可能会出现以下警告提示

> /apollo/./bazel-bin/modules/tools/navigator/viewer_raw.runfiles/apollo/modules/tools/navigator/viewer_raw.py:43: UserWarning: Matplotlib is currently using agg, which is a non-GUI backend, so cannot show the figure.
> plt.show()

解决方法：

```cpp
sudo apt-get update
sudo apt-get install tcl-dev tk-dev python3-tk
```

重新执行./bazel-bin/modules/tools/navigator/viewer_raw path_demo_3.5.record.txt 后会

正常显示如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/da75f491e109444fa069fb2b7228fc3b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

对路径进行平滑处理，200 是平滑长度的参数，如果平滑失败，请尝试更改此参数以使

平滑通过。首选数字在 150 到 200 之间，处理后会产生一个新的路径文件path_demo_3.5.record.txt.smoothed。

```cpp
bash modules/tools/navigator/smooth.sh path_demo_3.5.record.txt 200
```

验证平滑处理结果的正确性

```cpp
./bazel-bin/modules/tools/navigator/viewer_smooth path_demo_3.5.record.txt  path_demo_3.5.record.txt.smoothed 
```

若s-theta 和s-s数据为空的话，则表示生成的数据不正常（如下图），解决方法可参考[这里](https://hub.fastgit.org/ApolloAuto/apollo/pull/13938/files)。

![在这里插入图片描述](https://img-blog.csdnimg.cn/baf94b241a7742c58df8f7090f06b2c9.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

修改之后再重新执行

```cpp
bash modules/tools/navigator/smooth.sh path_demo_3.5.record.txt 200

./bazel-bin/modules/tools/navigator/viewer_smooth path_demo_3.5.record.txt  path_demo_3.5.record.txt.smoothed 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/0c8335f228e44491b36d6324540ae041.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

接着开启新的终端，启动Apollo，在浏览器中访问 http://localhost:8888 显示 DreamView 界面

```cpp
./scripts/bootstrap.sh start
```

回放录制的数据包

```cpp
cyber_recorder play -f data/bag/demo_3.5.record --loop
```

将产生的路径文件作为主题发布出去，供 relative_map 模块订阅使用，命令如下：

```cpp
bash scripts/navigator.sh path_demo_3.5.record.txt.smoothed
```

完成后 DreamView 显示如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/e326eaf2f53e459c894a640e005b01f0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 三、Routing 地图制作

**base_map、sim_map 和 routing_map 的区别：**

- base_map是包含所有道路、车道几何形状和标签的最完整的地图。其他地图都是基于base_map。
- sim_map是 base_map 用于 Dreamview 可视化的轻量级版本，它降低了数据密度以获得更好的运行时性能。
- routing_map具有车道拓扑结构的base_map。

## 3.1 提取路径

获取地图数据采集时产生的数据包路径，执行后会在当前目录下生成名为demo_3.5.txt的文件

```cpp
./bazel-bin/modules/tools/map_gen/extract_path demo_3.5.txt data/bag/demo_3.5.record 
```

## 3.2 base_map

生成 base_map，输出的结果是以 map_开头的.txt 格式的虚拟车道线数据文件

```cpp
./bazel-bin/modules/tools/map_gen/map_gen demo_3.5.txt
```

## 3.3 sim_map

修改生成的虚拟车道线数据文件名称为base_map.txt，modules/map/data目录下创建demo_3.5文件夹，将base_map.txt放置到demo_3.5文件夹内再执行以下指令生成 sim_map。

```cpp
./bazel-bin/modules/map/tools/sim_map_generator --map_dir=modules/map/data/demo_3.5 --output_dir=modules/map/data/demo_3.5
```

**注意**：map_gen.py 是生成三条车道的虚拟车道线数据文件，要想生成一条车道和两条车道，请使用 map_gen_single_lane.py 和 map_gen_two_lanes_right_ext.py。

## 3.4 routing_map

routing_map 用于 routing 模块，如果没生成 routing_map，则无法正常使用 routing 模块。

```cpp
bash scripts/generate_routing_topo_graph.sh  --map_dir modules/map/data/demo_3.5
```

会生 routing_map.bin 和 routing_map.txt两个文件。
![在这里插入图片描述](https://img-blog.csdnimg.cn/76be2d220b8a48838b6c98c4170720bc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 3.5 DreamView 显示

启动Apollo后，在浏览器中访问 http://localhost:8888 显示 DreamView 界面，完成后 DreamView 显示如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/944be028402b49ac86bdafbc0f9335da.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 参考

【1】[Apollo详解之高精地图模块——相对地图模块](https://blog.csdn.net/weixin_49024732/article/details/118659068)

【2】[Apollo详解之地图模块———制作高精地图](https://blog.csdn.net/weixin_49024732/article/details/118862027?spm=1001.2014.3001.5501)

【3】[开发者说｜Apollo高精度地图离线制作](https://mp.weixin.qq.com/s/q6y-YbD7sDpAnYB6KzBAag)

【4】[开发者说｜Apollo简易制图过程](https://mp.weixin.qq.com/s/fItXKlWZ4Z5BkGQ9OptzRw)

【5】Apollo低速微型车自动驾驶套件软件使用手册