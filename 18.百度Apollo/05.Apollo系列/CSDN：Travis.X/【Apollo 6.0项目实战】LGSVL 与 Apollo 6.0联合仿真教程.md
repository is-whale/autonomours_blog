- [【Apollo 6.0项目实战】LGSVL 与 Apollo 6.0联合仿真教程_Travis.X的博客-CSDN博客](https://blog.csdn.net/Travis_X/article/details/121834070)

# 前言

SVL Simulator 是一个用于自动驾驶汽车和机器人系统开发的仿真平台。

目前SVL Simulator 提供了百度开发的开源 AD 系统平台[Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020)以及Autoware Foundation开发的Autoware.AI和Autoware.Auto的集成。

本文教程在**Ubuntu 20.04** 下实现 LGSVL 与 Apollo 6.0的联合仿真。
![在这里插入图片描述](https://img-blog.csdnimg.cn/a6a4cbd5793e445bbf68524d27152bfc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16)

------

# 一、Apollo 6.0安装

Apollo 6.0安装教程参考 https://blog.csdn.net/Travis_X/article/details/120947607

------

# 二、LGSVL安装

## 1. 注册账户

先在[官网](https://www.svlsimulator.com/)注上册一个账户，注册后会收到了“完成注册”的电子邮件，单击了“验证电子邮件”链接，进行验证即可。
![在这里插入图片描述](https://img-blog.csdnimg.cn/6651e8a7daa444158e6de707709bef24.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

## 2. 下载Linux版本仿真器

到[官网](https://www.svlsimulator.com/)下载Linux版本的仿真器 svlsimulator-linux64-2021.3.zip。

------

## 3. 启动仿真器

解压 svlsimulator-linux64-2021.3.zip文件后双击打开simulator文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/67eb695acef443fabe92ff578008c2bb.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

打开后界面如下图所示
![在这里插入图片描述](https://img-blog.csdnimg.cn/1de4f7937b05426eae1f2ed0f8879e20.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
点击OPEN BROWSER配置会自动打开一个网页，登录自己的账号，如下图所示。

您可以查看Store中看到各种的地图、车辆类型和插件，可以创建自己的仿真环境。
![在这里插入图片描述](https://img-blog.csdnimg.cn/7363b441135947e78becece5567cba5f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

## 4. 配置自己的仿真环境

点击左侧显示栏的Simulation
![在这里插入图片描述](https://img-blog.csdnimg.cn/f1c5a059a045401f81f6bdaae5c551ff.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
选择右上角的Add New按钮，按照提示填写配置文件信息
![在这里插入图片描述](https://img-blog.csdnimg.cn/b68419947bf64f4193b35a1669456b64.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

因为有些地图Apollo6.0文件夹中没有以及有些车辆不支持Apollo6.0，所以地图和车辆可以按照我的来填写，选择地图BorregasAve和车辆Lexus2016RXHybrid，车辆下方要选择Apollo6.0（Module Testing）。
![在这里插入图片描述](https://img-blog.csdnimg.cn/4f9a6e5e2d38436dabb7f210da6a4903.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
下方的参数模式时间、天气、交通等参数可以自己随意填写，填写完后点击Save保存。
![在这里插入图片描述](https://img-blog.csdnimg.cn/08aaa674dc1c4f9ea1cc4c9868fed798.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
保存后会出现以下界面，但先不着急启动。
![在这里插入图片描述](https://img-blog.csdnimg.cn/aa3cffa42d254f9f90012fccace03045.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 三、LGSVL 与 Apollo6.0 联合仿真

## 1. Apollo启动

```cpp
cd apollo/

./docker/scripts/dev_start.sh

./docker/scripts/dev_into.sh

./scripts/bootstrap_lgsvl.sh

./scripts/bridge.sh
```

打开Dreamview http://localhost:8888/，在上方选择对应的模式、车型以及地图。
![在这里插入图片描述](https://img-blog.csdnimg.cn/f81054534dab45728cfa7fbae27f8bb1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

## 2. LGSVL启动

点击Run Simulation按钮
![在这里插入图片描述](https://img-blog.csdnimg.cn/065e43124cd346faaa02c1ce3ee64956.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
这时候可以看到加载的仿真环境。
![在这里插入图片描述](https://img-blog.csdnimg.cn/1d7bd54ba186460db6de1b778e9bddd0.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

## 3. LGSVL与Apollo的联合仿真

启动对应的模块就可以看到与仿真环境对应的车辆和地图。
![在这里插入图片描述](https://img-blog.csdnimg.cn/dda78e0ed0dc4bac9d86de46189db1cc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
点击左侧栏的Route editing进行全局的路径规划。
![在这里插入图片描述](https://img-blog.csdnimg.cn/6f086de342274cdeb43d6631cd38bcd9.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
发布目标点后，仿真环境的车辆就会开始进入自动驾驶模式。
![在这里插入图片描述](https://img-blog.csdnimg.cn/c1ffa1e1c04e4987b0e1f40bba621a04.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 四、演示视频



<iframe id="7fpbVFFx-1639367100892" src="https://player.bilibili.com/player.html?aid=252269093" allowfullscreen="true" data-mediaembed="bilibili" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; font-weight: normal; overflow-wrap: break-word; display: block; width: 660px; height: 330px;"></iframe>

【自动驾驶】Apollo 6.0 与 LGSVL 联合仿真（4）





<iframe id="Z3W1oHGy-1638797009334" src="https://player.bilibili.com/player.html?aid=977167682" allowfullscreen="true" data-mediaembed="bilibili" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; font-weight: normal; overflow-wrap: break-word; display: block; width: 660px; height: 330px;"></iframe>

【自动驾驶】Apollo 6.0 与 LGSVL 联合仿真（3）





<iframe id="L1lu6ce8-1638797216049" src="https://player.bilibili.com/player.html?aid=464650353" allowfullscreen="true" data-mediaembed="bilibili" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; font-weight: normal; overflow-wrap: break-word; display: block; width: 660px; height: 330px;"></iframe>

【自动驾驶】Apollo 6.0 与 LGSVL 联合仿真（2）





<iframe id="SFV70vXi-1638797184979" src="https://player.bilibili.com/player.html?aid=722177108" allowfullscreen="true" data-mediaembed="bilibili" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; font-weight: normal; overflow-wrap: break-word; display: block; width: 660px; height: 330px;"></iframe>

【自动驾驶】Apollo 6.0 与 LGSVL 联合仿真（1）

# 五、Apollo 模块上机实践

[【Apollo 6.0项目实战】HD-Map模块](https://blog.csdn.net/Travis_X/article/details/121486163)
[【Apollo 6.0项目实战】Canbus模块](https://blog.csdn.net/Travis_X/article/details/121539973)
[【Apollo 6.0项目实战】Localization模块](https://blog.csdn.net/Travis_X/article/details/121768756)
[【Apollo 6.0项目实战】Perception模块](https://blog.csdn.net/Travis_X/article/details/121518854)
[【Apollo 6.0项目实战】Prediction模块](https://blog.csdn.net/Travis_X/article/details/121658184)
[【Apollo 6.0项目实战】Routing模块](https://blog.csdn.net/Travis_X/article/details/121674874)
[【Apollo 6.0项目实战】Planning模块](https://blog.csdn.net/Travis_X/article/details/121793238)
[【Apollo 6.0项目实战】Control模块](https://blog.csdn.net/Travis_X/article/details/121802955)

------

# 参考

【1】[SVL Simulator: An Autonomous Vehicle Simulator](https://www.svlsimulator.com/docs/getting-started/getting-started/)