- [路径规划算法学习笔记9-RRT* - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/146951195)

**一、原理**

RRT*算法的核心在于两个过程：重新选择父节点和重布线。这两个过程相辅相成，重新选择父节点使新生成的节点路径代价尽可能小，重布线使得生成新节点后的随机树减少冗余通路，减小路径代价。

**重新选择父节点：**在新产生的节点 xnew 附近以定义的半径范围内寻找“近邻”，作为替换 xnew 父节点的备选。依次计算“近邻”节点到起点的路径代价加上 xnew 到每个“近邻”的路径的代价，把代价最小的点作为新的父节点。

**重布线：**如果近邻节点的父节点改为 xnew 可以减小路径代价，则进行更改。[[1\]](https://zhuanlan.zhihu.com/p/146951195#ref_1)

![img](https://pic2.zhimg.com/80/v2-25e8bc282f305d2420b43d8a9cdf2475_720w.jpg)1. 初始部分与RRT相同

![img](https://pic3.zhimg.com/80/v2-2d725d466681f77faa9c439e1030e616_720w.jpg)2. 搜索多个节点去作为new的父节点，看看通过哪个节点到达start最短。在本图中new-near-start是最短的，new-x1-near-start和new-x2-near-start均比第一条路长。

![img](https://pic2.zhimg.com/80/v2-d416d7d5ab8514b952de38b98326dc69_720w.jpg)3. 添加到集合之中

![img](https://pic1.zhimg.com/80/v2-d2111170ab5a4e4b5a0283c5686e0124_720w.jpg)4. 对于x1来讲，start-near-x1比start-near-new-x1的距离短，所以x1的父节点是near，不用修改。

![img](https://pic1.zhimg.com/80/v2-33c7da57a33d392150abaddde9f41394_720w.jpg)5. 对于x2来讲，start-near-x1-x2比start-near-new-x2的距离长，所以修改x2的父节点为new。

***RRT生成路径之后便停止，RRT\*生成路径之后还在不断优化。\***

**二、改进方法：**

**1.Kinodynamic-RRT\*:** Change Steer() function to fit with motion or other constraints in robot navigation.

![img](https://pic2.zhimg.com/80/v2-232712ae7b1ef7252fc1cf65092dbbd1_720w.jpg)

**2. Anytime-RRT\*:** Keep optimizing the leaf RRT tree when the robot executes the current trajectory Anytime Fashion.

**3. Informed RRT\*:** 将采样过程限制在一个椭圆里面。

![img](https://pic3.zhimg.com/80/v2-6b92c68f5055896c344df8ceb6904ab6_720w.jpg)

**4. Cross-entropy motion planning:**

![img](https://pic3.zhimg.com/80/v2-d87428eea6c03d45906cacd854f5b6ee_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-1e856f29be95b9ba27ab7d1f57382a08_720w.jpg)

步骤：生成路径；在节点附近进行多高斯模型采样，得到更多路径；在更多路径的节点做均值，均值化之后的节点重新进行采样，继续得到更多路径。

**5. 其他改进方法：**

Lower Bound Tree RRT(LBTRRT)[[2\]](https://zhuanlan.zhihu.com/p/146951195#ref_2)

Sparse Stable RRT[[3\]](https://zhuanlan.zhihu.com/p/146951195#ref_3)

Transition-based RRT(T-RRT)[[4\]](https://zhuanlan.zhihu.com/p/146951195#ref_4)

Vector Field RRT[[5\]](https://zhuanlan.zhihu.com/p/146951195#ref_5)

Parallel RRT(pRRT)[[6\]](https://zhuanlan.zhihu.com/p/146951195#ref_6)

Etc.[[7\]](https://zhuanlan.zhihu.com/p/146951195#ref_7)

## 参考

1. [^](https://zhuanlan.zhihu.com/p/146951195#ref_1_0)https://blog.csdn.net/yuxuan20062007/article/details/88843690
2. [^](https://zhuanlan.zhihu.com/p/146951195#ref_2_0)https://arxiv.org/pdf/1308.0189.pdf
3. [^](https://zhuanlan.zhihu.com/p/146951195#ref_3_0)http://pracsyslab.org/sst_software
4. [^](https://zhuanlan.zhihu.com/p/146951195#ref_4_0)http://homepages.laas.fr/jcortes/Papers/jaillet_aaaiWS08.pdf
5. [^](https://zhuanlan.zhihu.com/p/146951195#ref_5_0)https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=6606360
6. [^](https://zhuanlan.zhihu.com/p/146951195#ref_6_0)https://robotics.cs.unc.edu/publications/Ichnowski2012_IROS.pdf
7. [^](https://zhuanlan.zhihu.com/p/146951195#ref_7_0)https://github.com/zychaoqun