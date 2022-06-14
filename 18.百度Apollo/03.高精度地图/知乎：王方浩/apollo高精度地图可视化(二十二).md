- [apollo高精度地图可视化(二十二) - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/369780423)

最近写了一个Apollo高精度地图的可视化和格式转换工具，**通过pip安装**之后就可以使用`imap`命令来可视化和转换高精度地图的格式了。

```ps1con
pip install imap_box
```

## 1. 可视化地图

同时支持Apollo格式的地图和Opendrive格式的地图，在命令行中`-m`指定地图文件就可以完成显示了。

```ps1con
imap -m data/borregas_ave.txt
// or
imap -m data/town.xodr
```

### Apollo格式的地图

apollo格式的地图的风格是只显示车道中心线，你还可以通过鼠标点击当前的车道，会改变当前车道、前面的车道和后面的车道的颜色，并且在右上角显示当前车道号，方便调试。

![img](https://pic4.zhimg.com/80/v2-c8e593f2d6adad3d6aae6c8b1d271feb_720w.jpg)apollo格式地图

### OpenDrive格式

opendrive格式的地图元素比较丰富，采用的是显示所有的车道，包括车道边界线，以及自行车车道等（彩色的）。

![img](https://pic4.zhimg.com/80/v2-accc181298d32b93f5dba7f92f5c86b3_720w.jpg)opendrive格式的地图



## 2. 查找指定车道

假设你遇到某条车道报错，或者规划失败的时候，想在地图中查找这条车道的位置的时候，可以使用如下命令来查找这条车道，该车道会显示为红色。

```text
imap -m data/borregas_ave.txt -l lane_35
```

可视化的部分已经介绍完毕了，接下来我们看下地图转换。

## 3. 地图格式转换

Imap同时支持地图转换功能，你可以通过以下命令把地图从**OpenDrive格式转换到Apollo格式**，并且会同时可视化源地图。`-f`表示转换功能开启，`-i`为输入的地图（OpenDrive格式） ,`-o`为输出的地图路径(Apollo格式)。

```python3
imap -f -i data/town.xodr -o data/apollo_map.txt
```

执行完成上述命令之后，我们就可以在`data`目录下找到我们转换好的地图了。

![img](https://pic1.zhimg.com/80/v2-f6ff183da0f3a1ae897b9ce1ea87d670_720w.jpg)

## **项目地址**

项目的地图如下，目前功能还在进一步完善当中，如果你遇到问题，欢迎提交issue。

- [daohu527/imap: High-resolution map visualization and conversion tools (github.com)](https://github.com/daohu527/imap)