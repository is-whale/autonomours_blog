- [Apollo学习笔记2-docker安装_hello1268的博客-CSDN博客](https://blog.csdn.net/hello1268/article/details/112002481)

# 一、彻底卸载之前安装[docker](https://so.csdn.net/so/search?q=docker&spm=1001.2101.3001.7020)

1. 删除某软件,及其安装时自动安装的所有包

   ```
   sudo apt-get autoremove docker docker-ce docker-engine  docker.io  containerd runc
   sudo apt-get remove docker docker-engine docker.io containerd runc #官方
   ```

2. 删除无用的相关的配置文件

   ```
   dpkg -l | grep docker
   dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P # 删除无用的相关的配置文件
   ```

3. 卸载没有删除的docker相关插件(结合自己电脑的实际情况)

   ```
   sudo apt-get autoremove docker-ce-*
   sudo apt-get remove nvidia-docker2   #卸载nvidia docker
   sudo apt-get purge nvidia-docker   
   sudo apt-get -y remove docker docker-engine docker.io
   ```

   sudo apt-get purge docker-ce

4. 删除docker的相关配置&目录

   ```
   sudo rm -rf /etc/systemd/system/docker.service.d
   sudo rm -rf /var/lib/docker 
   sudo rm -rf /var/lib/nvidia-docker
   sudo rm -rf /etc/apt/sources.list.d/docker.*
   sudo rm -rf /etc/apt/sources.list.d/nvidia-*
   ```

5. 确定docker卸载完毕

   ```
   docker --version
   ```

# 二、安装docker

## 1、Installing Docker Engine

主要参考apollo官方文档: [prerequisite_software_installation_guide.md](https://github.com/ApolloAuto/apollo/blob/master/docs/specs/prerequisite_software_installation_guide.md).

我用的是apollo提供的脚本来安装的

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201231163405933.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

执行脚本安装Docker Engine

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201231164033302.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

## 2、安装 NVIDIA [Container](https://so.csdn.net/so/search?q=Container&spm=1001.2101.3001.7020) Toolkit

The NVIDIA Container Toolkit for Docker is required to run Apollo’s CUDA based Docker images.

You can run the following to install NVIDIA Container Toolkit:

```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get -y update
sudo apt-get install -y nvidia-docker2
```

重启docker使之生效

```
sudo systemctl restart docker
```

然后就可以愉快拉取Apollo的镜像了~，

# 三、Apollo镜像保存和加载

由于apollo镜像下载时间太长，我们直接加载之前已经保存的apollo镜像

1. 执行命令docker images查询目前镜像

   ```
   docker images
   ```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210101115740456.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

1. 执行命令docker images查询目前镜像

   ```
   docker save -o map_volume-san_mateo-latest.tar 48cd73de58ba
   ```

2. ls查看，镜像已保存![在这里插入图片描述](https://img-blog.csdnimg.cn/20210101120418463.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

3. 加载已保存镜像，到镜像路径下执行命令：

   ```
   docker load -i map_volume-san_mateo-latest.tar
   ```

# 四、本地和docker镜像互传文件

## 1. 本地文件传入docker镜像

1.先拿到容器的短ID或者指定的name。

```
docker ps -a
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210609164627505.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

2.然后根据这两项的任意一项拿到ID全称。

```
docker inspect -f '{{.Id}}' scrin/second-pytorch
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210609164656150.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

有了这个长长的ID的话，本机和容器之间的文件传输就简单了。

```
docker cp 本地文件路径 ID全称:容器路径

docker cp kitti dcb5ebba82c298dcd48c926fb15042d2128448648cbff02ce9e8c1cad81aa403:/root/data
```