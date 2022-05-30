- [无人驾驶算法学习（二）：卡尔曼滤波器Kalman Filter_su扬帆启航的博客-CSDN博客](https://blog.csdn.net/orange_littlegirl/article/details/88982606)

# 1. 引言

在完成传感器数据的解析和传感器信息的坐标转换后，我们就会获得某一时刻，自车坐标系下的各种传感器数据，这些数据包括障碍物的位置、速度；车道线的曲线方程、车道线的类型和有效长度；自车的GPS坐标等等。这些信号的组合，表示了无人车当前时刻的环境信息。

由于传感器本身的特性，任何测量结果都是有误差的。以障碍物检测为例，如果直接使用传感器的测量结果，在车辆颠簸时，可能会造成障碍物测量结果的突变，这对无人车的感知来说是不可接受的。因此需要在传感器测量结果的基础上，进行跟踪，以此来保证障碍物的位置、速度等信息不会发生突变。

最经典的跟踪算法莫过于[卡尔曼](https://so.csdn.net/so/search?q=卡尔曼&spm=1001.2101.3001.7020)在1960年提出的卡尔曼滤波器。在无人车领域，卡尔曼滤波器除了应用于障碍物跟踪外，也在车道线跟踪、障碍物预测以及定位等领域大展身手。

# 2. 基本数学理论

在介绍[卡尔曼滤波器](https://so.csdn.net/so/search?q=卡尔曼滤波器&spm=1001.2101.3001.7020)数学原理之前，先从感性上看一下它的工作原理。简单来讲，卡尔曼滤波器就是根据上一时刻的状态，预测当前时刻的状态，将预测的状态与当前时刻的测量值进行加权，加权后的结果才认为是当前的实际状态，而不是仅仅听信当前的测量值。一般的数学理论看书就行,本文结合实际物理学应用分析卡尔曼滤波的原理。

## 2.1 运动学建模

假设有个小车在道路上向右侧匀速运动，我们在左侧安装了一个测量小车距离和速度传感器，传感器每1秒测一次小车的位置s和速度v，如下图所示。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190402214053289.png)
我们用向量xt来表示当前小车的状态，该向量也是最终的输出结果，被称作状态向量（state vector）：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190402213815575.png)
由于测量误差的存在，传感器无法直接获取小车位置的真值，只能获取在真值附近的一个近似值，可以假设测量值在真值附近服从高斯分布。如下图所示，测量值分布在红色区域的左侧或右侧，真值则在红色区域的波峰处。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190402214702783.png)
由于是第一次测量，没有小车的历史信息，我们认为小车在1秒时的状态x与测量值z相等，表示如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190402214317110.png)
公式中的1表示第1秒。

## 2.2 预测状态量

预测是卡尔曼滤波器中很重要的一步，这一步相当于使用历史信息对未来的位置进行推测。根据第1秒小车的位置和速度，我们可以推测第2秒时，小车所在的位置应该如下图所示。会发现，图中红色区域的范围变大了，这是因为预测时加入了速度估计的噪声，是一个放大不确定性的过程。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190402213801571.png)
根据小车第一秒的状态进行预测，得到预测的状态xpre：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190402214803840.png)
其中，pre是prediction的简称；时间间隔为1秒，所以预测位置为距离+速度*1；由于小车做的是匀速运动，因此速度保持不变。

## 2.3 观测量

在第2秒时，传感器对小车的位置做了一次观测，我们认为小车在第2秒时观测值为z2，用向量表示第2秒时的观测结果为：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190402214915794.png)
很显然，第二次观测的结果也是存在误差的，我们将预测的小车位置与实际观测到的小车位置放到一个图上，即可看到：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190402213856646.png)
图中红色区域为预测的小车位置，蓝色区域为第2秒的观测结果。

很显然，这两个结果都在真值附近。为了得到尽可能接近真值的结果，我们将这两个区域的结果进行加权，取加权后的值作为第二秒的状态向量。

## 2.4 最终的输出

