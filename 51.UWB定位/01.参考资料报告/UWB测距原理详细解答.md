- [UWB测距原理详细解答_北京华星智控的博客-CSDN博客_uwb测距原理](https://blog.csdn.net/qq_35699674/article/details/122313425?spm=1001.2014.3001.5502)

UWB定位技术主要以UWB通信芯片为基础实现室内外高[精度](https://so.csdn.net/so/search?q=精度&spm=1001.2101.3001.7020)定位工作的，之所以能够实现定位的关键性因素有如下一个方面：

1.UWB芯片提供数据帧收发时纪录[时间戳](https://so.csdn.net/so/search?q=时间戳&spm=1001.2101.3001.7020)，这是能够进行两点间测距的基本条件，简单来说，通过计算数据在空中飞行时间*光速=数据飞行距离，从而测出两节点间的距离。

![在这里插入图片描述](https://img-blog.csdnimg.cn/276302766a2d42d0b5057443f19fdc90.png#pic_center)

2.有了数据帧收发时间戳，那么就必须提供足够高的时钟精度，因为1ns的时间电磁波就传输了30厘米，UWB芯片提供了LDE的微代码，通过PLL使得时钟达到了64G的频率，当然，这个时钟仅提供给LDE使用，得UWB芯片具备了超高精度的时间戳，64G的时钟可以使得UWB时钟分辨率为15.65ps。

3.在以上基础上，可以实现两点间测距的功能，那么如果需要实现定位呢，则需要一个终端分别和多个基站通信， 分别得到终端与各个基站的距离，且基站之间的位置与距离在部署前期通过测绘手段可以得到这些数据。从而得到了终端在这个定位系统中的位置，一般使用球面相交法，通过输入终端离基站的距离，计算出精确的位置信息。

![在这里插入图片描述](https://img-blog.csdnimg.cn/0f74e2ef76074f9f92e90b58bace1509.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5YyX5Lqs5Y2O5pif5pm65o6n,size_15,color_FFFFFF,t_70,g_se,x_16#pic_center)

测距过程如下图所示

T1时刻标签发起一个测距请求数据包；

T2时刻UWB基站收到了测距请求数据包；

T3时刻UWB基站发送一个回复数据包给到标签；

T4时刻标签收到了UWB基站的回复数据包；

T5时刻，标签发送一个最终数据包给UWB基站；

T6时刻UWB基站收到了最终回复数据包完成了测距过程。

![在这里插入图片描述](https://img-blog.csdnimg.cn/d2743605c269459191b857e5a2e76163.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5YyX5Lqs5Y2O5pif5pm65o6n,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)