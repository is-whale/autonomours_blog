- [CAN总线测试与汽车以太网测试的区别_怿星科技的博客-CSDN博客_can总线与以太网区别](https://blog.csdn.net/m0_47334080/article/details/106074991)
- [以太网与CAN的区别_怿星科技的博客-CSDN博客_can通信和以太网通信](https://blog.csdn.net/m0_47334080/article/details/106939302)

相信汽车电子领域的工程师们对于[CAN总线](https://so.csdn.net/so/search?q=CAN总线&spm=1001.2101.3001.7020)都非常熟悉，而随着以太网在汽车领域应用的增多，大家对于汽车以太网也已经有了一定的了解。今天我们将通过CAN总线通信与以太网通信在协议及拓扑上的区别引入CAN总线与以太网测试上的区别。

## 一、CAN总线与汽车以太网在协议上的区别

CAN总线协议主要分为三层：**物理层、[数据链路层](https://so.csdn.net/so/search?q=数据链路层&spm=1001.2101.3001.7020)和应用层**，我们在实际应用中所使用的CAN总线协议也相应比较少。

![img](https://img-blog.csdnimg.cn/20200512154237500.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ3MzM0MDgw,size_16,color_FFFFFF,t_70)

（CAN总线协议)

而**汽车以太网主要分为物理层、数据链路层、[网络层](https://so.csdn.net/so/search?q=网络层&spm=1001.2101.3001.7020)、传输层以及应用层**。

下图为汽车以太网的常用协议，从中可以看出汽车以太网所使用的协议非常多。

除常用协议外，由于以太网协议的兼容性，我们也可以将物联网常用的MQTT协议、传统通信行业的HTTP协议等应用于汽车以太网中。

![img](https://img-blog.csdnimg.cn/202005121543322.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ3MzM0MDgw,size_16,color_FFFFFF,t_70)

（汽车以太网常用协议）

## 二、CAN总线与汽车以太网在拓扑上的区别

**CAN总线是总线型网络 (广播式通信) ，即所有节点都连接到同一个传输媒介中，也就是说传输媒介中的电信号会影响到所有的节点。**

一般而言，总线通信中一条CAN线上会挂多个节点。

![img](https://img-blog.csdnimg.cn/20200512154401419.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ3MzM0MDgw,size_16,color_FFFFFF,t_70)

（CAN总线拓扑）

**汽车以太网是交换式网络 (交换机式通信)，即网络中有终端节点和交换机节点。**

交换机式通信指的是所有的终端节点都要通过交换机才能连接到一起，所有传递的信息都需要交换机进行转发。

![img](https://img-blog.csdnimg.cn/20200512154503447.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ3MzM0MDgw,size_16,color_FFFFFF,t_70)

（以太网拓扑）

## **三、CAN总线与汽车以太网在测试上的区别** 

由于CAN总线与汽车以太网在协议和拓扑上的不同，带来从设计、实现、测试的各种不同，下面我们重点介绍测试上的不同：

### 1、协议不同带来测试上的不同

汽车以太网一方面引入传统以太网的协议，另一方面新增了汽车应用相关的协议。因此汽车以太网通信涉及的协议更复杂，需测试内容更多。

从下表可以直观的看到汽车以太网测试与CAN总线测试在协议测试上的区别。

![img](https://img-blog.csdnimg.cn/20200512154616922.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ3MzM0MDgw,size_16,color_FFFFFF,t_70)

### 2、拓扑不同带来测试上的不同

A、由于以太网采用的是交换机式网络，因此其物理层测试与CAN总线的物理层测试差异很大。CAN总线物理层测试仅需测试电气特性；而汽车以太网一方面需要进行PMA测试，即对物理层的电气特性进行测试，另一方面还需要针对其点对点特性进行 IOP测试。

B、汽车以太网区分终端节点与交换机节点，由于所有传递的信息都需要交换机进行转发，对于交换机性能要求很高，因此终端节点与交换机节点的测试内容及测试工具均有所区别。交换机节点特有的测试内容包括交换机性能测试、交换机功能测试、路由测试。交换机节点部分测试如交换机性能测试等需要网络测试仪辅助测试，如下图所示：

![img](https://img-blog.csdnimg.cn/20200512154651360.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ3MzM0MDgw,size_16,color_FFFFFF,t_70)

（交换机节点测试拓扑）

C、汽车以太网的集成与整车测试与CAN总线测试区别很大，点对点通信导致集成测试难度较大，通常采用特殊测试拓扑和测试设备(VN5640的Bypass功能)来实现，如下图所示：

![img](https://img-blog.csdnimg.cn/20200512154736177.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ3MzM0MDgw,size_16,color_FFFFFF,t_70)

而CAN总线仅需直接连接到总线上即可监控总线上所有报文，如下图所示：

![img](https://img-blog.csdnimg.cn/20200512154833506.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ3MzM0MDgw,size_16,color_FFFFFF,t_70)

## 四、信息收发方式不同   

CAN总线为**广播式**通信，一个节点发送信息会占据所有通信媒介，发送节点只管自己发送，不关心谁去接收，总线上所有通信节点都会收到信息。

接收节点则根据自身的情况来决定是否接收信息。这就类似于在会议室里开会，一个人发言所有人都能听见，发言内容与谁相关，谁去关注就OK了。

![img](https://img-blog.csdnimg.cn/2020062411031817.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ3MzM0MDgw,size_16,color_FFFFFF,t_70)

 

以太网的交换机式通信，则是**点对点**的通信方式。发送节点在发送信息前，会首先想好信息要发送给谁，然后会把自己的地址和接收方的地址放到报文里去。节点A需要发送信息给节点B，可以简单理解为交换机内部把端口1和端口2给连起来了，因此信息就从A传到了B。在A和B收发的过程中，C/D/E节点都没有收到信息，他们之间的通信媒介也没受到影响。这就类似于打电话，一个人拨通另一个人的电话号码，就只有这两个人互相通话。那么如果有信息需要从发送节点发给多个节点，相当于召开多方电话会议，怎么办呢？这就有了多播和广播的概念。

![img](https://img-blog.csdnimg.cn/20200624110307143.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ3MzM0MDgw,size_16,color_FFFFFF,t_70)

 

多播指一对多的信息发送，广播指一对所有的信息发送。如果A节点希望**发送信息给多个节点**，则需要将自己的地址和多个接收方的地址（是一个提前设置好的多播地址）放到报文里去，此时可以简单理解为交换机把发送方的端口同多个接收方的端口连接起来了，因此信息就从A传到了多个节点。如果A节点希望**发送信息给所有节点**，则需要将自己的地址和所有接收方的地址（是一个提前设置好的广播地址）放到报文里去，此时可以简单理解为交换机把发送方的端口同所有端口连接起来了，因此信息就从A传到了所有节点。

![img](https://img-blog.csdnimg.cn/2020062411025155.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzQ3MzM0MDgw,size_16,color_FFFFFF,t_70)