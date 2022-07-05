- [Unity解析OSM数据，并生成简单模型_SuperWiwi的博客-CSDN博客_osm模型分析](https://blog.csdn.net/qq_36622009/article/details/106862309)

### 一、介绍[XML](https://so.csdn.net/so/search?q=XML&spm=1001.2101.3001.7020)数据格式

因为OSM数据就是XML数据格式，所以先介绍一下XML数据格式。

XML 指[可扩展标记语言](https://so.csdn.net/so/search?q=可扩展标记语言&spm=1001.2101.3001.7020)（eXtensible Markup Language）。

- XML 被设计用来传输和存储数据。
- XML 文档形成了一种树结构，它从"根部"开始，然后扩展到"枝叶"。
- XML 元素指的是从（且包括）开始标签直到（且包括）结束标签的部分。
  - XML 文档必须包含根元素。该元素是所有其他元素的父元素。
  - 所有的元素都可以有文本内容和属性（类似 HTML 中）。
  - 在 XML 中，省略关闭标签是非法的。所有元素都必须有关闭标签。

**XML 文档实例**

```xml
<?xml version="1.0" encoding="UTF-8"?> <!-- XML声明：定义 XML 的版本和所使用的编码 -->
<note date="12/11/2007"> <!--根元素-->
  <to>Tove</to>
  <from>Jani</from>
  <heading>Reminder</heading>
  <body>Don't forget me this weekend!</body>
</note>
```

以上显示了根元素note及它的四个子元素（Element）。

### 二、Unity解析XML数据格式的方法

#### 1.C#自带的方法

```csharp
//引入命名空间
using System.Xml;

public class Parser : MonoBehaviour
{
	//创建xml文档对象
    private XmlDocument doc = new XmlDocument();
    
    private void Start()
    {
    	//读入xml文件
    	XmlTextReader reader= new XmlTextReader("Assets/" + mapName);    	
    	//加载到xml文档对象中
    	doc.Load(reader);
    	
    	
    	//根据元素名称获取元素list
    	XmlNodeList elemList = doc.GetElementsByTagName("node");
		//获取元素的子元素们
		foreach (XmlNode node in elemList )
        {
            XmlNodeList children = node.ChildNodes;
        }
        
        
		//获取元素的某一属性名称
		string attrName = elemList[0].Attributes[0].Name;
		//获取元素的某一属性值
		string idStr = elemList[0].Attributes["id"].InnerText;
		//把string转为int
		int id = int.Parse(idStr);
    }
}
```

#### 2.Unity读取TextAsset方法

TextAsset是用于导入文本文件的格式。当您将文本文件放入项目文件夹时，它将被转换为TextAsset。支持的文本格式有:`.txt`、`.html`、`.htm`、`.xml`、`.bytes`、`.json`、`.csv`、`.yaml`、`.fnt`。

```csharp
using System.Xml;

[Tooltip("The resource file that contains the OSM map data")]
public string resourceFile;

[HideInInspector]    public Dictionary<ulong, OsmNode> nodes;

void Start()
{
	//读取文件
	var txtAsset = Resources.Load<TextAsset>(resourceFile);
	//加载xml内容到xml文件对象
	XmlDocument doc = new XmlDocument();
    doc.LoadXml(txtAsset.text);
    //获取单个元素
    XmlNode xmlNode = doc.SelectSingleNode("/osm/bounds");
    //获取元素list
    XmlNodeList xmlNodeList = doc.SelectNodes("/osm/node"));
}
```

### 三、OSM数据介绍

[OpenStreetMap](https://so.csdn.net/so/search?q=OpenStreetMap&spm=1001.2101.3001.7020)的元素（数据基元）主要包括三种：

- 点（Nodes）
- 路（Ways）
- 关系（Relations）

这三种元素构成了整个地图画面。其中，Nodes定义了空间中点的位置；Ways定义了线或区域；Relations（可选的）定义了元素间的关系。

**根元素：**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<osm version="0.6" generator="CGImap 0.0.2">
	<bounds minlat="34.1362000" minlon="-118.4360000" maxlat="34.2371000" maxlon="-118.2938000"/>
</osm>
```

**node元素：**

```xml
<node id="10537778" lat="34.1650043" lon="-118.2969154" user="mattmaxon" uid="96974" visible="true" version="18" changeset="787046" timestamp="2009-03-11T12:10:18Z"/>
<node id="10537785" lat="34.1732598" lon="-118.3062396" user="mattmaxon" uid="96974" visible="true" version="4" changeset="768622" timestamp="2009-03-09T13:57:18Z">
 	<tag k="highway" v="traffic_signals"/>
</node>
```

可以看到，node元素的属性有：`id`、`lat`、`lon`、`user`、`uid`、`visible`、`version`、`changeset`、`timestamp`。我们实际上需要的只有前三个属性。其他属性的介绍可以看[官方介绍](https://wiki.openstreetmap.org/wiki/Elements)。

关于Tag元素：

> Tags describe the meaning of the particular element to which they are attached.

简单来说就是用一些键值对表示这些元素的地图特征。并没有一个固定的字典来表示这些tag，但是可以参考这里：[Map Features page](https://wiki.openstreetmap.org/wiki/Map_Features)。

**way元素：**

```xml
<way id="40779547" user="NE2" uid="207745" visible="true" version="2" changeset="4563211" timestamp="2010-04-30T05:30:59Z">
  <nd ref="495645323"/>
  <nd ref="358076071"/>
  <nd ref="358076070"/>
  <nd ref="122950026"/>
  <nd ref="123377574"/>
  <nd ref="495645323"/>
  <tag k="landuse" v="residential"/>
  <tag k="source" v="City of Burbank"/>
</way>
```

每个way包含了多个nd元素。way的属性中我们也只需要id。

**relation元素：**

```xml
<relation id="112106" user="jerjozwik" uid="36489" visible="true" version="14" changeset="5359394" timestamp="2010-07-31T01:01:53Z">
  <member type="way" ref="33104747" role="inner"/>
  <member type="way" ref="33104749" role="inner"/>
  <member type="way" ref="33104751" role="inner"/>
  <member type="way" ref="33104776" role="inner"/>
  <member type="way" ref="33104779" role="inner"/>
  <member type="way" ref="33104780" role="inner"/>
  <member type="way" ref="33104799" role="inner"/>
  <member type="way" ref="33104802" role="inner"/>
  <member type="way" ref="33104805" role="inner"/>
  <member type="way" ref="33104815" role="inner"/>
  <member type="way" ref="33104857" role="inner"/>
  <member type="way" ref="29234226" role=""/>
  <tag k="type" v="multipolygon"/>
</relation>
```

relation元素记录了两个或多个数据元素(node、way和/或其他relation)之间的关系。例如：一种路线关系，它列出了组成主要公路、自行车路线或公共汽车路线的路线。relation元素的含义可以有很多，还是要依据tag来看。

### 四、Unity解析OSM数据

#### 1.定义node和way的数据结构

```csharp
using System.Collections.Generic;

public struct Node {
	public int id;
	public float lat, lon;
	public Node(int ID, float LAT, float LON) {
		id = ID;
		lat = LAT;
		lon = LON;
	}
}

public struct Way {
	public int id;
	public List<int> wnodes;

	public Way(int ID) {
		id = ID;
		wnodes = new List<int>();
	}
}
```

#### 2.获取XML文件中node和way的属性值并存储

```csharp
private List<Node> nodes = new List<Node>();
private List<Way> ways = new List<Way>();
[SerializeField]    private string mapName = "map2.osm";

doc.Load(new XmlTextReader("Assets/" + mapName));

//【存储所有nodes】
XmlNodeList elemList = doc.GetElementsByTagName("node");
for (int i = 0; i < elemList.Count; i++)
{
    nodes.Add(new Node(int.Parse(elemList[i].Attributes["id"].InnerText),
        float.Parse(elemList[i].Attributes["lat"].InnerText),
        float.Parse(elemList[i].Attributes["lon"].InnerText)
    ));
}

//【存储所有ways】
XmlNodeList wayList = doc.GetElementsByTagName("way");
int ct = 0;
foreach (XmlNode node in wayList)
{
   XmlNodeList wayNodes = node.ChildNodes;
   //存储way的id
   ways.Add(new Way(int.Parse(node.Attributes["id"].InnerText)));
   //存储way的每个node的id
   foreach (XmlNode nd in wayNodes)
   {
       if (nd.Attributes[0].Name == "ref")//根据元素属性筛选出node元素
       {
           ways[ct].wnodes.Add(int.Parse(nd.Attributes["ref"].InnerText));
           Debug.Log(ways[ct].wnodes.Count);
       }
   }
   ct++;
}
```

现在`nodes`和`ways`两个list已经存储了点和线的id、经纬度信息。

### 五、使用LineRender对OSM数据进行简单[可视化](https://so.csdn.net/so/search?q=可视化&spm=1001.2101.3001.7020)

（1）LineRender介绍：设置LineRender组件的Positions数组，确定一条Line上面控制点的位置。设置width曲线控制Line的宽度。比如现在就是长度为1，宽度约0.2的一条Line。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200619201700249.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NjIyMDA5,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020061920181894.png#pic_center)

（2）把每条way用LineRender画出来。需要把经纬度转变成xy坐标。

下面这种写法的执行速度很慢，只是体现个思路。

```csharp
private List<Transform> wayObjects = new List<Transform>();

[SerializeField]    private float x;
[SerializeField]    private float y;
//在osm文件的bounds元素中已知此区域的经纬度极值
//<bounds minlat="34.1362000" minlon="-118.4360000" maxlat="34.2371000" maxlon="-118.2938000"/>
[SerializeField]    private float boundsX = 34;
[SerializeField]    private float boundsY = -118;

for (int i = 0; i < ways.Count; i++)
{
    wayObjects.Add(new GameObject("wayObject" + ways[i].id).transform);
    
    wayObjects[i].gameObject.AddComponent<LineRenderer>();
    LineRenderer line = wayObjects[i].GetComponent<LineRenderer>();    
    line.startWidth = line.endWidth = 0.05f;
    line.positionCount = ways[i].wnodes.Count;
    
    for (int j = 0; j < ways[i].wnodes.Count; j++)
    {
        foreach (Node nod in nodes)//遍历nodes的list，找到对应node的经纬度
        {
            if (nod.id == ways[i].wnodes[j])
            {
                x = nod.lat;
                y = nod.lon;
            }
        }
        wayObjects[i].GetComponent<LineRenderer>().SetPosition(j, new Vector3((x - boundsX) * 100, 0, (y - boundsY) * 100));
    }
}
```

效果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020061920303456.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NjIyMDA5,size_16,color_FFFFFF,t_70#pic_center)

### 六、根据OSM数据创建道路和建筑的简单模型

创建简单道路和建筑模型要用到mesh编程的知识。简单来说就是给一游戏物体添加MeshFilter组件后，自己去设置这个Mesh的顶点、法线、三角形、uv。最后结果是这样：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200619210033769.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NjIyMDA5,size_16,color_FFFFFF,t_70#pic_center)
稍放大一点，可以看到道路上的贴图。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200619205647427.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NjIyMDA5,size_16,color_FFFFFF,t_70#pic_center)
在wireframe模式下，可以比较清楚的看到，道路是用多个三角形拼起来的，**交叉口没有做处理**。

建筑除了根据多个点画出底面外，还具备一定的高度。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200619210246327.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NjIyMDA5,size_16,color_FFFFFF,t_70#pic_center)

#### 1.重新定义node和way的数据结构

因为我们要区分哪些点是用于道路的，哪些点是用于建筑的，所以除了之前的“id”、“lan”、“lon”，我们还需要知道一些Tag的值。

（1）优化一下从xml中获取属性的方法，定义一个BaseOsm类：

```csharp
using System;
using System.Xml;

class BaseOsm
{
    protected T GetAttribute<T>(string attrName, XmlAttributeCollection attributes)
    {        
        string strValue = attributes[attrName].Value;
        return (T)Convert.ChangeType(strValue, typeof(T));
    }
}
```

（2）OSMNode类：

```csharp
using System.Xml;
using UnityEngine;

class OsmNode : BaseOsm
{
    public ulong ID {get; private set; }
    public float Latitude { get; private set; }
    public float Longitude { get; private set; }
    public float X { get; private set; }
    public float Y {get; private set; }
    
    // implicit conversion between OsmNode and Vector3
    //使得node可以像Vector3一样运算
    public static implicit operator Vector3 (OsmNode node)
    {
        return new Vector3(node.X, 0, node.Y);
    }
    
    public OsmNode(XmlNode node)
    {
        ID = GetAttribute<ulong>("id", node.Attributes);
        Latitude = GetAttribute<float>("lat", node.Attributes);
        Longitude = GetAttribute<float>("lon", node.Attributes);
		//从经纬度转坐标的方法分离出去了
        X = (float)MercatorProjection.lonToX(Longitude);
        Y = (float)MercatorProjection.latToY(Latitude);
    }
}
```

（3）OSMWay类：

```csharp
using System.Collections.Generic;
using System.Xml;

class OsmWay : BaseOsm
{
    public ulong ID {get; private set; }
    public bool Visible { get; private set; }
    public List<ulong> NodeIDs {get; private set; }
    public bool IsBoundary {get; private set; }//边界可以在场景中画出，方便判断
    public bool IsBuilding {get; private set; }
    public bool IsRoad {get; private set; }
    public float Height {get; private set; }
    
    public OsmWay(XmlNode node)
    {
        NodeIDs = new List<ulong>();
        ID = GetAttribute<ulong>("id", node.Attributes);
        Visible = GetAttribute<bool>("visible", node.Attributes);
        //保存way中的nodes的id
        XmlNodeList nds = node.SelectNodes("nd");
        foreach (XmlNode n in nds)
        {
            ulong refNo = GetAttribute<ulong>("ref", n.Attributes);
            NodeIDs.Add(refNo);
        }
		//判断是否是边界
        if (NodeIDs.Count > 1)
        {
            IsBoundary = NodeIDs[0] == NodeIDs[NodeIDs.Count - 1];
        }
        
       	Height = 10.0f; 
        XmlNodeList tags = node.SelectNodes("tag");
        foreach (XmlNode t in tags)
        {
            string key = GetAttribute<string>("k", t.Attributes);
            if (key == "height")//不确定是建筑还是道路高度
            {
                Height = 0.3048f * GetAttribute<float>("v", t.Attributes);
            }           
            else if (key == "building:levels")//建筑层数，每一层是3m
            {
                 Height = 3.0f * GetAttribute<float>("v", t.Attributes);
            }

            else if (key == "building")//是建筑
            {
                IsBuilding = GetAttribute<string>("v", t.Attributes) == "yes";
                Height = 10.0f;
            }
            else if (key == "highway")//是公路
            {
                IsRoad = true;
            }
            /** would preferably like to use only: 
            ** trunk roads
            ** primary roads
            ** secondary roads
            ** service roads
            */
        }
    }   
}
```

#### 2.创建模型

**（1）定义基类**

```csharp
using UnityEngine;
[RequireComponent(typeof(MapReader))]//该物体应当添加了MapReader组件
abstract class InfrstructureBehaviour : MonoBehaviour
{
    protected MapReader map;
    void Awake()
    {
        map = GetComponent<MapReader>();
    }

    protected Vector3 GetCentre(OsmWay way)
    {
        Vector3 total = Vector3.zero;
        foreach (var id in way.NodeIDs)
        {
            total += map.nodes[id];
        }
        return total / way.NodeIDs.Count;
    }
}
```

**（2）创建道路**

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
 
class RoadMaker : InfrstructureBehaviour
{
    public Material roadMaterial;
    IEnumerator Start()
    {        
        while (!map.IsReady){yield return null;}//等待地图数据解析完毕

        foreach (var way in map.ways.FindAll((w) => { return w.IsRoad; }))
        {
        	//【1】创建一个游戏物体，并将它的位置设置为道路中心
            GameObject go = new GameObject();
            Vector3 localOrigin = GetCentre(way);//找到这条道路的中心位置		
            go.transform.position = localOrigin - map.bounds.Centre;//减掉整个地图的中心位置，就是应该在场景中显示的道路中心了
			//【2】向游戏物体添加MeshFilter和MeshRenderer组件
            MeshFilter mf = go.AddComponent<MeshFilter>();
            MeshRenderer mr = go.AddComponent<MeshRenderer>();
			//设置材质
            mr.material = roadMaterial;
			//设置顶点位置、法线、uv、三角形
            List<Vector3> vectors = new List<Vector3>();
            List<Vector3> normals = new List<Vector3>();
            List<Vector2> uvs = new List<Vector2>();
            List<int> indices = new List<int>();

            for (int i = 1; i < way.NodeIDs.Count; i++)
            {
                OsmNode p1 = map.nodes[way.NodeIDs[i - 1]];
                OsmNode p2 = map.nodes[way.NodeIDs[i]];

                Vector3 s1 = p1 - localOrigin;
                Vector3 s2 = p2 - localOrigin;
                
                Vector3 diff = (s2 - s1).normalized;
                //使用叉积求出这条路的水平方向，并设置道路宽度
                var cross = Vector3.Cross(diff, Vector3.up) * 2.0f; // 2 meters - width of lane

                Vector3 v1 = s1 + cross;//第一个点左偏移
                Vector3 v2 = s1 - cross;//第一个点右偏移
                Vector3 v3 = s2 + cross;
                Vector3 v4 = s2 - cross;
                
				//添加顶点坐标
                vectors.Add(v1); vectors.Add(v2); vectors.Add(v3); vectors.Add(v4);
				//添加uv坐标
                uvs.Add(new Vector2(0,0)); uvs.Add(new Vector2(1,0)); uvs.Add(new Vector3(0,1)); uvs.Add(new Vector3(1,1));
				//添加顶点法线
				for(int i=0;i<4;i++){ normals.Add(-Vector3.up);}
                
                //设置三角形的顶点索引
                // index values
                int idx1, idx2,idx3, idx4;
                idx4 = vectors.Count - 1;
                idx3 = vectors.Count - 2;
                idx2 = vectors.Count - 3;
                idx1 = vectors.Count - 4;

                // first triangle v1, v3, v2
                indices.Add(idx1);
                indices.Add(idx3);
                indices.Add(idx2);

                // second triangle v3, v4, v2
                indices.Add(idx3);
                indices.Add(idx4);
                indices.Add(idx2);

                // third triangle v2, v3, v1
                indices.Add(idx2);
                indices.Add(idx3);
                indices.Add(idx1);

                // fourth triangle v2, v4, v3
                indices.Add(idx2);
                indices.Add(idx4);
                indices.Add(idx3);
            }

            mf.mesh.vertices = vectors.ToArray();
            mf.mesh.normals = normals.ToArray();
            mf.mesh.triangles = indices.ToArray();
            mf.mesh.uv = uvs.ToArray();

            yield return null;
        }
    }         
}
```

**（3）创建建筑物**

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;


class BuildingMaker : InfrstructureBehaviour
{
    public Material building;

    IEnumerator Start()
    {        
        while (!map.IsReady){yield return null;}

        foreach (var way in map.ways.FindAll((w) => { return w.IsBuilding && w.NodeIDs.Count > 1; }))
        {
            GameObject go = new GameObject();
            Vector3 localOrigin = GetCentre(way);
            go.transform.position = localOrigin - map.bounds.Centre;

            MeshFilter mf = go.AddComponent<MeshFilter>();
            MeshRenderer mr = go.AddComponent<MeshRenderer>();

            mr.material = building;

            List<Vector3> vectors = new List<Vector3>();
            List<Vector3> normals = new List<Vector3>();
            List<int> indices = new List<int>();

            for (int i = 1; i < way.NodeIDs.Count; i++)
            {
                OsmNode p1 = map.nodes[way.NodeIDs[i - 1]];
                OsmNode p2 = map.nodes[way.NodeIDs[i]];

                Vector3 v1 = p1 - localOrigin;
                Vector3 v2 = p2 - localOrigin;
                //【和道路不同的是，y坐标要加上高度】
                Vector3 v3 = v1 + new Vector3(0, way.Height, 0);
                Vector3 v4 = v2 + new Vector3(0, way.Height, 0);

                vectors.Add(v1);vectors.Add(v2);vectors.Add(v3);vectors.Add(v4);
                
			    for(int i=0;i<4;i++){ normals.Add(-Vector3.forward);}              
                
                // index values
                int idx1, idx2,idx3, idx4;
                idx4 = vectors.Count - 1;
                idx3 = vectors.Count - 2;
                idx2 = vectors.Count - 3;
                idx1 = vectors.Count - 4;

                // first triangle v1, v3, v2
                indices.Add(idx1);
                indices.Add(idx3);
                indices.Add(idx2);

                // second triangle v3, v4, v2
                indices.Add(idx3);
                indices.Add(idx4);
                indices.Add(idx2);

                // third triangle v2, v3, v1
                indices.Add(idx2);
                indices.Add(idx3);
                indices.Add(idx1);

                // fourth triangle v2, v4, v3
                indices.Add(idx2);
                indices.Add(idx4);
                indices.Add(idx3);
            }

            mf.mesh.vertices = vectors.ToArray();
            mf.mesh.normals = normals.ToArray();
            mf.mesh.triangles = indices.ToArray();

            yield return null;
        }
    }
}
```

### 七、参考

- 使用LineRender可视化：[OSM-2-Unity](https://github.com/raybarrera/OSM-2-Unity/tree/development)
- 创建简单模型：[osm-unity](https://github.com/SimonCuddihy/osm-unity)