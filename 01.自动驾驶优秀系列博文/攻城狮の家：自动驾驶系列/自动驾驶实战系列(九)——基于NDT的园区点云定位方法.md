- [自动驾驶实战系列(九)——基于NDT的园区点云定位方法 | 攻城狮の家 (xchu.net)](http://xchu.net/2020/06/05/48localization-init/)

 之前一直说总结一下定位模块的，拖到现在才开始，因为最近面试了下，发现这块做的内容已经忘记了不少。主要是基于NDT+UKF的定位方法，主要方法来源于一个开源的lidar localization project。在这里我主要对其代码进行讲解，并适配我们自己的园区场景，后续我将在此基础上进行一些工程上的改进，使之更加鲁棒和易用。本篇主对原项目进行了重写，目前修改的部分主要有以下两点:

- 新增了定位初始化模块。
- 改进map_loader节点，结合之前第四篇的动态记载。

由于数据是采用目前实习公司的，不会放出来，大家感兴趣的可以结合我的博客内容，对自己的传感器数据进行适配。起始博客中已经给出了关键的代码，如果看懂的话完全可以自己实现出来，这比直接跑别人的工程要好得多，也更有意义。

![image-20200605140712653](http://xchu.net/2020/06/05/48localization-init/image-20200605140712653.png)



**2020.6.07开始写..**

## 定位概述

 目前基于激光雷达的定位由于其全天时、稳定等特点，是自动驾驶领域的主流方案，主要运用的是SLAM技术。这里不同于传统的机器人SLAM，自动驾驶领域的L和M是解耦的。基于激光雷达定位，需要预先制作点云地图，然后基于点云地图通过点云匹配算法完成实时定位。目前一些大型的开源项目中，采用的定位方案通常是多传感器融合，非本篇要叙述的内容，但其核心还是基于点云的定位。主要分为两种，一类是以Apollo为代表的基于反射强度的2.5D地图定位方法，另外一类是基于ICP/NDT的3D点云定位方法。

 第一种由于国产激光雷达反射强度普遍不行，这里不太适用我的雷达，暂不考虑。本篇主要是基于NDT的3D点云定位方法。纵观整个点云定位流程，我们可以发现一些潜在的问题，也是影响定位鲁棒性的关键问题。

## 为什么采用NDT算法？

 这个问题在第一篇博客中已经做出说明，相比与ICP算法暴力求解，我认为NDT算法对环境进行概率建模更符合实际情况。其对匹配初值要求低，能容忍一定程度的环境变化，最后就是匹配速度快。但由于点云稀疏的特性，点云配准存在着一个通病。

 点云配准问题是一个**非凸优化问题**，**NDT和ICP算法的本质是局部搜索，通过对目标函数的迭代求解求得一个初值附近的局部最优解，而正确的空间转换可以理解成对目标函数的全局最优解。但目标函数是非凸的，在整个参数空间上有许多局部最优解，如果初值不在全局最优解附近，就可能收敛于其他次优解，导致找不到正确的转换结果**。而通常我们用到的pcl ndt icp库都是采用迭代法来求解的，所以有时候会出现，明明匹配的score很小，但是明显匹配失败的场景。

 此问题其实也就是点云对于局部特征的描述较好，但对于全局特征的描述能力较弱的表现。目前基于深度学习提取的点云特征在一定程度上缓解了这个问题，由于我还没过此领域基于深度学习方法的调研，这里不做评价。由这个原因导致的点云定位的第一步——定位初始化，光靠激光雷达一个传感器，不给初值，通过当前点云帧和全局地图匹配，进而得到当前位置，是不可能做到的，包括一些基于点云的传统重定位方法。**所以目前这部分一般采用GPS来给定大致的位置和姿态，完成初始化**。在后续的实时定位过程中，点云匹配的初值就是上一帧的定位结果。至于定位精度，这和迭代法设定的步长有关系，一般0.1m级别是能够保证的。

## 制图

参考以下博客

> 自动驾驶实战系列(一)——利用NDT算法构建点云地图 `http://xchu.net/2019/10/11/31ndt-map/`
>
> LOAM/LEGO_LOAM/ALOM/LIOM等开源算法

## 初始化定位

 初始化定位需要在点云地图上找到当前车辆的位置，这里通过GPS+NDT算法完成。GPS主要是提供初值，初值包括位置和姿态。双天线的GPS能够给出位置和yaw角。一般的GPS只能给出位置，需要通过车辆的运动在计算yaw角。

 这里我们直接采用GPS实时的一个odometry

## 实时定位

实时定位的点云配准初值主要来源于上一帧的定位结果。

## 定位地图加载

### 动态加载网格地图

定位地图加载的方式多样，如果是大地图场景，我们采用预先划分网格、动态记载的方法，参考博客

> 自动驾驶实战系列(六)——网格地图的动态加载：http://xchu.net/2020/01/20/39dynamic_map_loader/
>
> 自动驾驶实战系列(二)——点云地图划分网格并可视化：http://xchu.net/2019/10/30/32pcd-divider/

其实就是拿IO换内存，其主要目的还是减小点云地图的内存占用，以及点云匹配的target。

### GPS动态更新局部地图

如果在地图比较小的情况下，频繁的IO操作会拖慢整个定位系统的效率，这里我们根据GPS位置动态更新当前的局部地图。

首先**初始化参数**，包括地图的原点、更新频率等。

```
copyradius = private_nh.param<float>("radius", 100.0f); //  局部点云半径
trim_low = private_nh.param<float>("trim_low", 0.0f); // 在z轴方向截取点云
lidar_height = private_nh.param<float>("lidar_height", 2.4f);
trim_high = private_nh.param<float>("trim_high", 4.0f);

int mapUpdateTime = private_nh.param<int>("mapUpdateTime", 5); // 地图的更新频率，5s更新一次
auto downsample_resolution = private_nh.param<float>("downsample_resolution", 0.1f); //  降采样网格大小
std::string globalmap_pcd = private_nh.param<std::string>("global_map_pcd_path", "data/map.pcd"); //  地图位置
origin_latitude = private_nh.param<double>("origin_latitude", 22.663029715); // 地图原点的经纬度坐标
origin_longitude = private_nh.param<double>("origin_longitude", 114.045642255);
origin_altitude = private_nh.param<double>("origin_altitude", 59.620000000000005);

std::string gps_topic = private_nh.param<std::string>("gps_topic", "/novatel718d/pos"); // 接收的GPS Topic
```

然后**初始化当前pose以及加载地图**

```
copy            /**初始化pose**/
curr_pose.reset(new geometry_msgs::PoseStamped());
curr_pose->pose.position.x = private_nh.param<float>("init_x", 0.0f);
curr_pose->pose.position.y = private_nh.param<float>("init_y", 0.0f);
curr_pose->pose.position.z = 0.0f;

            /**加载全局地图并发布一次**/ // maybe add voxelgrid down sample
full_map.reset(new pcl::PointCloud<pcl::PointXYZI>());
pcl::io::loadPCDFile(globalmap_pcd, *full_map); //full_map指向全局地图

            /** 地图下采样 **/
boost::shared_ptr <pcl::VoxelGrid<pcl::PointXYZI>> voxelgrid(new pcl::VoxelGrid<pcl::PointXYZI>());
voxelgrid->setLeafSize(downsample_resolution, downsample_resolution, downsample_resolution);
voxelgrid->setInputCloud(full_map);
pcl::PointCloud<pcl::PointXYZI>::Ptr filtered(new pcl::PointCloud<pcl::PointXYZI>());
voxelgrid->filter(*filtered);
full_map = filtered;

            //   publish globalmap once
full_map->header.frame_id = "map";
globalmap_pub = nh.advertise<sensor_msgs::PointCloud2>("/globalmap", 5);//  全局地图
globalmap_pub.publish(full_map);
kdtree.setInputCloud(full_map);// 用kdtree索引
```

**局部地图的划分与更新**

```
copylocalmap_pub = nh.advertise<sensor_msgs::PointCloud2>("/localmap", 5, true);//  局部地图
timer = nh.createTimer(ros::Duration(mapUpdateTime), &GlobalmapProviderNodelet::localmap_callback, this);   // 定时器， 5s更新一次局部地图
gps_sub = mt_nh.subscribe(gps_topic, 10, &GlobalmapProviderNodelet::gnss_callback, this);   // 根据GPS位置划分地图

/**更新局部地图**/
ros::TimerEvent init_event;
localmap_callback(init_event);
NODELET_INFO("globalmap_provider_nodelet initial completed");
```

下面是详细的**callback**，根据GPS更新当前pose

```
copyvoid gnss_callback(const sensor_msgs::NavSatFixPtr &gps_msg) {

    if (std::isnan(gps_msg->latitude + gps_msg->longitude + gps_msg->altitude)) {
        ROS_INFO("GPS LLA NAN...");
        return;
    }

    gpsTools gpsTools;
    gpsTools.lla_origin_ << origin_latitude, origin_longitude, origin_altitude;

    if (gps_msg->status.status == 4 || gps_msg->status.status == 5 || gps_msg->status.status == 1 ||
        gps_msg->status.status == 2) {

        //  经纬转xy
        Eigen::Vector3d lla = gpsTools.GpsMsg2Eigen(*gps_msg);
        Eigen::Vector3d ecef = gpsTools.LLA2ECEF(lla);
        Eigen::Vector3d enu = gpsTools.ECEF2ENU(ecef);
        gpsTools.gps_pos_ = enu;

        // 更新当前pose
        gps_pos_ = enu;
        has_pos_ = true;
        curr_pose->pose.position.x = gps_pos_(0);
        curr_pose->pose.position.y = gps_pos_(1);
        curr_pose->pose.position.z = gps_pos_(2);
    }
}
```

最后是**局部地图的划分**，kd tree近邻搜索在当前定位半径100米区域内的局部点云

```
copyvoid localmap_callback(const ros::TimerEvent &event) {
    clock_t start = clock();

    std::vector<int> pointIdxRadiusSearch;
    std::vector<float> pointRadiusSquaredDistance;
    pcl::PointXYZI searchPoint;
    searchPoint.x = curr_pose->pose.position.x;//  近邻搜索
    searchPoint.y = curr_pose->pose.position.y;
    searchPoint.z = curr_pose->pose.position.z;

    pcl::PointCloud<pcl::PointXYZI>::Ptr trimmed_cloud(new pcl::PointCloud <pcl::PointXYZI>);
    float z_min_threshold = lidar_height + trim_low; // 取指定高度区间的点云
    float z_max_threshold = lidar_height + trim_high;

    // 搜索当前位置x， 100m半斤范围内的点云，id存在pointIdxRadiusSearch中
    if (kdtree.radiusSearch(searchPoint, radius, pointIdxRadiusSearch, pointRadiusSquaredDistance) > 0) {

        trimmed_cloud->points.reserve(60000);
        for (int i : pointIdxRadiusSearch) {
            if (full_map->points[i].z > z_min_threshold && full_map->points[i].z < z_max_threshold) {
                pcl::PointXYZI cpt;
                cpt.x = full_map->points[i].x;
                cpt.y = full_map->points[i].y;
                cpt.z = full_map->points[i].z;
                cpt.intensity = full_map->points[i].intensity;
                trimmed_cloud->points.push_back(cpt);
            }
        }
        trimmed_cloud->width = trimmed_cloud->points.size(); // 点云数量
        trimmed_cloud->height = 1;

        cout << "full_map.size()\t:" << full_map->size() << endl << "trimmed_cloud->width:\t"
            << trimmed_cloud->width << endl;
    }

    trimmed_cloud->header.frame_id = "map";
    pcl_conversions::toPCL(curr_pose->header.stamp, trimmed_cloud->header.stamp); 
    localmap_pub.publish(trimmed_cloud); //  pub当前的map

    NODELET_INFO(" local map updated on x:%f, y:%f, z:%f", searchPoint.x, searchPoint.y, searchPoint.z);
    clock_t end = clock();
    NODELET_INFO("trim time = %f seconds", (double) (end - start) / CLOCKS_PER_SEC);
}
```

整体下来代码还是比较容易实现的。

## 致谢

**hdl_localization**:`https://github.com/koide3/hdl_localization`