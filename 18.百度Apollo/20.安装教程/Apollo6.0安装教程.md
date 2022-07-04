- [可能是知乎上已知最详细的Apollo6.0安装教程 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/425483403)
- [Apollo6.0安装教程_vigigo的博客-CSDN博客_apollo dreamview 安装](https://blog.csdn.net/qq_40574123/article/details/121037037)
- [Apollo 笔记（01）— 下载、编译、启动、运行官方demo_wohu1104的博客-CSDN博客](https://blog.csdn.net/wohu1104/article/details/125464491)

基础准备：

**必须安装英伟达显卡驱动，且CUDA>=10.2**

我们工程师的设备（供参考）：

asus FA5061I, Ubuntu 18.04, AMD R5, 16G RAM, 1660Ti

正式安装开始

## 1.安装docker

参考的官方文档：[https://www.svlsimulator.com/docs/system-und1er-test/apollo6-0-instructions/](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/docs/system-und1er-test/apollo6-0-instructions/)

安装Docker CE ([参考官方文档](https://link.zhihu.com/?target=https%3A//docs.docker.com/engine/install/ubuntu/))

### 1.1 删除旧的docker版本（docker, docker.io, docker-engine）

```powershell
sudo apt-get remove docker docker-engine docker.io containerd runc
```

It's ok 如果apt-get 返回没有packages被安装了，

在第一次给机子安装docker engine时，需要set up the Docker repository， 之后就可以从repository中安装更新Docker

### 1.2 Set up the repository

更新apt package index 并且安装packages 以允许apt通过HTTPS来用repository

```text
sudo apt-get update
```

```text
 sudo apt-get install \ 
    apt-transport-https \ 
    ca-certificates \ 
    curl \ 
    gnupg \ 
    lsb-release
```

添加Docker's official GPG key:

```text
 curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

set up the **stable** repository

```text
 echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 1.3 安装Docker Engine

更新apt并且安装最新的Docker Engine版本，或者可以选择指定版本

（本机上安装了当前最新版20.10.7，没有问题）

```text
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

如果要安装指定版本：

**i. 列出available versions in my repo:**

![img](https://pic4.zhimg.com/80/v2-6f468ec93b7f83adb52f19c53118a28b_720w.jpg)

**ii. 安装一个指定版本，**例如, `5:20.10.7~3-0~ubuntu-bionic`.

```text
sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io
```

**iii. 验证是否安装成功, 用 hello-world**

```text
sudo docker run hello-world
```

**（当无法pull image的时候比如显示error response from daemon,**

**就指定国内的镜像源，新建个 /etc/docker/daemon.json，在里面写）**

```text
{
"registry-mirrors":["https://docker.mirrors.ustc.edu.cn"]
}
```

(然后重启docker, )

```text
sudo /etc/init.d/docker restart
```

到此docker engine安装完成

如果要卸载docker engine, CLI, 和Containerd packages:

```text
 sudo apt-get purge docker-ce docker-ce-cli containerd.io
```



```text
sudo rm -rf /var/lib/docker 
sudo rm -rf /var/lib/containerd
```

必须手动删除任何edited过的configuration files

(optional)官方文档提示：如果docker 是以sudo 开始的，Apollo可能无法运行，他们建议通过[post installation steps](https://link.zhihu.com/?target=https%3A//docs.docker.com/engine/install/linux-postinstall/)。

我在本机安装时似乎没有遇到这个问题。（遇到了）

如果遇到，可以试试

To create the `docker` group and add your user:

a. Create the `docker` group.

```text
$ sudo groupadd docker
```

b. Add your user to the `docker` group.

```text
$ sudo usermod -aG docker $USER
```

c. Log out and log back in so that your group membership is re-evaluated.

If testing on a virtual machine, it may be necessary to restart the virtual machine for changes to take effect.

On a desktop Linux environment such as X Windows, log out of your session completely and then log back in.

On Linux, you can also run the following command to activate the changes to groups:

```text
$ newgrp docker 
```

d. Verify that you can run `docker` commands without `sudo`.

```text
$ docker run hello-world
```

## 2.安装NVIDIA Docker 2 （[可参考官方文档](https://link.zhihu.com/?target=https%3A//docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html%23getting-started)）

再次强调：一定要先安装CUDA>=10.2, 不然之后apollo编译时会报错

### 2.1 Setting up Docker

Docker-CE on Ubuntu can be setup using Docker’s official convenience script:

```text
curl https://get.docker.com | sh \
  && sudo systemctl --now enable docker
```

### 2.2 Setting up NVIDIA Container Toolkit

Setup the `stable` repository and the GPG key:

```text
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \ 
   && curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add - \ 
   && curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```

(optional)To get access to `experimental` features such as [CUDA on WSL](https://link.zhihu.com/?target=https%3A//docs.nvidia.com/cuda/wsl-user-guide/index.html) or the new [MIG capability](https://link.zhihu.com/?target=https%3A//docs.nvidia.com/datacenter/tesla/mig-user-guide/index.html) on A100, you may want to add the `experimental` branch to the repository listing:

```text
curl -s -L https://nvidia.github.io/nvidia-container-runtime/experimental/$distribution/nvidia-container-runtime.list | sudo tee /etc/apt/sources.list.d/nvidia-container-runtime.list
```

### 2.3 在updating the package listing 后安装nvidia-docker2

```text
sudo apt-get update
sudo apt-get install -y nvidia-docker2
```

重启docker daemon 来完成安装

```text
sudo systemctl restart docker
```

## 3.安装Apollo 6.0 ([官方文档](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/docs/system-under-test/apollo6-0-instructions/%23top))

```text
git clone https://github.com/ApolloAuto/apollo.git
```

Checkout the `r6.0.0` branch:

```text
cd apollo 
git checkout r6.0.0 
 
```

lanuch the container navigate

```text
./docker/scripts/dev_start.sh 
```

编译完后会有container,

进入container

```text
./docker/scripts/dev_into.sh 
```

Build Apollo (optimized, not debug, with GPU support):

```text
./apollo.sh build_opt_gpu  
```

**（optional）我在此遇到的问题：内存不足**

16G的内存不足以支撑整个build，需要增加swap space ([参考网站](https://link.zhihu.com/?target=https%3A//bogdancornianu.com/change-swap-size-in-ubuntu/))

i. 关闭所有的swap 进程

```text
sudo swapoff -a 
```

Ii. Resize the swap （8G的话就是，bs=1G * count =8 ）

if = input file

of = output file

bs = block size

count = multiplier of blocks

```text
sudo dd if=/dev/zero of=/swapfile bs=1G count=8 
```

iii. 更改permission

```text
sudo chmod 600 /swapfile 
```

iv. 让文件夹作为swap使用

```text
sudo mkswap /swapfile 
```

v. 激活swap文件夹

```text
sudo swapon /swapfile 
```

Vi. Edit /etc/fstab and add the new swapfile if it isn’t already there

```text
/swapfile none swap sw 0 0 
```

vii. 查看现在swap的容量大小

```text
grep SwapTotal /proc/meminfo 
```

## 小结

按照上述步骤可以完成安装Apollo6.0，文章里面有提及我们在安装之时遇到的一些问题，个中心路历程和解决的方式方法供大家参考。如大家在安装配置中有遇到过其他问题，欢迎留言或私信探讨交流。