为了方便理解，可以将第2秒的状态向量写成：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190402215033146.png)
其中，w1为预测结果的权值，w2为观测结果的权值。两个权值的计算是根据预测结果和观测结果的不确定性来的，这个不确定性就是高斯分布中的方差的大小，方差越大，波形分布越广，不确定性越高，这样一来给的权值就会越低。
加权后的状态向量的分布，可以用下图中绿色区域表示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190402213938120.png)
你会发现绿色区域的方差比红色区域和蓝色区域的小。这是因为进行加权运算时，需要将两个高斯分布进行乘法运算，得到的新的高斯分布的方差比两个做乘法的高斯分布都小。两个不那么确定的分布，最终得到了一个相对确定的分布，这是卡尔曼滤波的一直被推崇的原因。

第1秒的初始化以及第2秒的预测、观测，实现卡尔曼滤波的一个周期。同样的，我们根据第2秒的状态向量做第3秒的预测，再与第3秒的观测结果进行加权，就得到了第3秒的状态向量；再根据第3秒的状态向量做第4秒的预测，再与第4秒的观测结果进行加权，就得到了第4秒的状态向量。以此往复，就实现了一个真正意义上的卡尔曼滤波器。

用一张图表示以上过程:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190402215556589.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

# 3. 利用卡尔曼滤波器完成对车速预测

前文用了一个简答的例子对卡尔曼滤波器的整个流程进行了介绍，下面我们根据卡尔曼滤波器的原理，编写代码，跟据激光雷达的测量值和状态预测方程进行卡尔曼滤波。

## 3.1 完整数学原理解析

在这里就要祭出卡尔曼老先生给我们留下的宝贵财富了，下面7个公式就是卡尔曼滤波器的理性描述，使用下面7个公式，就能够实现一个完整的卡尔曼滤波器。现在看不懂这7个公式没关系，继续往下看，我会一个一个做解释。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190402220155770.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

