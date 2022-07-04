- DreamView用法介绍
- [社群分享内容 | Apollo相对地图：基于人工驾驶路径的实时地图生成 (qq.com)](https://mp.weixin.qq.com/s?__biz=MzI1NjkxOTMyNQ==&mid=2247485175&idx=1&sn=1e3eb84464f916f114ef8ae324b035d3&chksm=ea1e1485dd699d93035a477151e11cd22980c42a06a9e244e10d13624d656e6623e60ceea1b2&mpshare=1&scene=1&srcid=05204RNUMC8uXQWlzQ55wlPO&sharer_sharetime=1621489830695&sharer_shareid=4364372046978bb504a21f48514f0710&exportkey=Ab%2BNw8WnidEMKOVBqVoyRSE%3D&pass_ticket=MvNCBmZE0OzTeSyRYMp6ii82l9F84qNkNyT%2FnfPYh19Wk0y0%2B%2FDGhIQTlcsiZUYG&wx_header=0#rd)

**Apollo相对地图**

**基于人工驾驶路径的实时地图生成**

百度资深软件工程师 **Yifei Jiang** 

相对地图是在Apollo 2.5的时候第一次对外开放。在3.0的时候我们和长沙智能驾驶研究院一起合作研发，对相对地图进行了功能和架构上的升级。今天我会给大家介绍什么是相对地图，为什么我们要做相对地图，以及相对地图是如何设计和实现的。

**![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIx0s4XEegHs7OWmBnicpJdlVF54FoVELxibxubAh1OY23tgzkgf5sSOCmA/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)**

本次分享将会从以下四个方面展开：

**一、相对地图的功能及特点**

**二、指引者的设计思想及在相对地图中的作用**

**三、相对地图在自动驾驶中的使用**

**四、相对地图和指引者的设计细节及特点总结**

## 1 **相对地图的功能及特点** 

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxdicibSwlIuYDsBP4qthuWOuUukFqk8zD956VRoq6lHPUh1tIcKyoEhbA/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1) 

这是Apollo一个简单框架图，在这张图的底部是**规划和控制**模块，负责车辆行驶轨迹的规划和执行。这个模块依赖两个重要的上游模块**高清地图和感知**。

**感知模块**主要提供了动态障碍物的信息，比如车辆，行人及其他障碍物。由于感知模块加入了视觉感知的能力，该模块也能够提供部分实时地图信息，比如车辆的当前车道线，斑马线，和红绿灯停止线。这类信息通过实时计算得到，成本低，但是依赖于路面状况及标识清晰度。

和实时地图相对应的是**高清地图模块**提供的静态地图信息。相比实时地图信息，静态地图信息是在线下提前收集和处理，这个信息由于是在线下制作，准确度高，信息种类更加丰富细致，而且不依赖于路面状况，但是制作周期长，成本高。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxVYS45pM5ict7D3xpDG1F3EEvUdoA0n3zibYdBlmiaQOm5S2tEbicMIr3Bw/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

为了将有不同特点和格式的实时地图信息和静态地图信息更好的结合在一起，同时也为了给规划和控制模块提供统一的接口。我们设计和开发了**相对地图模块**。

相对地图有如下两个特点：

**首先，产生的地图数据是相对于车的车身坐标系，这也是为什么该模块被命名为相对地图；在车身坐标系下，车辆坐标永远在原点，车头方向永远为0。**

**其次，地图数据会根据车的位置和朝向的变化实时更新，更新频率是10Hz。**

相对地图采用车身坐标系，而不是全局坐标系，有两个原因：

**第一，感知模块的原始数据输出是基于车身坐标系。**采用车身坐标系，我们就不需要把感知的输出再转化成全局坐标系。这样减少了转化带来的误差，也减少了转化的计算量。

**第二，在相对地图的一些模式下，只能使用车身坐标系，比如在定位不存在或者不准确的时候。**为了使相对地图能够在所有模式下进行无缝实时切换，我们统一了坐标系到车身坐标系。同时规划和控制模块也能够无感的处理不同的输入和场景。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxwtKlnux8hNZd1emCZtLPwIUQibyCahsqrDibtz9LwEm5lW8HIiaHpM64Q/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

