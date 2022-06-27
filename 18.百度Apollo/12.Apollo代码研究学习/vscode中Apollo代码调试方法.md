- [vscode中Apollo代码调试方法_hello1268的博客-CSDN博客_apollo 调试](https://blog.csdn.net/hello1268/article/details/125235888)

# 设置步骤：

1、copy以下4个文件至当前工作目录下的.vscode文件夹下

`launch.json`、`setttings.json`、`c_cpp_properties.json`、`tasks.json`

![在这里插入图片描述](https://img-blog.csdnimg.cn/46b68b7e517b43c68143180625e0c2e6.png)
2、选择vscode软件的Run/Add [Configuration](https://so.csdn.net/so/search?q=Configuration&spm=1001.2101.3001.7020)…,然后选择C/C++:(gdb)Launch

![在这里插入图片描述](https://img-blog.csdnimg.cn/8771f03f34324fb5ad97cab6d61616bf.png)

然后需要配置`launch.json`文件,上面已经配置好,并已经复制到当前工作目录下的.vscode文件夹下面了,文件内容如下

```cpp
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        
        {
            "name": "debug fusion",
            "type": "cppdbg",
            "request": "launch",
            "program": "/project/thirdparty/X86_64/cyber/bin/mainboard",
            "args": [
                "-d",
                "perception_fusion/middle_ware/cyber/dag/perception_fusion.dag"
            ],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        },
        {
            "name": "debug radar",
            "type": "cppdbg",
            "request": "launch",
            "program": "/project/thirdparty/X86_64/cyber/bin/mainboard",
            "args": [
                "-d",
                "/zhito/perception_radar/middle_ware/cyber/dag/perception_radar.dag"
            ],
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
            ]
        },
        {
            "name": "debug zhito_lidar_fusion",
            "type": "cppdbg",
            "request": "launch",
            "program": "/zhito/output/bin/mainboard",
            "args": [
                "-d",
                "/zhito/modules/drivers/surestar/dag/zhito_lidar_fusion.dag"
            ],
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
            ]
        },
        {
            "name": "debug static_transform",
            "type": "cppdbg",
            "request": "launch",
            "program": "/zhito/output/bin/mainboard",
            "args": [
                "-d",
                "/zhito/modules/transform/dag/static_transform.dag"
            ],
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
            ]
        },
        {
            "name": "debug cyber2zmq",
            "type": "cppdbg",
            "request": "launch",
            "program": "/zhito/output/bin/cyber2zmq",
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
            ]
        },
    ]
}
```

3、VSCODE中启动debug模式，选择debug radar，即可在程序中设置断点进行代码调试

![在这里插入图片描述](https://img-blog.csdnimg.cn/3bd4789577344c6a88d8850b644450e0.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/6bce724059824cb5b140aeac0666a31f.png)