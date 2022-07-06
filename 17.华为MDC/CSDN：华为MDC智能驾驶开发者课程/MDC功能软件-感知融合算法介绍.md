- [MDC功能软件-感知融合算法介绍_林北不要忍了的博客-CSDN博客_感知融合算法](https://blog.csdn.net/weixin_43849505/article/details/118227900)

## 一、实车传感器布局

华为网课中根据实际需要，布置了实车的传感器，包括0-8号共计九个camera、6个Rader和2个GPS传感器，图示如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625201522851.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
这里的传感器布局与后面的ARXML文件配置是一一对应的，在布局中有多少个传感器，在ARXML文件中就需要配置多少个接口。

## 二、实车应用软件[架构](https://so.csdn.net/so/search?q=架构&spm=1001.2101.3001.7020)

实车内部的数据都需要经过一些列的处理过程，首先是传感器获得数据，送入感知模块，之后送入融合模块，最后送入归控模块，根据传感器的类型，主要是下面三类的传感器数据。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625202105169.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
具体来说，获取的Lidar数据要和Camera数据先进行前融合，之后再与Lidar数据和Rader数据进行大融合，这样就可以得到目标障碍物的速度、角度、距离等数据，再送入归控模块进行自动驾驶算法的相关计算。
这张图右侧是对左侧的一个展开说明，其中的Lider-n和Camera-n就是对应着实车的传感器布局，每个Camera可以和任意一个Lidar数据进行融合，一共8个摄像头所以可以组成8对组合对，前融合的8组结果再与Lidar数据与Rader数据进行大融合，最后得到归控用的数据。

## 三、应用软件开发流程（Camera检测）

这一部分以Camera的检测为例，介绍了一下应用软件开发的流程，大体的流程是接收图像-》处理图像-》结果发送。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625203508725.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
任意一个camera获得的数据，先送至抽象节点，抽象节点以唯一的实例ID来区别自身，抽象节点通过接口传至算法节点，再将算法结果以ObjectArray的形式发送给下一个节点。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625203605741.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
整个的开发流程如图所示，图里面的关键在于数据流，单看数据流是不难理解的，传感器将感知的数据送入抽象节点，之后抽象节点送至算法节点，最后将结果发送出去，而数据在节点之间的传送，就需要进行ARXML的配置，通过配置文件，来实现传送的正常运行。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625205735751.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
根据网课里面的示例，需要配置的Port对应摄像头的数目，使用new里面的port相关的element，根据实际的摄像头数目进行配置，需要注意好port的命名方式。

## 四、应用软件开发流程（Lidar检测）

这一部分以Lidar检测为例演示了应用软件的开发流程，其实这一部分和上一部分的内容大差不差，都是根据传感器数目配置ARXML文件。
使用Liadr检测，开发的基本框架也有所改变，大体的流程现在变为接收点云-》处理点云-》结果发送。传输的流程和上一部分类似，如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625211224159.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
在Lidar的检测中，使用全局唯一的InstanceID来区分和识别不同的输入输出，依然是需要配置ARXML文件来协调节点之间的通信。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210625211357966.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)