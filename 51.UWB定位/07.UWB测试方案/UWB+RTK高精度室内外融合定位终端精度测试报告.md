- [UWB+RTK高精度室内外融合定位终端精度测试报告_北京华星智控的博客-CSDN博客](https://blog.csdn.net/qq_35699674/article/details/119859045?spm=1001.2014.3001.5502)

首先我们将融合定位终端URT放在卷尺起点0米处，获取此时终端获取到的经纬度坐标如下图所示。
![在这里插入图片描述](https://img-blog.csdnimg.cn/ecfd726abedb4d5cbf3b73b8a863fb8e.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1Njk5Njc0,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/0ed0fd883c7b427a85eef883402aeef1.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1Njk5Njc0,size_16,color_FFFFFF,t_70#pic_center)

起点获取到的经纬度坐标为：

$GNGGA,081824.00,3951.80418,N,11615.39064,E,4,12,0.75,52.0,M,-8.9,M,1.0,1937*48,3473,12

纬度：3951.80418

经度：11615.39064

将融合定位终端URT放在卷尺3米处，如下图所示。
![在这里插入图片描述](https://img-blog.csdnimg.cn/b29af1de89fe4244841618fc253a0cbb.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1Njk5Njc0,size_16,color_FFFFFF,t_70#pic_center)

尺子显示的距离是3米。

3米处终端获取到的经纬度坐标为

$GNGGA,081921.00,3951.80580,N,11615.39060,E,4,12,0.86,52.1,M,-8.9,M,2.0,1937*46,3473,12

纬度：3951.80580

经度：11615.39060

通过两点经纬度经过计算获得的距离为3.0078m，可得到水平误差小于5厘米。

将设备放在距离地面高度为116厘米的地方，通过终端获取此时的经纬度坐标为：

$GNGGA,082921.00,3951.80417,N,11615.39064,E,4,12,0.68,53.2,M,-8.9,M,1.0,1937*4F,3473,12

![在这里插入图片描述](https://img-blog.csdnimg.cn/a58aacad329f4d52a3d7d82a125779b3.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1Njk5Njc0,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2545e2952bbb4a92a8b8fcf90e03194f.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1Njk5Njc0,size_16,color_FFFFFF,t_70#pic_center)

纬度：3951.80417

经度：11615.39064

高程：53.2米

下降三脚架高度到73厘米，通过终端获取此时的经纬度坐标为：

$GNGGA,083636.00,3951.80417,N,11615.39063,E,4,12,0.75,52.8,M,-8.9,M,1.0,1937*47,3473,11

纬度：3951.80417

经度：11615.39063

高程：52.8米
![在这里插入图片描述](https://img-blog.csdnimg.cn/38bbd86b90c340f98ac59245f0a69f2d.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1Njk5Njc0,size_16,color_FFFFFF,t_70#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/64cdbfeec92e4560804b1134a574abeb.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1Njk5Njc0,size_16,color_FFFFFF,t_70#pic_center)

尺子得到的高度差为116-73=43厘米；

终端计算得到的高度差为532-528=40厘米；

可得融合终端高程[精度](https://so.csdn.net/so/search?q=精度&spm=1001.2101.3001.7020)优于5厘米。