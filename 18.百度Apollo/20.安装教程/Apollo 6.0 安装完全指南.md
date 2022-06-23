- [Apollo 6.0 安装完全指南 | 朝花夕拾 (shipengx.com)](https://blog.shipengx.com/archives/e4b9c8ad.html)

![Apollo 6.0 安装完全指南](https://www.lovebetterworld.com:8443/uploads/2022/06/23/62b4165b180ad.png)

# 0 前言

Apollo 是优秀的自动驾驶开发框架，出自百度之手，目前已更新到 6.0 版本，本文旨在详细记录 Apollo 6.0 在 Ubuntu 18.04 中的完整安装及运行过程，并会阐述在虚拟机和物理机中进行安装时的细微区别。

# 1 前置依赖软件安装

- 安装 Ubuntu 18.04
- 安装 NVIDIA 显卡驱动
- 安装 Docker 引擎
- 安装 NVIDIA 容器工具包

## 1.1 安装 Ubuntu 18.04

**系统安装**

Ubuntu 18.04.5 LTS (Bionic Beaver) 是官方推荐版本，笔者此处使用的即为该版本。下载系统镜像，使用 Rufus 开源工具制作安装启动盘，在虚拟机环境或物理机环境（单系统 or 双系统）中安装系统。关于 VMWare 虚拟机的安装配置可以参考此前的文章[《Win 10 中通过 VMWare 16 在 UEFI 引导模式下安装 Ubuntu 18.04 虚拟机并自定义分区》](https://blog.shipengx.com/archives/5103b2eb.html)。

**更新和升级**

完成系统安装后，配置 Ubuntu 软件仓库，并选择国内更稳定的镜像源，例如清华源：https://mirrors.tuna.tsinghua.edu.cn/ubuntu/

[![配置 Ubuntu 软件仓库](https://image.shipengx.com/%E9%85%8D%E7%BD%AE%20Ubuntu%20%E8%BD%AF%E4%BB%B6%E4%BB%93%E5%BA%93.png)](https://image.shipengx.com/配置 Ubuntu 软件仓库.png)

[配置 Ubuntu 软件仓库](https://image.shipengx.com/配置 Ubuntu 软件仓库.png)

随后，进行更新和升级：

```
sudo apt-get update
sudo apt-get upgrade
```

## 1.2 安装 NVIDIA 显卡驱动

如果是在物理机中安装的 Ubuntu，且机器配有 NVIDIA 显卡，需要安装对应驱动：

```
sudo apt-get update
sudo apt-add-repository multiverse
sudo apt-get update
sudo apt-get install nvidia-driver-455
```

随后，可以通过在终端中执行 `nvidia-smi` 命令来查看 NVIDIA 显卡工作是否正常（完成驱动安装后可能需要重启），正常情况下终端将显示下面的信息：

```
Prompt> nvidia-smi
Mon Jan 25 15:51:08 2021
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 460.27.04    Driver Version: 460.27.04    CUDA Version: 11.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  GeForce RTX 3090    On   | 00000000:65:00.0  On |                  N/A |
| 32%   29C    P8    18W / 350W |    682MiB / 24234MiB |      7%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A      1286      G   /usr/lib/xorg/Xorg                 40MiB |
|    0   N/A  N/A      1517      G   /usr/bin/gnome-shell              120MiB |
|    0   N/A  N/A      1899      G   /usr/lib/xorg/Xorg                342MiB |
|    0   N/A  N/A      2037      G   /usr/bin/gnome-shell               69MiB |
|    0   N/A  N/A      4148      G   ...gAAAAAAAAA --shared-files      105MiB |
+-----------------------------------------------------------------------------+
```

> **注意：** 如果是在虚拟机中安装的 Ubuntu 或物理机没有配置 NVIDIA 显卡，此步务必跳过，否则将导致后续步骤中启动 Apollo 开发容器时失败。虚拟机情况下这样做的根本原因是，虚拟机中无法虚拟 NVIDIA 显卡。

## 1.3 安装 Docker 引擎

Apollo 6.0 需要 Docker 19.03 及以上版本，在终端中直接执行下述命令即可完成 Docker 社区版的安装：

```
curl https://get.docker.com | sh
sudo systemctl start docker && sudo systemctl enable docker
```

重启 Docker 守护进程以使改动生效：

```
sudo systemctl restart docker
```

完成 Docker 安装后，在终端中执行下述命令并重启系统，这样可以免去每次执行 Docker 命令时需要添加 `sudo` 的繁琐：

```
sudo groupadd docker
sudo usermod -aG docker your_username
```

## 1.4 安装 NVIDIA 容器工具包

如果是在物理机中安装的 Ubuntu，且机器配有 NVIDIA 显卡，在安装了驱动的前提下，还需要安装 NVIDIA 容器工具包以运行 Apollo Docker 镜像中的 CUDA：

```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get -y update
sudo apt-get install -y nvidia-docker2
```

> **注意：** 如果是在虚拟机中安装的 Ubuntu 或物理机没有配置 NVIDIA 显卡，此步同样需要跳过。

# 2 克隆 Apollo 源码

通过 SSH 方式或 HTTPS 方式克隆 Apollo 源码仓库：

```
# 使用 SSH 的方式
git clone git@github.com:ApolloAuto/apollo.git

# 使用 HTTPS 的方式
git clone https://github.com/ApolloAuto/apollo.git
```

GitHub 在国内访问速度可能很慢，可以使用 Gitee 替代：

```
# 使用 SSH 的方式
git clone git@gitee.com:ApolloAuto/apollo.git

# 使用 HTTPS 的方式
git clone https://gitee.com/ApolloAuto/apollo.git
```

# 3 启动 Apollo Docker 开发容器

进入到 Apollo 源码根目录，打开终端，执行下述命令以启动 Apollo Docker 开发容器：

```
./docker/scripts/dev_start.sh
```

不出意外得话，启动成功后将得到下面信息：

[![成功启动 Apollo docker 开发容器](https://image.shipengx.com/%E6%88%90%E5%8A%9F%E5%90%AF%E5%8A%A8%20Apollo%20docker%20%E5%BC%80%E5%8F%91%E5%AE%B9%E5%99%A8.png)](https://image.shipengx.com/成功启动 Apollo docker 开发容器.png)

[成功启动 Apollo docker 开发容器](https://image.shipengx.com/成功启动 Apollo docker 开发容器.png)

如果是在虚拟机中安装的 Ubuntu 或物理机没有配置 NVIDIA 显卡，但却又安装了 NVIDIA 驱动，则在执行上述启动容器的操作时将遇到下面的报错：

[![无法启动 Apollo docker 开发容器](https://image.shipengx.com/%E6%97%A0%E6%B3%95%E5%90%AF%E5%8A%A8%20Apollo%20docker%20%E5%BC%80%E5%8F%91%E5%AE%B9%E5%99%A8.png)](https://image.shipengx.com/无法启动 Apollo docker 开发容器.png)

[无法启动 Apollo docker 开发容器](https://image.shipengx.com/无法启动 Apollo docker 开发容器.png)

解决方法是直接卸载 NVIDIA 相关安装项：

```
sudo apt purge nvidia*
```

# 4 进入 Apollo Docker 开发容器

启动 Apollo Docker 开发容器后，执行下述命令进入容器：

```
./docker/scripts/dev_into.sh
```

可以发现，进入容器后终端信息发生了相应变化，后面的操作都将在容器中进行：

[![进入 Apollo docker 开发容器](https://image.shipengx.com/%E8%BF%9B%E5%85%A5%20Apollo%20docker%20%E5%BC%80%E5%8F%91%E5%AE%B9%E5%99%A8.png)](https://image.shipengx.com/进入 Apollo docker 开发容器.png)

[进入 Apollo docker 开发容器](https://image.shipengx.com/进入 Apollo docker 开发容器.png)

> 笔者是在 VMWare 虚拟机中进行的上述所有步骤，所以会看到上面黄色的 WARNING 信息。

# 5 在容器中构建 Apollo

进入 Apollo Docker 开发容器后，在容器终端中执行下述命令构建 Apollo：

```
./apollo.sh build
```

构建成功后将得到下面的信息：

[![Apollo 构建成功](https://image.shipengx.com/Apollo%20%E6%9E%84%E5%BB%BA%E6%88%90%E5%8A%9F.png)](https://image.shipengx.com/Apollo 构建成功.png)

[Apollo 构建成功](https://image.shipengx.com/Apollo 构建成功.png)

> 如果报无权限创建目录的问题，在命令前加 `sudo` 即可。

# 6 运行 Apollo

## 6.1 启动 Apollo

完成 Apollo 构建后，在容器终端中执行下述命令：

```
./scripts/bootstrap.sh start
```

上述命令会启动 DreamView 并使能模块监控机制，在浏览器中访问 http://localhost:8888 来显示 DreamView 界面：

[![DreamView 界面](https://image.shipengx.com/DreamView%20%E7%95%8C%E9%9D%A2.png)](https://image.shipengx.com/DreamView 界面.png)

[DreamView 界面](https://image.shipengx.com/DreamView 界面.png)

## 6.2 选择驾驶模式和地图

在 DreamView 界面的对应下拉框中选择驾驶模式为“Mkz Standard Debug”，选择地图为“Sunnyvale with Two Offices”：

[![DreamView 中选择驾驶模式和地图](https://image.shipengx.com/DreamView%20%E4%B8%AD%E9%80%89%E6%8B%A9%E9%A9%BE%E9%A9%B6%E6%A8%A1%E5%BC%8F%E5%92%8C%E5%9C%B0%E5%9B%BE.png)](https://image.shipengx.com/DreamView 中选择驾驶模式和地图.png)

[DreamView 中选择驾驶模式和地图](https://image.shipengx.com/DreamView 中选择驾驶模式和地图.png)

## 6.3 回放 Demo 数据

在容器终端中执行下述命令下载 demo 数据：

```
cd docs/demo_guide/
python3 record_helper.py demo_3.5.record
```

由于网络原因，下载可能失败，可以点击[这里](https://blog.shipengx.com/download/demo_3.5.record)手动下载并将数据放到 `apollo/docs/demo_guide/` 目录下。继续在容器终端中执行下述命令来播放数据，`-l` 表示循环播放（loop）：

```
cyber_recorder play -f demo_3.5.record -l
```

至此，DreamView 界面中将呈现出自车规划轨迹、他车预测轨迹、路网等各种信息：

[![回放 Log 时的 DreamView 界面](https://image.shipengx.com/%E5%9B%9E%E6%94%BE%20Log%20%E6%97%B6%E7%9A%84%20DreamView%20%E7%95%8C%E9%9D%A2.png)](https://image.shipengx.com/回放 Log 时的 DreamView 界面.png)

[回放 Log 时的 DreamView 界面](https://image.shipengx.com/回放 Log 时的 DreamView 界面.png)

# 参考

1. [Pre-requisite Software Installation Guide]([docs/specs/prerequisite_software_installation_guide.md · ApolloAuto/apollo - Gitee.com](https://gitee.com/ApolloAuto/apollo/blob/master/docs/specs/prerequisite_software_installation_guide.md))
2. [Apollo 软件安装指南]([docs/quickstart/apollo_software_installation_guide_cn.md · ApolloAuto/apollo - Gitee.com](https://gitee.com/ApolloAuto/apollo/blob/master/docs/quickstart/apollo_software_installation_guide_cn.md))
3. [How to Launch and Run Apollo]([docs/howto/how_to_launch_and_run_apollo.md · ApolloAuto/apollo - Gitee.com](https://gitee.com/ApolloAuto/apollo/blob/master/docs/howto/how_to_launch_and_run_apollo.md#run-apollo))
4. [在虚拟机上安装运行百度 Apollo 6.0](https://blog.csdn.net/qq1291917670/article/details/115013849)
5. [在vmware上跑apollo 6.0 (无GPU) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/357006215)