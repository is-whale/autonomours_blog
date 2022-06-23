- [Ubuntu 18.04安装Apollo 6.0：从零开始到启动Demo（超多细节）_shao918516的博客-CSDN博客_ubuntu安装apollo](https://blog.csdn.net/shao918516/article/details/119223577)

最近在以Apollo平台为模板学习无人驾驶系统，在安装Apollo时遇到一些小问题，故写一篇文章作总结，初步介绍Apollo平台，并详解安装过程。前两章是文字介绍，急于安装的同学可以直接从第三章开始。

# 版本说明：

本文操作系统选择Ubuntu 18.04，[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020)版本为6.0，说明如下：

1. 现在Ubuntu长期支持版本已更新到20.04，但由于太新的缘故，很多第三方软件都不支持，包括Apollo，笔者年初更新到20.04后引起诸多不便，血泪教训无奈重装退回到18.04，18.04也是目前各主流软件支持的版本，兼容性也最好，同时也比16.04的官方支持更长，也是Apollo 6.0推荐的版本，所以强烈建议各位安装Ubuntu 18.04，非必要请不要升级到20.04。
2. Apollo各时期版本路线图如下：
3. ![在这里插入图片描述](https://img-blog.csdnimg.cn/8009e727c65645d6865d091d68eb8e11.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoYW85MTg1MTY=,size_16,color_FFFFFF,t_70#pic_center)
   我们选择最新的2020年9月发布的Apollo 6.0。根据官方说法，Apollo 6.0亮点主要有5点：
   (1). Apollo 6.0在算法模块上，引入了三个新的基于深度学习的模型。
   a. 在感知上，Apollo 6.0 实现了基于PointPillars的激光点云障碍物识别模型；
   b. 在预测上，Apollo 6.0 发布了基于语义地图的低速行人预测模型；
   c. 在规划上，Apollo 6.0 首次引入了基于语义地图的模仿学习
   (2). 集成了无人驾驶的相关内容；
   (3). 将主要工具、依赖库都升级到了最新版本，包括将python升级到3.6版本；
   (4). 对5.0发布的云服务（（Apollo数据流水线服务，搭配Apollo教育和开发者套件））也进行了全面升级；
   (5). 对v2x车路协同方案做了重大升级，首发对象级别的车端感知与路侧感知融合。
   **注意**：在作者看来，Apollo 6.0版本是具有里程意义的版本，包括加入深度学习算法、依赖库升级、云服务升级以及v2x车路协同升级等，标志着Apollo真正走向了成熟。更多详情请参考[《Apollo 6.0发布！无人化自动驾驶迎来新篇章》](https://mp.weixin.qq.com/s/-7gY6SYbLNy-mhpR9dJtIQ)。

版本选择暂时讨论到此，下面让我们来认识下阿波罗6吧。

# 1. Apollo入门介绍

本章分两节讲解Apollo系统，第一节对Apollo系统及其组成进行简介，第二节讲解各组成部分的运行流程，作为Apollo系统的入门。

## 1.1 Apollo系统简介

Apollo系统是百度一套完整的自动驾驶技术方案，它尝试串联整个无人驾驶的工作流程，提出了包括系统底层、硬件使用、数据学习方面的综合解决方案。Apollo系统架构如下图所示，绿色代表6.0版重点升级模块：![在这里插入图片描述](https://img-blog.csdnimg.cn/155833ba387647e297be0941e65cf602.webp?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoYW85MTg1MTY=,size_16,color_FFFFFF,t_70#pic_center)
按照上图，apollo自动驾驶分成四层技术栈，从下到上分别为：

1. **车辆认证平台**（Open Vehicle Certificate Platform）：自动驾驶最终都要落地到车上，因此apollo抽象了一个”车辆认证平台”层，通过电子化的方式控制车辆的行驶行为，即线控车辆，同时开放车辆接口标准。

2. **硬件开发平台**（Hardware Development Platform）：这一层为自动驾驶汽车提供计算、感知、交互的硬件能力，包括计算单元(车载处理器设备)、GPS/IMU(惯性测量设备)、摄像头、激光雷达、毫米波雷达、超声波雷达、HMI(人机接口)设备、黑盒子（数据记录）、ASU（Apollo Sensor Unit）、AXU（Apollo Extension Unit）、V2X车载单元等。

3. 开源软件平台

   （Open Software Platform）：这一层是百度Apollo开放的核心部分，从图中看到，这一层还可以分为三个子层，从下至上分别是：

   - kernel层：RTOS。这一层是运行于硬件上面的OS，对于自动驾驶这种实时性要求特别强的领域，这里显然只能是RTOS（实时操作系统）。Apollo 1.0开放的源码中包含一个”Apollo Kernel“的项目，在这个项目下汇集着可以满足实时性需求的OS kernel。当然目前还仅有一个选择：realtime linux kernel。这是apollo基于Linux Kernel 4.4.32+realtime patch定制的一款专用linux内核。
   - platform层：Cyber RT。在Kernel层的上面就是 apollo 的 runtime framework了，提供 platform 级的支撑。Apollo 1.0同样也创建了一个专用项目：apollo-platform，用于汇集满足apollo平台级支撑需求的platform。当前该项目下也仅提供了一种选择：Apollo ROS，即Cyber RT，是基于ROS1的Indigo版二次开发后的定制版ROS。Cyber RT基于自动驾驶需求出发，对ROS1主要做了三方面改进：为优化自动驾驶大量使用传感器引发很大的传输带宽需求， Cyber RT改变基于socket的网络传输模式，大量采用共享内存的node间通信机制，减少传输中的数据拷贝，显著提升传输效率, 尤其是在满足一对多的传输场景下效果明显；从鲁棒性出发，使用RTPS(Real-Time Publish Subscribe)服务发现协议实现完全的P2P网络拓扑，避免原ROS的以Master作为拓扑网络的中心的单点故障问题；使用protobuf替代原ROSmessage，提供很好的向后兼容，避免接口升级后，不同版本的模块难以兼容的问题。其实第二点改进也是ROS2正在做的事情。
   - modules层。在这一层是apollo的功能modules，当前似乎依旧是基于ROS的package开发的，在http://github.com/ApolloAuto/apollo/modules/common/apollo_app.cc你大致能看出来一个ROS Package的开发模板。这一层提供诸如：地图引擎、高精定位、感知(perception）、预测(prediction)、规划(planning)、控制（control）、人机交互及V2X适配器等诸多功能。

4. **云服务平台**（Cloud Services Platform）：Apollo还开放了云端数据平台，以及唤醒万物的DuerOS能力。DuerOS也是Baidu人工智能战略的重要棋子，似乎也是目前Baidu在AI方面最为成熟的、应用最广的产品，当然这一层还包括仿真、高精度地图、量产服务组建、安全、OTA、V2X道路救援（Roadside Service）等服务。

介绍完Apollo的组成，下面我们看看它们之间是如何运行工作的。

## 1.2 Apollo系统运行流程

Apollo系统运行流程如下图所示：![在这里插入图片描述](https://img-blog.csdnimg.cn/79682612cf574490b2f4028b190b303f.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoYW85MTg1MTY=,size_16,color_FFFFFF,t_70#pic_center)
感知模块从相机、雷达、IMU等传感器获得数据并处理，将处理结果结合高清地图数据传给预测模块，用于预测障碍物的未来运动轨迹。根据预测结果和由高清地图产生的定位信息，规划模块制定时空轨迹，并将轨迹路线交给控制模块执行。监控模块监控所有硬件和软件信息，并通过HMI（人机接口）展示给司机，并将特殊情况传递给守护模块，由守护模块作出紧急相应。控制模块、守护模块通过CANBus传输控制指令到硬件，同时也接受来自硬件的反馈信息。下面我们对上图中Apollo系统的个组成部分进行详细介绍：

**1. Perception（感知）**：感知模块用于识别自动驾驶车辆周围的环境，包括两个重要子模块：障碍物检测和交通灯检测。感知依赖LiDAR点云数据和相机原始数据，用于障碍物检测，此外交通灯检测需要依赖实时定位以及HD-Map。由于实时ad-hoc（点对点）交通灯检测在计算上是不可行的，因此交通灯检测需要依赖实时定位，以便确定何时何地通过相机捕获的图像检测交通灯。

**2. Prediction（预测）**：预测模块用于预测所有感知障碍物的未来运动轨迹，它封装了感知信息，代码如下：

```cpp
//set perception obstacle callback function
AdapterManager::AddPerceptionObstaclesCallback(&Prediction::RunOnce, this)
//set localization callback function
AdapterManager::AddLocalizationCallback(&Prediction::OnLocalization, this)
```

当预测模块接受到定位更新时，更新车辆内部状态；当感知模块发布障碍物信息时，触发预测模块实际执行。

**3.HD-Map**：即高精地图，该模块类似一个库，更多的作为查询引擎，提供有关道路的临时结构化信息。

**4. Localization（定位）**：定位模块利用GPS、LiDAR和IMU等各种信息来估计车辆本身的位置，目前有两种类型的定位模式：计时器回调函数OnTimer计算和多传感器融合。
OnTimer是基于RTK的定位方法，代码如下：

```cpp
timer_ = AdapterManager::CreateTimer(cyber::Duration(dutation), $RTKLocalization::OnTimer， this)
```

多传感器融合（MSF）方法注册了一些事件触发的回调函数，代码如下：

```cpp
void RTKLocalization::ImuCallback(const std::shared_ptr<localization::CorrectedImu> &imu_msg){
    std::uniqure_lock<std::mutex> lock(imu_list_mutex_);
    if (imu_list_.size() < imu_list_max_size_) {
        imu_list_.pop_front();
    }
    imu_list_.push_back(*imu_msg);
    return;
}
```

**5. Planning（规划）**：规划模块规划自动驾驶车辆所采取的时空轨迹。Apollo需要使用多个信息源来规划安全无碰撞的行使轨迹，因此规划模块会与几乎所有其它模块进行交互。首先，规划模块获得预测模块的输出和订阅交通灯检测输出；然后，规划模块从路由模块获取路由输出；最后，规划模块还需要知道定位信息以及当前的车辆信息，从而规划出详细的实际行使路线。

**6. Control（控制）**：控制模块通过生成控制命令（如加速、刹车或转向）来执行规划模块提供的时空轨迹。控制模块以规划轨迹作为输入，并生成控制命令传递给CanBus。Apollo控制模块主要考虑了车辆的运动学模型和动力学模块，并提供了车辆控制算法PID（Proportion Integral Differential，比例积分微分）和MPC（Model Predictive Control，模型预测控制）。PID车辆控制算法集中在LatController和LonController对象中，而MPC车辆控制算法集中在MPCController对象中。

**7. CANBus**：CAN总线是传递控制命令到车辆硬件的接口，同时也将硬件信息传递给软件系统。CANBus有两个数据接口，相关回调函数如下：

```cpp
AdapterManager::AddControlCommandCallback(&CanBus::OnControlCommand, this)
```

第一个数据接口是基于计时器的发布者，回调函数为OnTimer，启用后会定期发布底盘信息，将硬件信息传给软件系统。第二个数据接口是一个基于事件的发布者，回调函数为OnControlCommand，当CANBus模块收到控制命令会触发该函数，将控制命令发送到硬件系统。

**8. HMI**：Apollo平台的人机接口是一个从监测模块接收数据来查看自动驾驶车辆状态的模块，同时也可用来测试其它模块和控制车辆。Apollo的HMI是DreamView，它是一个Web应用程序，功能包括：（1）可视化自动驾驶模块的输出，例如规划轨迹、汽车定位、底盘状态等；（2）为用户提供人机交互界面，以查看硬件状态、打开/关闭模块以及启停自动驾驶车辆；（3）提供调试工具，比如PNC Monitor，以有效跟踪模块问题。

**9. Monitor（监控模块）**：Monitor是车辆中包括硬件在内的所有模块的监控系统，监控模块从其它模块接受数据并传递给HMI，以便司机查看并确保所有模块都正常工作。如果模块或硬件发生故障，监控就会向Guardian（新操作中心模块）发送警报，然后由Guardian模块决定采取哪些操作来防止系统崩溃。

**10. Guardian（守护模块）**：Guardian模块根据Monitor发送的数据作出相应决定，它有两个状态：一是当所有模块都正常工作，此时Guardian模块允许控制模块正常工作，控制信号将被正常发送到CANBus，Guardian模块就像不存在一样；二是当Monitor模块监测到模块崩溃，Guardian模块将阻止控制信号到达CANBus并使汽车停止。
Guardian会判定3种停车方式并依赖最终的Gatekeeper：（1）如果超声波传感器运行正常且未监测到障碍物，Guardian就会使汽车缓慢停止；（2）如果超声波传感器没有响应或异常，Guardian就会采取硬制动，使汽车马上停止；（3）还有一种情况，如果HMI通知驾驶员即将发生碰撞且驾驶员在10秒内没有干预，Guardian就会采用硬制动使汽车立即停止。

大致了解Apollo的架构和各功能模块后，我们从官方文档开始具体学习Apollo系统。

# 2. Apollo索引文档介绍

在介绍具体安装之前，先介绍下Apollo的帮助文档。官方文档时未加工的第一手信息，对于学习理解Apollo系统非常重要，而且Apollo文档写的较为详细，有时间可以多研究，没时间读博主的博客就够了，而且博主的文章对官方文档遗漏错误的地方进行过更正。[官方文档总体目录](https://github.com/ApolloAuto/apollo/tree/master/docs)如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/1f6ed01feb224498af608e18be3031e1.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoYW85MTg1MTY=,size_16,color_FFFFFF,t_70#pic_center)
各个文档介绍如下（其中以"_cn"结尾文档是其对应的中文文档，请根据需要选择）：
**1. quickstart**：quidkstart文件夹主要介绍如何安装apollo的软件和硬件。Apollo 6.0用到的文档主要有以下三个：

- apollo_6_0_quick_start_cn.md：名字可以看出，从它快速开始。
- apollo_software_installation_guide.md：软件安装指导。
- apollo_3_5_hardware_system_installation_guide.md(Since there are no changes in our Hardware setup, please refer to the Apollo 3.5 guide)：硬件安装指导。

**2. technical_tutorial**：技术说明文档，详细说明了文档架构和各种技术的说明文档、编码规范、车辆适配、版本编译等。主要文档如下：

- apollo_5.5_technical_tutorial.md：Apollo 5.5版技术说明文档，详细说明了文档架构和各种技术的说明文档，同样适用于Apollo 6.0。
- apollo_best_coding_practice.md：Apollo 编码最佳实践，简短讲了Apollo的编码规范。
- apollo_vehicle_adaption_tutorial_cn.md：Apollo车辆适配教程，本文所介绍的Apollo新车适配方法，可以帮助开发者快速接入新车辆，以方便开发者在Apollo上进行软件开发。
- intro_to_apollo_release_build.md：Apollo版本编译简介。

**3. technical_documents**：某技术具体细节的解释说明文档，包括混合引擎、开放区域轨迹分区及优化、泊车场景、规划组件、路径决策、速度限制、预测障碍等相关的算法，不一而论。

**4. cyber**：该文件夹下主要存放关于Cyber RT计算框架的相关知识文档，关于Cyber RT的总索引文档目录为：apollo/cyber/README.md。Cyber RT：Apollo Cyber RT是百度自研的无人车计算任务实时并行计算框架，框架核心理念基于组件，通过组件实现有预先设定的“输入”、“输出”。实际上，在框架中，每个组件代表一个专用的算法模块。可以暂且理解为百度研发的升级版ROS。关于Cyber RT及组件的学习，请参考[《Apollo Cyber RT学习日记 》](https://blog.csdn.net/qq_40254086/article/details/104356515?spm=1001.2014.3001.5501)。各个文件采用官方说明如下：

- CyberRT_Docker.md：How to Develop Cyber RT inside Docker Environment on Both x86 and ARM Platform. Official docker image for Cyber RT development, which is easiest way to build and play with Cyber RT. On top of that, we officially support development of Cyber RT on both x86 and ARM platform.
- CyberRT_Quick_Start.md: Apollo Cyber RT Quick Start. Everything you need to know about how to start developing your first application module on top of Apollo Cyber RT.
- CyberRT_Developer_Tools.md: Apollo Cyber RT Developer Tools. Detailed guidance on how to use the developer tools from Apollo Cyber RT.
- CyberRT_API_for_Developers.md: Apollo Cyber RT API for Developers. A comprehensive guide to explore all the APIs of Apollo Cyber RT, with many concrete examples in source code.
- CyberRT_FAQs.md: Apollo Cyber RT FAQs. Answers to the most frequently asked questions about Apollo Cyber RT.
- CyberRT_Terms.md: Apollo Cyber RT Terms. Commonly used terminologies in Cyber RT documentation and code.
- CyberRT_Python_API.md: Apollo Cyber RT Python Wrapper. Develop projects in Python.
- CyberRT_Scheduler_cn.md: schedule stratagies.

**5. specs**: A Deep dive into Apollo’s Hardware and Software specifications (only recommended for expert level developers that have successfully installed and launched Apollo)。specs存放Apollo软硬件开发的详细说明，包括软件层架构、编译和测试说明、lidar/imu标定、安全更新用户指南、bazel说明、bridge header协议、Planning模块架构和概述、Apollo坐标系统、Drealand介绍、dreamview使用表、感知模块介绍、软件预安装说明（Ubuntu/GPU Driver/Docker/NVIDIA Container Toolkit）等。

**6. howto**: Brief technical solutions to common problems that developers face during the installation and use of the Apollo platform. howto文件夹下主要存放一些特定问题的解决方法，解决动手做的问题，可以查看README.md进行索引查找。

**7. demo_guide**: 运行线下演示。如果你没有车辆及车载硬件， Apollo还提供了一个计算机模拟环境，可用于演示和代码调试。线下演示首先要Fork并且Clone Apollo在GitHub的代码，然后需要设置docker的release环境，请参照 howto文件夹中的docker安装文档。

**8. Apollo_Fuel**: Apollo传感器融合相关文档。

**9. D-kit**：Apolo开发工具包。

**10. FAQs**: Commonly asked questions about Apollo’s setup and modules.

准备工作终于铺垫完了，下面进入搭建开发环境环节。

# 3. Apollo开发环境搭建

本章开始介绍本文重点：Apollo开发环境搭建，共分为四部分：1. 环境预安装；2. 下载源码；3.拉取Docker镜像并编译；4.启动demo。下面开始一一介绍。

## 3.1 环境预安装

首先，需要准备安装环境，请参考文档specs/prerequisite_software_installation_guide.md。下面分别介绍Ubuntu Linux、NVIDIA GPU Driver、Docker Engine和NVIDIA Container Toolkit的安装。

### 3.1.1 安装Ubuntu Linux

我们的机器一般安装的是Windows，这里建议大家不要使用虚拟机，而是在同一台电脑上安装Windows和Ubuntu Linux双系统，这样两个系统互不干扰，出了问题可单独解决，即使严重到要重装时，也互不影响。安装教程请参考[《Windows10安装ubuntu18.04双系统教程》](https://www.cnblogs.com/masbay/p/11627727.html)。
安装Ubuntu需要注意的点：

1. 安装语言建议选择English(US)。博主在安装显卡驱动时，由于内核自动升级导致显卡驱动不匹配，进不了桌面，不得已只能在纯命令行模式下操作，而此时中文文件夹都会变成乱码，没办法访问，文件内资料也没办法备份，最后只备份了英文文档后选择重装，所以鉴于大家都是英语高高手的事实，还是选择英文版，容错率高一些。
2. 硬盘划分。由于学习自动驾驶一般要安装几个大的软件，如Apollo、ros、opencv等，所以建议对于/root划分60G到100G，其他的分给/home。
3. 装完系统后第一件事就是下载源更换为国内源，建议阿里源或清华源，鉴于Apollo 6.0高达14G多的下载量，你懂我的意思吧。更换教程请参考[《Ubuntu 更换国内源》](https://blog.csdn.net/qq_35451572/article/details/79516563)。
4. 系统进行第一次更新（命令sudo apt-get upgrade）后，在software & updates中，将更新设置为Never，需要的更新手动操作即可，否则不知道哪一天你就因为版本问题而疯掉，如下图：![在这里插入图片描述](https://img-blog.csdnimg.cn/526d0621033847d2baf62c3750cb45ce.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoYW85MTg1MTY=,size_16,color_FFFFFF,t_70#pic_center)
5. 安装搜狗输入法，请参考[《Ubuntu18.04下安装搜狗输入法》](https://blog.csdn.net/lupengCSDN/article/details/80279177)，版本可以选择2.4。Ubuntu输入中文还是搜狗最好用。
6. 对于N卡用户，上述安装完成后，需重启按进入BIOS设置，使更新生效。同时我们找到Security->secure boot这个启动选项，然后按enter键，选择disable，按F10保存退出后重启电脑。原因是如果系统启动的Secure Boot激活， 那么ubuntu18.04的kernel在启动的时候，要通过密码验证的这种方式加载kernel module，而N家的这个驱动并不是通过这种方式加载到内核中，所以我们无法check驱动了，NVIDIA相关命令也会失效，最简单粗暴的方式就是在开机的启动项里面disable secure boot这个功能。当未禁用Secure Boot时，输入NVIDIA-SMI命令，错误提示如下：

```bash
NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running.
```

### 3.1.2 安装NVIDIA GPU Driver

对于N卡用户，需要单独安装对应显卡驱动及cuda，安装之前，需要根据Ubuntu的内核版本来确定对应版本的显卡驱动。查看命令如下：

```bash
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

```bash
shaw@p1:~$ sudo apt-add-repository multiverse
shaw@p1:~$ sudo apt-get update
shaw@p1:~$ sudo apt-get install nvidia-driver-470
...
update-initramfs: Generating /boot/initrd.img-5.4.0-81-generic
I: The initramfs will attempt to resume from /dev/nvme1n1p2
I: (UUID=3d8c08e6-e615-4e5d-94ef-ca7744ce78c1)
I: Set the RESUME variable to override this.
```

如果没有禁用Secure Boot，此时会出现输入Ubuntu Boot密码的提示，如下图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/cb0ef1b41f034b1ba10abcea409ae9c1.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoYW85MTg1MTY=,size_16,color_FFFFFF,t_70#pic_center)
此时输入也无妨，不过要拿小本本记下来，以免以后忘记。不过内核还是无法加载N卡驱动，所以一定记得禁用Secure Boot（驱动安装后再禁用也可以）。当驱动版本不对，或没禁用Secure Boot时，执行dev_start.sh报错如下：

```bash
docker error response from daemon: oci runtime create failed...
: exit status 1, stdout:, stderr: nvidia-container-cli: initialization error: nvml error: driver not loaded: unknow...
```

此时重新按照此节重新安装显卡驱动即可。安装完成后，可以通过命令NVIDIA_SMI测试，出现下面提示说明安装成功：

```bash
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

### 3.1.3 安装Docker Engine

Apollo 6.0需要Docker 19.03及以上版本，此版本的Docker安装和之前版本不一样，更简单易操作，然而Apollo的官网文档并没有更新，大家按照以下命令操作即可：

```bash
shaw@p1:~$ docker [tab]

Command 'docker' not found, but can be installed with:

sudo snap install docker     # version 19.03.13, or
sudo apt  install docker.io

See 'snap info docker' for additional versions.

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

安装完成后，创建组docker，并将当前用户加入组中，后续就可以以用户身份操作docker而不是root，这一步至关重要，也是官方文档没有的，命令如下：

```bash
shaw@p1:~$ sudo groupadd docker
shaw@p1:~$ sudo usermod -aG docker $USER
```

此时重新登录系统（重启，这一步一定要做，否则会警告找不到用户而uid和gid又不匹配，造成错误）。完成添加用户到docker组的操作，以后就可以以用户身份操作Docker。
最后使用hello-world测试docker：

```bash
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

此时可以继续为docker添加启动项。此步看个人需要，经常使用docker的话可以执行此步骤：

```bash
shaw@p1:~$ sudo systemctl enable docker.service
shaw@p1:~$ sudo systemctl enable containerd.service
```

**注意**：Docker的更新也比较频繁，经常会出现新版本安装不成功又禁用旧版本服务的情况，此时别慌，仅需以下几个命令即可解决：

```bash
shaw@p1:~$ service docker start 
Failed to start docker.service: Unit docker.service is masked.
shaw@p1:~$ systemctl unmask docker.service
shaw@p1:~$ systemctl unmask docker.socket
shaw@p1:~$ systemctl start docker.service
shaw@p1:~$ service docker start 
docker run hello-world
```

另外，强烈建议非必要不要升级Docker，因为升级会造成系统Docker和镜像Docker的驱动nvidia-docker2版本不匹配，在进行启动Apollo镜像的操作dev_start.sh时报错。此时莫慌，只要完全卸载Docker，重新安装docker.io和nvidia-docker2，退回到原来版本即可。如需安装新版本，请彻底卸载Docker和nvidia-docker2后，重新安装即可。

### 3.1.4 安装NVIDIA Container Toolkit

安装NVIDIA Container Toolkit，可理解为Docker容器中的Apollo镜像安装NVIDIA驱动，以使Apollo镜像可以使用外部显卡的cuda进行人工智能学习。此步产生错误较少，按命令执行即可：

```bash
shaw@p1:~/code/apollo$ sudo apt install curl
shaw@p1:~/code/apollo$ distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
shaw@p1:~/code/apollo$ curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
shaw@p1:~/code/apollo$ sudo apt -y update
shaw@p1:~/code/apollo$ sudo apt install -y nvidia-docker2 
...
Unpacking nvidia-docker2 (2.6.0-1) ...
Setting up libnvidia-container1:amd64 (1.4.0-1) ...
Setting up libnvidia-container-tools (1.4.0-1) ...
Setting up nvidia-container-toolkit (1.5.1-1) ...
Setting up nvidia-container-runtime (3.5.0-1) ...
Setting up nvidia-docker2 (2.6.0-1) ...
Processing triggers for libc-bin (2.27-3ubuntu1.4) …
```

## 3.2 下载Apollo源码

源码下载，作者总结了三种：gitee、https连接github和ssh连接github。官方推荐gitee，说gitee代码更新比github快一些，当然下载速度也更快，大家可根据自己网络情况选择，作者选择的是ssh。此过程等待时间较长，如果超时重新执行命令即可。

### 3.2.1 三种下载方式

（1）使用gitee的https下载命令如下：

```bash
shaw@p1:~$ mkdir code && cd code
shaw@p1:~/code/ git clone https://gitee.com/ApolloAuto/apollo.git
Cloning into 'apollo'...
remote: Enumerating objects: 2623, done.
remote: Counting objects: 100% (2623/2623), done.
remote: Compressing objects: 100% (1497/1497), done.
...
```

（2）使用github的https连接命令如下：

```bash
shaw@p1:~$ git clone https://github.com/ApolloAuto/apollo.git
Cloning into 'apollo'...
remote: Enumerating objects: 313441, done.
remote: Counting objects: 100% (132/132), done.
remote: Compressing objects: 100% (79/79), done.
...
```

（3）使用github的ssh连接下载。在开始之前，需要设置自己的ssh-rsa秘钥。设置步骤如下：首先在本机生成ssh-rsa秘钥：

```bash
shaw@p1:~/code$ ssh-keygen -t rsa -C "$GITHUB_ACCOUNT"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/shaw/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/shaw/.ssh/id_rsa.
Your public key has been saved in /home/shaw/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:cm/ugEy8BsOTtHJBQg+JVTGxRPQ8AyrHO3+tHMq8YjE shao918516
The key's randomart image is:
+---[RSA 2048]----+
| +==@o           |
|...B *           |
|. + = =          |
| o + = o         |
|  + O + S        |
|  E= * * .       |
|   o. B o o      |
|  oo = o +       |
| . .=.o  .o      |
+----[SHA256]-----+

shaw@p1:~/code$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDZFbqOJ65nSZzPiWMy+Moipa40HzHsdnxs8qSqDwJkPhbelki/RlfyTZQLZYySqn1CaHeBAQgI4urjzNaUbjCC0C0dqzkaDQjDy/3pjm1Op0yV7xtBcUFp5vMYe9JV2tCFJgH6UNKBf9q5WK/2CCHQcxgEdftIAuk0TUviDPYRJEgFyJf/6oyA7Yzo1wbKB7GJuRAqR811ZJWs1vWMJTEV+yiXfhCqUoeY0hteF25Mp+qxIWmyLESUT1DIHmiU2ymjCfzsxTmMrcy2pSyyP84gwjHa2uMA4rEXHciDroGJotME/VByWF3pd2qw/UZHOsG/j/zDm3kBQAJNEmQFxAQR $GITHUB_ACCOUNT

shaw@p1:~/code$ ssh-agent bash
shaw@p1:~/code$ ssh-add ~/.ssh/id_rsa
Enter passphrase for /home/shaw/.ssh/id_rsa: ##（设为空即可）
Identity added: /home/shaw/.ssh/id_rsa (/home/shaw/.ssh/id_rsa)
...
```

复制~/.ssh/id_rsa.pub中以ssh-rsa开头、Github账户结尾的部分，登录github网页，点击个人头像进入设置界面，点击Account settings->SSH and GPG keys->SSH keys->New SSH key，将刚才内容粘贴后保存，如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/a9219be98c5b4238a898d960030f30cf.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAc2hhbzkxODUxNg==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
最后，使用github的ssh连接下载，命令如下：

```bash
shaw@p1:~/code$ git clone git@github.com:ApolloAuto/apollo.git
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

### 3.2.2 设置origin分支

如果我们只是下载运行，上述操作就够了。如果我们作为开发者想提交代码修改请求，还需要修改orgin分支为个人项目，upstream分支指向原作者项目地址，使用git修改操作如下：

```bash
## the initinal setup
shaw@p1:~/code$ cd apollo/
# Checkout the "master" branch
shaw@p1:~/code/apollo$ git checkout master
Already on 'master'
Your branch is up to date with 'origin/master'.
shaw@p1:~/code/apollo$ git remote -v
origin	git@github.com:ApolloAuto/apollo.git (fetch)
origin	git@github.com:ApolloAuto/apollo.git (push)

## Reset origin to your GitHub fork
shaw@p1:~/code/apollo$ git remote set-url origin git@github.com:YOUR_GITHUB_USERNAME/apollo.git
## Set Apollo GitHub repo as upstream
# Using SSH
shaw@p1:~/code/apollo$ git remote add upstream git@github.com:ApolloAuto/apollo.git
# Using HTTPS
shaw@p1:~/code/apollo$ git remote add upstream https://github.com/ApolloAuto/apollo.git

## You can confirm that the origin/upstream has been setup correctly by running:
shaw@p1:~/code/apollo$ git remote -v
origin  git@github.com:YOUR_GITHUB_USERNAME/apollo.git (fetch)
origin  git@github.com:YOUR_GITHUB_USERNAME/apollo.git (push)
upstream        git@github.com:ApolloAuto/apollo.git (fetch)
upstream        git@github.com:ApolloAuto/apollo.git (push)
```

## 3.3 拉取Apollo镜像并编译

### 3.3.1 拉取Apollo镜像

Ubuntu Linux系统环境准备好之后，就可以打开Apollo开发docker容器，拉取Apollo镜像，可参考官方文档：quickstart/apollo_software_installation_guide.md。
设置并进入APOLLO_HOME目录（也可以使用官方默认的APOLLO_ROOT_DIR），开始拉取Apollo镜像，此过程需要耐心等待，超时失败再次执行命令即可，命令如下：

```bash
shaw@p1:~/code/apollo$ echo "export APOLLO_HOME=$(pwd)" >> ~/.bashrc && source ~/.bashrc
shaw@p1:~/code/apollo$ echo $APOLLO_HOME
/home/shaw/code/apollo          
shaw@p1:~/code/apollo$ bash docker/scripts/dev_start.sh -C
...
[ OK ] Congratulations! You have successfully finished setting up Apollo Dev Environment.
[ OK ] To login into the newly created apollo_dev_shaw container, please run the following command:
[ OK ]   bash docker/scripts/dev_into.sh
[ OK ] Enjoy!
```

提示：启动Docker时，-C选项表示从国内服务器下载镜像，但有时会出现下载镜像失败的情形，如遇到该问题，可将-C选项去掉，直接从美国服务器下载镜像。因为我们在安装时已经设置了国内源，所以可以去掉-C，没设置国内源的可以试试这个选项。

### 3.3.2 从源代码编译Apollo

编译和测试请参考官方文档：specs/apollo_build_and_test_explained.md。Apollo使用bazel作为内嵌的编译工具，bazel是一款适用于中大型项目的高性能、可阅读的编译和测试工具，对Bazel的掌握有助于Apollo的编译，bazel基础使用可参考[《Bazel应用方法》](https://blog.csdn.net/qq_40254086/article/details/104479878)。当然不会Bazel也没关系，看懂作者的博客就可以了。
进入新创建的Docker容器apollo_dev_$USERNAME，编译Apollo源代码，执行命令如下：

```bash
# 进入docker容器
shaw@p1:~/code/apollo$ bash docker/scripts/dev_into.sh 
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
# 查看编译选项，可根据提示选择适合自己的编译指令
[shaw@in-dev-docker:/apollo]$ ./apollo.sh --help
...
Usage:
    ./apollo.sh [OPTION]

Options:
    config [options]: config bazel build environment either non-interactively (default) or interactively.
    build [module]: run build for cyber (<module> = cyber) or modules/<module>.  If <module> unspecified, build all.
    build_dbg [module]: run debug build.
    build_opt [module]: run optimized build.
    build_cpu [module]: build in CPU mode. Equivalent to 'bazel build --config=cpu'
    build_gpu [module]: run build in GPU mode. Equivalent to 'bazel build --config=gpu'
    build_opt_gpu [module]: optimized build in GPU mode. Equivalent to 'bazel build --config=opt --config=gpu'
...
```

我们选择带有GPU的最优化编译（无独显的小伙伴请去掉gpu）：

```bash
# 第二次编译时请执行clean
[shaw@in-dev-docker:/apollo]$ ./apollo.sh clean
[shaw@in-dev-docker:/apollo]$ ./apollo.sh build_opt_gpu
...                                                                  ^
(05:51:55) INFO: Elapsed time: 2320.935s, Critical Path: 198.71s
(05:51:55) INFO: 14542 processes: 2878 internal, 11664 local.
(05:51:55) INFO: Build completed successfully, 14542 total actions
==============================================
[ OK ] Done building apollo. Enjoy!
==============================================
```

**注意**：

1. 编译过程会等待一个多小时，在编译过程中会出现大量的警告信息，它会覆盖掉之前的安装信息，需要保存安装日志的要提前做一下日志备份。在此不得不吐槽某度的编程严谨性，博主刚参加工作时在华某公司，要求编译时绝对不允许出现警告级别的消息，否则会给代码留下安全隐患，必须去掉后才允许提交代码。所以希望百度改进这一点儿吧，这么多警告看的真的好烦。
2. 编译过程中，刚开始会出现黄色的几行ESD-CAN警告，如下：

```bash
[WARNING] ESD CAN library supplied by ESD Electronics doesn't exist.
[WARNING] If you need ESD CAN, please refer to:
[WARNING]   third_party/can_card_library/esd_can/README.md
```

如果没有CAN卡或是EMUC-CAN卡，请直接忽略；若CAN卡是ESD-CAN卡时，往下看。
首先，按照apollo/third_party/can_card_library/esd_can/README.md进行CAN驱动安装。然后从安装包中拷贝头文件和库文件到指定目录。最后，检查apollo/apollo.sh脚本中的函数check_esd_files()，这里看到1.0.0版本会检查3个文件：libntcan.so、libntcan.so.4、libntcan.so.4.0.1，所以需要建立软连接。具体操作如下：

```bash
1. After/when you purchase CAN card from ESD Electronics, please contact support@esd.eu to obtain the supporting software package for Linux.
  The CAN card to use with the IPC is ESD CAN-PCIe/402. For more information about the CAN-PCIe/402, see the [ESD CAN-PCIe/402 Product Page](https://esd.eu/en/products/can-pcie402)

2. After unpacking the software package, please find and copy the following files to the specific sub-directories (under this directory):
  * Copy ntcan.h to include/
  * Copy 64-bit libntcan.so.4.0.1 to lib/
  * Do the following to add the necessary symbolic links:

        cd ./lib/;
        ln -s libntcan.so.4.0.1 libntcan.so.4;
        ln -s libntcan.so.4.0.1 libntcan.so.4.0
```

## 3.4 启动demo

运行Dreamview命令./scripts/bootstrap.sh start，在Chrome或Firefox浏览器中打开网址http://localhost:8888/，可以查看初始化界面。这里有两种方法可以启动demo，一种是执行已保存的recorder文件，另一种是选择系统加载的默认路由。

### 3.4.1 执行recorder文件

在DreamView界面的对应下拉框中，选择驾驶模式为“Mkz Standard Debug”，选择车型为“Lincoln2017MKZLGSVL”，选择地图为“Sunnyvale with Two Offices”。执行操作如下：

```bash
[shaw@in-dev-docker:/apollo]$ ./scripts/bootstrap.sh start
Dreamview is running at http://localhost:8888
[shaw@in-dev-docker:/apollo]$ cyber_recorder play -f docs/demo_guide/demo_3.5.record -l
file: docs/demo_guide/demo_3.5.record, chunk_number: 3, begin_time: 1546888377338834894 (2019-01-08-03:12:57), end_time: 1546888422886740928 (2019-01-08-03:13:42), message_number: 61615
earliest_begin_time: 1546888377338834894, latest_end_time: 1546888422886740928, total_msg_num: 61615

Please wait 3 second(s) for loading...
Hit Ctrl+C to stop, Space to pause, or 's' to step.

[RUNNING] Record Time: 1546888400.807    Progress: 23.468 / 45.548
```

运行界面如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/d937b8181982436b98cad116e26acb95.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_Q1NETiBAc2hhbzkxODUxNg==,size_59,color_FFFFFF,t_70,g_se,x_16#pic_center)

### 3.4.2 执行规划路径

再次实验规划路径地图。按Ctrl+C停止界面，重新启动Dreaview：./scripts/bootstrap.sh restart；在Dreamview中打开Sim Control选项，并在右上侧选择“Sunnyvale Big Loop”地图；在Dreamview中切换左侧标签至“Module Controller”页，开启“Planning”与“Routing”模块；在Dreamview中切换左侧标签至“Default Routing”页，选择“Route: Reverse Early Change Lane”，若看到道路输出车辆规划轨迹，并且车辆向前行驶，表明Apollo 6项目构建运行成功。如下图所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/6e5386e3e2d04d50b1fc1f68be3b0a7c.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAc2hhbzkxODUxNg==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

1. **注意**：
   当运行demo时，可能会提示找不到命令cyber_recorder，此时只需要让cyber设置脚本cyber/setup.bash生效即可，命令如下：

```bash
[shaw@in-dev-docker:/apollo]$ echo $PATH
/opt/apollo/sysroot/bin:/usr/local/nvidia/bin:/usr/local/cuda/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
[shaw@in-dev-docker:/apollo]$ source cyber/setup.bash 
[shaw@in-dev-docker:/apollo]$ echo $PATH
/apollo/bazel-bin/modules/tools/visualizer:/apollo/bazel-bin/cyber/tools/cyber_launch:/apollo/bazel-bin/cyber/tools/cyber_service:/apollo/bazel-bin/cyber/tools/cyber_node:/apollo/bazel-bin/cyber/tools/cyber_channel:/apollo/bazel-bin/cyber/tools/cyber_monitor:/apollo/bazel-bin/cyber/tools/cyber_recorder:/apollo/bazel-bin/cyber/mainboard:/usr/local/cuda/bin:/opt/apollo/sysroot/bin:/usr/local/nvidia/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

有的教程让安装针对大文件使用Git进行管理的git-lfs、gpg秘钥、支持Apollo图像操作的GLFW（Graphics Library Framework）等，其中git-lfs在2019年5月17日更新中，因存在Bug，被Apollo项目停用Git LFS服务，对于其他两个，作者没安装也没影响到最后的运行。博主只会将安装最小化同时，将用到的讲到最细，读者可根据需要自行安装。

有问题的同学欢迎留言讨论。

最后，停止container。运行以下命令，可以停止所有docker

```bash
[shaw@in-dev-docker:/apollo]$ exit
shaw@p1:~/code/apollo$ docker stop $(docker ps -aq)
```