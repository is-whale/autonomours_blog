- [Apollo规划模块详解：算法实现-9类交通规则（上） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/433428958)

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

接下来对每一个交规对应的决策处理进行展开介绍

### 1.1 BACKSIDE_VEHICLE

在对后向来车的处理决策中,会对path_decision中的每一个障碍物进行遍历,在遍历时,如果是后向车辆,则选择忽略,即通过调用函数PathDecision::AddLongitudinalDecision()和函数PathDecision::AddLateralDecision()为障碍物打上"ignore”的标签,这两个函数的处理逻辑比较简单,这里就不再展开介绍.需要"ignore”的后向车辆主要是下面的2,3,4,三种情况,在4中,如果后向车辆的横向距离较大,其可能会选择超车,不能忽略,因此在此处不作处理.

\1. 在对后向车辆的情况处理时,对于前方障碍物及警戒级别的障碍物不作处理

![img](https://pic4.zhimg.com/80/v2-902bdbc25406f55ef84cd1c4df30c153_720w.png)

2.如果参考线上没有障碍物运动轨迹,则忽略

![img](https://pic4.zhimg.com/80/v2-8dba5fa59d1042798ba7c4ebe5401107_720w.jpg)

3.如果障碍物轨迹距离无人车的最近距离小于一个车长,则忽略

![img](https://pic4.zhimg.com/80/v2-986e20686b9af40bbc5cc7b95743df33_720w.jpg)

4.如果距离本车横向距离小于一定阈值的后向车辆,说明车辆不会超车.选择忽略

![img](https://pic1.zhimg.com/80/v2-ca7b5c7fbcc70e45610ced22a72c5dc0_720w.jpg)

### 1.2 CROSSWALK

1.在对遇人行横道的情况进行处理时,首先从地图中获取所有的人行横道,然后其进行遍历,

（1）当车头驶过人行横道的距离超过阈值min_pass_s_distance时,如果当前的crosswalk_status中crosswalk_id信息,且crosswalk_id就是当前正在遍历的crosswalk_id,则清除rosswalk_status中的ID及停止时间信息,即不需要停车;

![img](https://pic3.zhimg.com/80/v2-a9ac094f7781d20c044160c4afdba0ee_720w.jpg)

（2）如果已经通过行人横道,则忽略;

![img](https://pic3.zhimg.com/80/v2-17b9373f22736158c54fbbeda0b4120e_720w.jpg)

（3）对path_decision中的障碍物进行遍历,

i)通过调用函数GetADCStopDeceleration(),根据车头位置和停止线之间的距离计算停车减速度;

ii)通过调用函数Crosswalk::CheckStopForObstacle()来判断正在遍历的障碍物是否需要停车,这个函数的内部处理对应的代码比较长,这里就不再贴出,只对逻辑处理简单介绍

a) 如果障碍物类型是行人,自行车,移动物体或者位置类型,则直接返回false,不需要停车;

b) 如果障碍物不在扩展后的人行横道区域内,则直接返回false,不需要停车;

c)当障碍物的横向距离足够大时(大于stop_loose_l_distance), 如果障碍物的轨迹与参考线相交,则需要停车;

当障碍物的横向距离小于比较严苛的横向停车距离,如果是前道路范围内的前向障碍物,则需要停车;

当障碍物的横向距离小于比较严苛的横向停车距离,如果是不在当前道路范围内但轨迹与参考线相交的障碍物,则需要停车;

当障碍物的横向距离小于比较严苛的横向停车距离,如果是不在当前道路范围内但正在本车靠近的障碍物,则需要停车;

当障碍物的横向距离大于比较严苛的横向停车距离同时小于一定的阈值(宽松的停车距离)时,如果障碍物与参考线轨迹相交,则停车;

iv)如果(iii)处理的结果需要停车,并且停车减速度大于配置文件给出的最大减速度,同时障碍物的横向距离大于比较严苛的横向停车距离,则不再需要停车.

iii)如果需要停车,同时障碍物不在当前所查询的参考线所对应的车道上,且车辆到停止线的距离小于40米,当障碍物移动的速度小于等于0.3时,如果当前障碍物不在crosswalk_stop_timer[crosswalk_id]中,则将其插入其中;如果障碍物已经在了并且stop_time >= config_.crosswalk().stop_timeout(),则不再需要停车;

