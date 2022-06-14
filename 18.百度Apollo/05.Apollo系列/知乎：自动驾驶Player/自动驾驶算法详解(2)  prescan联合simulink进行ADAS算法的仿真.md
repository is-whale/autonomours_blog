- [自动驾驶算法详解(2) : prescan联合simulink进行ADAS算法的仿真 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/503994227)

**前言：**

看到了4年前的一篇笔记，prescan与simulink联合仿真来验证FCW算法，当时还是个萌新，prescan在仿真验证算法这块对我帮助很大。相比于carsim，prescan的场景配置更简单，而且与simulink接口配置方便，可以在仿真过程中更专注在ADAS算法本身。

所以将prescan与simulink联合仿真方法分享给大家，这个教程是当时一步一步截图记录的，十分详细的介绍了联合仿真过程，

**一、实验框架**

1、prescan进行场景搭建，包括道路、行车路径、前车、目标车、雷达、观察视角；

2、simulink中stateflow进行FCW状态机的搭建

3、simulink中各个子功能的验证

4、prescan与simulink的联合验证

**二、prescan建模**

**2.1、场景搭建**

![img](https://pic1.zhimg.com/80/v2-bce4e210a826f184b86c58fae84e7dc8_720w.jpg)

在区域1中选择相应的组件，拖动至区域2中，区域3中显示选中的组件的基本信息；

区域2中右键点击组件，可以对组件的参数进行设置

**2.2、车辆参数设置**

2.2.1 设置速度，起始距离

![img](https://pic2.zhimg.com/80/v2-a7af14e7c4f8a0919c1973b9f9c4ca55_720w.jpg)

2.2.2 开放动力学接口

![img](https://pic1.zhimg.com/80/v2-cad4f3d0b98f5555116a514b49558c44_720w.jpg)

2.2.3 开启碰撞检测

![img](https://pic3.zhimg.com/80/v2-8fe9aae90083c7095a785b302b39ecd6_720w.jpg)

在系统设置中打开碰撞检测：

![img](https://pic1.zhimg.com/80/v2-cb3c7a784e7a183aedff1d8f613daab0_720w.jpg)

**2.3、路径设置**

![img](https://pic2.zhimg.com/80/v2-e58e14bf85708a17646a9ae64f9c7e6d_720w.jpg)

**2.4、雷达传感器设置**

2.4.1 拖动雷达传感器到车辆

![img](https://pic3.zhimg.com/80/v2-f6b64b45a605216738f8b51f62e576f6_720w.jpg)

2.4.2 设置雷达参数

![img](https://pic4.zhimg.com/80/v2-f4b880ae4963505e130774fed17b7ee7_720w.jpg)

PS: 如果选择1束雷达波，则无法正确探测到前方目标，如果使用多束雷达波，则探测到的前方目标不能稳定存在，信号是脉冲的形式；如何使用**radar组件模拟毫米波雷达？**

**2.5 观察视角的设置**

![img](https://pic1.zhimg.com/80/v2-cd23ce2057ca83fa567bffb34896a190_720w.jpg)

将组件拖动至车辆上

**三 simulink中算法搭建**

**3.1 状态机搭建**

3.1.1 状态机

![img](https://pic2.zhimg.com/80/v2-977de7e0172679ed0f5b2e4e275fc7a9_720w.jpg)

3.1.2 输入与输出变量管理

新增：

![img](https://pic3.zhimg.com/80/v2-656c7b1e6f505791c4247f55caef6a82_720w.jpg)

chart > add inputs&outputs

管理：

![img](https://pic1.zhimg.com/80/v2-d51d437331de9215377e1967e8e0fccc_720w.jpg)

tools > model explorer

3.1.3 对输入变量的调用

![img](https://pic4.zhimg.com/80/v2-d377240b994ab96d61c33e5dd14fe367_720w.jpg)

输入变量为向量，跳转条件需要标量，通过增加输出变量进行观察，确认调用向量中正确的元素

**3.2 子功能的编写与验证**

![img](https://pic1.zhimg.com/80/v2-d5e0d70835d7b3e144e49324e2c9bcb8_720w.jpg)

FCW_ENABLE 模块

![img](https://pic3.zhimg.com/80/v2-53db767c35cd8809f51f9e2c2acadff2_720w.jpg)

TTC计算模块：

![img](https://pic3.zhimg.com/80/v2-2f0a53b232efca56e94f8a814faaa892_720w.jpg)

**以上各功能先进行验证后再连接**

**3.3 FCW算法模块的封装**

![img](https://pic1.zhimg.com/80/v2-461f4e82b731aa9da57bb9621c7602c4_720w.jpg)

留出需要prescan输入的接口：switchon、targetid、egov、relrance、relspeed

留出需要输出的接口：fcw_flag

**四、prescan与simulink联调**

**4.1 环境准备**

prescan中build与打开matlab：

![img](https://pic1.zhimg.com/80/v2-3a21da24048d192b73149b84ed38d1f0_720w.jpg)

每次build后simulink中重新刷新

![img](https://pic3.zhimg.com/80/v2-a98fe10d78144c50e26997e2a6042d36_720w.jpg)

**4.2 联合仿真**

![img](https://pic4.zhimg.com/80/v2-bfd29f756025b6a9073897c255f6cc8f_720w.jpg)

**4.3 运行仿真**

![img](https://pic2.zhimg.com/80/v2-4ffb380c0b1ac828d1c221fd6ef76fcd_720w.jpg)

**4.4 仿真结果**

![img](https://pic2.zhimg.com/80/v2-907f581264ea99dfea1da69d6a6aba75_720w.jpg)

**4.5 附件**

**需要这个demo完整工程文件的，可以私信我~**

![img](https://pic1.zhimg.com/80/v2-5426d9b93ba81441959b2c8fba2770fc_720w.jpg)