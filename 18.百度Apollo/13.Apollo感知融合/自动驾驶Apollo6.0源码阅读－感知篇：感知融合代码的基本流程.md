- [自动驾驶Apollo6.0源码阅读－感知篇：感知融合代码的基本流程_洪山橋嬉皮的博客-CSDN博客_apollo代码阅读](https://blog.csdn.net/ControlLearner/article/details/122973217?spm=1001.2101.3001.6650.7&utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~default-7-122973217-blog-123143893.pc_relevant_multi_platform_whitelistv1&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~default-7-122973217-blog-123143893.pc_relevant_multi_platform_whitelistv1&utm_relevant_index=10)

# Fusion

```bash
── fusion
   ├── app 入口
   ├── base 定义的数据类(需清晰了解)
   ├── common 证据推理，IF，KF等方法
   └── lib
       ├── data_association 
       │   └── hm_data_association 关联匹配算法
       ├── data_fusion
       │   ├── existence_fusion
       │   │   └── dst_existence_fusion 存在性证据推理更新
       │   ├── motion_fusion
       │   │   └── kalman_motion_fusion 卡尔曼更新运动属性
       │   ├── shape_fusion
       │   │   └── pbf_shape_fusion     更新形状属性
       │   ├── tracker
       │   │   └── pbf_tracker          航迹类
       │   └── type_fusion
       │       └── dst_type_fusion      类别证据推理更新
       ├── dummy 
       ├── fusion_system
       │   └── probabilistic_fusion 概率融合入口
       ├── gatekeeper
       │   └── pbf_gatekeeper 门限
       │       └── proto
       └── interface
```

## Fusion模块在哪儿启动？

(dag文件有一个或者多个`module_config`，而每个module_config中对应一个或者多个components。)
关键概念：[component](https://so.csdn.net/so/search?q=component&spm=1001.2101.3001.7020)

Perception 是一个大的 Component，它包含了很多子 Component，而数据融合作为一个子 Component 存在;
通过`modules/perception/production/dag/dag_streaming_perception.dag`中定义：

![在这里插入图片描述](https://img-blog.csdnimg.cn/cc987db9576545909197ba2e1e01ca78.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

所以我们需要去找`FusionComponent`，并且知道了其配置在`fusion_component_conf.pb.txt`中：

![在这里插入图片描述](https://img-blog.csdnimg.cn/3fb5b8b4866b4b8ab34d251b1a8c00ef.png)

能够得到以下信息：

1. 融合方法：ProbabilisticFusion
2. 主传感器：velodyne128
3. 融合结果存放：/perception/vehicle/obstacles

## FusionComponent的初始化

路径：`modules/perception/onboard/component/fusion_component.cc`
`FusionComponent`类主要负责接收融合所需要的来自不同传感器的目标序列信息，然后利用`ProbabilisticFusion`融合类完成实际融合操作，得到融合后的感知结果，最后把融合结果在`FusionComponent`发布，供其他模块使用。

![在这里插入图片描述](https://img-blog.csdnimg.cn/7d44abe08b504cfdb0dfb8c23d6860d6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

比较关心的应该是`InitAlgorithmPlugin（）`内的具体方法，这个是用来**初始化 Component 涉及的算法**的。

![在这里插入图片描述](https://img-blog.csdnimg.cn/da3aa6d0e705466187efb87c0c07e725.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

**融合具体的实现方法**明显在`ObstacleMultiSensorFusion类`中。

![在这里插入图片描述](https://img-blog.csdnimg.cn/05a308508c0a497abb7e3745aadf2f9b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

(具体未完全理解，先跳过了。)ObstacleMultiSensorFusion关键在于BaseFusionSystemRegisterer类;
结合前面的配置信息，我们知道在 ObstacleMultiSensorFusion::Init(）中 fusion_ 将由 **ProbobilisticFusion** 实现，那么这个类在哪里呢？

路径：`/modules/perception/fusion/lib/fusion_system/probabilistic_fusion/probabilistic_fusion.cc`

![在这里插入图片描述](https://img-blog.csdnimg.cn/e9dbc398c4704b0e8d6d7383b80bbc3a.png)

### 概率融合方法：ProbobilisticFusion

![在这里插入图片描述](https://img-blog.csdnimg.cn/6da48e36395548cba639d86b0308eb17.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

公共的一个Init()和一个Fuse()

#### Init()

bool ProbabilisticFusion::Init(const FusionInitOptions& init_options)

主要是读取实例化参数，参数定义在：

`modules/perception/production/data/perception/fusion/probabilistic_fusion.pt`

![在这里插入图片描述](https://img-blog.csdnimg.cn/0b98ff2e531c41498cd00f9f2dd58701.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_12,color_FFFFFF,t_70,g_se,x_16)

可以看出实际融合的传感器包括lidar、radar和camera，以及实际使用的跟踪算法、数据关联算法、门限保持方法的具体类名，禁止单独创建航迹(track)的[sensor](https://so.csdn.net/so/search?q=sensor&spm=1001.2101.3001.7020)，另外还定义了其他一些参数。

成员变量有 trackers_、macher_、gate_keeper_ 这些都是目标跟踪相关的;

## Fusion 的流程[框架](https://so.csdn.net/so/search?q=框架&spm=1001.2101.3001.7020)

回到FusionComponent类，清晰的知道`核心方法为Proc`：

![在这里插入图片描述](https://img-blog.csdnimg.cn/33d663d1a20741e88f4ee775e2d98ce3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

核心的核心`InternalProc中`核心代码是：

![在这里插入图片描述](https://img-blog.csdnimg.cn/405cca48a2bb4a2e8805f5bb6fa06a1c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

又需跳转到`fusion_->Process`

![在这里插入图片描述](https://img-blog.csdnimg.cn/33d39a5c2f234173a6a1f1471b92dfc7.png)

### fusion_->Fuse()

代码中有相应的注释，大概流程主要是4步：

![在这里插入图片描述](https://img-blog.csdnimg.cn/f3b84434753c4480b4b1fa7277b9c527.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_18,color_FFFFFF,t_70,g_se,x_16)

工程量比较大，下面分开讲解。

#### 1.save [frame](https://so.csdn.net/so/search?q=frame&spm=1001.2101.3001.7020) data

保存传感器进来的一帧数据，存储到deque容器中。

PS.

1. 只有当主传感器数据进来一帧之后，将started_ = true才会存储其他传感器的数据。
2. 如果当前帧不是主传感器，不会进行后续的操作，将return.
   ![在这里插入图片描述](https://img-blog.csdnimg.cn/5119daacc247402b8b5393244da5a178.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)
3. 关键函数：**AddSensorMeasurements**
4. 执行对象是 SensorManager

![在这里插入图片描述](https://img-blog.csdnimg.cn/dea5282d780e47bfa6377fd9248a3b35.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 2.query related sensor_frames for fusion

查询所有传感器最新一帧的数据，插入到frames中，按时间排序。

关键函数“：**GetLatestFrames**

![在这里插入图片描述](https://img-blog.csdnimg.cn/463a41a4e9044e2899ac39083b370dc6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

#### 3.perform fusion on related frames

**FuseFrame**

![在这里插入图片描述](https://img-blog.csdnimg.cn/0dc743532f05486da0b41f9bdc842062.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

关键函数：**FuseForegroundTrack(frame)**

![在这里插入图片描述](https://img-blog.csdnimg.cn/4e9d859fa8414e249af46975042063f4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

前景融合主要分为四个步骤：

1.目标之间数据关联[后续展开]

2.更新和新数据匹配上的 Tracks

3.更新未和数据匹配上的 Tracks

4.为未匹配到的新数据创建新的 Tracks

#### 4. collect fused objects

![在这里插入图片描述](https://img-blog.csdnimg.cn/6a954b3974a7453da55ae9558038cb4d.png)

先通过 `gate_keeper` 判断能不能将数据发布出去，如果能的话再执行 CollectObjectsByTrack 方法。

大致规则：

```bash
1. 不在视野范围内 Lidar、Camera、Radar 数据不能发。
2. 前向 Radar 不能发。
3. 后向雷达目标 Range 要大于指定阈值，速度的 Norm 值要大于 4,Track 概率的置信度要大于阈值。
4. Camera 要发数据的话，要保证是 3d 数据（深目相机），当然 TrafficCone 也就是锥形桶可以发，其它的类要求比较严格，要保证距离大于阈值，并且在夜晚环境不能发。
```

决定好哪些 FusedTrack 数据可以发之后，通过 ProbabilisticFusion::CollectObjectsByTrack() 执行最后的操作。 代码比较简单，就是一些简单的赋值动作。

## 发送结果

Fusion 执行完毕后，将视线跳转到 FusionComponent::Proc() 中来。

![在这里插入图片描述](https://img-blog.csdnimg.cn/7d311255201b4b219f403e7654062b7f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

将融合后的数据发送出去。 自此，单个周期的数据融合代码流程就分析完毕。

## 参考资料

关于[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020)启动模块的一些内容,更好的了解函数从哪里开始执行的

[自动驾驶 Apollo 源码分析系列，感知篇(二)：Perception 如何启动？](https://frank909.blog.csdn.net/article/details/111598755)