- [LGSVL官方文档理解（二）：传感器分类和消息包发布示例 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/351353885)

[全面小康冲鸭：LGSVL官方文档理解（一）：仿真的入门、配置和启动5 赞同 · 5 评论文章![img](https://pic4.zhimg.com/v2-b4fee1aaafdbaae1baed9154a1dfff4f_180x120.jpg)](https://zhuanlan.zhihu.com/p/350301593)

上面这篇文章是结合LGSVL官方文档，对如何配置仿真地图、车辆、仿真包做了简单的介绍，并一步一步的讲解如何启动一个自定义的仿真包。如果你仔细阅读了上面这篇文章，那么你应当能够顺利的启动一个仿真，并且了解WebUI界面和仿真器界面各个部分的作用都是什么。所以，我非常建议先看上面这篇文章。

本篇文章结合LGSVL官方文档里的“传感器”和“消息包”部分，讲解一些有关的基础知识，尽管会比上一篇文章较为难理解，但我会尽量用白话的语言讲解，你应当能够看懂。不过，我仍然建议你去看官方英文文档。

[Home - LGSVL Simulatorwww.lgsvlsimulator.com/docs/](https://link.zhihu.com/?target=https%3A//www.lgsvlsimulator.com/docs/)

**1、官方内置的传感器**

先给出这一章的官方链接

[Sensor parameters - LGSVL Simulatorwww.lgsvlsimulator.com/docs/sensor-json-options/](https://link.zhihu.com/?target=https%3A//www.lgsvlsimulator.com/docs/sensor-json-options/)

一般传感器有几个较为重要的参数：

- type: 传感器的类型，例如：Color Camera
- name：传感器的名字，对应于传感器的类型用于标识一个传感器，是自定义的，在车辆配置JSON中，一个传感器类型可以有多个传感器名字。例如：针对于Color Camera类型，我们可以根据传感器transform的不同，分别配置三个不同位置（中心、左、右）的传感器，分别是Center Camera、Left Camera、Right Camera。
- transform：是传感器的一组属性，包括 x, y, z, pitch, yaw, roll。
- parent：当前传感器的“父传感器”，类似于父节点、父类的意思，如果不为空，那么当前传感器的transform就是相对于父传感器的。如果为空，当前传感器的transform是相对于车辆中心点的。
- params：是当前传感器特有的一组属性，比较关注的有两个：topic（传感器发布/订阅的主题）和frame（传感器的frame_id，用于标识传感器）。除此之外，还有像Width，Height，Frequency等，从单词的意思上可以直观理解。这里简单说一下“发布和订阅”，完整的来说是“依附在车辆上的传感器，从仿真器（simulator）中通过桥接发布消息给ROS节点”和 “依附在车辆上的传感器，在仿真器中从ROS节点中通过桥接订阅消息”，那么针对仿真器，简单来说“发布”就是“simulator -->> ROS”，“订阅”就是“ROS -->> simulator”。暂时不理解不用担心，后面会通过例子讲到。

那么这里不妨先看一下Apollo3.0的传感器配置（也就是我们在webUI界面中给车辆配置的传感器JSON），你应该能够稍微的明白，可能你还不太懂type属性和topic属性应该怎样写，没关系，后面会讲到：

[Sample sensor configuration - LGSVL Simulatorwww.lgsvlsimulator.com/docs/apollo-json-example/](https://link.zhihu.com/?target=https%3A//www.lgsvlsimulator.com/docs/apollo-json-example/)

下面对几个较为重要的、常用的传感器做介绍，大体了解每个传感器发布/订阅什么消息，再结合官方的例子学习怎样写传感器配置即可，我们的重点还是在后面的消息上的讲解（官方这一章节都有例子，这里就不再给出）：

1> Clock 很直观，配置也很简单，用于发布仿真时间。

2> Color Camera 彩色相机 & Deep Camera 深度相机 & Segmentation Camera 语义分割相机

很好理解，这三种相机可以发布拍摄到的图片，可以把这些图片用于基于视觉的深度学习（车道线识别、车辆识别、交通灯识别等），根据transform的不同，相机的位置就不同，那么拍摄到的画面也就不同。官方文档中有一[章节](https://link.zhihu.com/?target=https%3A//www.lgsvlsimulator.com/docs/lane-following/)专门是用Color Camera采集到的左中右图片做CNN，用于车道保持，以后有机会会专门针对这一章节进行讲解。下面分别是三种相机所拍摄到的画面：

![img](https://pic4.zhimg.com/80/v2-d27dc99aa1a4eda77d24eb396ea9f4d3_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-1101b4b96b27764f8e272bf2f79f0c51_720w.jpg)

![img](https://pic3.zhimg.com/80/v2-c2f056b97c0642bd8f5fabe41c53bada_720w.jpg)

3> 3D ground truth 3D标签& 2D ground truth 2D标签 & Signal Sensor

这三个是LGSVL已经集成好的传感器，相当于直接帮我们把自动驾驶中的“检测”做好了，直接给出车辆、行人、交通信号灯的“包围框”。那么直观的看到下面的图片，你就知道这三种传感器都是干什么的了。多说一句，你可能会看到不同颜色的包围框，这是因为LGSVL规定了针对于不同的交通参与者、信号灯的状态赋予了不同包围框的颜色，详细的介绍请看官方文档。

![img](https://pic4.zhimg.com/80/v2-ba64eced8cc906495586c4756ffdafaf_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-de22bc98bcbb5189bdb2ba25969f2edd_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-12eaef3d433e6a7b0266dc5acf56831f_720w.jpg)

4> CAN-Bus 车辆底盘信息，用于发布车辆的速度、油门、刹车、大灯/警示灯/转向灯/雾灯/雨刷的状态、经度/维度/海拔等。

![img](https://pic2.zhimg.com/80/v2-9fdb5ec1f46281cedee78743d9ccc24d_720w.jpg)

5> Vehicle Control 车辆控制 & Vehicle State 车辆状态 用于从ROS订阅车辆控制信息（加速度、制动、车辆转向角、目标档位等）和车辆状态信息（大灯/车身灯/雨刷/手刹/驾驶模式灯）

![img](https://pic1.zhimg.com/80/v2-6137e713c280d79074cf75a994a52498_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-dfbe1e9aa347ed2a4879bd0c85cc3691_720w.jpg)

6> Lidar 雷达 用于发布每轮扫描获得的点云数据。

7> Keyboard Control 快捷键控制，是不是很眼熟，我们刚开始使用键盘来控制车轮移动、漫游地图，就是利用的这个传感器的功能，它的JSON配置是我们认识到的第一个传感器配置。

> { "type": "Keyboard Control", "name": "Keyboard Car Control" }

一般来讲，上面讲到的传感器足够我们的需要，无论你是做视觉上的深度学习、强化学习，还是做决策规划，都已经足够了。除此之外，还有激光雷达（似乎暂时还不支持，需要自己定义传感器插件）、车轮控制、手动控制、GPS、IMU（惯导）等。当然，还有更高级的，是自定义传感器插件，需要结合Unity Prefab和C#编程，以后有时间我会慢慢写。

这是一份JSON配置的文件，你可以复制粘贴到车辆的配置框里，不出错的话，你可以像我一样可视化这些传感器订阅/发布的消息：

NULL

**2、为了便于直观的理解，不得不依靠代码，给出仿真器发布消息包的示例。**

我们以“传感器JSON配置”和“消息包”，结合ROS2桥，通过一个示例来尽可能的让你理解是如何进行消息发布/订阅的。

我们以彩色相机传感器为例，先给出传感器的关键JSON配置，你应当已经理解这些参数的意义。（你应当通过官方给的例子知道怎样配置一个传感器JSON）：

- "type": "Color Camera",
- "name": "Main Camera"
- "Topic": "/simulator/sensor/camera/center/image/compressed"
- "Frame": "main_camera"

那么与之对应的消息包为（因为彩色相机、深度相机和语义分割相机属于相机传感器，第一列是消息类型名，第二列是桥接类型，第三列是消息依附的传感器（有可能没有依附的传感器），这样看来，是否就和上面的传感器JSON有关联呢）：

![img](https://pic3.zhimg.com/80/v2-8cf2ec11c92333d635f3fef89919d776_720w.jpg)

那么我们如在代码中使用呢？

```python3
# 与ROS相关，一般都要导入下面两句
import rclpy
from rclpy.node import Node
# 很重要，对应到想要发布/订阅的消息（参考上面给出的消息包），其他消息也类似，例如
# from lgsvl_msgs.msg import Detection3DArray
# from lgsvl_msgs.msg import SignalArray
# from lgsvl_msgs.msg import VehicleControlData
from sensor_msgs.msg import CompressedImage

# 必须继承ROS节点类，Node
class Drive(Node):
    def __init__(self):
        # 有父类的时候初始化，不懂这个方法的去Google吧 
        super().__init__('drive')
        # 很重要，是一种订阅消息的方法，参数即为“消息类型”，“主题”，和回调函数
        # 是不是又和上面的传感器参数和消息包对应起来了呢
        self.sub_image = self.create_subscription(CompressedImage, '/simulator/sensor/camera/center/image/compressed', self.callback)
    # 回调函数，很直观，用于打印出格式化的消息，测试消息有没有收到。
    # 注意：这是ROS节点内的代码，所以相对于ROS节点来说，是从仿真器中订阅消息的。
    def callback(self, msg):
        self.get_logger().info('Subscribed: {}'.format(msg.data))

# 下面就是很固定的语句，用于启动一个ROS节点
def main(args=None):
    rclpy.init(args=args)
    drive = Drive()
    rclpy.spin(drive)

if __name__ == '__main__':
    main()
```

上面是一个完整的ROS包代码部分（期望你对ROS包和节点有基础的了解，没有的话暂时也没有关系），你应当能够从代码的内容，配合我写的注释，简单的理解想要表达的意思。**这就是对应到上面“传感器+消息”的订阅代码的书写方式。**

OK ，现在你应当对传感器有足够多的理解，同时也对消息包的发布有了一定的了解。那么后面我将重点讲解LGSVL 中官方提供的消息包和常用的消息类型，同时结合ROS包的搭建给出完整的“ROS节点从仿真器订阅/发布消息”的整个流程。