- [V2X 网络安全的一些总结--证书_谢银森的博客-CSDN博客](https://blog.csdn.net/xieyinsen/article/details/123906755?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-20-123906755.pc_agg_new_rank&utm_term=rsu协议&spm=1000.2123.3001.4430)

V2X 网络的特点：广播消息，无线直连。V2X 网络是针对智能交通的一种全新的网络形态。

在此之前车载领域分车内网络：CAN（ can 为主流，本文不讨论 lin 和 can、车载以太网），IP 网络（基于ip 协议，域名寻址）。

V2X 网络用于云端服务（VSP）、车载（OBU）、路测设备（RSU）之间的网络通信。

因为消息的广播特性，加上消息的位置信息，参与者丰富，面临了传统身份系统不存在的问题。

1、消息含位置信息，--隐私问题。

2、不可信源、一个不可信车辆通过广播信息可以瘫痪整个交通体系。身份伪造问题。

3、数据重放问题。

其他：高速场景，超视距的数据量，单位时间对签名验签性能高要求。

引入丰富证书系统：

![img](https://img-blog.csdnimg.cn/7ead7323f26c4399ad108fa58f6e438e.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)







不同应用采用不同证书进行安全通信。

（OBU<--->OBU RSU<--->OBU RSU<--->VSP ）

身份证书（Identity Certificate）：身份证书由应用CA签发给OBU，用于特定的车联网应用。OBU使用身份证书向RSU或VSP证明其身份，以获得后者提供的某种车联网应用服务，例如警车与红绿灯进行交互，并控制后者的状态。针对某个特定的车联网应用，OBU只能拥有一个身份证书。

![img](https://img-blog.csdnimg.cn/33c734503d414bfe97f22c9d76e86fe4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA6LCi6ZO25qOu,size_20,color_FFFFFF,t_70,g_se,x_16)