不管是基于实时地图信息还是静态地图信息，相对地图对规划和控制模块提供了接口统一的地图数据。**这些地图数据约束和限制着规划和控制模块产生的轨迹和速度。**比如，数据中的车道线限制规划路径要在左右车道线之间。再比如地图中的红绿灯停止线限制车辆在红灯时不能超过停止线。

这种约束性的地图信息，使得规划和控制模块能够进行严谨而且精确的数学建型，然后进行数学优化，得到最优的规划结果。但同时也带来了一系列的问题。

**第一，约束信息必须准确而且完整。**因为缺失任何一个约束条件都会导致错误的规划。这样的要求会增加地图的成本。

**第二，每一个约束条件需要标注各种交规相关的属性**，比如，一个车道线，可能是白虚线，也可能是黄实线。不同的交规属性会影响规划和控制的输出。另外每一种约束也可能会有它的物理属性，比如一个路的边界可能是一个马路牙子，也可能是一道铁栅栏，甚至是一个悬崖。这种物理属性，也会影响规划和控制的输出。这些标注会增加地图制作标注的复杂性。

**第三，现实世界中，不是每一个约束都明显的存在。**比如在路口里面或者老旧的路面，车道线并没有标示出来。因为约束的完整性要求，我们必须要填补这些缺失，而且在填补时，需要考虑规划和控制的特性。这样就增加了地图制作的难度，也不能和规划控制进行很好的解偶。

## 2 指引者的设计思想及在相对地图中的作用

因为约束性地图信息带来的上述问题，我们在设计相对地图的时候一直在考虑，除了对规划控制提供限制和约束，告诉规划控制不可以做什么，我们能不能告诉规划和控制模块可以做什么，对规划和控制模块提出引导——这就是我们提出的**引导性的地图信息**。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxgeulGnr8QD1OWgGSCKJv2llI64plKQ8uOuPiaD4ibGTFlFqGhgILIL7A/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)



如上图所示的一个形象的比喻，约束性地图信息是就像在地面上摆交通锥，引导性的地图信息就像为规划和控制模块建一个虚拟的铁轨。

引导性信息有如下优点：

**第一，简化信息量，降低信息成本。**因为能做的什么可能性就只有一个或者几个，远远小于不能做什么的信息量。

**第二，对约束的依赖很低，不需要收集和标注约束信息。**因为引导的信息，已经考虑到了各种限制的影响。

**第三，限制性的地图信息，只对安全进行了限制，舒适性是由规划和控制在安全的基础上进行优化。**但是引导信息不仅有安全性上的引导，而且有舒适性的引导。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxSNWJaMFHzJRk8PtnMAx07bez6Gr9aFJECaZ3icMNmsquNldqQYgMLrg/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

具体到Apollo，引导性地图信息就是我们引入了的**指引线**概念。指引线是基于线下收集的人工驾驶路径生成的虚拟“轨道”， 如上图的绿色线段所示。

指引线有如下优点：

**首先，因为路径是由人工驾驶产生的，兼顾了安全和舒适性。**

**其次，指引线的成本低。**从人工驾驶路径到指引线的生成，是一个全自动的过程，不需要任何的人工标注。

**另外，整个指引线生成过程，是线下完成的。**我们在同一路段，可以结合不同的人，不同场景下收集多段路径，使得最终的指引线更加优化，减少偏差。

**最后，我们可以为不同的车型，产生不同的指引线。**比如说，大卡车和小轿车，尤其在拐弯的时候，路径有很大的区别。这种区别就可以体现在不同的指引线上，同时降低我们对车辆模型的依赖。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxE0MOO46dvia6NYNpZADICM0LDm6CMRxSNAS8UtFkdU6Gfe4hvP5VgFQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

为了在Apollo系统中引入指引线，我们加入了一个新的模块，叫**指引者**。指引者连接者高清地图和相对地图，为相对地图提供引导信息。 它即可以单独使用也可以和高清地图配合使用。这样相对地图为规划和控制不仅可以提供约束，也提供了引导。

根据约束和引导的重要性的不同，相对地图提供了两大模式： **模式1和模式2**。

