- [无人驾驶算法学习（十一）:IMU标定及Allan方差分析_su扬帆启航的博客-CSDN博客](https://blog.csdn.net/orange_littlegirl/article/details/98077564)

### 文章目录

- 0.引言
  [标定](https://so.csdn.net/so/search?q=标定&spm=1001.2101.3001.7020)IMU的工具包参考港科大的github: https://github.com/gaowenliang/imu_utils

- 1.安装依赖:
  sudo apt-get install libdw-dev

- 2.下载imu_utils和code_utils
  imu_utils下载地址为：https://[github](https://so.csdn.net/so/search?q=github&spm=1001.2101.3001.7020).com/gaowenliang/imu_utils
  code_utils下载地址为： https://github.com/gaowenliang/code_utils

- 3.安装顺序

  > 全局安装ceres库，code_imu依赖ceres。
  > 不要同时把imu_utils和code_utils一起放到src下进行编译。
  > 由于imu_utils 依赖 code_utils，所以先把code_utils放在工作空间的src下面，进行编译。然后再将imu_utils放到src下面，再编译。
  > 在code_utils下面找到sumpixel_test.cpp，修改#include "backward.hpp"为 #include “code_utils/backward.hpp”，再编译。
  > 否则报错:code_utils-master/src/sumpixel_test.cpp:2:24: fatal error: backward.hpp:No such file or directory

- 4.录制imu.bag

  > 让IMU静止不动两个小时，录制IMU的bag.
  > rosbag record /imu/data_raw -o imu120.bag

- 5.标定IMU

  > rosbag play -r 200　imu_utils/imu.bag
  > roslaunch imu_utils myImu.launch
  > launch文件内容:

  ```
    <launch>
        <node pkg="imu_utils" type="imu_an" name="imu_an" output="screen">
            <param name="imu_topic" type="string" value= "/imu/data_raw"/>    #imu topic的名字
            <param name="imu_name" type="string" value= "myImu"/>   
            <param name="data_save_path" type="string" value= "$(find imu_utils)/data/"/>
            <param name="max_time_min" type="int" value= "120"/>   #标定的时长
            <param name="max_cluster" type="int" value= "100"/>
        </node>
    </launch>
  123456789
  ```

- 6.结果显示

```
	%YAML:1.0
	---
	type: IMU
	name: myImu
	Gyr:
	   unit: " rad/s"
	   avg-axis:
	      gyr_n: 2.6843680840756695e-03
	      gyr_w: 2.6321874241472154e-05
	   x-axis:
	      gyr_n: 3.9359404154512782e-03
	      gyr_w: 4.0916654313736004e-05
	   y-axis:
	      gyr_n: 2.4696135810580253e-03
	      gyr_w: 3.3094615661402927e-05
	   z-axis:
	      gyr_n: 1.6475502557177048e-03
	      gyr_w: 4.9543527492775379e-06
	Acc:
	   unit: " m/s^2"
	   avg-axis:
	      acc_n: 4.8881967821011320e-02
	      acc_w: 1.5380403735651225e-03
	   x-axis:
	      acc_n: 7.2516456728875869e-02
	      acc_w: 2.2962008104801422e-03
	   y-axis:
	      acc_n: 3.7902410976698046e-02
	      acc_w: 1.6736315513040769e-03
	   z-axis:
	      acc_n: 3.6227035757460072e-02
	      acc_w: 6.4428875891114792e-04
```

> ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190803152400655.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

- 7.Allan方差分析
  接下来，去画出来这些方差图,在scripts下有很多matlab的脚本文件
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190926205319747.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)
  分析结果 如下：

> double gyro_bias_sigma = 0.00001; // 零偏稳定性，运行中缓慢变化
> double acc_bias_sigma = 0.0001; // 零偏稳定性，运行中缓慢变化
> double gyro_noise_sigma = 0.025; // rad/s 测量噪声
> double acc_noise_sigma = 0.029; //　m/(s^2) 测量噪声

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190926205425581.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29yYW5nZV9saXR0bGVnaXJs,size_16,color_FFFFFF,t_70)

参考:
imu_utils:https://blog.csdn.net/u011178262/article/details/83316968#2__IMU_110
imu_tk:https://zhuanlan.zhihu.com/p/22319718?refer=zimmon