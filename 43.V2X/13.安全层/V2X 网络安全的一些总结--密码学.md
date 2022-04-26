- [V2X 网络安全的一些总结--密码学_谢银森的博客-CSDN博客](https://blog.csdn.net/xieyinsen/article/details/123907123)

心得：最好的资料是标准。



# ECC椭圆曲线：

![img](https://img-blog.csdnimg.cn/71f8621f71f74a57a15e5b37fb531752.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_13,color_FFFFFF,t_70,g_se,x_16)





# 协议层：

一般情况下，计算数字签名时应执行以下操作：
\1. 计算原始数据的 Hash 值；
\2. 将 Hash 值作为输入，计算签名函数的输出。并不是对原始数据直接签名，而是对 Hash 值签名。
  验证签名时应执行以下操作：
\1. 计算原始数据的 Hash 值；
\2. 将 Hash 值和签名值作为输入，计算验签函数的输出，根据输出判断签名为“有效”或“无效”。
这只是一个简单描述，实际上 PKCS#1 中规定的签名和验签过程要复杂得多。

  计算 SM2 签名和验签的过程有点特殊，因为根据国内的行业标准，SM2 签名算法要和 SM3 Hash 算法搭配使用，并且计算 SM2 签名的输入并不是待签名数据的 SM3 杂凑值，而是一个预处理阶段的输出。
  预处理分为两步：
\1. 定义一个级联生成的字节流 T1 = ENTL || ID || a || b || x_G || y_G || x_A || y_A，
  其中 || 表示字节流的拼接，
  ENTL 是用两个字节表示的签名者 ID 的比特长度（注意不是字节长度），
  ID 为签名者的标识，
  a, b, x_G, y_G 都是标准中给定的值， x_A 和 y_A 是签名者的公钥,
  对字节流 T1 计算 SM3 杂凑值，得到的输出为 Z = SM3(T1)。
  这一步并没有用到待签名数据。
\2. 将待签名数据用 M 表示，将 Z 与待签名数据级联，得到 T2 = Z || M，计算 T2 的杂凑值，即   SM3(T2)，SM3(T2)才是 SM2 签名函数的真正输入。
 

签名流程：

![img](https://img-blog.csdnimg.cn/b5a3ccb07a194502be98d289be8852b3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16) ![img](https://img-blog.csdnimg.cn/c837a14e8ca64e7194177fb46414e54a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)



签名后的验证：



![img](https://img-blog.csdnimg.cn/9ef920890c18403ea2fd38bb2d6b4c15.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)

以上为协议层。



椭圆曲线层：

GBT 32918.1-2016 信息安全技术 SM2椭圆曲线公钥密码算法 第1部分：总则 2

1、基于椭圆曲线的特点，可正向多倍点。

2、关于 y 轴对称可以压缩公钥为32字节+ 标志位。

3、坐标体系

![img](https://img-blog.csdnimg.cn/3588957a581149d79426206913be83f6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)

4、多倍点

![img](https://img-blog.csdnimg.cn/1cfef46294264a8fa5489138ae7a4b1d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)





大数：大数是密码学底层数的操作。可以理解为长度32字节+的大整数。



Tommatch、GMP和 GMSSL

[libtom简介_dj0379的博客-CSDN博客_libtommath](https://blog.csdn.net/dj0379/article/details/51899818)

[GMP-C/C++（大数库）使用方法 - 新望 - 博客园](https://www.cnblogs.com/xinwang-coding/p/12803237.html)

[GmSSL/crypto at master · guanzhi/GmSSL · GitHub](https://github.com/guanzhi/GmSSL/tree/master/crypto)

# 思考：

路测、车侧、云端丰富异构设备，如何 多平台 可用？

​    openssl 不支持国密->北大安全实验室实现国密 gmssl。

​    openssl高版本开始支持国密->只对国际密码优化、没有国密优化->

​    gmssl也没有针对国密的优化->国密在车端性能不足？

优化心得：

多个维度 

1、协议层、椭圆曲线层、大数层