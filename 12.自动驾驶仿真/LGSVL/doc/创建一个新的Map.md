本文档将描述如何在SVL Simulator中创建新Map。

## 1 开始

1. 从Unity编辑启动SVL模拟器。 (as described [here](https://www.svlsimulator.com/docs/getting-started/getting-started/))

2. 在顶部菜单中，选择文件->新场景或按Ctrl+N。
3. 在Assets/External/Environments/NewMap /Models/Materials和/ prefab中创建你的新地图文件夹。
4. 克隆CubeTown存储库作为一个示例场景是很有帮助的，可以看到所有模拟器特性是如何设置和资产组织的。

## 2 导入

1. 导入您创建或获得的资源到Unity Editor中。

2. 移动模型，纹理，材料和你需要的任何其他资产到/NewMap文件夹。新映射应该没有/NewMap之外文件夹的资产依赖关系。

3. 不要包含脚本文件或非hdrp着色器。如果您的场景中需要脚本，则需要放入SVL Simulator的源文件并构建自己的二进制文件。

4. 如果你的资产有非标准着色器，你将需要在转换为HDRP之前将它们更改为统一标准着色器。

5. 一旦所有非标准着色器被改变，你可以将材料转换为HDRP材料。

6. 选择所有的材料。

7. 选择编辑->渲染管道->升级所选材料到高清晰度材料。如果材料都使用标准着色器，所有的着色器设置将转换，几乎不需要调整。

8. 确保所有材料都检查了GPU实例。

9. 现在我们可以开始构建新地图了。