- [apollo高精度地图制作(二十三) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/374472428)

这里主要分享下apollo高精度地图中点云地图的生成方式，采用的激光雷达是速腾32线激光雷达，apollo 5.0版本，**离线建图**的方式，后续会更新在线建图的方式，文章最后总结了建图过程中的一些问题和思考。

首先，apollo高精度地图中离线建图的过程一共包括3个步骤。

- **数据采集**
- **数据预处理**
- **建图**

## **数据采集**

数据采集主要是采集imu和点云的数据，如果有组合导航输出就更好了。正常开车采集数据，数据大概是1分钟800M左右。

使用如下命令录制数据包

```text
cyber_recorder record -c imu_topic localization_pose_topic lidar_topic
```

## 数据预处理

采集完bag包之后，通过以下命令解压其中的PCD文件（Lidar数据）和pose姿态，解压之后可以使用

```text
pcl_viewer xxx.pcd
```

来查看生成的点云。按数字键5可以查看点云的强度渲染效果。

解压bag包的命令如下

```text
./bazel-bin/modules/localization/msf/local_tool/data_extraction/cyber_record_parser
--bag_file=data/bag/xxx.record.00000 --cloud_topic=/apollo/sensor/velodyne64/compensator/PointCloud2
--out_folder=data/pcd
```

解压完成之后，如果是60s的bag包，会解压出来600帧点云和几个pose.txt文件。

接下来我们需要根据点云的时间戳获取当前点云的精确pose，由于点云的时间和IMU不是完全对应，还需要对imu的pose进行插值，得到优化后的pose。

- **input_poses_path** 上一步解压的pose文件
- **extrinsic_path** IMU到lidar的坐标转换参数文件
- **output_poses_path** 优化后的pose文件

```text
./bazel-bin/modules/localization/msf/local_tool/map_creation/poses_interpolator 
--input_poses_path=data/pcd/xx.txt --extrinsic_path=xxx 
--output_poses_path=data/pcd/pose.txt
```

> 注意这里插值的时候把imu的坐标转化到lidar的坐标了，其实也可以不必做这一步转化，后续可以优化下

## 建图

之后根据点云文件和优化后的pose文件通过NDT配准就可以得到生成好的点云地图，配准的过程是把当前帧的点云拼接到前一帧上。

![img](https://pic4.zhimg.com/80/v2-befba5c447def74b7bf60a891516f8fb_720w.jpg)

然后不断重复上述过程，得到拼接好的地图。最后制作好的效果如下图，开源代码在[ndt_mapping](https://link.zhihu.com/?target=https%3A//github.com/daohu527/ndt_mapping)。

![img](https://pic4.zhimg.com/80/v2-6a2bd70d932fb9d781b18789acaa30ef_720w.jpg)

## 问题整理

1. 目前匹配的时候原始点云拼接之后会越来越大，制作的地图很大的时候过于占用内存，是否考虑做优化，比如保存一部分点云到硬盘。
2. 目前点云的反射率效果不是非常理想，因此看不清车道线。
3. 动态障碍物没有剔除，导致建好的图有一些车辆残影
4. 没有对关键帧做处理，也没有做回环检测，十分依赖IMU的数据，后续考虑移植lego-loam到apollo

## 开源代码

完整代码已经更新，bag包示例也会陆续上传。

- [daohu527/ndt_mapping: Baidu apollo offline mapping tool (github.com)](https://github.com/daohu527/ndt_mapping)