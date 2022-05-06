- [ C-V2X通信架构中，PC5接口和V5接口的区别是什么？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/389948596)

通信学基础比较弱，看不是很懂， 能否通俗解释一下，非常感谢。

V5：UE中V2X应用之间的接口。

PC5：使用V2X业务UE之间用户面进行D2D（Device to Device）直接通信的接口。

![img](https://pic1.zhimg.com/v2-1ee73c70db1ecc488e4587cb3e024370_b.png)

C-V2X包含两种通信接口：
1）PC5（[直连通信接口](https://www.zhihu.com/search?q=直连通信接口&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1896606738})）：终端与终端之间的通信接口，即车、人、道路基础设施之间的短距离直接通信接口；其特点是：通过直连、广播、网络调度的形式实现低时延、高容量、高可靠的通信。

2）Uu（[蜂窝网通信接口](https://www.zhihu.com/search?q=蜂窝网通信接口&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1896606738})）：终端和基站之间的通信接口；其特点是：实现长距离和更大范围的可靠通信；

两种通信接口的使用条件：
Uu接口：当支持C-V2X的终端设备（如车载终端、智能手机，路侧单元等）处于基站的蜂窝网络覆盖范围内时，在蜂窝网络的控制下方可使用；

PC5接口：无论是否有蜂窝网络覆盖均可采用PC接口进行V2X通信。

C-V2X 将Uu接口和PC5接口相结合，彼此相互支撑，形成有效冗余来保障通信可靠性。

--------详细解释如下：----------------------

C-V2X，C 即 Cellular，V2X 就是 vehicle-to-everything，指车与外界的信息交换，它是基于[蜂窝网络](https://www.zhihu.com/search?q=蜂窝网络&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1896606738})的车联网技术。 

C-V2X 指从 LTE-V2X 到 5G V2X 的平滑演进，它不仅支持现有的 [LTE-V2X](https://www.zhihu.com/search?q=LTE-V2X&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1896606738}) 应用，还支持未来 5G V2X 的全新应用

C-V2X 主要包括 V2N（车辆与网络 / 云）、V2V（车辆与车辆）、V2I（车辆与道路基础设施）和 V2P（车辆与行人）之间的[连接性](https://www.zhihu.com/search?q=连接性&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1896606738})。

3GPP 在 Rel. 14 版本中增加了基于 LTE 系统的 V2X 服务标准研究，即 LTE-V2X，国内多家通信企业（华为、大唐、中兴）参与了 LTE-V 标准制定和研发。2016 年 9 月，首版涵盖了 V2V 和 V2I 的 V2X 标准发布；2017 年 6 月，进一步增强型 V2X 操作方案发布。在 Rel. 14 中，V2V 通信基于 D2D（ Device-to-Device）通信，其为 Rel.12 和 Rel.13 版本中的 Proximity Services (ProSe) 近距离通信技术的一部分。**新的 D2D 接口被命名为 PC5 接口**，**以实现可支持 V2X** 要求的增强型功能，这些增强型功能包括：支持高达 500Km / h 的相对车速、支持 eNB 覆盖范围内的同步操作、提升资源分配性能、[拥塞控制](https://www.zhihu.com/search?q=拥塞控制&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1896606738})和流量管理等。

 在 Rel. 14 中，LTE-V2X 主要有两种操作模式：通过 PC5 接口点对点通信（V2V）和通过 LTE-Uu 与网络通信（V2N）。

基于 PC5 接口的 V2V 通信也包括两种模式：管理模式（PC5 Mode 3）和非管理模式（PC5 Mode 4），当网络参与车辆调度时称为管理模式，当车辆独立于网络时称为非管理模式。

在管理模式下，**通过 Uu 接口的控制信令由基站（eNB）辅助进行流量调度和干扰管理**。

在非管理模式下，基于车辆间的[分布式算法](https://www.zhihu.com/search?q=分布式算法&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1896606738})来进行流量调度和干扰管理；



![img](https://pic3.zhimg.com/50/v2-f2f805738e63f3c20e035ab11a388135_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-f2f805738e63f3c20e035ab11a388135_720w.jpg?source=1940ef5c) V2V 车辆间通信的两种模式



C-V2X 还将持续平滑演进到 5G V2X，将对功能进一步增强，以支持低延迟和高可靠性 V2X 服务。

 除了 PC5 和 Uu 接口，C-V2X 技术构架还包括 V2X 控制功能、边缘应用服务器和 V2X 应用服务器。

![img](https://pic3.zhimg.com/50/v2-05de703a26d7540f592f72e886842644_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-05de703a26d7540f592f72e886842644_720w.jpg?source=1940ef5c)C-V2X 技术构架，来源 ngmn V2X 白皮书


V2X 控制功能（V2X control function）位于核心网，其为实现 V2X 通信向 UE 提供必要的参数以执行相关网络动作。

边缘应用服务器靠近数据源部署，解决了时延和网络负荷问题，将在许多 V2X 用例（比如实时高清地图更新等）中发挥重要作用。

V2X 应用服务器可部署于网络之外，由车企、移动运营商或第三方来运营，从而跨运营商跨车厂，这也解决了过去车企担心的依赖 C-V2X 会导致自动驾驶业务被电信运营商所控制的问题。

