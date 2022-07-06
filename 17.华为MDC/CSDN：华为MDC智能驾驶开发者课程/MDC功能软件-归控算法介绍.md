- [MDC功能软件-归控算法介绍_林北不要忍了的博客-CSDN博客](https://blog.csdn.net/weixin_43849505/article/details/118333137)

## 一、归控模块开发依赖

在介绍这一部分之前，首先要搞明白什么是归控模块。归控，展开就是规划控制，其中规划是根据目前的已知信息，决定自己要进行哪一个操作，而控制指的是依据自己的规划，做出相应的动作。举个例子，人站在变绿的红绿灯前面，人看见了灯的变化，这属于感知，看见后大脑作出反应，决定要过马路，这属于规划，人决定过马路于是要迈步往前，迈步向前则属于控制。

而对于MDC计算平台而言，规划控制主要还是依赖于平台的四大组成部分：硬件平台、软件平台、工具链以及安全控制。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210629132313131.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

## 二、归控模块开发总流程

与前面的感知融合模块类似，大体的步骤都是先编写ARXML文件，之后导入MDS生成代码，编写程序后导入到MDC平台上，最后运行得到返回结果。区别在于归控的开发，编码时有一种备选的方式，是将代码剥离，即不采用实车的检验，而是在虚拟环境下模拟，这种情况是要将AP框架和ROS框架剥离，具体的会在下面介绍。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021062913384592.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

## 三、规划模块应用层框架样例

这一部分简单举例了两个应用层的框架，首先是一个比较基本的寻迹版本，这个版本是按照给定的一些点进行规划，是一个比较基础的版本。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210629134323135.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
在此之上，还有一个进阶版本称为全功能版本，这个版本不再是固定的点，而是将寻迹版本中的点换为高精度地图和全局导航信息。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210629134448863.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

## 四、归控实例节点开发

这一部分则演示了一个归控节点的开发，ARXML文件的配置就不再重复了，依然是配置数据类型、通信协议等内容。将配置好的ARXML文件导入MDS并生成代码，完成代码的编写后，就进入算法的验证阶段，这里主要记录一下算法验证。

一般为了归控模块开发的高效性以及安全性，都会在实车调试之前进行一个多场景下的仿真测试，测试算法的运行是否正确。现在大多数的仿真测试平台都是X86架构的，而MDC是arm架构的，所以采用这种剥离的方式，测试代码而不区分框架。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210629135224607.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
上图所示的就是一个验证的基本流程，在SIL仿真中，通过转换节点得到ROS的信息，之后将ROS的数据转换为C++的标准数据格式，这样就可以进行单纯的算法的验证，验证之后再换为ROS数据继续下一步。

## 五、归控方案介绍

目前的轨迹规划主要有四种主流的技术路线：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210629135822664.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
下面主要介绍一下第一种方式。第一种方式主要是两部分：BP和MOP。
其中BP指的是行为决策，即根据时间、空间范围的安全性、效率性、舒适性、利他性等因素，进行行为的状态的转移，简单来说就是决定采取怎样的行为。展开来说BP首先需要世界认知，即感知周围的环境，之后进行预处理，根据预处理的结果决定顶层状态迁移，而顶层状态的迁移可以拆分为许多的基元状态，实现这些小的基元状态就称为基元行为，基元行为分为横向的和纵向的。简单来说顶层行为指的是大一些的行为，比如说超车，实现超车这个行为需要很多小的步骤，按照科三的要求要看后视镜、打灯三秒、变道加速再变回来，而这些具体的、细化的步骤就是基元行为。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210629141123153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
行为决策有很多的可选的方法，核心算法主要是下面几种：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210629141219279.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

而MOP指的是轨迹规划，即根据不同的行为，确定路点的序列。展开来说预处理之后，根据横向和纵向区分为轨迹生成和速度规划，其实从这里就更好理解所谓的横向和纵向，按照汽车行驶方向建立坐标系，向着行驶方向前进为纵向，垂直行驶方向为横向，沿着行驶方向上可以进行的行为不就是加减速、刹车（特斯拉出来挨打）、跟车等操作，而垂直行驶方向上能采取的动作就是变道等操作了。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210629142014294.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)