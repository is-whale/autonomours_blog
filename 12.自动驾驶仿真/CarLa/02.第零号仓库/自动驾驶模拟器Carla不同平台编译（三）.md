- [自动驾驶模拟器Carla不同平台编译（三） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/473419868)

![img](https://pic1.zhimg.com/80/v2-4c3f8204cd8545fa88e84248186c9f48_720w.jpg)

## 1.安装官方release版本

如果没有改代码的需求，建议直接安装官方发布的版本，节省时间。

carla支持2种操作系统，windows，linux（ubuntu）。ubuntu官方建议使用18.04，python支持2.7和3；windows只支持python3.下载的carla package里面包含了carla client， carla server。carla client有两种安装方式：.egg, .whl。.egg, .whl 使用其中一种方式即可。地图太大了，没有和carla package一起发布，需要单独下载。如果是carla 0.9.12以上版本，需要下载安装python package。carla 0.9.12以上版本只支持Vulkan，carla 0.9.12以下版本支持Vulkan和OpenGL，所以没有英伟达显卡的童鞋，请使用OpenGL方式。

具体安装方式参考 [Quick start package installation](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/start_quickstart/)

## 2.下载代码编译版本

### 2.1 linux系统编译

参考 [妲己不姓书：Carla 开发环境搭建（二）](https://zhuanlan.zhihu.com/p/470335288)

### 2.2 windows系统编译

需要64位windows系统，Carla和Unreal Engine都使用vs2019编译。

具体安装方式参考[Windows build - CARLA Simulator](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/build_windows/)

### 2.3 docker编译

需要先安装docker，再拉取carla image安装。

具体安装方式参考 [CARLA in Docker - CARLA Simulator](https://link.zhihu.com/?target=https%3A//carla.readthedocs.io/en/latest/build_docker/)