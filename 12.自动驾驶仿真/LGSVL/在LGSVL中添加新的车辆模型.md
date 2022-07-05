- [AutoWare.auto学习笔记（二）——在LGSVL中添加新的车辆模型 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/432578708)

## **1.软件平台**

操作系统：Windows10

使用软件：Blender 2.93、Unity 2020.3.3f1、SVL Simulator 2021.3

## **2.软件安装中遇到的一些问题**

（1）Blender为开源软件，安装没有遇到任何问题。

（2）Unity版本尽量选择2020.3.3f1，该版本为LGSVL帮助文档中提到的版本，不确定使用更高版本Unity是否存在问题。我使用了Unity Hub进行安装。

（3）SVL Simulator选择源码编译的方式安装（否则无法使用SVL的开发者模式），注意git clone代码时要选择branch为2021.3的发行版，否则可能会产生兼容性问题。

备注：以上各软件版本可能会随着LGSVL帮助文档的更新而发生变化，本文档记录于2021.11.12。

## **3.添加新的车辆模型**

主要参考LGSVL帮助文档：

[Developer mode - SVL Simulatorwww.svlsimulator.com/docs/running-simulations/developer-mode/](https://link.zhihu.com/?target=https%3A//www.svlsimulator.com/docs/running-simulations/developer-mode/)

文档中嵌入的教学视频位于Youtube平台，有梯子的朋友可以直接看，没有的话可以到B站搜索LGSVL官方账号，一样可以观看该视频。结合视频有助于理解文档内容（对于我这种five而言...）。

### **3.1在Blender中绘制模型**

文档中提到的建模软件除了Blender还有Maya，Blender开源，Maya收费。个人感觉，只要是能导出.fbx文件的建模软件应该都可以。因为不会用Blender，我就随便画了一个很简陋的模型。

![img](https://pic2.zhimg.com/80/v2-1cb7ea581411ee08ff30adfa9955c359_720w.jpg)

在建模时，最大的一个问题是坐标系。Blender中使用的是右手坐标系，而Unity中使用的却是左手坐标系。

[Unity坐标系 左手坐标系_Peter_Gao_的博客-CSDN博客_unity坐标系是左手还是右手blog.csdn.net/qq_42672770/article/details/108212808![img](https://pic3.zhimg.com/v2-066c57c2499d5b71cedf53db3f497fea_180x120.jpg)](https://link.zhihu.com/?target=https%3A//blog.csdn.net/qq_42672770/article/details/108212808)

这就意味着两个软件之间存在一定的坐标转换，而这个问题可以通过Blender导出.fbx文件时正确设置前进和向上的方向来解决，参考下面这篇文章：

[解决Blender模型导入Unity方向错误的问题www.bilibili.com/read/cv12612704/](https://link.zhihu.com/?target=https%3A//www.bilibili.com/read/cv12612704/)

通过以上方式，解决了全局坐标系在导入Unity之后的正确性，但并没有解决局部坐标系的问题。例如，在用圆柱体建模当作车轮时，圆柱体的Z轴通常是默认垂直于两个底圆面的，也就是说车轮的局部坐标系和Blender的全局坐标系并不相同，从而导致将其导入Unity之后车轮的Z轴并不朝上。可以通过以下方式调整物体的局部坐标系：选中物体，将当前坐标系设置为全局坐标系，在选项中勾选“仅影响原点”，然后在“物体——变换”中点击“对齐到变换坐标系”，这时再切回局部坐标系就会发现已经和全局坐标系一致了，记得再取消刚才的勾选。

![img](https://pic4.zhimg.com/80/v2-3d91bfa436c6dad056532ab4be39f0d3_720w.jpg)

![img](https://pic4.zhimg.com/80/v2-ec6b6a1649841b7762d91c1ba51e8e43_720w.jpg)

**需要注意：建模时所有物体都应该位于X-Y平面之上，否则会导致X-Y平面之下的部分（假设碰撞尺寸与轮胎尺寸相同的话）位于道路下方，从而使仿真出错。**

**3.2在Unity中配置车辆模型**

这部分内容按照帮助文档进行操作即可。值得一提的是，如果图省事，建模时可以不画灯光部分，在Unity中配置时有关灯光的步骤也可以跳过，不会影响后续模型的编译。

**需要注意：为了避免仿真时出现穿模等现象，应该将车轮的碰撞尺寸调整至建模时的尺寸。**

### **3.3模型发布**

在上传编译完的车辆模型时，发生了上传失败。起初，我以为是网络问题，挂了梯子上传也是同样的报错。找到了如下问答：

[Asset Bundle Upload Error · Issue #1364 · lgsvl/simulatorgithub.com/lgsvl/simulator/issues/1364![img](https://pic4.zhimg.com/v2-6cf96029c82cc6c9afd8aba3aa5764b3_180x120.jpg)](https://link.zhihu.com/?target=https%3A//github.com/lgsvl/simulator/issues/1364)

![img](https://pic2.zhimg.com/80/v2-4551f4a23fe0e6df94ea292816a3e701_720w.jpg)

问题的产生可能与上一步配置时用的Jaguar2015XE的版本有关，在git clone时要选择2021.1.1版本，否则会产生兼容性问题。

重新配置、编译之后，新模型成功上传。

### **3.4简单的仿真测试**

<iframe title="video" src="https://video.zhihu.com/video/1442532732609544192?player=%7B%22autoplay%22%3Afalse%2C%22shouldShowPageFullScreenButton%22%3Atrue%7D" allowfullscreen="" frameborder="0" class="css-uwwqev" __idm_id__="1761281" style="width: 688px; height: 387px;"></iframe>

LGSVL添加自定义车辆模型

## **4.总结**

在LGSVL中添加车辆模型不算困难，更难的地方可能在于未来需要结合电驱底盘的动力学模型对其中的C#脚本进行修改或者重写。