- [【Apollo 6.0项目实战】LGSVL 高精地图使用教程_Travis.X的博客-CSDN博客](https://blog.csdn.net/Travis_X/article/details/121625950)

# 言

环境：

- Ubuntu 20.04
- Apollo 6.0
- LGSVL仿真器

本文讲解的是如何使用 LGSVL [仿真器](https://so.csdn.net/so/search?q=仿真器&spm=1001.2101.3001.7020)提供的高精地图，并实现车辆在特定场景下的自动驾驶。

------

# 一、下载 HD Map

打开[LGSVL 官网](https://www.svlsimulator.com/)，选择希望采用的地图（以Highway101GLE为例），点击右下角眼睛图标即可查看整幅高精地图，点击下载对应版本的 HD Map。
![在这里插入图片描述](https://img-blog.csdnimg.cn/50f018c2694d4ad2ac5eeec729823c94.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/ce679e10a9ed4927bc02a9564f93a016.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 二、sim_map 和 routing_map 地图制作

下载的高精地图只有一个 base_map.bin 文件。base_map是包含所有道路、车道几何形状和标签的最完整的地图。为了在 Dreamview 上可视化高精地图，我们需要生成 sim_map，另外为了通过Routing 模块实现全局路径生成，我们还需要生成具有车道拓扑结构的 routing_map 用作全局路径规划。

在 modules/map/data目录下创建 Highway101GLE文件夹，将base_map.bin 文件放置到Highway101GLE。

## 2.1 [Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) 启动

```cpp
cd apollo/

./docker/scripts/dev_start.sh

./docker/scripts/dev_into.sh

```

## 2.2 sim_map 生成

执行以下指令

```cpp
./bazel-bin/modules/map/tools/sim_map_generator --map_dir=modules/map/data/Highway101GLE --output_dir=modules/map/data/Highway101GLE
```

## 2.3 routing_map 生成

bash scripts/generate_routing_topo_graph.sh --map_dir modules/map/data/Highway101GLE

正常情况下Highway101GLE文件夹下会包含五个文件。
![在这里插入图片描述](https://img-blog.csdnimg.cn/52dc300a72e44fecae0473c1d8f6422a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 三、LGSVL 与 Apollo 6.0 联合仿真

执行以下指令，实现 Apollo 与 LGSVL 的桥接。

```cpp
./scripts/bootstrap_lgsvl.sh

./scripts/bridge.sh
```

打开Dreamview http://localhost:8888/，在上方选择对应的模式、车型以及地图（Highway101GLE）。

启动LGSVL仿真器后，Dreamview 打开 Localization 、 Control、 Perception、Prediction、Routing、Planning 以及 Transform 模块，即可观察到可视化结果。
![在这里插入图片描述](https://img-blog.csdnimg.cn/97bb3782c7904481b1b8c7a2464e1f10.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------