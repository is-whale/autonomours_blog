- [基于AUTOSAR的AP平台的应用开发_林北不要忍了的博客-CSDN博客](https://blog.csdn.net/weixin_43849505/article/details/117792244)

## 一、MDC工具链总览

华为的MDC在开发过程中需要使用自己的开发工具，也就是MDC工具链。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210610213826727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
MDC工具链主要是三个部分：Mind Studio、MDS以及MMC，三个开发工具各自负责一部分，完成整个MDC的开发。
其中，Mind Studio主要是负责AI模型的生成，个人的理解这个工具负责的就是编写无人驾驶中需要的那部分AI模型，之后作为库文件导入到MDS中进行整合。这里有点像一个单独编写库文件的软件，编写好的库作为资源导入，可以直接使用。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210610223650300.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
MMC负责AUTOSAR的配置文件的管理，前面也提到过，AUTOSAR可以看作一个标准，MDC就是具体化这个标准的工具，通过这个工具生成配置文件，之后整合进MDS中。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210610223829113.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
最后MDS就起到一个IDE的作用，整合库和配置文件，生成可执行文件，最后将可执行文件放入MDC平台得到运算结果。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210610223849456.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
有Mind Studio提供的库，加上MMC的配置信息，最后由MDS编码并整合为可执行程序，运行在MDC计算平台上，这样就实现了整个MDC的一个开发流程。可以看出来，整个的可执行文件是需要在MDC平台上运行的，一旦离了这个黑盒子，整个程序是没法运行的（个人感觉华为在这波美国的芯片打压下学聪明了，即使没造出车也先占下坑）。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210610224658798.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

## 二、华为MDC Manifest Configurator

作为管理编写配置文件的程序，MMC有着自己的使用方式，这里截取一部分课程里面的教程，这个估计要一边使用一边学习了。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210610225916424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
首先是定义数据类型，将应用中的数据类型完善定义，之后定义接口，接口会涉及实例化的过程，即服务实例。之后再配置应用，包括这个应用的名称、有什么样的接口等内容。再往后是环境的配置，包括网络、环境的CPU、模式等内容。之后将实例化的服务于应用和machine做一个绑定，完成映射的端口的设置。最后配置执行管理模块的内容。

看了一下这部分的介绍，个人感觉这个MMC就是一个功能强大的配置文件管理程序，同样是XML文件，在本科准备毕设看的Spring里面，配置文件完全是手写，各种标签需要自己来记录，而MMC的优秀之处或者说是高级一些的开发工具的强大之处，就在于省去了这种无聊的配置，用一些更简单的方式去管理编写配置文件。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210610231136786.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
上图是进入以后的主界面，左侧是导入以后工程的结构树，右下角是具体的配置项，左上角也属于一个预览界面。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210620170605446.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
上图所示的是AR Packages的创建，AR Package是配置文件中用于理顺文件关系的常用结构，通过灵活创建AR Packages可以让文件结构更加清楚。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210620170859578.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
配置文件的最终需要落实到元素，也就是element上面，在对应的AR Packages里面新建需要的元素，在实际的使用中会发现，element中有超级多的选项，具体的类型还是要看配置的类型。图里面选择的是C++类型的element。创建元素之后就可以利用元素的properties界面进行元素值的修改。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210620171146631.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
除了简单的元素类型，也可以创建复杂的元素类型，比如说结构体类型，结构体类型使用的是ImplementationDataType，之后再在该元素下通过ImplementationDataTypeElement去构建整个结构体的内容。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210620171341497.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

下面按照演示视频实际操作一次，首先下载MMC压缩包，解压后运行，进入界面。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210611162147245.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
点击左上角文件，新建一个AUTOSAR Project，输入项目名称。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210611162301838.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
创建成功后，导入一个ARXML文件，使用左上角文件里面的IMPORT，选择filesystem。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210611162725407.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
将准备好的公共ARXML文件导入，全选后点击Finish即可。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021061116284362.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
导入成功之后可以在左侧看见导入的ARXML文件。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210611163407651.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
之后使用自带的校验功能校验一下arxml文件，项目名上右键，选择倒数第二个validation，之后有两种选择，由于arxml本身仍然是一种xml文件，所以完全可以按照xml文件的校验方式，这里也是查找了一下网络上的介绍，基于schema的校验更像是一种用校验xml文件的方式进行校验，翻译一下可以看成是基于模式的校验，个人理解就是照葫芦画瓢，需要一个模式文档，相当于一个标准模板，根据这个模板提供的正确范围，来进行校验。而根据model的校验我个人感觉更像是一个基于标准的校验，看官方的教学视频，里面有选择标准的一步，所以我猜测可能是根据可选的标准进行校验。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210612203754441.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
我尝试了一下故意改错一个地方，然后对比这两种校验方式有什么区别。首先是基于model的校验，改错之后选择validation model。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210611163549259.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
之后会进行校验，校验如果没有错误则会提示没错。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210611163638179.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210611163650429.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
出现错误则会在下方控制台出现提示，错误可以根据错误信息查找用户手册。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210611163909570.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
双击错误信息之后，会以下面的方式给出错误信息，是一种直观的界面形式。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210612204203867.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
下面再尝试一下使用schema的方式进行校验，换用validation schema，再次校验，得到的结果如下。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210612204647114.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
首先从校验结果来看就存在区别，这种方式校验的结果只有一个警告而没有错误，双击这个警告之后，进入的是代码的界面。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210612204754209.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
到最后也没搞明白这两种校验到底有什么区别，感觉一个是标准层面的一个是代码层面的，校验的结果都会给出错误信息，但是改正的方法不一样，好像基于model的更方便一些，毕竟是以界面的形式呈现而不是以xml的形式，看起来也更加方便。
*后面又听课时听到了一句这里的区别，基于schema是文本级的校验，二者本质上都是根据AUTOSAR的标准校验。*

## 三、华为MDC Development Studio

MDS属于一个编程的软件，可以进行代码的编写，因为程序需要跑在MDC单板上，所以会出现多人用一个板子的情况，所以华为给提供了下图所示的组网配置。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210612210843746.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
按照演示视频所示，准备好Ubuntu16.04，这里的MDS只能安装在16.04上，如果在18.04上解压，会出现无法安装的提示。
这里插一嘴，Ubuntu16.04可能会出现无法直接拖拽文件进入虚拟机的现象，解决方法是安装VMare Tools，需要在虚拟机的控制台里面输入指令：
1、sudo apt-get autoremove open-vm-tools
2、sudo apt-get install open-vm-tools-desktop
之后重启系统即可拖入文件。

将文件拖入虚拟机后，解压、安装运行环境和编译器，就可以进行项目的创建。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614171834449.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
创建一个新的MDC项目，创建后内部自带一个helloworld程序。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021061417193336.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
编译项目点击工具栏里面的小锤子，选择DEBUG进行编译。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614172055917.png)
运行则稍微有些麻烦，如果是远程连接MDC进行运行，需要输入配置信息，包括MDC的IP地址、用户名、密码以及使用的端口，用户名和密码在开发手册里面都有。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614172439147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
因为本人还没搞懂怎么外网连接MDC，这里先记录一下官方演示视频里面的操作，这里操作者是将MDC配置在了内部网络中，在同一个网络中远程登录MDC，使用的是内网IP地址，使用的账户和密码是开发手册里面提供的HOST账号。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614172725675.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
连接成功之后会有一个地址的选择，这里是选择要将本地项目放在MDC上的哪个位置。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614173410199.png)
设置成功远程连接之后，应用并且运行，控制台会显示远程运行的结果。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614173230730.png)

