- [车路协同自动驾驶数据集DAIR-V2X_同学来啦的博客-CSDN博客](https://blog.csdn.net/zhouqiping/article/details/123171905?ops_request_misc=%7B%22request%5Fid%22%3A%22165571968716782184648013%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fcode.%22%7D&request_id=165571968716782184648013&biz_id=&utm_medium=distribute.pc_search_result.none-task-code-2~code~first_rank_ecpm_v1~code_v1-3-123171905-0-null-null.nonecase&utm_term=V2X)

# 一、DAIR-V2X数据集简介

自动驾驶安全面临巨大挑战，单车智能存在驾驶盲区、中远距离感知不稳定等问题，导致自动驾驶车辆可运行设计域（ODD）受限，单车智能自动驾驶落地受阻。车路协同将助力保障自动驾驶安全运行。而数据是车路协同自动驾驶的关键，为促进学术界和产业界共同打造数据驱动的车路协同自动驾驶，清华大学智能产业研究院（AIR）依托北京市高级别自动驾驶示范区，推出全球首个车路协同自动驾驶数据集DAIR-V2X，共同探索车路协同自动驾驶的落地模式。
![在这里插入图片描述](https://img-blog.csdnimg.cn/df130041476f412d888b116b28f5ad7a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZCM5a2m5p2l5ZWm,size_20,color_FFFFFF,t_70,g_se,x_16)

## 1、数据集特色优势

DAIR-V2X车路协同数据集是首个用于车路协同自动驾驶研究的大规模、多模态、多视角数据集，全部数据采集自真实场景，同时包含2D&3D标注，具备如下特点：

- 总计72890帧图像数据和72890帧点云数据；
  ①DAIR-V2X协同数据集(DAIR-V2X-C)，包含40481帧图像数据和40481帧点云数据
  ②DAIR-V2X路端数据集(DAIR-V2X-I)，包含10084帧图像数据和10084帧点云数据
  ③DAIR-V2X车端数据集(DAIR-V2X-V)，包含22325帧图像数据和22325帧点云数据
- 首次实现车路协同时空同步标注；
- 传感器类型丰富，包含车端相机、车端LiDAR、路端相机和路端LiDAR等类型传感器；
- 障碍物目标3D标注属性全面，标注15类道路常见障碍物目标；
- 采集自北京市高级别自动驾驶示范区10公里城市道路、10公里高速公路、以及28个路口；
- 数据涵盖晴天/雨天/雾天、白天/夜晚、城市道路/高速公路等丰富场景；
- 数据完备，包含脱敏后的原始图像和点云数据、标注数据、时间戳、标定文件等。

## 2、数据集引用方式

```python
@inproceedings{
	yu2022dairv2x,
	title={DAIR-V2X: A Large-Scale Dataset for Vehicle-Infrastructure Cooperative 3D Object Detection},
	author={Yu, Haibao and Luo, Yizhen and Shu, Mao and Huo, Yiyi and Yang, Zebang and Shi, Yifeng and Guo, Zhenglong and Li, Hanyu and Hu, Xing and Yuan, Jirui and Nie, Zaiqing},
	booktitle={IEEE/CVF Conf.~on Computer Vision and Pattern Recognition (CVPR)},
	month = jun,
	year={2022}
}
```

## 3、数据集联合发布机构

- 清华大学智能产业研究院（AIR）
- 北京市高级别自动驾驶示范区
- 北京车网科技发展有限公司
- 百度Apollo
- 北京智源人工智能研究院

# 二、解决痛点

与仅包含单车端或路端的数据集相比，此次发布的车路协同DAIR-V2X数据集首次克服了以往车路协同在同一时空检测但数据不同步的难题，提出车路协同多模态融合方法并给出检测指标，解决了车路协同产业以往缺乏真实道路场景数据的痛点。

# 三、开展任务研究

基于该数据集，研究人员可以展开车端3D检测任务（包括单目、点云和多模态三种形式）、路端3D检测任务（包括单目、点云和多模态三种形式）、车路协同3D检测（包括单目、点云和多模态等多种组合形式）等相关研究。

## 1、车端3D检测

### 1.1 数据采集

#### （1）设备型号

- **Velodyne128 LiDAR**
  ①采样帧率：10Hz
  ②水平FOV：360，垂直FOV： 40°，-25°~15°
  ③最大探测范围：245m
  ④探测距离精度： <=3cm
  ⑤最小角分辨率（垂直）：0.11°
- **Camera**
  图像分辨率：1920x1080

#### （2）标定和坐标系

完备的车端3D感知需要获取相机和LiDAR传感器数据的相互位置和内外参数等，以建立不同传感器数据间的空间同步。

- **LiDAR坐标系**
  LiDAR坐标系是以LiDAR传感器的几何中心为原点，x轴水平向前，y轴水平向左，z轴竖直向上，符合右手坐标系规则。
- **相机坐标系**
  相机坐标系是以相机光心为原点，x轴和y轴与图像平面坐标系的x轴和y轴平行，z轴与相机光轴平行向前、与图像平面垂直。通过相机到LiDAR的外参矩阵，可以将点从相机坐标系转到LiDAR坐标系。
- **图像坐标系**
  图像坐标是以相机主点（即相机光轴与图像平面的交点，一般位于图像平面中心）为原点，x轴水平向右，y轴水平向下的二维坐标系。相机内参可以实现从相机坐标到图像坐标的投影。

### 1.2 数据标注

从车端数据中选择22325帧有效图像+点云多模态数据，利用2D&3D联合标注等技术标注图像和点云多模态数据中的道路障碍物目标的2D和3D框，同时标注了障碍物类别、障碍物3D信息、遮挡和截断等信息。其中DAIR-V2X的3D标注是以LiDAR为坐标系，同时保存如下标注信息：
①**障碍物类别**：一共15类，包括行人、机动车等，其中带Ignore表示目标像素值小于15x15或者遮挡部分大于4/5，OtherIgnore表示非人车目标像素值小于15x15或者遮挡部分大于4/5；
![在这里插入图片描述](https://img-blog.csdnimg.cn/01d3df0993c149edaab258dce4f6504d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZCM5a2m5p2l5ZWm,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
②**障碍物截断**：从[0, 1, 2]中取值，分别表示不截断、横向截断、纵向截断
③**障碍物遮挡**：从[0, 1, 2]中取值，分别表示不遮挡、0%～50%遮挡，50%～100%遮挡
④**alpha**：观察者视角，范围在[-pi, pi]
⑤**2D box**：图像中2D bounding box框
⑥**3D box**：3D bounding box，车端基于 LiDAR坐标系，路端基于虚拟LiDAR坐标系；包括 (height, width, length, x_loc, y_loc, z_loc) ，以米为单位；包括 (rotation_y) ，表示障碍物绕Y轴旋转角度

### 1.3 数据文件结构

![文件数据结构](https://img-blog.csdnimg.cn/67c5c85ab1214dc0a4483a6df8d6d7b1.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZCM5a2m5p2l5ZWm,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

### 1.4 评测指标

**目标检测精度mAP**：针对车辆、行人等目标，计算3D 边界框的尺寸、位置和置信度，基于 IoU 计算mean average precision (mAP) ，最终的精度是所有类别mAP的均值。

## 2、路端3D检测

### 2.1 数据采集

#### （1）路测传感器型号

- **300线LiDAR**：
  ①采样帧率：10Hz
  ②水平FOV：100° ，垂直FOV：40°
  ③最大探测范围：200～280m
  ④探测距离精度：<=3cm
- **Camera**：
  ①传感器类型：1英寸全局曝光CMOS
  ②采样帧率：25Hz
  ③传感器分辨率：最大采样分辨率为4096x2160
  ④图像格式：RGB格式，按1920x1080分辨率压缩保存为JPEG图像

#### （2）标定和坐标系

完备的路端3D感知需要获取相机和LiDAR传感器数据的相互位置和内外参数等，以建立不同传感器数据间的空间同步。其中路端LiDAR点云及相关内外参，全部转至x-y平面与地面平行的虚拟LiDAR坐标系。

- **虚拟LiDAR坐标系**
  虚拟LiDAR坐标系是以LiDAR传感器的几何中心为原点，x轴平行地面向前，y轴平行地面向左，z轴垂直于地面竖直向上，符合右手坐标系规则。由于路端LiDAR与地面存在俯仰角，为方便研究，通过路端LiDAR外参矩阵，统一将路端LiDAR坐标系转到虚拟LiDAR坐标系，同时将路端点云全部转到虚拟LiDAR坐标系。
- **相机坐标系**
  相机坐标系是以相机光心为原点，x轴和y轴与图像平面坐标系的x 轴和y 轴平行，z轴与相机光轴平行向前、与图像平面垂直。通过相机到LiDAR的外参矩阵，可以将点从相机坐标系转到LiDAR坐标系。
- **图像坐标系**
  图像坐标是以相机主点（即相机光轴与图像平面的交点，一般位于图像平面中心）为原点，x轴水平向右，y轴水平向下的二维坐标系。相机内参可以实现从相机坐标到图像坐标的投影。

### 2.2 数据标注

从车端数据中选择10084帧有效图像+点云多模态数据，利用2D&3D联合标注等技术标注图像和点云多模态数据中的道路障碍物目标的2D和3D框，同时标注了障碍物类别、障碍物3D信息、遮挡和截断等信息。其中Dair-V2X的3D标注是以LiDAR为坐标系，同时保存如下标注信息：
①**障碍物类别**：一共15类，包括行人、机动车等，其中带Ignore表示目标像素值小于15x15或者遮挡部分大于4/5，OtherIgnore表示非人车目标像素值小于15x15或者遮挡部分大于4/5；
![在这里插入图片描述](https://img-blog.csdnimg.cn/01d3df0993c149edaab258dce4f6504d.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZCM5a2m5p2l5ZWm,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
②**障碍物截断**：从[0, 1, 2]中取值，分别表示不截断、横向截断、纵向截断
③**障碍物遮挡**：从[0, 1, 2]中取值，分别表示不遮挡、0%～50%遮挡，50%～100%遮挡
④**alpha**：观察者视角，范围在[-pi, pi]
⑤**2D box**：图像中2D bounding box框
⑥**3D box**：3D bounding box，车端基于 LiDAR坐标系，路端基于虚拟LiDAR坐标系；包括(height, width, length, x_loc, y_loc, z_loc) ，以米为单位；包括 (rotation_y) ，表示障碍物绕Y轴旋转角度

### 2.3 数据文件结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/974ff66f7dd74504a2a1c35613706507.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZCM5a2m5p2l5ZWm,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

### 2.4 评测指标

**目标检测精度mAP**：针对车辆、行人等目标，计算3D 边界框的尺寸、位置和置信度，基于 IoU 计算mean average precision (mAP) ，最终的精度是所有类别mAP的均值。

## 3、车路协同3D检测

### 3.1 车路协同3D检测任务

车路协同3D检测是在通信带宽约束下，车端融合路端信息，实现3D目标检测的视觉感知任务。与传统自动驾驶3D检测任务相比，本任务需要解决车端与路端多视角、数据多模态、时空异步、通信受限等挑战，通过设计车路融合感知算法，实现盲区补充、提升感知精度。

#### （1）问题建模

- **输入**：车端多模态数据、路端多模态数据，以及对应的时间戳和标定文件
- **优化目标**：
  ①**提高检测性能**：提升算法在测试集上的3D目标检测精度；
  ②**减少路端数据使用量**：保证相近精度的前提下，降低路端数据使用量，减少通信时延；
  ③**减少传感器使用量**：保证相近精度的前提下，降低车端和路端传感器使用数量，以节省成本、降低能耗。

#### （2）评测指标

- **目标检测精度**：
  针对车辆、行人等目标，计算3D边界框的尺寸、位置和置信度，基于 IoU 计算mean average precision (mAP) ，最终的精度是所有类别mAP的均值。
- **路端数据使用量**：
  以算法使用的路端信息比特数作为评测指标。

#### （3）Baseline后融合参考方案

分别利用车端相机+LiDAR及路端相机+LiDAR传感器信息，计算3D目标位置、置信度等结果，在虚拟世界坐标系中将计算结果进行后融合。车路协同感知后融合整体流程如下图。
![在这里插入图片描述](https://img-blog.csdnimg.cn/b915e290dc5d419fb7cf951f37dfedbd.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZCM5a2m5p2l5ZWm,size_20,color_FFFFFF,t_70,g_se,x_16)

### 3.2 数据采集

#### （1）场景设置

- **路侧设备**：基于北京市高级别自动驾驶示范区，选择若干交通场景复杂路口，路侧部署相机和激光雷达，完成GPS授时同步，并完成相应的内参外参标定。
- **自动驾驶车辆**：利用配置好相机和激光雷达的自动驾驶车辆，完成GPS授时同步，并完成相应的内参外参标定。
- **设置路线并采集数据**：当自动驾驶路线经过路侧设备附近区域时，分别保存该时段路侧传感器和自动驾驶传感器数据。
- **截取数据**：从保存的传感器数据截取20s以上片段作为车路协同数据。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/c2e732c637754ea2a95696774f15c546.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZCM5a2m5p2l5ZWm,size_20,color_FFFFFF,t_70,g_se,x_16)

#### （2）路端采集设备

在每个路口安装至少一对相机和激光雷达，其中每对相机和激光雷达安装在相同方位，同时对该相机和激光雷达进行标定，并对图像去畸变。路侧传感器型号如下：

- **300线LiDAR**：
  ①采样帧率：10Hz
  ②水平FOV：100° ，垂直FOV：40°
  ③最大探测范围：280m
  ④探测距离精度：<=3cm
- **Camera**：
  ①传感器类型：1英寸全局曝光CMOS
  ②采样帧率：25Hz
  ③图像格式：RGB格式，按1920x1080分辨率压缩保存为JPEG图像

#### （3）车端采集设备

自动驾驶车配备1个顶端激光雷达1个前视摄像头，同时对该激光雷达和前视摄像头进行标定，并对图像去畸变。顶端激光雷达和前视摄像头型号如下：

- **Hesai Pandar40线LiDAR**：
  ①采样帧率：10Hz
  ②水平FOV：360° ，垂直FOV：40°，-25°~15°
  ③最大探测范围：200m
  ④反射率：10%
  ⑤最小垂直分辨率：0.33°
- **Camera**：
  ①采样帧率：20Hz
  ②水平FOV：128° ，垂直FOV：77°
  ③图像格式：RGB格式，按1920x1080分辨率压缩保存为JPEG图像

#### （4）标定和坐标系

为了达到不同传感器之间的空间同步，车路协同需要使用传感器参数信息进行坐标系转换，各坐标系之间的关系如下图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/13db54da6f6842e5970b450b8213eab5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZCM5a2m5p2l5ZWm,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

- **虚拟世界坐标系**
  虚拟世界坐标系是以地面某一随机位置为原点，x轴、y轴与地面平行，z轴垂直于地面竖直向上，符合右手坐标系规则。
- **LiDAR坐标系**
  LiDAR坐标系是以LiDAR传感器的几何中心为原点，x轴水平向前，y轴水平向左，z轴竖直向上，符合右手坐标系规则。
- **虚拟LiDAR坐标系**
  虚拟LiDAR坐标系是以LiDAR传感器的几何中心为原点，x 轴平行地面向前，y 轴平行地面向左，z 轴垂直于地面竖直向上，符合右手坐标系规则。由于路端LiDAR与地面存在俯仰角，为方便研究，通过路端LiDAR外参矩阵，统一将路端LiDAR坐标系转到虚拟LiDAR坐标系，同时将路端点云全部转到虚拟LiDAR坐标系。
- **相机坐标系**
  相机坐标系是以相机光心为原点，x 轴和y 轴与图像平面坐标系的x 轴和y 轴平行，z 轴与相机光轴平行向前、与图像平面垂直。通过相机到LiDAR的外参矩阵，可以将点从相机坐标系转到LiDAR坐标系。
- **图像坐标系**
  图像坐标是以相机主点（即相机光轴与图像平面的交点，一般位于图像平面中心）为原点，x 轴水平向右，y 轴水平向下的二维坐标系。相机内参可以实现从相机坐标到图像坐标的投影。
- **定位系统**
  利用GPS/IMU等定位和惯性系统，可实时获取自动驾驶车辆在全球定位系统的位置以及朝向角。为保护数据安全和研究方便，将真实世界定位系统得到的定位转换到虚拟世界坐标系下。

### 3.3 数据标注

- **数据抽样**
  当自动驾驶车辆经过路端设备所在路口时，车端路端传感器同步采集车路协同序列数据，从中抽取约100段时长20s的多模态序列数据，按照10HZ频率分别对车端和路端序列进行抽样得到离散帧。
- **3D标注**
  针对采样得到的路端和车端数据，利用2D&3D联合标注技术,标注图像和点云多模态数据中的道路障碍物目标的2D和3D框，同时标注障碍物类别、遮挡和截断等信息。其中DAIR-V2X的3D标注采用LiDAR坐标系。
  ①**障碍物类别**：一共15类，包括行人、机动车等，其中带略(Ignore)表示目标像素值小于15x15或者遮挡部分大于4/5，OtherIgnore表示非人车目标像素值小于15x15或者遮挡部分大于4/5。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/f3b695a139ab4550a7343cb7cf823ec5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZCM5a2m5p2l5ZWm,size_20,color_FFFFFF,t_70,g_se,x_16)
  ②**障碍物截断**：从[0, 1, 2]中取值，分别表示不截断、横向截断、纵向截断
  ③**障碍物遮挡**：从[0, 1, 2]中取值，分别表示不遮挡、0%～50%遮挡，50%～100%遮挡
  ④**alpha**：观察者视角，范围在[-pi, pi]
  ⑤**2D box**：图像中2D bounding box框
  ⑥**3D box**：3D bounding box，车端基于 LiDAR坐标系，路端基于虚拟LiDAR坐标系；包括 (height, width, length, x_loc, y_loc, z_loc) ，以米为单位；包括 (rotation_y) ，表示障碍物绕Y轴旋转角度
- **车路协同3D标注**
  基于车端和路端标注数据，以车端点云为协同标注时间基准，得到如下车路协同标注结果：
  ①**车端3D标注**：以车端3D标注作为车路融合感知结果的基准；
  ②**相同时间戳的车路目标结果融合**：选择车端和路端数据时间差小于10ms作为数据对，并对该车端和路端标注结果投影到相同虚拟世界坐标系进行结果融合。

### 3.4 数据文件结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/bddd2824c7cb4e2fa04c9d530799bf01.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZCM5a2m5p2l5ZWm,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
![在这里插入图片描述](https://img-blog.csdnimg.cn/8af1a099fa6448ae857c598c1fab4f6f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5ZCM5a2m5p2l5ZWm,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

> **DAIR-V2X官网网址：https://thudair.baai.ac.cn/index**