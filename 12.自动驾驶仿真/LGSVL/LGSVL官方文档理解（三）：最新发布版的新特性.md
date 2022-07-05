- [LGSVL官方文档理解（三）：最新发布版的新特性 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/369844938)

LG在今年的1月份（2021.01）发布了最新版的仿真器，带来了一些极具价值的新特性。最近这段时间虽然很少用仿真器，但也在一直关注着。今天终于有时间来简单的写一些新版本带来的一些新特性，以及我之前使用的过程中遇到的一些比较重要的关注点。

为了保持一致性，仍然是属于LGSVL官方文档理解的第三篇内容，毕竟这些新特性也被写到了官方的文档里面。我仍假设你已经阅读过我之前写的文章，或你对该SVL仿真器有一定的了解，对于一些很基础的内容我不再详细描述。另外， 想要很清楚的了解掌握仿真器的新特性，**建议还是自己手动实战一下，从官网下载打包好的文件，直接就可以执行打开仿真器**，因为了解和使用这些新特性并不需要我们在开发者模式下。

## 一、[全新的在线Web用户界面](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/docs/user-interface/web/store/)

这是仿真器版本更新的一大亮点。

之前我们使用仿真器，运行.exe或从开发者模式下启动仿真器，然后在仿真器界面点击“打开浏览器”按钮就会打开一个网站，我们都知道这个网站是一个本地地址，只能在仿真器打开的情况下使用，最后我们会在网站里下载车辆模型、地图等、加载仿真、运行仿真。这是之前版本的一个整体的仿真流程。最新版更新了仿真器界面和打开后的Web用户界面。

新版本主要的特性有：

- 需要创建一个LGSVL账号，使用账号来登录仿真器和Web用户界面。
- 所有的地图和车辆等资产可以直接从本地或商店加载到自己的云端账号中，然后在账号连接到的任意一个仿真集群中使用。
- 商店中有官方提供的公有资产，同时，所有的账户之间可相互分享资产，包括公共分享和私有分享。这样看来似乎仿真器在资产使用方面要有收费的倾向了~~~

下面做一些详细的介绍。

下图是最新版的仿真器打开之后的界面，是不是也和之前的不一样了，似乎比之前的更好看一点。点击界面右上角的小地球图标，我们可以选择：**在线仿真、离线仿真、断开与账户的连接、清除缓存和退出仿真器**。

