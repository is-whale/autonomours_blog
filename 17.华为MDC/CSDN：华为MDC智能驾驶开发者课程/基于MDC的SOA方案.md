- [基于MDC的SOA方案_林北不要忍了的博客-CSDN博客](https://blog.csdn.net/weixin_43849505/article/details/118054666)

这一部分对应华为云课堂网课的第四章的第二节，这一节主要是讲解了SOA的相关知识，并且再一次讲解了一下AUTOSAR的知识。

## 一、SOA与汽车工业发展的结合

所谓SOA，全称是面向服务的[架构](https://so.csdn.net/so/search?q=架构&spm=1001.2101.3001.7020)，英文名称是Service Orient Architecture，是一种ICT行业现在已经普遍使用的架构，是一种随着软件发展而产生的新架构。

引用一段百度百科的介绍：面向服务的架构（SOA）是一个组件模型，它将应用程序的不同功能单元（称为服务）进行拆分，并通过这些服务之间定义良好的接口和协议联系起来。接口是采用中立的方式进行定义的，它应该独立于实现服务的硬件平台、操作系统和编程语言。这使得构建在各种各样的系统中的服务可以以一种统一和通用的方式进行交互。面向服务架构，它可以根据需求通过网络对松散耦合的粗粒度应用组件进行分布式部署、组合和使用。服务层是SOA的基础，可以直接被应用调用，从而有效控制系统中与软件代理交互的人为依赖性。

与传统的架构相比，SOA实现了更好的复用、标准化，并且实现了软件与硬件之间的解耦，从硬件角度来看SOA具有集中化、网络化的特点，而从软件角度来看，SOA具有服务化、标准化的特点。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210619154123540.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
自动驾驶汽车系统也在向着SOA架构的方向发展，逐步向着集中化、标准化和服务化的方向进步。而实现标准化和服务化的就是AUTOSAR。前面的博客里总结过AUTOSAR的基本知识，这里就不多写了，放张截图。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210619154929216.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

## 二、采用SOA的优势

介绍面向服务的优势之前，最好先介绍一下前辈面向信号。具体的定义本人也说不清楚，根据网课讲师的介绍，大体上面向服务可以理解为面向对象，而面向信号可以理解为面向过程。面向服务通过订阅服务，传递的实际上是行为和能力，而面向信号是数据上的传递。打个比方需要完成一道数学难题，采用面向信号的方式就是你打了个电话，然后电话那头找个人做完，直接告诉你正确答案，而采用面向服务是派了个解题专员来给你解题，除了得到结果，还可以获得更多的讲解。面向服务是更加抽象的方式，是一种能力的传递。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210619160518772.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

除此之外，面向服务于面向信号还存在下面的这些差异。从图里也不难看出，开发效率那里，面向signal的开发是一种面向过程的方式，而面向service的开发是一种面向对象的方式，用这两个本科阶段常提的概念去理解这两种面向会更加简单。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210619164307422.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

知道这两种方式之后，理解SOA的优点就更加容易了。先从服务下手，所谓服务，值得就是一些被很好定义了接口和被封装了的业务单元。从定义来看很像是对象的定义，业务单元可以被调用并且向外提供一些一致而且有效的数据。服务之间通过接口来连接彼此，接口的定义已经是老生常谈了，由于接口的存在，服务的实现和接口的定义互不干扰，所以在灵活性和可扩展性上更加方便，只要接口保持不变，内部服务的更新迭代并不影响基于服务的APP的使用。

服务使用标准的通信接口，具有独立性、黑盒性、模块化的特征，可以远程访问、独立修改与更新，通过抽象的服务总线进行通信，采用SOA更加利于复用、动态集成、灵活部署，从而快速构建复杂的系统。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210619162132702.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

上图就是对SOA架构的一个示意，各个服务通过接口连接入服务总线，并且都遵守标准化的协议。采用这种结构之后，开发者关注的是怎样通过服务来构建自己的业务，而与服务本身的实现就没什么关系了。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021061916262573.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

除此之外，由于是通过接口相连，service支持的业务层具有良好的演进和升级能力。这里主要表现为两个方面，一个是前向兼容性，指的是旧版本的接收者可以以忽略新版本发发送接口中的新增部分，另一个是后向兼容性，指的是新版本接收者识别旧版本发送接口中的可用部分。这两个性质的存在，使得服务的收发双方及时版本不一致，也可以稳定地运行，从而保障系统级的持续演进与升级能力。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210619163139879.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

此外，服务支持灵活的部署方式，并不要求所有服务都部署在本地，即使在不同的ECU也可以正常使用。正是这个特点，让SOA更加方便开发。不仅仅是物理位置的松耦合，service还支持应用于物理设备间的灵活映射。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210619163838453.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

上面介绍的都是服务的有点，而SOA的灵魂就是服务，所以采用服务这一机制的SOA的优点基本上也就是服务的优点。采用SOA架构更加方便云/路的连接，即使服务的物理部署不在本地，也可以方便地完成应用的开发，并且在移植等方面也变得更加方便，用户可以利用服务，快速构建不同的APP功能。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210619161016352.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

## 三、面向服务的MDC软件栈

上面说了那么多面向服务的优点，还是为了引出面向服务的MDC。MDC的设计也是面向服务的，其主要的结构如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210619165712806.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
在硬件平台与应用之间，是MDC软件栈，软件栈包括三部分：MDC Studio、MDC Brain以及MDC Ware。

MDC Studio是MDC的开发工具链，支撑APP的设计、开发调试等功能。MDC Ware是MDC软件中间件，提供基础功能和各类服务，能够提供APP的运行环境。MDC Brain则是MDC业务的软件库和功能SDK，支持业务的功能定义和快速开发。