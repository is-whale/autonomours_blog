- [Apollo Canbus模块记录(上) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/423087991)

## 1.概述

　　Apollo的Canbus模块起着承上启下的作用：一方面，它收集车辆底盘以can报文形式发出的各种状态信息并以Chassis/ChassisDetail消息的形式发布到系统中；另一方面，它也接收上层发送的control command，将其中的控制信息分解成具体的控制型can报文下发给底盘，从而实现对车辆的操控。

![img](https://pic1.zhimg.com/80/v2-38d702927ec280b34ca6145c5aa505e8_720w.jpg)

Canbus模块的代码位于apollo/modules/canbus，跟底层的、具体的can card通信的代码则位于apollo/modules/drivers/canbus/这个目录。

下面我们结合具体的代码介绍一下Canbus模块的功能。

## 2.底盘信息的上报

我们先来看apollo/modules/canbus/[http://canbus_component.cc](https://link.zhihu.com/?target=http%3A//canbus_component.cc)这个模块入口文件。首先注意到CanbusComponent类派生自apollo::cyber::TimerComponent，这意味着CanbusComponent的Proc方法会被周期性的调用，Proc()的代码如图：

![img](https://pic4.zhimg.com/80/v2-34c4b51c3ac2cac7295f59786bcd4acf_720w.jpg)

Proc()主要做两件事：

\1) 调用PublishChassis()发布Chassis消息；

\2) 若FLAGS_enable_chassis_detail_pub为true，则调用　　　　　　　　　　PublishChassisDetail()发布ChassisDetail消息。

### 2.1 Chassis消息的发布

先看PublishChassis()方法：

![img](https://pic2.zhimg.com/80/v2-a1a0bb49de5188636690eefc2f131ba1_720w.jpg)

这个方法简洁明了：通过vehicle_controller_取得最新的Chassis消息，调用common::util::FillHeader()函数设置Chassis消息header的module_name、timestamp_sec和timestamp_sec字段，然后调用chassis_writer_->Write()将Chassis消息发布出去。

以PixLoop车型为例，vehicle_controller_实际指向的是一个PixloopController对象，我们来看看Chassis消息是如何在PixloopController::chassis()中生成的，由于这个函数代码较长，我们仅节选函数前面的一部分：

![img](https://pic3.zhimg.com/80/v2-c9af37c08c30296ab827f36c824fb282_720w.jpg)

7 - 20行设置Chassis的chassis_error_code、driving_mode、error_code和engine_rpm等字段，比较直观；除此之外，这个函数整个的后半部分就是根据第5行取得的ChassisDetail的内容来填充Chassis消息相应的字段，比如22 - 39行就是根据ChassisDetail来填充speed和brake字段。因此可以这样说，Chassis消息的绝大部分内容来自对ChassisDetail信息的提炼。

### 2.2 ChassisDetail消息的发布

了解了Chassis的内容从何而来，下面我们来看看ChassisDetail消息又是如何生成的。

在CanbusComponent::Init()中有这样一段代码：

![img](https://pic4.zhimg.com/80/v2-330e352f9713a1853adf2be85cb0a623_720w.jpg)

第3行调用RegisterCanClients()注册了4种can card：

![img](https://pic3.zhimg.com/80/v2-61c3d400f0d7021adbd4286d7563a666_720w.jpg)

随后第4行根据canbus模块配置文件中指定的can card类型创建CanClient:

![img](https://pic3.zhimg.com/80/v2-8b486ca964398bfc3c238baf25ef996a_720w.jpg)

以HERMES_CAN为例，其实就是创建了一个can::HermesCanClient对象并调用其Init()方法：

![img](https://pic2.zhimg.com/80/v2-7b72421f4d5318dde4836ad224585051_720w.jpg)

这个函数也平淡无奇，就是根据配置文件内容对CanClient做相应设置。

下面回到CanbusComponent::Init()代码片段，12行调用RegisterVehicleFactory()注册车型：

![img](https://pic1.zhimg.com/80/v2-ce3ed6a1f99e4caaa0aedb0b20f36ea0_720w.jpg)

对应于PixLoop车型，CanbusComponent::Init()随后第13行创建的vehicle_object指向的是PixloopVehicleFactory对象，20行创建的message_manager_实际指向PixloopMessageManager对象：

![img](https://pic3.zhimg.com/80/v2-7c85d28b3fa69ca92188723f6e49be2a_720w.jpg)

PixloopMessageManager构造函数如图：

![img](https://pic4.zhimg.com/80/v2-e84fa07bdd481f2829649a3c6ae76b4f_720w.jpg)

每个PixloopMessageManager对象包含2类Message：一类是以AddSendProtocolData()构建的、注释中所说的“Control Messages”，这类消息用于向底层发送控制命令；另一类是以AddRecvProtocolData()构建的“Report Messages”，用于底层向上层报告状态信息。这里的Autocontrol183是下行的控制型can报文，Autodatafeedback193和Autoremotecontroller283属于上行的报告型can报文：PixloopMessageManager对象就是一个can报文的集散地，统一管理着上行以及下行can报文的进进出出。

Autocontrol183是ProtocolData<ChassisDetail>的派生类：

![img](https://pic3.zhimg.com/80/v2-b2bef07e2d5e899612afe04bf48308aa_720w.png)

AddSendProtocolData()定义在PixloopMessageManager类的父类MessageManager中：

![img](https://pic4.zhimg.com/80/v2-1262eb20e927d4aa380f3ae31c21c0e3_720w.jpg)

该函数的主要功能是在第4行生成相应的ProtocolData对象并在第9行将该类对象记录在册。

类似的，Autodatafeedback193和Autoremotecontroller283也是ProtocolData<ChassisDetail>的派生类。AddRecvProtocolData()的定义为：

![img](https://pic1.zhimg.com/80/v2-8af519fbbcaa5925f10e8dd4fb5f9e38_720w.jpg)

到这里我们可以知道：PixloopMessageManager类的对象在构造时把Autocontrol183登记为要发送的数据，把Autodatafeedback193和Autoremotecontroller283登记为需要收集的can报文。那么，Autodatafeedback193和Autoremotecontroller283的信息是如何被收集的呢？收取报告型can报文的任务是由CanReceiver来完成的。我们接着往下看CanbusComponent::Init()代码片段第27行对can_receiver_的初始化：

![img](https://pic1.zhimg.com/80/v2-a7949b65672ee7a908146ae8bb03e340_720w.jpg)

这个函数5 - 8行将CanbusComponent::Init()之前创建的can_client和pt_manager传递给CanReceiver。

随后CanbusComponent::Init()会调用can_receiver_.Start()来启动Can Receiver：

![img](https://pic2.zhimg.com/80/v2-181aa5f016bcfad22024014899d84ea9_720w.jpg)

第8行创建了一个接收线程：

![img](https://pic1.zhimg.com/80/v2-159b90bad93eae2d0efba0eca751f358_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-93ee93a4a06cd4592ba38d7bac9666c9_720w.jpg)

第15行调用can_client_->Receive()从can总线收取10条can报文，对于收到的每条报文，44行调用pt_manager_->Parse()提取报文中的信息：

![img](https://pic1.zhimg.com/80/v2-dba852e8560ed56f939912f5968dfeec_720w.jpg)

这个函数4 - 5行根据can报文id找出相应的ProtocolData对象，然后11行调用该对象的Parse()方法，以Autodatafeedback193对象为例：

![img](https://pic4.zhimg.com/80/v2-418911cb21cd8939170182fa96c5351f_720w.jpg)

Autodatafeedback193::Parse()函数的主要功能是根据收取的can报文的内容设置ChassisDetail的相应字段，以emergency_stop_feedback字段为例：第3行调用18行的emergency_stop_feedback()来提取can 193报文中最后一个byte的倒数第2位的数值，并用该值来设置ChassisDetail的pixloop().auto_data_feedback_193().emergency_stop_feedback()字段。

ChassisDetail其他字段的设置与此类似，这里不再赘述。



至此，我们已经了解了canbus模块如何通过CanReceiver从底层接收感兴趣的can报文，提取其中的信息填充到ChassisDetail中并发布出去的整个过程。在下一篇文章中我们会进一步详细讲述上层传过来的control command是如何发送给底盘的。