- [Apollo规划决策算法仿真调试(2)：使用bazel 编译自定义代码模块 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/508512439)

**前言**

Bazel是一个类似于Make的编译工具，是Google为其内部软件开发的特点量身定制的工具，现在Google内部大部分软件都用Bazel进行构建。Apollo 使用Bazel来编译代码文件，Apollo提供了apollo.sh 脚本来进行编译，但apollo.sh 编译最小单元是modules，而且按照已经写好的build文件进行编译，如果新加cpp文件，则不能编译到。

本文将介绍如何修改Bazel 的build 文件，指定Apollo 编译具体的cpp文件。

**正文如下：**

一、Apollo 自带编译方法

![img](https://pic2.zhimg.com/80/v2-617f23c885da19649ec472ea69d38841_720w.jpg)

二、使用Bazel 自定义需要编译的

1、bazel的根目路就是apollo文件夹的根目录；

![img](https://pic3.zhimg.com/80/v2-ffa560735cd2d231fb9ab0ee0000fc26_720w.jpg)

2、编写cpp文件，并且修改对应模块的build文件

![img](https://pic1.zhimg.com/80/v2-c52a4321c4609e1211487852ea4ff778_720w.jpg)

3、进行编译

bazel build //modules/planning/scenarios/lane_follow:lane_follow_scenario_test_01 --compilation_mode=dbg

4、测试结果如下

可以看到编译结束后生成了对应的可执行文件：

![img](https://pic4.zhimg.com/80/v2-d0fb374e26c422ea16eefcc8b80cc617_720w.jpg)

这个文件可以断点调试：

![img](https://pic4.zhimg.com/80/v2-df66f3e7381a8ab02fadeaca903a4917_720w.jpg)