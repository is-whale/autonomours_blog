- [基于单目和低成本GPS的车道定位方法 (qq.com)](https://mp.weixin.qq.com/s/8Gf2ViIZOVPzVZK11uZWUg)

# 摘要

对于自动驾驶系统来说精确的车道定位是执行复杂驾驶的必要条件，传统的基于GNSS的方法通常不够精确，无法进行车道级定位以支持无人驾驶的功能，虽然基于激光雷达的定位可以提供精确的定位位姿，然而，激光雷达价格仍然是这种解决方案成为广泛应用的一大问题，因此，在这项工作中，我们提出了一种低成本的车道级定位解决方案，使用基于视觉的系统和低成本GPS实现高精度的车道级定位，实验表明，所提出的方法实现了良好的车道级定位精度，优于仅基于GPS的解决方案。

# 主要贡献

在这项工作中，作者提出了一种低成本的车道级定位解决方案，使用基于视觉的系统和低成本GPS实现高精度的车道级定位。本文的主要贡献是：

- 提出了一种基于视觉的低成本定位系统；
- 提出结合地图匹配方法和低成本GPS，实现高精度车道级定位；
- 在实时和真实环境中进行广泛的实验。

一般来说，基于高精地图的定位方案主要步骤：创建参考地图，找到相应的路段，并在地图上进行定位。

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81oQgAQYKhrRMYdX9lnsibpLnkWCEQ0K0HwuqlRXyurvicrKCgG5DJRPAA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**图1.给出了所提出方法的框架**

# 主要内容

在这项工作中，作者提出了一种使用地图匹配技术获得高精度的车道级定位的方法。图1给出了所提出方法的框架，提出的方法有三个输入：相机、GPS和参考地图，构成轨迹的GPS点如下所示：

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81OPgd9SMgGSyPeTQ7ficics0K7mWMSbVr2HZEs8OA1YaDiaYZiaeoSAd2Mg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

其中px和py分别表示经度和纬度坐标，w是当前GPS时间戳,参考地图位置r可以定义如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81Ra0gc3mgVveXK4E3XCkcZf2BET7wVWjJ4hl99SfmiabLLwXLBOMI9Ww/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

其中rx和ry分别表示经度和纬度坐标，j是参考点的位置编号,GPS提供车辆当前位置，并创建25mx25m区域的本地搜索地图，从参考地图中选择本地地图的参考点，该参考地图的点落在当前GPS位置当前所在的区域内，因此，其时间戳rw可以定义如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81qicwbHmahZyEnoZPiaV0gqQQia23ia7TZWz081DibIibzFDAAQ6B0YnDIWgg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

其中k是参考点在局部地图中的位置编号，使用简单的最近点算法计算并比较这些点与当前本地GPS位置之间的距离，然后使用滑动窗口技术，如图2所示

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81Yntn4puYicWEjI5jwH5VhmicqarD3upb2B9wM6iaKgCOTqjKQvKxVasGw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**图2.使用滑动窗口的地图匹配过程的图示**

搜索从当前GPS获取的位置与参考地图中车辆通过的位置之间的最近临近点（CP），使用欧几里得距离计算距离，最小距离则被选择为车辆的最合适位置，对应窗口的最近点由以下关系确定：

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81V7E6x4WobfntF8WOicNLLW9yFXl7JxWib5NicfJicfN9V1N81NmicGoHiaicw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

同时，相机提供要由车道分割算法处理的图像序列，使用车道线分割，可以得到车辆相对于中间车道的位置。

**A.创建参考地图**

参考地图是利用谷歌专业创建的，现在可以免费使用，我们首先创建中心车道路线并将其保存为KML格式，然后，我们提取该路径的坐标并将其存储为中心车道的参考地图，图3显示了使用Google Earth Pro创建的参考地图。

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81iaZDvYOxIBYjb2ztrabze0pkBkBAYrpXDTIx2LFlxjb2eosjzhadjbg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**图3.显示了使用Google Earth Pro创建的参考地图**

**B.找到相应的路段**

道路是多条线的，通常左右道路边界由两条多线表示，分隔车道的道路标记也用多条线表示，多车道线特征的单车道称为路段。该方法的一个重要步骤是从输入图像定位相应的路段，对于这项工作，采用LaneNet来生成车道线分割，使用LaneNet的二值图像输出并执行后处理，LaneNet的结果是基于路段数量的多线形式，为了获得相应的车道，LaneNet的输出需要进一步处理，以找到正确的路段，即车辆当前通过的路段。为此，我们使用一种简单的技术，包括线的霍夫变换，以获得图像上的多条线，将图像分成左右两侧，然后选择左侧的一条线和右侧的一条，它们的底部位置最接近图像的底部中心，这两条线应该是相应车道的边界。图4显示了获取相应路段的过程示例。

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81Ww9baXecFG27xqvGJI8ddkhbj6GKpOC79Hp1jc0mldQZxrTg2c7wFA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**图4.找到车辆当前位置相应路段的过程**

**C.参考地图上的车辆定位**

在实现最近点地图匹配方法后，我们估计车辆的最终位置，即相对于中间车道的位置。图5清楚地说明了车辆中心和中间车道之间的关系

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81lcCicHDdkUIlyUmkvVjdIzU4nFpJkC9lU6KqOwkuyMZgNeKgQhRhKvA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**图5.说明如何获得车辆中心和中间车道之间的偏离距离**

车辆中心相对于中间车道的估计距离公式如下：

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81eFjwb03loQOs8RpYXeS6eDn74iaTremxdDfExxEhNXQW1UsZrSGPbtg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

式中，d_m是车辆中心相对于中间车道的估计距离，单位为米，Lanewith_m是车道宽度，单位为m，（x2− x1）p_x是以像素为单位的车道宽度，d_px是车辆中心相对于以像素为中心的车道的估计距离,使用车辆中心与中心车道之间的估计距离，进行车道级别定位，如图6所示：

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81nibdNomCuwOKQTc4ibgJiaDrpuZejbKAzLwiadvNibfGZ45Fl4UMBmOY1DQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**图6.基于地图匹配估计车辆位置的图示**

# 实验与结果

我们提出的方法在850米长的道路上进行了测试，该道路由两条车道组成，每条车道宽3.5米，使用安装在测试车顶部的低成本GPS接收器测量车辆的当前位置，在进行实验时，我们没有使用高精度GPS作为地面实况数据，这样，我们测量了从中间车道到车辆中心的距离偏差。通过假设车辆大部分时间应位于中间车道，测量了车辆相对于中间车道的位置，作为我们的性能指标。表1显示了GPS定位和使用我们提出的方法获得的估计位置之间的比较的初始结果，实验的定性结果如图7所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81WsicGMibrZTN2VYbHlYs09xdHRqv4iaYXj949HGbPlZwLA2ibhibACB1PHw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**图7.所提出方法的定性的定位结果**

根据表1，测量的GPS位置比估计位置具有更高的方差和更高的平均值，这表明GPS测量不够精确，无法定位车辆的精确位置，与实测GPS相比，我们提出的方法估计的位置具有较小的均值和方差值，这表明所提出的方法比唯一的GPS方法工作得更好。

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81SQVgJZKrCmSszqc2xENFKic4dhT56MTBg0BprmlJWop575PGuO38TrA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**表1.使用提出的方法获得的GPS定位和估计位置之间的性能比较**

这也表明，大多数情况下，车辆沿着中间车道行驶，比较结果也可参见图8，很明显，提出的方法比仅使用GPS更准确地进行车辆定位。

![图片](https://mmbiz.qpic.cn/mmbiz_png/9qdbew8iaZLiaHaMFQGAV3dBUZcv8sdc81PD0s66AwKRY1or08gnSFxxz6yQnNPZbBic3Hnicweo9tJjAPTZicD1Wiaw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

**图8.a）使用唯一的GPS系统和b）提出的方法的车辆中心和中间车道之间的偏差直方图**

# 总结

在本文中，作者提出了一种低成本定位系统的解决方案，使用基于视觉的系统结合地图匹配方法和低成本GPS实现高精度车道级定位，一般来说，需要三个主要步骤：创建参考地图，找到相应的路段，并在地图上定位车辆。提出的方法用于实时测量车辆相对于中间车道的位置，初步结果显示与测量的GPS定位方案相比性能更好。