- [apollo6.0发行版安装到启动（内含超多踩坑细节，2022-3-8日亲测可用）_樱桃小栗子的博客-CSDN博客_apollo启动](https://blog.csdn.net/DLL200122/article/details/123358179)

**注意！！此方法安装的是发行版，发行版可以用来学习体验，比较好装，如果有开发和学习源码的需求请安装开发版。避免大家浪费时间。**

 本文中的安装版本为[Ubuntu](https://so.csdn.net/so/search?q=Ubuntu&spm=1001.2101.3001.7020) 18.04+ Apollo6.0,参考了这个前辈的文章，并对这个文章里的内容做了一些拓展补充，因为这个文章应该是比较久之前的了，有些内容需要更新。[Ubuntu 18.04安装Apollo 6.0：从零开始到启动Demo（超多细节）](https://blog.csdn.net/shao918516/article/details/119223577)

**目录**

[1.必备软件安装](https://blog.csdn.net/DLL200122/article/details/123358179#t0)

[1.1安装Ubuntu linux](https://blog.csdn.net/DLL200122/article/details/123358179#t1)

[1.2.安装NVIDIA GPU DRIVER](https://blog.csdn.net/DLL200122/article/details/123358179#t2)

[1.3.安装docker engine](https://blog.csdn.net/DLL200122/article/details/123358179#t3)

[1.4安装NVIDIA Container Toolkit](https://blog.csdn.net/DLL200122/article/details/123358179#t4)

[2.安装apollo](https://blog.csdn.net/DLL200122/article/details/123358179#t5)

[2.1下载安装包](https://blog.csdn.net/DLL200122/article/details/123358179#t6)

[2.2解压安装包](https://blog.csdn.net/DLL200122/article/details/123358179#t7)

[2.3 安装](https://blog.csdn.net/DLL200122/article/details/123358179#t8)

[3.跑demo](https://blog.csdn.net/DLL200122/article/details/123358179#t9)

[3.1启动dreamview程序](https://blog.csdn.net/DLL200122/article/details/123358179#t10)

[3.2下载演示包](https://blog.csdn.net/DLL200122/article/details/123358179#t11)

# 1.必备软件安装

## 1.1安装Ubuntu linux

我之前试过安装VM虚拟机，但是虚拟机无法安装NVIDIA GPU驱动，不装这个驱动是无法运行感知模块的，我也没找到解决办法，这里还是建议大家直接安装Linux系统吧。教程请参考[《Windows10安装ubuntu18.04双系统教程》](https://www.cnblogs.com/masbay/p/11627727.html)

安装Ubuntu需要注意的点：

1.安装语言建议选择English(US)，有前辈说中文环境后面可能会导致乱码，大家还是不要铤而走险了。
2.硬盘划分。由于学习自动驾驶一般要安装几个大的软件，如Apollo、opencv等，所以建议对于/root划分60G到100G，/home建议分40G以上。
3.装完系统后第一件事就是下载源更换为国内源，建议阿里源或清华源，如果不换源，那就很有可能卡死在下载中，我之前就痛苦很久，换了源之后就是神清气爽。更换教程请参考[Ubuntu 更换国内源](https://blog.csdn.net/qq_35451572/article/details/79516563)
4.系统进行第一次更新（命令sudo apt-get upgrade）后，在software & updates中，将更新设置为Never，需要的更新手动操作即可，否则不知道哪一天你就因为版本问题而疯掉，如下图：

![img](https://img-blog.csdnimg.cn/526d0621033847d2baf62c3750cb45ce.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoYW85MTg1MTY=,size_16,color_FFFFFF,t_70#pic_center)

 5.安装搜狗输入法，请参考[《Ubuntu18.04下安装搜狗输入法》](https://blog.csdn.net/lupengCSDN/article/details/80279177)Ubuntu输入中文还是搜狗最好用。

## 1.2.安装NVIDIA GPU DRIVER

对于N卡用户，需要单独安装对应显卡驱动及cuda，安装之前，需要根据Ubuntu的内核版本来确定对应版本的显卡驱动。查看命令如下：

```ruby
shaw@p1:~$ uname -r
5.4.0-81-generic
shaw@p1:~$ ubuntu-drivers devices
== /sys/devices/pci0000:00/0000:00:01.0/0000:01:00.0 ==
modalias : pci:v000010DEd00001FB8sv000017AAsd0000229Fbc03sc00i00
vendor   : NVIDIA Corporation
driver   : nvidia-driver-460-server - distro non-free
driver   : nvidia-driver-470 - distro non-free recommended
driver   : nvidia-driver-418-server - distro non-free
driver   : nvidia-driver-450-server - distro non-free
driver   : nvidia-driver-460 - distro non-free
driver   : xserver-xorg-video-nouveau - distro free builtin
 
== /sys/devices/pci0000:00/0000:00:1d.6/0000:52:00.0 ==
modalias : pci:v00008086d00002723sv00008086sd00000080bc02sc80i00
vendor   : Intel Corporation
manual_install: True
driver   : backport-iwlwifi-dkms - distro free
```

根据推荐的版本号，安装命令如下：

```vbnet
shaw@p1:~$ sudo apt-add-repository multiverse
shaw@p1:~$ sudo apt-get update
shaw@p1:~$ sudo apt-get install nvidia-driver-470
...
update-initramfs: Generating /boot/initrd.img-5.4.0-81-generic
I: The initramfs will attempt to resume from /dev/nvme1n1p2
I: (UUID=3d8c08e6-e615-4e5d-94ef-ca7744ce78c1)
I: Set the RESUME variable to override this.
```

安装完成后，可以通过命令NVIDIA_SMI测试，出现下面提示说明安装成功：

```sql
shaw@p1:~$ nvidia-smi
Sun Aug 22 14:03:06 2021       
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 470.57.02    Driver Version: 470.57.02    CUDA Version: 11.4     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|                               |                      |               MIG M. |
|===============================+======================+======================|
|   0  Quadro T2000        Off  | 00000000:01:00.0 Off |                  N/A |
| N/A   55C    P8     3W /  N/A |    174MiB /  3911MiB |      6%      Default |
|                               |                      |                  N/A |
+-------------------------------+----------------------+----------------------+
                                                                               
+-----------------------------------------------------------------------------+
| Processes:                                                                  |
|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
|        ID   ID                                                   Usage      |
|=============================================================================|
|    0   N/A  N/A      1253      G   /usr/lib/xorg/Xorg                 97MiB |
|    0   N/A  N/A      1509      G   /usr/bin/gnome-shell               74MiB |
+-----------------------------------------------------------------------------+
```

如果不成功，就把ubuntu系统重启一下使安装生效，我就是重启后才成功的。

## 1.3.安装docker engine

```erlang
shaw@p1:~$ sudo apt install docker.io
...
Created symlink /etc/systemd/system/multi-user.target.wants/containerd.service → /lib/systemd/system/containerd.service.
Setting up bridge-utils (1.5-15ubuntu1) ...
Setting up ubuntu-fan (0.12.10) ...
Created symlink /etc/systemd/system/multi-user.target.wants/ubuntu-fan.service → /lib/systemd/system/ubuntu-fan.service.
Setting up pigz (2.4-1) ...
Setting up docker.io (20.10.7-0ubuntu1~18.04.1) ...
Adding group `docker' (GID 127) ...
Done.
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /lib/systemd/system/docker.service.
Created symlink /etc/systemd/system/sockets.target.wants/docker.socket → /lib/systemd/system/docker.socket.
Processing triggers for systemd (237-3ubuntu10.51) ...
Processing triggers for man-db (2.8.3-2ubuntu0.1) ...
Processing triggers for ureadahead (0.100.0-21) ...
ureadahead will be reprofiled on next reboot
shaw@p1:~$ docker [tab]
docker        dockerd       docker-init   docker-proxy  
shaw@p1:~$ docker --version
Docker version 20.10.7, build 20.10.7-0ubuntu1~18.04.1
```

安装完成后，创建组docker，并将当前用户加入组中，后续就可以以用户身份操作docker而不是root，这一步至关重要，命令如下：

```ruby
shaw@p1:~$ sudo groupadd docker
shaw@p1:~$ sudo usermod -aG docker $USER
```

此时重新登录系统（重启，这一步一定要做，否则会警告找不到用户而uid和gid又不匹配，造成错误）。完成添加用户到docker组的操作，以后就可以以用户身份操作Docker。
最后使用hello-world测试docker：

```vbnet
shaw@p1:~$ systemctl start docker && systemctl enable docker
shaw@p1:~$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
b8dfde127a29: Pull complete 
Digest: sha256:0fe98d7debd9049c50b597ef1f85b7c1e8cc81f59c8d623fcb2250e8bec85b38
Status: Downloaded newer image for hello-world:latest
 
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

**注意**：Docker的更新也比较频繁，经常会出现新版本安装不成功又禁用旧版本服务的情况，此时别慌，仅需以下几个命令即可解决：

```ruby
shaw@p1:~$ service docker start 
Failed to start docker.service: Unit docker.service is masked.
shaw@p1:~$ systemctl unmask docker.service
shaw@p1:~$ systemctl unmask docker.socket
shaw@p1:~$ systemctl start docker.service
shaw@p1:~$ service docker start 
docker run hello-world
```

前辈建议非必要不要更新docker，虽然我刚开始做，没什么感受，但是想必大家都有被版本问题搞疯的经历，所以这里我们也按部就班地做吧。

## 1.4安装NVIDIA Container Toolkit

运行以下命令安装 NVIDIA Container Toolkit：

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get -y update
sudo apt-get install -y nvidia-docker2
```

安装完成后，重启docker使改动生效

```undefined
sudo systemctl restart docker
```

# 2.安装apollo

执行下列代表来检查docker的运行状态。

```lua
systemctl status docker
```

如果docker不在运行，就启动docker

```sql
systemctl start docker
```

先新建一个文件夹用来放apollo，挂载在/home下，我的叫code

```css
mkdir code && cd code
```

## 2.1下载安装包

使用gitee或者github的下载方法我都试过，对网速要求太高，我失败多次，这个用安装包下载的方法既快速也不易出错，推荐使用。安装包下载链接[Apollo](https://apollo.auto/developer_cn.html) ，直接下载在自己刚刚建好的文件夹里面，我就直接放在了code里。

## 2.2解压安装包

```undefined
tar -xvf apollo_v6.0_edu_amd64.tar.gz
```

## 2.3 安装

```bash
./apollo.sh
```

这个过程稍微有点长，我家网不好，装了大概四个小时，这个时候不用管。如果报了错，比如failed to start container，那很有可能没有启动docker，启动docker后再试试。

脚本执行成功后，将显示以下信息，您将进入 Apollo 的运行容器：

```ruby
[user@in-runtime-docker:/apollo]$ 
```

# 3.跑demo

进入容器之后，我们跑一下dremview的一个demo来测试是否安装成功。

## 3.1启动dreamview程序

在命令行中，输入以下命令，启动 Apollo 的 Dreamview 程序。

```bash
bash scripts/bootstrap.sh
```

如果报错

```haskell
Failed to start Dreamview. Please check /apollo/data/log or /apollo/data/core for more information
```

 那就关闭重新打开

```bash
bash scripts/bootstrap.sh stop
bash scripts/bootstrap.sh
```

## 3.2下载演示包

Record 是 Apollo 记录数据的一种数据格式，也就是这是录好的现成数据。下载指令

```ruby
wget https://apollo-system.cdn.bcebos.com/dataset/6.0_edu/demo_3.5.record
```

3.3播放演示包

```csharp
cyber_recorder play -f demo_3.5.record --loop
```

选项 `--loop` 用于设置循环回放模式。

在浏览器中输入 `http://localhost:8888`，访问 Apollo Dreamview：

![img](https://img-blog.csdnimg.cn/img_convert/942a3af8b45ae1924f360e7a6a265b23.png)

如果一切正常，可以看到一辆汽车在 Dreamview 里移动。那就说明到这里apollo已经全部安装成功啦！