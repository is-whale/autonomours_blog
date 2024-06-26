# 1 摘要 

本发明涉及无线通信协议技术领域，公开了一种V2X协议栈的通讯方法，包括**V2X协议栈将原始数据经应用层接收，获取V2X消息数据，依据V2X消息数据中的关键字段设置每个功能层级的配置信息，根据配置信息选择需要使用的功能层级，逐层调用向下传递后广播V2X消息；V2X协议栈接收V2X消息，根据配置信息选择需要使用的功能层级，逐层向上传递处理后传回至应用层**。 

本发明基于车联网行业标准中无线通信相关技术要求，通过模块化、层级化各主要功能，简化设计流程，减少功能叠加，增强系统稳定性，同时各层功能明确，各层耦合性小，上层调用下层提供的功能接口，下层不会调用上层功能接口，协议栈各层可根据需要单独对外提供本层的功能服务，稳定性、可靠性高。

1 .一种V2X协议栈的通讯方法，其特征在于，包括V2X协议栈将原始数据经应用层接收，获取V2X消息数据，依据V2X消息数据中的关键字段设置每个功能层级的配置信息，根据配置信息选择需要使用的功能层级，逐层调用向下传递后经空口广播V2X消息； V2X协议栈接收所述V2X消息，根据所述配置信息选择需要使用的功能层级，逐层向上传递处理后传回至应用层。 

2 .根据权利要求1所述的一种V2X协议栈的通讯方法，其特征在于，所述配置信息为关 键字和数值一一对应的配置文件，其中关键字用于区分不同层级，数值用于设置功能层级的状态，通过修改数值来设置与关键字对应的功能层级的状态。 

3 .根据权利要求1所述的一种V2X协议栈的通讯方法，其特征在于，所述根据配置信息选择需要使用的功能层级，逐层调用向下传递后广播V2X消息包括 

步骤S2‑2：获取V2X消息数据，根据配置信息确定是否启用管理子层，不启用管理子层 时执行步骤S2‑3，启用管理子层时，判断V2X消息数据是否为用户已订阅的数据，不是则丢弃所述V2X消息数据，是则得到V2X筛选数据，执行步骤S2‑3； 

步骤S2‑3：判断是否使用安全层，未使用安全层时，将V2X消息数据或V2X筛选数据直接 上传至消息子层，使用安全层时，将V2X消息数据或V2X筛选数据经过安全处理后分别得到对应的明文数据，将对应的所述明文数据上传至消息子层。 

4 .根据权利要求3所述的一种V2X协议栈的通讯方法，其特征在于，所述获取V2X消息数据包括 

步骤S2‑0：接入层指示接口接收V2X消息，接收成功后将V2X消息上报至适配层，适配层 指示接口接收上报的V2X消息，接收成功则进行解包并去掉消息头信息，将得到的V2X适配消息上报至数据子层； 

步骤S2‑1：数据子层指示接口接收V2X适配消息，接收成功则解析并提取关键字段，得到的V2X消息数据上报至管理子层。 

5 .根据权利要求3所述的一种V2X协议栈的通讯方法，其特征在于，所述使用安全层时， 将V2X消息数据或V2X筛选数据经过安全处理后分别得到对应的明文数据，将对应的所述明文数据上传至消息子层包括 安全层指示接口接收V2X消息数据或V2X筛选数据，接收成功后进行数据验签以及安全解密后得到对应的明文数据，并将对应的明文数据上传至消息子层。 

6 .根据权利要求5所述的一种V2X协议栈的通讯方法，其特征在于，消息子层指示接口接收所述明文数据，接收成功后进行ASN1C解码处理，获得结构化数据上传至用户应用子层； 用户应用子层指示接口接收结构化数据，接收成功后进行解析，提取关键字段进行业务处理。 

7 .根据权利要求1所述的一种V2X协议栈的通讯方法，其特征在于，所述V2X协议栈将原始数据经应用层接收，逐层调用传递后经空口广播V2X消息包括步骤S1‑1：原始数据在应用层进行整合得到编码数据； 

步骤S1‑2：根据配置信息确定是否启用安全层，启用安全层后对编码数据进行数据签名以及安全加密，成功后生成安全数据，根据安全数据请求数据子层服务；未启用安全层时，所述编码数据直接上传并请求数据子层服务； 

