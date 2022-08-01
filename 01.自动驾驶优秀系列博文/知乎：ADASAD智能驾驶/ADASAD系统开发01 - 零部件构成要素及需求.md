- [ADAS/AD系统开发01 - 零部件构成要素及需求 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/43109515)

本文属于ADAS控制器开发系列。以智能前视摄像头模块为基础。

## **一、常用缩写：**

为了方便描述，以后对下列名称用行业内通用缩写代替：

系统构成要素 - Building Blocks - BB

产品开发文档 - Product Development Document - PDD

系统 - System - SYS

软件 - Software - SW

算法 - Algorithm - Algo

硬件 - Hardware - HW

机械 - Mechanics - MECH

制造 - Manufacture - MFG

验证 - Validation - VAL

匹配 - Application - APP

标定 - Calibration - CAL

## **二、Building Block 列表：**

Building Block, 顾名思义是积木、砖块的意思，在这里主要指控制器模块这个产品的系统构成要素。BB主要是在产品系统层面进行划分，无视了实际开发实施中按照学科分类的习惯（SYS、SW、HW、APP、MFG、Algo）。

ADAS/AD的产品一般可以划分出30-60个BB模块，每个BB都会对应一个PDD用来描述开发需求。现以智能前视摄像头模块（IFC）为例，列举ADAS ECU中常见的各个BB：

1. System Architecture Design 系统架构设计
2. Hardware Design 硬件设计
3. Host（MCU） Software Architecture Design 主芯片的软件架构设计（硬件一般以MCU单片机为主）
4. SoC Software Architecture Design 其他片上系统芯片的软件架构设计
5. Functional Safety 功能安全（基于ISO26262标准）
6. Cyber Security 网络安全
7. Signal Coordinate Systems 坐标系系统（传感器、控制器、车辆及大地坐标系的转换）
8. Configuration Strategy 配置策略（产品的各种系统配置参数）
9. NVM&KAM 非易失存储器（Non-Volatile Memory）及磨损修正系数存储管理员（Keep-Alive Memory Manager）
10. Bootloader & Reprogramming Bootloader及烧录
11. Power Mode Manager 电源管理模块
12. Safety Protection Module 安全保护模块（产品自保护、安全机制）
13. Manufacturing 制造（一般指向ERP生产系统导入的相关工程信息）
14. Packaging & Mounting Guideline 包装与安装指南
15. Inter Diagnostic 内部诊断
16. CAN Diagnostic 基于CAN通信的诊断（基于UDS协议的外部诊断）
17. V-CAN Communication 车辆CAN的通信
18. P-CAN Communication 私有CAN的通信
19. SPI Communication 板级SPI通信（芯片间的串行通信）
20. I2C Communication I2C通信
21. Ethernet Communication 以太网通信
22. Inter Processor Communication 内部芯片间通信
23. Factory（EOL）/Service/Online（Auto） Alignment 工厂（下线）/服务站/在线（自动）校准（一般指智能前视摄像头模块或者毫米波雷达模块的传感器外参校准）
24. CCP & XCP CCP标定协议及XCP标定协议
25. Features 各种功能点（基于Simulink模型搭建，如ACC/AEB/LKA/TJA等功能）
26. Feature Calibration 功能标定
27. Feature Interface 功能接口（模型参数与嵌入式软件参数的接口mapping信息）
28. Debug Interface & I-CAN & Video Output 调试接口、调试CAN及各种视频输出接口（基于LVDS/CVBS/USB等接口）
29. Hardware Interface 硬件接口
30. HMI 人机交互界面（Human-Machine Interface，显示信息）
31. eHorizon & HD-Map 电子地平线及高精地图
32. Location 定位
33. Camera Module （Lens&Imager） 摄像头模组（镜头及图像传感器）
34. Camera Blockage Detection Algorithm 摄像头遮挡探测算法
35. Radar Blockage Detection Algorithm 毫米波雷达遮挡探测算法
36. Lane/Objects Detection Algorithm 车道线/目标检测算法（基于视觉）
37. Object Detection Algorithm 目标检测算法（基于雷达或者激光雷达）
38. Object Fusion Algorithm 目标融合算法（融合视觉/毫米波雷达目标）
39. Vehicle State Estimation Algorithm 车辆状态估计算法
40. EyeQ3 API Mobileye EyeQ3芯片API
41. EyeQ3 VFP EyeQ3芯片的视觉融合处理器
42. EyeQ3 Inter Processor Communication EyeQ3芯片内部处理器通信
43. EyeQ3 CAN Communication EyeQ3的CAN通信
44. EyeQ3 VFP Ethernet Communication EyeQ3的VFP处理器的以太网通信
45. System Integration Test 系统集成测试（主要是测试方案、测试计划、测试报告）
46. System Test 系统测试（包括台架测试和实车测试，需要编写CAPL脚本等工作）

综上可以看出，整个BB大致可分为架构、功能安全、网络安全、坐标系系统、电源管理、存储管理、配置管理、安全保护、刷写、通信、诊断、接口、算法、制造、包装&安装、高精地图、定位、EyeQ芯片等模块。

## **三、Building Blocks的意义：**

BB具有非常强的项目实战意义，可以节省大量的设计开发时间。每当一个新项目的需求来的时候，系统工程师首先会关注新项目可以Reuse（复用）哪些原有的BB，以及对应的产品开发文档；然后，对于无法完全沿用的BB，是否具有借鉴意义，在一定程度的修改后，能够满足新项目的要求；最后，对于项目库中无法Cover（覆盖）的需求，再设计全新的BB，以满足开发需要。经过以上的过程，就形成了新项目（新产品）的baseline。

## **四、产品需求类型：**

1. Component Functional Requirement 零部件功能需求（主要指芯片）
2. Electrical Requirement 电气需求
3. Mechanical Requirement 机械需求
4. Software Requirement 软件需求
5. System Test Requirement 系统测试需求
6. Environment Requirement 环境需求
7. Recyclability / Substance of Concern Requirement 再循环利用及关注物需求
8. Validation Requirement 验证需求
9. Manufacturing Requirement 制造需求
10. Logistic Requirement 逻辑需求
11. Reliability Requirement 可靠性需求
12. Quality Requirement 质量需求
13. Serviceability Requirement 可维护性需求
14. Functional Safety Requirement 功能安全需求