- [LGSVL-地图标注 - 简书 (jianshu.com)](https://www.jianshu.com/p/9b57216e052e)

## Map Annotation

LGSVL模拟器支持在现有的3D环境中（Unity scenes）创建、编辑、导出高精度地图。地图可以存储为Apollo、Autoware、Lanelet2支持的格式。
 在目前阶段，推荐在windows环境中，以Unity项目的方式运行模拟器来标注地图。
 具体的标注方法请参考[这里](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Flgsvl%2Fsimulator%2Fblob%2Fmaster%2FDocs%2Fdocs%2Fmap-annotation.md).

## 导出地图标注

高精度地图可以多种格式导出，目前支持的格式如下：

- Apollo  5.0 HD Map
- Apollo  3.0 HD Map
- Autoware Vector Map
- Lanelet2 Map
- OpenDrive Map
   要导出地图：
- 打开`Unity`中的`HD Map Export`：`Simulator`->`Export HD Map`
- 选择下拉菜单中想要的格式`Export Format`
- 输入想要保存的地址
- 选择`Export`来创建导出地图

## 导入地图标注

模拟器可以导入多种格式的标注地图，目前支持的格式有：

- Lanelet2 Map
- Apollo 5.0 HD Map
- OpenDRIVE Map
   要导入地图：
- 打开`Unity`中的`HD Map Export`选项：`Simulator`->`Import HD Map`
- 从下拉菜单`Import Map`中选择想要导入地图的格式
- 选择导入地图的文件位置
- 点击`Import`来导入地图

## 地图格式

关于各种地图格式的具体信息，请查看以下链接：

- [Apollo HD](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FApolloAuto%2Fapollo%2Fissues%2F4048)
- [Autoware Vector](https://links.jianshu.com/go?to=https%3A%2F%2Ftools.tier4.jp%2Fvector_map_builder%2Fuser_guide%2F)
- [Lanelet2](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Ffzi-forschungszentrum-informatik%2FLanelet2)
- [OpenDRIVE](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.opendrive.org%2Fdownload.html)