步骤S1‑3：数据子层服务请求成功后，数据子层接收上述的安全数据或编码数据，并对数据进行封装形成V2X数据包，根据V2X数据包请求适配层服务，适配层服务请求成功后，适配层进行适配封装得到V2X消息包，并请求接入层服务。 

8 .根据权利要求7所述的一种V2X协议栈的通讯方法，其特征在于，所述步骤S1‑3后设有步骤S1‑4：接入层服务请求成功后，接入层根据配置信息选择V2X通信模组，由通信模组对V2X消息包进行封装转换后得到最终的V2X消息，经空口对外广播V2X消息。 

9 .根据权利要求7所述的一种V2X协议栈的通讯方法，其特征在于，所述步骤S1‑1包括步骤S1‑10：原始数据进入到协议栈的应用层，通过用户应用子层对原始数据进行处理得到用户应用数据，通过用户应用数据请求消息子层服务； 

步骤S1‑11：消息子层服务请求成功，消息子层接收用户应用数据并进行ASN1C编码得到编码数据。 

10 .根据权利要求8所述的一种V2X协议栈的通讯方法，其特征在于，所述V2X通信模组 包括蜂窝通讯接口以及直连通讯接口，所述直连通讯接口包括高通模组和辰芯模组。

# 2 一种V2X协议栈的通讯方法

## 2.1 技术领域

 [0001] 本发明涉及无线通信协议技术领域，具体涉及一种V2X协议栈的通讯方法。 

## 2.2 背景技术 

[0002] 车联网是智慧交通系统发展的最新方向，是采用先进的无线通信和新一代互联网 等技术，全方位实施车‑车（V2V）、车‑路（V2I）、车‑网（V2N）、车‑人（V2P）等动态实时信息交 互，并在全时空动态交通信息采集与融合的基础上开展车辆主动安全控制和道路协同管 理，充分实现人车路的有效协同。 

[0003] 上述四种车辆通信的情况：V2V、V2I、V2N、V2P，可统称为V2X，即Vehicle to Everything，意为车与外界信息的交换。 

[0004] 目前行业协会等标准组织已针对车辆网无线通信在技术上提出了一系列的标准， 将系统架构及基本功能做了相关说明，将车联网无线通信系统按照功能划分成若干功能层 级，该功能层级自上而下分为应用层、安全层、网络层、接入层。 

[0005] **目前V2X协议栈用到的是DSMP协议去解析。**网络层由数据子层和管理子层两部分构成；管理子层主要完成系统配置及维护，为所有的数据子层实体提供管理接口等功能。数据子层主要包括适配层(Adaptation Layer)、IP和UDP/TCP以及合作式智能运输系统DSMP。 目前V2X协议栈用到的是DSMP协议去解析。**数据子层利用管理子层提供好的接口传输应用层间的数据流。应用层自己要解析出来得出的BSM ,SPAT ,MAP ,RSI ,RSM五大消息集，实现相关场景算法。** 

[0006] 例如申请号为202010310297 .7，公布号为CN 111615078的专利中，公开了一种C‑ V2X协议栈的通信方法及装置，解决现有技术中引入数据不安全、移植性差、接口不灵活、应用程序支持差的问题，但是目前还存在如下问题：当需要接收多种不同消息时，会存在网络层过滤掉有用数据的风险；同时，提供的系统选配不灵活，调用不方便，且占用资源较多。

## 2.3 发明内容 

[0007] 针对现有技术存在的不足，本发明的目的在于提供一种V2X协议栈的通讯方法，基于车联网行业标准中无线通信相关技术要求，通过模块化、层级化各主要功能，简化设计流程，减少功能叠加，增强系统稳定性。 

[0008] 为了实现上述目的，本发明提供如下技术方案： V2X协议栈将原始数据经应用层接收，获取V2X消息数据，依据V2X消息数据中的关键字段设置每个功能层级的配置信息，根据配置信息选择需要使用的功能层级，逐层调用向下传递后经空口广播V2X消息； V2X协议栈接收所述V2X消息，根据配置信息选择需要使用的功能层级，逐层向上传递处理后传回至应用层。 