## 四、华为MDC软件接口平台

这一部分主要是介绍了MDC内部数据传输的通道的相关信息，主要是传感器的数据的传输。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614204405810.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
MDC内部数据通路主要是上图中的结构，从图中可以看出，数据的获取依赖于外部的传感器，而传感器通过接口与MDC连接，通过内部的分布式通信网络，在各个功能模块之间传输数据信息。MDC支持多种传感器接入，包括CAN、UART、LIN、GMSL和ETH接口。同时支持多种传感器消息输出协议，包括DDS（Data Distribution Service）和SOME/IP（Scalable Service-Oriented Middleware over IP）协议。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614204834584.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
上图是传感器数据传输方式，其实是前面一张图的细化，可以看出，Rader/ECU等主要是主要是通过CAN通道进入MCU子系统，再以SOME/IP协议与HOST子系统中的功能模块进行沟通。而GPS/IMU则使用UART通道，同样是进入MCU子系统再与HOST子系统交流。而Liadr数据因为比较大，所以直接使用车载以太接口与HOST子系统沟通。Camera则使用GSML通道，先经过ISP，ISP分成两种形式后以DDS协议送入HOST子系统。

下面总结一下上面的四种通道，对于每一种通道，主要是通道配置和收发消息结构，通道配置指的是接口的设置，而收发消息指的是消息的结构，很像是计算机网络里面的帧结构。对这部分本人也是一知半解，以前没接触过，这里只简单记录一下。
①CAN通道配置
CAN通道属于一种针对于汽车内部系统开发的总线标准，属于现场总线的一种，所谓现场总线，指的是主要用于现场设备间的数字通信的总线，个人感觉就是对时间要求十分严格的总线结构。
配置时主要修改下面的内容：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614214134156.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
收发数据的结构如下入所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614214334251.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614214428338.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
这里顺便记录一下里面提到的SOME/IP协议，这个协议是AUTOSAR标准里面的一个应用层协议，属于一种基于IP的可扩展面向服务的中间件。
所谓中间件指的是将具体业务与基本逻辑解耦的部件，通俗一点来说可以将中间件看作中间商，用户要买车，就找到买车中介，用户不需要知道车是谁的，而是由中介来提供，车主也不和车主打交道，只要自己交车能卖出去就行。

