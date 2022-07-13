- [详解自动驾驶仿真数据集 SHIFT：A Synthetic Driving Dataset for Continuous Multi-Task Domain Adaptation_自动驾驶小学生的博客-CSDN博客](https://blog.csdn.net/cg129054036/article/details/125581858)

本文介绍一个新的[自动驾驶](https://so.csdn.net/so/search?q=自动驾驶&spm=1001.2101.3001.7020)仿真数据集：`SHIFT`，论文收录于 ***CVPR2022***。`适应连续变化的环境`是自动驾驶系统一直以来要面临的挑战。然而，目前现有的图像和视频驾驶[数据集](https://so.csdn.net/so/search?q=数据集&spm=1001.2101.3001.7020)`无法反应真实世界的变化本质`。

因此论文作者介绍了一个`最大的自动驾驶多任务合成数据集SHIFT`。在`云量、雨量`和`雾的密度`、`光照时间`、`车辆和行人密度`等方面呈现离散和连续的变化。`SHIFT` 数据集包含综合的传感器套件和多种主流感知任务的注释，可以在域变化上`研究感知系统性能的退化`，促进`连续域适应策略`的研究。**虽然是仿真数据集，但是在其上观察到的感知算法域变化趋势与真实数据集基本相符，未来将会有助于合理收集真实数据集和开发感知算法**。

数据集主页：https://www.vis.xyz/shift/

------

### The SHIFT Dataset

论文引言和相关工作就不介绍了，这里直接介绍 `SHIFT` 数据集。表1是现有公开的自动驾驶数据集的比较，可以看到 `SHIFT` 是最大的合成数据集，也是唯一一个提供`连续域变化`的数据集。`SHIFT` 总共包含4850个序列，最长的序列有33分钟，**250万帧注释**，注释包括物体2D/3D框、语义/实例分割、2D/3D多目标追踪ID、深度图、光流图等。

![在这里插入图片描述](https://img-blog.csdnimg.cn/3e148a83b5324cb781a293580ae44b5e.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/9c4b8324e24446859bbcfed0131e3351.png)
`SHIFT` 数据集传感器布置如下图所示，包含5个RGB摄像头、一对双目摄像头、一个深度摄像头、一个光流传感器和GNSS/IMU以及128线激光雷达，所有传感器进行了时间同步和空间同步，采样频率为10Hz。
![在这里插入图片描述](https://img-blog.csdnimg.cn/e18ac9a41f4c4acb872f8c68d2dc628b.png#pic_center,)
`SHIFT` 数据集的一大特色就是提供了不同条件下的数据，一共包含24种域。除了离散域，还包含连续域，如下图所示，从白天到夜晚的的连续数据，以及从晴天到雨天的连续数据。

![在这里插入图片描述](https://img-blog.csdnimg.cn/c63036bfe1cc4dffa5a232157af251be.png#pic_center,)

------

### Experiments

`SHIFT` 数据集主要用于研究感知算法在域变化时的性能。作者这里测试了四种域适应测略：`Targeted DA、Untargeted DA、Incremental DA、Continusous TTA`。

![在这里插入图片描述](https://img-blog.csdnimg.cn/7eb46b8c0a2f4260a5fdd6e35d6aba7d.png#pic_center,)
每一个模型都是在 `clear-daytime domain` 下训练的，然后在其它域下测试。可以看到仿真数据集和真实数据集上模型都有着相同的变化趋势。
![在这里插入图片描述](https://img-blog.csdnimg.cn/8696f09dd1a444449362fbd6f98df71d.png)
在不确定性估计中，在 BDD100K上证实了SHIFT上观察到的校准的整体退化，仿真数据与真实数据的结论基本一致。

![在这里插入图片描述](https://img-blog.csdnimg.cn/50062bd2ed9544c8b6cf2d05531071ae.png#pic_center,)
在多任务学习上，可以看到适当的任务组合能够有效抵消域变化的影响，提高雨天、雾天、夜晚的模型性能。

![在这里插入图片描述](https://img-blog.csdnimg.cn/00eeca5b9c06412da8af016d073d2135.png)