iv)如果经过(2)和(3)判断之后,最后的结果是需要停车,则pedestrians.push_back(obstacle_id),供后续处理使用;

![img](https://pic2.zhimg.com/80/v2-b7421b2e4d5951fa093da9e15f53440d_720w.jpg)

\4) 如果上述所得vector不为空,则将障碍物和人行横道信息存入crosswalks_to_stop中,至此对人行横道和障碍物的遍历结束.

![img](https://pic2.zhimg.com/80/v2-c29fa7f6bdd5f21c6ae721b89364fb99_720w.png)

\2. 对上述所得crosswalks_to_stop中的每个人行横道都生成虚拟障碍物(长度为0.1,宽度为车道宽),并设置停车决策信息;同时查找将要遇到的第一个人行横道

\3. 更新状态信息

![img](https://pic1.zhimg.com/80/v2-b7202325a39937555b58bdfd5c083ef4_720w.jpg)

### 1.3 DESTINATION

到达目的地的情况下,障碍物促使本车采取靠边停车或者寻找合适的停车点.

\1. 靠边停车功能激活的情况下(配置文件中默认设置为false)

若存在有效的靠边停车点,则相关处理与人行横道中对遍历后需要停车的人行横道的处理一致,即创建虚拟障碍物,并封装成新的PathObstacle加入该ReferenceLineInfo的pathdecision中。

![img](https://pic3.zhimg.com/80/v2-ecca4ec0f5bce5d2e9899b3f22d23bfe_720w.jpg)

2.如果靠边停车功能没有激活,或者靠边停车停车情况下没有有效的停车点。

需要注意的是,此场景下车辆的停车点并不是我们所选择的目的地,而是距离一小段距离。

![img](https://pic2.zhimg.com/80/v2-897882a81f01a5ee1ad03443f935379d_720w.png)

其他的处理就和1中的处理类似

### **1.4 KEEP_CLEAR**

禁停区分为两类，第一类是传统的禁停区，第二类是交叉路口。对于禁停区的处理和对人行横道上障碍物构建虚拟墙很相似。具体做法是在参考线上构建一块禁停区，从纵向的start_s到end_s(这里的start_s和end_s是禁停区start_s和end_s在参考线上的投影点)。禁停区宽度在配置文件中设定(4米)。

\1. 禁停区

在对传统禁停区处理时,首先从地图中获取禁停区的信息,然后遍历获取到的禁停区,对每一个禁停区通过调用函数KeepClear::BuildKeepClearObstacle()做具体的处理

1)检查自车是否已经驶入禁停区,如果是,则忽略

![img](https://pic4.zhimg.com/80/v2-e1ff3554659e2ab3cb9dd301f8f8c9fb_720w.jpg)

\2) 对于还未经过的禁停区,生成虚拟障碍物,并封装到path_obstacle中

![img](https://pic2.zhimg.com/80/v2-015b42f0cf92605a100bff99e17efd19_720w.jpg)

\2. 交叉口

与传统禁停区相比,对交叉口进行处理时,需要根据交叉口的类型来计算交叉口开始的s值(pnc_junction_start_s),用来生成虚拟障碍物的标定框

### 1.5 REFERENCE_LINE_END

当参考线结束时,一般需要重新路由查询,所以需要停车,这种情况下如果程序正常,一般是前方没有路了,需要重新查询当前点到目的地新的路由,具体代码也是跟人行横道的不可忽略障碍物一样,在参考线终点前构建一个停止墙障碍物,并打上Stop的标签.

\1. 检查车辆是否接近参考线终点,如果是,则不生成虚拟停止墙

![img](https://pic2.zhimg.com/80/v2-32a44cac606594f950ac8f130ded8509_720w.png)

\2. 生成虚拟的停止墙,并设置相关的决策信息

![img](https://pic2.zhimg.com/80/v2-f20d4ba8d244cee5475993ee74c7ad3d_720w.jpg)

## 总结

本篇中我们详细介绍了交通规则中的九种交通规则中的五种：BACKSIDE_VEHICLE（后向来车）、CROSSWALK（人行横道）、DESTINATION（目的地）、KEEP_CLEAR（禁停区）、REFERENCE_LINE_END（参考线结束）；在下篇文章中我们会给大家介绍剩下的四种交通规则