[0009] 在本发明中，进一步的，所述配置信息为关键字和数值一一对应的配置文件，其中关键字用于区分层级，数值用于设置功能层级的状态，通过修改数值来设置与关键字对应的功能层级的状态。 

[0010] 在本发明中，进一步的，所述根据配置信息选择需要使用的功能层级，逐层调用向 下传递后广播V2X消息包括步骤S2‑2：获取V2X消息数据，根据配置信息确定是否启用管理子层，不启用管理子层时执行步骤S2‑3，启用管理子层时，判断V2X消息数据是否为用户已订阅的数据，不是则丢弃所述V2X消息数据，是则得到V2X筛选数据，执行步骤S2‑3； 

步骤S2‑3：判断是否使用安全层，未使用安全层时，将V2X消息数据或V2X筛选数据直接上传至消息子层，使用安全层时，将V2X消息数据或V2X筛选数据经过安全处理后分别得到对应的明文数据，将对应的所述明文数据上传至消息子层。 

[0011] 在本发明中，进一步的，所述获取V2X消息数据包括步骤S2‑0：接入层指示接口接收V2X消息，接收成功后将V2X消息上报至适配层，适配层指示接口接收上报的V2X消息，接收成功则进行解包并去掉消息头信息，将得到的V2X 适配消息上报至数据子层； 

步骤S2‑1：数据子层指示接口接收V2X适配消息，接收成功则解析并提取关键字段，得到的V2X消息数据上报至管理子层。 

[0012] 在本发明中，进一步的，所述使用安全层时，将V2X消息数据或V2X筛选数据经过安全处理后分别得到对应的明文数据，将对应的所述明文数据上传至消息子层包括安全层指示接口接收V2X消息数据或V2X筛选数据，接收成功后进行数据验签以及安全解密后得到对应的明文数据，并将对应的明文数据上传至消息子层。 

[0013] 在本发明中，进一步的，消息子层指示接口接收所述明文数据，接收成功后进行 ASN1C解码处理，获得结构化数据上传至用户应用子层； 用户应用子层指示接口接收结构化数据，接收成功后进行解析，提取关键字段进行业务处理。 

[0014] 在本发明中，进一步的，所述V2X协议栈将原始数据经应用层接收，逐层调用传递后经空口广播V2X消息包括 

步骤S1‑1：原始数据在应用层进行整合得到编码数据； 

步骤S1‑2：根据配置信息确定是否启用安全层，启用安全层后对编码数据进行数据签名以及安全加密，成功后生成安全数据，根据安全数据请求数据子层服务；未启用安全层时，所述编码数据直接上传并请求数据子层服务； 

步骤S1‑3：数据子层服务请求成功后，数据子层接收上述的安全数据或编码数据， 并对数据进行封装形成V2X数据包，根据V2X数据包请求适配层服务，适配层服务请求成功后，适配层进行适配封装得到V2X消息包，并请求接入层服务。 

[0015] 在本发明中，进一步的，所述步骤S1‑3后设有步骤S1‑4：接入层服务请求成功后，接入层根据配置信息选择V2X通信模组，由通信模组对V2X消息包进行封装转换后得到最终的V2X消息，经空口对外广播V2X消息。 

[0016] 在本发明中，进一步的，所述步骤S1‑1包括步骤S1‑10：原始数据进入到协议栈的应用层，通过用户应用子层对原始数据进行处理得到用户应用数据，通过用户应用数据请求消息子层服务； 

步骤S1‑11：消息子层服务请求成功，消息子层接收用户应用数据并进行ASN1C编码得到编码数据。 

[0017] 在本发明中，进一步的，所述V2X通信模组包括蜂窝通讯接口以及直连通讯接口， 所述直连通讯接口包括高通模组和辰芯模组。 

[0018] 与现有技术相比，本发明的有益效果是： 本发明通过设置配置信息，使各层功能独立，上层调用下层功能接口，减少各层之 间功能耦合性，上层调用简单，传递数据参数量小。各层的功能实现均以接口调用的形式暴露给上层应用实体，整体实现了协议栈占用资源的节约。

从用户角度，本协议栈各层功能明确，协议栈使用便捷，可调用的功能服务接口简单，入参和出参结构简单，系统稳定性高，可 靠性高。 

