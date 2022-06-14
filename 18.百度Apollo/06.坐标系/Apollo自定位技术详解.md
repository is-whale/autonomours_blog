- [三、Apollo自定位技术详解（入门技术与基础知识）_一只小麻团的博客-CSDN博客_apollo定位](https://blog.csdn.net/mabingyao/article/details/104812167)

### 1.技术入门

##### 无人车自定位系统

相对一个坐标系，确定无人车的位置和姿态。
位置：3个自由度，x、y、z
姿态：3个自由度，三个方向的旋转：横滚（roll）、俯仰（pitch）、航向（yaw）

自动驾驶汽车定位系统指标要求

| 项目   | 指标     | 理想值 |
| ------ | -------- | ------ |
| 精度   | 误差均值 | <10cm  |
| 鲁棒性 | 最大误差 | <30cm  |
| 场景   | 覆盖场景 | 全天候 |

##### 为什么无人车需要精确定位系统？

与自动驾驶地图配合提供静态环境感知
定位系统提供速度、角速度、加速度等信息，用于路线规划和车辆控制

##### 定位方法

- 基于电子信号定位：GNSS
  航迹推算：IMU
  环境特征匹配：LiDAR、camera

1. 卫星实时动态差分技术包括位置差分和距离差分
   距离差分又包括伪距差分、载波相位差分
   (1) 伪距差分：利用多个卫星发射信号到基站、接收站，可以得到一组有误差的距离值，差分消除误差，再传递给移动站，精度：米级
   (2) 载波相位差分：全球、全天候、全天时；高精度；实时；精度：cm级，小于5cm
   (3)缺点：
   （i）需要基站传递误差，基站布置成本高
   （ii）强依赖可视卫星数
   （iii）易受电磁环境干扰
   （iv）GNSS信号遮挡引起多径效应
2. 环境特征匹配
   激光定位：实时激光点云与预先建立的地图匹配
   视觉定位：视觉车道线地图与在线车道线检测匹配
3. 惯性导航
   (1) IMU包括加速度计（瞬时加速度）+陀螺仪（瞬时角速度），需要剔除重力加速度
   (2) 消费级IMU：精度低、价格便宜；光纤IMU：精度高、价格昂贵
   (3) 工作过程：
   （i）通过角速度积分得到姿态增量，与前一个姿态加和得到当前姿态
   （ii）加速度积分得到速度
   （iii）二者结合可以得到位置信息
   (4) 优点：六自由度信息；短时精度高；输出频率高200HZ以上、无延迟；无外部依赖输入
   (5) 缺点：误差随时间累积
4. 多传感器融合定位（GNSS，激光定位，视觉定位，惯性导航）
   ![多传感器融合定位](https://img-blog.csdnimg.cn/20200312152516740.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21hYmluZ3lhbw==,size_16,color_FFFFFF,t_70)
   核心模块KF：状态误差的卡尔曼滤波器，接收惯性导航的递推作为时间更新，保证滤波器能够有高频输出。
   接收GPS、激光点云定位和视觉定位，得到位置和姿态的更新，做低频的状态的更新。

### 2.相关基础知识

#### 2.1 三维几何变换

坐标系：根据各个轴位置关系的不同，空间中的坐标系分为左手坐标系和右手坐标系（常用）

- 二维旋转：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200312104453882.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21hYmluZ3lhbw==,size_6,color_FFFFFF,t_70)
- 三维旋转：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200312105932463.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21hYmluZ3lhbw==,size_6,color_FFFFFF,t_70)
  存在的问题：旋转矩阵内的参数远大于旋转的自由度
  优化：欧拉角、四元数
- 三维平移
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200312110910325.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21hYmluZ3lhbw==,size_16,color_FFFFFF,t_70)

#### 2.2 刚体的位置与朝向（认为车是一个刚体）

（1）刚体坐标系：为确定刚体的位置和朝向，需要在刚体内取一点P为原点建立刚体坐标系
（2）刚体位置和朝向：刚体的位置和朝向可以看作刚体原点P相对参考坐标系原点O所做的平移，可以使用三维平移向量表示

#### 2.3 常用的坐标系：

