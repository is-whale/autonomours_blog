- [数字管家丨基于AI视频识别的精准公交OD技术应用 (qq.com)](https://mp.weixin.qq.com/s/jREFFjhmI8M6wrWnAjn0sw)

# 01 为什么要进行精准公交OD识别？

随着中国城镇化进入“下半场”，以往“重量轻质”的粗放式公交发展已不能满足时代的要求。同时，城市轨道交通成网运营、信息技术快速发展，使公交行业呈现出前所未有的发展趋势。

1、掌握公交出行精准OD，有效支撑公交规划及政策精细化研究工作

需求的精细化掌握，是推动网络优化与服务创新、谋求公交客流“结构性增长”的重要前提。目前，公交客流数据主要是基于电子支付的被动采集，只有上车位置，无下车位置数据，因此无法进行全出行链路的规律分析。通过刷卡数据进行OD反推，所得的OD准确率仅为60%，难以满足线网优化调整及补贴、考核等精细化的规划研究工作的要求。

2、构建大公共交通客流识别库，建立公交人物画像库，拓展更多应用场景

目前公交出行的支付途径多种多样，公交卡不与乘客唯一绑定，电子支付数据又分散在不同的运营商，因此无法建立长期跟踪、全出行链路的公交客流识别库。利用人脸识别+人物属性识别（PAR）+行人重识别（ReID）技术，可构建地面公交客流识别库；结合轨道交通以及核心城区、口岸枢纽、公交站台的行人识别数据，可构建千万级的人物画像体系，对市民进行全出行链跟踪；将交通客流识别库与商业、医疗、教育等智慧城市大数据叠加应用，可拓展更多应用场景。

# **02 基于AI视频识别的精准公交OD技术**

深城交利用计算机视觉技术与深度学习算法，开发出基于人物着装与体态特征的行人重识别算法（ReID），并创新公交OD采集的技术手段，推出基于AI视频识别的精准公交OD技术，对公交乘客进行检测跟踪，可实现OD数据的实时统计。该公交OD采集方法的技术思路可以总结为：

## 1、一个架构：云-边-端架构

基于AI视频识别的精准公交OD技术，将“云”——互联网上的云端服务器、“边”——边缘计算网关与“端”——车载智能终端设备，三者联动起来，构建成IoT（物联网，Internet of Things）核心计算能力。

![图片](https://mmbiz.qpic.cn/mmbiz_png/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXo2zMFTAChWSQUiaib0RzmoKSjLicgoLLV0Af8d41BaOcVyCXZ2yMGDrbRA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图1 云-边-端架构图

## 2、三套算法：乘客检测跟踪算法、行人重识别算法、OD关联算法

**乘客检测跟踪算法：**通过目标跟踪网络检测跟踪每个乘客的运动轨迹，利用乘客上下车判定策略，判断视频中检测跟踪到的乘客上下车状态，并存取运动过程中乘客的高质量快照。

![图片](https://mmbiz.qpic.cn/mmbiz_png/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXo2kFvibqkUXDHYOFuTqEMSDwUApRQkvDTjbvnA3JYibFEWxpflUbibibbxg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图2 上车乘客目标跟踪

![图片](https://mmbiz.qpic.cn/mmbiz_png/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXo5Z13CWgTmiaCXiaylJEMsbbfNEHB4MoTlzRM96WASBkZLKicR5ia0Ro2zg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图3 下车乘客目标跟踪

**行人重识别算法：**对快照图片进行实时特征提取，形成特征向量，并进行相似度计算和匹配。同时，通过乘客上下车时间缩小相似度匹配的范围，提高相似度匹配的精度。

![图片](https://mmbiz.qpic.cn/mmbiz_png/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXoOnCic6VQsbM5k3h9tdI6QYsKCOpX0dBAg8DVYuaSxvicoUqpfpicRAEjQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图4 ReID数据集样例展示

**OD关联算法：**根据行人重识别算法得到的乘客上下车时间，与公交车到离站时间、站名和运行路段等信息进行关联，可以分析得出每个乘客出行轨迹，从而得到乘客出行的精准公交OD。

![图片](https://mmbiz.qpic.cn/mmbiz_png/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXoUkuyxU30Vff4xRwvTibnMQKdpRV1rMjydwuOs3QnZs9t4NpZRQhU7cA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图5 上下车乘客特征匹配及OD关联

# 03 精准公交OD技术的应用效果如何？

实现同一公交车内一趟次视频，乘客检测跟踪精度超过90%，乘客相似度匹配精度超过90%，OD分析平峰期准确率超过80%，高峰期准确率超过70%；跨公交车OD分析，准确率超过60%。

目前工业界宣传的视频OD识别最好水平为80%，而相关的视频识别产品进行客流采集和OD识别的测试结果，其识别率和匹配率测试最好结果均低于80%，整体OD识别精度低于60%。可见，深城交的基于AI视频识别的精准公交OD技术处在业界较高的水平。

该项技术调查周期短、效率高、准确度高、自动化程度高，可大幅降低人工成本，并在短时间内积累大量的公交OD数据，为公交线网规划、线路站点调整、优化出行服务提供数据支撑。

![图片](https://mmbiz.qpic.cn/mmbiz_gif/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXoa8NggeaSxktuLT3AibpjHV7iaDuuAcPoGpyaJvOHyz8mFziaDYQlkMMNg/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

图6 基于AI视频识别的精准公交OD技术

# 04 精准公交OD的技术创新

相对于市场上其他视频识别技术，本次研究的主要创新点可总结为以下三点：

- 构建实际场景的多目标追踪/行人重识别/行人属性识别数据集，提升模型的泛化性能；
- 研究现有深度学习模型在公交车内场景下的不足，对网络结构和损失函数进行改进提升模型的性能；
- 针对行人密集、行人遮挡、光照变化等场景进行了改进提升。

# 05 精准公交OD技术的应用场景

为了强化公交治理业务创新，提升行业管理效能以及公交资源配给的科学化、精细化和智能化水平，让城市公交系统运转得更聪明、更智慧、更高效，深圳市开展交通运输一体化智慧平台建设项目。深城交在平台的初步设计过程中，将基于AI视频识别的精准公交OD技术广泛地应用到常规公交资源智能化调控与决策支持的各个领域。

## 1、公交线网

依托精准公交OD数据，进行走廊和片区的公交客流分析，可以实现将地面公交线路优化拓展到全公交网络优化。

![图片](https://mmbiz.qpic.cn/mmbiz_png/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXoRwibgz3VREicQx9IkxQgg7fiaZPdwTiaP62fqfCj2cvuwIExxQBP8bkibsw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

![图片](https://mmbiz.qpic.cn/mmbiz_png/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXohjaVApBV3uriaica05E3kT1bP0qogo7Vytaic6Zjr81zGzeNuPjQEKEhQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图7 公交线路、线网评估优化

面向线网优化：实现减少非高峰时段供给，减少通道同质化供给，逐步减少拟取消线路的供给。

![图片](https://mmbiz.qpic.cn/mmbiz_png/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXoVo1mSxsp2Qkj3RiaGG7IpB2NVg8iahNC8S4UqxmWkzEWcwhhuluTncBg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图8 公交线路的优化区间示意图

（来自深城交TransPaas平台）

面向两网融合：基于公交-轨道线网空间布局、客流特征，对“公交-公交”、“公交-轨道”线网之间的竞合关系进行评估，识别重复、衔接、运力互补以及可替代性等关系的线路，为公交线网“减重增效”提供支撑。



![图片](https://mmbiz.qpic.cn/mmbiz_gif/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXor4cdms42EkvW2kvbfVlBDiaYEpBgjK3o35kwjfsX98CztAn2bVtaxEA/640?wx_fmt=gif&wxfrom=5&wx_lazy=1)

图9 “公交-轨道”线网之间的竞合关系评估

面向轨道接驳：通过诊断接轨道驳存在的短板，增加接驳供给，增加区域连接服务供给，为强化轨道接驳服务提供支撑。

![图片](https://mmbiz.qpic.cn/mmbiz_png/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXo4GA8fsrg7jUiacMZ2AiaAzmqAhaz32CUkZgMR5gKIoIHVUSQicsn4Yicgw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图10 轨道接驳覆盖程度评估

## 2、票价与补贴

依托精准公交OD数据和深圳通数据，可进行人卡匹配的客流分析，实现面向细分群体、面向乘客活动链与需求的票价制定和补贴优化。

**面向票价优惠补贴：**优化公益性公交线路的票价优惠补贴，从普惠式补贴转向特定人群及通勤、通学出行等特定需求的精准补贴。

**面向公交定价机制：**优化公交线路定价机制，推行“按里程计价、市民多乘多花、企业合理盈利”的公交线路定价模式。

![图片](https://mmbiz.qpic.cn/mmbiz_png/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXo5icWDAia8338b5NofJgXRt3ho3yQQfR2HNVH2hq5iaWuktodiboUkfQMmA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图11 深圳市第四轮公交票价及补贴发展方向

## 3、公交运力

依托精准公交OD数据和企业运营调度数据，可以优化运力空间布局，协助公交企业制定运力配置计划。

**面向运力布局：**“方向不均衡系数”是高峰时段区域间公交往返客流的比值，围绕“线-廊-网”，对高峰期各区域的“方向不均衡系数”进行评估，识别“最不均衡”的线路和廊道，为提升运力配置效能提供支撑。

![图片](https://mmbiz.qpic.cn/mmbiz_png/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXoyvDRL0ibopjbrXxcSSna6JhbCoSCglcicyicJfX3iaeMsYZC08jyiadKXxg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图12 深圳市各片区早高峰方向不均衡系数

（箭头所指为大客流方向）

**面向班次配置：**由“线路级”转向“班次级”的精细化服务水平评估，支撑公交运行仿真平台搭建，实现公交运行的精准推演，推动公交服务水平考核评价升级。支撑线路运营方式优化，如增减班次、开行区间车等。支撑车型结构优化，如实现公交小型化。

![图片](https://mmbiz.qpic.cn/mmbiz_png/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXofGkrdtCKHticdcIJib2oqeEhxvjalbqzNso6alyia91Q1zw3JKvCmKUTQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图13 公交“班次级”运行评估

![图片](https://mmbiz.qpic.cn/mmbiz_png/KW0w5hqe9VxbuMw03bnAvQJibcLIiaUsXo81icIZAmlXnsW4Bz0oMdBzyD8fGvAsz1n6HXccOJeYeAZ8Cfj3nYdUQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图14 班次级满载率评估、客流效益评估

# **结语**

结合国家在人工智能、5G、物联网方向上的发展战略，深城交突破行人重识别（ReID）、云-边-端一体化等关键技术，在大数据实验室搭建模拟场景进行实践测试，旨在建立精准公交OD识别核心技术体系，精准识别市民公交出行需求，加速推进地面公交与轨道两网融合发展，完善公交票价与补贴制定体系，优化公交运力配置，加快智慧公交在城市的产业落地，推动与之相关的上下游企业的快速发展，促进综合、智慧、绿色、安全的“公交都市”建设。