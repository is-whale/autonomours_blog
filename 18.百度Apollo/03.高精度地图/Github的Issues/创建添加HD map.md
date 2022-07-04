- [apollo1.5 创建添加HD map · Issue #641 · ApolloAuto/apollo (github.com)](https://github.com/ApolloAuto/apollo/issues/641)

Q：您好，请教两个问题，对于一个特定场地，如何创建hd map？ 如何将创建的hd map添加到apollo中？

A：主流HDMap制作方案主要借助于激光雷达、Camera等设备，借助于点云处理算法、图像视频算法、SLAM等技术来制作。国内的高精度地图受到相关法律法规的约束，如果有大规模的场景应用需求可以考虑与百度合作。
在Apollo中使用HDMap 可以参考文档https://github.com/ApolloAuto/apollo/blob/master/modules/map/data/README.md

Q：嗯，看了下https://github.com/ApolloAuto/apollo/blob/master/modules/map/data/README.md，有几问题请教下：

1. /modules/map/data/下的地图目录中通常包含这几个文件base_map.bin，base_map.xml，routing_map.txt，base_map.lb1， default_end_way_point.txt，sim_map.bin，base_map.txt，routing_map.bin， sim_map.txt，想知道每个文件的具体作用是什么？与仿真环境中的哪些有对应关系？
2. 贵司提供的这些文件是如何生成的？具体需要哪些传感器？apollo1.5中是否提供了相应的工具生成这些文件。
   我们计划在一个简单的场地上，先把整个系统跑起来，所以想知道如何根据一个已有场地，生成可以在apollo中使用的hd map，谢谢。

A：

1. 如 README 所示，xml, bin, txt, lb1 都是不同的文件格式，适配不同的读取器，内容是一致的。
   base_map 是基础地图;
   routing_map 是routing模块所需的、对 base_map 预处理得来的地图;
   sim_map 是simulation模块所需的、对 base_map 降采样以提高传输和渲染效率;
   default_end_way_point 是一个proto，定义了该地图默认的终点，用户可以一键导航到这个终点。
   我们建议这些文件都成套提供，避免不必要的麻烦。
2. 如 msbeta 所述，base_map的制作比较复杂，需要的设备较多，我们没有包含在代码中，而是作为一项服务。
   从base_map制作routing_map，见 modules/routing/topo_creator/topo_creator.cc
   从base_map制作sim_map，见 modules/map/tools/sim_map_generator.cc
   选取 default_end_way_point 可以用 modules/tools/mapshow/mapshow.py 来选择 lane_id 和 s.

Q：嗯，具体怎么使用制作base_map的服务？

A：高精度地图的文件(base_map)的制作服务目前以合作方式对外开放。如果只是简单场地的测试，并且希望自己制作，需要首先获取场地的地理坐标(UTM坐标，获取方式依据不同的应用场景不尽相同，比如采用激光雷达，视觉slam，甚至GPS轨迹或者通过人工处理的方式等等),然后将数据组织成高精度地图(base_map)的格式即可

Q：想问下将数据组织成在apollo中可使用的高清地图，使用的是什么工具？

A：建议的地图制作流程为：

1. 原始数据采集(视觉、激光雷达、GPS等)以及处理；
2. 地图数据生成。从步骤一生成的数据通过算法或者人工的方式获取地图数据；
3. 地图格式组织。将地图数据转换为Apollo的高精度地图格式（可以参照base_map.xml格式,其他的地图都可以从base_map.xml生成）
   这三个步骤的工具均需要自己开发，如果只是小规模的简单测试，也可以参照base_map.xml格式手工组织数据。
