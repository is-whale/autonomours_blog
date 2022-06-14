- [Apollo规划决策算法仿真调试(1)：使用Vscode断点调试apollo的方法 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/506275430)

**前言**

Vscode 作为轻量化的调试工具深受广大开发者的青睐，虽然大家都用它来看新闻逛论坛炒股，但是用它开发算法确实方便。

Apollo作为成熟的自动驾驶系统被广泛使用，但是关于它调试代码的方法却介绍很少，相信大家也一定希望可以在apollo代码中打断点，来看程序执行过程中的变量以及逻辑，本文将介绍如何使用Vscode打断点调试apollo。

![img](https://pic4.zhimg.com/80/v2-228f011cfd368489a3317af82e71630b_720w.jpg)

**调试方法如下：**

1、Vscoed中安装对应插件，需要安装Remote-Containers Docker 两个插件：

![img](https://pic1.zhimg.com/80/v2-574555b98d9d1d463db46eebcd1d81cc_720w.jpg)

2、下载Apollo工程并执行脚本构建apollo镜像

```bash
bash docker/scripts/dev_start.sh
```

第一次构建镜像比较耗时，可以切换国内源加速，看到OK 说明镜像拉取成功。

![img](https://pic1.zhimg.com/80/v2-13686e56ae01b3334ec8847499f98018_720w.png)

3、第一次使用Vscode 连接apollo docker时，可能要先在命令行进入docker

```bash
bash docker/scripts/dev_into.sh
```

出现类似下面字样说明已经进入docker

![img](https://pic4.zhimg.com/80/v2-bfb6775a4573e2c63c1beaa6b23c68ab_720w.png)

4、此时可以使用Vscode的 Remote-Containers 插件来连接docker

右键需要进入的容器，选择attach to cotainers 进入容器

![img](https://pic3.zhimg.com/80/v2-bc6f9462ba68ef55a247068b81bbc40a_720w.jpg)

当左下角出现对应容器的名称，并且终端显示apollo docker的路径，则说明连接成功：

![img](https://pic3.zhimg.com/80/v2-4f3ab3b19f6d0a0c70fe38fc84d6f3ee_720w.jpg)

5、接下来这步最关键，需要build可调试版本的软件，以apollo 5.5 举例，指令如下：

```bash
bash apollo.sh build_cpu --jobs=1 --ram_utilization_factor 60
```

6、在当前容器中安装debug需要的插件，可以使用配置文件来安装：

```json
{
 "extensions": [
 "ms-vscode.cmake-tools",
 "ms-vscode.cpptools",
 ],
 "workspaceFolder": "/apollo",
 "remoteUser": "xxx",
 "remoteEnv": {
 "HISTFILE": "/apollo/.dev_bash_hist"
 }
}
```

7、接下来一步也很重要，根据需求来写vscode的launch.json；以单元测试来距离，写法如下，可以根据不同需求来替换program

```json
{
 "version": "0.2.0",
 "configurations": [

 {
 "name": "(gdb) Launch",
 "type": "cppdbg",
 "request": "launch",
 // "program": "/apollo/bazel-bin/modules/planning/scenarios/lane_follow/lane_follow_scenario_test",
 // "program": "/apollo/bazel-bin/modules/control/control_component_test",
 "program": "/apollo/bazel-bin/modules/control/control_component_lib",


 "args": [],
 "stopAtEntry": false,
 "cwd": "${workspaceFolder}",
 "environment": [],
 "externalConsole": false,
 "MIMode": "gdb",
 "setupCommands": [
 {
 "description": "Enable pretty-printing for gdb",
 "text": "-enable-pretty-printing",
 "ignoreFailures": true
 }
 ],
 "miDebuggerPath": "/usr/bin/gdb"

 }
 ]
}
```

8、接下来在对应cpp文件中打断点，就可以使用Vscode 的debug功能了

![img](https://pic3.zhimg.com/80/v2-9b98b2641d7d39ea9938ed9d73763f8e_720w.jpg)