- [自动驾驶模拟器Carla 开发环境搭建（二） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/470335288)

carla是一个开源自动驾驶模拟器。可以模拟相机，雷达等数据。

## 1.开发环境

ubuntu 18.04.04

无NVIDIA显卡及驱动

carla 0.9.11

UnrealEngine 4.24

## 2.代码下载

carla代码位置

[https://github.com/carla-simulator/carla](https://link.zhihu.com/?target=https%3A//github.com/carla-simulator/carla)

1）使用git clone 下载carla代码。

Ubuntu Git安装及配置

[Git安装与配置0 赞同 · 0 评论文章![img](https://pica.zhimg.com/v2-beb087b6af8b7bb888555625fa1223b6_r.jpg?source=172ae18b)](https://zhuanlan.zhihu.com/p/470047206)

carla的代码编译依赖git下载代码时生成的文件，从A机器上下载代码再复制到B机器上，在B机器上会编译不过。建议直接使用git clone在要编译代码的机器上直接下载carla代码。

```text
git clone git@github.com:carla-simulator/carla.git //下载代码
```

下载代码后不要忘记切换到自己需要的版本号。

```text
git tag  //查看tag版本号
git checktout 0.9.11 //切换到固定版本
```

2）UnrealEngine 代码下载。

carla 使用 UnrealEngine 引擎，需要下载UnrealEngine 代码。UnrealEngine 是一个私有库，需要在UnrealEngine 官网注册账号后，再去关联github账号，才能在github上下载到UnrealEngine 的代码。具体注册账号方式参见UnrealEngine官网

```text
https://www.unrealengine.com/en-US/ue4-on-github
```

carla版本号和UnrealEngine 版本号有一一对应关系。打开carla 代码根目录，找到README.md，能看到carla版本对应的UnrealEngine 版本号。 carla 0.9.11 对应 UnrealEngine 4.24.

![img](https://pic4.zhimg.com/80/v2-403ba04311bf311e4815da2b24d8e9bf_720w.jpg)

下载代码后不要忘记切换到自己需要的版本号。

```text
git tag  //查看tag版本号
git checktout 4.24.0-release //切换到固定版本
```

## 3.代码编译

官方代码编译文档参见

```text
https://carla.readthedocs.io/en/latest/build_linux/
```

出现如下提示，说明 UnrealEngine编译成功。

![img](https://pic2.zhimg.com/80/v2-00e99ed69daa0c27b0bfa70ee5aa6f3d_720w.jpg)

出现如下提示，说明carla client编译成功。

![img](https://pic4.zhimg.com/80/v2-e03c8749b611873c3eff9f7a667fc513_720w.jpg)

编译过程中可能会遇到一些问题

```text
～/carla/PythonAPI/examples$ python spawn_npc.py -n 80
```

1） ImportError: No module named carla

程序找不到 CARLA Python的路径。

把下面内容粘贴到～/.bashrc或者~/.profile最底部,修改完文件后使用source ~/.bashrc source ~/.profile

```text
export PYTHONPATH=$PYTHONPATH:～/carla/PythonAPI/carla/dist/carla-0.9.11-py2.7-linux-x86_64.egg
```

2）ImportError: No module named numpy

numpy安装，如果机器上有多个版本的python，建议安装Anaconda。

Anaconda包括Conda、Python以及一大堆安装好的工具包，比如：[numpy](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/numpy/5678437)、[pandas](https://link.zhihu.com/?target=https%3A//baike.baidu.com/item/pandas/17209606)等。安装方法参考下面链接。

[Anaconda软件安装流程100 赞同 · 15 评论文章![img](https://pic3.zhimg.com/v2-0c50debe4cde5d4ccba537c5241a912c_r.jpg?source=172ae18b)](https://zhuanlan.zhihu.com/p/63171810)

安装完以后，使用Anaconda创建python环境。我使用的是python2.7的环境。

[使用Anaconda切换Python3.x环境与Python2.7环境_Fortuna_的博客-CSDN博客_anaconda python2.7blog.csdn.net/levon2018/article/details/84316088![img](https://pic3.zhimg.com/v2-d14d42ee3d7161fe46046bd34ab6162e_180x120.jpg)](https://link.zhihu.com/?target=https%3A//blog.csdn.net/levon2018/article/details/84316088)

切换到python2.7环境后,sudo apt-get install python-numpy 安装python2.7版本对应的numpy

![img](https://pic4.zhimg.com/80/v2-926792019c71b556b7ef5de0a8245b5f_720w.jpg)

3） RuntimeError: cannot import pygame, make sure pygame package is installed.

切换到python2.7环境，pip install pygame

![img](https://pic2.zhimg.com/80/v2-5c5db357918b46278babbb0c154c59c5_720w.jpg)

4） make launch后，显示如下问题：Cannot find a compatible Vulkan driver (ICD).

![img](https://pic1.zhimg.com/80/v2-a78a72af487c0ed9a51cfdb69964b03c_720w.jpg)

在 build`安装`时，Vulkan 是默认的渲染API。如果无法使用Vulkan 渲染 API ，可以通过更改设置文件的方式来使用OpenGL API。

```text
sudo gedit ~/carla/Uitl/BuildTools/BuildCarlaUE4.sh
```

将RHI=”-vulkan”更改成 RHI=”-opengl”

## 4.代码运行

在carla根目录下执行make launch

![img](https://pic3.zhimg.com/80/v2-66b7a72bda49636bec121e5770655826_720w.jpg)

点击 play后，页面卡顿，退出，是系统swap空间不够导致的。需要增加swap空间8G以上。

[在Ubuntu 18.04上添加交换空间的方法www.cppcns.com/os/linux/259697.html](https://link.zhihu.com/?target=http%3A//www.cppcns.com/os/linux/259697.html)

新开一个terminal，在/carla/PythonAPI/examples$ 路径下输入 python spawn_npc.py -n 80