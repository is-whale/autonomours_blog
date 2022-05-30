- [无人驾驶算法学习（六）：多传感器融合MSF算法_su扬帆启航的博客-CSDN博客_msf算法](https://blog.csdn.net/orange_littlegirl/article/details/89889453)

# 1.引言

本文的多传感器融合是建立在读懂《Quaternion kinematics for the error-state Kalman ﬁlter》基础上的 ，是一种相机和IMU融合的理论，里面讲解了IMU的误差状态运动方程构建。误差状态四元数，是有开源的程序的，但是它是集成在[rtslam](https://github.com/damarquezg/rtslam)里面的，不方便提取出来使用。
但还有另外一个开源的程序，ETH的[MSF](https://github.com/ethz-asl/ethzasl_msf)，可以比较方便地用在自己的工程里面，并且它的理论与误差状态四元数很接近，稍微有点不同，所以MSF开源程序就成了一个不错的选择。
所以本人研究了ETH的两篇文章：《Vision Based Navigation for Micro Helicopters》和《A Robust and Modular Multi-Sensor Fusion Approach Applied to MAV Navigation》
其核心就是[扩展卡尔曼滤波EKF](https://blog.csdn.net/orange_littlegirl/article/details/89059588)，本人把MSF的核心代码阅读了一遍，推导了论文中的算法，并最后做了实验。

# 2. 算法理论

MSF关键点：

- 我们使用IMU含噪声的测量值和bias去计算估计状态量X。并且没有考虑测量值的噪声项和扰动，从而产生了误差。我们把所有不确定性引来的误差放在误差变量δx中考虑，并推导了误差状态运动模型，以此求解Fd状态转移矩阵和P过程协方差矩阵
- 利用其它来源的数据如GPS,视觉等来作为矫正（使误差可观）。通过估计状态量X计算Hj，利用观测量和估计量计算残差，最后计算K，来更新P和误差变量δx，最后更新状态量X。

## 2.1 MSF基本模型

基本模型如下图所示。MSF的可扩展性很好，程序里可以接入新的传感器，比如GPS，激光雷达，码盘。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506191126137.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)下图是msf的原理示意图：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506191449238.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)
下图是eskf中所有变量解释表
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506191616114.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

## 2.2 预测

状态表示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019050619170089.png)
理想运动模型：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506191720571.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)
实际运动模型：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506191743503.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)
误差状态表示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506191838616.png)
误差运动状态模型：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019050619190691.png)
**注意：在eskf中有详细推导和解释**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506192134605.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506192143477.png)

误差状态导数的转移矩阵：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506192208527.png)
二阶泰勒展开（忽略二阶以上微小量）：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506192232824.png)
误差状态的转移矩阵：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506192259333.png)
预测误差状态和预测误差差协方差矩阵：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019050619232831.png)

## 2.3 测量与更新

p测量模型：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506192452162.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

q测量模型：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506192627468.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

测量更新：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506192646918.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

# 3. 核心代码分析

核心算法的代码分析如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506192812682.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)
预测与更新算法调用关系图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190506193312312.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

# 4. 代码实战

[msf-ethsal编译及使用](http://www.liuxiao.org/2016/07/ros-多传感器卡尔曼融合框架-ethzasl-msf-framework-编译与使用/)
[相机IMU融合四部曲（三）:MSF详细解读与使用](https://www.cnblogs.com/ilekoaiq/p/9311357.html)
*基本操作*

```
roslaunch msf_updates position_pose_sensor.launch 
roslaunch msf_updates viconpos_sensor.launch
rosrun rqt_reconfigure rqt_reconfigure#初始化卡尔曼滤波器,很重要!!
如下界面,需要点击filter
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019070915171485.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)