- 预测状态：
  x ′ = F x + u x^{&#x27;}=Fx+u*x*′=*F**x*+*u*
  根据本时刻的状态:x x*x*,即位置和速度,利用状态转移矩阵F F*F*,再加上外部的影响u,预测下一时刻的状态x ′ x^{&#x27;}*x*′
- 预测误差的协方差矩阵:
  P ′ = F P F T + Q P^{&#x27;}=FPF^T+Q*P*′=*F**P**F**T*+*Q*
  状态向量x x*x*中的两个变量p（位置）和v（速度）存在相关性，用误差协方差矩阵P P*P*表示。外部无法检测到的干扰，比如风的干扰，也会增加不确定性。把这些没有被跟踪的干扰当作协方差为Q Q*Q*的噪声来处理。Q Q*Q*是过程噪音协方差矩阵，它会增加不确定性。
- 测量和预测值之差:
  y = z − H x ′ y=z-Hx^{&#x27;}*y*=*z*−*H**x*′
  z z*z*是测量向量，表示传感器测量到的数据（比如传感器测量到的汽车的位置）。测量值和预测值（更新后的）之间存在误差y y*y*
  如果状态向量和测量向量包含的数据不同，比如状态向量x x*x*包括位置和速度，而测量向量只有位置信息,需要引入变换矩阵H = [ 1 , 0 ] H=[1,0]*H*=[1,0]
- 测量的噪声协方差矩阵:
  R R*R*
  测量的传感器存在噪声导致测量结果存在噪声协方差,由于此处只有位置信息,所有,R R*R*为位置的方差
- 计算卡尔曼增益的中间变量:
  S = R + H P ′ H T S=R+HP^{&#x27;}H^T*S*=*R*+*H**P*′*H**T*
- 卡尔曼增益:
  K = P ′ H T S − 1 K=P^{&#x27;}H^TS^{-1}*K*=*P*′*H**T**S*−1
- 更新状态矩阵:
  x = x ′ + K y x=x^{&#x27;}+Ky*x*=*x*′+*K**y*
- 更新预测误差协方差矩阵:
  P = ( I − K H ) P ′ P=(I-KH)P^{&#x27;}*P*=(*I*−*K**H*)*P*′
- 各个变量关系如下:

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190405220258778.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

## 3.2 编程实现

- 实战分析

1.用python实现,行驶数据分为两类，一类是真实数据，另一类是传感器测量到的数据。
真实数据表现汽车的运动模型,它是通过函数generate_data()来实现的。为了突出重点，我对函数进行了简化，比如删除了单位换算，一律使用国际单位制；简化了运动模型。
测量数据需要考虑传感器的误差，假设误差呈高斯分布，那么，可以在真实数据的基础上加上一个高斯分布的样本，样本的期望为0，标准差为0.15，利用generate_lidar()来实现。
2.创建Matrix类并且利用numpy实现多种矩阵的功能，比如矩阵乘法：ndarray.dot()；矩阵的迹：ndarray.trace()；转置矩阵：ndarray.T；计算逆矩阵的方法：Matrix.inverse()；创建单位矩阵：numpy.eye…为卡尔曼滤波的矩阵运算打基础
3.利用卡尔曼滤波器对状态量和误差协方差矩阵进行更新

```c++
#!/usr/bin/python
# -*- coding: UTF-8 -*-
# 生成汽车行驶的真实数据

# 汽车从以初速度v0，加速度a行驶10秒钟，然后匀速行驶20秒
# x0:initial distance, m
# v0:initial velocity, m/s
# a:acceleration，m/s^2
# t1:加速行驶时间，s
# t2:匀速行驶时间，s
# dt:interval time, s

import numbers
import numpy as np
import matplotlib.pyplot as plt  
import math

def generate_data(x0, v0, a, t1, t2, dt):
    a_current = a
    v_current = v0
    t_current = 0

    # 记录汽车运行的真实状态
    a_list = []
    v_list = []
    t_list = []

    # 汽车运行的两个阶段
    # 第一阶段：加速行驶
    while t_current <= t1:
        # 记录汽车运行的真实状态
        a_list.append(a_current)
        v_list.append(v_current)
        t_list.append(t_current)
        # 汽车行驶的运动模型
        v_current += a * dt
        t_current += dt

    # 第二阶段：匀速行驶
    a_current = 0
    while t2 > t_current >= t1:
        # 记录汽车运行的真实状态
        a_list.append(a_current)
        v_list.append(v_current)
        t_list.append(t_current)
        # 汽车行驶的运动模型
        t_current += dt

    # 计算汽车行驶的真实距离
    x = x0
    x_list = [x0]
    for i in range(len(t_list) - 1):
        tdelta = t_list[i+1] - t_list[i]
        x = x + v_list[i] * tdelta + 0.5 * a_list[i] * tdelta**2
        x_list.append(x)
    return t_list, x_list, v_list, a_list

# 生成雷达获得的数据。需要考虑误差，误差呈现高斯分布
def generate_lidar(x_list, standard_deviation):
    return x_list + np.random.normal(0, standard_deviation, len(x_list))

# 获取汽车行驶的真实状态
t_list, x_list, v_list, a_list = generate_data(100, 5, 4, 10, 20, 0.1)

# 创建激光雷达的测量数据
# 测量误差的标准差。为了方便观测，可以增加该值。
standard_deviation = 0.15
# 雷达测量得到的距离
lidar_x_list = generate_lidar(x_list, standard_deviation)
#　雷达测量的时间
lidar_t_list = t_list

# 可视化.创建包含2*3个子图的视图
fig, ((ax1, ax2, ax3), (ax4, ax5, ax6)) = plt.subplots(2, 3, figsize=(20, 15))

# 真实距离
ax1.set_title("truth distance")
ax1.set_xlabel("time")
ax1.set_ylabel("distance")
ax1.set_xlim([0, 21])
ax1.set_ylim([0, 1000])
ax1.plot(t_list, x_list)

# 真实速度
ax2.set_title("truth velocity")
ax2.set_xlabel("time")
ax2.set_ylabel("velocity")
ax2.set_xlim([0, 21])
ax2.set_ylim([0, 50])
ax2.set_xticks(range(21))
ax2.set_yticks(range(0, 50, 5))
ax2.plot(t_list, v_list)

# 真实加速度
ax3.set_title("truth acceleration")
ax3.set_xlabel("time")
ax3.set_ylabel("acceleration")
ax3.set_xlim([0, 21])
ax3.set_ylim([0, 5])
ax3.plot(t_list, a_list)

# 激光雷达测量结果
ax4.set_title("Lidar measurements VS truth")
ax4.set_xlabel("time")
ax4.set_ylabel("distance")
ax4.set_xlim([0, 21])
ax4.set_ylim([0, 1000])
ax4.set_xticks(range(21))
ax4.set_yticks(range(0, 1000, 100))
ax4.plot(t_list, x_list, label="truth distance")
ax4.scatter(lidar_t_list, lidar_x_list, label="Lidar distance", color="red", marker="o", s=2)
ax4.legend()
# plt.show()##观查数据


# 使用卡尔曼滤波器
# 初始距离。注意：这里假设初始距离为0，因为无法测量初始距离。
initial_distance = 0

# 初始速度。注意：这里假设初始速度为0，因为无法测量初始速度。
initial_velocity = 0

# 卡尔曼滤波器需要调用的矩阵类
class Matrix(object):
    # 构造矩阵
    def __init__(self, grid):
        self.g = np.array(grid)
        self.h = len(grid)
        self.w = len(grid[0])

    # 单位矩阵
    @staticmethod
    def identity( n):
        return Matrix(np.eye(n))

    # 矩阵的迹
    def trace(self):
        if not self.is_square():
            raise(ValueError, "Cannot calculate the trace of a non-square matrix.")
        else:
            return self.g.trace()
    # 逆矩阵
    def inverse(self):
        if not self.is_square():
            raise(ValueError, "Non-square Matrix does not have an inverse.")
        if self.h > 2:
            raise(NotImplementedError, "inversion not implemented for matrices larger than 2x2.")
        if self.h == 1:
            m = Matrix([[1/self[0][0]]])
            return m
        if self.h == 2:
            try:
                m = Matrix(np.matrix(self.g).I)
                return m
            except np.linalg.linalg.LinAlgError as e:
                print("Determinant shouldn't be zero.", e)

    # 转置矩阵
    def T(self):
        T = self.g.T
        return Matrix(T)

    # 判断矩阵是否为方阵
    def is_square(self):
        return self.h == self.w

    # 通过[]访问
    def __getitem__(self,idx):
        return self.g[idx]

    # 打印矩阵的元素
    def __repr__(self):
        s = ""
        for row in self.g:
            s += " ".join(["{} ".format(x) for x in row])
            s += "\n"
        return s

    # 加法
    def __add__(self,other):
        if self.h != other.h or self.w != other.w:
            raise(ValueError, "Matrices can only be added if the dimensions are the same")
        else:
            return Matrix(self.g + other.g)

    # 相反数
    def __neg__(self):
        return Matrix(-self.g)

    #减法
    def __sub__(self, other):
        if self.h != other.h or self.w != other.w:
            raise(ValueError, "Matrices can only be subtracted if the dimensions are the same")
        else:
            return Matrix(self.g - other.g)

    # 矩阵乘法：两个矩阵相乘
    def __mul__(self, other):
        if self.w != other.h:
            raise(ValueError, "number of columns of the pre-matrix must equal the number of rows of the post-matrix")    
        return Matrix(np.dot(self.g, other.g))

    # 标量乘法：变量乘以矩阵          
    def __rmul__(self, other):
        if isinstance(other, numbers.Number):
            return Matrix(other * self.g)

# 状态矩阵的初始值
x_initial = Matrix([[initial_distance], [initial_velocity]])

# 误差协方差矩阵的初始值
P_initial = Matrix([[5, 0], [0, 5]])

# 加速度方差
acceleration_variance = 50

# 雷达测量结果方差
lidar_variance = standard_deviation**2

# 观测矩阵，联系预测向量和测量向量
H = Matrix([[1, 0]])

# 测量噪音协方差矩阵。因为测量值只有位置一个变量，所以这里是位置的方差。
R = Matrix([[lidar_variance]])

# 单位矩阵I = Matrix.indentity(2)报错

I = Matrix([[1, 0]])
I=I.identity(2)
print I

# 状态转移矩阵
def F_matrix(delta_t):
    return Matrix([[1, delta_t], [0, 1]])

# 外部噪音协方差矩阵
def Q_matrix(delta_t, variance):
    t4 = math.pow(delta_t, 4)
    t3 = math.pow(delta_t, 3)
    t2 = math.pow(delta_t, 2)
    return variance * Matrix([[(1/4)*t4, (1/2)*t3], [(1/2)*t3, t2]])

def B_matrix(delta_t):
    return Matrix([[delta_t**2 / 2], [delta_t]])

# 状态矩阵
x = x_initial

# 误差协方差矩阵
P = P_initial

# 记录卡尔曼滤波器计算得到的距离
x_result = []

# 记录卡尔曼滤波器的时间
time_result = []

# 记录卡尔曼滤波器得到的速度
v_result = []

for i in range(len(lidar_x_list) - 1):
    delta_t = (lidar_t_list[i + 1] - lidar_t_list[i]) 
    # 预测
    F = F_matrix(delta_t)
    Q = Q_matrix(delta_t, acceleration_variance)

    # 注意：运动模型使用的是匀速运动，汽车实际上有一段时间是加速运动的
    x_prime = F * x
    P_prime = F * P * F.T() + Q

    # 更新
    # 测量向量和状态向量的差值。注意：第一个时刻是没有测量值的，
    # 只有经过一个脉冲周期，才能获得测量值。
    y = Matrix([[lidar_x_list[i + 1]]]) - H * x_prime
    S = H * P_prime * H.T() + R
    K = P_prime * H.T() * S.inverse()
    x = x_prime + K * y
    P = (I - K * H) * P_prime
    x_result.append(x[0][0])
    v_result.append(x[1][0])
    time_result.append(lidar_t_list[i+1])

    # 把真实距离、激光雷达测量的距离以及卡尔曼滤波器的结果（距离）可视化
ax5.set_title("Lidar measurements VS truth")
ax5.set_xlabel("time")
ax5.set_ylabel("distance")
ax5.set_xlim([0, 21])
ax5.set_ylim([0, 1000])
ax5.set_xticks(range(0, 21, 2))
ax5.set_yticks(range(0, 1000, 100))
ax5.plot(t_list, x_list, label="truth distance", color="blue", linewidth=1)
ax5.scatter(lidar_t_list, lidar_x_list, label="Lidar distance", color="red", marker="o", s=2)
ax5.scatter(time_result, x_result, label="kalman", color="green", marker="o", s=2)
ax5.legend()

# 把真实速度、卡尔曼滤波器的结果（速度）可视化
ax6.set_title("Lidar measurements VS truth")
ax6.set_xlabel("time")
ax6.set_ylabel("velocity")
ax6.set_xlim([0, 21])
ax6.set_ylim([0, 50])
ax6.set_xticks(range(0, 21, 2))
ax6.set_yticks(range(0, 50, 5))
ax6.plot(t_list, v_list, label="truth velocity", color="blue", linewidth=1)
ax6.scatter(time_result, v_result, label="Lidar velocity", color="red", marker="o", s=2)
ax6.legend()

plt.show()
```

真实数据和测量数据生成完毕后，下面将这些数据可视化。一共会创建6幅图像，分别是真实距离，真实速度，真实加速度，激光雷达测量的距离值可视化，真实距离、激光雷达测量的距离以及卡尔曼滤波器的结果（距离）可视化，真实速度、卡尔曼滤波器的结果（速度）可视化:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190406104822574.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

- 结果分析:

为了更好地了解各个参数的意义，我们可以尝试修改一些参数，观察图像的变化。

首先看看激光雷达的测量误差。激光雷达的标准差standard_deviation是0.15，真实数据和测量数据的图像是上图中的第4幅，可以看点表示测量数据的离散点分布在表示真实数据的折线的周围。

如果改变标准差standard_deviation，比如从0.15增加到15，离散点的分散程度就明显多了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190406110351479.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

接下来看看真实数据，测量数据和卡尔曼滤波器测量到的距离，也就是第5幅图。当标准差standard_deviation是0.15时，3种数据很难看出差别。

把标准差standard_deviation增加到15，差别就比较明显了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190406110404436.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)
得出以下结论：

第一，相比于测量数据，卡尔曼滤波器计算得出的数据更加接近真实数据；在使用卡尔曼滤波器时，设置的初始距离initial_distance和初始速度initial_velocity都是0，因为初始数据无法测量，所以假设为0.

第二，标准差standard_deviation越大，测量数据越不准确，并且会影响卡尔曼滤波器的精度。比较两幅图可以看出，当标准差standard_deviation是0.15时，卡尔曼滤波器的数据点分布在真实数据周围；而当标准差standard_deviation增加到15时，卡尔曼滤波器的数据点是从0开始的，此时的误差较大；初始距离initial_distance和初始速度initial_velocity同样会影响卡尔曼滤波器的测量精度。保持标准差standard_deviation为15不变，把初始距离initial_distance设置为100（同真实距离），初始速度initial_velocity设置为5（同真实速度），可以发现卡尔曼滤波器的结果更加接近真实数据了,如下图所示:

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190406110848751.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)