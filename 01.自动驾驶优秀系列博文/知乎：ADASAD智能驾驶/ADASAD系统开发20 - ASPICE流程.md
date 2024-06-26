- [ADAS/AD系统开发20 - ASPICE流程 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/50969672)

本文属于ADAS控制器开发系列。以智能前视摄像头模块为基础。

**前言**

相信每个从事汽车电子开发的人都会有这样的心路历程：1.刚毕业时，懵懵懂懂的进入公司，跟着公司的培训走，了解自己岗位的内容，以及与其他岗位的交互，还要熟悉V模型开发流程；2.工作几年后，睁开眼睛看外部的世界，例如跟从事IT行业的同学们聊聊，跟转行做医疗器械的同学们扯扯，突然会想一件事情，为什么汽车电子的开发会是这样一种形态呢？都涉及到系统和软件的开发，但是组织形式和开发形式却又如此的不一样？是什么东西在指导并搭建了这样一种特定的开发组织形式呢（即开发流程）？

具体再提几个深入一点的问题：

- 为什么汽车电子行业的开发要有系统、软件、硬件、结构（机械）、匹配、集成、测试、验证、确认、制造、质量管理、工程项目管理、产线项目管理、产品管理、风险管理等复杂的开发人员设置？
- 为什么在汽车电子相关的几个供应商和主机厂跳来跳去，发现公司的项目组织形式和部门组织形式都大差不差，都长成某种特定的样子呢？
- 为什么汽车行业那么多搞开发的不会写代码？不会写代码也能特么叫开发人员？？
- 为什么汽车电子开发要用V-model流程进行开发？

上述一切的答案就在于ISO15504 - Automotive Software Process Improvement and Capability dEtermination（Automotive SPICE，软件流程改进和能力测定的汽车行业定制化版本）。这是一个由欧洲车企发起的车载软件开发的流程标准，用于欧洲主机厂对供应商进行软件流程能力评估。包括：奥迪、宝马、奔驰、菲亚特、福特、捷豹路虎、保时捷、大众、沃尔沃等。

这个流程集合的意义在于，假如某个车企的软件是按照这个流程开发出来的，就认为符合“车规”，软件的功能安全等级能够满足车辆安全要求。

**正文**

A-SPICE 3.0 总览：

