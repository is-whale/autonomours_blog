- [Apollo学习笔记1-ESD_CAN调试_hello1268的博客-CSDN博客](https://blog.csdn.net/hello1268/article/details/113704449)

# 适配 ESD CAN

CANBUS模块是[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020)需要根据底盘协议写底盘控制和反馈代码的地方，感觉也是Apollo最难调试的部分。这部分首先要选好CAN卡，因为不是Apollo推荐的CAN卡，驱动程序和对应接口，可能都需要自己调整，Apollo推荐的是ESD CAN。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210205214908940.png#pic_center)

**注：**１.购买IPC时需要注意主板上是否有PICe[插槽](https://so.csdn.net/so/search?q=插槽&spm=1001.2101.3001.7020)， ESDCAN卡是要插在这个槽上的。同时注意在你的CAN网络中是否存在120欧姆的匹配电阻，如果没有，ESDCAN上的JX300上的跳线拿下来插到JX400上，如果有，不用动。

 ２.Apollo给的文档中可以找到安装ESD CAN的安装方式(https://github.com/ApolloAuto/apollo-kernel/blob/master/linux/ESDCAN-README.md)，提供了两种方式：第一种方式在编译Apollo的时候同时编译，第二种方式在build Apollo之后安装。本文采用第二种方式，Apollo也推荐采用第二种方式。

 ３.Apollo的消息传递全部是由google的Protocol Buffers来实现，建议搭建先看一下相关的教程：https://developers.google.com/protocol-buffers/及http://www.ibm.com/developerworks/cn/linux/l-cn-gpb/。.proto生成的文件都是写保护的，不可以修改。

### 1. ESD CAN卡安装

首先把跳线帽安到CAN卡相应的位置，之后把CAN卡插到工控机（IPC）的插槽里。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210205215012194.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

### 2.ESD CAN驱动安装

- **驱动下载**

  官网下载驱动包`esdcan-pcie402-linux-x86_64-4.1.1.tgz`，由于使用Ubuntu18.04，且内核版本为5.4.0-58，**目前ESD CAN官网只有此驱动支持系统内核5.0以上。**

  下载完毕后，解压，解压后的文件目录如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210205215138986.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

- **驱动安装**

  （1） 将目录`esdcan-pcie402-linux-x86_64-4.1.1/include`下的`ntcan.h`复制到目录`/usr/local/include` 下，程序在调用CAN接口时都需要包含该头文件

  ```shell
  cd esdcan-pcie402-linux-x86_64-4.1.1/include
  sudo cp ntcan.h /usr/local/include"
  ```

  （2）将目录`esdcan-pcie402-linux-x86_64-4.1.1/lib64`下的动态链接库`libntcan.so.4.2.3`复制到目录`/usr/local/lib`下

  ```shell
  sudo cp libntcan.so.4.2.3 /usr/local/lib
  ```

  进入`/usr/local/lib`，执行以下命令

  ```shell
  cd /usr/local/lib
  sudo ldconfig -v -n /usr/local/lib   #会生成"libntcan.so.4"文件
  sudo ln -sfv libntcan.so.4 libntcan.so  #会生成"libntcan.so"文件。
  ```

  （3）确认`/etc/udev/rules.d`中是否有`esdcan-pcie402-dev.rules`，如果没有从`esdcan-pcie402-linux-x86_64-4.1.1/udev`目录拷贝。

  ```shell
  sudo cp esdcan-pcie402-dev.rules /etc/udev/rules.d/
  sudo service udev restart
  ```

  （4）建立动态链接库

  ```shell
  $ ldconfig -p | grep ntcan
  $ cat /etc/ld.so.conf | grep /usr/local/lib
  $ sudo sh -c "echo /usr/local/lib >> /etc/ld.so.conf"
  $ sudo ldconfig
  ```

  动态链接库建立完成

  （5）测试CAN卡工作。请接入您的CAN分析仪，如果没有，也可以接入一个能发送CAN帧设备(比如单片机、ARM等)，设置[波特率](https://so.csdn.net/so/search?q=波特率&spm=1001.2101.3001.7020)为500k，并能发送数据到ESD CAN。

  ```shell
  $ cantest 3 0 0x00 0xffff 1 10 100 1000 5000 2 -1
  ```

  > 每个参数的含义为：
  >
  > 3 – canRead
  >
  > 0 – net0
  >
  > 0x00 – first-id 0x00
  >
  > 0xffff – last-id 0xffff
  >
  > 1 – count of CMSG-packets
  >
  > 10 – txbuf (useless here)
  >
  > 100 – rxbuf
  >
  > 1000 – tx timeout every 1 second (useless here)
  >
  > 5000 – rx timeout every 5 seconds
  >
  > 2 – baud rate 500k bit/s
  >
  > -1 – count of ntcan-API-Calls, -1 is forever canRead the bus.
  >
  > 如果ESD CAN接收到了数据，那么CAN卡驱动调试完毕。

  下图为使用cantest发送数据，ESD CAN接收到的数据
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210205215350430.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

- **Apollo安装ESD CAN内核驱动**

  在目录"esdcan-pcie402-linux-x86_64-4.1.1/src"下，参考apollo安装esdcan驱动方式，生成esdcan-pcie402.ko文件

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210205215427933.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

执行命令如下

```shell
make -C /lib/modules/`uname -r`/build M=`pwd`  #生成esdcan-pcie402.ko文件
sudo make -C /lib/modules/`uname -r`/build M=`pwd` modules_install  #加载ko文件
sudo install -p -v -g root -o root -m u=rw,g=r,o=r esdcan-pcie402.ko /lib/modules/$(uname -r)/kernel/drivers/pci/ #同时把ko文件拷贝到/lib/modules/$(uname -r)/kernel/drivers/pci/目录
```

### 3.Apollo ESD CAN 库添加

参考Apollo官方文档： [Apollo ESDCAN 库添加](https://github.com/ApolloAuto/apollo/tree/master/third_party/can_card_library/esd_can).

（1）拷贝esdcan-pcie402-linux-x86_64-4.1.1/include到apollo/third_party/can_card_library/esd_can/目标下

```shell
sudo cp -r include/ sudo cp -r include/ ~/apps/apollo/third_party/can_card_library/esd_can/
```

（2）拷贝esdcan-pcie402-linux-x86_64-4.1.1/lib64到apollo/third_party/can_card_library/esd_can/目标下，并重命名lib

```shell
sudo cp -r include/ sudo cp -r include/ ~/apps/apollo/third_party/can_card_library/esd_can/
sudo mv lib64 lib
```

（3）添加esdcan库软链接

```shell
sudo ln -s libntcan.so.4.0.1 libntcan.so.4;
sudo ln -s libntcan.so.4.0.1 libntcan.so.4.0
```