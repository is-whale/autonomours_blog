- [CAN总线-增强诊断服务篇3---应用层_谢银森的博客-CSDN博客](https://blog.csdn.net/xieyinsen/article/details/123929168)

资料来源14229

先说清楚报文组成的状态

应用层协议单元A_PDU

第一个 byte 为 SID 即服务ID 比如0x10 会话模式服务

后七个字节是具体会话内容。

对应的应答 SID 偏移0x40 比如0x10 应答 SID 0x50 

后面字节分为肯定响应和否定响应，和响应内容。

应用层主要分为六大模块



# 诊断和通信管理：

​    

![img](https://img-blog.csdnimg.cn/9d2af748b42e43eeac468e2fd7ab8e10.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)

主要介绍

0x10 

0x27

# 数据传输：

![img](https://img-blog.csdnimg.cn/97fcbf8db57046b48f7ba5c60891289b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)

 0x22

 0x2A

 0x2E

# 数据的清洗和读取

![img](https://img-blog.csdnimg.cn/f11b30efc30b45828841cb73d364291b.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)



# 上传下载：



![img](https://img-blog.csdnimg.cn/b1ccd7b7f4c8491a96e2e364c8c6bded.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)





# 例程控制

0x31 



[UDS(四)应用层_车小猿的博客-CSDN博客_uds应用层协议](https://blog.csdn.net/weixin_44522306/article/details/113939885)

[UDS(五)应用层10/3E_车小猿的博客-CSDN博客_uds应用层协议](https://blog.csdn.net/weixin_44522306/article/details/114014780)

[UDS（六）应用层 11/27_车小猿的博客-CSDN博客](https://blog.csdn.net/weixin_44522306/article/details/114087836)

[UDS（七）应用层 28/85_车小猿的博客-CSDN博客_uds85服务运用](https://blog.csdn.net/weixin_44522306/article/details/114255675)