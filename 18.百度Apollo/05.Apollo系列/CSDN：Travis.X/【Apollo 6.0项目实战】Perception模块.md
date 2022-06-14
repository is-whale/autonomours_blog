- [【Apollo 6.0项目实战】Perception模块_Travis.X的博客-CSDN博客](https://blog.csdn.net/Travis_X/article/details/121518854)

# 前言

环境：

- Ubuntu 20.04
- Apollo 6.0
- LGSVL仿真器

## [Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020) 6.0软件框架

![在这里插入图片描述](https://img-blog.csdnimg.cn/66d527b5163d444abd699023053db962.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

- **Perception——感知模块识别自动驾驶汽车周围的环境。感知模块内部包含两个重要的子模块：障碍物检测和交通灯检测。**
- Prediction——预测模块用来预测与感知障碍物未来的运动轨迹。
- Routing——路由模块告诉自动驾驶汽车通过全局路径到达目的地。
- Planning——规划模块规划自动驾驶汽车要采取的时空轨迹。
- Control——控制模块通过产生油门、刹车和转向等控制命令来执行计划的时空轨迹。
- CanBus —— CanBus 是将控制命令传递给车辆硬件的接口。它还将机箱信息传递给软件系统。
- HD-Map——该模块类似于库。它不是发布和订阅消息，而是经常用作查询引擎支持，以提供关于道路的特定结构化信息。
- Localization——定位模块利用各种信息源（如 GPS、LiDAR 和 IMU）来估计自动驾驶汽车的位置。
- HMI ——Apollo 中的人机界面或 DreamView 是用于查看车辆状态、测试其他模块和实时控制车辆功能的模块。
- Monitor——车辆中所有模块的监控系统，包括硬件。
- Guardian——新的安全模块，用于干预监控检测到的失败和action center相应的功能。 执行操作中心功能并进行干预的新安全模块应监控检测故障。
- Storytelling——隔离和管理复杂场景的新模块，创建可触发多个模块操作的Story。所有其他模块都可以订阅此特定模块。

本文讲解的是如何在 LGSVL[仿真器](https://so.csdn.net/so/search?q=仿真器&spm=1001.2101.3001.7020)中测试自动驾驶车辆的感知能力，包括视觉感知、激光雷达感知以及多传感器的感知融合，主要目的是初步了解到 Apollo 感知模块整体结构以及各个模块的主要组成部分，对各感知模块的输入输出有比较清晰的认识。

------

# 一、视觉感知

**目前关于视觉部分红绿灯检测和障碍物检测的模块化测试没成功，之后再进行补充。可以先看之后的激光雷达感知和 LGSVL 传感器感知。**（＃－.－）

![在这里插入图片描述](https://img-blog.csdnimg.cn/071dbcfb927d4133ae981ad2268ad511.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/f25b8706231e4cb885bb3058f5c45acc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/f2f1fc8f448d4f1f955f03f37d306d00.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 二、激光雷达感知

进入 svlsimulator 官网给自己的无人车添加激光雷达传感器，调整传感器的位置。
![在这里插入图片描述](https://img-blog.csdnimg.cn/78f14aa8ddcc46f1bb36ad226d206ba6.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
可以参考如下的车辆JSON配置：

```xml
    "name": "Lidar Sensor",
    "parent": null,
    "pluginId": "b30d0478-8c7b-4687-bfc2-b3cdb3f5faff",
    "sortKey": 9,
    "plugin": {
      "isFavored": true,
      "isShared": false,
      "isOwned": false,
      "accessInfo": {
        "userAccessType": "favored",
        "owner": {
          "id": "0d888b00-fa53-47c1-882a-b68391268a11",
          "firstName": "SVL",
          "lastName": "Content"
        }
      },
      "supportedSimulatorVersions": [
        "2021.3",
        "2021.2",
        "2021.2.2",
        "2021.1",
        "2021.1.1"
      ],
      "id": "b30d0478-8c7b-4687-bfc2-b3cdb3f5faff",
      "name": "Lidar Sensor",
      "type": "LidarSensor",
      "category": "sensor",
      "ownerId": "0d888b00-fa53-47c1-882a-b68391268a11",
      "accessType": "public",
      "description": "This sensor returns a point cloud after 1 revolution.\nSee https://www.svlsimulator.com/docs/simulation-content/sensors-list/#lidar for more details.",
      "copyright": "LG Electronics Inc.",
      "licenseName": "LG Content",
      "imageUrl": "/api/v1/assets/download/preview/dd44a969-c038-4966-a39f-a445ab3b6c00",
      "status": "active",
      "owner": {
        "id": "0d888b00-fa53-47c1-882a-b68391268a11",
        "firstName": "SVL",
        "lastName": "Content"
      },
      "shareRequests": []
    },
    "type": "LidarSensor"
```

**注意**：我添加的是128线激光雷达，话题名称为 /apollo/sensor/lidar128/compensator/PointCloud2，参考坐标名称为 velodyne128。如果使用其他线束的激光雷达，需要修改 /apollo/modules/perception/production/dag/目录下的dag_streaming_perception.dag 文件和 /apollo/modules/perception/production/conf/perception/lidar目录下的velodyne128_detection_conf.pb.txt 文件。

例如 23 行修改成与 LGSVL 上 Lidar 输出的相同话题名称。

```cpp
vim modules/perception/production/dag/dag_streaming_perception.dag 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/f7a532777a2146ca98f0637869587207.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
修改第1行以及第5行与 LGSVL 中的设置保持一致。

```cpp
vim modules/perception/production/conf/perception/lidar/velodyne128_detection_conf.pb.txt 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/80a007a587a34fca82d0fb05694b10fd.png#pic_center)
完成激光雷达的设置后，启动Apollo Docker容器和 LGSVL 仿真器，打开Dreamview http://localhost:8888/，在上方选择对应的模式、车型以及地图（根据自己的仿真环境选择相应的地图）。

在Module Controller标签页启动Perception模块。

正常情况下的显示如下，可以看到感知模块对点云进行了处理，最终实现目标的识别。
![在这里插入图片描述](https://img-blog.csdnimg.cn/291582b929094d8bace4e3d54a3fcd06.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 三、LGSVL 传感器感知

Apollo 视觉感知模块输入输出如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/d7abb4fa4eff41ab8112f9a5b597c941.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
Apollo 激光雷达感知模块输入输出如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/c84740f076774d04bd66f46306e8858a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

多传感器融合后感知模块的输入输出如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/83464fd57c824aca87af59545786bd39.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)
对比上面三张图可以看出，检测红绿灯模块的输出话题为 **/apollo/perception/traffic_light**，检测障碍物模块的输出话题为 **/apollo/perception/obstacles** （具有航向、速度和分类信息的三维障碍物轨迹）和 **/perception/inner/PrefusedObjects** （输出给融合模块的障碍物信息）。

LGSVL 仿真器提供了3D Ground Truth sensor 和 Signal sensor，分别用作障碍物检测（输出话题为/apollo/perception/obstacles）和交通灯检测（输出话题为/apollo/perception/traffic_light），**换句话说，如果添加以上两种传感器，就可以完全绕过 Apollo 的感知模块，直接获取到红绿灯检测的信息和障碍物信息。** 当然，这也就**没必要给车辆添加激光雷达、相机和毫米波雷达等传感器（如果是需要显示图像、点云信息的话还是得添加相关的传感器）**，同理Perception 和 Traffic light 模块也就没有启动的必要。

## 3.1 3D Ground Truth sensor

3D Ground Truth sensor替换 Apollo 的对象检测模块。输出话题 **/apollo/perception/obstacles** 。
![在这里插入图片描述](https://img-blog.csdnimg.cn/5f836f333b1f4b53a58c3838115f5313.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 3.2 Signal sensor

Signal sensor 替换 Apollo 的红绿灯检测模块。输出话题为 **/apollo/perception/traffic_light** 。
![在这里插入图片描述](https://img-blog.csdnimg.cn/0d3a0611099a4445bd9e0cbb0377eb41.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

## 3.3 视频演示

<iframe id="TnTJrxYN-1638952149878" src="https://player.bilibili.com/player.html?aid=977167682" allowfullscreen="true" data-mediaembed="bilibili" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; font-weight: normal; overflow-wrap: break-word; display: block; width: 660px; height: 330px;"></iframe>

【[自动驾驶](https://so.csdn.net/so/search?q=自动驾驶&spm=1001.2101.3001.7020)】Apollo 6.0 与 LGSVL 联合仿真（3）

------

# 参考

【1】[Apollo视觉感知能力介绍](https://apollo.auto/Apollo-Homepage-Document/Apollo_Doc_CN_6_0/上机使用教程/上机实践Apollo视觉感知能力/Apollo视觉感知能力介绍)
【2】[Apollo激光雷达感知介绍](https://apollo.auto/Apollo-Homepage-Document/Apollo_Doc_CN_6_0/上机使用教程/上机实践Apollo激光雷达感知能力/Apollo激光雷达感知介绍)
【3】[Apollo感知融合能力介绍](https://apollo.auto/Apollo-Homepage-Document/Apollo_Doc_CN_6_0/上机使用教程/上机实践Apollo激光雷达感知能力/Apollo激光雷达感知介绍)
【4】[LGSVL 仿真器官方文档](https://www.svlsimulator.com/docs/archive/2020.06/modular-testing/#3d-ground-truth-sensor-more-details)