[0019] 同时，本方案将网络层将管理子层和数据子层分离，并可以通过配置信息选配是 否使用管理子层，在不适用该管理子层时，应用层直接循环调用网络层接收数据接口函数， 达到应用层消息透传发送和接收的目的，无需前期针对不同消息进行多次请求。应用层调用网络层接收数据的接口函数时，由上至下，逐层调用下层接口函数，各层之间独立、耦合 性小。 

[0020] 同时，本方案安全层具备选配功能，在实际应用中，特别是一些接口测试对比试验 中，不需要安全层参与消息的加解密和签名验签，本专利的选配功能，可在这种情况下，选择不使用安全层，应用数据直接进行网络层封装，再到接入层，继而对外发送空口消息；空口消息接收也不需安全层解密和验签，经网络层解包后直接进行应用层数据处理。 

[0021] 如此，实现了本方案的应用灵活、便捷，二次开发接口调用灵活，功能强大。

## 2.4 具体实施方式 

[0023] 下面将结合本发明实施例中的附图，对本发明实施例中的技术方案进行清楚、完整地描述，显然，所描述的实施例仅仅是本发明一部分实施例，而不是全部的实施例。基于本发明中的实施例，本领域普通技术人员在没有做出创造性劳动前提下所获得的所有其他实施例，都属于本发明保护的范围。 

[0024] 需要说明的是，当组件被称为“固定于”另一个组件，它可以直接在另一个组件上或者也可以存在居中的组件。当一个组件被认为是“连接”另一个组件，它可以是直接连接到另一个组件或者可能同时存在居中组件。当一个组件被认为是“设置于”另一个组件，它可以是直接设置在另一个组件上或者可能同时存在居中组件。

本文所使用的术语“垂直的”、 “ 水平的”、“ 左”、“ 右”以及类似的表述只是为了说明的目的。 

[0025] 除非另有定义，本文所使用的所有的技术和科学术语与属于本发明的技术领域的 技术人员通常理解的含义相同。本文中在本发明的说明书中所使用的术语只是为了描述具体的实施例的目的，不是旨在于限制本发明。本文所使用的术语“及／或”包括一个或多个相关的所列项目的任意的和所有的组合。 

[0026] 本发明基于车联网行业标准中无线通信相关技术要求，通过模块化、层级化各主要功能，简化设计流程，减少功能叠加，增强系统稳定性。请参见图1，根据行业标准技术要求，V2X协议栈包括多个功能层级，自上而下划分为四层：应用层、安全层、网络层、接入层。 

[0027] 其中，应用层包含用户应用子层、消息子层。 

[0028] 安全层包含安全数据处理模块、安全凭证管理模块、安全服务接口模块。 

[0029] 网络层分为管理子层、数据子层。其中，管理子层为专用管理实体；数据子层包括适配层、UDP/TCP和IP、专用短消息协议功能模块。 

[0030] 接入层包含蜂窝通信接口以及直连通信接口。 

[0031] 在本发明提供的一实施例中，请参见图7，为了便于对实现上述功能层级进行管 理，本发明的V2X协议栈的文件目录按照功能划分为编译用目录和代码目录，编译目录主要用于存放固件、脚本以及资源文件。代码目录按照功能层级划分为应用层目录、安全层目 录、网络层目录、接入层目录、配置目录、工具目录、第三方库目录以及一级头文件目录。 

[0032] 其中，其中应用层目录二次划分为用户应用子层目录以及消息子层目录；所述安全层目录二次划分为安全数据处理模块目录、安全凭证管理模块目录以及安全服务接口模块目录，所述网络层目录二次划分为数据子层目录、管理子层目录、适配层目录，接入层目录包括蜂窝通信接口目录、直连通信接口目录。上述每个目录划分为源码目录和头文件目录，其中源码目录主要用于存放与各层目录功能相对应的实现数据。

[0033] 此外，配置目录存放配置文件读写功能、各层级或功能模块配置参数存取的实现数据（代码），下文中的配置信息存放于该目录中。工具目录存放通用工具（如asn1c编解码 工具）开源代码。第三方库目录存放调用的第三方功能库函数的库文件。一级头文件目录存放各层级或功能模块公用的头文件。 

