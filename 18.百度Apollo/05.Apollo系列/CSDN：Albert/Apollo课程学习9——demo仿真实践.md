- [Apollo 课程学习9——demo仿真实践_Albert的博客-CSDN博客](https://blog.csdn.net/weixin_43476492/article/details/108000187)

# 测试浏览器是否支持WebGL

打开 [https://get.webgl.org](https://get.webgl.org/)
测试结果：

![img](https://img-blog.csdnimg.cn/20200814160729407.jpg)
其他方法可参考
[Does My Browser Support WebGL](https://www.cnblogs.com/liushen/p/7159839.html)

# 安装[ubuntu](https://so.csdn.net/so/search?q=ubuntu&spm=1001.2101.3001.7020)系统

主要参考文档：
[Download Ubuntu Desktop](https://ubuntu.com/download/desktop)
[VMware WorkstationV15.5.6.16341506 官方中文正式版](https://www.cr173.com/soft/68480.html)
[VMware安装Ubuntu（虚拟机）](https://www.cnblogs.com/iors/p/10869124.html)

![img](https://img-blog.csdnimg.cn/20200814114925297.jpg#pic_center)
安装完成后，进入Terminal。

# git-lfs 安装

```go
sudo apt-get install git
```

![img](https://img-blog.csdnimg.cn/20200814124843929.jpg#pic_left)

```c
sudo apt install curl
```

![img](https://img-blog.csdnimg.cn/20200814124639613.jpg#pic_left)

```c
curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash
```

![img](https://img-blog.csdnimg.cn/20200814125554548.jpg#pic_left)

```c
sudo apt-get install git-lfs
```

![img](https://img-blog.csdnimg.cn/20200814130924911.jpg)

```c
sudo apt-get update
```

![img](https://img-blog.csdnimg.cn/20200814131103523.jpg)

# 下载apollo源码

命令（下拉最新版本apollo）：

```c
git clone https://github.com/apolloauto/apollo
```

或者[Github—apollo代码下载](https://github.com/ApolloAuto/apollo/)

因为github上下载得很慢，有时候会下载失败（尝试了一个下午，最后还是失败了）；
所以最后用[中国码云—apollo代码下载](https://gitee.com/guopu/apollo)，下载速度明显快了很多
（这里是从百度apollo–github官方移植过来的，代码是一样的）

我下载的是Apollo5.0的源码

![img](https://img-blog.csdnimg.cn/2020081422261842.jpg#pic_center)
![img](https://img-blog.csdnimg.cn/20200814222707493.jpg#pic_center)

```c
git clone https://gitee.com/guopu/apollo.git
```

![img](https://img-blog.csdnimg.cn/20200814223103448.jpg)
终于弄好了，在Github折腾了一下午的源码下载居然在码云10分钟就搞定了，笑哭

![img](https://img-blog.csdnimg.cn/2020081422394176.jpg)

# 安装docker

```c
cd apollo
bash docker/setup_host/install_docker.sh
```

![img](https://img-blog.csdnimg.cn/2020081506523725.jpg)

# 拉取镜像

**使用阿里云镜像加速器**
[参考文档](https://blog.csdn.net/weixin_43569697/article/details/89279225)

```c
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://           com"]   //将"registry-mirrors": ["https://......com"] （对应自己的加速地址）复制到文件中
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker

bash docker/scripts/dev_start.sh
```

![img](https://img-blog.csdnimg.cn/20200815100056744.png)