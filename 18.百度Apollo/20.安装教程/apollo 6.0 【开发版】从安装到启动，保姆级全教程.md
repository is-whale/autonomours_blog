- [apollo 6.0 【开发版】从安装到启动，保姆级全教程_樱桃小栗子的博客-CSDN博客_apollo教程](https://blog.csdn.net/DLL200122/article/details/123681231)

注意！！此方法安装的是**开发版**，也就是可以看见源码，可以自己开发的版本，如果只是想体验一下自动驾驶，对源码没有学习需要，那安装**发行版**就行，发行版装起来更简单，教程指路[《apollo6.0发行版安装全教程》](https://blog.csdn.net/DLL200122/article/details/123358179)

两个版本的前期软件安装工作是一样的。

# 1.必备软件安装

## 1.1安装Ubuntu linux

我之前试过安装VM虚拟机，但是虚拟机无法安装NVIDIA GPU驱动，不装这个驱动是无法运行感知模块的，我也没找到解决办法，这里还是建议大家直接安装Linux系统吧。教程请参考[《Windows10安装ubuntu18.04双系统教程》](https://www.cnblogs.com/masbay/p/11627727.html)

安装Ubuntu需要注意的点：

1.安装语言建议选择English(US)，有前辈说中文环境后面可能会导致乱码，大家还是不要铤而走险了。
2.硬盘划分。由于学习自动驾驶一般要安装几个大的软件，如Apollo、opencv等，所以建议对于/root划分60G到100G，/home分40G及其以上。（硬盘容量允许，肯定是越大越好）

3.装完系统后第一件事就是下载源更换为国内源，建议阿里源或清华源，如果不换源，那就很有可能卡死在下载中，我之前就痛苦很久，换了源之后就是神清气爽。更换教程请参考[Ubuntu 更换国内源](https://blog.csdn.net/qq_35451572/article/details/79516563)
4.系统进行第一次更新（命令sudo apt-get upgrade）后，在software & updates中，将更新设置为Never，需要的更新手动操作即可，否则不知道哪一天你就因为版本问题而疯掉，如下图

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

## 2.1下载Apollo 6.0

从github上下载：

```bash
git clone https://github.com/ApolloAuto/apollo.git
```

从gitee上下载

```bash
git clone https://gitee.com/ApolloAuto/apollo.git
```

如果你在这一步成功了，那你就可以直接到下一步，如果像我一样失败了，报错信息如下：

```vbnet
Cloning into 'apollo'...
remote: Enumerating objects: 329426, done.
remote: Counting objects: 100% (337/337), done.
remote: Compressing objects: 100% (336/336), done.
fatal: The remote end hung up unexpectedly.22 GiB | 6.13 MiB/s      
fatal: early EOF
fatal: index-pack failed
```

那就只克隆master分支最近一次的commit，减小文件大小：

```bash
git clone --depth 1 --branch master https://gitee.com/ApolloAuto/apollo.git
```

成功信息如下：

```vbnet
Cloning into 'apollo'...
Warning: Permanently added the RSA host key for IP address '13.250.177.223' to the list of known hosts.
remote: Enumerating objects: 313436, done.
remote: Counting objects: 100% (127/127), done.
remote: Compressing objects: 100% (77/77), done.
remote: Total 313436 (delta 63), reused 95 (delta 50), pack-reused 313309
Receiving objects: 100% (313436/313436), 2.42 GiB | 246.00 KiB/s, done.
Resolving deltas: 100% (234642/234642), done.
Checking out files: 100% (9715/9715), done.
```

## 2.2设置origin分支

如果我们只是下载运行，上述操作就够了。如果我们作为开发者想提交代码修改请求，还需要修改orgin分支为个人项目，upstream分支指向原作者项目地址，使用git修改操作如下：

```sql
git checkout master
git remote -v
git remote set-url origin git@github.com:YOUR_GITHUB_USERNAME/apollo.git
git remote add upstream git@github.com:ApolloAuto/apollo.git
git remote add upstream https://github.com/ApolloAuto/apollo.git
git remote -v
```

## 2.3 拉取镜像

```bash
bash docker/scripts/dev_start.sh
```

这个过程会比较慢，而且如果网速不好，会中断，如果你多次中断，我建议你可以切换手机热点，我刚开始用WiFi一直失败，后来连了手机热点（我的手机热点网速感觉并不如wifi快，但是热点的确可以成功，WiFi却不行，具体原因我也不清楚，大家可以试试看）

```vbnet
Adding user `xu' ...
Adding new user `xu' (1000) with group `docker' ...
Creating home directory `/home/xu' ...
Copying files from `/etc/skel' ...
[ OK ] Congratulations! You have successfully finished setting up Apollo Dev Environment.
[ OK ] To login into the newly created apollo_dev_xu container, please run the following command:
[ OK ]   bash docker/scripts/dev_into.sh
[ OK ] Enjoy!
```

显示这样，就说明成功了。

然后进入docker

```bash
bash docker/scripts/dev_into.sh
```

成功输出如下：

```rust
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
 
[xu@in-dev-docker:/apollo]$ 
```

## 2.4编译apollo

当然这里，首先要检查确保您在开发Docker容器中

有GPU的话

```bash
./apollo.sh build_opt_gpu
```

没有GPU,就用CPU

```bash
./apollo.sh build_opt
```

编译很考验电脑性能，这个过程需要很大的耐心，不用管，就让它自己跑就行了。如果有GPU,但是用GPU跑失败了，那就用CPU的指令试试，如果编译成功了，屏幕上会输出ok。

## 2.5下载数据包

电脑不是实车，自然没有数据，所以我们想运行，需要下载一个录好的数据包

```bash
cd docs/demo_guide/
python3 record_helper.py demo_3.5.record
```

通过这个指令，可以下载一个数据包，但是如果又是像我一样，网络不好又没有vpn，然后下载失败，那就换个解决办法。（下载失败，它会输出一个bad luck，如果成功就不用管后面的）

**如果下载失败看这里**，下载失败可能是没下载成功，也有可能是下载一个残缺的文件，所以大家进入apollo-docs-demo-guide,看一下有没有一个叫demo_3.5.record的文件，如果存在这个文件，那就说明是下载了一个残缺文件，所以一定要先把这个文件删除，否则没办法下载新的。

```ruby
rm demo_3.5.record
```

然后用这个新指令来下载

```ruby
wget https://apollo-system.cdn.bcebos.com/dataset/6.0_edu/demo_3.5.record
```

下载成功后，运行dreamview程序

```bash
bash scripts/bootstrap.sh
```

然后播放演示包

```csharp
cyber_recorder play -f demo_3.5.record --loop
```

 在浏览器中输入`http://localhost:8888`，访问 Apollo Dreamview：

![img](https://img-blog.csdnimg.cn/img_convert/e236c372dee0d06a5b5677550adb5161.png)

屏幕上的车辆可以正常行驶，到这里，apollo6.0开发版就安装完啦！可以用ctrl+C终止程序运行。