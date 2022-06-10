- [UWB定位：第二篇．原理 - 简书 (jianshu.com)](https://www.jianshu.com/p/c7d1bdcd126c)

## 定位方案

### 接收信号强度指示(RSSI)

RSSI(Receive Signal Strength Indicator)通过测量无线信号在接收端的功率大小并根据无线信号的Friis传输模型计算出收发端之间的距离，
 ![\begin{gathered} P_r[dBm] = P_t[dBm] + G_t[dB] + G_r[dB] - L[dB] - 20\log_{10}(4\pi d/\lambda) \\ \Downarrow \\ d = \frac{\lambda}{4\pi} 10^{(P_t - P_r + G_t + G_r - L)/20} \\ \end{gathered}](https://math.jianshu.com/math?formula=%5Cbegin%7Bgathered%7D%20P_r%5BdBm%5D%20%3D%20P_t%5BdBm%5D%20%2B%20G_t%5BdB%5D%20%2B%20G_r%5BdB%5D%20-%20L%5BdB%5D%20-%2020%5Clog_%7B10%7D(4%5Cpi%20d%2F%5Clambda)%20%5C%5C%20%5CDownarrow%20%5C%5C%20d%20%3D%20%5Cfrac%7B%5Clambda%7D%7B4%5Cpi%7D%2010%5E%7B(P_t%20-%20P_r%20%2B%20G_t%20%2B%20G_r%20-%20L)%2F20%7D%20%5C%5C%20%5Cend%7Bgathered%7D)
 其中，![P_r](https://math.jianshu.com/math?formula=P_r)/![P_t](https://math.jianshu.com/math?formula=P_t)分别表示接收/发送信号功率级，![G_r](https://math.jianshu.com/math?formula=G_r)/![G_t](https://math.jianshu.com/math?formula=G_t)分别表示接收/发送天线增益，![L](https://math.jianshu.com/math?formula=L)表示PCB、连接线、连接器等带来的损耗，![d](https://math.jianshu.com/math?formula=d)表示设备间距离，![\lambda](https://math.jianshu.com/math?formula=%5Clambda)表示无线信号的中心波长。

从Friis传输模型中可以看出，RSSI的测距结果受收发天线设计，多径传播，非视距传播，直接路径损耗等环境因数影响较大，实际应用中测距精度~10m量级，远低于基于时间戳测距的方法，因而基于RSSI的方法很少直接用于UWB定位。

### 飞行时间(TOF) / 到达时间(TOA)

TOF(Time of Flight)/TOA(Time of Arrival)通过记录测距消息的收发时间戳来计算无线信号从发送设备到接收设备的传播时间，乘以光速然后得到设备间的距离。根据测距消息的传输方式不同可分为单向测距和双向测距，其中单向测距中测距消息仅单向传播，为获得设备间的飞行时间需要双方设备保持精确的时钟同步，系统实现复杂度和成本较高，而双向测距对双方设备的时钟同步没有要求，系统实现复杂度和成本很低，因而我们主要关注双向测距这种方案。

#### 双向测距(TWR)

![img](https:////upload-images.jianshu.io/upload_images/19885429-61e9e0b8f3931bfd?imageMogr2/auto-orient/strip|imageView2/2/w/742/format/webp)

TWR示意图

TWR(Two-Way Ranging)方法需要设备间支持**双向通信**，通过UWB信号收发时间戳计算UWB信号的往返时间然后乘光速从而获得两个设备间的实际距离信息。

- 单边双向测距(SS-TWR)

  SS-TWR(Single-Sided Two-Way Ranging)算法中测距请求设备发起测距请求，而测距响应设备监听并响应测距请求，然后测距请求设备利用所有时间戳信息计算出设备间的飞行时间。

  - 测距流程

    ![img](https:////upload-images.jianshu.io/upload_images/19885429-af7a2d45c5f1d683?imageMogr2/auto-orient/strip|imageView2/2/w/660/format/webp)

    SS-TWR

    具体的，SS-TWR算法中设备A发起测距请求信息，设备B响应测距并返回消息处理时延![T_{reply}](https://math.jianshu.com/math?formula=T_%7Breply%7D)，设备![A](https://math.jianshu.com/math?formula=A)收到响应消息后计算出消息的往返时延![T_{round}](https://math.jianshu.com/math?formula=T_%7Bround%7D)，然后即可计算出设备A，B间的飞行时间：![T_{prop} = 0.5 \cdotp (T_{round} - T_{reply})](https://math.jianshu.com/math?formula=T_%7Bprop%7D%20%3D%200.5%20%5Ccdotp%20(T_%7Bround%7D%20-%20T_%7Breply%7D))。

  - 距离计算与误差分析

    假定设备A，B的晶振频率偏移分别为![e_A](https://math.jianshu.com/math?formula=e_A)，![e_B](https://math.jianshu.com/math?formula=e_B)，有
     ![\begin{gathered} \breve{T}_{round} = (1 + e_A) \cdotp T_{round} \\ \breve{T}_{reply} = (1 + e_B) \cdotp T_{reply} \\ \end{gathered}](https://math.jianshu.com/math?formula=%5Cbegin%7Bgathered%7D%20%5Cbreve%7BT%7D_%7Bround%7D%20%3D%20(1%20%2B%20e_A)%20%5Ccdotp%20T_%7Bround%7D%20%5C%5C%20%5Cbreve%7BT%7D_%7Breply%7D%20%3D%20(1%20%2B%20e_B)%20%5Ccdotp%20T_%7Breply%7D%20%5C%5C%20%5Cend%7Bgathered%7D)
     带入![\breve{T}_{prop} = 0.5 \cdotp (\breve{T}_{round} - \breve{T}_{reply})](https://math.jianshu.com/math?formula=%5Cbreve%7BT%7D_%7Bprop%7D%20%3D%200.5%20%5Ccdotp%20(%5Cbreve%7BT%7D_%7Bround%7D%20-%20%5Cbreve%7BT%7D_%7Breply%7D))，此外，![T_{prop} = 0.5 \cdotp (T_{round} - T_{reply})](https://math.jianshu.com/math?formula=T_%7Bprop%7D%20%3D%200.5%20%5Ccdotp%20(T_%7Bround%7D%20-%20T_%7Breply%7D))，有
     ![\begin{aligned} \breve{T}_{prop} &= 0.5 \cdotp (T_{round} - T_{reply}) + 0.5 \cdotp (e_A T_{round} - e_B T_{reply}) \\ &\simeq T_{prop} + 0.5 \cdotp (e_A - e_B) \cdotp T_{reply} \\ \end{aligned}](https://math.jianshu.com/math?formula=%5Cbegin%7Baligned%7D%20%5Cbreve%7BT%7D_%7Bprop%7D%20%26%3D%200.5%20%5Ccdotp%20(T_%7Bround%7D%20-%20T_%7Breply%7D)%20%2B%200.5%20%5Ccdotp%20(e_A%20T_%7Bround%7D%20-%20e_B%20T_%7Breply%7D)%20%5C%5C%20%26%5Csimeq%20T_%7Bprop%7D%20%2B%200.5%20%5Ccdotp%20(e_A%20-%20e_B)%20%5Ccdotp%20T_%7Breply%7D%20%5C%5C%20%5Cend%7Baligned%7D)
     从而SS-TWR的测距误差为：
     ![\tilde{T}_{prop} \triangleq \breve{T}_{prop} - T_{prop} = 0.5 \cdotp (e_A - e_B) \cdotp T_{reply}](https://math.jianshu.com/math?formula=%5Ctilde%7BT%7D_%7Bprop%7D%20%5Ctriangleq%20%5Cbreve%7BT%7D_%7Bprop%7D%20-%20T_%7Bprop%7D%20%3D%200.5%20%5Ccdotp%20(e_A%20-%20e_B)%20%5Ccdotp%20T_%7Breply%7D)
     由此可知，SS-TWR的测距误差既与设备间的相对晶振频率差值成正比，也与测距消息响应的时长成正比。

- 双边双向测距(DS-TWR)

  DS-TWR(Double-Sided Two-Way Ranging)算法中测距双方设备都会发起一次测距请求，等价于双方设备各自发起一次SS-TWR，分别具有误差![\tilde{T}^{(1)}_{prop} = 0.5 \cdotp (e_A - e_B) \cdotp T^{(1)}_{reply}](https://math.jianshu.com/math?formula=%5Ctilde%7BT%7D%5E%7B(1)%7D_%7Bprop%7D%20%3D%200.5%20%5Ccdotp%20(e_A%20-%20e_B)%20%5Ccdotp%20T%5E%7B(1)%7D_%7Breply%7D)，![\tilde{T}^{(2)}_{prop} = 0.5 \cdotp (e_B - e_A) \cdotp T^{(2)}_{reply}](https://math.jianshu.com/math?formula=%5Ctilde%7BT%7D%5E%7B(2)%7D_%7Bprop%7D%20%3D%200.5%20%5Ccdotp%20(e_B%20-%20e_A)%20%5Ccdotp%20T%5E%7B(2)%7D_%7Breply%7D)，当![T^{(1)}_{reply} \simeq T^{(2)}_{reply}](https://math.jianshu.com/math?formula=T%5E%7B(1)%7D_%7Breply%7D%20%5Csimeq%20T%5E%7B(2)%7D_%7Breply%7D)时，DS-TWR的整体测距误差![\tilde{T}_{prop} = 0.5 \cdotp (\tilde{T}^{(1)}_{prop} + \tilde{T}^{(2)}_{prop}) \simeq 0](https://math.jianshu.com/math?formula=%5Ctilde%7BT%7D_%7Bprop%7D%20%3D%200.5%20%5Ccdotp%20(%5Ctilde%7BT%7D%5E%7B(1)%7D_%7Bprop%7D%20%2B%20%5Ctilde%7BT%7D%5E%7B(2)%7D_%7Bprop%7D)%20%5Csimeq%200)，相较与SS-TWR可极大的提升测距精度。

  - 测距流程

    ![img](https:////upload-images.jianshu.io/upload_images/19885429-803ad62ef1b7ccd8?imageMogr2/auto-orient/strip|imageView2/2/w/659/format/webp)

    DS-TWR-4round

    从图中可以看出朴素的DS-TWR算法实现需要测距双方交换4条消息，分析测距消息交换流程可以发现，第二条测距响应消息和第三条测距请求消息都是由同一设备相继执行，因而可合并为一条消息。由于测距流程上消息交换次数减少，从而一方面减小测距时间，另一方面降低测距功耗，且不影响测距精度。改进后的DS-TWR如下图所示：

    ![img](https:////upload-images.jianshu.io/upload_images/19885429-204a7fe5f8f2861c?imageMogr2/auto-orient/strip|imageView2/2/w/659/format/webp)

    DS-TWR-3round

    DS-TWR算法根据![T^{(1)}_{reply}](https://math.jianshu.com/math?formula=T%5E%7B(1)%7D_%7Breply%7D)是否等于（或接近）![T^{(2)}_{reply}](https://math.jianshu.com/math?formula=T%5E%7B(2)%7D_%7Breply%7D)又可分为：

    - 非对称双边双向测距(ADS-TWR)：

      ADS-TWR(Asymmetric Double-Sided Two-Way Ranging)不必![T^{(1)}_{reply} = T^{(2)}_{reply}](https://math.jianshu.com/math?formula=T%5E%7B(1)%7D_%7Breply%7D%20%3D%20T%5E%7B(2)%7D_%7Breply%7D)，使得在测距流程中对时间控制更具灵活性。

    - 对称双边双向测距(SDS-TWR)：

      SDS-TWR(Symmetric Double-Sided Two-Way Ranging)中![T^{(1)}_{reply} = T^{(2)}_{reply}](https://math.jianshu.com/math?formula=T%5E%7B(1)%7D_%7Breply%7D%20%3D%20T%5E%7B(2)%7D_%7Breply%7D)，可简化测距计算过程，特别适合在低功耗的微控制器上使用。

  - 距离计算与误差分析

    假定设备A，B的晶振频率偏移分别为![e_A](https://math.jianshu.com/math?formula=e_A)，![e_B](https://math.jianshu.com/math?formula=e_B)，即
     ![\begin{gathered} \breve{T}_{round} = (1 + e_A) \cdotp T_{round} \\ \breve{T}_{reply} = (1 + e_B) \cdotp T_{reply} \\ \end{gathered}](https://math.jianshu.com/math?formula=%5Cbegin%7Bgathered%7D%20%5Cbreve%7BT%7D_%7Bround%7D%20%3D%20(1%20%2B%20e_A)%20%5Ccdotp%20T_%7Bround%7D%20%5C%5C%20%5Cbreve%7BT%7D_%7Breply%7D%20%3D%20(1%20%2B%20e_B)%20%5Ccdotp%20T_%7Breply%7D%20%5C%5C%20%5Cend%7Bgathered%7D)
     此外，
     ![\begin{gathered} T_{prop} = 0.5 \cdotp (T_{round} - T_{reply}) \\ \end{gathered}](https://math.jianshu.com/math?formula=%5Cbegin%7Bgathered%7D%20T_%7Bprop%7D%20%3D%200.5%20%5Ccdotp%20(T_%7Bround%7D%20-%20T_%7Breply%7D)%20%5C%5C%20%5Cend%7Bgathered%7D)
     DS-TWR的测距计算方式有如下两种：

    - SDS-TWR方式

      ![\begin{aligned} \breve{T}_{prop} &= 0.25 \cdotp (\breve{T}_{round1} - \breve{T}_{reply1} + \breve{T}_{round2} - \breve{T}_{reply2}) \\ &= 0.5 \cdotp (2 + e_A + e_B) \cdotp T_{prop} + 0.25 \cdotp (e_A - e_B) \cdotp (T_{reply1} - T_{reply2}) \\ \end{aligned}](https://math.jianshu.com/math?formula=%5Cbegin%7Baligned%7D%20%5Cbreve%7BT%7D_%7Bprop%7D%20%26%3D%200.25%20%5Ccdotp%20(%5Cbreve%7BT%7D_%7Bround1%7D%20-%20%5Cbreve%7BT%7D_%7Breply1%7D%20%2B%20%5Cbreve%7BT%7D_%7Bround2%7D%20-%20%5Cbreve%7BT%7D_%7Breply2%7D)%20%5C%5C%20%26%3D%200.5%20%5Ccdotp%20(2%20%2B%20e_A%20%2B%20e_B)%20%5Ccdotp%20T_%7Bprop%7D%20%2B%200.25%20%5Ccdotp%20(e_A%20-%20e_B)%20%5Ccdotp%20(T_%7Breply1%7D%20-%20T_%7Breply2%7D)%20%5C%5C%20%5Cend%7Baligned%7D)
       测距误差为：
       ![\begin{aligned} \tilde{T}_{prop} \triangleq \breve{T}_{prop} - T_{prop} = 0.5 \cdotp (e_A + e_B) \cdotp T_{prop} + 0.25 \cdotp (e_A - e_B) \cdotp (T_{reply1} - T_{reply2}) \\ \end{aligned}](https://math.jianshu.com/math?formula=%5Cbegin%7Baligned%7D%20%5Ctilde%7BT%7D_%7Bprop%7D%20%5Ctriangleq%20%5Cbreve%7BT%7D_%7Bprop%7D%20-%20T_%7Bprop%7D%20%3D%200.5%20%5Ccdotp%20(e_A%20%2B%20e_B)%20%5Ccdotp%20T_%7Bprop%7D%20%2B%200.25%20%5Ccdotp%20(e_A%20-%20e_B)%20%5Ccdotp%20(T_%7Breply1%7D%20-%20T_%7Breply2%7D)%20%5C%5C%20%5Cend%7Baligned%7D)
       当![T_{reply1} = T_{reply2}](https://math.jianshu.com/math?formula=T_%7Breply1%7D%20%3D%20T_%7Breply2%7D)，有![\tilde{T}_{prop} = 0.5 \cdotp (e_A + e_B) \cdotp T_{prop}](https://math.jianshu.com/math?formula=%5Ctilde%7BT%7D_%7Bprop%7D%20%3D%200.5%20%5Ccdotp%20(e_A%20%2B%20e_B)%20%5Ccdotp%20T_%7Bprop%7D)，即测距误差仅与设备间的无线信号飞行时间![T_{prop}](https://math.jianshu.com/math?formula=T_%7Bprop%7D)有关，相对测距误差![0.5\cdotp(e_A + e_B) \sim 1e^{-5}](https://math.jianshu.com/math?formula=0.5%5Ccdotp(e_A%20%2B%20e_B)%20%5Csim%201e%5E%7B-5%7D)。

    - AltDS-TWR方式

      ![\begin{aligned} \breve{T}_{prop} &=　\frac{(T_{round1} \cdotp T_{round2} - T_{reply1} \cdotp T_{reply2})}{(T_{round1} + T_{round2} + T_{reply1} + T_{reply2})} \\ &= \Big\{\begin{matrix} (1 + e_A) \cdotp T_{prop} \\ (1 + e_B) \cdotp T_{prop} \\ \end{matrix} \end{aligned}](https://math.jianshu.com/math?formula=%5Cbegin%7Baligned%7D%20%5Cbreve%7BT%7D_%7Bprop%7D%20%26%3D%E3%80%80%5Cfrac%7B(T_%7Bround1%7D%20%5Ccdotp%20T_%7Bround2%7D%20-%20T_%7Breply1%7D%20%5Ccdotp%20T_%7Breply2%7D)%7D%7B(T_%7Bround1%7D%20%2B%20T_%7Bround2%7D%20%2B%20T_%7Breply1%7D%20%2B%20T_%7Breply2%7D)%7D%20%5C%5C%20%26%3D%20%5CBig%5C%7B%5Cbegin%7Bmatrix%7D%20(1%20%2B%20e_A)%20%5Ccdotp%20T_%7Bprop%7D%20%5C%5C%20(1%20%2B%20e_B)%20%5Ccdotp%20T_%7Bprop%7D%20%5C%5C%20%5Cend%7Bmatrix%7D%20%5Cend%7Baligned%7D)
       测距误差为：
       ![\begin{aligned} \tilde{T}_{prop} &\triangleq \breve{T}_{prop} - T_{prop} \\ &= \Big\{\begin{matrix} e_A \cdotp T_{prop} \\ e_B \cdotp T_{prop} \\ \end{matrix} \end{aligned}](https://math.jianshu.com/math?formula=%5Cbegin%7Baligned%7D%20%5Ctilde%7BT%7D_%7Bprop%7D%20%26%5Ctriangleq%20%5Cbreve%7BT%7D_%7Bprop%7D%20-%20T_%7Bprop%7D%20%5C%5C%20%26%3D%20%5CBig%5C%7B%5Cbegin%7Bmatrix%7D%20e_A%20%5Ccdotp%20T_%7Bprop%7D%20%5C%5C%20e_B%20%5Ccdotp%20T_%7Bprop%7D%20%5C%5C%20%5Cend%7Bmatrix%7D%20%5Cend%7Baligned%7D)
       可见，该方式的测距误差仅与设备间的无线信号飞行时间![T_{prop}](https://math.jianshu.com/math?formula=T_%7Bprop%7D)有关，相对测距误差![\max(e_A, e_B) \sim 1e^{-5}](https://math.jianshu.com/math?formula=%5Cmax(e_A%2C%20e_B)%20%5Csim%201e%5E%7B-5%7D)。

    SDS-TWR方式具有较小的计算量，但当双方设备的测距响应时延不相等时，有较大的误差；AltDS-TWR方式计算相对复杂，但对双方设备的测距响应时延没有要求，灵活性较强。

#### 多边定位算法

标签![T](https://math.jianshu.com/math?formula=T)通过与多(2维>=3, 3维>=4)个位置坐标固定且已知的基站![A_k](https://math.jianshu.com/math?formula=A_k)轮流进行TWR获得相应的测距数据![D_k](https://math.jianshu.com/math?formula=D_k)，通过求解以下方程获得定位结果：
 ![\left\{ \begin{gathered} \sqrt{(x - x_{A_1})^2 + (y - y_{A_1})^2 + (z - z_{A_1})^2} = D_k \\ \sqrt{(x - x_{A_2})^2 + (y - y_{A_2})^2 + (z - z_{A_2})^2} = D_k \\ \vdots \\ \sqrt{(x - x_{A_k})^2 + (y - y_{A_k})^2 + (z - z_{A_k})^2} = D_k \\ \end{gathered} \right.](https://math.jianshu.com/math?formula=%5Cleft%5C%7B%20%5Cbegin%7Bgathered%7D%20%5Csqrt%7B(x%20-%20x_%7BA_1%7D)%5E2%20%2B%20(y%20-%20y_%7BA_1%7D)%5E2%20%2B%20(z%20-%20z_%7BA_1%7D)%5E2%7D%20%3D%20D_k%20%5C%5C%20%5Csqrt%7B(x%20-%20x_%7BA_2%7D)%5E2%20%2B%20(y%20-%20y_%7BA_2%7D)%5E2%20%2B%20(z%20-%20z_%7BA_2%7D)%5E2%7D%20%3D%20D_k%20%5C%5C%20%5Cvdots%20%5C%5C%20%5Csqrt%7B(x%20-%20x_%7BA_k%7D)%5E2%20%2B%20(y%20-%20y_%7BA_k%7D)%5E2%20%2B%20(z%20-%20z_%7BA_k%7D)%5E2%7D%20%3D%20D_k%20%5C%5C%20%5Cend%7Bgathered%7D%20%5Cright.)

### 到达时差(TDOA)

![img](https:////upload-images.jianshu.io/upload_images/19885429-10fac88950170b0b?imageMogr2/auto-orient/strip|imageView2/2/w/660/format/webp)

TDOA示意图

TDOA(Time Difference of Arrival)是对TOA算法的改进，不是直接利用信号到达时间，而是通过检测信号到达多个**严格时间同步**基站的到达时间差来计算标签的位置，该方法不需要标签和基站保持时间同步。时间同步分有线时间同步和无线时间同步两种方式，有线时间同步通过专用的有线时间同步器进行时钟分发，精度~0.1ns，但时钟网络的部署和维护代价以及成本较高。无线时间同步不需要特殊同步设备，精度~0.3ns低于有线时间同步，不过其系统部署维护和成本相对较低。由于无线时间同步从部署维护难度，系统扩展灵活性，和成本上都优于有线时间同步，同时***TDOA定位\***算法在这这两种方式中的用法几乎一致，我们主要关注无线时间同步方式下的TDOA定位。

TDOA定位方案根据标签端的工作模式可分为：

- 基于服务器(Server-based) TDOA:

  ![img](https:////upload-images.jianshu.io/upload_images/19885429-bf9fe134ac850dac?imageMogr2/auto-orient/strip|imageView2/2/w/660/format/webp)

  TDOA-server

  这种方案是市场上最常见的UWB定位方案，又称为主动式TDOA，在这种方案中，标签端持续的广播定位信标(beacon)信号，基站记录标签beacon信号的接收时间戳并将该标签相关的信息发往中心定位服务器，由于所有基站的时间都已同步，定位服务器即可根据beacon信号到达不同位置基站的时间差值来计算标签的位置信息。简单的说，标签端***发送\***定位信标信号，在***服务器处\***完成标签的位置计算，根据具体场景需求服务器可以将标签的位置信息通过网络(Wifi/Bluetooth/Zigbee/UWB/...)再发送回标签端。

  - 优势：
    - 标签端每次定位只需要发送一条定位消息，从而降低能量消耗；
    - 视距范围内所有的基站都可用来定位，从而可获得更鲁棒和更精确的定位结果；
    - 定位网络可以实时监测所有发送测距消息的标签及其位置；
  - 劣势：
    - 所有的基站都必须时间同步，使得系统的复杂度和成本较高；
    - 服务器端输出标签的定位结果，标签端获取定位结果必须借助其他通信网络；
    - 由于标签端仅发送定位消息而不接收，数据聚合和协作定位很难实现；

  该方案相较于基于客户端TDOA方案适合：
   \- 医院、养老院、护理院、隧道、矿井等需要进行人员监控和追踪的场所；
   \- 工厂、企业等需要对资产、员工、访客、设备等进行追踪的场合；
   \- 大型公共场所如商场、会展厅、博物馆等需要对人流密度进行监控和分析的情形；
   \- ...

- 基于客户端(Client-based) TDOA

  ![img](https:////upload-images.jianshu.io/upload_images/19885429-2c4d14b1e0b8ed14?imageMogr2/auto-orient/strip|imageView2/2/w/660/format/webp)

  TDOA-client

  这种方案又称为反向TDOA或被动式TDOA，在这种方案中，基站端按照一定的次序在相应的时间槽上广播定位信标(beacon)信号，标签记录基站beacon信号的接收时间戳，由于所有基站的时间都已同步，标签本身即可利用不同基站的位置信息和相应基站beacon信号到达的时间戳计算出标签的位置信息。简单的说，标签仅***接受\***基站发送的定位信标信号，在***本地处\***完成标签的位置计算，根据具体场景需求标签可以将自己位置信息再发送(UWB/Wifi/Bluetooth/Zigbee/...)到服务器端。

  - 优势：
    - 标签端直接输出定位结果，定位的实时性更高；
    - 理论上网络可支持无限容量的标签同时进行定位；
    - 视距范围内所有的基站都可用来定位，从而可获得更鲁棒和更精确的定位结果；
  - 劣势：
    - 为防止基站广播定位消息发生冲突，必须采用较复杂的时间分片机制，使得系统复杂度很高；
    - 标签端持续监听基站定位消息，使得能量消耗较高；
    - 所有的基站都必须时间同步，使得系统的复杂度和成本较高；
    - 如果标签仅接收基站定位消息而不发送消息，定位网络无法监测网络内存在的标签；

  该方案相较于基于服务器TDOA方案适合：
   \- 工厂、园区等地方的AMR(自主移动机器人)导航；
   \- 大型公共场所如商场、会展厅、博物馆、医院、停车场等地的导引；
   \- 大规模机器群体（如无人机队，仓储物流搬运机器群等）协作调度指引；
   \- 大规模多人在线的VR应用与游戏等；
   \- ...

#### TDOA定位算法

2维TDOA定位需要至少3个位置坐标固定且已知的基站，而3维TDOA定位需要至少4个位置坐标固定且已知的基站，假定标签![T](https://math.jianshu.com/math?formula=T)某次测距共有![N](https://math.jianshu.com/math?formula=N)个基站![\{A_k\}_{1:N}](https://math.jianshu.com/math?formula=%5C%7BA_k%5C%7D_%7B1%3AN%7D)参与，则可获得![C_N^2](https://math.jianshu.com/math?formula=C_N%5E2)组时间差值，然而其中相互独立的约束仅有![N-1](https://math.jianshu.com/math?formula=N-1)个。记标签![T](https://math.jianshu.com/math?formula=T)与基站![A_i](https://math.jianshu.com/math?formula=A_i)，![A_j](https://math.jianshu.com/math?formula=A_j)测得的时间差为![\Delta_{i,j}](https://math.jianshu.com/math?formula=%5CDelta_%7Bi%2Cj%7D)，可取如下相互独立的约束方程用于求解标签位置：
 ![\left\{ \begin{gathered} \sqrt{(x - x_{A_2})^2 + (y - y_{A_2})^2 + (z - z_{A_2})^2} - \sqrt{(x - x_{A_1})^2 + (y - y_{A_1})^2 + (z - z_{A_1})^2} = c \cdotp \Delta_{1,2} \\ \sqrt{(x - x_{A_3})^2 + (y - y_{A_3})^2 + (z - z_{A_3})^2} - \sqrt{(x - x_{A_2})^2 + (y - y_{A_2})^2 + (z - z_{A_2})^2} = c \cdotp \Delta_{2,3} \\ \sqrt{(x - x_{A_4})^2 + (y - y_{A_4})^2 + (z - z_{A_4})^2} - \sqrt{(x - x_{A_3})^2 + (y - y_{A_3})^2 + (z - z_{A_3})^2} = c \cdotp \Delta_{3,4} \\ \vdots \\ \sqrt{(x - x_{A_N})^2 + (y - y_{A_N})^2 + (z - z_{A_N})^2} - \sqrt{(x - x_{A_{N-1}})^2 + (y - y_{A_{N-1}})^2 + (z - z_{A_{N-1}})^2} = c \cdotp \Delta_{N,N-1} \\ \end{gathered} \right.](https://math.jianshu.com/math?formula=%5Cleft%5C%7B%20%5Cbegin%7Bgathered%7D%20%5Csqrt%7B(x%20-%20x_%7BA_2%7D)%5E2%20%2B%20(y%20-%20y_%7BA_2%7D)%5E2%20%2B%20(z%20-%20z_%7BA_2%7D)%5E2%7D%20-%20%5Csqrt%7B(x%20-%20x_%7BA_1%7D)%5E2%20%2B%20(y%20-%20y_%7BA_1%7D)%5E2%20%2B%20(z%20-%20z_%7BA_1%7D)%5E2%7D%20%3D%20c%20%5Ccdotp%20%5CDelta_%7B1%2C2%7D%20%5C%5C%20%5Csqrt%7B(x%20-%20x_%7BA_3%7D)%5E2%20%2B%20(y%20-%20y_%7BA_3%7D)%5E2%20%2B%20(z%20-%20z_%7BA_3%7D)%5E2%7D%20-%20%5Csqrt%7B(x%20-%20x_%7BA_2%7D)%5E2%20%2B%20(y%20-%20y_%7BA_2%7D)%5E2%20%2B%20(z%20-%20z_%7BA_2%7D)%5E2%7D%20%3D%20c%20%5Ccdotp%20%5CDelta_%7B2%2C3%7D%20%5C%5C%20%5Csqrt%7B(x%20-%20x_%7BA_4%7D)%5E2%20%2B%20(y%20-%20y_%7BA_4%7D)%5E2%20%2B%20(z%20-%20z_%7BA_4%7D)%5E2%7D%20-%20%5Csqrt%7B(x%20-%20x_%7BA_3%7D)%5E2%20%2B%20(y%20-%20y_%7BA_3%7D)%5E2%20%2B%20(z%20-%20z_%7BA_3%7D)%5E2%7D%20%3D%20c%20%5Ccdotp%20%5CDelta_%7B3%2C4%7D%20%5C%5C%20%5Cvdots%20%5C%5C%20%5Csqrt%7B(x%20-%20x_%7BA_N%7D)%5E2%20%2B%20(y%20-%20y_%7BA_N%7D)%5E2%20%2B%20(z%20-%20z_%7BA_N%7D)%5E2%7D%20-%20%5Csqrt%7B(x%20-%20x_%7BA_%7BN-1%7D%7D)%5E2%20%2B%20(y%20-%20y_%7BA_%7BN-1%7D%7D)%5E2%20%2B%20(z%20-%20z_%7BA_%7BN-1%7D%7D)%5E2%7D%20%3D%20c%20%5Ccdotp%20%5CDelta_%7BN%2CN-1%7D%20%5C%5C%20%5Cend%7Bgathered%7D%20%5Cright.)

### 到达角度AOA / 到达相差PDOA

![img](https:////upload-images.jianshu.io/upload_images/19885429-51d8d63589a40c74?imageMogr2/auto-orient/strip|imageView2/2/w/455/format/webp)

AOA角度计算

AOA(Angle of Arrival)/PDOA(Phase Difference of Arrival)通过计算信号到达不同位置接收天线的相位差值来计算信号的接收方向从而确定其相对于自身的朝向。

![d\sin(\theta) = \frac{\lambda\Delta\phi}{2\pi} \Longrightarrow \theta = \sin^{-1}(\frac{\lambda\Delta\phi}{2\pi d})](https://math.jianshu.com/math?formula=d%5Csin(%5Ctheta)%20%3D%20%5Cfrac%7B%5Clambda%5CDelta%5Cphi%7D%7B2%5Cpi%7D%20%5CLongrightarrow%20%5Ctheta%20%3D%20%5Csin%5E%7B-1%7D(%5Cfrac%7B%5Clambda%5CDelta%5Cphi%7D%7B2%5Cpi%20d%7D))

#### AOA定位算法

![img](https:////upload-images.jianshu.io/upload_images/19885429-d75ea9aad6852f1e?imageMogr2/auto-orient/strip|imageView2/2/w/660/format/webp)

AOA定位示意图

***该方案需要配置多天线，项目暂未使用该方案（略过，待续）\***

------

## 总结

![img](https:////upload-images.jianshu.io/upload_images/19885429-b448f82495e9833e.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/800/format/webp)

UWB定位方案比较