**模式1是以约束为主**：在这种模式下，相对地图接入了指引线和高清地图数据，为规划和控制提供了引导和完整的约束。 这样的模式适用于复杂驾驶场景，比如城市道路。

**模式2是以引导为主**：这种模式下，相对地图不依赖高清地图，只接入实时地图信息和指引线，为规划和控制提供了引导和基本约束。这种模式适用简单驾驶场景。比如高速或者乡村道路。由于不依赖高清地图，这种模式下，开发者可以快速和低成本的部署Apollo进行真实道路测试和运营。

## 3 相对地图在自动驾驶中的使用

下面，我们用一个例子来说明**相对地图是如何应用到实际的路测中**。在这个例子中我们将聚焦在模式2，即以引导为主，约束信息是来自感知的实时地图信息。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxcDkRibu4NzSVqUrgdJZicd7ZTtewDHXWeh0dKYh7Nqn2Jj3n23X8JQ2g/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

上图是我们在地图上截取的一个路段。我们假设要在该路段部署自动驾驶。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxUWDNeE6crpL2kfx3l3wCiapLo5OaNO6cp9Qm1IcBVLBp9Xriaa9Ty8ow/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

在部署之前，我们首先要做的是收集人工驾驶轨迹。如上图所示，红色的轨迹是我们收集的轨迹数据，这个数据覆盖了几乎所有的车道及行车可能。

在实际操作中，可只收集用于无人驾驶的车道。这个收集过程可以由专业的司机专门来收集，驾驶过程中尽量避免换道，一个车道行驶一遍即可。

此外，这个过程也可以由普通用户众包完成，这种情况下，由于数据收集中可能存在噪音以及不可控的换道，每个车道需要收集多次行驶路径。另外众包的车型也要保证相似。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIx28YCALPVt15Dw95W3bfuiabxL0OTuC6J1hcmib7cIia7M9WWVjdhO2q3w/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

人工驾驶轨迹收集完成后会被自动转化成指引线。这个过程是一个全自动的过程，无需任何人工标注。有噪音的数据，以及过于频繁换道的数据在这个过程中也会被滤掉。

如上图所示，最终生成的指引线（绿色）以车道为单位，也包含了车道之间的连接，比如掉头和左右拐弯。以上就是我们部署自动驾驶前的准备工作。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxibAov7aqCzo1FHg1SyTLp2x3fz7IBrFDHCBoTicSdDX4e5rws9IDx9yQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

有了指引线之后，我们就可以在该区域开始自动驾驶了。我们假设，车在A点，想要自动驾驶到B点。我们首先向普通导航地图，比如百度地图，请求道路级别的routing。

如上图所示，道路中间的蓝色线段，就是我们得到的从A点到B点的道路级别routing。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxMtKSnYiaaFibRicE2h53oZdYicIic0tMrKW8BnkUxLBoMkyP4WazNiaIA0aw/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)



然后我们拿道路级别的routing去和之前生成的指引线进行匹配，得到A点到B点之间的指引线。

如上图所示，我们拿到了三条橘黄色的指引线。给出三条指引线是为了给下游的规划和控制模块更多的灵活性。

比如，当A点和B点之间有障碍物时，无人车可以切换到相邻车道绕过障碍物，再切换到和B点相连的车道。为了使规划和控制模块更好的执行换道的策略，我们为每一条指引线设置了不同的优先级，横向越接近终点的指引线优先级越高。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxtt8hAS7CF7T9rhppxhoNxd4A7v5Lma3fB3ChOFdBTyJGgPY04ndzdQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

无人车在进行无人驾驶的时候，相对地图模块根据指引线和实时车道线实时生成地图，并以10Hz的频率进行更新。

在这个过程中，指引线被转化为车身坐标系，并作为车道中心线；从感知模块的到的车道线作为车道的左右边界；以上信息被融合在一起产生相对地图。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxlEHNzBsa72yqgkQTOFOf2Tiawic4Q1S28BCXOfmGArfPGiby2DNpiboCZA/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

在无人驾驶过程中，**相对地图会有两种特殊情况**。

**第一种情况是由于定位不准，指引线暂时不可用。**在这种情况下，相对地图只根据感知的实时车道线来生成地图信息，无人车可以继续进行L3级别的无人驾驶，属于车道保持或者自适应续航控制状态。

