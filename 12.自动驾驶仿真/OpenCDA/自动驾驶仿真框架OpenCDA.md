- [详解自动驾驶仿真框架OpenCDA: An Open Cooperative Driving Automation Framework Integrated with Co-Simulation_自动驾驶小学生的博客-CSDN博客_自动驾驶开源框架](https://blog.csdn.net/cg129054036/article/details/119297117)
- [OpenCDA：一个开源的多车协同自动驾驶仿真平台 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/399299136)

![在这里插入图片描述](https://img-blog.csdnimg.cn/d3d006efa36c4d73a5e5e3f2e7fb205e.gif#pic_center)

本文介绍一款同时支持`协同驾驶开发与测试、自动驾驶全栈开发 和 CARLA-SUMO联合仿真`的开源[框架](https://so.csdn.net/so/search?q=框架&spm=1001.2101.3001.7020) `OpenCDA`，论文已收录于 ***ITSC 2021***。主要feature有：

1. 支持CARLA-SUMO联合仿真，CARLA端主管环境渲染、传感器模拟、车辆动力，Sumo端主管交通仿真。
2. 同时支持`协同驾驶/单车智能`的开发。内置简单易用的V2X模拟，可以灵活模拟各种噪声与信号延迟。
3. OpenCDA自带默认的`感知、定位、规划、控制、协同变道与车队行驶`的算法，可以说搞懂了OpenCDA就等于搞懂了如何在CARLA里做完整的自动驾驶全栈开发。
4. 自带10+个测试场景，可快速测试你各个模块算法的鲁棒性。在特定地图下自定义场景快捷简单，只需给定一个yaml文件+10行代码即可搞定！
5. 框架高度模块化，可以轻易将任一模块里的算法切换为用户的算法，而不会影响其他的模块运行。
6. 提供默认的评价指标，每次仿真运行后自动评价各模块表现与整车表现。
7. 安装简便，文档记录详细，有完整的开发者手册。

论文链接为：https://arxiv.org/pdf/2107.06260.pdf

项目链接为：https://github.com/ucla-mobility/OpenCDA

文档链接为：https://opencda-documentation.readthedocs.io/en/latest/

### 1. Overview of OpenCDA

下面对 `OpenCDA` 进行介绍，如下图所示，`OpenCDA`由三部分组成：`simulation tools`、`cooperative driving automation system`、`scenario manager`。

- 仿真工具中，环境仿真使用`CARLA`仿真，交通仿真使用`SUMO`仿真，此外还使用了其它仿真工具，例如无线通信仿真工具`ns-3`等。
- 协同驾驶系统中，传感器模块收集原始的传感器信息，信息传送到感知层，然后传递到规划层，最后通过执行层发送执行命令，`CARLA` 执行器执行命令。
- 场景管理中，包含`场景配置文件`，`场景初始化`，`特定事件触发器`，`评估功能`。场景中包含静态元素和动态元素，静态元素由`CARLA`确定，动态元素由配置文件确定。当任务结束时，会对整个驾驶过程进行评估，包括交通层面评估以及单车层面评估。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/8a946c87dd474ae8988d8f095ba04963.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

------

**Software Class Design and Logic Flow**

为了更好地说明`OpenCDA`是如何工作的，这里以一个应用例子 `vehicle platooning` 来说明，首先介绍`类组件`，如下图所示。

使用分层类管理器来控制仿真，最基础的类为 `VehicleManager`，包含着全栈协同驾驶算法。类成员 `PerceptionManager` 和 `LocalizationManager` 对自车进行感知和定位。`BehaviorAgent` 规划驾驶行为，同时其 `LocalPlanner` 通过三次样条插值方法产生轨迹：
y t = α 0 + α 1 x t + α 2 x t 2 + α 3 x t 3 (1) y_t = \alpha_0+\alpha_1x_t+\alpha_2x_t^2+\alpha_3x_t^3 \tag{1}*y**t*​=*α*0​+*α*1​*x**t*​+*α*2​*x**t*2​+*α*3​*x**t*3​(1)
a t = { min ⁡ ( v t a r g e t − v t Δ t , a 1 ) , if v t a r g e t ≥ v t max ⁡ ( v t a r g e t − v t Δ t , a 2 ) , otherwise (2) a_t =\begin {cases} \min(\frac{v_{target}-v_t}{\Delta t},a^1), &\text {if $v_{target} \geq v_t$} \\ \max(\frac{v_{target}-v_t}{\Delta t},a^2), &\text {otherwise} \end{cases} \tag{2}*a**t*​={min(Δ*t**v**t**a**r**g**e**t*​−*v**t*​​,*a*1),max(Δ*t**v**t**a**r**g**e**t*​−*v**t*​​,*a*2),​if *v**t**a**r**g**e**t*​≥*v**t*​otherwise​(2)
x t = v t − 1 Δ t + a t − 1 Δ t 2 2 (3) x_t = v_{t-1}\Delta t + \frac{a_{t-1}\Delta t^2}{2} \tag{3}*x**t*​=*v**t*−1​Δ*t*+2*a**t*−1​Δ*t*2​(3)
v t = v t − 1 + a t − 1 Δ t (4) v_t = v_{t-1} + a_{t-1}\Delta t \tag{4}*v**t*​=*v**t*−1​+*a**t*−1​Δ*t*(4)

其中，x t , y t x_t,y_t*x**t*,*y**t*是汽车在t t*t*时刻的位置，α 0 , α 1 , α 2 , α 3 \alpha_0,\alpha_1,\alpha_2,\alpha_3*α*0,*α*1,*α*2,*α*3是三次多项式系数，a t a_t*a**t*是加速度，其中a 1 , a 2 a^1,a^2*a*1,*a*2是与舒适性相关的加速度和减速度。Δ t \Delta tΔ*t*是时间精度，v t a r g e t , v t v_{target},v_t*v**t**a**r**g**e**t*,*v**t*是最终想要的目标速度和t t*t*时刻的速度。

产生后的轨迹会传送到 `ControlManager` 产生转向，制动，加速等控制命令。`V2XManager` 会发送或接收由其余`CAVs`生成的数据包，用于协同驾驶应用。
![在这里插入图片描述](https://img-blog.csdnimg.cn/6ab6884da078468dbc17cdc3eeaa8b6a.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70#pic_center)
仿真工作时的逻辑流如下图所示。场景测试时：

- 通过`yaml`文件配置`CARLA server`、`交通情况`和每一个`汽车参数`（传感器参数、检测模型、目标速度等）。
- 接下来，每辆汽车通过`V2XManager`信息共享，如果激活了协同应用，`CoopPerceptionManager`和`CoopLocalizationManager`会使用所有的信息进行目标检测和定位；反之，汽车会选择默认的`PerceptionManager`和`LocalizationManager`。
- 信息传送到下流模块，进行规划。同样地，协同应用激活的话会选择协同策略做出决策；反之`BehaviorAgent`和`TrajectoryPlanner`会规划行为并生成平顺的轨迹。
- 最终，`ControlManager`输出控制命令，`CARLA server`将这些命令应用在对应汽车上，更新信息，进行下一步仿真。
- 仿真终止时，内置评估工具箱对驾驶性能进行评估，包括感知、定位、规划、控制、安全性等。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/4680d347580a4868b65ffdc01ef839bb.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

------

### 2. Experiment Setups and Evaluation Measurement

为了验证`OpenCDA`的实际效果如何，作者以`汽车编队（vehicle platooning）`为例来说明，整个仿真测试时间步长为0.05 s 0.05s0.05*s*。

#### 2.1 Platooning Protocol Design

首先是编队协议设计，在编队应用中，所有车辆由`PlatoonManager`来管理，协议如下图所示。整体上，整个驾驶任务可以分成许多子任务，编队成员根据编队状态的不同也有着不同的驾驶模式。

`编队应用被激活后`，编队中的`领航车（leading vehicle）`会通过`V2XManager`听取外部车辆的入队请求。如果没有收到请求，整个队列会保持稳定行驶而领航车会保持领航模式。与此同时，`如果协同感知应用被激活`，编队每一个成员会共享彼此间的感知数据（如图像，3D点云），并且会通过`PerceptionManager`进行感知处理，领航车会得到更好的感知结果。

没有车辆申请入队的话，所有`跟随车（following vehicle）`会平缓的调整车速使队列的相邻汽车保持一个恒定时间间隔，为了完成这个任务，编队成员会通过`V2XManager`得到前车的轨迹，方法如下：
p o s j t = p o s j − 1 t − L j − 1 + p o s j t − Δ t × g a p / Δ t 1 + g a p / Δ t (5) pos^t_j = \frac{pos^t_{j-1}-L_{j-1}+pos^{t-\Delta t}_j\times gap/ \Delta t}{1+gap/ \Delta t} \tag{5}*p**o**s**j**t*​=1+*g**a**p*/Δ*t**p**o**s**j*−1*t*​−*L**j*−1​+*p**o**s**j**t*−Δ*t*​×*g**a**p*/Δ*t*​(5)

v j t = ∣ ∣ p o s j t − p o s j t − Δ t ∣ ∣ Δ t (6) v^t_j = \frac{||pos^t_{j}-pos^{t-\Delta t}_{j}||}{ \Delta t} \tag{6}*v**j**t*=Δ*t*∣∣*p**o**s**j**t*−*p**o**s**j**t*−Δ*t*∣∣(6)

其中p o s j t , p o s j − 1 t pos^t_j,pos^t_{j-1}*p**o**s**j**t*,*p**o**s**j*−1*t*是t t*t*时刻车辆编号为j j*j*的位置和其前车位置。L j − 1 L_{j-1}*L**j*−1是前车长度，Δ t \Delta tΔ*t*是时间精度，g a p gap*g**a**p*是想要的时间间隔。v j t v^t_j*v**j**t*是t t*t*时刻汽车速度。

如果队列收到了一个入队请求，领航车会根据申请车的位置，规划路径来判断是否可以进行入队操作。如果入队申请被拒的话，车辆会继续寻找并保持单车驾驶模式；否则`PlatooningManager`会选择最合适的入队位置，如果需要的话，队列成员会调整自身速度，入队车辆会移动到入队位置完成入队操作。
![在这里插入图片描述](https://img-blog.csdnimg.cn/ca0673050f0d4b5f83589a75c35ae30b.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70#pic_center)

------

#### 2.2 Platooning Scenario Testing Design

下图是编队联合仿真测试场景片段，整个测试都使用了感知和定位算法，感知算法为`yolov5`，定位算法为`GNSS/IMU`融合算法。

![在这里插入图片描述](https://img-blog.csdnimg.cn/210a0e94132c461eaf035105ecbe2d01.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

如下图所示，这里有两个测试场景：

- 单车道编队，5辆汽车在同一车道行驶，为了测试队列稳定性，`当领航车突然改变车速时`，观察跟随车会不会继续保持想要的时间间隔。
- 协同编队，当其余车道上车辆申请入队时，领航车决定最佳的入队位置，这里使用了两种算法来选择最佳入队位置：一种是`heuristic-based`，另一种是`Genetic Fuzzy System`。

| Single lane platooning                                       | Cooperative Merge and join the platoon                       | Real-world human-driven vehicle speed profile                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![在这里插入图片描述](https://img-blog.csdnimg.cn/8dc432eace4a4c37b958c25958dade55.png) | ![在这里插入图片描述](https://img-blog.csdnimg.cn/07bab60c6b4b4c7bb1291eea308dee7f.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70) | ![在这里插入图片描述](https://img-blog.csdnimg.cn/6cd53d85ea5442b3a85642476399c6a5.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70) |
|                                                              |                                                              |                                                              |

------

#### 2.3 Evaluation Measurements

这里是一些评价指标：

- 安全性，自动驾驶中最重要的评价指标，使用了`碰撞时间(TTC)`，`平均碰撞时间(attc)`，`危险频率（碰撞时间小于警告时间）`，平均碰撞时间计算方法为：
  A T T C = ∑ t = 1 N x i t − x i − 1 t − l v i t − v i − 1 t N (7) ATTC= \frac{{\sum^{N}_{t=1}}\frac{x^t_i-x^t_{i-1}-l}{v^t_i-v^t_{i-1}}}{N} \tag{7}*A**T**T**C*=*N*∑*t*=1*N*​*v**i**t*​−*v**i*−1*t*​*x**i**t*​−*x**i*−1*t*​−*l*​​(7)
  其中，x i t x^t_i*x**i**t*​是t t*t*时刻车辆位置，x i − 1 t x^t_{i-1}*x**i*−1*t*​是前车位置，l l*l*是车辆长度，N N*N*是v i t < v i − 1 t v^t_i<v^t_{i-1}*v**i**t*​<*v**i*−1*t*​的仿真时间步数。
- 稳定性，使用时间间隔和加速度来表示；
- 效率，使用完成入队时间和加速度标准差来表示。

------

### 3. Result Analysis

#### 3.1 Single Lane Platooning

单车道入队测试结果为：

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![在这里插入图片描述](https://img-blog.csdnimg.cn/b6367019a1f847e28bb5ec502c09e683.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70) | ![在这里插入图片描述](https://img-blog.csdnimg.cn/5a232b84ef9a416e94a3b8fe031d4273.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70) |
|                                                              |                                                              |

------

#### 3.2 Cooperative Merge and Joint Platoon

协同入队测试结果为：

![在这里插入图片描述](https://img-blog.csdnimg.cn/a616ec1ebe8c47839ef26fbb4245b55b.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2d20cac6e79a4ef4a1e809eb4ae37841.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NnMTI5MDU0MDM2,size_16,color_FFFFFF,t_70)