[0034] 如此，方案将V2X协议栈的文件目录进行清晰的划分，对头文件和源文件，及引用 的第三方开源代码进行分类管理，使得整个工程的结构更清晰，扩展性好，维护性好。实现功能进行抽象，规范统一格式，各层层次分明，功能独立，使得整个V2X协议栈结构清晰，便于管理和后期维护。 

[0035] 其次，本发明抽象各个功能层级和功能模块的主要功能，抽象出主要内容包括各 层的初始化、去初始化、功能请求、数据指示、获取发送配置参数、获取接收参数。 

[0036] 例如，统一规划、标准化功能，在函数实现上根据数据收发的数据流向将功能归纳 为以下指示接口功能函数： cv2x_XXXGetSendPara为获取发送数据配置参数功能函数，该参数有两种数据来 源，一是配置文件、一是调用接口需传入的入参结构体。 

[0037] cv2x_XXXGetRecvPara为获取接收数据参数功能函数，主要为接收消息中携带的 发送端配置参数，如功率、频点、消息头参数。

[0038] cv2x_XXXInit为层级或功能模块初始化函数。 

[0039] cv2x_XXXDeinit为层级或功能模块去初始化函数。 

[0040] cv2x_XXXRequest为层级或功能模块服务请求函数。 

[0041] cv2x_XXXIndication为层级或功能模块接收数据处理函数。 

[0042] 需要说明的是，本文以下涉及的各个功能层级中对应的指示接口以及服务请求均 为功能函数，例如适配层服务请求cv2x_ada pte rReq ues t和适配层指示接口 cv2x_ adapterIndication。 

[0043] 本发明提供的另一实施例中，基于上述协议栈的数据，本实施方式提供一种V2X协 议栈的通讯方法，包括V2X协议栈将原始数据经应用层接收，获取V2X消息数据，依据V2X消 息数据中的关键字段设置每个功能层级的配置信息，根据配置信息选择需要使用的功能层 级，逐层调用向下传递后广播V2X消息； V2X协议栈接收V2X消息，根据配置信息选择需要使用的功能层级，逐层向上传递 处理后传回至应用层。 

[0044] 所述配置信息为关键字和数值一一对应的配置文件，其中关键字用于区分层级， 也就是层级的配置选项，数值用于设置功能层级的状态，通过修改数值来设置与关键字对 应的功能层级的状态。其中，配置信息中包括对每一功能层以及功能层中的子层的配置文件，也可以理解为各个层级具有配置功能的功能开关，根据实际应用场景选择开或关相应的功能。具体的，配置信息可以根据不同的应用场景进行手动修改，也可以通过关键字段自动识别设置，如此，可根据实际需要对功能层级进行选择，从而实现了各层相互独立，减少各层之间功能耦合性。 

[0045] 例如，代表消息子层的配置信息设为MSG_LAYER_USE=0，MSG_LAYER_USE表示消息 子层，设置为0代表使用，设置为1即代表不使用。 

[0046] 在本发明中提供的一个实施例中，如用户想使用协议栈的接入层，网络层之上为自己做数据，那么就可以读取配置文件，将网络层以上的各层均关闭，也就是将配置信息， 网络层之上各层配置数值均设为1，在调用协议栈函数接口时，就直接调用接入层指示接 口，用通信模组发空口消息。 

[0047] 请参见图3，方案将V2X协议栈在一次收发空口数据流程进行了逐层传递，逐层调用，具体包括 步骤S1‑1：原始数据在应用层进行整合得到编码数据； 步骤S1‑2：根据配置信息确定是否启用安全层，启用安全层后对编码数据进行数 据签名以及安全加密，成功后生成安全数据，根据安全数据请求数据子层服务；未启用安全 层时，所述编码数据直接上传并请求数据子层服务； 步骤S1‑3：数据子层服务请求成功后，数据子层接收上述的安全数据或编码数据， 并对数据进行封装形成V2X数据包，根据V2X数据包请求适配层服务，适配层服务请求成功 后，适配层进行适配封装得到V2X消息包，并请求接入层服务。 

[0048] 步骤S1‑4：接入层服务请求成功后，接入层根据配置信息选择V2X通信模组，由通 信模组对V2X消息包进行封装转换后得到最终的V2X消息，经空口对外广播V2X消息。 