这种情况适用于短暂的定位不准确，比如过隧道或者桥洞，一旦定位恢复，相对地图会自动切换回正常的指引线+实时车道线模式。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxaiaa0V7GL7GDa2rhAnU7lkMajYCMNSGOiacsKPyPEBuP4CT1WAUicnz0w/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

**第二种特殊情况是感知无法检测到车道线，**如上图所示，车辆行驶在路口（或者老旧路面），没有车道线。这时相对地图的生成只根据指引线，车道边界会根据历史数据进行预测，或者根据左右侧指引线进行估算。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIx8r6b00mA14pTwHCP7xleonM9YmgUOEB0tzhJnibKIK7cxRUJNujXDbA/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

从上面的例子，大家可以看出指引线在自动驾驶中起着多种不同的作用：

**首先，指引线连提供了车道级别的导航，它接着出发点和目的地，保证无人车能够达到终点。**

**第二，指引线会被规划模块用作路径规划的参考线，为路径规划提供安全性和舒适性的引导。**

**第三，指引线是高清地图的载体。在进行自动驾驶时，我们无需加载整张高清地图，而是沿着指引线，加载指引线周边的高清地图数据。**

**最后，指引线还是生成相对地图的重要数据之一。**

## 4 相对地图和指引者的设计细节及特点总结

下面，我们来看一下生成指引线的模块，指引者，是如何设计和实现的。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxZbITqOABekQHNdfJ7fgN2nmXGpR8pj1aq1bcatLdNicSoaN32ufRicTQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

指引者是一个云端服务，如上图的中间部分所示，指引者有两个数据库，一个存放**指引线数据**，另外一个存放**高清地图数据**。除此之外，还有一系列的数据处理及算法功能模块。为了使指引者能够提供指引线，我们首先要把线下采集的人工驾驶路径数据上传到云端（如图中**步骤1**所示）。

指引者会对数据进行一系列的处理，最终生成指引线并存储在数据库中。之后，用户会在Dreamview中选择目的地发出导航请求（如图所示**步骤2**）。

指引者中的导航请求处理器会接受导航请求并转发给百度地图API（图中**步骤3**），并从百度地图API得到道路级别的导航（步骤4）。

导航处理器会根据道路级别的导航进行指引线匹配（**步骤5**）得到指引线。

如果有相应的高清地图数据存在，指引线会和周边的高清地图进行关联（**步骤6**）。

最终，指引线会传回给Dreamview（**步骤7**）。

Dreamview得到指引线数据后发送给下游模块，比如预测模块和规划模块。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxTu4U91FjpCNz6xv4CQpolQzy2FrS3y6vt9g7s9lwBfF09Bjy6fic43Q/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

除了云端服务，我们也提供了一个离线指引者的工具，供开发者在没有网络的情况下，或者在私人封闭测试场中使用指引线和相对地图。这个工具可以把收集到的人工驾驶数据进行一系列的处理包括：**路径抽取，路径平滑，和指引线产生**。

最终把生成的指引线数据发送到 ROS topic “/apollo/navigation”。 这样相对地图就可以收到指引线，并产生相对地图。 关于离线指引者的具体使用发法，大家可以参照我们Github上的文档，链接列在了上图中。

这个文档是我们的外部开发者根据他们的实践经验总结的，写的非常详细。

在上面的使用相对地图的例子中，我们多次提到**百度地图API**。下面为大家总结一下，相对地图是如何利用和集成百度地图API，以及这样的设计带来的好处。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxBozsjpBJ1r35ibYKxkib2fnqVlLNEcdeGwCbIT9xrROgS5k0CfoRvziaw/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

**首先，我们利用百度地图为用户提供发送导航请求的界面。**如上图所示，用户可以在地图界面中选择一个目的地，然后点击左上角的红色Route按钮。这样的一个体验就像用户在手机上使用百度地图导航一样。

**其次，在无人驾驶过程中，我们会把无人车的位置实时显示在地图上，为乘客提供当前位置的信息。**如上图所示，百度地图中间的红色位置图标就是无人车的当前位置。

