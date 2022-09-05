- [C-V2X空中接口技术原理-笔记 - 哔哩哔哩 (bilibili.com)](https://www.bilibili.com/read/cv14059795/)

# 1、接口协议栈

## 1）接口及频率 

UU接口： 2GHz
PC5接口：5.9GHz

![img](https://i0.hdslb.com/bfs/article/7f802e2edf2310f98de66c48d2f7cf35b738e238.png@942w_561h_progressive.webp)

其他接口技术原理参见中国通信标准化协会《基于LTE的车联网通信系统中的核心网设备》

## 2）接口协议栈

接口是指不同网元之间的信息交互方式。

既然是信息交互，就应该使用彼此都能看懂的语言，这就是接口协议。

接口协议的架构称为协议栈。

根据接口所处位置分为空中接口和地面接口，响应的协议也分为空中接口协议和地面接口协议。空中接口是无线制式最个性的地方，不同无线制式，其空口的最底层（物理层）的技术实现差别巨大。

例如：上图中PC5，UU是空中接口，S1，S6a是地面接口

## 3）接口分层设计

协议栈的分层结构有助于实现简化设计，设计人员只需要关注本层协议的设计即可，也极大方面了产品的设计和推广。底层协议为上层提供服务；上层使用下层的提供的功能，上层不必清楚下层过程处理的细节。

![img](https://i0.hdslb.com/bfs/article/a9da98d619336348264b878937a24e661e997736.png@942w_257h_progressive.webp)

# 2、用户面协议栈

![img](https://i0.hdslb.com/bfs/article/b0b97bd09c890df39e2bffd3d2bfb4f0a4eebaff.png@942w_434h_progressive.webp)UU

![img](https://i0.hdslb.com/bfs/article/95bb2439b133835a9630a7f8a5830eb535548ac9.png@942w_399h_progressive.webp)

![img](https://i0.hdslb.com/bfs/article/3719cfece2a8d2403107aa50547ce6a9131dbf5f.png@942w_405h_progressive.webp)

# 3、控制面协议栈

![img](https://i0.hdslb.com/bfs/article/2d2be387daa9eb4481a6ac577e5532dee7ae8f96.png@942w_372h_progressive.webp)UU

![img](https://i0.hdslb.com/bfs/article/e71428f12b06ee955afdc67ab4ec6e68bd79c6d7.png@942w_339h_progressive.webp)

![img](https://i0.hdslb.com/bfs/article/ce59ee326f36ccdc4de8bb355c5721d1d7d9dd91.png@942w_485h_progressive.webp)