| 坐标系                        | 简述                                                         | 特点                                                         | 图示                                                         | 例子                                                         |
| ----------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 地心惯性坐标系—i系（ECI）     | 原点：地球原点；Z轴：沿地轴方向指向北极；X轴、Y轴：位于赤道平面内，与Z轴满足右手法则，并且分别指向两个恒星 | X、Y轴指向两颗恒星；不随地球自转而转动；可以作为地球附近传感器输出的惯性坐标系 | ![i系](https://img-blog.csdnimg.cn/20200312153121830.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21hYmluZ3lhbw==,size_16,color_FFFFFF,t_70)红色坐标系 | 在研究载体在地球表面附近的运动时，近似看作关性坐标系，如IMU  |
| 地心地固坐标系—e系（ECEF）    | 原点：地球原点；Z轴：沿地轴方向指向北极；X轴：赤道平面与格林威治子午面的交线上；Y轴：在赤道平面，与X轴、Z轴满足右手法则 | 与地球固定在一起，随地球旋转；假如起始时刻i系与e系重合，二者旋转关系只与时间相关。旋转图示角度，i系与e系重合；地球表面的任意一点可以使用确定的坐标系（x，y，z）表示 | ![e系](https://img-blog.csdnimg.cn/20200312153202523.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21hYmluZ3lhbw==,size_16,color_FFFFFF,t_70)绿色坐标系 | 常用的如WGS84坐标系系统                                      |
| 当地水平坐标系—l系            | 原点：载体所在的地球表面；X、Y轴在当地水平面内分别指向东和北，Z轴垂直向上，与X、Y轴满足右手法则 | 与地球固连在一起，随地球转动；与e系的旋转关系只与载体所在精度、纬度有关 | ![l系](https://img-blog.csdnimg.cn/20200312153250105.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21hYmluZ3lhbw==,size_16,color_FFFFFF,t_70)蓝色坐标系 | 机器人领域的世界坐标系(w系)；导航坐标系(n系)；“东-北-天(E-N-U)”坐标系 |
| 通用横轴墨卡托投影（UTM投影） | “等角横轴割圆投影” ：椭圆柱割地球于南纬80度、北纬84度两条等高圈，投影后两条相割的经线上没有变形，而中央经线上长度比0.9996。按经度分为60个带，没带6度，从西经180度算起 | 坐标（x，y）加投影带号才能唯一表示地球表面一点；坐标与经纬度或者ECEF坐标系有确定的转换关系 | ![UTM](https://img-blog.csdnimg.cn/20200312153344673.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21hYmluZ3lhbw==,size_16,color_FFFFFF,t_70) | 平时定位所输出的坐标系                                       |
| 车体坐标系—b系                | 原点：载体质量中心与载体固连，车体选取原点在后轴中心位置；X轴：沿载体轴向指向右；Y轴：指向前；Z轴：与X、Y轴满足右手法则指向天。（R-F-U右前上坐标系） | 与载体固连在一起，随载体一起转动；与n系的旋转关系可以表征载体的当前姿态信息 | ![车体坐标系](https://img-blog.csdnimg.cn/2020031215341347.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21hYmluZ3lhbw==,size_16,color_FFFFFF,t_70) | /                                                            |
| IMU坐标系                     | 原点：陀螺仪和加速计的坐标原点；X、Y、Z三轴方向分别与陀螺仪和加速计的对于轴向平行 | 与载体固连在一起，不考虑安装偏角，与载体坐标系重合；与n系的旋转关系可以表征载体的当前姿态信息 | ![IMU坐标系](https://img-blog.csdnimg.cn/20200312153514809.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21hYmluZ3lhbw==,size_16,color_FFFFFF,t_70) | /                                                            |
| 相机坐标系                    | 图中O点为摄像机光心（投影中心），Xc轴和Yc轴与成像平面坐标系的x轴和y轴平行；Zc轴为摄像机的光轴，和图像平面垂直 | 通常IMU坐标系的原点在世界坐标系的位置是已知的，通过IMU坐标系到相机坐标系的外参，以及IMU坐标系的姿态，可以得到相机坐标系到世界坐标系的转换 | ![相机坐标系](https://img-blog.csdnimg.cn/20200312153550149.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21hYmluZ3lhbw==,size_16,color_FFFFFF,t_70) | /                                                            |
| 激光雷达坐标系                | 原点：多线束中心旋转轴的交点处，z轴沿轴线向上；X、Y轴如俯视图所示 | 通过IMU坐标系到激光雷达坐标系的外参，以及IMU坐标系的姿态，可以得到激光雷达坐标系到世界坐标系的转换 | ![激光雷达坐标系](https://img-blog.csdnimg.cn/20200312153624566.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21hYmluZ3lhbw==,size_16,color_FFFFFF,t_70) | /                                                            |

无人车定位信息中涉及的坐标系
![无人车定位信息设计的坐标系](https://img-blog.csdnimg.cn/2020031215375982.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21hYmluZ3lhbw==,size_16,color_FFFFFF,t_70)