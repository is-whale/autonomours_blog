- [ADAS/AD控制器模块开发06 - 高精地图与自动驾驶_我爱露营车的博客-CSDN博客](https://blog.csdn.net/Maple_Lu1986/article/details/82666045?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-82666045-blog-106592197.pc_relevant_multi_platform_whitelistv3&spm=1001.2101.3001.4242.1&utm_relevant_index=3)

## **一、有HD-Map之前的ADAS是如何实现车辆控制的？**

在高精地图加持之前的ADAS系统，功能一般是由感知+数据处理+系统[状态机](https://so.csdn.net/so/search?q=状态机&spm=1001.2101.3001.7020)+PID控制器（或LQR控制器）组成的。拿智能前视摄像头模块实现LKA功能为例，按照dataflow来梳理：

1. 通过镜头和COMS图像传感器来获取单帧图片；
2. 按照感兴趣区域（ROI，*region-of-interest*）裁剪图片；
3. 图片经过灰度化、二值化，获得车道线的Lane_marklet（车道线标记小段，如图1）；
4. 通过霍夫变换，将marklet拟合成Lane_segments（车道线段，如图2）；
5. 将Lane_segments的位置从图片坐标系转换为世界坐标系；
6. 对Lane_segments进行合并，合并不了的删除掉；
7. 从整车CAN上抓取vehicle speed、yawrate等信号，用于计算三阶车道线模型，获得车道线的Lane_geometry。所谓的三阶车道线模型，即**Lateral Offset = a0 + a1\*x + a2\*x2+ a3\*x3 ; 其中，x - longitudinal range x X向参考距离；该距离x=V(车速) \* Tpreview(sec) , T 代表0.5-1秒之间的一个标定值。a0 - 当前Y向位置； a1 - Y向速度； a2 - 曲率；a3 - 曲率变化率。**
8. 通过卡尔曼滤波器，跟踪并更新车道线的Lane_geometry（车道几何线，用于抽象车道线，如图3）的几何形状；
9. 通过霍夫变换的信心值和车道线跟踪器的信心值，获得车道线的信心值；
10. 将车道线信息和来自车辆CAN上的其他信息（车速、YawRate、steer angle等）传给LKA功能；
11. LKA对各种输入信号进行数据处理，例如滤波、判断等；
12. 计算可以直接被LKA状态机使用的判断信号，如TTLC（time to lane crossing）、Lane position、车轮外缘到车道线的距离、Lane change、Road status（路面曲率等）、方向盘转角的inhibit信号、油门踏板的inhibit信号、刹车踏板的inhibit信号等等；
13. LKA主状态机进行LKA状态控制，状态包括：fault、off、standby、enabled、intervention left、intervention right、warning left、warning right等状态，如图6；
14. 利用前馈PID控制器，通过引入主状态机的状态信号，以及横向速度、距离车道线横向距离、heading angle、横向加速度、路面曲率等参数，建立基于车辆运动状态和路面状态的前馈PID控制模型，计算转向扭矩。

![img](https://pic2.zhimg.com/80/v2-bb9cbacfe1e36edbad8217983f85f939_hd.jpg)

图1 Lane Marklet 车道线标记小段，黄色

 

![img](https://pic2.zhimg.com/80/v2-9f5b1c61c20fa82124a21aedb1caf845_hd.jpg)

图2 Lane Segments 车道线段，红色

 

![img](https://pic3.zhimg.com/80/v2-b1213651cd90067ac77335fe6eccd786_hd.jpg)

图3 Lane Geometry 车道几何线，绿色

 

![img](https://pic1.zhimg.com/80/v2-9afaa5ce8ab2dfc0219ee46c39ca5928_hd.jpg)

图4 车道线检测算法框图

 

![img](https://pic2.zhimg.com/80/v2-274ece0200f9e94396fbfaea03181171_hd.jpg)

图5 LKA功能架构

 

![img](https://pic4.zhimg.com/80/v2-1b3b802896378b4777e789193684d0d3_hd.jpg)

图6 LKA主状态机

通过上述描述，可以悉知，在ADAS功能中，根本没有planning模块，车辆控制完全没先验轨迹（如图7的绿色轨迹段）的存在，控制全靠计算车辆与两个车道线之间的动态关系，利用[PID控制](https://so.csdn.net/so/search?q=PID控制&spm=1001.2101.3001.7020)器直接生成控制扭矩，来控制车辆的状态。而AD领域的车辆控制，是有先验轨迹的存在的，所有的精华都在先验轨迹的生成，只要计算出先验轨迹，车辆的真正控制非常简单，例如按照先验轨迹分解出车辆的速度-转角控制量，然后速度再分解成加速度、减速度。加速度对应油门，减速度对应刹车，转角对应方向盘转角，车辆就可以按照先验轨迹作为pilot，最大程度的拟合先验轨迹行驶。这也就是所谓的ROS机器人技术路线（ROS，Robot Operating System 机器人操作系统）。这条“先验轨迹”，其实就是像工厂里的物料小车一样，只不过工厂的物料小车是按照路面上事先贴好的磁性轨迹行驶的（如图7），而智能驾驶车的先验轨迹是存在于planing算法模块中的。

![img](https://pic3.zhimg.com/80/v2-628f33790a51a1438a0b0dadfe9cdc62_hd.jpg)

图7 “先验轨迹”trajectory

 

![img](https://pic4.zhimg.com/80/v2-2ccd1af4e138f4bbbd9307ee494f1347_hd.jpg)

图8 物料小车的磁性轨迹

而利用PID控制器这种粗暴的控制方式有很大的缺点。第一，算法与功能落地（即在车上的匹配）之间，有个巨大的、技术逻辑上的“模糊地带”，这个地带没有明显的符合逻辑的方法可以帮助你顺利的将算法使用在目标车型上（例如无法通过准确的仿真来测试效果），只能靠工程实践（也就是大量测试、标定、试参数）来缝合这个模糊地带。这就是传说中的“标定”！为什么汽车行业有大量的标定工程师？例如，发动机标定工程师、安全气囊控制器算法标定工程师、ADAS feature标定工程师，就是因为功能在车辆上的“落地”应用，需要大量的功能标定工作，说白了就是各种试参数，例如凑PID参数，凑标定系数，有时候完全就是在硬标。第二，由于标定环节的存在，为了能够更加准确的评判标定的效果和功能的性能（performance），只能设置更加复杂、巨大的测试验证矩阵来cover住这种“稀里糊涂”的功能匹配方式。

## **二、有了HD-Map之后的AD是如何进行车辆控制的？**

首先，先贴出目前主流的高精地图的数据格式，和包含的车道信息。

```go
typedef enum _GUIDETYPE
{
    GUIDE_CAR_POS = 0, //车辆位置
    GUIDE_LINE,
    GUIDE_REGION,
    GUIDE_OBJS,
    GUIDE_MANEUVER,
 
    GUIDE_MAX = 0xFFFFFFFF
} GUIDETYPE;
 
typedef struct _DiffPosition
{
    int8        x;                 //经纬度^8后相对于上一个点的差值，第一个点存储相对于指定位置的差值
    int8        y;
    int8        z;
    int16       slope;              //Value: [-9000,9000]，Unit: 0.01 degree
    int16       superelev;          //如上
    int16       heading;            //Value: [0,36000]，Unit: 0.01 degree, 北顺
    int16       curvature;          //Value: [-10000,10000],Unit: 0.0001/m
                                    //For example, 0.0123/m 表示为 Curvature =123
                                    //- for right turn; + for left turn;
                                    //Right or left is based on the link positive direction
} DiffPosition;
 
typedef struct _GuideLaneCenterline // 分段内的单条引导线，均为所属分段的车道中心线
{
    uint32  laneType;               // 当前引导线所属车道属性
                                    // bitmap, from right to left
                                    // 1:Regular Lane
                                    // 3:Acceleration Lane
                                    // 4:Deceleration Lane
                                    // 3 and 4: Compound lane
                                    // 5:HOV Lane
                                    // 7:Slow lane
                                    // 8:Passing/Overtaking lane
                                    // 9: Hard shoulder lane (no geometry) 10:Truck parking lane
                                    // 11:Regulated access lane
                                    // 17: Soft shoulder lane(no geometry)
                                    // 18: Emergency parking strip(nogeometry)
                                    // 19:Bus lane
                                    // 20:Bicycle lane (no geometry) 21:Turn Lane
                                    // 22:Reversible lane
                                    // 23:Centre turn lane
                                    // 24:Truck escape ramp
 
    uint8 laneSeq;                  // lane sequence number of cur link,
                                    // starts from 1 with the left most non-transition lane,
                                    // and +1 each time from the left to right according to the travel direction
 
    uint8 laneArrowType;            // 当前引导线所属车道上箭头类型
                                    // 0  Not applicable 没有箭头
                                    // 1 Forward
                                    // 2 Right
                                    // 3 Right and forward
                                    // 4 Left
                                    // 5 Left and forward
 
    uint8 lMarkingType;             // 当前引导线左边marking类型
                                    // 0 Not investigated 10 Single Dashed
                                    // 12 Short Thick Dashed
                                    // 13 Double Dashed
                                    // 20 Single Solid
                                    // 21 Double Solid
                                    // 30 Left Solid/Right Dashed
                                    // 31 Left Dashed/Right Solid
                                    // 32 Turn variable lane marking
                                    // 33 Single thick solid
                                    // 99 Virtual Marking
    uint8 rMarkingType;             // 同上
 
    uint16 swidth;                  // cm,车道开始位置车道宽度
    uint16 ewidth;                  // cm,车道结束位置车道宽度，若当前车道宽度没有变化swidth，ewidth相等
                                    // note:: 车道开始和结束位置宽度并非线性渐变
 
    uint16 speedMax;                // 速度单位：0.01m/s
    uint16 speedMin;                // 速度单位：0.01m/s
 
    int64 x;                        //当前引导线起始点坐标（GAUSS 单位 cm）
    int64 y;
    int32 z;
    int16 posNum;                   //当前引导单元中的position点个数（每个点的坐标存储和前一个点的差值）（包含起始点，其xyz差值为0）
    DiffPosition[posNum];           //当前引导线单元的差值position点的信息（相对于前一个点的位置差值）
} GuideLaneCenterline;
 
typedef struct _GuidelinesSection   //当前的引导线分段，包含>=1条引导线，分段内各个引导线按照GuidelineUnit存储
{
    int32 linkId;                   //
    int32 lenght;                   //当前路段长度，单位：cm
 
    uint8 isBridge;                 // 0 is not bridge
    uint8 isTunnel;                 // 0 is not tunnel
    uint8 TranType;                 // Transition road type
                                    // 0 means No transition zone
                                    // 1 Lane(s) opening/closing on one side- i.e. Exit, Entrance
                                    // 2 Lane(s) opening/closing in the middle– i.e. road split, road merge
                                    // 3 Only for Toll Booth area. Toll booth entrance and toll booth exit are both transition road.
                                    //   For this section of road, Lane number or Lane Width is not applicable
                                    // 4 Road width widening or narrowing or no change, but all lanes change simultaneously.
    uint16 roadForm;                // 0 Not attributed
                                    // 1 JCT(Motorway Junction)
                                    // 21 Entrance Ramp
                                    // 22 Exit Ramp
 
 
    int16 lineNum;                  //当前分段包含引导线总数，>=1，引导线按照从左到右顺序依次存储
    uint16 topoSuccessor[lineNum];  //当前分段中各个引导线和下一个分段的拓扑连接关系，现在认为连接处为自然平滑连接关系
 00000001
    uint16 topoPredecessor[lineNum];//当前分段中各个引导线和上一个分段的拓扑连接关系，其他同上
 
    GuideLaneCenterline[lineNum];   //当前分段的单条引导线存储数组，引导线Index从0开始递增至lineNum-1
} GuidelinesSection;
 
typedef struct _Guidelines                  // **引导线总结构体**
{
    uint8 sectionNum;                               //当前输出引导线分段总数，各个分段按照脱出顺序从自车位置开始向前依次存储
    GuidelinesSection subSectionLines[sectionNum];  //分段引导线信息
 
} Guidelines;
 
// 随后点为相对于前一个点的GAUSS坐标差值 cm
} RouteMarking;
 
 
typedef struct _GuideSectionTopo    // 前后两条分段之间的拓扑链接关系
{
    int16 inSegIdx;                 // 脱入分段在GuideRegion结构体中存储数组中的Index
    int16 outSegIdx;                // 脱出分段在GuideRegion结构体中存储数组中的Index
    int16 inSegLaneNum;             // 脱入分段上车道数
    int16 outSegLaneNum;            // 脱出分段上车道数
    uint16 successorLane[inSegLaneNum];  // 脱入分段各个lane对应脱出分段上的连接的lane在其数字中的index
    uint16 segType;                 // guide segment类型信息
                                    // 0: 表示当前自车车后沿路线上的分段
                                    // 1: 表示当前自车车前沿路线上的分段
                                    // 2: 表示当前自车前方第一个汇入点上其他非沿路线汇入分段
                                    // 3: 表示当前自车前方第一个分叉点上其他非沿路线脱出分段
} GuideSectionTopo;
 
typedef struct _GuideRegion
{
    int16 sectionNum;                       //当前REGION中的分段个数
    int32 dsitance[sectionNum];             //各个分段起始位置到当前自车位置沿link线距离，单位cm
    RouteSection routeSection[sectionNum];  //存储分段信息
 
    int16 topoNum;                      // 前后两条分段之间的拓扑链接关系个数
    GuideSectionTopo topos[topoNum];    // 前后两条分段之间的拓扑链接关系内容
} GuideRegion;
 
typedef enum _ADManeuverType
{
    ADMT_none = 0,
    ADMT_tollStation,               //收费站
    ADMT_serviceArea,               //服务区
    ADMT_notAllowOvertaking,        //禁止超车
    ADMT_allowOvertaking,           //允许超车
    ADMT_waypoints,                 //途经点
    ADMT_endpoints,                 //终点
    ADMT_enterRamp,                 //进入匝道
    ADMT_leaveRamp,                 //驶离匝道
    ADMT_rampImport,                //岔口交汇
    ADMT_overpass,                  //立交桥
    ADMT_accelerationLane,          //加速车道
    ADMT_decelerationLane,          //减速车道
    ADMT_enterMainRoad,             //进入主路
    ADMT_distanceAlongRoadTips,     //沿路行驶距离提示
    ADMT_urgencyParkingStrip,       //紧急停车带
    ADMT_tunnelEntrance,            //隧道入口
    ADMT_tunnelExport,              //隧道出口
    ADMT_leftNarrowed,              //左侧变窄
    ADMT_rightNarrowed,             //右侧变窄
    ADMT_bothSidesNarrowed,         //两侧变窄
    ADMT_narrowBridge,              //窄桥
    //todo 代码中出现JCT，但是在这里没有出现
 
    ADMT_ComplexIntersection        //复合路口
} ADManeuverType;
 
 
 
// 只包含lane id的link结构，用于表述maneuver上的lanetopo关系
struct LanePath
{
    uint32_t laneNum;
    LaneID*  pathResult;
};
 
typedef struct _ADManeuver
{
    ManeuverType maneuverType;  //
    int distance;               // maneuver点到当前自车距离, cm
    Point64 pos;                // maneuver起始点绝对坐标
    int64 predecessorLinkId;    // maneuver托入link id
 
    int speed;                  // maneuver点之后的建议车速     0.01m/s
                                // ManeuverType_enterRamp ManeuverType_tunnelEntrance ManeuverType_distanceAlongRoadTips 为当前特殊lane上的建议速度
                                // ManeuverType_leaveRamp ManeuverType_tunnelExport 为当前maneuver点之后的建议车速
                                // ManeuverType_accelerationLane ManeuverType_decelerationLane 为当前加减速车道终点处的最低目标车速
 
    int length;                 // maneuver为特殊路段时，特殊路段长度 cm，无效值-1， 以下类型有效
                                // ManeuverType_accelerationLane  ManeuverType_decelerationLane
                                // ManeuverType_urgencyParkingStrip ManeuverType_tunnelEntrance
                                // ManeuverType_overpass // 天桥在当前行驶路段上的投影长度
 
    int overpassHeight;         // ManeuverType_overpass 天桥距离当前路线所属道路路面高度 cm 无效值 -1
 
    uint8 laneNumber;           // maneuver所在位置脱出todo:主路上车道总数
    uint8 laneSeqNum;           // maneuver所对应特殊车道在主路上的lane seqNum，对以下类型有效
                                // ManeuverType_accelerationLane ManeuverType_decelerationLane
                                // ManeuverType_enterMainRoad ManeuverType_urgencyParkingStrip
    uint16 suggestLanes;        // bitmap 当前自车位置建议车道
                                // 遇到ManeuverType_distanceAlongRoadTips ManeuverType_narrowRoad ADMT_enterRamp
                                // 等类型时候车辆便要开始为其做相应变道准备
 
    ADManeuverUnionAttribute unionAttribute;    //互斥属性存储在一个联合体中
 
    //todo todo todo
    uint8_t    lanePathNum;     // 从自车位置到maneuver所有LanePath number
    LanePath*  paths;           // 从自车到maneuver位置所有lane的topo 关系
} ADManeuver;
```

根据以上高精地图的结构体可以看出，拥有如此丰富的地图信息，要进行车辆控制，简直就像开了挂一样啊。假如车辆能够动态掌握自车前1公里和后0.5公里的高精地图信息的话（也有设置成前500米后200米的），完全可以站在“上帝视角”提前计算出预先需要行驶的轨迹，比如在车辆需要下高架桥的场景中，车辆自己会根据高精地图信息提前计算出一个先验轨迹，可能会是这样的：先按照本车道中心线行驶200米，然后向右换道；由于通过感知和预测，知道右侧有一辆车，在前方50m的位置正以较低速度行驶，所以换到右侧车道时，要在右侧车道行驶50m后，继续向右换道，换到驶离高架路的辅道上，然后在辅路上行驶600m，下高架路。

当然，以上纯属是为了解释先验轨迹的概念，进行了夸张描述。事实上，一般受限于感知传感器的能力（前向毫米波雷达探测距离约170-220米，64线威力登激光雷达探测距离100米左右），一般先验轨迹的长度设置在200米左右就足够了，因为即便设置的更长，由于障碍物检测、跟踪无法超过200米的距离，因此将轨迹规划距离设置太长也没有太大意义。

最后提一点，先验轨迹的规划，不仅只考虑车道信息，也要考虑摄像头、毫米波雷达、激光雷达等传感器感知到的车道线、交通标志、车辆、行人、骑自行车的人等等障碍物信息和道路信息。障碍物信息包括体积信息、位置信息、运动状态信息，和障碍物的轨迹预测信息。综合以上所有信息，才能规划出理想的、不会发生交通事故的 、不会违反道路法律法规的、安全的、舒适的先验轨迹！

PS：所谓“先验轨迹”的说法，完全是自己杜撰出来的概念，其实真正搞自动驾驶的人从没这种说法，就是planning规划出来的trajectory。只不过我在对比了ADAS与AD的技术路线之后，感觉“先验轨迹”这个词更能说明两者之间的差异。本质上，在TJA和基于车道居中的LKA功能（Lane Centering）也有类似的轨迹概念，但是谈不上“先验”。更多的是基于YawRate拟合出的车辆历史行驶轨迹，或者直接用车道中间线作为默认的控制基准（但只是静态的，通过识别的左右车道线算出车道中间线，无法进行变道轨迹规划）。

一般地，在高精地图中，将车道中间线作为默认的先验轨迹线，变道之后，新车道的中心线就会成为新的先验轨迹线。以车道中心线为基准，当遇到障碍物时，再在该基准上，进行新的路径规划计算，不论是道内跟车、道内躲避超车、换道超车行驶还是换道躲避等。

## **三、车机（中控大屏）的导航是如何与智能驾驶的高精地图进行交互的？**

先介绍下导航地图与高精地图的区别。

导航地图，英文缩写SD-Map，保存的是道路级别的信息，注意英文名road level info；导航地图的路径规划，叫做全局路径规划，注意英文名Routing（路由）；

高精地图，应为缩写HD-Map，保存的是车道级别的信息，注意英文名Lane level info；高精地图的路径规划，叫做局部路径规划，注意英文名Planning（规划）；

![img](https://pic3.zhimg.com/80/v2-8ff17d5a0e85b89c02f8395035d640a2_hd.jpg)

图9 导航地图与高精地图的Mapping关系

如图8所示，实际上，无论是SD-Map还是HD-Map，都有一个相同的概念（或者叫参数），就是Road Segments（也有叫做Road Section）的，本文称作路段，路段的含义是：按照道路属性是否发生变化将连续的道路分割成路段。所谓道路属性发生了变化，例如车道线数量增加或减少了、右侧一个车道分叉了（比如下高架路的辅道，存在道路分离点），如图10所示。

![img](https://pic4.zhimg.com/80/v2-9e162bdb12cc1abb7d5fc23befdbf1a3_hd.jpg)

图10 路段的定义

有了路段这个属性，且路段属性的唯一性。可以将SD-Map的路段与HD-Map的路段进行一对一的匹配。也就是说，当进行SD-Map的Routing规划时，将整个routing的路段编号和信息做成一个数组，发送给HD-Map，HD-Map根据编号来依次提取road segments中的lane info，用于planning规划，即先验轨迹的规划