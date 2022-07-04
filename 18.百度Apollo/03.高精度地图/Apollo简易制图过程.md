- [开发者说｜Apollo简易制图过程 (baidu.com)](https://baijiahao.baidu.com/s?id=1714747321689350734)

本文将主要从以下几个方面来介绍：

- 制图工具
- 预览录制的轨迹
- 生成好的地图
- 添加红绿灯信息
- 查看生成好的地图
- 完整制作地图过程

## 1 制图工具

制图工具在"apollo/modules/tools/map_gen"目录下，主要的文件如下：

![img](https://pics5.baidu.com/feed/cefc1e178a82b901dcbb9ee1c263fd7e3b12efc2.png?token=730f0e0983e8c5e862bd5e6e3a49dfc9)

制图的步骤如下：

1、先通过"extract_path.py"读取bag包中录取的车辆轨迹，原理是订阅"/apollo/localization/pose"中的消息，然后保存到文件；

2、然后通过"map_gen.py"读取生成的车辆轨迹，生成高精度地图，原理是把录制的轨迹当做车道中心线，进行采样，然后保存成Apollo的高精度地图格式。

同时再和大家介绍下其它的两个制作工具：

1、map_gen_single_lane.py 读取文件中的轨迹，生成一条车道线(Lane)，和"map_gen.py"的区别是，它只会生成single_lane。

2、map_gen_two_lanes_right_ext.py 读取文件中的轨迹，和map_gen_single_lane.py的区别是会生成两条车道线。

> 和map_gen的区别需要进一步研究

## 2 预览录制的轨迹

通过"extract_path.py"解压好轨迹之后，可以通过plot_path.py来可视化录制的轨迹。

```
python modules/tools/map_gen/plot_path.py path.txt
```

![img](https://pics0.baidu.com/feed/e850352ac65c10380d7af9c01fffc71ab17e89a7.png?token=98dbe54a2189eabf703071708dcb5618)

## 3 生成好的地图

进入容器，编译地图生成工具。

```
bazel build modules/tools/map_gen:all./bazel-bin/modules/tools/map_gen/map_gen path.txt
```

生成好的地图格式如下：

![img](https://pics5.baidu.com/feed/f603918fa0ec08fa75bd2096eb00696454fbdabd.png?token=92682b000ad11213c69ffd83ebf1f46a)

## 4 添加红绿灯信息

生成好地图之后，可以通过"add_signal.py"来添加红绿灯。

```
./bazel-bin/modules/tools/map_gen/add_signal.py map_file your_signal_map
```

这里map_file就是刚才生成的地图文件，而your_signal_map是红绿灯的文件，红绿灯文件实际上就是Apollo HDMap中指定的红绿灯格式，也就是需要按Apollo HDMap中指定的红绿灯格式保存红绿灯的数据，然后把它添加到map_file中。

## 5 查看生成好的地图

查看生成好的地图两种方式，一种是通过MapViewers，一种是通过MapShow，这里只验证了MapShow。

先在Apollo容器中编译MapShow。

```
bazel build modules/tools/mapshow:all
```

因为在容器中不能显示图形界面，因此在容器外运行以下命令，会提示如下错误：

```
root@in-dev-docker:/apollo# ./bazel-bin/modules/tools/mapshow/mapshow -m your_map_name  /apollo/./bazel-bin/modules/tools/mapshow/mapshow.runfiles/apollo/modules/tools/mapshow/mapshow.py:119: UserWarning: Matplotlib is currently using agg, which is a non-GUI backend, so cannot show the figure.  plt.show()
```

最后，在容器外的Apollo目录中执行。

```
./bazel-bin/modules/tools/mapshow/mapshow -m your_map_name -sl
```

看到如下画面便是生成好的高精度地图：

![img](https://pics6.baidu.com/feed/6159252dd42a28348d899130ef5b9de314cebfd2.png?token=b5b5244293dda74854313f904d49f2e8)

## 6 完整制作地图过程

以下是Python版本的制图过程，需要Apollo5.0之前的版本：

**1、解压制作好的地图**

```
python modules/tools/map_gen/extract_path.py points data/bag/20210406112554.record.00000
```

查看当前轨迹

```
python modules/tools/map_gen/plot_path.py points 
```

**2、生成地图**

```
python modules/tools/map_gen/map_gen.py points 
```

**3、查看地图**

```
python modules/tools/mapshow/mapshow.py -m map_points.txt
```

生成地图

```
python modules/tools/create_map/convert_map_txt2bin.py -i map_points.txt -o /apollo/modules/map/data/your_map_dir/base_map.bin
```

生成sim_map

```
./bazel-bin/modules/map/tools/sim_map_generator -map_dir=/apollo/modules/map/data/your_map_dir -output_dir=/apollo/modules/map/data/your_map_dir
```

生成routing_map

```
/apollo/bazel-bin/modules/routing/topo_creator/topo_creator -map_dir=/apollo/modules/map/data/your_map_dir --flagfile=modules/routing/conf/routing.conf
```

测试之前需要修改"vi modules/common/data/global_flagfile.txt"，屏蔽选项"--log_dir/--use_navigation_mode"

```
--map_dir=/apollo/modules/map/data/your_map_dir
```

测试生成的routing_map是否可以联通

```
python modules/tools/routing/debug_topo.py
```

> \* 《Apollo简易制图过程》
>
> https://zhuanlan.zhihu.com/p/358796415