- [华为MDC软件架构 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/367834493)

**华为MDC软件架构**

平台软件零层逻辑架构如下图，由基础层、功能层、应用层和服务层组成。

![img](https://pic1.zhimg.com/80/v2-1cc77e990890b10a69e6eeafc7fcc478_720w.jpg)

零层逻辑架构

从平台软件一层逻辑架构可以看出，MDC用了华为自研的越影操作系统、兼容Autosar标准的软件中间件，提供完整的工具链，并且考虑了功能安全和信息安全。

![img](https://pic1.zhimg.com/80/v2-94a40fcce2c7cef59aa7867071eb6750_720w.jpg)

一层逻辑架构

在2019年第四季度，MDC使用基于鲲鹏920s和升腾310硬件的第一代软件架构。MCU软件用于诊断和健康监控等，鲲鹏920软件分为自动驾驶功能域和数据处理域，感知软件则放在了具备AI超强功能的升腾310。

![img](https://pic4.zhimg.com/80/v2-53674fa3602219cf576df5a95474bf7b_720w.jpg)

第一代版本软件部署架构示意图

在2020年第四季度，MDC使用基于升腾610SoC的第二代软件架构。

![img](https://pic4.zhimg.com/80/v2-28633e01b125b76f053edfa341aea5df_720w.jpg)

第二代版本软件示意图

软件架构支持超声波、毫米波雷达，GPS和惯量传感器，激光雷达和摄像头的灵活配置。

![img](https://pic2.zhimg.com/80/v2-a1094b754df4272e180d43930ad22035_720w.jpg)

华为自研越影OS接口设计兼容Linux，并且有实时安全通信设计。

![img](https://pic1.zhimg.com/80/v2-0f29f50d6714582e6d581bc40532d670_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-88f817e27c78e3ead12d52a25435707c_720w.jpg)

越影OS

MDC的功能安全整体目标为最严格的ASILD，支持fail operational。

![img](https://pic4.zhimg.com/80/v2-10aab8d8bdaa48896e35326d562eabc3_720w.jpg)

MDC内部模块的功能安全ASIL分解如下，以满足整体ASIL D的目标。

![img](https://pic3.zhimg.com/80/v2-0d7c9ed41f91ee73299efe609b719f9a_720w.jpg)

ASIL分解

MDC同时考虑了信息安全，MDC和其他的部件关系如下，包括7类边界。

![img](https://pic2.zhimg.com/80/v2-bd99d93a4fb450786d9d88ff0daffeb1_720w.jpg)

这会造成MDC的关键资产可能会收到攻击，包括接口资产、数据资产、软件/固件资产和硬件资产。

![img](https://pic2.zhimg.com/80/v2-b8ec9f566c0cc9ea7b071c9661a3eb81_720w.jpg)

MDC对各种威胁都有相应的解决方案。

![img](https://pic3.zhimg.com/80/v2-4de7e71ca6ce3df5e96a0358976667d2_720w.jpg)

接口威胁分析和解决方案

![img](https://pic4.zhimg.com/80/v2-16a9b7b64bcd7fd4f365fec4183ec8cf_720w.jpg)

通信威胁分析和解决方案

![img](https://pic4.zhimg.com/80/v2-9e0fecbfe0341b6602782de6ca426bcf_720w.jpg)

软件和固件威胁分析和解决方案

![img](https://pic4.zhimg.com/80/v2-ba20c887083fa80c34edb2da88cb82bb_720w.jpg)

硬件威胁分析和解决方案

华为还有完整的测试平台和工具链，为MDC的开发提供了全栈解决方案。