- [高精度地图lanelet2或opendrive转vectormap_neophack的博客-CSDN博客](https://blog.csdn.net/u011014502/article/details/120707425)

参照[vector map converter (!2) · Merge requests · Autoware Foundation / MovedToGitHub / utilities · GitLab](https://gitlab.com/autowarefoundation/autoware.ai/utilities/-/merge_requests/2)

一、系统准备

## ubuntu18.04

https://mirrors.163.com/ubuntu-releases/18.04.6/ubuntu-18.04.6-desktop-amd64.iso

## 二、ros安装melodic

[cn/melodic/Installation/Ubuntu - ROS Wiki](http://wiki.ros.org/cn/melodic/Installation/Ubuntu)

## 三、编译转换工具

```bash
sudo apt-get install libboost-dev libeigen3-dev libgeographic-dev libpugixml-dev libpython-dev libboost-python-dev 
mkdir -P ~/catkin_ws/src/

cd ~/catkin_ws/src/

git clone https://gitlab.com/mitsudome-r/utilities.git

cd .../

catkin_make
```

## 四、Lanelet2转[vector](https://so.csdn.net/so/search?q=vector&spm=1001.2101.3001.7020) map

1. roscore
2. mkdir converted_map
3. rosrun vector_map_converter lanelet2vectormap _map_file:=~/colcon_ws/src/Lanelet2/lanelet2_maps/res/mapping_example.osm _origin_lat:=49.00345654351 _origin_lon:=8.42427590707 _save_dir:=~/converted_map/
4. start run time manager
5. select converted vector map files under Map tab as shown in the image

## 五、opendrive转vector map

1. Download a map [sample](http://www.opendrive.org/tools/Crossing8Course.zip) form OpenDrive Downloads page.
2. roscore
3. mkdir autoware_map vector_map
4. rosrun vector_map_converter opendrive2autowaremap _map_file:=Crossing8Course.xodr _country_codes_dir:=autoware/utilities/vector_map_converter/countries/ _save_dir:=autoware_map/
5. rosrun vector_map_converter autowaremap2vectormap _map_dir:=autoware_map _save_dir:=vector_map/ _create_whiteline:=true
6. Load the vector_map in vector_map/ with the same step as steps 4-6 in **Converting from Lanelet2** section.
   However, use this [tf file](https://mp.csdn.net/autowarefoundation/autoware.ai/utilities/uploads/8a0031ca38b28ac83a5229ba6f54f2e9/tf_opendrive.launch) instead.
7. Set up rviz as step 7 in **Converting from Lanelet2** section.
8. You should see following vector_map.

![img](https://img-blog.csdnimg.cn/dcd2e8bd6a904c32859b7d324ec4856b.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbmVvcGhhY2s=,size_20,color_FFFFFF,t_70,g_se,x_16)