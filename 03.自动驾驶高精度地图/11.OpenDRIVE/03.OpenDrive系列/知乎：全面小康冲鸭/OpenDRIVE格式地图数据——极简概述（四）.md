- [OpenDRIVE格式地图数据——极简概述（四） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/388825359)

## 一、解析前的数据读取

首先，我们知道OpenDRVIE格式的地图文件是以XML格式保存的其文件名后缀是.xodr。那么我们就需要一个能够解析XML文件的功能包，由于我所有的解析工作都是用C++写的，所以这里选用了[开源库tinyXML2](https://link.zhihu.com/?target=https%3A//github.com/leethomason/tinyxml2)，相对于tinyXML1，其使用分配更少的内存，速度更快，想要更多的了解请去看上面的Github链接。

使用方法也比较简单，只需要在我们的项目文件系统中添加tinyxml2.h和tinyxml2.cpp这两个文件，然后在使用的地方引用就好。这里给出几个会用到的tinyXML2使用方法：

对于函数形参，返回值，调用方式等一些比较细节的讲解请查阅别的资料，我只结合实例说一下函数的用途，我认为这些对于我们的XML文件读取工作已经足够了。

1、首先肯定是实例化一个XMLDocument类的对象。

```cpp
tinyxml2::XMLDocument document;
```

2、我们需要加载XML文件，并通过返回值判断是否成功。

```cpp
std::string filename = "borregasave.xodr";
if (document.LoadFile(file_name.c_str()) != tinyxml2::XML_SUCCESS) {
    ERROR("load failed");    // 这个ERROR是我自己定义的函数,下同
}
```

3、获得XML的根元素，后面就用这个根元素来访问XML中的各个元素。

```cpp
const tinyxml2::XMLElement* root_node = document.RootElement();
```

4、找到该元素（可以是任一元素）下面的第一个具名子元素。

```cpp
auto road_node = xml_node.FirstChildElement("road");
if (!road_node) {
    ERROR("xml data missing road node");
}
```

5、找到子元素的下一个兄弟元素，往往和4中的函数一起使用，用于遍历一个元素下的所有同名子元素。

```cpp
road_node = road_node->NextSiblingElement("road");
```

6、查询元素中的某个属性，将该属性值存入到std::string类型的对象。

```cpp
std::string road_id = "";    
road_node->QueryStringAttribute("id", &road.id);
```

7、查询元素中的某个属性，将该属性值存入到double类型的对象。

```cpp
double road_length = 0;    
road_node->QueryDoubleAttribute("length", &road.length);
```

OK，以上大概就是从XML中加载元素和属性数据会用到的函数，其实我们简单的思考一下，无非就是要从.xodr文件中获得我们需要的“元素”和“元素的各种属性”。忘了“元素”和“属性”区分的请看下面这张图，红色的是元素，绿色的是该元素具有的属性。

![img](https://pic3.zhimg.com/80/v2-91547756af2ce8a5dfee5d4ff37c2d1a_720w.jpg)

第一部分的最后简单提一下，在我做的OpenDRIVE地图解析工作中，数据读取部分我只获取了**“道路”“车道”和“交叉口”三种元素（及其属性）和其内部对应的相关子元素（及其属性）**，这些暂时足够我的使用，如果你想要读取更多的内容，比如“信号灯”，“道路表面”，甚至是“桥梁”，“高程”等，都可以调用上面说的tinyXML2函数来读取数据。

下面给出读取后的数据存储方式（有些类型是我自己定义的，不影响理解）：

```cpp
/* 道路信息 */
struct Road {
	std::string id;	// 道路ID
	std::string junction_id;	// 道路所属的交叉口ID
	double length;	// 道路长度
	RoadLink link;		// 道路连接
	std::vector<Geometry> reference_line;	// 道路中心线的几何形状
	std::vector<Point> reference_line_points;	// 道路中心线离散坐标点（计算获得）
	std::vector<RoadType> type;	// 道路类型
	std::vector<RoadSection> road_sections;	// 道路段
};
/* 车道信息 */
struct Lane {
	std::string road_id = "NONE";	// 车道所属的道路ID
	std::string road_section = "NONE";	// 车道所属的道路段ID
	std::string id = "NONE";	// 车道的本地ID
	std::string predecessor_id = "NONE";	// 车道连接—前驱
	std::string successor_id = "NONE";	// 车道连接—后继
	std::string type;	// 车道类型
	std::string lane_change;	// 允许的变道类型
	std::vector<LaneWidth> width;	// 车道宽度参数
	std::vector<Point> center_line_points;	// 车道中心线离散坐标点（计算获得）
	double length = 0;	// 车道长度
};
/* 交叉口信息 */
struct Junction {
	std::string id;	// 交叉口ID
	std::vector<Connection> connection;	// 交叉口内的道路连接
};

// 同时先给出以下结构
/* 笛卡尔空间XYZ坐标系下表示的位置 */
typedef struct MXYZ {
	double x;
	double y;
	double z; 
}MXYZ;

/* 基于道路参考线的Frenet坐标系下表示的位置 */
typedef struct MSLZ {
	MLaneUId lane_uid;
	MRoadS s;
	double l;
	double z;
}MSLZ;
```

## 二、都解析了哪些内容

上面的数据读取工作只是从.xodr文件中读取一些会用到的“元素”和“属性”信息并保存它们，并没有做任何的计算。例如，我们都知道OpenDRIVE中有很多**用三次多项式或参数形式的三次多项式表示的车道宽度信息，道路参考线信息等，我们在读取的时候也只是记录下来这些三次多项式的参数**（如下），用作后面的解析与计算。

```cpp
/* 三次多项式 */
struct Poly3 {
	double a;
	double b;
	double c;
	double d;
};
/* 参数形式的三次多项式 */
struct ParamPoly3 {
	double aU = 0;
	double bU = 0;
	double cU = 0;
	double dU = 0;
	double aV = 0;
	double bV = 0;
	double cV = 0;
	double dV = 0;
};
```

下面这些函数，就是一些基于高精地图的常用方法的实现方式，比如“坐标系的转换”，“全局路由的发现”，“查询车道信息”，“查询限速”，“获得车道中心线参考点”等等，在自动驾驶中非常的实用，就像在文章开始时说的那样，你不必完完全全看懂所有的函数实现，而是**对这种实现方式，代码层面的实现计算方法有一定的认识**，就已经足够了，这样当你真正会用到这些东西的时候，你只需要略加思考，就可以着手写出来代码实现了。

当然，我还是用我拙劣文字表达能力，尽可能的讲清楚一点。有些函数实现比较简单，我们就一笔带过。

下面开始。

**1、通过采样获得离散化的道路中心线坐标点 & 车道中心线坐标点**

```cpp
void OpenDriveParse::ParseToDiscretePoints()
```

该函数在我的实现里是不允许外部调用的，因为该函数获得的离散点会辅助后面的函数，不作为一个对外接口。

先看我画的陋图，比较直观，该函数的目标就是求出这些所有的道路中心线采样点和车道中心线采样点。

![img](https://pic2.zhimg.com/80/v2-9dbb8702fbce92bec95fcc54d52c1e85_720w.jpg)

- 对于道路中心线，我们从<road>元素中的<planView>元素里可以获得道路中心线的形状及数学表示，进而可以计算出道路中心线上离散采样点的坐标。

（1）下图是一个“**Line**”型的道路中心线，那么我们根据<geometry>中的属性值完全能够计算出离散点的全局XY坐标（这里暂不考虑Z轴，下同）。也即是我们有了中心线“起点”的XY坐标和朝向角hdg，又知道中心线的总长度，那么设置不同的采样间隔，即可获得采样后的离散坐标点。

![img](https://pic4.zhimg.com/80/v2-7804c5dfb2ec9d03581a9c5cb96b8bdb_720w.png)

再看一下代码是如何实现的：

```cpp
double delta_s = 0.2;   // 采样间隔
int sample_num = int(geometry.length / delta_s);    // 采样的点数
for (int k = 0; k < sample_num + 1; k++)
{
    double x = geometry.x + (delta_s * k) * std::cos(geometry.hdg);
    double y = geometry.y + (delta_s * k) * std::sin(geometry.hdg);
    // 这里保存"x, y, hdg, s, l"
    road_reference_points.push_back(Point{ x, y, geometry.hdg, s, 0 });

    s += delta_s; 
}
```

（2）下图是一个“**Parampoly3**”型的道路中心线，同样我们可以根据<geometry>中的属性值来计算离散坐标点，只不过较为麻烦。

首先我们应当知道这是一个“参数”“局部”三次多项式表示的道路中心线，那么第一步应当通过采样求出以当前<geometry>起点为原点的局部离散坐标点。

第二步将所有的局部坐标点转换回全局坐标点。

注意：这里的“参数”三次多项式是指三次多项式自变量是s值或归一化后的s值，函数值分别是X和Y（如有不懂，请查看我之前写过的几篇同名文章，或更深入的学习[官方文档](https://link.zhihu.com/?target=https%3A//www.asam.net/index.php%3FeID%3DdumpFile%26t%3Df%26f%3D4089%26token%3Ddeea5d707e2d0edeeb4fccd544a973de4bc46a09)，下同）。

![img](https://pic4.zhimg.com/80/v2-6070902736f6a4198a11d1804ffdad7f_720w.png)

再看一下代码是如何实现的，比较直观：

```cpp
    ParamPoly3 parampoly3 = geometry.param_poly3;
    double delta_s = 0.2;   // 采样间隔
    int sample_num = int(geometry.length / delta_s);    // 采样的点数
    double delta_normalize_s = 1.0 / sample_num;    
    for (int k = 0; k < sample_num + 1; k++)
    {
        double t = delta_normalize_s * k;       // t属于[0，1]
        // 局部坐标
        double u = parampoly3.aU + parampoly3.bU * t + parampoly3.cU * t * t + parampoly3.dU * t * t * t;
        double v = parampoly3.aV + parampoly3.bV * t + parampoly3.cV * t * t + parampoly3.dV * t * t * t;
        double du_dt = parampoly3.bU + 2 * parampoly3.cU * t + 3 * parampoly3.dU * t * t;
        double dv_dt = parampoly3.bV + 2 * parampoly3.cV * t + 3 * parampoly3.dV * t * t;
        double hdg = std::atan2(dv_dt, du_dt);  // 通过一阶导数求朝向角

        Point front = Point{ geometry.x, geometry.y, geometry.hdg, 0, 0 };      // 当前起点坐标
        Point colc = Point{ u, v, hdg, 0, 0 };   // 局部坐标
        Point next;   // 全局坐标
        Local_To_Global(front, colc, next);     // 局部坐标转换回全局坐标
        road_reference_points.push_back(Point{ next.x, next.y, next.hdg, s, 0 });

        s += delta_s;
    }
```

（3）下图是一个“**Arc**”型的道路中心线，

![img](data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='1259' height='42'></svg>)

这个实现较为麻烦，暂时还没有想到便捷的方法。

在我的实现中，方法为：

1、可以看出其曲率是固定的，且不为零，那么我们根据曲率和半径互为倒数的关系计算出圆的半径，然后根据起点全局坐标计算出圆心的全局坐标。同时，我们可以根据弧长（length）等于半径乘以弧度求出该段弧长对应的圆心角。

2、以圆心坐标为局部原点，起点的朝向为局部坐标系的x轴朝向，建立局部坐标系。

3、对圆心角进行间隔采样，利用X=Rcos(θ）和 Y=Rsin(θ）求出圆弧上的点在以圆心为原点的局部坐标系下的坐标。

4、把所有的局部坐标转换回全局坐标。

代码暂时不贴，放一张图：

![img](data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='1037' height='872'></svg>)

- 对于车道中心线，我们从<lane>元素里的<width>元素里可以获得车道的宽度的数学表示，进而结合前面算出来的道路中心线离散采样点可以推算出车道中心线上离散采样点的坐标。（OpenDRIVE官方文档里说也有另一种表示方法，即车道边界用<lane>元素中的<border>元素来表示，由于我暂时没有遇到这种表示的，暂不考虑。）

如下图所示，是一连串的车道<width>

![img](https://pic2.zhimg.com/80/v2-ad86c1a8d23370d7132b4aa9b5c92d55_720w.png)

我们第一步要做的是获得车道中心线某点处的车道宽度，从<width>中的属性值可以看出，对于某一条目，只要给出s值在当前条目下的偏移量（s - sOffset），代入到三次多项式中即可计算出当前s点的车道宽度。代码如下（这里注意s_section是当前车道所在的车道段的偏移量，s是沿着道路参考线的绝对偏移量）：

```cpp
    int idx_poly3 = 0;
    // 首先先找到当前s值在哪一个条目里面
    for (int k = 0; k < width.size(); k++)
    {
        if (k == width.size() - 1)
        {
            idx_poly3 = k;
            break;
        }
        if (s >= width[k].sOffset)
            continue;
        idx_poly3 = k - 1;
        break;
    }
    double sOffset = width[idx_poly3].sOffset;  // 该条目的起始偏移量
    Poly3 poly3 = width[idx_poly3].poly3;
    double ds = s - s_section - sOffset;    // 相对于该条目的偏移量，用于计算
    double calc_width = poly3.a + poly3.b * ds + poly3.c * ds * ds + poly3.d * ds * ds * ds;
```

我们第二步要借助于该s值处的道路参考线离散点和该s值处的车道宽度，计算出该s值处的车道线离散点坐标。看上面的图比较直观，这里按照我的理解，从Frenet坐标系来看，相同s值处的道路Frenet坐标点和车道Frenet坐标点只有l方向的值不同，而车道l方向的值可以通过车道宽度计算出来，进而可以算出车道在该s点的全局坐标，

如图：

![img](https://pic3.zhimg.com/80/v2-fdd438f30389a50f921c96489e89ccd2_720w.jpg)

代码如下：

```cpp
    const double x = rpoint.x - l * std::sin(rpoint.hdg);
    const double y = rpoint.y + l * std::cos(rpoint.hdg);
    return Point{ x, y };
```

**2、创建车道节点，也即是为每一个车道提取、保存连接关系（前驱车道、后继车道以及平行车道）**

```cpp
void OpenDriveParse::CreateLaneNodes()
```

看图~

![img](data:image/svg+xml;utf8,<svg xmlns='http://www.w3.org/2000/svg' width='1894' height='1296'></svg>)

该方法的主要作用是为了后面使用Astar算法（不了解Astar的去找一下资料吧）做全局路由，然而与一般的在栅格地图上做Astar算法不同的是，我们这里是道路网络。

借助于百度Apollo中的规划思想，**我们以每一条车道为一个节点，每一条车道的“同向车道”“前驱车道”“后继车道”都作为该车道的“邻居车道”**。这样，我们的节点与节点之间的连接关系也就建立起来了，进而可以使用Astar算法来规划全局路由，那么如果规划成功，路由的结果即是“从起点车道到终点车道，具有连接关系的车道组”。

节点的创建与节点连接关系的建立从代码层面上来说比较简单，代码不再给出。

如下图所示，**主要读取“道路前驱和后继”和“车道前驱和后继”，以及“交叉口中的连接”**，然后把他们存储起来即可，同时，我在代码中还做了重复连接关系的检查等。注意：暂时假设所有的“同向车道”都是可以变道的，所以所有的同向车道也都是自身车道的邻居。

![img](https://pic2.zhimg.com/80/v2-01599a35745fe9e10f6d875282322f55_720w.png)

道路前驱和后继

![img](https://pic1.zhimg.com/80/v2-53579c9286874664869adbaeb4bb7268_720w.jpg)

车道前驱和后继

![img](https://pic3.zhimg.com/80/v2-d4518bbd890f3a67155a316e36fec7a6_720w.jpg)

交叉口中的连接关系

**3、创建一个锚点**

```cpp
MAnchor OpenDriveParse::CreateAnchor(MAnchorId id, MSLZ pos)
```

该函数用于创建一个锚点，包括“该锚点的ID”，“该锚点的SLZ坐标”二者，其结构如下：

```cpp
/* 锚点结构体 */
typedef struct MAnchor
{
	MAnchorId anchor_id; // 用户自定义锚点ID
	MSLZ slz; // 锚点SLZ坐标
}MAnchor;
```

**4、创建一个车道全局ID**

```cpp
MLaneUId OpenDriveParse::CreateLaneUId(const MRoadId road_id, MSectionIndex section_index, MLaneLocalId lane_id)
```

该函数用于创建一个车道全局ID，包括“车道所在的道路ID”，“车道所在的道路段ID”，“车道在道路上的本地ID”三者，其结构如下：

```cpp
/* 车道的全局Id */
typedef struct MLaneUId {
	MRoadId road_id;
	MSectionIndex section_index;
	MLaneLocalId local_id;	// 在道路参考线左侧为正，右侧为负
}MLaneUId;
```

**5、创建一个 SLZ坐标**

```cpp
MSLZ OpenDriveParse::CreateSLZ(MLaneUId lane_uid, double s, double l)
```

该函数用于创建一个SLZ坐标，包括“该SLZ坐标所在的车道全局ID（见7）”“沿着道路参考线方向的s值”，“沿着垂直参考线方向的l值”，“z值”三者，其结构如下：

```cpp
/* 基于道路参考线的Frenet坐标系下表示的位置 */
typedef struct MSLZ {
	MLaneUId lane_uid;
	MRoadS s;
	double l;
	double z;
}MSLZ;
```

**6、规划路径函数**

```cpp
MErrorCode OpenDriveParse::MapPlanRoute(MAnchorArray anchor_list, MRoute* route)
```

该函数使用Astar算法，在我们之前创建的**“车道节点”和“车道连接关系”**中寻找一组**从起点车道到终点车道的具有连接关系的车道组**。

献丑了：

![img](https://pic2.zhimg.com/80/v2-bfdef876f072dd1e07fc102e4463f6c5_720w.jpg)

首先我们看一下锚点数组MAnchorArray的结构，包括“该锚点数组的长度”，“锚点数组”二者（MAnchor的结构见6）。

```cpp
/* MAnchor数组 */
typedef struct MAnchorArray
{
	uint32_t length; // 数组长度
	std::vector<MAnchor> array; 
}MAnchorArray;
```

然后，全局路由MRoute的结构如下，包括“车道数组”，“起点SLZ坐标”，“终点SLZ坐标”，“从起点到终点沿着参考线方向走过的总s值”：

```cpp
/* 路径信息，用于存储路径规划算法返回的路径信息 */
typedef struct MRoute
{
	MLaneUIdArray lane_uids;
	MSLZ begin;
	MSLZ end;
	MRouteS distance;
}MRoute;
```

那么我们的Astar算法也即是找到两个锚点（Anchor）之间的路由车道。对照基于栅格的Astar算法，本实现的Astar部分的代码如下（暂时假定同车道的g值为0，不同车道的g值为车道长度，启发式h值通过车道中心点欧氏距离的平方给出，具体这个Astar寻路算法的优化还暂时没有考虑好，更加感兴趣的请去看百度Apollo的全局规划代码）：

```cpp
    while(!OPEN.empty())
    {
        pair cur = OPEN.top();
        OPEN.pop();
        CLOSE.push_back(cur);
        if (cur.index == target_index) {
            find_road = true;
            break;
        }
        std::vector<node> nodes; // 该车道的所有邻居
        ...
        // 对该车道的所有邻居
        std::cout << "nodes size: " << nodes.size() << std::endl;
        for(int i = 0; i < nodes.size(); i++)
        {
            double new_cost = 0;
            if(nodes[i].type == "parall")
                new_cost = g[cur.index] + 0;
            if (nodes[i].type == "suc") {
                MLaneInfo lane_info;
                MErrorCode code = MapQueryLaneInfo(nodes[i].lane_uid, &lane_info);
                new_cost = g[cur.index] + lane_info.length;
            }
            if(g.find(nodes[i].index) == g.end())
                g[nodes[i].index] = INT_MAX;
            if(new_cost < g[nodes[i].index])
            {
                g[nodes[i].index] = new_cost;
                PARENT[nodes[i].index] = cur.index;
                OPEN.push(pair{nodes[i].lane_uid, nodes[i].index, f_value(g[nodes[i].index], nodes[i].lane_uid, target_laneuid, target_xyz)});
            }
            std::cout << "neighbor lane uid: " << nodes[i].lane_uid.road_id << " " << nodes[i].lane_uid.section_index
                << " " << nodes[i].lane_uid.local_id << " " << nodes[i].index << " " << f_value(g[nodes[i].index], nodes[i].lane_uid, target_laneuid, target_xyz) << std::endl;
        }
        std::cout << "quit " << OPEN.size() << std::endl;
    } //while
```

**7、查询车道的基本信息**

```cpp
MErrorCode OpenDriveParse::MapQueryLaneInfo(MLaneUId lane_uid, MLaneInfo* lane_info)
```

车道基本信息MLaneUId结构体如下，包括“车道全局ID”，“车道起始S值”，“车道终点S值”，“车道总长度”四者。

```cpp
/* 车道基本信息结构体 */
typedef struct MLaneInfo {
	MLaneUId lane_uid;
	MRoadS begin;
	MRoadS end;
	MRoadS length;
}MLaneInfo;
```

这个查询的实现也很简单，代码如下，基本就是从XML中的车道具有的属性值中获得。

```cpp
    lane_info->lane_uid = lane_uid;

    RoadSection section = roads[std::stoi(lane_uid.road_id)].road_sections[lane_uid.section_index];
    int lane_id = lane_uid.local_id;

    Lane lane;
    // 这里在存储的时候，为了区分左右车道做了分开存储
    if (lane_id > 0)
        lane = section.left_lanes[section.left_lanes.size() - lane_id];
    else
        lane = section.right_lanes[int(-lane_id - 1)];

    lane_info->length = lane.length;   
    lane_info->begin = section.s;
    lane_info->end = section.s + lane.length;
```

**8、查询车道上某一个位置的最大限速 km/h**

```cpp
MErrorCode OpenDriveParse::MapQueryLaneSpeedAt(MLaneUId lane_uid, MRoadS s, double* speed_limit)
```

查询某个位置上的限速，也是根据XML中限速属性来获得的，很简单不用计算（除非你要对速度的单位进行换算，不过我非常建议你在着手写时，既存储速度数值，也要存储速度单位，这总不是一件坏事情）。

从下图中可以看出来，如果<type>下面还有很多兄弟<type>的话，那么就应当根据当前s值来对应到某一个<type>中，然后获得对应的限速。（我的实现暂时只考虑道路限速，其实车道元素里面应该也有限速）

![img](https://pic4.zhimg.com/80/v2-756514a79eaa4fbdaa0b92e282c26c87_720w.png)

**9、查询车道上某一个位置的变道类型**

```cpp
MErrorCode OpenDriveParse::MapQueryLaneChangeTypeAt(MLaneUId lane_uid, MRoadS s, MLaneChangeType* lane_change_type)
```

查询某个位置上的变道类型，这里注意一点，和上面查询限速是比较类似的，我们都是查询当前位置沿着道路参考线方向某位置（s值）的限速/变道，都是不考虑横向偏移的，这一点从元素的属性中也能看出来，都是写的“s = ”。

如图所示，laneChange即表示变道类型，可以看出，对于图片上所示的车道，沿着参考线的方向，先允许左右都可变道，后不允许变道：

![img](https://pic4.zhimg.com/80/v2-31c91f91672443b913f89b5e25a82873_720w.png)

**10、计算得到车道左右边界线采样点**

```cpp
MErrorCode OpenDriveParse::MapQueryLaneBoundaries(MLaneUId lane_uid, double sampling_spacing, MSLZArray* left_boundary, MSLZArray* right_boundary)
```

车道左右边界线离散采样点的实现方式和上面的1中的车道中心线离散采样点的实现方式有些区别，在该实现中，最后获得的离散坐标点是用Frenet坐标系下的SLZ表示的。如下：

```cpp
/* MSLZ数组 */
typedef struct MSLZArray
{
	uint32_t length;
	std::vector<MSLZ> array;
}MSLZArray;
```

也即是按照S方向的采样间隔，使用某S值处的车道宽度计算出该点的L值，组成SLZ坐标（这里仍不考虑Z轴，假设Z轴坐标为0），不再做下一步的全局转换。

该函数参数中sampling_spacing代表采样间隔（下同）。

**11、计算得到车道中心线采样点**

```cpp
MErrorCode OpenDriveParse::MapCalcLaneCenterLine(MLaneUId lane_uid, double sampling_spacing, MSLZArray* result)
```

这里和上面的10几乎一样，不同的是这里是根据采样间隔来计算车道中心线采样点，最后的结果也是SLZ坐标（MSLZArray）。不再过多阐述。

**12、计算得到车道中心线的某一段（s1~s2）中心线采样点集**

```cpp
MErrorCode OpenDriveParse::MapCalcLaneCenterLineInterval(MLaneUId lane_uid, double s1, double s2, double sampling_spacing, MSLZArray* result)
```

这里是上面11的特例，只不过这里是计算**某一段（s1~s2）之间的车道中心线采样点**。方法相同，其实在我的代码实现中，函数11的计算最终会调用12来计算，整体的计算过程是在12里面的，如下：

```cpp
MErrorCode OpenDriveParse::MapCalcLaneCenterLine(MLaneUId lane_uid, double sampling_spacing, MSLZArray* result)
{
    MLaneInfo lane_info;
    MapQueryLaneInfo(lane_uid, &lane_info);

    return MapCalcLaneCenterLineInterval(lane_uid, lane_info.begin, lane_info.end, sampling_spacing, result);
}
```

函数参数中的s1是沿着道路参考线方向的采样起点s坐标，函数参数中的s2是沿着道路参考线方向的采样终点s坐标。

**13、查看道路所在的JunctionID**

```cpp
MErrorCode OpenDriveParse::MapQueryRoadJunctionId(const std::string road_id, std::string* junction_id)
```

这个函数的实现也很简单，看图我们就知道了， junction等于-1表示当前车道不在交叉路口内，否则即代表在junction属性值所对应的交叉路口内。

![img](https://pic4.zhimg.com/80/v2-b578429276862c706783437579cb5fdb_720w.png)

![img](https://pic4.zhimg.com/80/v2-254cf7044410ac9838febe0e19cb8bef_720w.png)

------

剩下的这几个，由于涉及到笛卡尔坐标系和Frenet坐标系的相互转换，其转换方式稍微有点麻烦，放在下一篇来写。

**14、XYZ 转 SLZ 坐标，优先搜索hint road附近的道路**

```text
MErrorCode OpenDriveParse::MapFindSLZ(MXYZ xyz, MLaneUId hint, MSLZ* slz)
```

**15、XYZ 转 SLZ 坐标，直接全局搜索**

```text
MErrorCode OpenDriveParse::MapFindSLZWithOutHInt(MXYZ xyz, MSLZ* slz)
```

**16、SLZ 转 XYZ 坐标**

```text
MErrorCode OpenDriveParse::MapCalcXYZ(MSLZ slz, MXYZ* xyz)
```

**17、确认指定点是否在某一道路边界范围内**

```cpp
bool OpenDriveParse::IsPointOnRoad(MXYZ& xyz, Road& road, int& idx, double& s, double& l)
```

------

后面，还会有一些上面某些函数**可视化的结果和关于OpenDRVIE的一些总结与思考**，我都会在下一篇写出来（已经在写了...）