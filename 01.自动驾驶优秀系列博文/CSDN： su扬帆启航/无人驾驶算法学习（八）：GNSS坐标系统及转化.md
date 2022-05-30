- [无人驾驶算法学习（八）：GNSS坐标系统及转化_su扬帆启航的博客-CSDN博客_无人驾驶中用到的八大坐标系](https://blog.csdn.net/orange_littlegirl/article/details/92388068)

本文主要是讲解Apollo项目开发过程中用到的定位坐标，着重讲解GNSS坐标系统及其转化，本文参考 [Apollo开发文档](http://www.51apollo.com/2018/08/01/apollo2-5技术文档学习指南/)和网上 [知行合一2018](https://blog.csdn.net/davidhopper/article/details/79162385) 的博文。

# 1.全球地理坐标系

在[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020)项目中，我们使用一个全球地理坐标系统来表示高精地图(HD map)中的元素。全球地理坐标的常用选择
是纬度（latitude）、经度（longitude）和海拔（elevation）。全球地理坐标系普遍采用地理信息系统（Geographic Information System，GIS）中用到的WGS-84（the World Geodetic System dating from 1984）坐标系，世界大地测年系统自1984年起，作为表示经纬度的标准坐标系对象。通过使用这个标准的坐标参考系统，可以唯一地在地球表面标识任何点(除了北极)：x坐标和点的y坐标，其中x对应经度，y对应纬度。WGS-84广泛应用于GIS服务中，如测绘、定位、导航等。的定义我们使用的全球地理坐标系如下图所示。海拔值定义为椭球面高度（the ellipsoidal height）。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190616124252406.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

# 2.局部坐标系—东-北-天坐标(ENU)

局部坐标系定义为：

- X轴：指向东边
- Y轴：指向北边
- Z轴：指向天顶
  如下图所示：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/2019061612450922.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)
  ENU局部坐标系采用三维直角坐标系来描述地球表面，实际应用较为困难，因此一般使用简化后的二维投影坐标系来描述。在众多二维投影坐标系中，统一横轴墨卡托（The Universal Transverse Mercator ，UTM）坐标系是一种应用较为广泛的一种。UTM 坐标系统使用基于网格的方法表示坐标，地球被划分为60个区域，每个区域都是经度为6度的区域，在每一区域内的横切墨卡托投影。阿波罗项目采用UTM坐标系作为模块中的局部框架，如定位、规划等。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190616124748162.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)
  参考细节：http://geokov.com/education/utm.aspx
  https://en.wikipedia.org/wiki/Universal_Transverse_Mercator_coordinate_system

# 3.车辆坐标系—右-前-天坐标（RFU）

车辆坐标系定义如下：

- X轴：面向车辆前方，右手所指方向
- Y轴：车辆前进方向
- Z轴：与地面垂直，指向车顶方向
  注意：车辆参考点为后轴中心。
  如下图所示：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190616125317941.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

# 4.坐标系转化

## 4.1椭球体下的大地坐标系基础

