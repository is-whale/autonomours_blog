- [uwb高精度定位_北京华星智控的博客-CSDN博客_uwb高精度定位系统](https://blog.csdn.net/qq_35699674/article/details/103096221)

华星智控无线定位系统，采用先进的脉冲无线定位技术，通过在工作区域部置定位基站，检测进入生产现场的相关人员配置标签的方式，实现对进入生产现场人员的实时高[精度](https://so.csdn.net/so/search?q=精度&spm=1001.2101.3001.7020)定位，在这种复杂的应用场景下最高精度10~30cm，系统容量大，实时性好。该无线定位系统基于超窄脉冲技术，是国内领先的高精度无线定位产品。无线定位系统使用先进的超窄脉冲精确测量飞行时间技术，实现了底层的精确测距/计时；结合位置解算算法，实现了各层的精确定位。基于系统定位功能开发的电子围栏及SOS报警等功能，有利于现场作业人员的动态管理，避免发生不安全事件。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY2Njk4XyVFOCVCNCVBOCVFNiVBMyU4MCVFNCVCQSVCQSVFNSU5MSU5OCVFNSVBRSU5QSVFNCVCRCU4RDAxLnBuZw?x-oss-process=image/format,png)

人员定位系统主要包括定位微基站、定位微标签、定位解算服务器、定位解算引擎及POE交换机、网线等网络设备构成。图中定位基站是基础定位单元区，该区域内的定位基站使用POE网线供电并通讯；进入现场的人员或现场的设备设备通过佩戴或安装定位微标签实现区域内的实时位置定位。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY2Njk5XyVFOCVCNCVBOCVFNiVBMyU4MCVFNCVCQSVCQSVFNSU5MSU5OCVFNSVBRSU5QSVFNCVCRCU4RDAyLnBuZw?x-oss-process=image/format,png)

定位微基站可以通过WiFi或者以太网将采集到的数据回传到解算服务器进行解算。无线定位系统的系统架构如下图所示。

利用先进的室内、室外人员定位系统，可以有效地加强对现场操作人员、技术人员的安全管理，实时定位现场人员，直观及时地反映区域内的作业情况、人员状况，提高人员安全保障的力度及效率，是人员安全监控管理的有力工具。

项目系统采用先进的UWB技术和手机GPS技术相结合实现了室内室外人员定位全覆盖，系统硬件由监控主机、交换机、定位基站、定位卡、手机GPS终端组成。首先根据室内布局一定数量的定位基站，通过定位基站使得室内无线信号覆盖，然后为人员佩带定位手环，各方向的定位基站不断接收定位手环发送无线信息，定位基站将定位手环的信息通过交换机上传至监控主机，系统根据定位手环位置信息及手机GPS定位信息进行分析处理，进而实现人员定位、历史轨迹回放等功能，精度满足预警需求。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY1NjcxX3V3YmdwcyVFNiU5OSVCQSVFOCU4MyVCRCVFNiU4OSU4QiVFOCVBMSVBODAxLnBuZw?x-oss-process=image/format,png)

智能定位系统-系统功能

系统基于对生产人员的实时位置信息的分析，全方位智能化实现对安全生产的预防，监控，报警，追责。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMDA3OTk4XyVFNSVBRSVBNCVFNSU4NiU4NSVFNSVBRSU5QSVFNCVCRCU4RCVFNyVCMyVCQiVFNyVCQiU5Ri5naWY)

实现室内室外定位 各类人员的实时高精度位置信息在控制中心实现实时可视化管理，实现对系统的全局把控，防范安全事故的发生。 局域网的电脑或者PAD都可以通过web形式来查看位置信息,包含uwb和GPS定位信息.

标签智能休眠 为增加待机时长，减少无线射频信号发射，标签增加智能休眠机制，标签静止等情况下标签智能休眠。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY1NjcyX3V3YmdwcyVFNiU5OSVCQSVFOCU4MyVCRCVFNiU4OSU4QiVFOCVBMSVBODAyLnBuZw?x-oss-process=image/format,png)

区域告警 系统实时监测人员位置信息当生产人员靠近或者进入危险区域，系统可通过手环震动的形式做出报警，对现场生产人员给予及时的提醒，以防止安全事故的发生。 对于有权限人员进行区域内人员静止检测，当人员长时间未活动时系统给出告警信息。 区域联保互保，当互保人员距离超出设定范围。系统给出报警，并且手环震动提示。

低电量提示 定位设备具有自检测电池电量功能，当设备电量低时提示及时充电。

人员静止检测 设备内置静止检测传感器，当检测设备长时间未运动向系统提交静止信息，系统根据设备所在区域判断人员未携带设备或发生危险情况。

考勤辅助 基于系统高精度定位的特性，可在厂区入口等任意位置加设定位设备实现考勤辅助功能，记录出入区域记录并记录时间。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMDA4Nzc1XyVFOCU4NyVBQSVFNSU4QSVBOCVFOCU4MCU4MyVFNSU4QiVBNC5naWY)
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMDEzMzM3XyVFOCU4MCU4MyVFNSU4QiVBNCVFNiU5NyVCNiVFOSU5NyVCNCVFNyVCQiU5RiVFOCVBRSVBMS5wbmc?x-oss-process=image/format,png)
心率检测 通过腕带心率模块检测人的生命体征，及时监测人的体征状态，当被检测人的心率波动较大时，系统给出报警并归档。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY1NjczX3V3YmdwcyVFNiU5OSVCQSVFOCU4MyVCRCVFNiU4OSU4QiVFOCVBMSVBODAzLnBuZw?x-oss-process=image/format,png)

