- [Apollo进阶课程⑭ | Apollo自动定位技术——三维几何变换和坐标系介绍_自动驾驶小学生的博客-CSDN博客_apollo坐标转换](https://blog.csdn.net/cg129054036/article/details/88805950)

本节主要介绍自定位的一些基础知识，主要包括三维几何变换和几个坐标系。

根据各个轴位置关系的不同，空间中的坐标系可以分为左手坐标系和右手坐标系。如下图所示。

![img](https://img-blog.csdnimg.cn/2019032520595335.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                                    **空间坐标系**

## 1.三维几何变换---旋转

旋转是指在一个坐标系下，把一个点旋转到另一个位置的几何变换。

![img](https://img-blog.csdnimg.cn/20190325211831161.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                                    **二维旋转**

如上所示：将P旋转θ角到P'的位置,在二维坐标系下，由一个2×2的矩阵表示，如图中的R(θ)。点的旋转其实也可以理解为两个坐标系的一个旋转。对于三维而言，多了一个维度，可以用3×3矩阵来表示。

分解来看，可以通过X轴Y轴Z轴分别去旋转，对应的分量分别是RX，RY和RZ。三维旋转矩阵可以由对应三个方向的基本旋转矩阵相乘来表示。三维旋转矩阵存在九个元素，对九个元素进行优化是非常复杂。通常情况下会使用是欧拉角或者四元数的方式进行优化，如下图所示。

![img](https://img-blog.csdnimg.cn/2019032521191665.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                                    **三维旋转**

------

## 2.三维几何变换----平移

平移是当前点相对于坐标原点的位置变化，通常由当前位置的坐标表示，由一个3x1的向量表示，如下所示。

![img](https://img-blog.csdnimg.cn/20190325212024951.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                                    三维平移

### 2.1刚体的位置和朝向

![img](https://img-blog.csdnimg.cn/20190325212131620.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                                    **刚体的位置和朝向**

刚体是一种有限尺寸，可以忽略形变的固体。自然界不存在完美的刚体，但物体通常可以假定为完美刚体。自动驾驶中汽车可以认为是一个刚体。

刚体坐标系通常是取刚体内的一点P为原点建立的局部坐标系。刚体的位置可以看作刚体原点P相对于参考坐标系原点O所做的平移，可以使用三维平移向量表示。刚体的朝向可以由刚体原点P的朝向来表示。

------

## 3. 坐标系

下面介绍一下定位里面常用的坐标系。

### 3.1 ECI地心惯性坐标系

ECI地心惯性坐标系，也称i系，这个坐标系如下图所示：

![img](https://img-blog.csdnimg.cn/20190325212302987.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                                    **地心惯性坐标系ECI**

它圆心就是地球的原点，Z轴沿地轴方向朝向北极， X轴和Y轴位于赤道平面内，与Z轴满足右手法则，并且X轴和Y轴分别指向两个恒星。也就是说不随着地球的自转而发生变化。它是一个非常固定的坐标系。IMU测量得到的加速度，角速度都是相对于这个坐标系的。

------

### 3.2 ECFF地心地固坐标系

ECEF地心地固坐标系也称为e系，它的原点也是地球的原点， Z轴指向北极， 它与前面的ECI的区别就在于它的XY随着地球的自转而转动，它是以地球为基准的。它的X轴指向格林威治的子午面的交线， Y轴在赤道平面内与X轴、Z轴满足右手系法则，常用的如WGS84坐标系。其特点是与地球固定在一起，随地球一起转动。

![img](https://img-blog.csdnimg.cn/20190325212404971.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                                    **地心地固坐标系ECEF** 

------

### 3.3当地水平坐标系

当地水平坐标系，一般也称为L系。如下图所示，它的原点位于载体所在的地球表面，X轴和Y轴在当地水平面内，分别指向东向和北向，Z轴垂直向上，与X轴和Y轴满足右手法则。在导航解算过程中通常也把该坐标系选取为导航坐标系（n系），也称为“东-北-天（E-N-U）”坐标系。

![img](https://img-blog.csdnimg.cn/20190325212525555.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                                    **当地水平坐标系**

------

### **3.4 UTM坐标系**

UTM(UNIVERSAL TRANSVERSE MERCARTOR GRIDSYSTEM，通用横轴墨卡托格网系统)坐标是一种平面直角坐标，这种坐标格网系统及其所依据的投影已经广泛用于地形图。其投影是一种“等角横轴割圆柱投影”，椭圆柱割地球与南纬80度，北纬84度两条等高圈，投影后两条相割的经线上没有变形，而中央经线上长度比为0.9996。该投影方法按经度把地球划分成了60个区域，那么每6度一个区域，例如北京其实在第50区。南北又做了划分，相当于把地球划分成很多块。

![img](https://img-blog.csdnimg.cn/20190325212619356.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                                    **UTM投影**

### 

------

### 3.5 车体坐标系

车体坐标系原点在载体质量中心与载体固连处（相对于车载，选取原点位于后轴中心位置），X轴沿载体轴向指向右，Y轴指向前，Z轴与X轴和Y轴满足右手法则指向天向。该坐标系通常称为“右-前-上（R-F-U）”坐标系。它是一个局部坐标系，它与N系或者导航坐标系、当地水平坐标系之间的旋转关系表示现在车的姿态。

![img](https://img-blog.csdnimg.cn/20190325212718861.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                                    **车体坐标系**

------

### 3.6IMU坐标系

IMU坐标系其实和车体坐标系基本上是一样，它的原点在陀螺仪和加速度计的坐标原点，XYZ三个轴方向分别与陀螺仪和加速度计对应的轴向平行。在不考虑安装误差角的情况下，载体坐标系和IMU坐标系是一样的。

![img](https://img-blog.csdnimg.cn/20190325212816358.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                                    **IMU坐标系特点**

------

### 3.7 相机坐标系

相机坐标系以相机光心为原点，Xc轴和Yc轴与成像平面坐标系的x轴和y轴平行，Zc轴为摄像机的光轴，和图像平面垂直。由点O与Xc、Yc、Zc轴组成的坐标系成为相机的坐标系，如下图右边所示。通常情况下并不会将相机坐标系和其他的全局坐标系直接连接起来，因为已经选IMU作为汽车原点。通过一个外参，把相机和IMU关联起来，也就是标定的外部参数。当知道IMU在世界坐标系的姿态和位置就可以得到相机坐标系到世界坐标系的转换。

![img](https://img-blog.csdnimg.cn/2019032521290779.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                                    **相机坐标系**

------

### 3.8 激光雷达坐标系

下图为激光雷达坐标系以及其俯视图，从图中可以看出激光雷达坐标系的坐标原点位于多线束中点旋转轴的交点处，Z轴沿轴线向上，X和Y轴如俯视图所示。其测量的点坐标是在激光雷达坐标系下的三维坐标。

要转换到世界坐标系，可以使用相机坐标系下点转换到世界坐标系的类似方法。通过IMU坐标系到激光雷达坐标系的外參，以及IMU坐标系的姿态，得到激光雷达坐标系到世界坐标系的转换。

![img](https://img-blog.csdnimg.cn/20190325212959531.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                                ***\*激光雷达坐标系及其俯视图\****

------

### 3.9 无人车定位信息中涉及的坐标系

下图给出了定位模块中输出的各个坐标系下的信息以及不同坐标系之间的关系。

![img](https://img-blog.csdnimg.cn/20190325213056752.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

​                                            **        \**无人车定位信息中涉及的坐标系\****

定位输出信息主要包括UTM坐标系的XY,IMU的姿态四元数， ENU坐标系下的速度。另外还输出一些和车体相关信息，例如车体姿态的四元数，车体坐标系下的加速度和角速度。这些输出的信息可以给感知和PNC使用。各个坐标系之间是一些基本的旋转关系。