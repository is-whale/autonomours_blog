- [路径规划服务基于java实现_zyy_pipi的博客-CSDN博客](https://blog.csdn.net/qq_37731856/article/details/125995496)

# 前言

在生活中我们经常使用百度地图高德地图得导航功能，其中它的路线规划是怎么实现得呢？下面我们研究一下吧！

本文基于java语言结合开源引擎GraphHopper实现

------

# 一、路径规划服务是什么？

路线规划是导航的前提，根据目的地、出发地以及路径策略设置，为用户量身设计出行方案。同时可结合实时交通，帮助用户绕开拥堵路段，提供更贴心、更人性化的出行体验。

路径规划是运动规划的主要研究内容之一。运动规划由路径规划和轨迹规划组成，连接起点位置和终点位置的序列点或曲线称之为路径，构成路径的策略称之为路径规划。
路径规划在很多领域都具有广泛的应用。在高新科技领域的应用有：机器人的自主无碰行动；无人机的避障突防飞行；巡航导弹躲避雷达搜索、防反弹袭击、完成突防爆破任务等。在日常生活领域的应用有：GPS导航；基于GIS系统的道路规划;城市道路网规划导航等。在决策管理领域的应用有：物流管理中的车辆问题(VRP)及类似的资源管理资源配置问题。通信技术领域的路由问题等。凡是可拓扑为点线网络的规划问题基本上都可以采用路径规划的方法解决。

# 二、GraphHopper是什么？

## 1.GraphHopper介绍

GraphHopper 是一个快速且开源的道路路由引擎。

优点：

```
速度快，内存效率高
适用于 OpenStreetMap 数据
由于 Apache 许可证，业务友好
从大型服务器扩展到移动设备
在 Java™ 中实现
适用于安卓和树莓派
有一个大的测试套件
高度可定制

```

官网
http://graphhopper.com/

GITHUB地址:
https://github.com/graphhopper/graphhopper

支持数据格式：

目前GraphHopper 默认使用得是osm的数据，可以从OpenStreetMap下载数据，支持osm和osm.pbf文件，同时也可以基于postgresql+postgis或者shp文件进行数据管理

## 2.GraphHopper 结合java使用

目前有两种使用方式：

### 1.将GraphHopper-web 单独部署 调用其API接口服务（简单不推荐）

源码文件夹里找出搭载web离线路径规划服务所需要的jar包、config.yml配置文件、以及下载好的osm数据。
![在这里插入图片描述](https://img-blog.csdnimg.cn/c9f44376ebb24da7a47602ff5f4b260c.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/3c95c2a98d534aaabd431ab7ed47c10d.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/594f48f4e1d14ef38824c7a921910058.png)

OpenStreetMap 地图数据 （china-latest.osm.pbf和china-latest-free.shp）

China：https://download.geofabrik.de/asia/china.html

各省osm数据下载：http://download.openstreetmap.fr/extracts/asia/china/

![在这里插入图片描述](https://img-blog.csdnimg.cn/0e099375a1f7445ca91fc77c665b8910.png)

随后在此文件夹进入cmd，输入启动命令即可开启web服务，命令如下：
java -Xmx2g -Xms2g -Dgraphhopper.datareader.file=beijing2.osm -jar graphhopper-web-0.12-SNAPSHOT.jar server config.yml

开启服务后即可进行离线路径规划服务，请求地址为：
http://localhost:8989/route?point=40.042997,116.300074&point=40.0458666,116.1656608&type=json&locale=zh-CN&vehicle=car&weighting=fastest&points_encoded=false

也可将web服务部署在服务器，只需修改配置文件中的ip地址，就可以让别人也能访问你所部属的路径规划服务。

路径规划返回的Json字段的具体含义可以参考GraphHopper官网对api的定义，地址：https://graphhopper.com/api/1/docs/routing/

### 2.通过pom形式引入maven，使用其核心库（推荐，高度可定制）

引入相关maven

```c
<dependency>
    <groupId>com.graphhopper</groupId>
    <artifactId>graphhopper-core</artifactId>
    <version>[LATEST-VERSION]</version>
</dependency>
```

OpenStreetMap 地图数据 （china-latest.osm.pbf和china-latest-free.shp）

China：https://download.geofabrik.de/asia/china.html

各省osm数据下载：http://download.openstreetmap.fr/extracts/asia/china/

数据这里暂时使用osm数据 shp数据和postgis数据后期在进行讲解（数据下载可从上方地址下载然后通过osmconvert64.exe工具进行处理转换成osm）

进行数据文件初始化

```c
@Configuration
public class InitRouteConfig {

    private static final Logger logger = LoggerFactory.getLogger(InitJDBCConnectionConfig.class);

    @Resource
    private SystemConfigMapper systemConfigMapper;

    @Bean
    public GraphHopper initConnection() {
        logger.info("-------开始初始化路径规划服务数据！-----------");
        GraphHopper hopper = new GraphHopper();
        // OSM 文件路径
        List<SystemConfig> systemConfigList = systemConfigMapper.selectByExample(new SystemConfigExample());
        if (systemConfigList.size() > 0) {
            String routeFilePath = "当前路径规划数据文件的存放文件夹";
            File fileT = new File(routeFilePath + File.separator + "route.osm");
            if (fileT.exists()) {
                hopper.setOSMFile(routeFilePath + File.separator + "route.osm");
                // 读取完OSM数据之后会构建路线图，此处配置图的存储路径
                hopper.setGraphHopperLocation(routeFilePath);
                // 目前免费包仅支持car、bike、foot三种交通方式的导航
                List<Profile> profiles = new ArrayList<>();
                // 目前提供三种出行方式 驾车 骑行 步行 优先返回最快路线 不考虑经济成本
                profiles.add(new Profile("car").setVehicle("car").setWeighting("fastest").setTurnCosts(false));
                profiles.add(new Profile("bike").setVehicle("bike").setWeighting("fastest").setTurnCosts(false));
                profiles.add(new Profile("foot").setVehicle("foot").setWeighting("fastest").setTurnCosts(false));
                hopper.setProfiles(profiles);
                // 开启速度模式
//        hopper.getCHPreparationHandler().setCHProfiles(new CHProfile("car"));
                // 导入过程可能需要几分钟，加载可能需要几秒钟，取决于导入的区域数据大小
                hopper.importOrLoad();
                logger.info("-------初始化路径规划服务数据完成！-----------");
            } else {
                logger.info("-------初始化路径规划服务数据失败！未检查到route.osm文件-----------");
            }
        }else{
            logger.info("-------初始化路径规划服务数据失败！请检查系统配置临时文件存放路径是否正确！-----------");
        }
        return hopper;
    }

}
```

应用代码

```c
public static void routing(GraphHopper hopper) {
        // 配置一个简单的请求
        GHRequest req = new GHRequest(42.508552, 1.532936, 42.507508, 1.528773).
        // 注意，即使只有这样一个配置文件，我们也必须指定要使用的配置文件
                        setProfile("car").
                // 定义转弯指令的语言
                        setLocale(Locale.CHINA);

		// 设置返回最多备用路线3条
        req.getHints().putObject(Parameters.Algorithms.AltRoute.MAX_PATHS, 3);
         // 设置无法通行区域
        req.putHint(Parameters.Routing.BLOCK_AREA, "纬度,经度,纬度,经度");

        GHResponse rsp = hopper.route(req);

        // 报错处理
        if (rsp.hasErrors())
            throw new RuntimeException(rsp.getErrors().toString());

        // 使用最佳路径，请参阅GHResponse类以了解更多可能性。
        ResponsePath path = rsp.getBest();

        // 全路径的点、距离（米）和时间（毫秒）
        PointList pointList = path.getPoints();
        double distance = path.getDistance();
        long timeInMs = path.getTime();

        Translation tr = hopper.getTranslationMap().getWithFallBack(Locale.UK);
        InstructionList il = path.getInstructions();
        // 迭代所有turn指令
        for (Instruction instruction : il) {
            // System.out.println("distance " + instruction.getDistance() + " for instruction: " + instruction.getTurnDescription(tr));
        }
        assert il.size() == 6;
        assert Helper.round(path.getDistance(), -2) == 900;
    }
```