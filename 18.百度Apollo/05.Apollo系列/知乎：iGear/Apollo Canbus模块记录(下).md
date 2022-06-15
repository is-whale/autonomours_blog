- [Apollo Canbus模块记录(下) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/424329672)

上一篇我们讲了canbus模块如何通过CanReceiver从底层接收感兴趣的can报文，提取其中的信息填充到ChassisDetail中并发布出去的整个过程。这篇我们继续详细讲述上层传过来的control command是如何发送给底盘的。

## 3.控制信息的下发

这一块主要包括控制命令的接收解析、以及包含控制信息的can报文的发送两部分内容。

### 3.1 控制命令的解析

先来看control command的接收。CanbusComponent::Init()有这样一段代码：

![img](https://pic3.zhimg.com/80/v2-2435c6cc1852ad36c1af97b1a2e6f486_720w.jpg)

因FLAGS_receive_guardian为false，所以9 - 14行创建了一个Reader来接收ControlCommand消息，收到消息以后就会调用13行注册的OnControlCommand()来进行解析：

![img](https://pic3.zhimg.com/80/v2-56ddf40e523f8ccac2d9c5f96506498e_720w.jpg)

2 - 18行对ControlCommand消息的时间戳和序列号进行检查，而函数的主要动作是20行的vehicle_controller_->Update()和25行的can_sender_.Update()。我们先看前一个Update():

![img](https://pic3.zhimg.com/80/v2-b5f9e595a79b883c8e13a405591e12b6_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-75677b2f0f7f85dcb3df248960f6a88d_720w.jpg)

7 - 36行是根据ControlCommand.pad_msg中的action字段进行驾驶模式的切换，38行至函数末尾都是根据ControlCommand的相应字段对要发往底盘的控制信息进行更新，以42 - 44行为例，若ControlCommand包含gear_location字段，则调用Gear()对档位进行调整：

![img](https://pic4.zhimg.com/80/v2-7628acfdef2421ac2f0bfbf70efafa97_720w.jpg)

set_gear_shift()定义在这里：

![img](https://pic1.zhimg.com/80/v2-aa31ee0b697854a141dd65b3a4854c3c_720w.jpg)

这个函数功能很简单：将目标档位值保存在gear_shift_成员变量中，稍后我们会看到这个动作的功用。顺便提一下，这里的Autocontrol183类是不是看起来有些眼熟？对的，就是这个类在前面介绍的PixloopMessageManager类的构造函数中被定义为“Control Messages”，用于向下发送控制信息。

现在回到CanbusComponent::OnControlCommand()函数，20行的Update()返回后，会在25行调用can_sender_.Update()：

![img](https://pic1.zhimg.com/80/v2-ec5e7470c8b65478ee1917afbacee7a4_720w.jpg)

这个函数遍历调用send_messages_中每个元素的Update()函数。CanSender的send_messages_在PixloopController::Init()函数中设置：

![img](https://pic3.zhimg.com/80/v2-bbedac1d415733587998db9a15e0db66_720w.jpg)

第15行的AddMessage()的定义为：

![img](https://pic1.zhimg.com/80/v2-3be9533f17aab1556610157d1d57ca14_720w.jpg)

所以，对于Pixloop车型来说，CanSender的send_messages_只有一个元素，该元素是用PixloopMessageManager中唯一的“Control Messages”即Autocontrol183对象构建的一个SenderMessage对象。因此，CanSender<SensorType>::Update()中第4行调用的是SenderMessage::Update():

![img](https://pic2.zhimg.com/80/v2-7a4f2bca994dd92253f18f8dc2778445_720w.jpg)

第7行调用protocol_data_->UpdateData()，其实就是调用Autocontrol183:: UpdateData():

![img](https://pic4.zhimg.com/80/v2-7c56e29217f6a5d0005a0db5cf448197_720w.jpg)

前面我们看的是档位的例子，所以这里我们重点关注11行的set_p_gear_shift()，注意这里的参数gear_shift_正是Autocontrol183::set_gear_shift()中设置的那个成员变量：

![img](https://pic3.zhimg.com/80/v2-bdf34047632c749424b54f6b6aac079e_720w.jpg)

函数也很简单：就是用ControlCommand中包含的目标档位的值设置要发送的can报文的相应字段。类似地，can报文其他字段的设置，比如steering、braking等等，采用的是同样的机制。

至此，我们对上层传递过来的ControlCommand中的信息如何转化为can报文中的控制字段的过程介绍完毕，下面我们再来看看控制型can报文的发送。

### 3.2 控制型can报文的发送

与CanReceiver负责报告型can报文的接收类似，控制型can报文的发送由CanSender负责。CanSender在CanbusComponent::Init()中初始化：

![img](https://pic4.zhimg.com/80/v2-81568d0961e7b17d98f96839d2deab23_720w.jpg)

CanSender::Init()很简单，主要是保存用于can报文发送的can_client_，见下面函数定义的13行：

![img](https://pic4.zhimg.com/80/v2-d1444ee733a3a25af5fcc77397ad194b_720w.jpg)

CanSender的启动也在CanbusComponent::Init()中：

![img](https://pic1.zhimg.com/80/v2-366dd33107cd3a6f93665f4c3e850b50_720w.png)

CanSender::Start()定义如下：

![img](https://pic4.zhimg.com/80/v2-bde2ff4f5a070f2b35f7606297059183_720w.jpg)

第8行启动了一个线程来运行PowerSendThreadFunc():

![img](https://pic3.zhimg.com/80/v2-61ced1b608c0bdeece85611738d0136a_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-76bbb2c0d2c1b216088bfec1ee33f925_720w.jpg)



PowerSendThreadFunc()函数的主体是22 - 39行的for循环：对于send_messages_中的每一个元素message，如需要发送，则在31行取出存储在该message中的、已经根据上层ControlCommand更新过的、需要发送的CanFrame，然后在33行调用can_client_->SendSingleFrame()将其发送底层的can驱动程序。

## 4.小结

通过上面的介绍，再次总结canbus模块的主要功能包括两方面

一方面，用CanReceiver对象通过CanClient::Receive()从底层的can驱动程序接收指定的报告型can报文，提取其中包含的底盘信息并以Chassis/ChassisDetail消息的形式发布到系统中；

另一方面，接收上层发送的底盘控制ControlCommand消息，解析其中的控制指令，构建相应的控制型can报文，用CanSender对象通过CanClient::SendSingleFrame()将控制型can报文下发给底层的can驱动程序，从而实现了对底盘的操控。