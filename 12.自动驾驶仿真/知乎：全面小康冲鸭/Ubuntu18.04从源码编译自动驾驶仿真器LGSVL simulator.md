- [Ubuntu18.04从源码编译自动驾驶仿真器LGSVL simulator - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/350031359)

1、[LGSVL自动驾驶仿真器官方文档](https://link.zhihu.com/?target=https%3A//www.lgsvlsimulator.com/docs/)

该文档是入手LGSVL simulator的官方教程，对文档的通读和理解有助于搭建自定义的自动驾驶仿真环境、AD stack自动驾驶仿真测试等具有重大帮助。

关于本文档的详细介绍请移步：NULL

2、[LGSVL官网](https://link.zhihu.com/?target=https%3A//www.lgsvlsimulator.com/)

非常简洁的官网，任何你想要找的关于LGSVL的帮助都可以在这里找到：官方文档、下载地址、Github代码库、Maps&Vehicles、F&Q、联系方式等。

3、[LGSVL simulator-GitHub开源代码](https://link.zhihu.com/?target=https%3A//github.com/lgsvl/simulator)

仿真器的源码地址，除了master分支外，还有几个以release时间命名的分支。值得注意的是，如果你想要使用"lgsvl bridge"，请clone以"release-2020.06"命名的分支(稍微留个心，后面会讲到)。

此外，在GitHub地址的上级目录（[https://github.com/lgsvl](https://link.zhihu.com/?target=https%3A//github.com/lgsvl)）下，有很多配合simulator使用的包和用例，比如PythonAPI、ros2-lgsvl-bridge、lgsvl-msgs、LoggingBridge、lanefollowing等。

4、[LGSVL官方邮箱](mailto:contact@lgsvlsimulator.com)

官方邮箱，有关于仿真器的任何问题都可以发送邮件请教，但更希望在[github-issues](https://link.zhihu.com/?target=https%3A//github.com/lgsvl/simulator/issues)上面提问题，减少冗余问题，也更希望有问题先去issues上面搜索。

------

**本文章主要参考 "LGSVL simulator doc => Quick Start => Getting Started" 章节，介绍仿真器的源码构建和运行。**

1、为了保持仿真器在运行的时候有良好的性能和帧率，LGSVL对电脑的硬件提出了几点要求，虽然2G的显存或更低的配置也可以运行，但不会达到想要的效果，不过仍可以在运行开始之前，降低仿真画面的分辨率。如果你想要配合Apollo或Autoware仿真，请至少拥有8G（官方说的是10G）的显存和更高的电脑配置，但官方始终建议把simulator和AD stack分两台电脑上运行，通过IP和端口通信，减少电脑压力，同样能够获得不错的仿真效果。建议如下：

> 4 GHz Quad core CPU
> Nvidia GTX 1080 (8GB memory)
> Windows 10 64 Bit

由于LGSVL在windows下能够获得更好的运行效果，所以官方在给建议的时候只提到了windows10 64Bit，其实在Ubuntu 18.04上也可以使用仿真器，只是性能稍有差异，为了方便开发，个人建议使用Ubuntu18.04系统，以下的教程也是针对Ubuntu18.04系统的，win10系统上的流程也大同小异，请参考[win10源码编译LGSVL simulator的官方教程](https://link.zhihu.com/?target=https%3A//www.lgsvlsimulator.com/docs/build-instructions/)。

2、从LGSVL官网下载地址上可以直接下载windows/Linux的压缩文件，然后解压后运行exe文件即可。关于LGSVL simulator界面、simulator Web界面的介绍和使用请移步：NULL

值得注意的是，你仍是要有一块不错的显卡和已经装好的显卡驱动。如果你只是想先体验一下该仿真器，非常建议在windows下面运行可执行文件，因为win应该已经帮你装好了适配的显卡驱动，你也不需要装任何其他的依赖。但是在Ubuntu系统上，你需要额外安装依赖包：

```text
sudo apt install libvulkan1
```

并且对仿真器赋予可执行权限：

```text
sudo chmod +x simulator
```

如果你想要了解显卡驱动和CUDA、cuDNN，甚至是tensorflow和pytorch在Ubuntu18.04上的安装与配置，请移步：NULL

------

上面的如果你做不到的话，下面的可以不用看了...（划掉哈哈哈）因为我们要从源码编译LGSVL simulator，这是一件很头大的事情。源码编译的好处在于你可以自定义仿真环境，适合开发人员使用。下面我们将按照官方教程一步一步的来。

写在前面，如果你真的要使用LGSVL开发，非常建议新建一个lgsvl文件夹，然后把下面要下载、安装的东西都塞到这个文件夹里，方便管理。

**1、首先我们需要安装Unity-hub，然后通过Unity-hub安装Unity。**

这是因为仿真器是基于Unity引擎开发的，大部分的代码也都是C#，那么我们就可以通过Unity结合C#编程来自定义开发。不过在我们第一次从源码编译的时候，最好不要对源码和仿真场景做任何修改，毕竟我们才刚刚入门。

[Unity-hub下载链接public-cdn.cloud.unity3d.com/hub/prod/UnityHub.AppImage](https://link.zhihu.com/?target=https%3A//public-cdn.cloud.unity3d.com/hub/prod/UnityHub.AppImage)

Unity-hub在Ubuntu系统上只是一个可执行文件，那么我们下载好之后，赋予可执行权限，双击即可运行。

```text
sudo chmod +x UnityHub.AppImage
```

然后，我们需要注册并在Unity-hub上登录账号，通过手动激活来获得使用Unity-hub的凭证。

![img](https://pic4.zhimg.com/80/v2-ae299eda776a71fcc0244681eefb41fb_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-587843ddbc5ddb8d490709be441ced6c_720w.jpg)

最后，通过Unity-hub下载Unity。如果你想要更改Unity的下载位置，请在下载前先在设置中修改下载位置。

![img](https://pic1.zhimg.com/80/v2-45428d8dfc8af7190a841a0f23ca3be0_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-202b63327fbca96549ba7e3f51e80d06_720w.jpg)

注意1：下载单个版本的Unity至少需要你留出8G的空间。

注意2：可能现在你还不太懂“ROS2和桥接”的相关概念，那么我非常建议你下载Unity 2019.3.15f1版本，因为该版本对应仿真器代码仓库中的“release-2020.06”分支，该分支可以使用ROS2以及桥接，而主分支不可以使用。如果你非常了解这方面的知识并且确定你不会用到桥接，那么建议你下载Unity 2019.3.3f1版本，该版本对应的simulator是最新的版本，对应现在的“master”分支。

**2、下载和安装Node.js**

Node.js是Java的运行环境，之所以下载Node.js是因为仿真器需要WebUI界面。官方建议下载12.16.1 LTS版本，其实只要是12.x版本就可以。安装官方教程：

```text
curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
sudo apt-get install -y nodejs
```

安装过程较慢，会安装好多依赖包，甚至还会安装python2，那么如果你主机上有python3，你是否需要考虑一下python版本的共存问题呢^_^。安装完成后，测试是否安装成功，打印出安装的版本。

```text
node --version
>v12.16.1
```

![img](https://pic4.zhimg.com/80/v2-7ca166f82fd4b7e74da48cf7379fb5e7_720w.jpg)

**3、安装git-lfs。**

全称git-large file storage，顾名思义是用来clone大型存储库的，当然前提是你要已经安装好git了。这个git-lfs是一定要装的，不要以为你有git就可以clone仿真器的源码，只有git你clone下来的源码是不全的~。

[git-lfs Linux 下载链接github.com/git-lfs/git-lfs/releases/download/v2.13.2/git-lfs-linux-amd64-v2.13.2.tar.gz](https://link.zhihu.com/?target=https%3A//github.com/git-lfs/git-lfs/releases/download/v2.13.2/git-lfs-linux-amd64-v2.13.2.tar.gz)

解压后，执行下面的语句安装。安装完再次执行下面的语句测试，如果打印出类似的Error信息可以忽略，只要有下面那句就说明安装成功:

```text
git lfs install
>Git LFS initialized.
```

![img](https://pic1.zhimg.com/80/v2-9641625bc1670681b2fc984f2cf230e0_720w.jpg)

**4、Clone仿真器的源码**

上面搞了这么多，终于到这一步了。已经讲过，根据对应的Unity版本，选择从master或者release-2020.06分支clone，clone完成后会出现一个simulator的文件夹。

![img](https://pic2.zhimg.com/80/v2-1684c4507d25171dc912b453edd99a59_720w.jpg)

例如，我本人从release-2020.06分支clone：

```text
git clone -b release-2020.06 https://github.com/lgsvl/simulator.git
```

额，该过程可能较慢，看配置吧，我的3900XT，一下就好了...

然后官方也给出了测试方法，用来判断clone下来的仓库是否完整：进入到simulator/Assets/Materials/EnvironmentMaterials/文件夹里面，如果你能打开EnvironmentDamageAlbedo.png这张图片，那么就成功了！只要你装了git-lfs，然后慢慢的等待clone结束，都不会出现什么问题。否则，请先安装git-lfs，然后进入到simulator文件夹下，使用git lfs pull再来一次吧！

**5、添加地图和车辆**

在官方教程里，这一步是放在了打开项目的后面。其实道理是一样的，只要在build之前在对应的文件夹里添加地图和车辆（因为GitHub的源码仓库是不带这些资产的，需要自己额外clone）。这里给出了四个地图对应的GitHub地址：

> [https://github.com/lgsvl/CubeTown](https://link.zhihu.com/?target=https%3A//github.com/lgsvl/CubeTown)
> [https://github.com/lgsvl/SingleLaneRoad](https://link.zhihu.com/?target=https%3A//github.com/lgsvl/SingleLaneRoad)
> [https://github.com/lgsvl/Shalun](https://link.zhihu.com/?target=https%3A//github.com/lgsvl/Shalun)
> [https://github.com/lgsvl/SanFrancisco](https://link.zhihu.com/?target=https%3A//github.com/lgsvl/SanFrancisco)

同样通过git clone 把这四个地图clone到simulator/Assets/External/Environments文件夹下面，其中SanFrancisco地图较大，可能会较慢，还是看CPU吧。也可以先clone一个SingleLaneRoad地图，测试能否正常构建，然后再clone其他的。

这里是车辆的GitHub地址：

> [https://github.com/lgsvl/Jaguar2015XE](https://link.zhihu.com/?target=https%3A//github.com/lgsvl/Jaguar2015XE)

通过git clone 把这个车辆放入到simulator/Assets/External/Vehicles文件夹下面。

关于地图和车辆的介绍，请移步到官网：

[LGSVL Simulator Contentcontent.lgsvlsimulator.com/![img](https://pic4.zhimg.com/v2-170b01367540c996732dab58e4bb53d7_180x120.jpg)](https://link.zhihu.com/?target=https%3A//content.lgsvlsimulator.com/)

多说一点，在simulator项目里还有传感器（sensor）和桥（bridge）资产，这些有内置好的，后期也可以自定义，非常灵活，那么在我们第一次构建的时候先不用管这两个资产就好了，我们先只关注地图（map）和（vehicle）。

**6、打开项目、构建项目**

能不能成功就看这一步了，上面做了那么多就是在做铺垫。

1> 启动Unity-hub，点击“添加”然后选择“simulator”文件夹，这样项目就加载进hub，归hub管理了。

- 注意：请检查一下simulator使用的Unity的版本，看和本地的Unity的版本是否对应。一般来说，本地版本比项目版本高，没有问题会自动兼容，但低不行。最好还是本地版本和项目版本一样。在下载Unity版本的时候，也可以先把项目导入到Unity-hub里面，这时候项目名称下面会显示该项目需要的Unity版本，然后再下载对应的版本即可。

2> 打开项目，很简单，双击项目名称打开。第一次打开加载的比较慢，因为会加载Unity的各种包，项目里的各种资产，也包括我们clone下来的地图和车辆。这时候你的CPU利用率会达到100%，你的内存占用在暴涨...请确保你有一台配置较好的电脑。

3> 打开项目之后，使用命令行进入到simulator/WebUI目录下，执行以下命令安装依赖，该命令在packages.json文件没有发生变动时，只执行一次即可，那么一般我们只执行一次。

```text
npm install
```

![img](https://pic3.zhimg.com/80/v2-aac5bf88add68a741f791591f50d64ba_720w.jpg)

4> 在项目的菜单栏里，点击Simulator > Build WebUI。构建完成后会提示WebUI build is completed。

![img](https://pic3.zhimg.com/80/v2-b7e8b5aa6c49a5e34640ac236f6b2252_720w.jpg)

- 一般不会出错，否则请再次执行第三步。
- 再出错请关闭项目，再次打开，再从第三步开始执行。
- 再出错，那就是clone的不完整了...

这一步是在构建WebUI，其实就是仿真器启动后通过本地端口要打开的一个Web界面，具体的介绍请转到：NULL

5> 在项目的菜单栏里，点击Simulator > Build，选中我们导入的地图和车辆，确定好构建版本是Linux，其他的先不用管，点击Build，然后就是漫长的等待，稍安勿躁。

第一次构建我的机子大概八分钟，构建期间界面会卡死，会不停的弹出来进度条，会弹出来构建信息可以查看。构建期间可能还会报错，然后错误消失，再报错，再消失...只要等最终的构建完成之后没有错误，那就是成功了。构建结束之后界面恢复正常，可以点击。

OK，如果有错，请关闭项目，再次打开走一遍流程。如果没有出错，那么就说明所有的构建全部完成！

![img](https://pic4.zhimg.com/80/v2-bd1b8b5a5f4381acde3b5862080564a7_720w.jpg)

**7、Play**

点击菜单栏 File => Open Scene，选择simulator/Asserts/Scenes/LoaderScene.unity

![img](https://pic4.zhimg.com/80/v2-283b965e7d79451fea4584f4c67549a3_720w.jpg)

然后点击 Play按钮，如果没有报错，说明LGSVL Simulator已经被我们从源码编译安装成功了，可以运行了，点击Open Browser按钮打开浏览器（[l](https://link.zhihu.com/?target=http%3A//localhost%3A8080/%23/)ocalhost:8080）！

![img](https://pic2.zhimg.com/80/v2-cc82a0cca240929d87dd5cc15afebb6d_720w.jpg)

可能你现在还不太会使用，没关系，和构建成功相比，学习如何使用要简单很多，请移步到：NULL

我这几天会写几篇关于LGSVL 的文章，想来想去，还是不对自动驾驶的知识做介绍了，以后有机会再写关于自动驾驶决策规划方面的知识。所以，把搭建LGSVL 的自动驾驶仿真环境作为第一篇，毕竟没有环境怎样实战呢～，至于为什么抛弃了Carla，是因为后面要结合Apollo联合仿真。

虽然马上过年，但我很乐意分享，会尽快完成其他几篇的～

![img](https://pic2.zhimg.com/80/v2-98aee668d42a9dce651e73eaefad9469_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-855e1ba500c14944111a4be9f317a9f6_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-569886e4c50f25a800d38b4db63d76d9_720w.jpg)