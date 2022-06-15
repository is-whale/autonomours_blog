- [如何学习百度apollo无人驾驶预测模块？ - 知乎 (zhihu.com)](https://www.zhihu.com/question/360069581/answer/2528458772)

作者：iGear

## **引言**

自动驾驶主车（Autonomous Driving Car ，ADC）行驶时，周围的车辆及行人在接下来的几秒内将要做什么？是否有碰撞的可能？这对于实现安全的自动驾驶而言至关重要，这也是自动驾驶领域中的轨迹预测模块的问题：**对周边车辆、行人在接下来数秒时间的多种行为状态进行预测**，进一步影响主车的路径规划。

近几年中自动驾驶行为预测领域很火的一种方式是——采用**类似VectorNet**（《VectorNet: Encoding HD Maps and Agent Dynamics from Vectorized Representation》）这个分层的[图神经网络](https://www.zhihu.com/search?q=图神经网络&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2528458772})的思想，将道路等静态环境信息以及动态交通参与者的运动轨迹均进行编码，编码后经过**类似TNT** （《TNT:Target-driveN Trajectory Predictio》）思想的方式进行轨迹预测。

2021年12月29日，Apollo7.0版本正式发布。本次7.0版本预测模块更新部分的核心思路与上述两篇论文非常接近，但主要区别在于使用MLP（多层感知机）替代了GCN（图神经网络），以及增加了更加丰富的工程化的优化方式。

**本文就Apollo7.0的预测模块及其所涉及技术进行分享。**

注：文章图片来自Apollo7.0技术分享课内容及相关论文，参考文献附于文末。

## 预测模块概述

###  **整体框架**

绿色表示预测模块所依赖的上游模块，包括了规划、定位、感知、Storytelling和高精度地图，下游是规划模块。

红色虚线框内是预测模块的整体逻辑，包含**容器（Container），场景（Scenario）、评估器（Evaluator）和预测器（Predictor）。**

![img](https://pic2.zhimg.com/50/v2-3e5c49b588dfbc204ee0c0a51aef909d_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-3e5c49b588dfbc204ee0c0a51aef909d_720w.jpg?source=1940ef5c)

### **容器 Container**

输入上游的一系列信息，输出障碍物、主车和相关联车道信息。

![img](https://pic1.zhimg.com/50/v2-a7462e97fd7d5aaa9156f673fae81d22_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-a7462e97fd7d5aaa9156f673fae81d22_720w.jpg?source=1940ef5c)

### **场景 Scenario**

输入障碍物车状态、主车状态及相关车道信息，输出场景相关联信息。**其中interaction（交互标志位）是7.0中新增加的一部分。**

![img](https://pica.zhimg.com/50/v2-2ae21045a7f2b2fcc7720f403aab7037_720w.jpg?source=1940ef5c)![img](https://pica.zhimg.com/80/v2-2ae21045a7f2b2fcc7720f403aab7037_720w.jpg?source=1940ef5c)

**6.0和7.0的区别**

6.0 的思想是将车辆及人过去的状态和位置作为输入，放在神经网络中，得到一个未来的预测，实际上还是[拟合函数](https://www.zhihu.com/search?q=拟合函数&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2528458772})的核心思想。

7.0中会先根据障碍车的位置判断是不是交互式的车辆（危险等级为ineraction及caution），然后针对交互式的车辆用交互式模型。

如果是其他类型的车辆（normal），使用巡航MLP评估器及路口MLP评估器，对于不在道路上的，使用[卡尔曼滤波器](https://www.zhihu.com/search?q=卡尔曼滤波器&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2528458772})。所以因此7.0更加细分了感知障碍物的类别判断，新增的一类使感知预测更加细致，提升点在于有针对性，预测效果也更好。

### **评估器 Evaluator**

输入障碍物的信息及场景信息，评估器为机器学习的模型，输出障碍物的轨迹或意图，其中障碍物意图是指障碍物在每个车道序列的概率。

![img](https://pic2.zhimg.com/50/v2-e112b57fdc58233a28ccfc95234c5dee_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-e112b57fdc58233a28ccfc95234c5dee_720w.jpg?source=1940ef5c)

**障碍物主要分为四类：自行车、行人、车辆、未知障碍物**（感知模块中未检测出的）。

在预测模块中，不同的障碍物类型对应不同的评估器和预测器。

关于不同评估器的介绍，可参考：[王方浩：apollo预测模块分享（二十一）](https://zhuanlan.zhihu.com/p/367557601)

> *知识点：障碍物*
> *感知侧输入的障碍物分为静态障碍物和[动态障碍物](https://www.zhihu.com/search?q=动态障碍物&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2528458772})，只有动态障碍物会进入预测模块，静态障碍物不做处理，打包后输出至规划侧。*

**这部分中Apollo6.0和7.0的结构区别**

车辆（vehicle）部分中，Apollo6.0版本先将其分为onlane和[injunction](https://www.zhihu.com/search?q=injunction&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2528458772})，再按caution和normal然后去做划分。而Apollo7.0版本直接做等级的预分，只在normal的情况下做 onlane和injunction 的划分。

此外7.0版本中多了interaction的等级，其和[caution](https://www.zhihu.com/search?q=caution&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2528458772})均指向JOINTLY_PREDICTION_PLANNING_EVALUATOR。6.0版本中的onlane情况下的caution 和 normal 均指CRUISE_MLP_ EVALUATOR。

![img](https://pic3.zhimg.com/50/v2-35d15245de81ff85963c6486af8083b3_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-35d15245de81ff85963c6486af8083b3_720w.jpg?source=1940ef5c)

**预测器 Predictor**

[预测器](https://www.zhihu.com/search?q=预测器&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2528458772})是在Evaluator获取预测轨迹或意图后，进行轨迹的延伸或者生成，处理完后最终生成8秒的轨迹。

![img](https://pic4.zhimg.com/50/v2-fa9d2e15ddab618d0886c059f73228aa_720w.jpg?source=1940ef5c)![img](https://pic4.zhimg.com/80/v2-fa9d2e15ddab618d0886c059f73228aa_720w.jpg?source=1940ef5c)

预测器分类如下所示：

![img](https://pic1.zhimg.com/50/v2-f16a5c5da24db5c168ea9a7e70ee8ae4_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-f16a5c5da24db5c168ea9a7e70ee8ae4_720w.jpg?source=1940ef5c)

![img](https://pic3.zhimg.com/50/v2-375ca155041bbdea9d724bc27804ae3f_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-375ca155041bbdea9d724bc27804ae3f_720w.jpg?source=1940ef5c)

## **环境信息编码**

### **为什么要编码？**

最初对障碍车辆的预测，只是依赖于障碍车自身的运动状态，但障碍车的运动状态与当下所处环境是密不可分的。例如，当下障碍车在一个路口的右转车道上，可以判定其基本上要右转，如果在直行车道上，则很可能直行。

**编码的作用是把道路之类的信息以形成特征的方式保存，以此决定障碍车的预测轨迹。**

此外，当主车ADC的planning轨迹有可能和障碍车辆产生交互时，例如主车需要变道、加塞时，也会对周边车辆产生影响，因此[编码流](https://www.zhihu.com/search?q=编码流&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2528458772})中也会包含主车轨迹。

### **如何编码？**

在Apollo7.0的设计中，如下图所示，蓝色的表示人行道，四个向量的四条边均是有效的，连接后用以表示人行道；道路边缘是指polyline（下图中的[多线段](https://www.zhihu.com/search?q=多线段&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2528458772})），也是以向量的方式形成；障碍物（obstacle）轨迹和自车 ADC 轨迹同理。

用这种方式，可以将左边的仿真图变成了右边编码后的图。

![img](https://pic2.zhimg.com/50/v2-64c381a7dd512232da5c8b6e44b7897b_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-64c381a7dd512232da5c8b6e44b7897b_720w.jpg?source=1940ef5c)

**具体的结构化数据存储信息编码过程如下：**

其中，Xs,Ys为起始点的横纵坐标，Xe,Ye 为结束点的横纵坐标， attr是需要额外放置的属性，lane_type 是判断线端是什么类型，虚线or实线，[双黄线](https://www.zhihu.com/search?q=双黄线&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2528458772})or单黄线，id是指每条路的标识。

障碍物中的l,w，对应路网的 0,0，是指障碍物的长和宽。

![img](https://pic3.zhimg.com/50/v2-98194df4b7d1f832a62ca86a2544c69a_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-98194df4b7d1f832a62ca86a2544c69a_720w.jpg?source=1940ef5c)

信息编码完成之后，我们可以将其抽象的理解成这幅彩虹图，每个图都是由多个矢量组成的，不同颜色的方块都表示不同的九维矢量（红色为主车轨迹，黄色为障碍物轨迹，绿色为道路，蓝色为人行道）。每一列可以描述为多线段，也就是多矢量的一个线段，它表示是一个完整的物体。

![img](https://pic2.zhimg.com/50/v2-325b92fc1e31b4a04b5f3e626c79da93_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-325b92fc1e31b4a04b5f3e626c79da93_720w.jpg?source=1940ef5c)

### **编码后总体结构**

编码后形成的不同颜色的色块。每一个色块其又由多种子元素构成。例如绿色（表示道路）由三段构成，轨迹则被分为两段。

之后需要做的事情有：

**1.形成子图**

**2.全局图的交互**

**3.输出最终的语义地图**



![img](https://pic3.zhimg.com/50/v2-46e06618382fb3f908b806c380d04d37_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-46e06618382fb3f908b806c380d04d37_720w.jpg?source=1940ef5c)

**1.子图**

子图：每一个向量特征进行处理

在子图阶段会每一个元素进行一次全连接，让每个元素之间产生联系。例如，其将红色人行道（crosswalk）的每一条边全连接在一起，lane和轨迹也是如此。

子图的网络架构具体如下：

![img](https://pic3.zhimg.com/50/v2-992fa1d1f0364142b06ca991da92115a_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-992fa1d1f0364142b06ca991da92115a_720w.jpg?source=1940ef5c)

原文公式：

![img](https://pica.zhimg.com/50/v2-9c8d9c4cf81c42c75ce1527eb51d4448_720w.jpg?source=1940ef5c)![img](https://pica.zhimg.com/80/v2-9c8d9c4cf81c42c75ce1527eb51d4448_720w.jpg?source=1940ef5c)

翻译为人话的公式：

![img](https://pic1.zhimg.com/50/v2-4e9f0b0ef99f748ea4a8441aff7edf38_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-4e9f0b0ef99f748ea4a8441aff7edf38_720w.jpg?source=1940ef5c)

Node Encoder 这里用的是MLP，就是全连接的一个网络结构，将数据相关联并提升到高维空间。

Permutatation Invariant Aggregator 使用的是**Max Pooling，最大值池化。**

对Max Pooling的更深入了解可参考：[对Max Pooling的理解_117瓶果粒橙的博客-CSDN博客_maxpooling](https://link.zhihu.com/?target=https%3A//blog.csdn.net/weixin_41513917/article/details/102514739)

这里在VectorNet中，在MLP上包裹了一层GCN，从而使有连接关系的节点（每一条轨迹都由带有前后指向关系的节点组成）能够进行特征层面的传播。

Apollo这里单纯采用了MLP，我们猜测可能会在特征层损失一部分向量信息（节点与节点的连接关系）。

但当下核心的问题是，当我们考虑部署的情况时，我们需要将我们的模型使用TorchScript的jit方法导出成一个中间状态，才可以以ONNX的形式或在C++平台上部署。GCN当前对TorchScript的导出还不友好，比如GCN通常采用torch_gemotric.data 来进行数据输入，而如果想通过TorchScript进行导出，则只支持[tensor](https://www.zhihu.com/search?q=tensor&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2528458772})的形式；以及在某些算子层，TorchScript也不直接支持GCN的导出。因此单独使用MLP反而是一个更加朴适的方法，这是我们对这一个部分的理解。

在下一篇文章中，我们则会全面展开着在GCN部署时遇到的问题，我们的优化历程，以及如何end-end考虑预测模块模型的设计与部署。

**2.全局图**

全局图：对所有的向量进行处理

这部分是对每个完整的物体做处理，这一部分会对所有的物体都会做一个交互。这里交互既包含了车辆，也包含了路网在内。

![img](https://pica.zhimg.com/50/v2-eea43ae22c538dea511bc326f955f552_720w.jpg?source=1940ef5c)![img](https://pica.zhimg.com/80/v2-eea43ae22c538dea511bc326f955f552_720w.jpg?source=1940ef5c)

原文公式：

![img](https://pic1.zhimg.com/50/v2-81abb8e5749f2f9ed870851aec565159_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-81abb8e5749f2f9ed870851aec565159_720w.jpg?source=1940ef5c)

![img](https://pic2.zhimg.com/50/v2-df1ab0ce0daa99d46188a204b44f2c77_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-df1ab0ce0daa99d46188a204b44f2c77_720w.jpg?source=1940ef5c)

翻译为人话的公式：

![img](https://pic3.zhimg.com/50/v2-f0fab822262ceff49992b9a000ab2ab1_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-f0fab822262ceff49992b9a000ab2ab1_720w.jpg?source=1940ef5c)

MultiHeadattention的具体操作就是把Attention加在一起。Attention是指之前的特征（P）经过Q 矩阵，T K矩阵，V 矩阵，之后再做softmax合在一起。

说到这里大家有点晕，我们重新梳理一下：

基本意思是指第一层进来的东西，经过Max Pooling,再经过Self Attention 运算，就会输出整体的这个结果。

> *知识点：Attention 机制*
> *对于一个图像而言，当对其进行预测，或是做类似YOLO的目标检测或图像分类，其特征是对于整张图而言的。但Attention机制下会采用某些权重比，让其对某一部分的区域产生兴趣，这部分会多学。例如照片中的某个位置有只狗，这个位置的权重相对会高一点。*
> *Apollo这里用的是 self Attention，核心思想是使全局图的元素产生更多的交互，产生更多的兴趣点。*
> *此处我们不再展开讲解Self-Attention, 想要补充相关知识的同学可以观看[李宏毅](https://www.zhihu.com/search?q=李宏毅&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A2528458772})老师的关于这一部分的教学片，非常详细也非常清楚。*

**3.输出最终的语义地图**

最后输出为每一个物体所对应的特征向量，然后取出需要的特征向量进入下一步处理流程。

![img](https://pic1.zhimg.com/50/v2-df484fd6cf26217f8a48c84b760bd55e_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-df484fd6cf26217f8a48c84b760bd55e_720w.jpg?source=1940ef5c)

## **轨迹生成器**

《VectorNet: Encoding HD Maps and Agent Dynamics from Vectorized Representation》这篇论文原文在经过编码后，直接使用了比较通用的方法作为解码器，生成目标预测的轨迹点。

而Apollo7.0中经过编码后，使用**类似于TNT (**Target-driveN Trajectory Prediction)思想的方式进行轨迹预测。

总体结构如图：

![img](https://pic1.zhimg.com/50/v2-711b6376821397386d177ef21bc63bd7_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-711b6376821397386d177ef21bc63bd7_720w.jpg?source=1940ef5c)

在实际道路上采一些样点，我们称其为**候选点**（图中的菱形样点），根据**候选点**，预测可能会到达的**选中点**（图中所示的星点），最后由可到达的**选中点**，决定可能会生成何种轨迹。

总结而言，就是从一堆点中学习出一些真正可能去到的**选中点**，再学习出从当前位置到**选中点**的轨迹。

预测任务分为三步：

**1.给定环境的context，估计每个候选点的可能性，从而选择概率高的候选点，下图分别用钻石和星星表示候选点和选中点**

**2.根据目标，估计每个选定目标的轨迹（分布）**

**3.对所有的轨迹进行排名的评分和选择**

![img](https://pic2.zhimg.com/50/v2-dc1f2cd7aad64b2a5772458a6e849207_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-dc1f2cd7aad64b2a5772458a6e849207_720w.jpg?source=1940ef5c)

**Target -> 轨迹 -> 轨迹概率分布 -> 轨迹交互**

### **Target**

假设过去的状态：



![img](https://pic1.zhimg.com/50/v2-b630f3539c9345535539c22de127e17b_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-b630f3539c9345535539c22de127e17b_720w.jpg?source=1940ef5c)

预测未来的状态：

![img](https://pic3.zhimg.com/50/v2-9001e07b6c421388ef79647457747cce_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-9001e07b6c421388ef79647457747cce_720w.jpg?source=1940ef5c)

Cp 代表着此时的背景（环境）

用真实的Target拼接特征向量来生成预测的轨迹，用生成的轨迹和真实轨迹做一个Huber loss。

使用X=（Sp，Cp）表示所有的过去状态，就是要尝试估计 p(sF |x)：给定过去状态未来状态的边缘概率。

![img](https://pic2.zhimg.com/50/v2-ff1349658237792ac6471fdcd5307606_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-ff1349658237792ac6471fdcd5307606_720w.jpg?source=1940ef5c)

这里的核心思想是，通过设计目标空间τ(Cp) (候选点位置), 候选点分布p(τ|x)可以很好地捕捉意图不确定性。一旦确定了目标，进一步证明了控制不确定性（轨迹）可以通过简单的单峰分布可靠地建模。通过一组离散位置来近似目标空间τ(Cp)，将p(τ |x)的估计转化为一个分类任务。

![img](https://pica.zhimg.com/50/v2-b477c1cf9afade505b75fa408dfd6ced_720w.jpg?source=1940ef5c)![img](https://pica.zhimg.com/80/v2-b477c1cf9afade505b75fa408dfd6ced_720w.jpg?source=1940ef5c)

![img](https://pic1.zhimg.com/50/v2-e2f1df439b4f29675e0bd01c5a2a31f8_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-e2f1df439b4f29675e0bd01c5a2a31f8_720w.jpg?source=1940ef5c)

![img](https://pic3.zhimg.com/50/v2-77c3bb42d3b65d6821d2cddf036b2ae9_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-77c3bb42d3b65d6821d2cddf036b2ae9_720w.jpg?source=1940ef5c)

在以上的公式中，N 表示广义的正态分布，Huber作为距离函数，平均值表示为v。可训练功能 f和v由两层多层感知器（MLP）实现，目标坐标和场景上下文特征作为输入。他们预测目标位置上的离散分布及其最可能的偏移量。

Softmax！最终核心：分类问题，每个候选点到底哪个属于哪一个选中点（星点）。

![img](https://pic1.zhimg.com/50/v2-ae8bf1be5343adbc9801d2b6ec3492a4_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-ae8bf1be5343adbc9801d2b6ec3492a4_720w.jpg?source=1940ef5c)

Loss：Lcls是交叉熵，Loffset是HuberLoss，u是预测选中点离最近的真值选中点的距离。

![img](https://pic1.zhimg.com/50/v2-41702ebb8572ad16d16039c9b7f55d25_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-41702ebb8572ad16d16039c9b7f55d25_720w.jpg?source=1940ef5c)

Apollo7.0目前使用的是道路中心采样方式，使用其来预测车辆的轨迹。

![img](https://pic1.zhimg.com/50/v2-7bba08a7692995d295748cf156eb655d_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-7bba08a7692995d295748cf156eb655d_720w.jpg?source=1940ef5c)

Apollo7.0对于Target点的计算方式相同，不多赘述。

![img](https://pic2.zhimg.com/50/v2-334e4443504c0df3b88b002f55384a28_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-334e4443504c0df3b88b002f55384a28_720w.jpg?source=1940ef5c)

![img](https://pica.zhimg.com/50/v2-56c7c3b86d742f0e857310563b9fd79d_720w.jpg?source=1940ef5c)![img](https://pica.zhimg.com/80/v2-56c7c3b86d742f0e857310563b9fd79d_720w.jpg?source=1940ef5c)

### **Trajectories**

上一步得到选中点之后，接下来就是根据选中点生成相应的轨迹。

![img](https://pic3.zhimg.com/50/v2-a35402572ba06a5a47d451b4bee8bad8_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-a35402572ba06a5a47d451b4bee8bad8_720w.jpg?source=1940ef5c)

（TNT）原论文中的公式如下

这里存在两个假设：时间步长是条件独立的，也就是时间上步与步的预测是独立的，可以使模型避免了顺序预测，提高了计算效率；对一个选中点只有一条最大概率的Trajectory，这样的轨迹分布为正态单峰分布的，这在短期内肯定是正确的；对于较长的时间范围，可以在（中间）目标预测和运动估计之间进行迭代，这样假设仍然成立。

总而言之，通过两层的MLP来预测每一个Target的轨迹SF，它将上下文特征X和目标位置τ作为输入，并为每个目标输出一条最可能的未来轨迹。由于它是以第一阶段的预测目标为条件的，为了使学习过程顺利进行，训练时通过输入位置真值作为目标

![img](https://pic1.zhimg.com/50/v2-3dd6f3e7d85bb79e1ffa51921b49e5d4_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-3dd6f3e7d85bb79e1ffa51921b49e5d4_720w.jpg?source=1940ef5c)

损失函数则是预测轨迹与真实轨迹的差值，这么做期望达到生成轨迹最后一个点，跟Target的点尽可能的重合。

### **Distribution**

这一部分在原论文中没有提及，属于Apollo7.0版本的新增部分。

根据轨迹去计算轨迹的一个概率分布，真实分布（计算公式见下图）是指被选轨迹距离真实轨迹越近时，它的概率越高。前向计算前向计算就是计算备选轨迹的概率分布。

![img](https://picx.zhimg.com/50/v2-2ff85db3d64cb34b0986a33ff4a05123_720w.jpg?source=1940ef5c)![img](https://picx.zhimg.com/80/v2-2ff85db3d64cb34b0986a33ff4a05123_720w.jpg?source=1940ef5c)

### **Interaction**

这一部分也是Apollo7.0中新增的重要部分。

前文所述考虑了障碍车预测轨迹和环境的影响，但还需要与主车规划轨迹进行交互打分，去选择最优轨迹。例如交互前该障碍车预测轨迹为0.8 分，和主车的轨迹交互后可能不再是 0.8 分，可能会重新打分。



![img](https://pic3.zhimg.com/50/v2-75ccb320888737a3549d40bbf80e99ec_720w.jpg?source=1940ef5c)![img](https://pic3.zhimg.com/80/v2-75ccb320888737a3549d40bbf80e99ec_720w.jpg?source=1940ef5c)

**交互情况**

以下四张图为不同情况下（路口、车道行驶、换道、左转）可判断为交互车辆的情况。

![img](https://pic1.zhimg.com/50/v2-55478eef740726fb70624d098e0ed058_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-55478eef740726fb70624d098e0ed058_720w.jpg?source=1940ef5c)

![img](https://pica.zhimg.com/50/v2-8b75ece35bc68d87065519f60b3a3fe8_720w.jpg?source=1940ef5c)![img](https://pica.zhimg.com/80/v2-8b75ece35bc68d87065519f60b3a3fe8_720w.jpg?source=1940ef5c)

![img](https://pic2.zhimg.com/50/v2-2b6fdac3c2cf8d1eafbf62c3a0585af3_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-2b6fdac3c2cf8d1eafbf62c3a0585af3_720w.jpg?source=1940ef5c)

![img](https://pic4.zhimg.com/50/v2-90f23c8889f7e19ebe530ed1e098c1e2_720w.jpg?source=1940ef5c)![img](https://pic4.zhimg.com/80/v2-90f23c8889f7e19ebe530ed1e098c1e2_720w.jpg?source=1940ef5c)

**交互分数计算**

假如说50条备选轨迹，就会有50条**交互分数（用cost表示）**。是指交互的严重程度，如果cost较大，说明危险程度较大，会削弱对该条预测轨迹的发生概率。

预测轨迹与规划轨迹之间的时间对齐。预测未来3s，时间间隔0.1s, 共有30个选中点。

30个选中点之间的**相对距离（D）**累加，30个点的**相对速度（V）**累加起来，两项经过两个**条件权重（W1和 W2）**进行加权，之后相加得到**交互分数cost**。如果距离越近，那么cost越大；如果速度差距越大，那么cost也越大。

Tips：W1和 W2这两项条件权重是通过编码器的特征向量，经过MLP实现的。

![img](https://pica.zhimg.com/50/v2-edd3851a1b4cb363918c621aa41709f3_720w.jpg?source=1940ef5c)![img](https://pica.zhimg.com/80/v2-edd3851a1b4cb363918c621aa41709f3_720w.jpg?source=1940ef5c)

### **预测效果演示**

第一张图为最原始的环境编码后的矢量图。

第二张图为选中点预测结果（即上文所述的星点）。

第三张图是备选轨迹生成，根据每个选中点的位置生成了相应的轨迹。

第四张图是最优轨迹的选择，通过本身的轨迹预测分数和主车轨迹的交互分数，得到一个最优轨迹。

![img](https://pic1.zhimg.com/50/v2-3f5852c9f744293c32b976daf379af33_720w.jpg?source=1940ef5c)![img](https://pic1.zhimg.com/80/v2-3f5852c9f744293c32b976daf379af33_720w.jpg?source=1940ef5c)

## **测评指标**

为衡量预测效果的好坏，（TNT）论文使用的是MR、MinFDE、MinFDE和DAC几个指标来进行衡量。

**MR (Miss Rate，缺失率)** ，描述检测结果中的漏检率的指标。

**minFDE( Minimum Final Displacement Error , 最小最终距离误差)**，对于 N 个预测轨迹，选择最终轨迹预测点与真值预测误差最小的作为评估结果

**minADE( Minimum Average Displacement Error，最小平均距离误差 )**，对于 N 个预测轨迹，选择平均轨迹预测点与真值预测误差最小的作为评估结果。

MinFDE只能评估最好的估计有多好，但不能评估所有轨迹的优劣。

因此提出新的标准**DAC(Drivable Area Compliance ，最小最终距离误差）**。如果模型产生n个可能的未来轨迹，并且其中m个轨迹在某个点离开可移动区域，则该模型的DAC为（n m）/ n。因此，较高的DAC意味着更好的预测轨迹质量。

如果存在n个样本，并且其中m个具有其最佳轨迹的最后一个坐标距离地面真值超过2.0 m，则未命中率为m / n。

表2及表3分别为在地图采样时样点取点距离的效果对比，表2为车辆检测中取样的效果，可以看出每1m取一个样点可性价比最优。表3为行人检测中的取样效果，可以看出每0.5m取一个样点可性价比最优。

![img](https://pic2.zhimg.com/50/v2-b926d3813dfdc6bbf73296b7cc23f7cf_720w.jpg?source=1940ef5c)![img](https://pic2.zhimg.com/80/v2-b926d3813dfdc6bbf73296b7cc23f7cf_720w.jpg?source=1940ef5c)

从下图的检测结果的测评指标来看，TNT的效果较好。

![img](https://pica.zhimg.com/50/v2-bf076cd3f1c86eb07dbece79470b219e_720w.jpg?source=1940ef5c)![img](https://pica.zhimg.com/80/v2-bf076cd3f1c86eb07dbece79470b219e_720w.jpg?source=1940ef5c)

当前Apollo 7.0的轨迹预测由于有许多工程化方法，尚未提供量化分析其性能的方法或指标。我们可以通过剥离其代码，使用到传统轨迹预测的评测指标上来。但不可否认的是，由于TNT本身方法在之前轨迹预测中表现较好，且其改进版本DenseTNT也拿到了相关比赛的第一名，我们认为该方法对于准确性是有依托的。

## 结语

对Apollo7.0的预测模块及所涉及技术进行探讨分享全文结束

码字不易，更新虽慢，但每篇都是用心之作。希望可以帮到屏幕前正在阅读的你，也欢迎多多转发，分享，讨论~

再次声明：文章图片来自Apollo7.0技术分享课内容及相关论文。参考文献附于文末。

**参考文献**

Gao J , Sun C , Zhao H , et al. VectorNet: Encoding HD Maps and Agent Dynamics from Vectorized Representation[J]. 2020.

Zhao H , Gao J , Lan T , et al. TNT: Target-driveN Trajectory Prediction[J]. 2020.

-----本文首发于公众号i车Gear联，转载需标明出处