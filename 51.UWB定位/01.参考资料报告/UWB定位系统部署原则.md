- [UWB定位系统部署原则_北京华星智控的博客-CSDN博客_系统部署原则](https://blog.csdn.net/qq_35699674/article/details/122226941?spm=1001.2014.3001.5502)

超宽带（UWB脉冲）使用频段为大于3GHz以上，相比于2.4Ghz（手机、WIFI信号）频段来说UWB信号绕射性能很差，相当于点对点的通信，因为这个特点使得UWB技术具有天生的测距性能优势，UWB技术用于室内定位可以实现10~30厘米的精确定位[精度](https://so.csdn.net/so/search?q=精度&spm=1001.2101.3001.7020)。

![在这里插入图片描述](https://img-blog.csdnimg.cn/d67f0ad2d0ed4c0fa527357f8e26622d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5YyX5Lqs5Y2O5pif5pm65o6n,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 1.UWB定位基站的部署原则

二维定位原理基于三角定位算法实现（如下图所示），要保证良好的定位精度就需要保证标签和不少于3个基站可靠的测距，理想的情况是标签和不少于3个基站可视（基站和标签之间不存在遮挡）这种情况将会获得最好的定位精度。

![在这里插入图片描述](https://img-blog.csdnimg.cn/4612addb3cd9434da6cbfc2d4f7a2942.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5YyX5Lqs5Y2O5pif5pm65o6n,size_15,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 2.定位原理说明

如右图所示,信号发射源发射信号脉冲，信号接收机需要提前安装在需要定位的空间里面，并需要自定义一个直角坐标系统，测绘出每个接收机的X,Y,Z坐标。信号发射源发射的脉冲飞行速度为光速C，脉冲到达3个信号接收机的时间分别为T1,T2,T3,通过光速C*时间T就可以算得三个距离L1,L2,L3，分别以三个距离为半径画出三个圆圈的交集处就是信号发射源的位置，这就是三角定位原理。

![在这里插入图片描述](https://img-blog.csdnimg.cn/4612addb3cd9434da6cbfc2d4f7a2942.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5YyX5Lqs5Y2O5pif5pm65o6n,size_15,color_FFFFFF,t_70,g_se,x_16#pic_center)

如下图所示的定位空间，部署的时候如果要求较高的定位精度（10~30cm）需要按照长宽比尽量接近1:1的原则部署定位基站(图中红色圆圈代表定位基站)，1:1的意思就是说如果该空间的宽度10米那么部署基站长度方向最好也不要超过10米部署，原因在于长宽比过大会导致精度因子变差，定位精度变差。

![在这里插入图片描述](https://img-blog.csdnimg.cn/f71148a3230f440ab7894a5464c085fe.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5YyX5Lqs5Y2O5pif5pm65o6n,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

如下图所示的定位空间，部署的时候如果要求的定位精度不高。比如只需要1米内的定位精度，可以按照长宽比不超过1:3的原则部署定位基站(图中红色圆圈代表定位基站)。

![在这里插入图片描述](https://img-blog.csdnimg.cn/fa7cded9c253429987c16e8a89bd5077.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5YyX5Lqs5Y2O5pif5pm65o6n,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

部署基站的时候如果存在较大的设备遮挡需要增加基站密度，保证在需要定位的位置，标签都能和不少于3个基站可靠的测距。如下图所示，比如中间位置存在3米高的金属罐体，那么需要新增绿色基站使得在左右区域能够保证最基本的定位条件。

![在这里插入图片描述](https://img-blog.csdnimg.cn/00d858b56bd548fb8fdf1e993d093595.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5YyX5Lqs5Y2O5pif5pm65o6n,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

在只有3个基站的时候，尽可能安装成锐角三角形，避免出现钝角三角形情况，钝角三角形会对系统精度造成影响 。

![在这里插入图片描述](https://img-blog.csdnimg.cn/65d4f4498eed4e9ca1e588c76b8cc11f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5YyX5Lqs5Y2O5pif5pm65o6n,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center)

[SOA成功部署的五大原则doc![img](https://csdnimg.cn/release/blogv2/dist/components/img/star.png)0星超过10%的资源34KB![img](https://csdnimg.cn/release/blogv2/dist/components/img/arrowDownWhite.png)下载](https://download.csdn.net/download/weixin_38590520/12213693)

## 3.安装位置选择

1：定位天线不要贴近金属物。电磁波通过金属面反射会造成非常强的多径效应，从而影响定位精度。

2：不要靠近高功率设备（全频段谐波），比如的无线电，大型机械日光灯等，由于UWB 的信号接近噪声水平，任何高功率多次谐波对它都会带来影响。

3：远离液体，液体对电磁波吸收非常明显。

4：选择视野开阔处安装。

5：定位基站如若采用POE 供电，距离不宜超过100米，必须选用超五类以上等级的网线，如果品质不好或者过长 ，会导致设备供电压不够无法正常工作。

6：工作环境温度 建议在 -40 ℃ ~ 80 ℃ 。

## 4.高度要求

为了让基站辐射覆盖性能更佳，安装高度建议3 ~8 米。

## 5.位置要求

适度离开墙体，基站安装的位置要适度离开墙体，建议和墙体保持750px 的距离，避免在识别的时候，造成多径的误识别。

## 6.支架安装

由于定位基站的特殊性，避免带来定位的误差，定位基站在安装的时候，需要和墙体保持一定的距离，在很多时候，需要特别为设备准备支架，确保和墙体的间隔，参考位置要求。安装实例如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/97dd54cc740a4b75b1bcfaeb1af6a204.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5YyX5Lqs5Y2O5pif5pm65o6n,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)