- [网联V2X测试解决方案_Polelink北汇信息的博客-CSDN博客_v2x测试方案](https://blog.csdn.net/weixin_51954443/article/details/109494849?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-26-109494849.pc_agg_new_rank&utm_term=v2x协议栈&spm=1000.2123.3001.4430)

## V2X测试的挑战

V2X，顾名思义就是vehicle-to-everything，通过现代通信与网络技术，实现车与人、车、路、后台等信息交换共享，从而帮助汽车实现安全、舒适、节能、高效行驶。

由于涉及到车与周围环境的通信交互，V2X的应用面临各种各样的通信环境和交通场景的组合。在V2X研发测试验证过程中如果还大量采用道路测试方法，我们将面临测试里程和测试时间急剧增加所带来的巨大挑战。

北汇信息推出V2X测试解决方案支持在实验室环境下完成应用场景仿真和通信环境仿真测试，可以极大的加速V2X研发验证过程。

北汇信息作为[Vector](https://so.csdn.net/so/search?q=Vector&spm=1001.2101.3001.7020)、Rohde & Schwarz、IPG、PikeTec等国际知名企业的官方合作伙伴，同时也是IMT-2020（5G）推进组蜂窝车联（C-V2X）工作组成员，我们致力于在V2X领域积极开展LTE-V2X和5G-V2X的测试验证技术研究等工作，积极推动中国V2X的产业落地，为客户提供V2X成套测试系统及服务。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20201104172601273.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MTk1NDQ0Mw==,size_16,color_FFFFFF,t_70#pic_center)

## 测试解决方案覆盖

北汇信息作为国内一流测试服务商，联合Vector、Rohde & Schwarz、IPG、PikeTec等国际知名测试工具提供公司，为国内V2X的研发提供完备的测试解决方案。

**V2X测试解决方案主要覆盖：**

V2X功能测试解决方案

```
○ V2X（PC5）端到端应用场景的HIL测试
○ TBOX基站仿真测试
○ V2X应用算法MiL/SiL测试
○ V2X应用算法的代码测试
```

V2X一致性测试解决方案

```
○ ITS协议栈一致性测试
○ 接入层协议一致性测试
○ GCF一致性测试
```

V2X射频测试解决方案

场地测试解决方案

```
○ V2X数据采集系统
```

![V2X 开发V模型](https://img-blog.csdnimg.cn/20201104172908218.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MTk1NDQ0Mw==,size_16,color_FFFFFF,t_70#pic_center)

## V2X功能测试解决方案

**V2X（PC5）端到端应用场景的HIL测试**

针对V2X应用算法测试需求，北汇信息运用虚拟场景和射频通信的联合仿真技术，提供完整的端到端V2X应用场景的HIL测试系统。

通过该测试系统用户可以构建高保真的V2X应用场景，并通过射频仪表将虚拟测试场景中的各种交通参与者（车辆及路边设施）信息转换为V2X空口信号(Uu/PC5)输入给被测控制器（OBU/RSU）。这样用户在实验室环境下就可完成OBU或RSU的控制模型或算法的硬件在环测试。同时能够对V2X通信终端的射频性能进行信令测试，评估射频一致性。

**测试系统能力：**

- 满足C-V2X终端硬件在环测试
- 满足端到端全协议栈应用场景测试
- 支持不同国家ITS消息解析：CAM，DENM，Spat/MAP，IVI，BSM……DSMP（中国）
- 覆盖OBU/RSU硬件功能性测试、ITS协议一致性测试
- 支持多种总线CAN、LIN、MOST、FlexRay和车载以太网通信
- 针对Uu口，系统满足信令射频测试需求
- 针对PC5口，系统支持信令发射机测试，非信令接收机测试

**测试系统组要组件（主要针对PC5端口测试）：**

- 场景仿真软件：提供高保真V2X虚拟仿真场景、动态交通流仿真及高精度动力学仿真
- Vector CANoe.Car2x：提供ITS协议栈的网络层和应用层协议仿真
- Vector VT板卡：提供CAN、车载以太网口板卡、车载电源模拟，故障注入仿真、数字模拟信号仿真及监控；同时提供车辆虚拟ECU的模型仿真
- R&S CMW500：支持多达400个车辆或交通设施节点的PC5信号仿真，覆盖到接入层（PHY，MAC，PDCP，RLC）
- R&S SMBV100A/B：输出GNSS信号，用于被测OBU/ECU和仿真车辆之间的数据同步和导航，支持GPS, Galileo, GLONASS, BeiDou等多种制式

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020110417344641.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MTk1NDQ0Mw==,size_16,color_FFFFFF,t_70#pic_center)

北汇信息基于场景仿真软件提供T/CSAE 53-2017 16个应用场景测试库。具体包含仿真测试场景有：

- 前向碰撞预警
- 盲区预警/变道辅助
- 紧急制动预警
- 逆向超车碰撞预警
- 闯红灯预警
- 交叉路口碰撞预警
- 左转辅助
- 车内标牌
- 高优先级车辆让行
- 弱势交通参与者预警
- 车辆失控预警
- 异常车辆提醒
- 道路危险状况提示
- 基于信号灯的车速引导
- 限速预警
- 前方拥堵提醒

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201104173916925.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MTk1NDQ0Mw==,size_16,color_FFFFFF,t_70#pic_center)

## V2X应用算法MIL/SIL测试

北汇信息提供多种V2X 算法MIL/SIL测试组合来完成V2V/V2I应用测试，如前方碰撞警告，左转辅助，紧急车辆等场景仿真。

**理想化测试**

基于场景仿真软件中定制的V2X传感器模型，附加到虚拟仿真车辆模型和交通对象上。可自由定义的消息格式，可配置的通信范围和速率。不使用ETSI, WAVE, LTE-V协议栈，使用理想的无线电信道，但可人工添加的通信随机特性，即消息接收概率和消息延迟。

**带无线通信仿真的测试**

通过无线网络仿真软件NS-3中的WI-FI、LTE或者5GNR通信模型与场景仿真软件进行联合仿真，为C-V2X MIL/SIL测试提供更加精准的测试环境。便于用户进行各种无线网络条件下的V2X/V2I算法仿真测试。

**V2X应用算法软件测试**

针对V2X应用算法代码的软件测试，北汇信息提供静态测试和动态测试相关的测试工具，提供满足嵌入式产品的测试环境，覆盖嵌入式测试的各个环节。

**静态分析-Helix QAC/QAC++**

针对静态分析，我们提供美国Perforce公司的Helix QAC/QAC++代码静态测试工具，它可以自动化执行C/C++代码的静态分析，支持MISRA C/C++，AUTOSAR，CERT，CWE等代码规范。

**模型在环（MIl）测试-TPT**

针对基于模型开发MBD的开发方式，我们提供德国PikeTec公司的模型测试工具TPT,在代码测试之前要先针对模型进行模型在环（MIL）测试，验证模型是否符合设计的需求。

**软件代码SIL测试-VectorCAST**

针对软件代码SIL测试，我们提供德国Vector公司的代码动态测试工具VectorCAST，它提供全自动地测试执行和评估；基于需求的测试跟踪；增量式回归测试以及代码覆盖率分析等功能。

- 模型测试工具TPT
- 静态分析工具QAC
- 动态测试工具VectorCAST

## V2X一致性测试解决方案

**ITS协议栈一致性测试**

北汇信息基于德国Vector公司的CANoe和vTESTStudio提供ITS协议栈测试用例开发及执行平台，同时提供ITS协议一致性测试用例包。

ITS协议栈一致性测试开发参考中国信息通信研究院定义的ITS一致性测试规范。测试规范针对5种消息类型进行测试，主要测试各类消息是否可以正常发送接收，消息结构参数配置是否符合协议要求。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201104174130561.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MTk1NDQ0Mw==,size_16,color_FFFFFF,t_70#pic_center)
测试系统提供提供符合《基于LTE的车联网无线通信技术-网络层/消息层协议一致性测试方法》要求的可执行测试套件，具体测试项包括：

- DSM消息发送/接收测试
- DSA消息发送/接收测试
- BSM消息集协议一致性测试
- SPAT消息集协议一致性测试
- RSI消息集协议一致性测试
- RSM消息集协议一致性测试
- MAP消息集协议一致性测试

## 接入层一致性测试/GCF一致性测试

北汇信息基于R&S CMW500和SMBV100提供接入层一致性测试/GCF一致性测试系统。

测试方案系统组成如下：

- CMW500：针对PC5端口测试，CMW500可以模拟单辆车或者多辆汽车，覆盖到接入层（PHY，MAC，PDCP，RLC）。针对Uu端口测试，CMW500用于模拟基站。
- SMBV100A/B：输出GNSS信号，用于被测OBU/ECU和仿真车辆之间的数据同步和导航。

说明：PC5端口测试需要同时使用CMW500+SMBV100B，CMW500用于模拟配合车辆。Uu端口测试仅需要CMW500模拟基站。信号可以通过传导或者OTA的方式与被测OBU建立连接。3GPP 36.523-1 第24章定义了V2X接入层协议一致性测试用例。

![image.png](https://img-blog.csdnimg.cn/20201104174233980.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MTk1NDQ0Mw==,size_16,color_FFFFFF,t_70#pic_center)

## 射频测试方案

**系统构成CMW100/CMW500**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201104174259814.png#pic_center)

**系统能力：**

- 针对Uu端口（V2N，Vehicle to Network）满足非信令射频研发、生产、校准测试。
- 针对PC5（V2V，V2I，V2P）端口，满足非信令射频研发，生产，校准测试。

**支持的测试项**

- 频谱类

  ```
  ○ 带内杂散
  ○ 领道泄漏比（ACLR）
  ○ 频谱辐射模板
  ○ 频率误差
  ```

- 功率类

  ```
  ○ 功率动态范围
  ○ 功率监测
  ○ 发射功率
  ```

- 其他

  ```
  ○ 矢量幅度误差（EVM）
  ○ 矢量幅度误差 Vs. 子载波
  ○ 均衡器频谱平坦度
  ○ 幅度误差
  ○ 相位误差
  ○ IQ星座图
  ○ RB分配
  ```

## V2X场地测试解决方案

**V2X数据采集系统**

在V2X功能路试中，我们需要对车载OBU单元及相关控制器的数据进行采集，同时也需要对路边设施单元或者周围车辆V2X通信报文进行采集，这主要涉及到各个V2X通信单元的无线数据同步获取，数据存储，以及后续的V2X报文分析和回放。北汇信息基于在ADAS路试数据采集的成功经验，扩展了V2X路试数据采集系统。

V2X报文采集：采用LTE V2X BOX数据采集盒，可以同时接收路试车辆、路边单元以及周围车辆发出的V2X无线报文，并通过以太网传给ADAS Logger进行数据记录。

数据记录存储：ADAS Logger 是专门为车载数据记录的设计的高性能 PC，具有丰富接口类型，有 CAN、Ethernet、USB 等，依然有 RS232 接口用于 GPS 接收器。同时支持各个接口的数据同步。可扩展至 16TByte的存储空间，另外集成 Vector 的 VN8900 或 VN7572 板块，可支持 8 路 CAN和I/O信号。

数据分析：将Logger存储的数据导入到CANoe软件中可以完成对V2X报文，CAN报文的同步分析，用以分析实车V2X通信功能、性能。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201104175512602.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl81MTk1NDQ0Mw==,size_16,color_FFFFFF,t_70#pic_center)

北汇信息是德国Vector公司、德国Rohde&Schwarz（简称R&S）公司、德国IPG公司、德国PikeTec公司、美国Perforce公司等国际知名企业的官方合作伙伴，同时也是IMT-2020（5G）推进组蜂窝车联（C-V2X）工作组成员，我们致力与在V2X领域积极开展LTE-V2X和5G-V2X的测试验证技术研究等工作，积极推动中国V2X的产业落地，为客户提供V2X成套测试系统及服务。