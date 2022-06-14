- [Apollo课程学习7——ROS_Albert的博客-CSDN博客](https://blog.csdn.net/weixin_43476492/article/details/107991069)

目前ROS仅适用于Apollo 3.0之前的版本，最新代码及功能还请参照Apollo 3.5及5.0版本。

# ROS概述

## 一、背景介绍

![img](https://img-blog.csdnimg.cn/20200813204435541.jpg#pic_center)
ROS是机器人学习和无人车学习最好Linux平台软件，资源丰厚。无人车的规划、控制算法通常运行在Linux系统上，各个模块通常使用ROS进行连接。

![img](https://img-blog.csdnimg.cn/20200813204449925.jpg#pic_center)

## 二、ROS的几个核心概念

- **松耦合**：ROS是一个松耦合的框架，松耦合就是各个节点之间的通信是一个解耦合的关系。
- **节点**：一个算法模块，比如自动驾驶系统里面的感知模块、定位模块、决策模块或者控制模块，这些模块就是一个简单的算法集合，在ROS里面被称为一个节点。
- **节点管理器Roscore**：在ROS里面被定义为Master，用来集中式管理各个独立的、松耦合、无序节点之间的逻辑关系，它是轻量级的介入，当各个节点启动完成以后，他们在通信连接完成之前起到中转也就是类似于交换机的作用。
- **Topic**：两个节点之间的通信主题。Topic内部使用的数据格式是Message。Message是一系统简单的数据类型或者是一些自定义的复杂数据类型，所组装成的一个描述文件。

![img](https://img-blog.csdnimg.cn/20200814172249199.png#pic_center)

- **Roscore**：启动一个节点管理器。Roscore默认启动的时候启动了一个**隐藏节点**，它是一个记录日志相关的节点，所有节点发生的Log都会被Roscore启动的Rosout所订阅，订阅完之后会根据一些特定的规则把这些Log分级，然后分模块、分文件打印到对应的模块日志里。
- **Roslaunch**：启动很多个节点。
- **Rosnode list**：列出当前系统里面所存在的节点。
- **Rosnode info**：查看某一节点的具体的一些信息。
- **Rostopic list**：可以查看所存在Topic的一些列表。
- **Rostopic info**：可以查看到发送这个Topic的发送方，订阅这个Topic的订阅方。
- **Rostopic type**：查看Topic内部所使用的MSG的数据结构。
- **Rostopic pub**：调试计算节点模块的一些基本功能。
- **Rostopic echo**：是相当于起了一个Listener节点，去展示Talker发的Topic包含的具体信息。
- **Rostopic HZ**：统计Talker节点发送Obstacle topic的频率，根据此频率能简单的探测系统是否按照我们所预期的方向来执行。

![img](https://img-blog.csdnimg.cn/20200814180515830.png#pic_center)
**两个节点进行通信的链路过程**，大概分为五步：

- 第一步：发送节点去向Master注册一个发送节点。
- 第二步：接收节点去向Master注册一个接收节点。
- 第三步：Master向接收节点发送一个已有发送节点的一个信息拓扑。
- 第四步：接收节点拿到这个拓扑信息之后去向发送节点请求建立一个tcp连接。
- 第五步：在发送节点和接收节点建立一个P2P的单点拓扑连接之后就持续不断的向接收节点发送信息。

## 三、ROS的Catkin编译系统

ROS是基于Cmake编写的Catkin编译系统。编译完成之后，通过Source devel下面的Setup bash就可以把自己编写的节点程序给Source到ROS的环境里面，然后去执行节点里面的一些基本功能。

![img](https://img-blog.csdnimg.cn/20200814173715427.png#pic_center)
Catkin config指定了命令行编译的一些方式，这些方式可以在Cmakelists里面进行编写：

![img](https://img-blog.csdnimg.cn/20200814174234939.png#pic_center)

## 四、ROS的仿真功能Gazebo

![img](https://img-blog.csdnimg.cn/20200814174421559.png#pic_center)

# ROS的不足

![img](https://img-blog.csdnimg.cn/20200814174839454.png#pic_center)

- 实验性项目里面采用的Topic是Message，数据量是比较小的，但在实际自动驾驶场景里面数据量非常大。**ROS架构对大数据传输存在很大的性能瓶颈**，一种直接后果是时延非常高，这在自动驾驶整个系统里面是非常危险的。
- **中心化的网络存在明显的单点风险**。如果Roscore存在一些故障退出，而节点之间使用了需要不定时的交互方式，像Service 、Parem进行数据交互的时候就会存在一定的风险。如果是分布式系统， Roscore只存在于一台机器上，Roscore如果出现故障，两台机器之间通信就处于一个不可信的状态。
- ROS现有的数据格式**缺少后向兼容**。

# Apollo ROS对ROS的改进

![img](https://img-blog.csdnimg.cn/20200814175259523.png#pic_center)

## 一、通信性能优化

- **原因**：自动驾驶大量使用传感器引发很大的传输带宽需求。单路传感器消息有多个消费者时负载成倍增长。
- Apollo ROS做了一个基于共享内存的通信机制减少数据的复制次数，从而提升这种通信模式的效率：

![img](https://img-blog.csdnimg.cn/20200814175513343.png#pic_center)
![img](https://img-blog.csdnimg.cn/20200814175630170.png#pic_center)
![img](https://img-blog.csdnimg.cn/2020081417564835.png#pic_center)

## 二、去中心化网络拓扑

**拓扑网络的缺点**：

![img](https://img-blog.csdnimg.cn/20200814175903115.png#pic_center)
Apollo ROS进行了比较大的改造：先把这个中心化的网络拓扑给去掉，然后建立了一个点对点之间的一个复杂网络拓扑，主要是使用RTPS服务发现协议去完成P2P网络拓扑。

![img](https://img-blog.csdnimg.cn/20200814180558104.png#pic_center)
通过**RTPS拓扑发现方式**，Apollo ROS去除了对Rosmaster这一个单点的依赖，从而提升整个系统的鲁棒性。这个修改完全**是对ROS底层的修改**，用户基于原生ROS代码写的节点程序，到Apollo ROS是完全兼容的一个迁移即开发者不需要去改动任何的接口，就可以直接使用RTPS网络拓扑这种新的关系建立。

### 1、节点建立连接和通讯流程

**第一步：Sub节点启动，通过组播向网络注册。**
订阅节点在启动的时候，它会向当前这个域里面所有的节点发送信息：现在有一个新的节点要启动。

![img](https://img-blog.csdnimg.cn/2020081418084933.png#pic_center)
**第二步：通过节点发现，两两建立unicast。**
所有的节点在接收到新加入这个节点发生拓扑信息变更之后，会和新加入这个节点分别建立两两连接关系。

![img](https://img-blog.csdnimg.cn/20200814180912612.png#pic_center)
**第三步：向新加入的节点发送它们已经有拓扑信息。**
所有已经存在的节点会向新加入的节点发送它们已经有拓扑信息，这个连接关系发送给接收节点，供接收节点去更新自己的网络拓扑结构。

![img](https://img-blog.csdnimg.cn/20200814180926682.png#pic_center)
**第四步：收发双方建立连接，开始通信。**
当新加入节点接到所有节点发送出来的历史拓扑信息之后，它会根据它自己注册的实际消息内容去决定和哪些节点建立实际的通信连接。

![img](https://img-blog.csdnimg.cn/20200814181309142.png#pic_center)

## 三、数据兼容性扩展

Apollo ROS 为了满足数据兼容，深度整合了Protobuf的功能。用户可以直接定义Proto的字段信息，同时信息传递的过程**不需要再进行额外的Message的数据转化**。另外，在使用调试工具的时候，通过Rostopic echo可以看出原始消息传递的实际展示。

原生ROS和Apollo ROS对数据兼容支持的对比：

![img](https://img-blog.csdnimg.cn/20200814181618167.png#pic_center)

# 工具

![img](https://img-blog.csdnimg.cn/20200814182610984.png#pic_center)
![img](https://img-blog.csdnimg.cn/20200814182844270.png#pic_center)
![img](https://img-blog.csdnimg.cn/20200814183026157.png#pic_center)
![img](https://img-blog.csdnimg.cn/20200814183043230.png#pic_center)
![img](https://img-blog.csdnimg.cn/20200814183133531.png#pic_center)
![img](https://img-blog.csdnimg.cn/20200814183156232.png#pic_center)
![img](https://img-blog.csdnimg.cn/20200814183208852.png#pic_center)
![img](https://img-blog.csdnimg.cn/20200814183222502.png#pic_center)
![img](https://img-blog.csdnimg.cn/20200814183235632.png#pic_center)

## 备注

因为没有进行实际操作，确实不太懂，所以就只截了部分图，日后实践了再重新听一遍课。