电子围栏功能 通过uwb定位系统的管理后台设置电子围栏，“越界预警、滞留预警、长时间静止预警”等功能。工人误入危险区域，系统会自动发出警报并且手环震动报警。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMDA4Njk2XyVFNyU5NCVCNSVFNSVBRCU5MCVFNSU5QiVCNCVFNiVBMCU4RiVFNSU4QSU5RiVFOCU4MyVCRC5naWY)

轨迹回放 轨迹回放可以选择某个标签对应的时间段的走过的轨迹，并且可以加速回放，而且可以显示移动轨迹。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzE0MjcyXyVFOCVCRCVBOCVFOCVCRiVCOSVFNSU5QiU5RSVFNiU5NCVCRS5naWY)
围栏报警列表 这里是显示围栏报警统计信息，可以查询任意标签，并且可查看进入和离开时间 。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMDEzMzM0XyVFNyU5NCVCNSVFNSVBRCU5MCVFNSU5QiVCNCVFNiVBMCU4Ri5naWY)

低电量报警列表 显示标签电量低时自动报警，并显示剩余电量。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY2NzA2XyVFOCVCNCVBOCVFNiVBMyU4MCVFNCVCQSVCQSVFNSU5MSU5OCVFNSVBRSU5QSVFNCVCRCU4RDAyLmpwZWc?x-oss-process=image/format,png)

该无线定位系统的核心是实现了人员位置的实时、精确定位、生命体征监测。在工厂的应用中，可以实现以下功能：

1，实时位置定位。当工作人员进入定位区域以后，在任何时刻任意位置，定位基站都可以感应到信号，并上传到监控中心服务器，经过软件处理，得出各具体信息（如：人员ID，位置，具体时间），同时可把它动态显示（实时）在监控中心的大屏幕或电脑上，并作好备份，使得管理人员可随时了解工作人员的状态。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY2NzA1XyVFOCVCNCVBOCVFNiVBMyU4MCVFNCVCQSVCQSVFNSU5MSU5OCVFNSVBRSU5QSVFNCVCRCU4RDAxLmpwZWc?x-oss-process=image/format,png)

2，工作人员远距离工作状态，后台报警提示。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY2NzEwXyVFOCVCNCVBOCVFNiVBMyU4MCVFNCVCQSVCQSVFNSU5MSU5OCVFNSVBRSU5QSVFNCVCRCU4RDA2LmpwZWc?x-oss-process=image/format,png)

3，突发情况报警。当工作区域发生突发情况，员工可通过按下所携带的定位标签上的定位按钮发出警报，同时监控室的动态显示界面会立即触发报警事件并进行记录。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY2NzA5XyVFOCVCNCVBOCVFNiVBMyU4MCVFNCVCQSVCQSVFNSU5MSU5OCVFNSVBRSU5QSVFNCVCRCU4RDA1LmpwZWc?x-oss-process=image/format,png)

4，电子围栏。当工人进入危险区域或进入其他不允许活动的区域时，该无线定位系统能够分析出这种非正常的情况，并及时做出报警信息，为管理人员提供重要参考；
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY2NzA4XyVFOCVCNCVBOCVFNiVBMyU4MCVFNCVCQSVCQSVFNSU5MSU5OCVFNSVBRSU5QSVFNCVCRCU4RDA0LmpwZWc?x-oss-process=image/format,png)

5，日常人员管理。管理者可随时观看大屏幕或电脑上的工厂人员及设备活动情况，并可查看任意区域、任何班组/部分/个人的信息状况，并可进行报表打印（可选），历史数据查询（可选），为管理带来极大的方便。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMDEzMzQ1XyVFOCVBNyU4NiVFOSVBMiU5MSVFOCU4MSU5NCVFNSU4QSVBOC5naWY)

6，生命体征监测。管理者可以通过心率监测模块来监测工作人员的生命体征，结合标签上的振动传感器来辅助判断工作人员的活动状态。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY2NzA3XyVFOCVCNCVBOCVFNiVBMyU4MCVFNCVCQSVCQSVFNSU5MSU5OCVFNSVBRSU5QSVFNCVCRCU4RDAzLmpwZWc?x-oss-process=image/format,png)

工作流程 标签发射出UWB信号→基站接收UWB信号→计算服务器进行数据解算→计算服务器输出定位数据至上级应用系统。 手机GPS信号通过4G基站传到服务器在地图上呈现。

基站部署方案 ，每层基站安装点位及网线路由如下所示：
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY2NzAwXyVFOCVCNCVBOCVFNiVBMyU4MCVFNCVCQSVCQSVFNSU5MSU5OCVFNSVBRSU5QSVFNCVCRCU4RDAxLnBuZw?x-oss-process=image/format,png)
一层共安装基站19个，各房间内15个，楼道3个，室外1个。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY2NzAxXyVFOCVCNCVBOCVFNiVBMyU4MCVFNCVCQSVCQSVFNSU5MSU5OCVFNSVBRSU5QSVFNCVCRCU4RDAyLnBuZw?x-oss-process=image/format,png)
二层共安装基站20个，各房间内17个，楼道3个。
![在这里插入图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy53ZXpoYW4uY24vY29udGVudC9zaXRlZmlsZXMvODg2NDYvaW1hZ2VzLzEyMzY2NzAyXyVFOCVCNCVBOCVFNiVBMyU4MCVFNCVCQSVCQSVFNSU5MSU5OCVFNSVBRSU5QSVFNCVCRCU4RDAzLnBuZw?x-oss-process=image/format,png)
三层共安装基站20个，各房间内17个，楼道3个。