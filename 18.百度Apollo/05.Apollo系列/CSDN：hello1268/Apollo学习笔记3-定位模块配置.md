- [Apollo学习笔记3-定位模块配置_hello1268的博客-CSDN博客](https://blog.csdn.net/hello1268/article/details/111626703)

# 环境介绍

> 1. Apollo-5.5
> 2. Ubuntu 18.04
> 3. 惯导主机：星网宇达 Newton-M2）

# 导航设备参数配置

下面介绍导航配置的方法。当设备正确接入系统后，在/dev/下面有名为ttyACM0的设备，即表示M2已经被正确地加载了。在配置导航设备之前，我们先给导航设备添加一个规则文件。在终端中输入以下命令来查看设备的端口号：

```
ls -l /sys/class/tty/ttyACM0
```

记下形如1-10:1.0的一串数字；在系统/etc/udev/rules.d/目录下执行sudo touch 99-kernel-rename-imu.rules命令新建一个文件99-kernel-rename-imu.rules,执行sudo vim 99-kernel-rename-imu.rules命令添加文件内容：

> ACTION==“add”,SUBSYSTEM==“tty”,MODE==“0777”,KERNELS==“1-10:1.0”,SYMLINK+=“imu”

其中的1-10:1.0就是上面记下的一串数字，根据实际情况进行替换即可；然后先按ESC键然后再按:wq保存文件内容退出，并重启系统。重启系统后执行cd /dev命令，用ls -l imu命令查看设备，要确保imu存在。配置设备时，需要将设备的串口线连接上电脑的串口才可以对设备进行配置，也就是说，用来配置设备的电脑主机需要拥有串口。Windows下可以通过串口助手、串口猎人或者COMCenter等工具进行配置，Linux下可以通过Minicom、cutecom等工具进行配置。linux下建议使用cutecom软件，可使用sudo apt install cutecom来安装此软件，在终端中使用sudo cutecom命令打开该软件，在软件中open名为ttyS0的设备。

```
sudo apt-get install cutecom
```

来安装此软件，在终端中使用

```
sudo cutecom
```

命令打开该软件，在 Device 中选择 ttyS0，点击 Open 即可输入相应配置指令来配置惯导。
点击右上方 Settings 可以设置[波特率](https://so.csdn.net/so/search?q=波特率&spm=1001.2101.3001.7020)等参数。
注：在配置惯导前，输入指令：

```
$cmd,get,sysinfo*ff
```

**获取惯导设备的软件版本，若软件版本为 1.8， 1.8 版本软件存在登录千寻账号时自踢问题，联系惯导厂家获取最新软件包进行升级，将惯导升值至 2.6 版本以上。**
在配置过程中，每输入一条配置指令时，惯导都会返回字段：

```
$cmd,config,ok*ff。
```

若未返回配置成功信息，请检查连线是否正常、端口是否选择正确。

## 导航设备配置

## （1）杆臂配置

车尾天线（后天线，通常是主天线，也就是 Primary）杆臂配置：

```
$cmd,set,leverarm,gnss,x_offset,y_offset,z_offset*ff
```

这里的杆臂值就是车辆集成环节中测量所得的杆臂值，杆臂值请以自己使用的实际情况为准。

## （2）GNSS 航向配置

天线车头车尾前后安装

```
$cmd,set,headoffset,0*ff
```

## （3）导航模式配置

```
$cmd,set,navmode,FineAlign,off*ff
$cmd,set,navmode,coarsealign,off*ff
$cmd,set,navmode,dynamicalign,on*ff
$cmd,set,navmode,gnss,double*ff
$cmd,set,navmode,carmode,on*ff
$cmd,set,navmode,zupt,on*ff
$cmd,set,navmode,firmwareindex,0*ff
```

## （4） USB 接口输出设置

```
$cmd,output,usb0,rawimub,0.010*ff
$cmd,output,usb0,inspvab,0.010*ff
$cmd,through,usb0,bestposb,1.000*ff
$cmd,through,usb0,rangeb,1.000*ff
$cmd,through,usb0,gpsephemb,1.000*ff
$cmd,through,usb0,gloephemerisb,1.000*ff
$cmd,through,usb0,bdsephemerisb,1.000*ff
$cmd,through,usb0,headingb,1.000*ff
```

## （5）网口配置

```
$cmd,set,localip,192,168,0,12*ff
$cmd,set,localmask,255,255,255,0*ff
$cmd,set,localgate,192,168,0,1*ff
$cmd,set,netipport,203,107,45,154,8002*ff
$cmd,set,netuser,qxrpaf001:ncu1234*ff
$cmd,set,mountpoint,RTCM32_GGB*ff
```

这里我们假设您所使用的无线路由器的 IP 地址为 192.168.0.1,那么我们将 M2 主机的 IP地址设置为 192.168.0.12，子网掩码为 255.255.255.0，网关为 192.168.0.1，netipport 设置的是 RTK 基站的 IP 地址和端口， netuser 设置的是 RTK 基站的用户名和密码， mountpoint是 RTK 基站的挂载点。网络配置请依据自己所使用的路由器的实际情况自行更改为相应的配
置， RTK 基站信息请以自己的实际情况为准。注意：在 M2 的网络模块配置完成后，在 IPC 主机中应该是可以 ping 通 IMU 的 ip 地址的；否则， IMU 无法正常联网，在后续的 GNSS 信号检查中会一直显示 SINGLE 而不是我们期望的 NARROW_INT。

*注：本系统使用千寻知寸账号，千寻账号信息为上述配置指令内容，使用前需确认账号密
码是否修改，若已修改需重新配置。*

在终端输入：

```
ping 192.168.0.12
```

能够与 IMU 通讯，如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210202115945940.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

若无法 ping 通，可尝试将连接在路由器的工控机和 IMU 的网线插口交换位置，可重复试几次或重启工控机。

## （6） PPS 授时接口输出

```
ppscontrol enable positive 1.0 10000
log com3 gprmc ontime 1 0.25
```

将所有配置逐条发送给设备，得到设备返回以下字段，

```
$cmd,config,ok*ff
```

说明配置成功，配置成功后要进行配置保存，发送以下指令，

```
$cmd,save,config*ff
```

然后将该设备断电后重新上电加载后即可使用。注意： PPS 授时接口输出的两条配置命令
是没有返回$cmd,config,ok*ff 字段的，这是正常情况，不用担心。
注：确认是否已成功配置，先断电再上电，重启惯导设备，打开 cutecom，输入：

```
$cmd,get,leverarm*ff
$cmd,get,netpara*ff
```

惯导将返回相应配置信息，确认是否为之前配置的信息。

# 设备系统文件配置

系统文件配置主要包括三个部分， GNSS 配置、关闭点云定位和定位模式配置。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021020213065446.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

## （1） GNSS 配置

修改
/apollo/modules/calibration/data/dev_kit/gnss_conf
文件夹下面的配置文件：
gnss_conf.pb.txt
修改如下内容配置基站信息：

> rtk_from {
> format: RTCM_V3
> ntrip {
> address: “”
> port:
> mount_point: “”
> user: “”
> password: “”
> timeout_s: 5
> }
> push_location: true
> }

这是 RTK 基站信息相关的配置，请依据自己的实际情况进行配置。注意： RTK 基站信息需
要同时配置在 M2 的 IMU 主机中和 apollo 的开发套件的 gnss_conf.pb.txt 配置文件中。
注：配置文件内容修改如下所示：
address: “203,107,45,154”
port: 8002
mount_point: “RTCM32_GGB”
user: “qxrpaf001”
password: "ncu1234

## （2）检测 GPS 信号

将车辆移至室外平坦开阔处，进入 Apollo 系统，在终端中执行 gps.sh 脚本打开 gps 模
块。输入命令：

```
cyber_monitor
```

进入：
/apollo/sensor/gnss/best_pose
条目下，查看 sol_type 字段是否为 NARROW_INT。若为 NARROW_INT，则表示 GPS 信号良
好；若不为 NARROW_INT，则将车辆移动一下，直到出现 NARROW_INT 为止。进入
/apollo/sensor/gnss/imu
条目下，确认 IMU 有数据刷新即表明 GPS 模块配置成功。
***注：检测前需配置导航设备与工控机连接端口，否则可能导致无法接收数据。\***

导航设备与工控机连接端口配置完成后，进入 Apollo 系统，启动、进入 docker，输入：

```
bash scripts/bootstrap.sh
```

启动 DreamView,在浏览器中输入：http://localhost:8888
进入 DreamView， 先在 1 处选择 Dev Kit，在 2 处点击 Setup。
在 1 处选择 A45 即可，不需要点击 Setup，点击 Setup 后会启动所有模块，循迹演示只需启动部分模块。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210204160901818.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

**在 docker 中输入：**

```
bash scripts/gps.sh
bash scripts/localization.sh
cyber_monitor
```

启动 gps 和 localization，出现如下界面，表示 gps 和 localization 启动正常，若缺少部分条目，在次执行上述 3 条指令，条目为绿色表示有数据收发，红色表示无数据收发。进入： **/apollo/sensor/gnss/best_pose**
条目下，查看 sol_type 字段是否为 **NARROW_INT**。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210204160659567.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)

若为 NARROW_INT，则表示 GPS 信号良好；若不为 NARROW_INT，则将车辆移动一下，直到
出现 NARROW_INT 为止。进入
**/apollo/sensor/gnss/imu**
条目下，确认 IMU 有数据刷新即表明 GPS 模块配置成功。
若检测不到 gps 数据，请检测惯导是否配置正确、 IMU 能否 Ping 通。
（3）关闭点云定位
apollo/modules/localization/conf/localization.conf
在上述文件中将：
–enable_lidar_localization=true
修改为：
**–enable_lidar_localization=false**
（4）定位模式配置
apollo/modules/localization/conf/localization_config.pb.txt
在上述文件中这个配置应为：
localization_type:MSF
**M2 不支持 RTK 模式。**
apollo/modules/localization/launch/localization.launch在上述文件中的
dag_streaming_rtk_localization.dag
修改为：
**dag_streaming_msf_localization.dag**
补充：
除了上述步骤，还需要：
在 localization 模块的 msf 文件夹下，修改下图中文件 ant_imu_leverarm.yaml 里的参
数，也就是量取前天线和后天线的杆臂值填入。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210204160432503.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2hlbGxvMTI2OA==,size_16,color_FFFFFF,t_70#pic_center)