[0049] 在本发明中，进一步的，V2X协议栈数据源抽象为三类：配置文件或边缘计算单元 或其他接口。如图4所示，步骤S1‑1：原始数据在应用层进行整合，包括步骤S1‑10：原始数据进入到协议栈的应用层，通过用户应用子层对原始数据进行 处理得到用户应用数据，通过用户应用数据请求消息子层服务； 步骤S1‑11：消息子层服务请求成功，消息子层接收用户应用数据并进行ASN1C编 码得到编码数据。 

[0050] 具体的，用户应用子层接收到配置文件或边缘计算单元或其他接口信息，读取的数据信息后，进行检验、整合处理，按照接口协议生成标准应用消息，再向消息子层调用消 息子层服务请求。调用消息子层服务请求成功后首先将数据信息填充到规范的消息数据格 式中，进行ASN1C编码，得到编码数据，调用消息子层服务请求失败则自动跳转至本程序结 束，退出协议栈。 

[0051] 之后根据配置信息确定是否启用安全层，确定启用后判断是否使用安全签名或加 密，确定则进行安全数据签名或加密，生成安全数据，安全签名或加密失败则返回错误码跳 至程序末尾，退出V2X协议栈。未启用安全层时，所述编码数据直接上传并请求数据子层服 务。数据子层服务请求成功后，数据子层接收上述的安全数据或编码数据，并对数据进行封 装形成V2X数据包，数据子层服务请求失败则返回错误码跳至程序末尾，退出V2X协议栈。根 据V2X数据包请求适配层服务，适配层服务请求成功后，适配层进行适配封装得到V2X消息 包，并请求接入层服务。适配层服务请求失败则返回错误码跳至程序末尾，退出V2X协议栈。 

[0052] 本方案安全层具备选配功能，在实际应用中，特别是一些接口测试对比试验中，不 需要安全层参与消息的加解密和签名验签，本专利的选配功能，可在这种情况下，选择不使 用安全层，应用数据直接进行网络层封装，再到接入层，继而对外发送空口消息；空口消息 接收也不需安全层解密和验签，经网络层解包后直接进行应用层数据处理。 

[0053] 在本发明提供的一实施例中，例如在判断应用层数据字段的一致性，即双收发时 发送的数据和接收的数据是否一致，这时不希望使用安全层加解密，那就可以配置不适用 安全层，也就是将配置信息中不选择安全层，如此，实现了安全层独立且可配置，简化了数 据流的传输流程，使系统选配更加灵活方便。 

[0054] 其中，本方案的安全层分为安全数据处理模块、安全凭证管理模块、安全服务接口 模块： 安全数据处理模块负责对需要发送的应用消息进行数字签名或加密，对接收到的 应用消息进行数字签名验证或数据解密。 [0055] 安全凭证管理模块负责与安全凭证管理实体交互获得相关的公钥证书和证书撤 销列表等安全凭证或数据。 [0056] 安全服务接口模块负责提供安全凭证和安全数据的存储和密码运算服务，如公钥 证书和证书撤销列表的存储，秘钥的生成和存储，签名、验签、加密、解密和哈希运算等密码 运算。并向安全数据处理功能模块和安全凭证管理功能模块提供安全服务应用接口。 [0057] 以此来完成安全签名或加密的工作，实现V2X的通信安全。 

[0058] 接着，数据流到达适配层进行适配层封装，之后根据配置信息进行适配层通信模 组选择，调用接入层服务请求，继而生成空口数据，广播消息，广播该消息。 

[0059] 其中，本发明中作为优选，V2X通信模组包括蜂窝通讯接口以及直连通讯接口，直 连通讯接口包括高通模组和辰芯模组。如此划分的接入层抽象适配了不同种类的V2X通信 模组，通过配置文件选配，可支持适配不同厂家的V2X通信模组。

[0060] 在本发明中，请参见图5，进一步的，V2X协议栈接收空口数据并逐层处理后传回至 应用层，包括 步骤S2‑0：接入层指示接口接收V2X消息，接收成功后将V2X消息上报至适配层，接 收失败则返回错误码跳至程序末尾，退出V2X协议栈，适配层指示接口接收上报的V2X消息， 接收成功则进行适配层解包并去掉适配层消息头信息，得到的V2X适配消息上报至数据子 层，接收失败则返回错误码跳至程序末尾，退出V2X协议栈； 步骤S2‑1：数据子层指示接口接收适配层上报数据，接收成功则进行网络层解包， 解析网络层消息头信息并提取关键字段，得到的V2X消息数据上报至管理子层，接收失败则 退出V2X协议栈。 

