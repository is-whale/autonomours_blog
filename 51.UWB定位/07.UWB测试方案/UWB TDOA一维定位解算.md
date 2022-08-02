- [UWB TDOA一维定位解算_Vortex6的博客-CSDN博客_uwb 一维定位](https://blog.csdn.net/qingqingdepiaoguo/article/details/123848376?spm=1001.2101.3001.6650.16&utm_medium=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~default-16-123848376-blog-104823083.pc_relevant_multi_platform_whitelistv3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~BlogCommendFromBaidu~default-16-123848376-blog-104823083.pc_relevant_multi_platform_whitelistv3&utm_relevant_index=20)

 在某些定位场景，比如在隧道、走廊等区域，需要用到一维解算，下面介绍TDOA的长直线解算定位标签位置（当然也可以用TWR实现一维解算）。定位模型与已知量如下：

![img](https://img-blog.csdnimg.cn/769ce6822eb54f1d90c5bc974dc02282.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVm9ydGV4Ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

 解算不考虑z坐标，基站A和基站B的坐标分别为已知量（X1，Y1），（X2，Y2），定位标签坐标（X，Y）为未知量，![TDOA_{AB}](https://latex.codecogs.com/gif.latex?TDOA_%7BAB%7D)为已知量，从UWB设备测量中获得；从模型中还可以得出：

![img](https://img-blog.csdnimg.cn/c9306f8be9e7479e812aa1d2ef28e457.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVm9ydGV4Ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

  先说结论，（X，Y）的解为:

![img](https://img-blog.csdnimg.cn/179fea9ffe3e4f00bac31f0fb7f360ef.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVm9ydGV4Ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

 以下为推导过程：

 根据基站A、基站B和定位标签的位置，可得直线方程:

![img](https://img-blog.csdnimg.cn/1e8d653030e04f03a910e8371064e73a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVm9ydGV4Ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

 所以：

![img](https://img-blog.csdnimg.cn/8280fffb66fe459b873184cec39cd27d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVm9ydGV4Ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

 根据三角函数关系：

![img](https://img-blog.csdnimg.cn/e1d995cc758d4d998195083d01b048c9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAVm9ydGV4Ng==,size_20,color_FFFFFF,t_70,g_se,x_16)

 将⑥代入⑤，即可得出③。

对解算及实际应用过程中，有以下思考：

1、上述的解算，可以支持在斜线中的一维定位，比只在x或y方向运动的一维算法适应性更强；

2、当定位标签的实际位置不在基站A和基站B之间时，该算法将不适用，需要其他辅助逻辑来过渡到下一定位区域，这种情形下TWR算法具有更大的逻辑优势（毕竟测量的是绝对距离）；

 3、对于z坐标，只部署两个基站的情况下，定位标签的z坐标只能是取固定值；