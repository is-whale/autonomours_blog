- [自动驾驶模拟器Carla高精度地图（五） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/481898230)

## 1.地图格式

### 1.1 OpenDRIVE

OpenDRIVE是一个开源的地图格式，由ASAM发布。OpenDRIVE地图文件扩展名是.xodr,使用XML语法描述路网的文件。OpenDRIVE中存储了道路，车道，路标，信号灯等数据。

OpenDRIVE官网

[https://www.asam.net/standards/detail/opendrive/](https://link.zhihu.com/?target=https%3A//www.asam.net/standards/detail/opendrive/)

Carla使用的是OpenDRIVE 1.4格式。OpenDRIVE 1.4格式由VIRES Simulationtechnologie GmbH公司发布，ASAM没有公开，需要自己注册账号才能获取。OpenDRIVE 1.4以后的格式，ASAM都公开了。

### 1.2 OpenMapStreet

OpenStreetMap是一个由用户共同合作创造世界自由编辑的地图项目。该项目于2004年由Steve Coast在英国创立的，其灵感来自于维基百科，它的优势在于拥有英国和其他地方的专有地图数据。此后，它已发展成为拥有超过100万使用GPS设备，航空摄影和其他自由来源收集数据的注册用户。该网站是由英国注册的非盈利性组织OpenStreetMap基金会支持。

OpenMapStreet官网

[https://www.openstreetmap.org/#map=5/35.588/134.380](https://link.zhihu.com/?target=https%3A//www.openstreetmap.org/%23map%3D5/35.588/134.380)

Carla支持直接导入OpenMapStreet格式的地图。OpenMapStreet地图文件扩展名是.osm。Carla也支持把OpenMapStreet转换成OpenDRIVE格式。

详细步骤参考：[https://carla.readthedocs.io/en/latest/tuto_G_openstreetmap/#adv_opendrive.md](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/tuto_G_openstreetmap/%23adv_opendrive.md)

## 2.地图导入

Carla支持多种方式导入地图。

![img](https://pic4.zhimg.com/80/v2-a7be154074f31980aa617c830b8501e7_720w.jpg)

**有需要上面这个思维导图的小伙伴请关注并私信我。**

### 2.1 自定义地图

自定义地图可以使用RoadRunnner。RoadRunner是Mathworks的自动驾驶仿真软件，可用于针对自动驾驶系统仿真和测试设计三维场景，可以创建区域特定的道路标志和标记以自定义道路场景，可以插入路标、信号灯、护栏和建筑物等三维模型，支持激光雷达点云、航拍图像和 GIS 数据的可视化，可以使用 OpenDRIVE® 导入和导出道路网络，导出的场景可在自动驾驶仿真器和游戏引擎中使用，包括 CARLA、Vires VTD、NVIDIA DRIVE Sim®、百度 Apollo®、Cognata, Unity®、和虚幻引擎 (Unreal® Engine)。

RoadRunner官网

[https://www.mathworks.com/products/roadrunner.html](https://link.zhihu.com/?target=https%3A//www.mathworks.com/products/roadrunner.html)

RoadRunner可以导出OpenDRIVE格式的地图，.xodr 和.fbx文件。RoadRunner 也提供了RoadRunner plugin，方便carla导入地图。

RoadRunner是付费软件，我没有使用。有RoadRunner的小伙伴可以参考下面链接创建地图：

[https://carla.readthedocs.io/en/latest/tuto_M_generate_map/](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/tuto_M_generate_map/)

RoadRunner导出的 xodr 和.fbx文件，导入Carla后，可以使用Unreal Engine editor进一步编辑，包括分层地图，交通灯，交通标志，天气等。Unreal Engine editor编辑完以后就可以生成步行导航了。

### 2.2 导入自定义地图

安装Carla的方式决定了导入地图的方式。安装Carla package (binary) 版本和编译Carla源代码后生成的版本导入地图的方式是不同的。

安装Carla package (binary) 版本只能使用docker环境导入，且不能使用Unreal Engine editor编辑地图。

具体步骤参考：[https://carla.readthedocs.io/en/latest/tuto](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/tuto)

编译Carla源代码后生成的版本导入地图有三种方法。

A 使用make import。

我使用了这种方式。

B RoadRunner plugin。

付费软件，我没有使用。有RoadRunner的小伙伴可以参考下面链接给Carla导入地图。

[https://carla.readthedocs.io/en/latest/tuto_M_add_map_alternative/](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/tuto_M_add_map_alternative/)

C 手动导入。

详细步骤参考下面链接：

[https://carla.readthedocs.io/en/latest/tuto_M_add_map_alternative/#manual-import](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/tuto_M_add_map_alternative/%23manual-import)

### 2.3 OpenStreetMap

Carla可以直接导入OpenStreetMap文件.osm,也可以把OpenStreetMap转成OpenDRIVE格式。