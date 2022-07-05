- [自动驾驶模拟器Carla Sensor（六） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/484743284)

## 1.Carla支持的Sensor种类

- [Collision detector](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23collision-detector) ：碰撞检测。当车辆碰撞到其他物体时，会触发sensor发送数据。
- [Lane invasion detector](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23lane-invasion-detector) ：压线检测。当车轮压到车道边界线时，会触发sensor发送数据。
- [Obstacle detector](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23obstacle-detector) ：障碍物检测。车辆周围障碍物识别。
- [Depth camera](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23depth-camera) ：深度相机。图像带有“深度”,“深度”指图像中的物体距离相机的远近。
- [RGB camera](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23rgb-camera) ：RGB相机。普通摄像头。
- [Semantic segmentation camera](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23semantic-segmentation-camera) ：语义分割相机。识别的每种类型数据用不同颜色表示。
- [DVS camera](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23dvs-camera) :事件相机。当某个像素的亮度变化累计达到一定阈值后，输出一个事件。
- [Optical Flow camera](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23optical-flow-camera) ：光流照相机。 通过连续的图像序列来检测物体微小的动作变化。
- [LIDAR sensor](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23lidar-sensor) ：激光雷达。提供雷达点云数据。
- [Semantic LIDAR sensor](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23semantic-lidar-sensor) ：语义激光雷达。和激光雷达类似，点云数据中包含了更多数据。
- [Radar sensor](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23radar-sensor) ：毫米波雷达。提供检测到的物体相对于毫米波雷达的速度，距离，角度等。
- [GNSS sensor](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23gnss-sensor) ：全球卫星导航系统。提供车辆经纬度，高度。
- [IMU sensor](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23imu-sensor) ：惯性传感器。提供加速度传感器，陀螺仪等数据。
- [RSS sensor](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23rss-sensor) ：责任敏感安全传感器。集成了[C++ Library for Responsibility Sensitive Safety](https://link.zhihu.com/?target=https%3A//github.com/intel/ad-rss-lib)

Carla Sensor官方接口说明文档：[https://carla.readthedocs.io/en/latest/ref_sensors/](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/)

**点击上面sensor名字的超链接可以直达该sensor接口说明文档。**

Sensor分两种类型，一种是事件触发发送数据的，例如压线检测，只有当车轮压到车道线时才会触发；一种是连续更新数据的，一般是周期性发送数据。以detector结尾的是事件触发类型的Sensor，在代码中对应/LibCarla/source/carla/sensor/data/XXXEvent.h；其他是连续发送数据类型的Sensor，在代码中对应/LibCarla/source/carla/sensor/data/XXXMeasurement.h。按照server和client分，[Lane invasion detector](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/ref_sensors/%23lane-invasion-detector) 是在Client端实现的，其余Sensor 在server端实现。Sensor 使用 UE坐标系统右手定则，x向前，y向右，z向上。

## 2.Sensor 类图

此类图是使用doxygen生成的。

安装doxygen graphviz

```text
sudo apt-get install doxygen graphvi
```

在carla代码根木下执行doxygen，根目录下会生成Doxygen目录，在这个目录下找到index.html,就是生成文档的主页。 如果不想自己生成文档，官网有生成完的文档[http://carla.org/Doxygen/html/index.html](https://link.zhihu.com/?target=http%3A//carla.org/Doxygen/html/index.html)

![img](https://pic1.zhimg.com/80/v2-a6569ca8f1b269f47de4d5e34eedb49c_720w.jpg)

所有的Sensor都从AActor继承而来。一个Sensor本质上就是一个Actor。Sensor的实现部分在AXXXSenosr类里面，对应的代码位置在/Unreal/CarlaUE4/Plugins/Carla/Source/Carla/Sensor/中。