![img](https://pic2.zhimg.com/80/v2-cab4ebecd8aeb8f875c9d1ba8eb06c5d_720w.jpg)

**具体工程开发流程步骤（ENG）**

![img](https://pic4.zhimg.com/80/v2-b7e67585532482075c211e445a870c47_720w.jpg)

现在知道汽车电子零部件供应商里的**系统工程师**角色来源了吧？

看SYS.1/SYS.2/SYS.3/SYS.4/SYS.5的流程定义，需要这么一个角色来cover对应着五个环节的任务。分别是：需求获取、系统需求分析、系统架构设计、系统集成和集成测试、系统确认测试。

按照开发步骤详细讲讲工程开发细节：

**需求获取：**从客户获取客户需求，并确认需求被正确理解；

**系统需求分析：**将已定义的客户需求转换为一组系统需求，用以指导系统设计；

**过程成果：**

a. 已经定义了系统需求；

b. 已经对系统需求进行了分类和分析以获取正确性和可验证性；

c. 已经分析了系统需求对运行环境的影响；

d. 已经定义了实现系统需求的优先级；

e. 系统需求能够根据需要更新；

f. 已经在利益相关者（客户等）需求和系统需求之间建立了一致性和双向可追溯性；

g. 利益相关者需求已经根据成本，进度和技术影响进行了评估；

h. 系统需求已经传达给受影响的各方并且达成了一致。

**输出物包括以下几种：**

需求分析报告（RAR, Requirement Analysis Report）,比如诊断、CAN通信、XCP、电源、KAM存储机制等模块的RAR分析报告。

系统需求说明书（SYSRS, System Requirement Specification），里面需要定义好功能需求和非功能需求。系统说明书的结构可是按照项目相关集分组，也可按照项目逻辑顺序进行排序，也可根据项目相关标准进行分类，还可根据客户要求来分类。

接口需求说明书（IRS，Interface Requirement Specification），定义各个系统模块之间的接口。例如：两个产品之间、过程之间或者过程任务之间的关联；双方共同遵守的准则及格式；关键时间依赖性或前后顺序；描述各个系统组件之间的物理接口，如总线接口（CAN、LIN）、收发器（类型、制造厂商）、附加接口（IEEE、ISO、USB等）、数字接口（PWM）和模拟接口等；识别软件与软件组件之间的接口，如进程间通信机制、总线通信机制等。

验证标准（Validation Specification）。具体内容包括：1. 每条需求都可验证或评估；2. 验证准则定义了验证需求时所遵循的定性及定量的准则；3. 验证准则标明进行需求验证时所遵循的已达成共识的约束条件。

审查记录（RR，Review Record）：需提供评审的上下文信息（内容、出席评审的人员列表、评审状态）、覆盖信息（checklist、review标准、需求、标准符合性）、检查发现（不一致性、改进建议）、记录信息（评审准备完成状态、评审准备所花时间、评审所花时间、评审人员和角色的评审意见）、识别所需的纠正信息（风险识别、偏差list、解决问题所要求的行动及任务、纠正措施的负责人、已识别问题的状态及目标关闭日期）

跟踪记录（TR，Traceability Record）：跟踪所有需求（客户需求和内部需求）、识别生命周期中的产品&需求之间的映射关系、需求和工作产品之间的连接关系（例如：需求-设计-代码-测试-交付）、在生命周期各阶段提供需求到相关工作产品之间的正向和反向映射

沟通记录（Communication Record）：信件、传真、电子邮件、语音记录、电报；

变更控制记录（Change Control Record）, 记录变更请求，生成基线化产品（baseline product）。具体包括：采用控制机制来控制正式项目的基线化产品（baseline）、变更请求记录（识别受变更影响的系统即文档、识别变更请求、识别变更的负责团队、识别变更的状态）、与相关客户请求及内部变更请求建立连接关系、适当的批准、对重复的请求进行识别和分组

**系统架构设计：**建立系统架构，确定哪些需求分配给哪些系统元素，并根据定义的标准评估所设计的系统架构。 具体的：

a. 提供所有系统的设计；

b. 描述系统元素之间的相互关系；

c. 描述系统元素与软件之间的相互关系；

d. 详细说明每个必需系统元素的设计，需要考虑到以下内容：内存和容量的需求、硬件接口需求、用户接口需求、外部系统接口需求、性能需求、指令结构、安全及数据保护特性、系统参数设定、人工操作、可重用组件等

e. 系统元素与需求之间的映射关系

f. 描述系统组件运行模式（启动、停止、睡眠模式、诊断模式等）

g. 描述在不同运行模式下各个系统组件之间依赖关系；

h. 描述系统和系统组件的动态行为。

**过程可能产生的成果：**

a. 已经定义了系统架构设计，并已经标识了系统元素；

b. 系统需求已经被分配给了系统元素；

c. 系统元素的每个接口已经定义；

d. 已经定义了系统的动态行为目标；

e. 在系统需求和系统架构设计之间已经建立了一致性和双向可追溯性；

f. 系统架构设计已经传达给受影响的各方并且达成了一致。

**软件需求分析Software Requirements Analysis：**将系统需求的相关部分转换为一组软件需求

1. 指定软件需求：使用系统需求、系统架构及以上两者的变更需求来确定所需软件的功能和特性。在软件需求规范中指定功能性和非功能性软件需求。只有在软件开发中，系统需求和系统架构才会设计到一个给定的操作环境。
2. 结构化软件需求：结构化可以按照项目相关的集群分组，或者按照逻辑排序，或者按照相关标准进行分类，或者为干系人需求设置优先级。
3. 分析软件需求：分析指定的软件需求包括他们的相互依赖关系、确保正确性、技术可行性、可验证性和支持风险识别。分析成本影响，进度和技术的影响。
4. 分析对操作环境的影响：分析系统元素接口和操作环境对软件需求的影响；
5. 开发验证标准：为每个软件需求开发验证标准，以便于每条需求验证可以定性和定量的度量。
6. 建立双向可追溯性：建立系统需求和软件需求之间的双向可追溯性。建立系统架构和软件需求之间的双向可追溯性。
7. 确保一致性：确保系统需求和软件需求之间的一致性。保证系统架构和软件需求之间的一致性要求。
8. 沟通已确认的软件需求：沟通已确认的软件需求并为所有相关方更新这些软件需求。

**输出物包括：**

1. 沟通记录；
2. 审查记录；
3. 变更控制记录；
4. 追溯性记录；
5. 分析报告Analysis Record：分析的内容、分析人、所采用的分析准则（选择的准则或采用的优先级计划、决策准则、质量准则）、记录结果（决定或选择的内容、选择的原因、做出的假定、潜在风险）、正确性分析的方面（完整性、可理解性、可测试性、可验证性、可行性、有效性、一致性、内容的充分性）
6. 接口需求规格说明书（IRS）：更新细化
7. 软件需求说明书（Software Requirement Specification）：识别适用的标准、识别软件架构考虑及约束条件、识别必需的软件元素、识别软件元素之间的关联关系、考虑给出以下信息（必需的软件性能特性、必需的软件接口、必需的安全特性、数据库设计需求、必需的错误处理及属性恢复机制、必需的资源消耗特性）
8. 验证标准

**软件架构设计Software Architectural Design**：建立一个架构设计和确定哪些软件需求分配给软件的哪些元素，并根据定义的标准评估软件架构设计。

**过程成果：**

1. 定义了软件体系结构设计，确定了软件的元素；
2. 软件需求分配给软件的元素；
3. 定义了每个软件元素的接口；
4. 软件元素的动态行为和资源消耗目标已定义；
5. 建立软件需求和软件架构设计之间的一致性和双向可追溯性；
6. 软件架构设计被所有受影响的各方达成一致并已沟通。

具体步骤：

1. 软件架构设计：指定软件要素和相关方面的功能和非功能软件需求；软件在适当的层级分解为元素，并在详细设计中描述组件（所谓组件，指软件架构设计的最底层的元素）；
2. 分配软件需求：分配所有软件需求到软件架构设计元素；
3. 定义软件元素接口：识别、开发和文档化软件元素之间的接口；
4. 描述动态行为：参照系统动态行为，评估和文档化软件元素间的时序和动态交互行为。（动态行为是由操作模式确定的，如启动、关闭、正常模式、校准、诊断等等；进程和进程内部通信、任务、线程、时间片、中断等。另外，在评估动态行为时，目标平台和潜在载荷应被考虑）；
5. 定义资源消耗目标：在软件架构设计的适当层级，为相关元素定义并记录所有软件组件的资源消耗目标。（资源消耗通常被明确为资源，诸如内存ROM、RAM、外部/内部EEPROM或Flash数据，CPU负载等）
6. 评估可替代的软件架构；
7. 双向可追溯性（需求与架构之间）
8. 确保一致性
9. 沟通已确定的软件架构设计

输出物：

1. 软件架构设计：包括内容有：

a. 软件架构整体描述；

b. 包含任务结构的运行系统描述；

c. 确定任务与进程之间的通信；

d. 识别必需的软件元素；

e. 识别自主开发及供应方提供的代码；

f. 识别软件元素之间的关联及依赖关系；

g. 确定数据存储及灾备方案；

h. 描述不同模型系列或配置如何衍生出产品变体；

i. 描述软件的动态行为（启动、关闭、软件升级、错误处理、恢复）；

j. 确定数据存储位置及数据损坏的预防办法；

k. 描述哪些数据是在什么情况下是持续存在的；

l. 还要充分考虑以下内容（软件必需的性能特性、软件必需的接口、软件必需的安全特性、数据库设计需求）

2. 接口需求规格说明书

3. 跟踪记录

4. 审查需求

5. 沟通记录

**软件详细设计和单元实现Software Detailed Design and Unit Construction：**步骤几乎以以上步骤完全一致..

**软件单元验证Software Unit Verification：**软件单元测试过程的目的是验证软件单元，证明软件单元符合软件详细设计和非功能性软件需求。

过程成果：

1. 指定了包括回归策略在内的软件单元验证策略；包括软件单元发生变更时重新验证的回归策略。验证策略应定义如何证明软件单元符合软件详细设计和非功能性需求。单元验证的技术可能包括静态/动态分析、代码审查、单元测试等。
2. 根据软件单元验证策略开发软件单元验证标准，该策略适用于证明软件单元提供符合软件详细设计和非功能性软件需求；单元验证标准可能包括单元测试用例，单元测试数据，静态验证，覆盖目标和编码标准；单元测试规范可以实现诸如作为自动测试台中的脚本。
3. 根据软件单元验证策略和软件单元验证标准对软件单元进行验证，并记录结果；其中，静态验证可能包括静态分析、代码审查、针对编码标准和指南的检查以及其他技术。
4. 在软件单元之间建立一致性和双向可追溯性，验证标准和验证结果；
5. 汇总单元验证结果，并传达给所有相关方。

输出物包括：

1. **测试规范说明书 Test Specification：**包括测试设计规格书、测试用例规格书、测试过程规格书、识别回归测试的测试用例；对于系统集成测试，要识别必需的系统要素，例如硬件要素、接线要素、参数设定和数据库等；识别系统元素集成必要的序列或排序
2. **测试计划Test Plan：**分级的测试计划、测试策略（黑盒/白盒测试、系统边界测试、回归测试策略等）；如有必要，编制综合测试计划
3. **验证结果及测试报告Verification Result and Test Report：**验证checklist、通过的项、失败的项、待验证的项、发现的问题issue、风险分析、解决方案、结论、签名确认。测试报告按照要求，形成测试日志分级、形成异常报告、形成测试报告分级。
4. **分析报告Analysis Reprot**
5. **三记录（沟通记录、审查记录、跟踪记录）**

**软件集成和集成测试Software Integration and Integration Test：**将软件单元集成到更大的软件项目中，直到形成与软件架构设计一致的完整集成软件，并确保软件项目是经过测试的，可以证明包括软件单元之间和软件项目之间的接口在内的集成软件项目符合软件架构设计。

步骤：

1. 开发软件集成策略
2. 开发包括回归测试策略在内的软件集成测试策略
3. 制定软件集成测试规范。软件集成测试用例可能关注：软件项目间的正确数据流、软件项目间的数据流的及时性和时间依赖性、使用界面对所有软件项目数据的正确解释、软件项目间的动态交互、符合接口的资源消耗目标
4. 集成软件单元和软件项
5. 选择测试用例，要有足够的覆盖度
6. 执行软件集成测试
7. 老三样（双向可追溯、确保一致性、总结和交流经验）

输出物：

1. 软件项，两大块，一个是集成的软件，一个是文档。集成的软件可分为：源代码、软件元素、可执行代码、配置文件；文档包括：描述和识别源代码、描述和识别软件元素、描述和识别配置文件、描述和识别可执行代码、描述软件生命周期状态、描述归档和发布标准、描述软件单元编译、描述软件组件的构建
2. 集成的软件：多个软件组件的集合。这里的软件一般是针对某一特定ECU配置的一组可执行文件以及有关的文档和数据。
3. 测试老三样（说明书、计划、报告）
4. 记录老三样（沟通、审查、追溯）
5. 构建列表Build list: 识别软件应用系统的聚合、识别所需的系统元素（参数设定、宏程序库、基本数据、作业控制语言等）、识别软件编译时必需的顺序序列、识别输入输出资源库。

**软件确认测试Software Qualification Test：**确保集成的软件已经过了测试并满足软件需求。与上一步几乎相同。

**系统集成和集成测试System Integration and Integration Test：**集成系统项以生成集成系统，符合系统架构设计并确保系统项被测试，用以证明集成的系统项与系统架构设计的一致性，包括系统项之间的接口。

1. 开发包含回归测试策略的系统集成测试策略；
2. 开发系统集成测试规格说明书，包含：根据系统集成测试策略的每一个系统项集成阶段的测试用例。有四个需要注意的点：a. 接口描述系统元素和系统集成测试用例的输入关系；b. 符合架构设计：指定的集成测试能够适时证明，系统项间的接口满足系统架构设计提供的规范。c. 系统集成测试用例应关注：系统项间正确的信号流、系统项间信号流的及时性和时序、使用一个接口信号的所有系统项的正确解释、系统之间的动态交互关系；d. 系统集成测试可能支持使用仿真环境（硬件在环仿真、车辆网络仿真、数字样机）。
3. 集成系统项；按系统集成策略，整合系统项，形成集成系统。系统集成可以逐步执行集成系统项（例如，作为原型硬件的硬件元素、外围设备、传感器、执行器、结构和已集成的软件），生产出与系统架构设计保持一致的集成的系统。
4. 选择测试用例：从系统集成测试规范说明书中选择测试用例。有足够覆盖率。
5. 执行系统集成测试
6. 老三样（双向可追溯、确保一致性、总结和交流经验）

**系统确认测试System Qualification Test：**确保集成的系统已经测试，为符合系统需求和系统准备交付提供依据。与上述步骤大同小异，略。

## **具体支持流程步骤（SUP）**

**一、质量保证流程 Quality Assurance（SUP1）**

目的：为工作产品和流程严格按照预定义的规定和计划提供独立的、客观的保证，并解决和预防不符合性的情况。

1. 开发项目质量保证策略；主要是保证工作产品和质量保证在项目层面独立、客观且无利益冲突地执行。所谓独立性，可能是财务或组织架构的独立。质量保证可能协调并利用其它流程的成果，比如核查、验证、联合评审、审计与问题管理。流程质量保证包括流程评估和审计，问题分析，定期检查的方法、工具、文档和流程定义的坚持，报告和经验教训，为未来的项目改善流程。工作产品质量保证包括评审、问题分析、报告和经验教训，来改善未来的工作产品。
2. 确保工作产品质量；相关工作产品的需求可能包括适当标准带来的需求。被发现的不符合的工作产品会进入问题解决流程（SUP9），并被归档、分析、解决和追踪以关闭问题并预防。
3. 保证流程活动的质量；按照质量保证策略和项目日程表执行流程活动质量保证，以确保活动契合既定目标并把结果归档。相关过程项目标可包括从适当标准带来的目标；在流程定义或实施过程中发现的问题可进入流程改善流程（PIM3），以描述、记录、分析、解决和追踪，最终关闭问题并预防。
4. 总结和沟通质量保证活动和结果；根据质量保证策略，定期给相关责任人报告性能、偏差和质量保证活动的趋势以获取信息和行动。
5. 确保不符合项的解决；应当就过程和产品的质量保证活动中发现的不一致和偏差进行分析、追踪、纠正并预防的工作。
6. 实施质量问题升级机制；根据质量保证策略建立一个渐进式管理机制，以确保可以将问题升至合适的质量保证管理等级和其它利益攸关方来解决问题。

**输出物清单：**

1. 质量计划：a. 质量目标；b. 定义保证质量所需的活动任务; c. 参考相关工作产品; d. 质量评估/保证的方法； e. 参考法规要求、标准及客户需求；f. 识别预期的质量标准；g. 为定义的生命周期和计划的相关活动指定监控时间表及质量检查点; h. 以达到预期质量为目标设立时间表；i. 实现目标的方法：需要执行的任务、任务负责人、需要执行的审计、资源承诺；j. 识别工作产品和过程任务的质量标准。k. 指定采取纠正措施前允许的门限/耐受度；l. 定义质量度量和基准数据；m. 定义质量记录采集机制和采集时机；n. 指定质量报告对过低质量所影响过程的反馈机制; o. 由质量责任机构/部门的批准；p. 定义质量保证（QA）的独立性；q. 确定升级机会和渠道; r. 定义客户与供应商质量保证之间的协作。
2. 质量记录；定义需要保罗的信息；定义产生信息的任务/活动/过程；定义数据收集日期；定义相关数据来源；识别相关质量准则；使用信息识别相关的度量；识别创建记录或记录需要满足的、需要遵循的任何需求。
3. 审查记录：略
4. 问题记录：识别问题报送人的姓名与相关联系细节；识别负责修复问题的组织/人员；包含问题描述；识别问题的分类（严重度、紧急度、关联性等）；识别已报告问题的状态；识别问题修复的目标发布版本；识别问题的预期关闭日期；识别问题关闭准则；识别问题复验活动。
5. 纠正措施记录：识别初始问题；识别行动项的负责人；定义解决方案（为解决问题而采取的一系列行动）；识别问题发现日期及期望关闭日期；包含状态指示器；标明后续的审核活动。
6. 质量标准。定义对质量的期望。建立工作产品充分程度的准则（必需元素、预期的完整性、精确度）；识别构成已定义任务的完整性的内容；建立生命周期变迁准则，以及每个已定义过程/活动的准入准出条件；建立预期的性能属性；建立产品可靠性属性。

**二、配置管理 Configuration Management（SUP8）**

配置管理流程，是为了建立和维护所有流程或项目工作产品的完整性，并提供给相关干系人。

1. 开发配置管理策略，包括： 职责和资源、工具和存储库、对配置项及其命名规范的识别、权限、配置项的历史和基线（需要/可选的基线；命名规范；分支方法、合并以及建立基线；发布和批准基线）。配置管理策略通常为了配合商务搞的不同版本的产品（Variants变种）配置管理。
2. 识别配置项；软件开发的配置项通常是：配置管理计划；需求文档、架构和设计文档；软件开发环境；软件开发计划；供应商协议；质量保证计划；软件单元代码和文档；应用参数；测试用例和测试结果，评审记录；编译清单，集成报告；用户手册。硬件和结构也可以有配置项，例如硬件布局、图纸、电路板、物料清单等。
3. 建立分支管理策略。指定分支管理策略，适用于使用相同源码基线进行并行开发。分支管理策略列出了关于何种情况下允许分支，是否需要认证，分支如何合并，需要怎样处理以确认所有变更在未央及其他变更和原生软件的情况下相容地集成到一起。
4. 控制修改和发布。根据配置管理策略建立配置项控制机制，并使用此机制控制修改和发布。
5. 建立基线（baseline）。根据配置管理策略建立内部和外部交付基线。
6. 报告配置状态。记录并报告配置项状态，以便于支持项目管理和其他相关流程。
7. 核实配置项信息。例如评审。
8. 管理配置项和基线的存储。

**输出物：**

1. 配置项：包含模块、子系统、函数库、测试用例、编译器、数据、文档、物理介质和外部接口。除此之外，配置项还要有版本维护相关信息。一般建立配置项表格时，需要包含配置项类型、相关配置管理库/文件/系统、责任人、置于配置管理控制下的日期、状态信息（例如：开发阶段、已打基线、已发布）、与下一层配置项的关联关系、变更控制记录的识别、变更历史的识别
2. 处理和存储指南：定义以下任务来实现产品的处理和存储：提供原版代码副本及文档、灾难恢复方案、登记关键安全性问题；提供描述来指导如何存储产品：所需存储环境、使用的保护媒介、所需包装材料、存储项、评估存储产品；提供恢复指南
3. 配置管理计划：定义或引用流程来控制配置项的变更；定义度量标准来确定配置管理活动的状态；定义配置管理审计标准；由配置管理职能部门批准；确定配置库工具或机制；含有控制项历史及状态的管理记录及状态报告；为配置管理库指定位置及访问机制；指定存储、处理及交付机制。
4. 恢复计划：识别恢复项，如执行恢复的规程和方法；恢复的时间表；关键的依赖关系；恢复所需的资源；备份维护列表；负责恢复的人员及分配的角色；所需特殊材料；所需工作产品；所需设备资源；所需文档；备份的存储位置；恢复的通知人联系方式；验证步骤；恢复的成本估算。
5. 基线：标识一致且完整的一条或一组工作产品和工件的状态；下一个过程阶段的基础；唯一的且不能更改的。
6. 配置管理记录（工作产品或工作项的修改状态；识别配置项；识别要执行的活动，例如备份、存储、归档和交付配置项；维持产品一致性）
7. 变更历史（变更描述、变更对象的版本信息、变更日期、变更发起人信息、变更控制记录信息）
8. 配置管理系统

**三、解决问题管理 Problem Resolution Management**

1. **总览**

解决问题管理流程，是为了确保所有发现的问题都被识别、分析、管理、受控知道解决。

整个流程会有一些成果产生：

- 制定问题管理策略；
- 记录、唯一标识并且分类问题；
- 对问题进行分析和评估，确定可接受的解决方案；
- 执行解决问题措施；
- 跟踪问题直至关闭；
- 所有问题状态和趋势可知。

**2. 流程步骤：**

a. 制定解决问题管理策略：制定问题对策管理策略，包括问题对策活动、问题状态模型、警报通知、执行这些操作后的响应能力和紧急处理策略。设定好 到 关切方的接口，并维护。

b. 识别并记录问题：每个问题都要单独识别、描述并记录。应当提供支持信息来重现和诊断该问题。支持信息一般包括问题的起源。如何重现。环境信息、何人发现等等；单独识别问题有助于对变更的追踪能力。

c. 记录问题状态信息：根据问题状态模型得出的状态信息是绑定到问题上的，有利于对问题的追踪。

d. 诊断问题原因及其影响：调查、诊断问题原因及影响，确定适当行为项并对问题进行分类。问题分类（例如，A/B/C, 轻微，中等，严重）可根据严重程度、影响、危害性，紧迫性，相关性等进行分类。

e. 授权紧急问题处理行动：若根据问题对管理策略，解决问题需要一个紧急对策，那同样根据本策略，为了紧急响应必须包含一定的授权。

f. 提出警报通知：根据策略，若一个问题对其他的系统或者攸关方有很强的影响，那么必须发出提示警告信息。

g. 启动问题解决：根据对策管理策略，采取适当措施以解决问题，包括对过往行为的审查，或发起更新请求。合适的行为包含发起以变更请求，参考变更请求管理流程。

h. 跟踪问题直至问题解决：跟踪问题的状态信息，直指问题关闭。关闭问题之前，需得到正式的问题关闭授权。

i. 分析问题趋势：从问题管理系统收集并分析数据（发生频率、探测度、影响范围等），识别趋势，必要时采取相应行动。收集到的数据一般包括问题发生的位置，何时以何种方式被发现，有何影响等。流程改进的实施（预防问题）是在流程改进流程（PIM3）中完成的。对通用项目管理的改进（比如经验教训总结）是项目管理流程的内容（MAN3）。对与通用工作产品有关的改进是质量保证流程（SUP1）的部分。

**3. 输出物：**

a. 问题管理计划：定义问题解决活动，包含问题识别、问题记录、问题描述即问题分类；问题解决办法：评估及修正问题；定义问题跟踪机制；问题解决方案的搜集及分配机制。

b. 问题记录：识别问题提出人姓名及联系方式；识别负责修复问题的组织或人员；包含问题描述；识别问题的分类（严重度、紧迫度、关联性等）；识别已报告问题的状态；识别问题修复的目标发布版本；识别问题的预期关闭日期；识别问题关闭准则；识别问题复验行动项。

c. 分析报告：分析的内容；分析人；所采用的的分析准则（选择的准则或采用的优先级计划、决策准则、质量准则）；记录结果（决定或选择的内容、选择的原因、做出的假定、潜在风险）；正确性分析的方面包括（完整性、可理解性、可测性、可验证性、可行性、有效性、一致性、内容的充分性）。

d. 评估报告：声明评估目的；使用的评估方法；评估所用的需求；假设及限制；识别所需的内容和范围信息（评估日期、参与者、详细内容、评估所使用的手段如检查单和工具等）；记录评估结果（数据、识别所需的纠正及预防措施、改进机会）。

e. 问题状态报告：显示问题记录的摘要信息（按照问题分级/分类）；问题解决的状态（打开问题及关闭问题的变化趋势）。

**四、变更请求管理**

1. **总览**

确保变更请求被管理、跟踪以及监控。

过程产出：

a. 制定变更管理策略；

b. 记录且识别变更请求；

c. 识别与其他变更请求之间的依赖关系；

d. 定义变更请求实施结果的确认标准；

e. 对变更请求进行分析、按优先级排序并预估所需资源；

f. 以变更的优先级即资源的可用性为基础对变更进行核准；

g. 实施已核准的变更并跟踪其直至关闭；

h. 所有变更请求的状态可知；

i. 建立变更请求和受影响的工作产品间的双向可追溯性。

**2. 流程步骤：**

a. 制定变更请求管理策略：制定变更请求管理策略，包括：变更请求活动，变更请求状态模型，分析标准，以及实行这些行动后的响应。设定好到波及方的接口并维护。变更请求状态模型包含：打开、研究中、批准施行、已分配、已实施、已修复、已关闭等。一个分析标准的典型是：资源需求、调度问题、风险、收益等。变更请求行为确保变更请求能被系统地识别、分析、记录、实行和管理。变更请求管理策略可覆盖产品生命周期的不同阶段，比如原型设计和系统开发。

b. 标识和记录变更请求：每个问题都要根据管理策略单独识别、描述并记录，包括变更请求的发起人和发起原因。

c. 记录变更请求的状态。

d. 分析和评估变更请求：根据管理策略分析变更请求，包括与涉及工作产品和其他变更请求的依赖。评估该变更请求的影响并创建实行确认标准。

e. 在实施变更请求前批准：在实施变更之前，要根据管理策略批准，并基于分析结果和资源可用性确定变更请求的优先级。变更控制盘是一个批准变更需求的公共机制。变更请求可被分配释放。

f. 评审变更请求的实施：变更请求实施评审要在变更请求关闭之前进行，以确保请求符合确认标准且被应用在其他所有相关过程中。

g. 追踪变更请求直至关闭：并提供反馈给发起者。

i. 建立双向可追溯性：创建在变更请求和涉及到的工作产品之间的双向追踪能力。为防变更请求是由问题发起的情况，可创建变更请求与相应问题报告之间的双向追踪能力。双向追踪支持完整性、一致性和影响分析。

**3. 输出物：**

a. 变更管理计划：定义变更管理活动，包含变更识别、变更记录、变更描述、变更分析及变更实施。定义跟踪变更请求状态的方法。定义验证及确认活动。变更审核及变更影响范围评审。

b. 变更请求：识别变更目的。识别请求状态（新建、已接收、被拒绝）。识别请求人联系信息。被影响的系统。对现有系统运行的影响。对相关文档的影响。请求的严重度和需要完成的日期。

c. 审查（评审记录）：需提供评审上下文信息（评审的内容、出席评审的人员列表、评审状态）；提供评审覆盖信息（检查单、评审准则、需求、标准符合性）；检查发现（不一致性、改进建议）；记录信息包括（评审准备完成状态、评审准备所花费时间、评审花费的时间、评审人员&角色及评审意见）；识别所需的纠正措施（风险识别、按优先级排序的偏差列表和发现的问题列表、解决问题所需的行动及任务、纠正措施的负责人、已识别问题的状态即目标关闭日期）。

d. 变更控制记录：采用一种控制机制来控制正式项目的发布库中基线化产品；变更请求记录，生成基线化产品（工作产品、软件、客户文档等），包括识别受变更影响的系统和文档、识别变更请求、识别变更的负责团队、识别变更的状态；与相关客户请求、内部变更请求建立连接关系；适当的批准；读重复的请求进行识别和分组。

**五、项目管理**

1. **总览**

在项目的需求和约束条件下识别、建立和控制项目生产产品所必须的活动和资源。

过程产出：

a. 项目工作范围已定义；

b. 已评估在现有资源和约束下实现项目目标的可行性；

c. 已评估并量化了完成工作的任务和必要的资源，并按大小分类；

d. 已识别并监控了项目元素之间的接口，项目与其他项目及组织单位的接口；

e. 项目执行计划已被开发、实施和维护；

f. 已监督并报告项目进展；

g. 当项目未达到目标时，实施已计划的偏差纠正措施，并防止已识别问题复发。

**2. 流程步骤：**

a. 定义工作范围：确定项目的目标、动机和边界。

b. 定义项目生命周期：定义项目生命周期，应该定义合适的项目范围，项目背景，项目量级和项目复杂性。这通常意味着项目生命周期和客户的开发过程是一致的。

c. 评估项目的可行性：按照时间因素，项目预算，可用资源几个约束条件的技术可行性来评估实现项目目标的可行性。

d. 定义、监控、调整项目活动：定义、监控、调整项目活动以及他们的依赖项应该依据已经定义的项目的生命周期和预算。根据需要调整活动以及他们的依赖项。结构化的以及可量化的活动以及相关工作包支持足够的进度监控。项目活动通常覆盖工程、管理和支持流程。

e. 确定、监控、调整项目预算和资源：确定、监控、调整项目预算和资源应该根据项目目标，项目风险，项目动机以及项目边界。应该使用适当的评估方法。必要资源，例如人员、基础设施（工具、测试设备、通信机制）、硬件/材料等。项目风险和质量标准可能被考虑。预算和资源典型的应该包含工程、管理和支持过程。

f. 保证所需的技能、知识和经验：为项目确定所需的技能、知识和经验，保证选择的个人和团队已经拥有或者能够及时取得这些技能，知识和经验。所需技能与知识培训的差异应该着重提供。

g. 识别、监视和调整项目接口和已达成一致的事项。识别和约定项目与其他项目（包括子项目），组织单元以及其他利益相关者的接口并且监控已经达成一致的事项。项目接口涉及工程、管理和支持过程。

h. 明确、监督、调整项目进度：为项目活动分配资源，并且安排整个项目的活动。这些安排在项目生命周期中应持续更新。这涉及到所有的工程、管理和支持过程。

i. 确保一致性：确保预算、活动、日程、计划、接口以及项目约定受各方的影响是一致的。

j. 评审和报告项目进度：定期评审和报告项目的状态，并根据预计的努力和持续时间对所有受影响的各方履行活动，防止出现问题复发。项目评审可以由管理人员定期执行。在项目收尾阶段，项目评审有助于识别，例如，最佳实践和经验教训。

**3. 输出物：**

a. 项目计划：定义以下内容，如：将要开发的工作产品、使用的生命周期模型及方法、与项目管理相关的客户需求、需要完成的任务、任务责任人、项目资源、时间表/里程碑/预期时间、预算、质量标准；识别以下内容，如：关键依赖关系、必需的工作产品、项目风险及风险减轻计划、未完成任务的应急措施。

b. 沟通记录：每一组都是可识别、可检索的数据；所有形式的人际沟通，包含信件、传真、电子邮件、语音记录和电报等。

c. 变更请求：识别变更目的；识别请求状态（新建、已接收、被拒绝）；识别请求人联系信息；被影响的系统；对现有系统运行的影响；对相关文档的影响；请求的严重度和需要完成的日期。

d. 审查（评审）记录（略）

e. 纠正行动记录：识别初始问题；识别已定义行动项的执行负责人；定义解决方案（为解决问题而采取的一系列行动项）；识别问题发现日期及预算的关闭日期；包含状态指示器；标明后续的审核活动。

f. 进度安排（Schedule）：识别需要执行的任务；识别必要任务预期和实际开始、完成日期；考虑识别关键任务及任务依赖性；识别任务完成状态与计划日期对照；与计划的资源数据进行映射。

g. 工作分解项（Work breakdown structure）：定义计划执行的任务及其修正案；记录任务的负责人；记录任务间关键依赖关系；记载输入输出工作产品；记录已定义工作产品之间的关键依赖关系。

h. 干系人组织列表（Stakeholder groups list）：标识出 相关干系人组织、每个干系人组的权重与重要性、每个干系人组的代表、每个干系人组的信息需要。

i. 项目状态报告：项目当前状态报告；时间表，包括计划的进度、实际的进度、计划进度偏差原因、后续进展的风险、保证进度所采取的应急计划；预算，包括计划支持、实际支出、计划支出和实际支出的偏差原因、预期的未来支出、满足预算目标的应急计划；质量目标，包括实际的质量度量、与计划质量产生偏差的原因；为达到质量目标的应急计划；项目问题，如影响项目实现既定目标的问题、规避影响项目达成目标的风险的应急计划。

**六、供应商监控（Supplier Monitoring）**

跟踪和评估供应商在履行约定需求时的表现。

过程产出：

a. 联合活动，根据需要执行，客户和供应商之间的约定；

b. 所有信息，商定交换，供应商和客户之间的定期沟通；

c. 根据协议监控供应商的执行；

d. 如果需要更改协议，客户和供应商共同协商，并文档化协议。

**2. 流程步骤：**

a. 同意和维护联合流程：共同的接口和信息交换。建立和维持一个关于交换信息和共同的流程，共同的接口，责任，类型以及共同活动，通信，会议，转台报告和审核频率的协议。共同的流程节接口通常包括项目管理、需求管理、变更管理、配置管理、问题解决、质量保证和客户验收。要执行的共同的活动应通过客户和供应商双方同意。在这个过程中客户是评估方。供应商指评估方的供应商。

b. 交换达成一致的信息：使用客户和供应商之间定义的共同的接口交换所有达成一致的信息。达成一致的信息应包括所有相关的工作产品。

c. 与供应商一起进行技术开发评审：根据约定定期与供应商评审开发，主要涵盖技术方面的问题与风向和跟踪打开的项直至关闭。、

d. 审查供应商的进度：根据约定定期审查供应商有关进度，质量和成本的进展。跟踪打开的项直至关闭并执行风险缓解活动。

e. 纠正偏差行动：如果商定的目标没有达成，应该采取行动以纠正商定的项目计划之间的偏差，并防止发现问题的再次发生。协商修改目标并且将协定归档。



\3. 输出物：

a. 承诺或协议：参与各方签署承诺/协议；确定承诺的内容；确定完全满足承诺所需的资源（时间、人员、预算、设备、设施）。

b. 验收记录：交付时的收据记录、收货日期识别、已交付组件识别、记录基于客户自定义验收标准的验证结果、收获客户的签名。

c. 沟通记录

d. 会议纪要：会议目的、出席人员、会议日期、会议地点、涉及到的之前的会议纪要、收获了什么、识别提出的问题、未解决的问题、下次会议

e. 进展状态记录：两方面，一方面是计划于实际的状态记录，如实际任务及计划任务的状态；实际结果及既定目的的情况；实际资源及计划资源的分配；实际成本及计划预算的情况；实际工时及计划时间表的情况；实际质量及计划质量的情况。另一方面，记录所有与计划活动的偏差及其原因。

f. 变更请求：识别变更目的；识别变更请求的状态（新建、已接收、被拒绝）；识别变更申请人的联系信息；被变更所影响的系统；对现有系统运行的影响；对相关文档的影响；变更请求的严重度和需要完成的日期。

g. 审查记录

h. 纠正措施记录

i. 分析报告