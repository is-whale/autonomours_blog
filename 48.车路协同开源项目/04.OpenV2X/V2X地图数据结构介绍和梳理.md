- [V2X技术周 ｜ V2X地图数据结构介绍和梳理 (qq.com)](https://mp.weixin.qq.com/s/933qwF6wqgXrvjkrENJL_g)

**
V2X 地图**

- 定义：地图消息。由路侧单元广播，向车辆传递局部区域的地图信息。包括局部区域的路口信息、路段信息、车道信息，道路之间的连接关系等。单个地图消息可以包含多个路口或区域的地图数据。路口处的信号灯信息则在 SPAT 消息中详细定义。

- 

- MAP 消息的主体结构，是一个层层嵌套的形式。其中实线框为必有项，虚线框为可选项。 



![图片](https://mmbiz.qpic.cn/mmbiz_png/OzEcg7CtMiaKOQaBlgGbmibPaFJqKRAbHdXZYiboGNMEOYD5iaa1PNb76MUOhswonFtlfOH9xiawazwFge5rUhzNicZw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)







**示例文件**



![图片](https://mmbiz.qpic.cn/mmbiz_jpg/OzEcg7CtMiaKicYpSsXdsiaAEBp8OiajXgxlzicCvATDp2UynHdicMxmeBDZclwLRmkc1oxtnfV3WtyCbhw7OoPbk0ibQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)



- 上述示例中注释为数据类型，参考下述数据帧、数据元素。



- 根据示例，初步画出路口图为：



![图片](https://mmbiz.qpic.cn/mmbiz_png/OzEcg7CtMiaKicYpSsXdsiaAEBp8OiajXgxlEnU38fLR83nelUKU1GONL3iaXyCIvfgbMtbUD7g7dbIIicZRc5RMsiaeg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)



- **问题**：目前根据标准无法确定如何画出弧形路口。