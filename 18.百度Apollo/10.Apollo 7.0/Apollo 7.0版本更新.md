- [Apollo 7.0版本更新 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/458316518)

## 新版本

最近刚发布了Apollo 7.0版本，第一时间就跟进了部分内容，还没来得及看源码。总的来说，增强了**感知、预测和规划**3个模块的能力，这也是目前自动驾驶出问题最多，也最值得改进和优化的模块。

部分服务开始上云，也更加接近量产和后续维护，很期待接下来有提供车队运维的云服务公司和运营公司出现。



以下是Apollo 7.0版本更新的具体内容，主要对比**6.0和7.0版本**的差异。

## 概述

- 3个全新的深度学习模型，以增强 Apollo 感知和预测模块的能力
- 基于以往仿真服务的 PnC 强化学习模型训练和仿真评估服务

### 具体内容

```text
cyber // classloader去除poco依赖；优化部分代码
docker // 增加x86和arm环境；增加部分脚本
docs  // 增加technical_documents；更新部分文档

scripts // 删除了一些无效脚本;替换python格式化工具（autopep8->yapf）;
third_party // 增加localization_msf，删除lz4\poco\local_integ
tools // 增加install

moudules
 calibration // 删除车辆标定参数目录
 dreamview  // 增加fuel_monitor,删除data_collection_monitor;增加defaultrouting;增加传感器标定格式文件
 drivers/lidar // 优化激光雷达代码
 monitor  // 增加camera_monitor
 perception // camera部分功能修复;检测部分增加smoke;transformer部分增加singlestage转换;inference更新onnx，tensorrt部分;
               detector增加cnn_segmentation\mask_pillars_detection\ncut_segmentation\point_pillars_detection，
               去除segmentation
 planning // 增加dead_end场景
 prediction // 增加新的评估器jointly_prediction_planning_evaluator
 task_manager // 新增功能
 tools  // 增加sensor_calibration; 更新vehicle_calibration部分内容
```

值得注意的是感知camera模块疑似修复，还没来得及测试。