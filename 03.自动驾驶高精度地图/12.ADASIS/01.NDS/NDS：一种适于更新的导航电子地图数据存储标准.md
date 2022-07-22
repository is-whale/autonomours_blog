- [NDS：一种适于更新的导航电子地图数据存储标准_地图数据模型师的博客-CSDN博客_nds地图](https://blog.csdn.net/keykeywu/article/details/9666135?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2~default~BlogCommendFromBaidu~default-1-9666135-blog-124225339.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2~default~BlogCommendFromBaidu~default-1-9666135-blog-124225339.pc_relevant_default&utm_relevant_index=1)

**NDS：一种适于更新的导航电子地图数据存储标准**

**吴中恒**

**摘要：**随着导航电子地图需求的不断增长，地图数据更新技术已成为制约其发展的一个瓶颈。本文介绍了新一代可以支持增量更新的导航电子地图存储标准NDS的研究情况，阐述了其产生的背景和发展目标，分析了其适于更新的数据模型，详细论述了其数据内容的特点和其完善的地图数据更新机制，并以一个地图数据更新的实例，生动的展示NDS在地图数据更新上的特点，为导航电子地图产业发展所需要重点解决的科学与技术问题，提供了新思路。

**关键词：NDS 增量更新 嵌入式数据库**

# 1引言

目前，车载导航和便携式导航产品已经被人们广泛使用，其便捷的POI检索、最优路径规划、智能语音引导等功能给人们的日常出行带来诸多便利。然而随着城市经济建设的迅猛发展，城市道路日新月异，为了能准确的反映城市变化，必须定期更新地图数据。现阶段，地图更新问题已经成为制约车载导航应用发展的一个“瓶颈”，而地图增量更新技术则是解决该问题的关键之一。导航电子地图的更新技术是一个复杂的系统工程（见图1），其涉及到电子地图信息的收集、融合、编译、差分、传输和集成等诸多问题，其中电子地图数据的物理存储格式是解决这一问题的基础。![img](https://img-blog.csdn.net/20130731123755093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V5a2V5d3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

图1 导航电子地图数据更新技术体系

目前市场上的车载导航产品采用的物理存储格式种类繁多，多为各企业自行定义的基于文件的物理存储格式。由于这些存储格式是在移动设备的计算能力弱的背景下设计的，多是为了满足快速路径规划等需求，其在地图增量更新方面，目前并不完全适用，主要表现在：

（1）文件存储为主

由于导航产品一般以便携式设备为载体，基于嵌入式操作系统开发，基本不具备数据库环境，目前几乎所有导航电子地图存储均采用了文件存储方式。

（2）紧凑结构不利更新

结构紧凑易于计算的文件存储往往不能方便地进行数据的增删操作。因为文件存储中大量使用了地址偏移、计数等，地图增量更新引起的一系列增删操作，将引发一连串的文件更新操作，文件的结构难以维护。

（3）缺乏统一的标准

目前市场上导航产品的物理存储格式种类繁多，粗略统计不下三十余种，这样势必会出现一个服务中心应对众多个性化终端处理的难题。因此从实用的角度看，推广采用统一数据规格或存储格式是一个比较优化的策略。

针对上述问题，国内外专家已经启动了新一代可以支持增量更新的导航电子地图存储标准的研究，其中比较有影响力的有日本的KIWI3.0和欧洲的NDS。

日本KIWI协会研究的KIWI3.0是在日系导航产品已经广泛采用的KIWI2.0的基础上，针对增量更新需求的改进版，其主要采用永久编码、原数据和扩展表（存储变化信息）的方式实现地图更新。但其设计基本还是沿用KIWI2.0的框架，其结构针对增量更新这一需求仍然存在着parcel数据量超限、要素之间依赖紧密、规划数据中重叠区域引起的大范围更新的等问题，其未来发展的局限性较大。

欧洲NDS组织研究的NDS，是近3年发展起来的多跨国企业联合开发的统一地图存储标准，其突出特点是：在兼顾性能和功能的基础上，采用了数据库技术存储地图数据，能够比较好地解决地图增量更新、扩展和数据安全的问题。

# 2NDS的技术体系

## 2.1背景和目标

2005年，德国宝马、大众等车厂发起，联合导航电子地图数据提供商和导航系统软件提供商成立了PSI（PSF Standard Initiative）组织，提出了开发一种安全、可靠、易用、便于更新的导航电子地图存储标准。其目标是：

l 支持增量更新

l 适应多种发布方式和存储介质

l 能够作为一种世界通用标准

l 将地图和导航应用分开

l 高兼容性和互操作性

l 较数据压缩和高性能

l 易于扩展

l 可以有效防止非法数据拷贝

PSI组织工作的最终成果是NDS（NavigationData Standard），它是一种基于嵌入式数据库的导航电子地图数据存储标准。

【注】http://www.nds-association.org/the-nds-standard/

## 2.2适于更新的数据模型

与传统的导航电子地图物理存储格式一样，NDS的技术流程，也是采用将交换格式的数据，经过预处理后，编译成NDS格式的数据，并在此基础上，开发应用于手持的或者车载的导航应用，提供地图显示、路径规划等服务，但NDS数据格式的组织，也有其独特的地方。

NDS采用[SQLite](https://so.csdn.net/so/search?q=SQLite&spm=1001.2101.3001.7020)嵌入式数据库进行地图数据的存储。其中，地图显示和道路规划等信息记录在BLOB变长字段中，POI直接采用数据表存储。采用基于嵌入式数据库存储的方式，可以充分利用数据库的检索特性，提高效率。同时，对地图数据进行更新时，插入、删除等涉及的操作系统底层的字节级的操作由DBMS自行维护，这样，地图数据的更新将是以地图要素为单位，仅仅需要对逻辑层的关系进行维护，而非像传统的导航电子地图物理存储格式一样，需要对组成地图要素每一个字节、地址偏移等进行处理，花费更多的精力在物理层的操作上。

NDS在嵌入式数据库中采用了分层分块的组织方式，它根据地图数据的内容，分为地图显示、路径规划、名称、POI、交通信息、语音表达等六个内容层，分别存储在嵌入式数据库的不同数据表中。对于某一内容层的数据，划分为多个比例尺的数据表达层；对以某一内容层指定比例尺的数据，进行分块(Tile)表达和存储。对于某内容层指定比例尺的某块(Tile)的数据，在NDS中，变现为数据库表中的一条记录，即对应在数据表中的一行。数据间的关联，不再是通过传统的地址偏移来链接，而是通过数据库ID来互相引用的。NDS数据模型充分结合了数据库的特性，当需要对地图数据进行更新时，NDS数据进行逐块(Tile)的更新，即替换数据表中块(Tile)对应的一行。这种更新方式，充分利用了DBMS的特性，简化了关系的维护。

## 2.3数据内容及特点

NDS是由地图显示、道路规划、POI、名称、交通信息、语音表达等六大部分组成的一个SQLite数据库，每一部分对应着一系列的SQLite表结构。其中，地图显示部分包括基础地图显示和高级地图显示，支持三维地形、三维建筑物和卫星影像等的显示；交通信息部分包括对TMC和VICS的支持。其数据内容如下图所示：

![img](https://img-blog.csdn.net/20141211111528893?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V5a2V5d3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

图2NDS数据内容结构图

### 2.3.1适于更新的分层分块组织方式

NDS数据采用WGS84坐标系统，在不同的内容层中，数据以多比例尺的方式存储。随着比例尺的变化，地理要素会被综合，次重要的要素将被过滤掉。对以某一内容层指定比例尺的数据，采用分块(Tile)的方式组织的，每个块(Tile)都是一个规则正方形格网（如下图），彼此之间没有重叠，对应在NDS数据库中，就是数据表中的一行记录。

![img](https://img-blog.csdn.net/20141211111607656?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V5a2V5d3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

图3 NDS的分层分块方式

这样的组织方式，在对地图显示数据进行更新时，不用像KIWI数据格式中那样，需要维护地图显示数据中集成格网、分割格网等复杂的逻辑结构，仅仅将对应的发生变化的正方形格网替换即可。同样，在对路径规划数据进行更新时，亦避免了像KIWI、SDAL数据格式中那样，需要维护路径规划数据中以不规则多边形方式组织的或者彼此有重叠的拓扑信息的关系。

NDS的分层分块方式，使得地图数据的更新操作可以仅仅以正方形格网进行简单的替换即可完成。

### 2.3.2适于更新的要素表达方式

  NDS中的地图要素，根据其内容的不同，分成规划、名称、显示、POI、TMC等多个要素类（如下图），要素类可能会在多个比例尺不同的内容层中出现。要素都拥有自己的属性，要素间的关系，通过对彼此的引用记录在以块为单位的格网单元中。

![img](https://img-blog.csdn.net/20141211111629562?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V5a2V5d3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

图4 NDS对象模型图

  在KIWI数据格式中，其为了提高计算效率，引入了集成交叉点、Multilink等复杂的要素，要素的结构庞大，在更新中关系的维护非常困难。相对于KIWI这样的传统导航电子地图物理存储格式，NDS中的地图要素对象模型的设计结构简洁，要素的粒度划分更原子级，要素间的关系清晰，在地图数据更新中，无论是要素本身的更新还是对要素间关系的维护，都变得更加容易。

### 2.3.4可扩展的高级地图显示支持

  NDS中提供了对高级地图显示的支持，它是为了给终端用户提供更加友好的的显示效果、人机界面，提供3D的支持，包括数字地形模型、3D车道模型、卫星影像、建筑物模型、3D landmarks和3D icons等。

(1) 正射影像显示

![img](https://img-blog.csdn.net/20141211111709592?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V5a2V5d3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

图5 DOM

(2) 数字地形显示

![img](https://img-blog.csdn.net/20141211111746218?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V5a2V5d3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

图6数字地面模型

(3) 三维建筑物模型

![img](https://img-blog.csdn.net/20141211111813957?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V5a2V5d3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

图7三维城市建筑模型

(4) 多比例尺3Dlandmarks

  ![img](https://img-blog.csdn.net/20141211111843317?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V5a2V5d3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)![img](https://img-blog.csdn.net/20141211111915187?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V5a2V5d3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

图8 多比例尺3D landmarks

  针对用户需求的多样性和实时性，NDS标准数据格式的设计中，高级地图显示以分层分块的方式存储在数据格式中，其独立性高，结构简洁，与其他要素类间的耦合度低，利于进行更新操作。

# 3NDS的更新机制

## 3.1NDS的更新方式

现阶段，导航系统应用于多种的车载、移动终端，对于地图更新这一新需求，需要应对不同的存储介质和更新方式。PSI将提供一套整体解决方案，以一个统一的地图数据存储标准NDS，来解决众多导航系统及多样化的更新方式的问题。

NDS将广泛的支持多种更新方式：

(a) 对整个数据库的增量更新方式

(b) 补丁包的方式

(c) 为已有数据库添加内容的方式（高级显示、语音表达）

(d) POI的更新、替换等方式

(e) 地图数据的扩展

(f) 针对整个国家的增量更新方式

(g) 属性的更新、替换、添加

## 3.2一个更新的实例

下面，将以一个具体的更新实例，举例说明NDS是如何进行导航电子地图数据更新的。

(1) 更新前

初始化的NDS数据库，其覆盖的范围包括Belgium，Netherlands,，Luxemburg，数据库中包含地图显示、路径规划、名称等三个数据内容层，每个数据层分为多个比例尺，固定比例尺下的数据分块存储在数据库中。



![img](https://img-blog.csdn.net/20141211111948500?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V5a2V5d3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

图9 NDS数据—更新前

在Netherlands区域，某一街区的道路形状发生变化，这样，涉及到NLD数据库中的几何信息和属性信息需要更新。具体的更新操作，表现在NDS数据标准中，需要对发生更新的数据块，即数据库对应的表中的一行，进行替换操作。

(2) 更新后

NDS接受到了增量更新包V2，对NLD数据库中几何信息和属性信息发生变化的块进行更新，即将对应的地图显示、路径规划中的数据块替换，名称数据更新，对应的结构关系如下图。

![img](https://img-blog.csdn.net/20141211112009054?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva2V5a2V5d3U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



图10NDS数据—更新后

在现实情况中，发生变化的可能道路的形状、名称、拓扑连接关系、交通限制信息等的，反映在NDS中，则需要将发生变化的信息分类对应到具体的数据内容层中去，将与之对应的数据块更新。

NDS在设计之初，就针对解决地图数据更新的问题，其数据存储结合了嵌入式数据库诸多特性，数据结构的组织在利于快速更新这一基础上，充分考虑了高效检索与快速计算的导航应用需求，能较好的解决传统导航电子地图物理存储格式所面临的诸多瓶颈问题。

# 4结论

现代计算机技术飞速发展，微电子技术和存储技术不断进步，嵌入式系统的内存和各种永久存储介质容量都在不断增加，嵌入式数据操作系统日益成熟，嵌入式数据库用途也更加的广泛。

目前车辆导航系统中以阶段性的更新数据的方式来更新地图数据这一方法已不能满足导航电子地图数据库对数据现势性的要求。传统的基于文件系统物理存储的导航电子地图数据物理格式亦难以支持地图数据的增量更新。

NDS组织结合主流计算机技术发展的潮流，针对导航电子地图行业的新需求，提出了一套先进的整体解决方案，设计出了基于嵌入式数据库的导航电子地图数据存储标准NDS。NDS提出与实现得益于现代计算机技术的进步，可以预见随着嵌入式数据库系统的完善和发展，基于数据库的导航应用系统将更加广泛的应用于市场中。

# 参考文献

[1] KIWI 3.0 Document(Draft) Kiwi-W Consortium. 2006

[2] GDF-GeographicData Files - Version 4.0

[3] PSF Specificationfor SDAL Format Version 1.7

[4] Update ScenarioDescription. PSF Standardization Initiative 2007

[5] NDS Specification1.2. PSF Standardization Initiative 2007

[6] ActMAPSpecification (Version 1.0). ActMAP Consortium. 2004

[7] http://www.nds-association.org/the-nds-standard/