**最后，也是最重要的，我们利用百度地图的导航API为指引者提供了道路级别的导航。**这样的道路级别导航保证了行车线路的准确性，同时也考虑了实时路况，为最终匹配到精确的合理的指引线提供了保障。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxnSlYbR2PoIITupNK45k31073OhOg84B9AHC3sl55GYjTrq3YxCG6icQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

上面这张图总结**相对图和指引者和Apollo中各个模块之间的关系。**这张图的中间是相对地图模块，它依赖于感知，定位和指引线。指引线可以由云端指引线产生，也可以由离线的指引者工具产生。相对地图会融合各种地图信息，根据路况条件和输入数据，工作在不同的模式下。生成的相对地图数据，会传给预测和规划模块。**云端指引线连接着高清地图和百度地图API。**

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/C4wVziccAsSLeWojha3UibicJ9lNFnQhVIxKK0DJJnroI8EjFcARtSH4HAZzG87IV0zlbp8Nn4yibQBcUkJzlJQ3cg/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

最后，总结一下相对地图的三种工作模式：

图中的模式1和模式2就是之前提到的**指引线+高清地图方案**和**指引线+基于感知的实时地图信息方案**。模式0是模式2的一种特例，只依赖于基于感知的实时地图信息。

首先对于适用场景，模式0由于没有指引线，适用于短时间的车道保持和巡航。模式1由于有高清地图信息，适用于复杂的驾驶场景，比如城市道路。模式2由于只有一些基本的实时地图信息，适用于简单驾驶场景，比如高速或者乡村道路。

对于自动驾驶级别，模式0属于L3级别，模式1属于L4级别。模式2由于有限的地图信息，属于前两种模式之间。

关于部署成本，由于模式0只依赖于感知算法，没有线下数据收集的需求，部署成本几乎为零。模式1由于依赖高清地图，部署成本高。模式2虽然依赖指引线，但指引线的制作成本低，总体部署成本也偏低。

**关于对定位的依赖：**由于模式0只使用感知数据，可以完全不依赖定位。模式1和模式2使用指引线或者高清地图，所以对定位有强依赖。

最后，关于对道路标示的要求，即**路面交通线的清晰程度**：由于模式0是基于视觉对车道线进行识别，对标示清晰度要求高。其他两种模式由于有了指引线或者高清地图作为参考，对道路标示要求低。

以上就是对于相对地图的介绍和分享。非常感谢大家的参加！更多Apollo相关的技术干货也可以继续关注后续的社群分享。

相关学习资料和自动驾驶相关技术内容，大家可以关注【**Apollo开发者社区**】的微信公众号来获取，也可以在Apollo GitHub上提出技术问题与我们互动，欢迎大家沟通交流！

## **Q&A 问答**

1 Q：相对地图如何扩展成多车道，目前没有车道线检测是否也可以实时生成相对地图，百度地图的routing API怎么和相对地图结合的？

A：需要采集多条指引线，每一个车道一条。没有车道线检测，也可以只依赖指引线生成相对地图。在模拟器里的百度地图的界面上选择终点，点击route就可以。前提是云端要有相应路段的指引线。

2 Q：Apollo有一个分支叫mobileeye_radar，为什么已经有十个月没有更新了？请问是遇到了什么问题呢？

A：这个分支已经合入了master，所以没有更新。这个分支里的主要更新可参照

https://github.com/ApolloAuto/apollo/tree/mobileye_radar/modules/third_party_perception

3 Q：Flocalization模块中用于lidar定位的地图需要rtk提供的高精pose 和对应的lidar数据来生成，鉴于rtk成本较高，请问这里能否用其他方式来提供足够精度的pose？

A：我们也在探索和开发基于摄像头/图像的定位方法，不依赖RTK。

4 Q：使用相对地图的规划和使用高精度地图的规划有什么区别？使用相对地图时如何实现变道行驶？

A：规划是通用的，没有区别。相对地图支持多车道，可以在规划和控制模块中实现变道行驶。

5 Q：百度API提供的导航地图、车道线、实时感知的三维场景，这三者的坐标都是经过坐标偏转加密的吗？

A：百度API的数据是经过坐标偏转加密的。车道线和实时感知的数据是基于车身坐标系的。我们在系统中已经做了对齐。对开发者来说是无感的。