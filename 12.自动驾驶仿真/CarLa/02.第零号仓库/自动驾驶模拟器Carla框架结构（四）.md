- [自动驾驶模拟器Carla框架结构（四） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/476896378)

Carla自动驾驶模拟器是Client-Server架构。

Server端一部分是UnrealEngnie，一部分是Carla ,使用C++实现。Server端负责和仿真相关的功能：sensor 渲染，物理计算，更新world state和actor等。

Client端是Carla，由多个Client组成，支持多个Client同时运行 ,一部分是C++实现的，一部分是python实现的。Client端控制actor的逻辑，设置world条件。

![img](https://pic1.zhimg.com/80/v2-afb203b7dd14afa088ce3d5fc1413180_720w.jpg)

Client-Server之间的通信方式采用的是RPC框架，TCP协议。

LibCarla源码在.../LibCarla/source/carla/rpc。

![img](https://pic1.zhimg.com/80/v2-69020f6cf493b55cdb9f0deb40f27758_720w.jpg)

下面简单介绍一下RPC。

RPC（Remote Procedure Call）—[远程过程调用](https://www.zhihu.com/search?q=远程过程调用&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"article"%2C"sourceId"%3A"334657641"})，它是一种通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议。

![img](https://pic1.zhimg.com/80/v2-b77aa5007e00b5cbaf49993992c16b18_720w.jpg)

关于RPC更详细内容，请参考

[学致私教：服务之间的调用为啥不直接用 HTTP 而用 RPC？90 赞同 · 7 评论文章![img](https://pic1.zhimg.com/v2-61566e2289cadd1e25259c7bb2716e28_180x120.jpg)](https://zhuanlan.zhihu.com/p/334657641)

Carla代码结构分析

```text
├── Build //编译时产生的文件夹，里面是编译安装的依赖工具
├── Co-Simulation //联合仿真
│      ├──PTV-Vissim //和 PTV-Vissim联合仿真
│      └── Sumo //和 Sumo联合仿真
├── Dist
├── Docs //markdown说明文档
├── Doxygen //如果安装了  Doxygen，使用 Doxygen生成的文档在这个文件夹
├── Examples //使用CARLA's C++ API的例子
├── Import //外部地图导入
├── IlibCarla //carla和ue4交互
├── PythonAPI //使用python实现的一些仿真例子
├── Unreal //Carla Plugin
└── Util //开发过程中使用的工具
```