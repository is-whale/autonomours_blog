- [apollo简易制图过程(二十) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/358796415)

之前介绍了高精度地图制作和采集流程，正常的制图流程比较繁琐，这里主要介绍下apollo的简易制图过程，**只需要rtk定位**就可以制作一个简单的高精度地图。下面主要分享下制作过程。

## 制图工具

制图工具在"apollo/modules/tools/map_gen"目录下，主要的文件如下：

![img](https://pic3.zhimg.com/80/v2-0810dce12af98119c8851aa39632ed9a_720w.jpg)

制图的步骤如下

1. 先通过"extract_path.py"读取bag包中录取的车辆轨迹，原理是订阅"/apollo/localization/pose"中的消息，然后保存到文件。
2. 然后通过"map_gen.py"读取生成的车辆轨迹，生成高精度地图，原理是把录制的轨迹当做车道中心线，进行采样，然后保存成Apollo的高精度地图格式。

下面再介绍下其它的2个制作工具

map_gen_single_lane.py 读取文件中的轨迹，生成1条车道线(lane)，和"map_gen.py"的区别是，它只会生成single_lane。

map_gen_two_lanes_right_ext.py 读取文件中的轨迹，和map_gen_single_lane.py的区别是会生成2条车道线。

> 和map_gen的区别需要进一步研究

## 预览录制的轨迹

通过"extract_path.py"解压好轨迹之后，可以通过plot_path.py来可视化录制的轨迹。

```text
python modules/tools/map_gen/plot_path.py path.txt
```

![img](https://pic4.zhimg.com/80/v2-5840c382de64e87c9c1f45a273376b77_720w.jpg)



## 生成好的地图

进入容器，编译地图生成工具。

```text
bazel build modules/tools/map_gen:all
./bazel-bin/modules/tools/map_gen/map_gen path.txt
```

生成好的地图格式如下

![img](https://pic4.zhimg.com/80/v2-7fd0c8aad3e72862da19616889c13ec7_720w.jpg)



## 添加红绿灯信息

生成好地图之后，可以通过"add_signal.py"来添加红绿灯。

```text
./bazel-bin/modules/tools/map_gen/add_signal.py map_file your_signal_map
```

这里map_file就是刚才生成的地图文件，而your_signal_map是红绿灯的文件，红绿灯文件实际上就是apollo hdmap中指定的红绿灯格式，也就是说你需要按照apollo hdmap中指定的红绿灯格式保存红绿灯的数据，然后把它添加到map_file中。

## 查看生成好的地图

查看生成好的地图有2种方式，一种是通过mapviewers，一种是通过mapshow，这里只验证了mapshow。

先在apollo容器中编译mapshow。

```text
bazel build modules/tools/mapshow:all
```

因为在容器中不能显示图形界面，因此在容器外运行以下命令，会提示如下错误：

```text
root@in-dev-docker:/apollo# ./bazel-bin/modules/tools/mapshow/mapshow -m your_map_name  
/apollo/./bazel-bin/modules/tools/mapshow/mapshow.runfiles/apollo/modules/tools/mapshow/mapshow.py:119: UserWarning: Matplotlib is currently using agg, which is a non-GUI backend, so cannot show the figure.
  plt.show()
```

最后，在容器外的apollo目录中执行

```text
./bazel-bin/modules/tools/mapshow/mapshow -m your_map_name -sl
```

就可以看到如下画面了，就是生成好的高精度地图。

![img](https://pic1.zhimg.com/80/v2-0ce12ffb2a1b2c5af1bb8d178e5f53b8_720w.jpg)



## 完整制作地图过程

以下是python版本的制图过程，需要apollo5.0之前的版本

1. 解压制作好的地图

```text
python modules/tools/map_gen/extract_path.py points data/bag/20210406112554.record.00000
```

查看当前轨迹

```text
python modules/tools/map_gen/plot_path.py points 
```

2. 生成地图

```python3
python modules/tools/map_gen/map_gen.py points 
```

3. 查看地图

```text
python modules/tools/mapshow/mapshow.py -m map_points.txt
```

生成地图

```text
python modules/tools/create_map/convert_map_txt2bin.py -i map_points.txt -o /apollo/modules/map/data/your_map_dir/base_map.bin
```

生成sim_map

```text
./bazel-bin/modules/map/tools/sim_map_generator -map_dir=/apollo/modules/map/data/your_map_dir -output_dir=/apollo/modules/map/data/your_map_dir
```

生成routing_map

```text
./bazel-bin/modules/routing/topo_creator/topo_creator -map_dir=/apollo/modules/map/data/your_map_dir --flagfile=modules/routing/conf/routing.conf
```

> 修改--map_dir=/apollo/modules/map/data/your_map_dir

测试之前需要修改"vi modules/common/data/global_flagfile.txt"，屏蔽选项"--log_dir/--use_navigation_mode"

```text
--map_dir=/apollo/modules/map/data/your_map_dir
```

测试生成的routing_map是否可以联通

```python3
python modules/tools/routing/debug_topo.py
```