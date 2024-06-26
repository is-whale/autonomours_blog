- [UWB室内定位技术的三种算法分析 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/426441623)

随着人类社会进入移动互联网的新时代,基于地理位置信息的相关服务也迅速的发展起来.目前人们已经不仅仅满足于室外环境下的位置信息服务,在室内环境下的人员和设备定位等服务也存在巨大需求,但是室内环境不同于空旷的室外,存在许多障碍物,影响无线信号的定位效果,因此如何提高室内无线定位精度的相关技术研究也成为了热点。

![img](https://pic1.zhimg.com/80/v2-a24f9a9e2913932b7f4e2a9420672c54_720w.jpg)



而[UWB定位](https://link.zhihu.com/?target=http%3A//www.uwbcloud.cn/)技术之所以在过去十年中不断被Wi-Fi和4G、5G等技术替代，主要原因在于生态建设、短距离的数据传输应用受到了抑制。今天，UWB已经被用于一些需要高精度定位的物流或大型商场中，只是规模较小，尚未达到真正的大规模应用。

[UWB室内定位](https://link.zhihu.com/?target=http%3A//www.uwbcloud.cn/)属于高精度室内定位的一种，UWB定位常用的有三种定位算法，包括TWR定位算法、TOA定位算法和TDOA定位算法，接下来为大家详细介绍：

TWR定位算法:

TWR定位算法的全称是Two Way Ranging，是一种双向测距定位算法。

TOA算法：全称是Time of Arrival，通过测量被测UWB定位标签(B)与已知位置基站(P1,P2,P3)间的报文传输时间，计算出距离；采用三个以上的距离值，通过三角定位，计算出被测标签的位置。不需要已知位置基站间时钟同步。

基站收到标签的多种信息；

服务器根据上传的信息进行时钟分配；

标签根据分配的时间同基站进行测距定位；

已知坐标的基站为一组，实现三维定位；

标签进入定位区域后，按照分配的时间和顺序，依次与基站进行测距；

距离信息通过有线/无线网络上传到服务器，实现位置实时跟踪。

TDOA定位算法：

TDOA，全称是Time Difference of Arrival，通过测量被测标签(B)与已知位置基站(P1,P2,P3)间的报文传输时间差，计算出距离差；计算出被测标签的位置。需要已知位置基站间时钟同步。