> ![这里是引用](https://img-blog.csdnimg.cn/20190616141610786.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

## 4.2大地坐标系转化为空间直角坐标系（BLH—>XYZ）

> ![这里是引用](https://img-blog.csdnimg.cn/20190616142137315.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

## 4.3空间直角坐标系转化为大地坐标系（XYZ—>BLH）

> ![这里是引用](https://img-blog.csdnimg.cn/20190616142115351.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

## 4.4高斯投影与UTW（墨卡托）投影

[参考博客的链接](https://www.cnblogs.com/wicked-fly/p/4726680.html)

> 设想用一个椭圆柱横切于椭球面上投影带的中央子午线，按上述投影条件，将中央子午线两侧一定经差范围内的椭球面正形投影于椭圆柱面。将椭圆柱面沿过南北极的母线剪开展平，即为高斯投影平面。
> 高斯-克吕格投影在长度和面积上变形很小，中央经线无变形，自中央经线向投影带边缘，变形逐渐增加，变形最大之处在投影带内赤道的两端。由于其投影精度高，变形小，而且计算简便（各投影带坐标一致，只要算出一个带的数据，其他各带都能应用），因此在大比例尺地形图中应用，可以满足军事上各种需要，能在图上进行精确的量测计算。
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190616155914291.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)
> 高斯-克吕格投影分带：按一定经差将地球椭球面划分成若干投影带,这是高斯投影中限制长度变形的最有效方法。分带时既要控制长度变形使其不大于测图误差，又要使带数不致过多以减少换带计算工作，据此原则将地球椭球面沿子午线划分成经差相等的瓜瓣形地带,以便分带投影。通常按经差6度或3度分为六度带或三度带。
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190616160233225.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)
> 注意：UTM投影与高斯投影的主要区别在南北格网线的比例系数上，高斯-克吕格投影的中央经线投影后保持长度不变，即比例系数为1，而UTM投影的比例系数为0.9996。UTM投影沿每一条南北格网线比例系数为常数，在东西方向则为变数，中心格网线的比例系数为0.9996，在南北纵行最宽部分的边缘上距离中心点大约 363公里，比例系数为 1.00158。

## 4.5高斯投影转换公式

> 其中地理坐标就是（B，L），平面直角坐标就是高斯-克吕格子的平面直角坐标
> ![这里是引用](https://img-blog.csdnimg.cn/20190616160339774.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

## 4.6高斯投影代码实战

> 注意这是从WGS84坐标系到高斯投影的转化，百度地图用的经纬度坐标并非是高斯WGS84，但是WGS84是世界通用的坐标系，经过测试其精确度还是可以的。

```
@author:cheng.jiang
#include "iostream"
#include "math.h"
using namespace std;
struct PingMian
{
	double x;
	double y;
	double z;
};
 
struct WGS84
{
	double longitude;//经度
	double latitude;//纬度
	double height;//高度
};
void GeodeticToCartesian(PingMian& pingmian, const WGS84& wgs84)
{
	double b;//纬度度数
	double L;//经度度数
	double L0;//中央经线度数
	double L1;//L - L0
	double t;//tanB
	double m;//ltanB
	double N;//卯酉圈曲率半径
	double q2;
	double X;// 高斯平面纵坐标
	double Y;// 高斯平面横坐标
	double s;// 赤道至纬度B的经线弧长
	double f; // 参考椭球体扁率
	double e2;// 椭球第一偏心率
	double a; // 参考椭球体长半轴
	//
	double a1;
	double a2;
	double a3;
	double a4;
	double 	 b1;
	double	 b2;
	double	 b3;
	double	 b4;
	double	c0;
	double	c1;
	double	c2;
	double	c3;
	//
	int Datum, prjno, zonewide;
	double IPI;
	//
	Datum = 84;// 投影基准面类型：北京54基准面为54，西安80基准面为80，WGS84基准面为84
	prjno = 0;// 投影带号
	zonewide = 3;
	IPI = 0.0174532925199433333333; // 3.1415926535898/180.0
	b = wgs84.latitude; //纬度
	L = wgs84.longitude;//经度
	if (zonewide == 6)
	{
		prjno = trunc(L / zonewide) + 1;
		L0 = prjno * zonewide - 3;
	}
	else
	{
		prjno = trunc((L - 1.5) / 3) + 1;
		L0 = prjno * 3;
	}
	if (Datum == 54)
	{
		a = 6378245;
		f = 1 / 298.3;
	}
	else if (Datum == 84)
	{
		a = 6378137;
		f = 1 / 298.257223563;
	}
	//
	L0 = L0 * IPI;
	L = L * IPI;
	b = b * IPI;
 
	e2 = 2 * f - f * f; // (a*a-b*b)/(a*a);
	L1 = L - L0;
	t = tan(b);
	m = L1 * cos(b);
	N = a / sqrt(1 - e2 * sin(b) * sin(b));
	q2 = e2 / (1 - e2) * cos(b) * cos(b);
	a1 = 1 + 3 / 4 * e2 + 45 / 64 * e2 * e2 + 175 / 256 * e2 * e2 * e2 + 11025 /
		16384 * e2 * e2 * e2 * e2 + 43659 / 65536 * e2 * e2 * e2 * e2 * e2;
	a2 = 3 / 4 * e2 + 15 / 16 * e2 * e2 + 525 / 512 * e2 * e2 * e2 + 2205 /
		2048 * e2 * e2 * e2 * e2 + 72765 / 65536 * e2 * e2 * e2 * e2 * e2;
	a3 = 15 / 64 * e2 * e2 + 105 / 256 * e2 * e2 * e2 + 2205 / 4096 * e2 * e2 *
		e2 * e2 + 10359 / 16384 * e2 * e2 * e2 * e2 * e2;
	a4 = 35 / 512 * e2 * e2 * e2 + 315 / 2048 * e2 * e2 * e2 * e2 + 31185 /
		13072 * e2 * e2 * e2 * e2 * e2;
	b1 = a1 * a * (1 - e2);
	b2 = -1 / 2 * a2 * a * (1 - e2);
	b3 = 1 / 4 * a3 * a * (1 - e2);
	b4 = -1 / 6 * a4 * a * (1 - e2);
	c0 = b1;
	c1 = 2 * b2 + 4 * b3 + 6 * b4;
	c2 = -(8 * b3 + 32 * b4);
	c3 = 32 * b4;
	s = c0 * b + cos(b) * (c1 * sin(b) + c2 * sin(b) * sin(b) * sin(b) + c3 * sin
		(b) * sin(b) * sin(b) * sin(b) * sin(b));
	X = s + 1 / 2 * N * t * m * m + 1 / 24 * (5 - t * t + 9 * q2 + 4 * q2 * q2)
		* N * t * m * m * m * m + 1 / 720 * (61 - 58 * t * t + t * t * t * t)
		* N * t * m * m * m * m * m * m;
	Y = N * m + 1 / 6 * (1 - t * t + q2) * N * m * m * m + 1 / 120 *
		(5 - 18 * t * t + t * t * t * t - 14 * q2 - 58 * q2 * t * t)
		* N * m * m * m * m * m;
	Y = Y + 1000000 * prjno + 500000;
	pingmian.x = X;
	pingmian.y = Y;
	pingmian.z = 0;
}
int main(void)
{
	PingMian pingmian, pingmian2;
	WGS84 wgs84, wgs84t;
	wgs84.latitude = 39.924135;//纬度
	wgs84.longitude = 116.40337;//经度
	GeodeticToCartesian(pingmian, wgs84);
	wgs84t.latitude = 40.000341;//纬度
	wgs84t.longitude = 116.52899;//经度
	GeodeticToCartesian(pingmian2, wgs84t);
 
	printf("故宫博物院X = %f ; Y = %f;\r\n", pingmian.x, pingmian.y);
	printf("北京工人体育场X = %f； Y = %f；\r\n", pingmian2.x, pingmian2.y);
	system("pause");
	return 0;
}
```

# 5.Frenet坐标

> 确定车和路之间的关系，我们通常将车辆的在大地坐标坐标转化为车辆和道路之间的frenet坐标。
> 在Frenet坐标系中，我们使用道路的中心线作为参考线，使用参考线的切线向量 t 和法线向量 n 建立一个坐标系，如下图的右图所示，这个坐标系即为Frenet坐标系，它以车辆自身为原点，坐标轴相互垂直，分为 s 方向（即沿着参考线的方向，通常被称为纵向，Longitudinal）和 d 方向（即参考线当前的法向，被称为横向，Lateral），相比于笛卡尔坐标系，Frenet坐标系明显地简化了问题，因为在公路行驶中，我们总是能够简单的找到道路的参考线（即道路的中心线），那么基于参考线的位置的表示就可以简单的使用纵向距离（即沿着道路方向的距离）和横向距离（即偏离参考线的距离）来描述，同样的，两个方向的速度（s˙ 和 d˙）的计算也相对简单。
> ![在这里插入图片描述](https://img-blog.csdnimg.cn/2019061616283316.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)
> 本文不涉及规划和控制的知识，对Frenet有兴趣的同学参考以下链接：
> [AdamShan的博客](https://blog.csdn.net/adamshan/article/details/80779615)