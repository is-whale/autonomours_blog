- [无人驾驶算法学习（十二）:imu中的常见的数值积分方法:欧拉,中值,龙格-库塔积分_su扬帆启航的博客-CSDN博客_龙格库塔积分](https://blog.csdn.net/orange_littlegirl/article/details/98726688)

# 1.积分基本概念

> [非线性](https://so.csdn.net/so/search?q=非线性&spm=1001.2101.3001.7020)微分方程:
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190807100348684.png)
> 在有限的时间间隔Δt积分:
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190807100430253.png)
> 连续时间内:![在这里插入图片描述](https://img-blog.csdnimg.cn/20190807100524436.png)
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/2019080710053619.png)

# 2.欧拉积分

> 欧拉方法假设导数f（·）在区间内是恒定的，有公式:
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/2019080709514441.png)
> 作为一般的RK方法，这对应于单阶段方法，可以是
> 描述如下。计算初始点的导数:
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190807095309932.png)
> 并用它来计算终点的积分值:
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190807095341190.png)
> 示意图:
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190807095420937.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

# 3.中值积分

> 中值积分法假设导数是间隔中点的导数，并进行一次迭代来计算此中点的fx值:
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190807095739321.png)
> 中值积分法可以解释为如下的两步法。
> 首先，使用Euler方法整合到中点，使用前面定义的k1
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190807095834779.png)
> 然后使用此值来评估中点的导数k2，从而积分得:
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190807100740540.png)
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190807100810844.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

# 4.RK4积分(4阶[龙格库塔法](https://so.csdn.net/so/search?q=龙格库塔法&spm=1001.2101.3001.7020))

> Runge-Kutta4假定评估值
> 对于f ( ) {f_{()}}*f*()​在间隔的开始，中点，中点的中点和结束。它使用四个阶段迭代计算积分，用四个导数k 1~k 4，顺序获得。
> 然后对这些导数进行加权平均，以获得4阶估计值间隔中的导数。
> RK4方法更好地指定为一个小算法而不是一步式公式
> 以上两种方法。 RK4集成步骤是:
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190807101752673.png)
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190807101803424.png)
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/2019080710182034.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

参考文章附录A:[四元数误差状态卡尔曼滤波](https://arxiv.org/abs/1711.02508)
参考亮哥的博客说明:http://www.xinliang-zhong.vip/msckf_notes/