[0061] 具体的，空口数据流也就数V2X消息先由V2X协议栈接入层接收处理，适配层接收 接入层上报数据，进行适配层解包，解包后的适配层数据去掉适配层头部信息，以去掉适配 层封装的适配层消息头。 

[0062] 此外，现有的V2X通讯过程中，回传数据需要在网络层的MIB列表中查找是否为前 期请求的网络数据消息，采用回调方式进行数据接收和处理。当需要接收多种不同消息时， 需要向网络层进行多个请求，才能避免不被网络层过滤掉有用数据。本专利中，在不使用管 理子层时，应用层直接循环调用网络层中的数据子层指示接口，达到应用层消息透传发送 和接收的目的，无需前期针对不同消息进行多次请求。 

[0063] 例如，如图6所示，所述V2X协议栈接收空口数据并经过各层处理后传回应用层，还 包括 步骤S2‑2：获取V2X消息数据，根据配置信息确定是否启用管理子层，不启用管理 子层时执行步骤S2‑3，启用管理子层时，判断V2X消息数据是否为用户已订阅的数据，不是 则丢弃所述V2X消息数据，是则得到V2X筛选数据，执行步骤S2‑3； 步骤S2‑3：判断是否使用安全层，未使用安全层时，将V2X消息数据或V2X筛选数据 直接上传至消息子层，使用安全层时，将V2X消息数据或V2X筛选数据经过安全处理后分别 得到对应的明文数据，将对应的所述明文数据上传至消息子层。 

[0064] 本方案将网络层将管理子层和数据子层分离，并可以通过配置文件选配是否使用 管理子层，在不适用该管理子层时，应用层数据直接进行网络封装，回传数据直接网络解 包，不进行管理子层消息过滤，起到应用消息透传的功能。同时，应用层调用数据子层指示 接口时，由上至下，逐层调用下层接口函数，各层之间独立、耦合性小。此外，应用层也可以 直接调用接入层指示接口，下层数据收发功能不受上层影响，实现了二次开发接口的灵活、 便捷。 

[0065] 具体的，根据配置信息，确定是否启用管理子层，例如当用户发一个业务公告或是 订阅一类消息，就可以使用管理子层。若启用，管理子层则根据网络层提取的关键字段判断 是否为用户前期订阅的数据，如果不是用户前期订阅的数据，则在此环节进行消息丢弃处 理。只有用户前期订阅的数据才能向上传递。未启用管理子层时，适配层处理后的数据流将 直接经网络层解包、去除网络层消息头，再上传至消息子层。 

[0066] 如果是用户前期订阅的数据，得到V2X筛选数据。然后判断是否使用了安全数据处 理选项，也就是是否使用安全层，若使用，通过V2X消息数据或V2X筛选数据调用安全层指示 接口，接收V2X消息数据或V2X筛选数据，并进行数字签名验证或安全解密处理，接收失败失败则返回错误码跳至程序末尾，退出V2X协议栈。经数字验签或安全数据解密后得到明文数 据，明文数据向上传递到应用层中的消息子层。未使用安全层时，V2X消息数据或V2X筛选数 据直接流入应用层的消息子层。 

[0067] 消息子层指示接口接收上述明文数据，接收成功则进行ASN1C数据格式解码，获得 结构化的数据信息，解码后的数据向上传递到用户应用子层，用户应用子层根据实际应用 逻辑，对消息子层上报的结构化数据进行解析，依据用户需求提取关键字段进行业务处理， 失败则返回错误码跳至程序末尾，退出V2X协议栈。 

[0068] 最后提取用户感兴趣的数据字段上报给上层边缘计算单元或者是其他用户管理 平台。 

[0069] 如此，本发明各层使用配置文件提取软件运行时的固定参数，避免设备配置数据 跨层传递，增加软件运行负担。 