②UART通道配置
UART通道也是一种总线结构，上世纪笨重的键盘、鼠标使用的那种需要拧进去的接口就是这种通道。在UART通信中，两个UART直接相互通信，并且以异步方式发送数据。配置的方式与CAN类似，都是修改固定位置的json文件。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614220000508.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
收发信息格式如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614220035981.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021061422005230.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
一个UART帧由四部分组成：同步头、帧头、数据和校验组成，结构如下图所示：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210620153106721.png)
其中，同步头主要是负责时间同步，这部分的长度及定义可以通过配置文件进行自定义。帧头的格式和长度都可以自定义，最多可以同时配置20种数据帧头定义。校验的部分可选校验算法，目前只进行长度校验，数据的部分由收到数据的用户自己进行检验。
③ETH数据通道配置
激光雷达使用以太网直连到MDC平台，可直接使用socket连接激光雷达，获取裸数据，不需要提供额外的透传接口。
④Camera数据通道
传感器是Camera的时候，数据的传输主要是依靠GSML传入ISP，ISP将数据分成下面两种形式，之后再送入HOST子系统。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210614220351589.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)

## 五、基于AP的应用软件开发流程

这一节的网课的主讲人的吐字实在是不清楚，听的也一知半解，这一节的网课主要演示的是一个MDC上的程序的开发过程。
前面提到过MDC开发首先是利用MMC产生配置的ARXML文件，导入到MDS之后利用Mind Studio产生的库文件进行编程，最后运行在MDC上，应用软件的开发流程大体就是如此。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210616224342447.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
MDC其中有两个可以用于二次开发的芯片，一个是鲲鹏920，称为HOST，另一个则是昇腾310，成为一Mini，前者注重于计算能力，是服务器级的CPU，后者是图形处理芯片，需要使用GPU加速的功能软件应该部署在Mini上，其余节点可以部署在HOST上。
网课下面示范了一次配置ARXML，这一部分讲师的吐字简直崩溃。一个新建的ARXML文件的结构如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210616225301122.png)
与官方演示视频里面的结构有所差别，官方的结构是platform是在common外面的，应该也不影响。一个ARXML最关键的三个部分：common、functional_software和platform。
其中，common部分主要是一些公共配置，主要包括一些基本的数据结构、与硬件的配置和接口的配置，从名字也可以看出，这部分是所有ARXML都需要的，展开common来看，里面的部分也和其功能相对应：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210616230013436.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
functional_software部分主要负责功能软件节点的配置，包括：应用配置文件、执行进程配置以及软件服务示例配置文件。
platform主要是平台提供的服务，包括驱动、传感器、抽象等，展开这个部分也可以看出，里面包含特别多的配置文件，正好对应平台提供的各种服务。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210616230250463.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
之后网课以transform为例展示了interface的配置，这里也按照演示视频跟着操作了一遍。首先在对应的位置新建新增一个ARPACKAGES，用于存放功能软件消息需要用到的基础数据结构，直接在对已你搞的位置下面右键new一个ARPACKAGES，之后RENAME（也可以直接点击包，修改ShortName项）即可。这里的ARPACKAGES主要是AUTOSAR中一个用于防止系统出现重复命名的结构。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021061623123916.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
之后创建一个接口arxml文件，反复看网课，也没听见讲师说这个arxml文件放在哪里。一个接口包括两部分，一个是接口消息的结构体配置，另一个是接口的通信方式和消息推送的模式配置，命名的时候按照xxx_service_interface的模式来进行。根据接口的要求，在对应的位置新建两个Package。新建arxml文件使用的是new里面的AUTOSAR File，之后根据结构层层新建package即可。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210616234743341.png)
建好之后首先编写第一个package，这里需要按照ROS msg消息编写结构体，由于不清楚这个消息的格式，所以这里只放一下演示里面的截图。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210616235439689.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
之后编写另一个package，这个package分为两部分，分别是传输协议配置和消息推送模式配置，按照图示的结构进行编写。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210617000641848.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
这样就完成了一个interface的配置。接下来是以transform为例的functional_software的演示，这一部分最主要的是三个arxml文件：Transform_application.arxml（应用配置文件）、Transform_excution.arxml（执行进程文件）和Transform_service.arxml（软件服务示例配置文件），按照下面所示的形式建好arxml文件：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210617002119436.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
其中Transform_application.arxml主要配置了软件服务对应的应用组件是什么、提供哪些类型的服务端口以及可执行文件与软件根组件之间的映射关系，Transform_excution.arxml主要配置了软件组件的可执行文件与部署进程之间的映射关系、进程资源配置以及进程启动相关的配置参数，最后Transform_service.arxml配置了软件服务都有哪些服务实例，这些服务实例如何部署以及服务实例与应用端口以及machine实例之间的映射关系。
这里是以transform为例，所以所有的命名都是transform开头的，在实际配置各类文件时，将名称更换为对应的名称，结构按照这个继续就行。
之后配置这三个arxml文件，流程如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210617002809338.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210617002822829.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210617002838544.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210617002914482.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2021061700292730.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210617002939362.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80Mzg0OTUwNQ==,size_16,color_FFFFFF,t_70)
完成这些配置之后，就得到了一个完整的arxml，导出后可以在MDS里面导入，生成符合AP AUTOSAR架构的代码框架，然后再填写代码，编写每个函数内部的具体实现。

总结一下的话，这最后一节主要是讲解了两个arxml的配置实例，一个是配置interface，另一个是配置functional_software。
配置interface主要是两部分：消息的结构体（命名格式为XXX_MsgDataType）、接口的通信方式和消息推送的模式配置（命名格式为XXX_MsgServiceInterface）。配置这两部分需要新建一个arxml
文件，在里面的Functionalsoftware包下面新建两个包，消息的结构体的部分利用ImplementationDataType以及ImplementationDataTypeElement进行具体的配置，接口的通信方式和消息推送的模式用ServiceInterface和DdsServiceInterfaceDeployment进行具体的配置。
配置functional_software则包含三部分：Transform_application.arxml（应用配置文件，命名格式为XXX_application.arxml）、Transform_excution.arxml（执行进程文件，命名格式为XXX_excution.arxml）和Transform_service.arxml（软件服务示例配置文件，命名格式为XXX_service.arxml），不同的是这部分的配置是三个arxml文件，而interface是一个arxml文件里面的两个包。