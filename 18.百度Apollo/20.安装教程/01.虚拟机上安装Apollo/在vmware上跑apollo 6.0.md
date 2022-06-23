- [在vmware上跑apollo 6.0 (无GPU) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/357006215)

主要是参考[官网的教程](https://link.zhihu.com/?target=https%3A//github.com/ApolloAuto/apollo/blob/master/docs/quickstart/apollo_software_installation_guide.md):， 但是我自己目前是在vmware上跑而且GPU不能直连，所以安装教程里的GPU部分都跳过

## 1. 先装好Ubuntu 18.04和Docker

虚拟机不支持GPU那就先别装nvidia相关的（否则出错，还需要卸载nvdia相关的） 

[https://github.com/ApolloAuto/apollo/blob/master/docs/specs/prerequisite_software_installation_guide.md](https://link.zhihu.com/?target=https%3A//github.com/ApolloAuto/apollo/blob/master/docs/specs/prerequisite_software_installation_guide.md)

**Ubuntu链接** [https://releases.ubuntu.com/18.04.5/](https://link.zhihu.com/?target=https%3A//releases.ubuntu.com/18.04.5/)

```text
sudo apt-get update
sudo apt-get upgrade
```

**使用bash来装 docker**

> There is also a dedicated bash script Apollo provides to ease Docker installation, which works both for X86_64 and AArch64 platforms.

```text
https://github.com/ApolloAuto/apollo/blob/master/docker/setup_host/install_docker.sh
```

**运行保存的sh文件**

```text
chmod a+x install_docker.sh #给install_docker.sh可执行权限
./install_docker.sh
sudo systemctl restart docker
```

## 2. 下载Apollo 源

```text
git clone https://github.com/ApolloAuto/apollo.git
cd apollo
git checkout master
```

## 3. 启动阿波罗开发Docker容器

```text
bash docker/scripts/dev_start.sh
```

> （有些人可能前面无意中安装了NVIDIA相关的报错：[ERROR] Failed to start docker container "apollo_dev" based on image: apolloauto/apollo:dev-x86_64-20180906_2002。Error: Could not load UVM kernel module. Is nvidia-modprobe installed?，可以采用sudo apt purge nvidia* 卸载所有NVIDIA。然后重新bash docker/scripts/dev_start.sh）

```text
bash docker/scripts/dev_into.sh
./apollo.sh build
```

## 4. 构建Apollo

```text
./apollo.sh clean
./apollo.sh build_opt
```

> Note: Please run ./apollo.sh build_fe before ./apollo.sh build_opt if you made any modifications to the Dreamview frontend.

## 5. 运行

```text
./scripts/bootstrap.sh
```

打开浏览器 输入 http://localhost:8888
选择驾驶模式和地图：From the dropdown box of Mode Setup, select "Mkz Standard Debug" mode. From the dropdown box of Map, select "Sunnyvale with Two Offices".

重放demo记录：

```text
cd docs/demo_guide/
python3 record_helper.py demo_3.5.record
cyber_recorder play -f demo_3.5.record -l
```

> （这个可能遇到cyber_record 命令无法找到， 需要到apollo/cyber目录下运行 ./setup.bash， 然后 export PATH=/apollo/bazel-bin/cyber/tools/cyber_recorder/:$PATH,此时>>echo $PATH 路径应包含/ apollo / bazel-bin / cyber / tools / cyber_recorder / 再回到docs/demo_guide/下去） 就可以看到运行的了：

![img](https://pic2.zhimg.com/80/v2-78a38561114b8dc6cdad3062e5863d59_720w.jpg)

## 结束deamview

```text
cd ../..
root@in-dev-docker:/apollo# ./scripts/bootstrap.sh stop
```

## 退出docker Ctrl-D

## 要重新进的话

```text
cd apollo
bash docker/scripts/dev_start.sh
bash docker/scripts/dev_into.sh
运行：  ./scripts/bootstrap.sh
......
```