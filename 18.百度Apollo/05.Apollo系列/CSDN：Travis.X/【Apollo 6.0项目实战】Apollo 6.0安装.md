- [【Apollo 6.0项目实战】Apollo 6.0安装_Travis.X的博客-CSDN博客](https://blog.csdn.net/Travis_X/article/details/120947607)

# 一、安装教程

详细的安装教程可以参考
[开发者说｜Apollo 6.0 安装完全指南](https://mp.weixin.qq.com/s?__biz=MzI1NjkxOTMyNQ==&mid=2247500332&idx=1&sn=d92671fea0e433884ef1fc8cc8329078&chksm=ea1dd05edd6a5948e7d135c231275a4b188f80d42bdce76feb97fecaa0d0aeca52006af232fa&mpshare=1&scene=1&srcid=1025UTpY4Ge35xof2zUW3EeR&sharer_sharetime=1635129864842&sharer_shareid=08197ba589ef0842eb95118b7486efc5&exportkey=AZeWj1tjEhrnwXpHfVjeALs=&pass_ticket=AXsYv26haTfaSv8Ghq0psSYq3N2JDHgMGI8NodexxS9f2TFMJphJP6bHV9QFZfo9&wx_header=0#rd)
[Ubuntu 18.04安装Apollo 6.0：从零开始到启动Demo（超多细节）](https://blog.csdn.net/shao918516/article/details/119223577)
[Ubuntu20.04+Apollo6.0安装](https://blog.csdn.net/weixin_45929038/article/details/120113008?spm=1001.2101.3001.6650.6&utm_medium=distribute.pc_relevant.none-task-blog-2~default~OPENSEARCH~default-6.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~OPENSEARCH~default-6.no_search_link)

------

# 二、启动 [Apollo](https://so.csdn.net/so/search?q=Apollo&spm=1001.2101.3001.7020)

## 2.1 启动Apollo Docker 开发容器

进入到 Apollo 源码根目录，打开终端，执行下述命令以启动 Apollo Docker 开发容器：

```cpp
sudo bash docker/scripts/dev_start.sh
sudo bash docker/scripts/dev_into.sh 
```

## 2.2 启动 Apollo

```cpp
./scripts/bootstrap.sh start
```

在浏览器中访问[ http://localhost:8888 ](http://localhost:8888/)来显示 DreamView 界面
![在这里插入图片描述](https://img-blog.csdnimg.cn/929e4eaf5de14e4bb301955a7f184bfd.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

------

# 三、使用Dreamview查看数据包

Record 是 Apollo 记录数据的一种数据格式，以 .record 为后缀的文件就是我们说的 record 数据包。

在命令行中，输入下面的命令，下载 record 数据包。

```cpp
wget https://apollo-system.cdn.bcebos.com/dataset/6.0_edu/demo_3.5.record
```

在浏览器中输入 http://localhost:8888，访问 Apollo Dreamview，终端中输入以下指令：

```cpp
sourc cyber/setup.bash       
cyber_recorder play -f demo_3.5.record --loop
```

正常显示如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/41fe92f275394f4aaee1454ff0d4a067.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAVHJhdmlzLlg=,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)



<iframe id="NogUW2dd-1638804108614" src="https://player.bilibili.com/player.html?aid=251966481" allowfullscreen="true" data-mediaembed="bilibili" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; font-weight: normal; overflow-wrap: break-word; display: block; width: 660px; height: 330px;"></iframe>

【自动驾驶】Apollo数据回放

------

# 四、关闭Apollo docker

```cpp
./scripts/bootstrap.sh stop
exit
docker stop $(docker ps -aq)
```

------

# 参考

【1】[开发者说｜Apollo 6.0 安装完全指南](https://mp.weixin.qq.com/s?__biz=MzI1NjkxOTMyNQ==&mid=2247500332&idx=1&sn=d92671fea0e433884ef1fc8cc8329078&chksm=ea1dd05edd6a5948e7d135c231275a4b188f80d42bdce76feb97fecaa0d0aeca52006af232fa&mpshare=1&scene=1&srcid=1025UTpY4Ge35xof2zUW3EeR&sharer_sharetime=1635129864842&sharer_shareid=08197ba589ef0842eb95118b7486efc5&exportkey=AZeWj1tjEhrnwXpHfVjeALs=&pass_ticket=AXsYv26haTfaSv8Ghq0psSYq3N2JDHgMGI8NodexxS9f2TFMJphJP6bHV9QFZfo9&wx_header=0#rd)
【2】[Ubuntu 18.04安装Apollo 6.0：从零开始到启动Demo（超多细节）](https://blog.csdn.net/shao918516/article/details/119223577)
【3】[Ubuntu20.04+Apollo6.0安装](https://blog.csdn.net/weixin_45929038/article/details/120113008?spm=1001.2101.3001.6650.6&utm_medium=distribute.pc_relevant.none-task-blog-2~default~OPENSEARCH~default-6.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~OPENSEARCH~default-6.no_search_link)
【4】[使用Dreamview查看数据包](https://apollo.auto/document_cn.html?target=/Apollo-Homepage-Document/Apollo_Doc_CN_6_0/)