![img](https://pic3.zhimg.com/80/v2-b40688a5bfd33f27ecfadce565ceedb2_720w.jpg)



这里简单提一下

- 现在我们看到的界面是一个离线仿真的状态，我们可以在下拉框中选择本地已经有的仿真测试用例，然后运行他们。一般我们第一次使用是没有本地仿真用例的，只有通过在线仿真（会自动下载所需的车辆、地图等）之后才会在本地缓存一些测试用例。
- 一般我们都会使用在线仿真。点击“Go Online”之后界面变成如下样子，我们点击“LINK TO CLOUD”之后就会打开网站，第一次是需要我们登录账号的，如果没有就先注册账号。

![img](https://pic4.zhimg.com/80/v2-5012073e260b4ede6c40700f9466d597_720w.jpg)

下面我已经注册过账号了，会出现如下界面，这个就是全新的Web用户界面。我们在“New Cluster”中输入一个名字来代表当前的仿真器。

![img](https://pic1.zhimg.com/80/v2-0a85c8c657145e82f130c9b5ef187fd8_720w.jpg)

![img](https://pic2.zhimg.com/80/v2-bfa93b1873e52d8083ba99e54aeadb65_720w.jpg)

- 现在我们就可以在该用户界面中随意的浏览了，我们看侧边栏就能够看到有商店（没有买卖就没有...），有我自己的云端库，有仿真集群，有仿真和测试报告（这个后面会说，也是新版本带来的新特性）。**我们可以把商店中的资产加入到自己的云端库中，然后配置一个仿真场景，启动仿真，并在仿真结束后查看仿真报告。**我相信这些操作对于你之前使用老版本的仿真操作是很类似的，你应当能够自己启动一次仿真。

![img](https://pic1.zhimg.com/80/v2-e0f2d49dd8ed743c929f2fd1579c78dc_720w.jpg)

- 在这里只提两个仿真比较重要的地方，在我们配置一个仿真测试的时候，要从“Select Cluster”中选择我们之前加入的仿真集群。自由的开启下面三个选项，分别是：为本次仿真创建仿真测试报告，使用无界面模式仿真、使用交换模式仿真。

![img](https://pic1.zhimg.com/80/v2-104efe6c2f306e0a8def26dc212c5954_720w.jpg)

- 另一个比较重要的地方是我们要选择一个[“Runtime Template”运行模板](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/docs/running-simulations/runtime-templates/)，现在一共包括四个：随机交通模板、可视化场景编辑模板、PythonAPI模板、API模板。一般我们第一次使用先选择第一个随机交通模板“Random Traffic”。

![img](https://pic3.zhimg.com/80/v2-b7e8856905e6463a6f56adfe61070802_720w.jpg)

## [二、可视场景编辑器](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/docs/creating-scenarios/visual-scenario-editor/)

这是新版本中的又一个新特性，虽然现在提供的编辑功能较为简单，但已经很不错了。

新版本主要的特性有：

- 支持为仿真器中的车辆和行人添加轨迹点
- 以GUI编辑器的方式放置自身车辆、交通参与者和物体等，同时能够设置他们的行为与行为触发方式
- 编辑结束之后可以预览、保存编辑后的场景 ，然后在仿真器中加载我们编辑后的场景。

下面简单说明一下

看到这个仿真器界面了吗，第一个按钮是“打开我们的Web用户界面”，也就是上面说的第一个新特性。那么第二个按钮就是“打开场景编辑器”。

![img](https://pic2.zhimg.com/80/v2-8909abbff0e21ba2d43ad75336db2ca9_720w.jpg)

下图是场景编辑器打开之后的界面，我在打开之后加载了CubeTown地图，并自定义了一辆车与一个行人的起始位置与它以后要到达的轨迹点。

![img](https://pic1.zhimg.com/80/v2-a8972bedd4529ff716874cc2ab475530_720w.jpg)

![img](https://pic1.zhimg.com/80/v2-bf4003a13e705762ef46171b6d48fe1c_720w.jpg)

这里简单对场景编辑器的一些功能说明一下：

- 我们可以加载任何一份地图，然后在地图上添加自己要控制的车辆、其他的交通车辆、以及行人。
- 我们可以为交通参与者制定行为方式，一般使用如上图所示的车辆要到达的轨迹点，对于每一个轨迹点，我们可以制定到达该轨迹点的速度和该轨迹点的触发方式。
- 最后我们可以在预览侧栏，预览我们添加的车辆或行人的运动，保存编辑后的场景，并在仿真器中使用。注意：**（1）在使用时要在“Runtime Template”运行模板中选择“可视场景编辑”，然后选择我们保存的JSON场景文件即可。（2）使用Visual Scenario Runtime运行模板的端到端的仿真只支持Linux系统。**

该场景编辑器中提供的功能比较简单，各位可以自己亲手使用一下，感受一下，看是否能够被你所利用。

## 三、生成仿真测试报告

这个就是前面提到的仿真测试报告，也是一个很不错的新特性。

- 使用运行模板的仿真可以选择记录运行报告，报告中的内容包括：违反停车标志、违反限速、紧急刹车、车辆碰撞、闯红灯等。
- 同时有仿真录像可以回放。

![img](https://pic1.zhimg.com/80/v2-3ccabbc00fc3280b8e6778277e0b9e4c_720w.jpg)

下面是一些比较小的特性

## [四、路网的生成](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/docs/simulation-content/hd-map-mesh-generation/)

这个主要是针对开发者模式，可以导入OpenDRIVE等格式的高精地图来生成自定的地图环境。

## 五、传感器插件的提升

- 所有的传感器在仿真器中以传感器插件的形式可用。
- **给特定类型的传感器采集的数据通过后处理增加了噪声。**
- 激光雷达传感器的性能得到大幅度改善。
- 增加了新的传感器数据：激光雷达-点云数据、控制校准标准、视频记录传感器、舒适度传感器

## [六、增加了Apollo预测和规划模块的独立测试](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/docs/tutorials/modular-testing/)





最后，总结来说，新版本带来的新特性在用户交互界面和地图编辑、制作新地图方面有了很大的提升，从仿真器的源码编译、使用方式上来说并没有很大的区别。你仍然可以很好的使用新版本的仿真器。

------

最后，写一下我使用中的一些较为重要、需要关注的点：

- 在官网可以请求仿真器的使用Demo，申请就好。
- SVL仿真器现在已经支持64位的Windows系统和Linux系统，为了最佳的性能，建议使用Windows系统。
- 仿真器所有功能的开发者模式（Unity编辑器）只支持Windows系统。
- 使用PythonAPI或Visual Scenario Runtime运行模板的端到端的仿真只支持Linux系统。
- 最低实时仿真帧率要在15fps或传感器的期望帧率，从而避免相机或激光雷达等关键传感器丢帧。
- 端到端的仿真最好使用ROS桥接，而不是PythonAPI， 这样可以做到实时仿真。下面是我从Github Issue上找到的官方对PythonAPI的解释。

![img](https://pic1.zhimg.com/80/v2-40ad2c9c7ceab72750c5d400fa283468_720w.jpg)

- 如果你要做视觉感知，可以利用传感器收集的图像、点云数据，然后用自己的算法来处理。如果你要做决策和规划，那么也有相应的ROS消息包能够提供地面真相，而无需关注感知预测等。
- 如果你想在该仿真器上使用深度学习，请查看这里，一句话，主要是讲利用汽车前置三个（左中右）摄像头采集到的图片信息来学习车道保持：

[Deep learning lane following modelwww.svlsimulator.com/docs/tutorials/lane-following/![img](https://pic3.zhimg.com/v2-c8e1772d1a832de302ba88cc5a92b84a_180x120.jpg)](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/docs/tutorials/lane-following/)

- 如果你想在该仿真器上使用强化学习，请查看这里，主要是结合gym做强化学习：

[OpenAI Gym - SVL Simulatorwww.svlsimulator.com/docs/third-party-integration/openai-gym/](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/docs/third-party-integration/openai-gym/)

- 其实我一直比较关系的是怎样查询到车辆在高清地图中的一些信息，比如车道信息、道路信息等等。但是似乎该仿真器一直都没有实现该功能，另外从本质上来说，这些功能的查询应该是属于高清地图解析模块的，是不依赖于任何仿真器的。**所以我自己也写了OpenDRIVE地图解释工具**，但还不是很完善，同时又因为自己的一些别的事情暂时搁置了。

[OpenDRIVE格式地图数据——极简概述（三）11 赞同 · 4 评论文章](https://zhuanlan.zhihu.com/p/361246912)

但是，我在最新版的文档中看到有[车道线传感器](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/docs/simulation-content/lane-line-sensor/)，**但是只能在Cyber桥接下使用，并且数据格式是和Apollo5.0相兼容的。**大概是能够提供车辆前方一段距离的车道中心线的三次多项式形式的建模，似乎也么有提供车道ID啥的，感兴趣的小伙伴可以自行查看。

最后，能想到什么再写吧，这次就先写到这里~~~

------

最后，这次真的最后了，关于这个仿真器能做什么，下面是官方给出的解释：

[Introduction - SVL Simulatorwww.svlsimulator.com/docs/getting-started/introduction/](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/docs/getting-started/introduction/)

同时，你也可以去哔哩哔哩、Youtube上观看官方发布的一些教程或仿真视频，因为这个仿真器的教程真的不多......