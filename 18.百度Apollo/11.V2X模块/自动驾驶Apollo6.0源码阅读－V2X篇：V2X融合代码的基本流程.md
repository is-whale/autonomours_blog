- [自动驾驶Apollo6.0源码阅读－V2X篇：V2X融合代码的基本流程_洪山橋嬉皮的博客-CSDN博客](https://blog.csdn.net/ControlLearner/article/details/122108780)

# Fusion流程

## 1.Fusion模块在哪里启动？

![在这里插入图片描述](https://img-blog.csdnimg.cn/9de7d3129a444225870a436f26510ac1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

这个是在/home/mogo/lxy/Gitte/[apollo](https://so.csdn.net/so/search?q=apollo&spm=1001.2101.3001.7020)/modules/v2x/dag/v2x_perception_fusion.dag中定义的。

所以，我们需要去找V2XFusionComponent。并且知道了它的配置v2x_fusion_tracker.conf中;

![在这里插入图片描述](https://img-blog.csdnimg.cn/57d308cafd1049b7a95f9fa73f110fcd.png)

能够得到以下信息：

1. 融合参数

![在这里插入图片描述](https://img-blog.csdnimg.cn/f022ace47d354a49a4aa5fada0ccd685.png)

## 2.Fusion [Component](https://so.csdn.net/so/search?q=Component&spm=1001.2101.3001.7020)的初始化？

Fusion Component的地址是这个：

modules/v2x/fusion/apps/v2x_fusion_component.h

Init

![在这里插入图片描述](https://img-blog.csdnimg.cn/d482500ed8514a06ae8a158a950bff84.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

第一步，创建接收器v2x_obstacle_topic

第二步，创建接收器localization_topic

第三步，创建接收器perception_obstacle_topic

## 3.Fusion 的流程[框架](https://so.csdn.net/so/search?q=框架&spm=1001.2101.3001.7020)

核心方法是**Proc**

![在这里插入图片描述](https://img-blog.csdnimg.cn/faa70715ca624467a97065452fbac5ca.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

核心的方法是**V2XMessageFusionProcess**，那么**V2XMessageFusionProcess**中的核心代码是什么呢？

![在这里插入图片描述](https://img-blog.csdnimg.cn/0dbb7741011f4a548a3c25b8e16d0ca7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

**流程图**

![在这里插入图片描述](https://img-blog.csdnimg.cn/e4d930d978464bfd9c2df73e5a72204f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_14,color_FFFFFF,t_70,g_se,x_16)

有趣的是，

```cpp
std::vector<Object> fused_objects;
std::vector<Object> v2x_fused_objects;
std::vector<std::vector<Object>> fusion_result;
```

这三个每次进来都会重新定义。

下面分开讲解核心代码

### 3.1 fusion_.CombineNewResource(perception_objects, &fused_objects, &fusion_result);

### 3.2 fusion_.CombineNewResource(v2x_objects, &fused_objects, &fusion_result);

![在这里插入图片描述](https://img-blog.csdnimg.cn/502a240011714dceb27c585145c975ca.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

第一步，遍历fused_objects，如果fused_objects.size()<1，将new_objects的目标赋值到fused_objects;并且将每个new_objects以vector形式push_back给fusion_result;

(new_objects，可以是perception_objects，也可以是v2x_objects)

第二步，计算fused_objects与new_objects的代价矩阵，存入association_mat;

第三步，采用匈牙利方法获得代价矩阵的最小分配结果。

第四步，更新fused_objects和fusion_objects.

### 3.3 fusion_.GetV2xFusionObjects(fusion_result, &v2x_fused_objects);

![在这里插入图片描述](https://img-blog.csdnimg.cn/60ffa9ac8ad74e74ab5edca0dc72ea90.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

主要工作是重新整合fused_objects的type，整合完之后，序列化就发布出去。

该模块的融合，没有进行跟踪，仅进行了匹配工作。

## 4.compute Associate [Matrix](https://so.csdn.net/so/search?q=Matrix&spm=1001.2101.3001.7020)

**ComputeAssociateMatrix**

![在这里插入图片描述](https://img-blog.csdnimg.cn/8ede61a4a8984cbdb9222e582abb7384.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

可以很清晰的看到，关键两个函数是：**CheckDisScore()\**和\**CheckTypeScore()**,下面展开讲这两个：

**CheckDisScore（）**
![在这里插入图片描述](https://img-blog.csdnimg.cn/b099c8739e694787a621f181e55dc691.png)

这个函数，核心在**CheckODistance()**

![在这里插入图片描述](https://img-blog.csdnimg.cn/40b4fd896ba141698f39ec67ba247408.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_18,color_FFFFFF,t_70,g_se,x_16)

**std::hypot()**函数返回所传递的参数平方和的平方根(相当于2维欧式距离)，最终DisScore为：
s c o r e = 2.5 × m a x { 0 , s c o r e p a r a m s . m a x m a t c h d i s t a n c e ( ) − d i s } score = 2.5 \times max \{0,score_params_.max_match_distance() - dis \}*s**c**o**r**e*=2.5×*m**a**x*{0,*s**c**o**r**e**p*​*a**r**a**m**s*.​*m**a**x**m*​*a**t**c**h**d*​*i**s**t**a**n**c**e*()−*d**i**s*}
**CheckTypeScore**

![在这里插入图片描述](https://img-blog.csdnimg.cn/a54b28170e804da48e065bc8875abdbf.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5rSq5bGx5qmL5ayJ55qu,size_20,color_FFFFFF,t_70,g_se,x_16)

根据new_objects与fused_objects的类型区别，选择不同的计算score的方法。

回到代价矩阵计算方法：

![在这里插入图片描述](https://img-blog.csdnimg.cn/cd9a92a811e045739c59212346caf5f5.png)

如果score大于等于阈值则输出score，否则为0;