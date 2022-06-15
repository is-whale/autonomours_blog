**目录结构**

预测模块看似代码很多，实际上一总结起来就没多少了，下面是预测模块的整体目录结构：

```text
预测模块的目录结构如下：
.
├── BUILD
├── common                      // common目录，公用类
├── conf                        // 启动配置
├── container                   // 1. 消息容器
├── dag                         // 启动文件dag
├── data                        // 模型文件路径
├── evaluator                   // 3. 评估者
├── images                      // 文档（图片）
├── launch                      // 启动，加载模块
├── network
├── pipeline                    // 工具
├── prediction_component.cc           // 预测模块主入口
├── prediction_component.h             
├── prediction_component_test.cc
├── predictor                   // 4. 预测器
├── proto                       // protobuf消息格式
├── README_cn.md               // 文档（中文介绍，建议直接看英文）
├── README.md                  // 文档（英文介绍）
├── scenario                   // 2. 场景
├── testdata                   // 测试数据
├── util                       // 工具
└── visualization              // 可视化预测信息
```

实际上根据预测模块的流程如下：

```text
container -> scenario -> evaluator -> predictor
```

也就是说根据预测模块的入口文件"[http://prediction_component.cc](https://link.zhihu.com/?target=http%3A//prediction_component.cc)"，然后追踪上述4个目录的代码流程就可以了，工作量省去了不少吧！

**预测流程**

先了解整个预测的业务流程，和各个模块的作用，这点直接看官方文档[[1\]](https://www.zhihu.com/question/360069581/answer/2528458772#ref_1)，我就直接粘贴过来了：

- 容器（container）
  容器存储来自订阅通道的输入数据。目前支持的输入是感知模块的障碍物，车辆定位和车辆规划。

- 评估器（evaluator）
  评估器对任何给定的障碍分别预测路径和速度。评估器通过使用存储在prediction/data/模型中的给定模型输出路径的概率来评估路径（车道序列）。
  将提供三种类型的评估器，包括：

- - 成本评估器：概率是由一组成本函数计算的。
  - MLP评估器：用MLP模型计算概率
  - RNN评估器：用RNN模型计算概率

- 预测器（predictor）
  预测器生成障碍物的预测轨迹。当前支持的预测器包括：

- - 空：障碍物没有预测的轨迹
  - 单行道：在公路导航模式下障碍物沿着单条车道移动。不在车道上的障碍物将被忽略。
  - 车道顺序：障碍物沿车道移动
  - 移动序列：障碍物沿其运动模式沿车道移动
  - 自由运动：障碍物自由移动
  - 区域运动：障碍物在可能的区域中移动