[0070] 如图2所示，在本实施方式中， 工作原理及特点： 原始数据进入到V2X协议栈的应用层，由用户应用子层对原始数据进行检验、整合 处理，按照接口协议生成标准应用消息，再向消息子层请求消息编码，得到编码数据。之后 根据配置信息确定是否安全层，使用安全层后对编码数据进行安全签名或加密，生成安全 数据；安全数据调用网络层数据子层请求，进行网络层封装；配置信息未选中使用安全层 时，所述编码数据直接上传并请求数据子层服务。数据子层接收上述的安全数据或编码数 据，并对数据进行封装形成V2X数据包，根据V2X数据包请求适配层服务，适配层服务请求成 功后，适配层进行适配封装得到V2X消息包，并请求接入层服务。之后根据配置信息进行适 配层通信模组选择，继而生成空口广播V2X消息。 

[0071] V2X消息先由V2X协议栈接入层接收处理，后经适配层解包，解包后的适配层数据 去掉适配层头部信息，得到V2X适配消息上报至数据子层。数据子层解析并提取关键字段， 得到的V2X消息数据上报至管理子层；根据配置信息，判断是否启用管理子层功能，启用了 管理子层，判断V2X消息数据是否为用户已订阅的数据，不是则丢弃该数据；如果是用户感 兴趣的消息，或者未启用管理子层，则根据配置判断是否启用了安全层，如果启用V2X消息 数据或V2X筛选数据则调用安全层指示接口进行安全数据验签或解密，验签或解码后的明 文数据由消息子层指示接口进行消息子层数据解码；未使用安全层时，V2X消息数据或V2X 筛选数据直接上传至消息子层，消息子层进行消息解码；最后提取用户感兴趣的数据字段 上报给上层边缘计算单元或者是其他用户管理平台。 

[0072] 本发明实现了使用配置文件进行参数传递和配置信息的确定，各层功能独立，上 层调用下层功能接口，减少各层之间功能耦合性，上层调用简单，传递数据参数量小。各层 的功能实现均以接口调用的形式暴露给上层应用实体，整体实现了V2X协议栈占用资源的 节约。 

[0073] 从用户角度，本V2X协议栈各层功能明确，使用便捷，可调用的功能服务接口简单， 入参和出参结构简单，系统稳定性高，可靠性高。如此，实现了本方案灵活、便捷，二次开发 接口调用灵活，功能强大。 

[0074] 上述说明是针对本发明较佳可行实施例的详细说明，但实施例并非用以限定本发 明的专利申请范围，凡本发明所提示的技术精神下所完成的同等变化或修饰变更，均应属于本发明所涵盖专利范围。

# 附图说明 

[0022] 附图用来提供对本发明的进一步理解，并且构成说明书的一部分，与本发明的实 施例一起用于解释本发明，并不构成对本发明的限制。

在附图中： 

![image-20220905104751627](https://img-blog.csdnimg.cn/img_convert/9ab27906b66db8aa49ae771b78b3644c.png)

图1是本发明提出的V2X协议栈结构框图； 

![image-20220905104840106](https://img-blog.csdnimg.cn/img_convert/a1faab1a8167db0912d20aef53e91bfd.png)

图2是本发明提出的V2X协议栈收发消息数据流； 

![image-20220905104902778](https://img-blog.csdnimg.cn/img_convert/db1e51c7717423c636fd62ff24e10078.png)

图3是本发明的提出的V2X协议栈发送消息的流程示意图； 

![image-20220905104911347](https://img-blog.csdnimg.cn/img_convert/f4a27ba6d8d5f16f996453f9e0fb307b.png)

图4是本发明中步骤S1‑1的流程示意图； 

![image-20220905104920051](https://img-blog.csdnimg.cn/img_convert/5984d11bdcf694c7ea4bacaf7ab3bd6b.png)

图5是本发明的提出的V2X协议栈接收消息中步骤S2‑0和步骤S2‑1的流程示意图； 

![image-20220905104930595](https://img-blog.csdnimg.cn/img_convert/25df7453713f36357f2630a3414b4e3c.png)

图6是本发明的提出的V2X协议栈接收消息的部分流程示意图； 

![image-20220905104943068](https://img-blog.csdnimg.cn/img_convert/508ac78b00476ad4fd7ede5004f828f5.png)

图7是本发明的V2X协议栈文件目录结构示意图。