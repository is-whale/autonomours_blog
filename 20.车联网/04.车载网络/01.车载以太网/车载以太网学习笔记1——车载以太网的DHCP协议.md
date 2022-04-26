- [车载以太网学习笔记1——车载以太网的DHCP协议_panamera12的博客-CSDN博客_车载以太网协议](https://blog.csdn.net/wteruiycbqqvwt/article/details/106552326?spm=1001.2014.3001.5502)

常见[DHCP](https://so.csdn.net/so/search?q=DHCP&spm=1001.2101.3001.7020)过程

1、DHCP DISCOVER
当DHCP客户端计算机DHCP DISCOVER广播消息：
2、DHCP OFFER
所有接收到DHCP客户端发送的DHCPDISCOVER广播消息的DHCP服务器会检查自己的配置，如果具有有效的DHCP作用域和富余的IP地址，则DHCP服务器发起DHCPOFFER广播消息来应答发起DHCPDISCOVER广播的DHCP客户端
3、DHCP REQUEST
当DHCP客户端接受DHCP服务器的租约时，它将发起DHCPREQUEST广播消息，告诉所有DHCP服务器自己已经做出选择，接受了某个DHCP服务器的租约。
4、DHCP ACK
提供的租约被接受的DHCP服务器在接收到DHCP客户端发起的DHCPREQUEST广播消息后，会发送DHCPACK广播消息进行最后的确认，在这个消息中同样包含了租约期限及其他TCP/IP选项信息。
5、租约续约
DHCP服务器将IP地址提供给DHCP客户端时，会包含租约的有效期，默认租约期限为8天（691200秒）。除了租约期限外，还具有两个时间值T1和T2，其中T1定义为租约期限的一半，默认情况下是四天（345600秒），而T2定义为租约期限的的7/8，默认情况下为7天（604800秒）。当到达T1定义的时间期限时，DHCP客户端会向提供租约的原始DHCP服务器发起DHCP REQUEST请求对租约进行更新，如果DHCP服务器接受此请求则回复DHCP ACK消息，包含更新后的租约期限；如果DHCP服务器不接受DCHP客户端的租约更新请求（例如此IP已经从作用域中去除），则向DHCP客户端位于回复DHCP NACK消息，此时DHCP客户端立即发起DHCP DISCOVER进程以寻求IP地址。如果DHCP客户端没有从DHCP服务器得到任何回复，则继续使用此IP地址直到到达T2定义的时间限制。此时，DHCP客户端再次向提供租约的原始DHCP服务器发起DHCP REQUEST请求对租约进行更新，如果仍然没有得到DHCP服务器的回复则发起DHCP DISCOVER进程以寻求IP地址。

![img](https://img-blog.csdnimg.cn/20200604180244363.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d0ZXJ1aXljYnFxdnd0,size_16,color_FFFFFF,t_70)

***\*车载以太网 DHCP协议\****

![img](https://img-blog.csdnimg.cn/20200604171704404.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d0ZXJ1aXljYnFxdnd0,size_16,color_FFFFFF,t_70)

 

![img](https://img-blog.csdnimg.cn/20200604171816206.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d0ZXJ1aXljYnFxdnd0,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/20200604171846125.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d0ZXJ1aXljYnFxdnd0,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/20200604172001262.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d0ZXJ1aXljYnFxdnd0,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/20200604172235380.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d0ZXJ1aXljYnFxdnd0,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/20200604172405527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d0ZXJ1aXljYnFxdnd0,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/20200604172612507.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d0ZXJ1aXljYnFxdnd0,size_16,color_FFFFFF,t_70)

 

电脑上网的首要步骤，是确定四个参数：

　　* 本机的IP地址

　　* 子网掩码

　　* 网关的IP地址

　　* DNS的IP地址

这四个参数缺一不可。

​    \* 本机的IP地址：192.168.1.100
　　* 子网掩码：255.255.255.0
　　* 网关的IP地址：192.168.1.1
　　* DNS的IP地址：8.8.8.8

如果是人为手动设置，则它们是给定的，计算机每次开机，都会分到同样的IP地址，这种情况被称作"静态IP地址上网"。

但是，这样的设置很专业，普通用户望而生畏，而且如果一台电脑的IP地址保持不变，其他电脑就不能使用这个地址，不够灵活。出于这两个原因，大多数用户使用"动态IP地址上网"。

**所谓"动态IP地址"，指计算机开机后，会自动分配到一个IP地址，不用人为设定。它使用的协议叫做****DHCP协议****。**

这个协议规定，每一个子网络中，有一台计算机负责管理本网络的所有IP地址，它叫做"DHCP服务器"。新的计算机加入网络，必须向"DHCP服务器"发送一个"DHCP请求"数据包，申请IP地址和相关的网络参数。

如果两台计算机在同一个子网络，必须知道对方的MAC地址和IP地址，才能发送数据包。但是，新加入的计算机不知道这两个地址，怎么发送数据包呢？

DHCP协议做了如下规定：

**DHCP**协议，首先，**它是一种应用层协议，建立在UDP协议之上**，所以整个数据包是这样的：

![img](https://img-blog.csdnimg.cn/20200604174314335.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d0ZXJ1aXljYnFxdnd0,size_16,color_FFFFFF,t_70)

（1）最前面的"以太网标头"，设置发出方（本机）的MAC地址和接收方（DHCP服务器）的MAC地址。前者就是本机网卡的MAC地址，后者这时不知道，就填入一个广播地址：FF-FF-FF-FF-FF-FF。

（2）后面的"IP标头"，设置发出方的IP地址和接收方的IP地址。这时，对于这两者，本机都不知道。于是，发出方的IP地址就设为0.0.0.0，接收方的IP地址设为255.255.255.255。

（3）最后的"UDP标头"，设置发出方的端口和接收方的端口。这一部分是DHCP协议规定好的，发出方是68端口，接收方是67端口。

  这个数据包构造完成后，就可以发出了。以太网是广播发送，同一个子网络的每台计算机都收到了这个包。因为接收方的MAC地址是FF-FF-FF-FF-FF-FF，看不出是发给谁的，所以每台收到这个包的计算机，还必须分析这个包的IP地址，才能确定是不是发给自己的。当看到发出方IP地址是0.0.0.0，接收方是255.255.255.255，于是DHCP服务器知道"这个包是发给我的"，而其他计算机就可以丢弃这个包。

   接下来，DHCP服务器读出这个包的数据内容，分配好IP地址，发送回去一个"DHCP响应"数据包。这个响应包的结构也是类似的，以太网标头的MAC地址是双方的网卡地址，IP标头的IP地址是DHCP服务器的IP地址（发出方）和255.255.255.255（接收方），UDP标头的端口是67（发出方）和68（接收方），分配给请求端的IP地址和本网络的具体参数则包含在Data部分。

   新加入的计算机收到这个响应包，于是就知道了自己的IP地址、子网掩码、网关地址、DNS服务器等等参数。