- [Apollo规划模块详解：算法实现-9类交通规则（下） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/436139193)

## 引

车辆在道路上行驶必然受到交通规则的限制，Apollo中对交通规则的应用是把交通规则作为虚拟障碍物，处理后相关信息存入ReferenceLineInfo中。

![img](https://pic3.zhimg.com/80/v2-f9d85128c1d4bc89d49f4ec95fec41be_720w.jpg)

对交通规则的整个梳理流程在上图所示的for循环中进行，通过for循环来遍历配置文件/apollo/modules/planning/conf/traffic_rule_config.pb.txt中设置的交通规则，主要涉及的交通规则有以下九类：

- **BACKSIDE_VEHICLE（后向来车）**
- **CROSSWALK（人行横道）**
- **DESTINATION（目的地）**
- **KEEP_CLEAR（禁停区）**
- **REFERENCE_LINE_END（参考线结束）**
- **REROUTING（重新路由）**
- **STOP_SIGN（停车）**
- **TRAFFIC_LIGHT（交通灯）**
- **YIELD_SIGN（让行）**

上篇文章我们讲述了前五类交通规则，本篇文章继续给大家详细介绍余下四类交通规则。

## 1.6 REROUTING

重新路由的处理思想就是根据车辆当前的位置等信息判断是否需要重新请求路由

\1. 车辆如果到达终点，则不需要重新申请路由

![img](https://pic2.zhimg.com/80/v2-1431310b74a4e96a9d96a0f07e7bb6c1_720w.jpg)

\2. 如果当前是直行道，则不需要重新申请路由

![img](https://pic4.zhimg.com/80/v2-9bf5e9ada578b56451a230ab719ec86f_720w.png)

3.如果车辆不在当前路由段上，则不需要重新申请路由

![img](https://pic2.zhimg.com/80/v2-d14ee81da82ca31899d516840a1bab55_720w.png)

4.如果车辆即将退出当前passage，则不需要重新申请路由

![img](https://pic3.zhimg.com/80/v2-279561c14b4ef69b799b6ef0514920fa_720w.png)

\5. 若当前路由段的终点不在当前参考线所在的车道上，则不需要重新申请路由；

![img](https://pic2.zhimg.com/80/v2-c405ef872c5857c37cab3fde23074fb9_720w.jpg)

\6. 如果车辆在变更路由之前不能到达当前passage的终点，则不需要重新申请路由

![img](https://pic1.zhimg.com/80/v2-d11121c60e042654bedfd35cbe6f6a6c_720w.jpg)

\7. 如果车辆在短时间内已经重新申请过路由，则不需要再重新申请路由

![img](https://pic2.zhimg.com/80/v2-1bc9309a0165516473aa570f857b7fed_720w.jpg)

如果车辆当前的状态不符合上述7种情况，则重新申请路由，重新申请路由由函数Frame::Rerouting（）完成，函数的主要实现逻辑如下：

1）获取routing_request信息；

2）根据车辆当前的位置，获取距离车辆最近的车道；

3）更新重新路由请求中的路点信息及状态

## 1.7 STOP_SIGN

关于这个停止牌，emmmmmm本人走过的路比较少，没咋在国内的城市道路上见过，美国道路上好像遍地都是，就是看到这个Stop_sign必须得停车，所以对这个停止牌的处理逻辑就是生成停止墙。

1.首先根据车辆当前位置和ID判断是否已经驶过正在遍历的停止牌，如果是，则不再处理，否则则走下面的逻辑生成虚拟停止墙

![img](https://pic2.zhimg.com/80/v2-dc753ad9300e1ca2305ad96df972a051_720w.jpg)

\2. 关于生成停止墙的具体逻辑一直没有详细介绍，这里就借助这个停止牌来展开介绍一下生成停止墙的函数BuildStopDecision()

1）首先来看一下函数的输入首先来看一下函数的输入

![img](https://pic4.zhimg.com/80/v2-ec2887762256c741e796541729d09daf_720w.jpg)

2） 下面来看一下虚拟停止墙并添加停止决策的具体处理逻辑

i）在生成虚拟停止墙时，首先当前遍历的停止牌是否在当前所查询的参考线上，如果不在，则不做处理；

![img](https://pic4.zhimg.com/80/v2-3cfd265b94bd996a22be73c1bf800cf7_720w.png)

ii）通过调用函数Obstacle *Frame::CreateStopObstacle()生成虚拟停止墙

a）在生成虚拟障碍物时，首先需要确定障碍物的中心位置以及长，宽，航向角等信息，然后根据这些信息生成标定框，障碍物的宽度为4米（覆盖当前车道宽度），长度为0.1米，中心位置为停止区域开始的位置往前延伸半个宽度

![img](https://pic1.zhimg.com/80/v2-6f6a907a1c048048152cb71e044bd5fc_720w.jpg)

b）调用函数Obstacle::CreateStaticVirtualObstacles(),根据障碍物的长，宽，中心位置灯信息生成障碍物

![img](https://pic4.zhimg.com/80/v2-21c5f029a53990e2ca9656c8e0fa735f_720w.jpg)

c） 把障碍物封装到path_decision中

c-1）对障碍物的属性做判断处理，如设置障碍物的边界，判断障碍物是否是阻塞障碍物

c-2）根据障碍物的位置和状态信息判断是否可忽略

如果是，打上“ignore”的标签，添加相关决策信息

如果是不可忽略的障碍物，则建立ST图（建立ST图主要是针对动态障碍物的，与此处无关就先不展开介绍了）

d）按照decision.proto中定义的内容设置停车决策

![img](https://pic2.zhimg.com/80/v2-878628ca693300bb3d9283f0719fad45_720w.jpg)

e）按照上述设置的停车决策更新相关的决策信息

## 1.8 TRAFFIC_LIGHT

对交通灯的处理是红灯停，绿灯行，emmmm，黄灯也停

首先从map中获取存储所有信号灯的vector，然后对每一个信号灯进行遍历；

1）如果根据车辆当前的位置判断已经驶过红绿灯，则当前遍历的红绿灯则不作任何处理

2）遍历已经驶过的交通灯的ID，如果当前交通灯已经完成，则不做处理；

![img](https://pic3.zhimg.com/80/v2-0e270892184261d0863ac39cf6ad0dca_720w.jpg)

3）根据自车与交通灯的欧氏距离以及s差值，判断交通灯是否偏离，如果是，则不做处理；

![img](https://pic1.zhimg.com/80/v2-f1ab7d2d06eebee9607e877e0c6e3f84_720w.jpg)

4）剩下的就是根据交通灯的颜色判断处理，如果是绿色，则不做处理，如果是红/黄/未知的颜色色，并且计算出的停车减速度大于最大减速度，则说明距离较远，这次先不做处理

![img](https://pic1.zhimg.com/80/v2-4b813bad5d2237b91ca0b7b96f59b6f8_720w.jpg)

5）这么看下来，其实是只对红/黄/未知灯，且加速在合理范围内的交通灯做停车处理，处理策略就是生成虚拟停车墙，关于这个墙面介绍的已经很详细了

![img](https://pic4.zhimg.com/80/v2-1b06ee5162bdfac2ca610a46573eeabb_720w.jpg)

## 1.9 YIELD_SIGN

停车让行的处理逻辑与交通灯一致，就不再展开介绍了

1. 从地图中获取停车让行的vector信息；

2. 对停车让行vector进行遍历，如果是没有处理过的则生成相关的虚拟停车墙