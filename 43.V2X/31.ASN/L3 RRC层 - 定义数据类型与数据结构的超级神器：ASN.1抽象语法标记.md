- [[4G&5G专题-60]：L3 RRC层 - 定义数据类型与数据结构的超级神器：ASN.1抽象语法标记_文火冰糖的硅基工坊的博客-CSDN博客](https://blog.csdn.net/HiWangWenBing/article/details/114745229)

## 第1章  L3 RRC层功能概述

### 1.1 RAN的架构概述

![](https://img-blog.csdnimg.cn/20210127000312493.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hpV2FuZ1dlbkJpbmc=,size_16,color_FFFFFF,t_70)

从上图可以看出，**RRC**协议处于**空口协议栈**的L3层，处于PDCP与[NAS](https://so.csdn.net/so/search?q=NAS&spm=1001.2101.3001.7020)层之间。

### 1.2 RRC协议概述

![](https://img-blog.csdnimg.cn/20210127105027565.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hpV2FuZ1dlbkJpbmc=,size_16,color_FFFFFF,t_70)

RRC：**Radio Resource Control protocol，无线资源**控制协议，又称为“”接入层信令AS”

**RRC协议有两个大的基本功能**

1）在基站和手机之间传递L3层无线资源控制信令, 即接入层信令AS，**比如为终端建立无线数据承载，**

2）帮助手机和核心网信令网关在空口传递"非接入层信令**NAS**"。也就是说NAS消息是承载在RRC消息中在空口传输的，NAS消息在有线传输网是承载在S1AP消息中。

### 1.3 RRC消息类型

![](https://img-blog.csdnimg.cn/2021031012101751.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hpV2FuZ1dlbkJpbmc=,size_16,color_FFFFFF,t_70)

**（1）小区级系统信令消息--MIB与SIB**

**（2）小区级系统寻呼消息**

**（3）用户与基站之间的专有信令消息**

**（4）用户与核心网之间的专有信令消息**

## **第2章** RRC消息与ASN.1

### 2.1 RRC消息与ASN.1的关系

RRC消息是使用ASN.1文法描述的。学习RRC协议有必要先了解一下ASN.1协议。  

### 2.2 RRC使用ASN.1的好处

使用ASN.1协议定义网元之间的接口有一些好处，比如：

（1）编码方式紧凑，

（2）不需要定义私有协议，**ASN.1在通信领域使用的非常广泛的协议。**

（3）不需要开发编码器和解码器。

### **2.3** **ASN.1协议简介**

**（1）什么是ASN.1协议**

<u><strong><span>ASN.1不是编程语言，而是数据以及数据结构描述语言，它定义的数据结构与数据可以被转换成其它编程语言所需要的数据结构。</span></strong></u>

ASN.1 – Abstract Syntax Notation dot one，抽象记法1。数字1被ISO加在ASN的后边，是为了保持ASN的开放性，可以让以后功能更加强大的ASN被命名为ASN.2等，但至今也没有出现。

ASN.1[抽象语法标记](https://baike.baidu.com/item/%E6%8A%BD%E8%B1%A1%E8%AF%AD%E6%B3%95%E6%A0%87%E8%AE%B0/3024369)（Abstract Syntax Notation One） ASN.1是一种 ISO/ITU-T 标准，用来描述了数据的**表示、[编码](https://baike.baidu.com/item/%E7%BC%96%E7%A0%81/80092)、传输和解码**的**数据格式**。

它提供了一整套正规的格式用于描述**数据对象**的**结构**，而不管语言上如何执行及这些数据的具体指代，也不用去管到底是什么样的应用程序。

**在任何需要以数字方式发送信息的地方，ASN.1 都可以发送各种形式的信息（声频、视频、数据等等）。**

ASN.1 和特定的 ASN.1 编码规则推进了结构化数据的传输，尤其是网络中应用程序之间的结构化数据传输，它以一种独立于计算机架构和语言的方式来**描述数据结构**。

**（2）ASN.1的编码**

**所谓****ASN.1的编码，是指如何把文本描述的数据结构以及数据（变量）转换成特定的二进制码的方法。这种转换方法就是ASN.1的编码。**

这些编码规则描述了如何对 ASN.1 中定义的数值进行编码，以便用于传输，而不管计算机、编程语言或它在应用程序中如何表示等因素。

ASN.1支持的编码有：基本编码规则（BER） -X.209 、规范编码规则（CER）、识别名编码规则（DER）、压缩编码规则（PER）和 XML编码规则（XER）。

BER：Basic Encoding Rule，基本编码规则，这种编码规则长被应用于设备与网管中心使用SNMP协议的场合。

XER： XML Encoding Rule，XML编码规则，这种编码规则，常被应用与基站OAM平面与网管中心的消息通信。

PER： Packed Encoding Rules，压缩性编码，因为空口资源比较紧张，**RRC消息采用PER编码，不需要按照8比特做对齐 ，最大限度的减少了空口传输的数据量。**

**（3）ASN.1的应用**

OSI 协议套中的[应用层协议](https://baike.baidu.com/item/%E5%BA%94%E7%94%A8%E5%B1%82%E5%8D%8F%E8%AE%AE/3668945)使用了 ASN.1 来描述它们所传输的 PDU，这些协议包括：

用于传输电子邮件的 X.400、用于[目录服务](https://baike.baidu.com/item/%E7%9B%AE%E5%BD%95%E6%9C%8D%E5%8A%A1)的 X.500、用于 VoIP 的 H.323 和 SNMP。它的应用还可以扩展到[通用移动通信系统](https://baike.baidu.com/item/%E9%80%9A%E7%94%A8%E7%A7%BB%E5%8A%A8%E9%80%9A%E4%BF%A1%E7%B3%BB%E7%BB%9F)（UMTS）中的接入和[非接入层](https://baike.baidu.com/item/%E9%9D%9E%E6%8E%A5%E5%85%A5%E5%B1%82/5876110)，这里就是RRC协议和NAS协议。

### **2.4** **ASN.1的特点**

**（1）可扩性**

**（2）可靠性**

**（2）兼容性**

**（3）简洁性**：简洁的[**二进制**编码](https://baike.baidu.com/item/%E4%BA%8C%E8%BF%9B%E5%88%B6%E7%BC%96%E7%A0%81/1758517)规则（BER、CER、DER、PER，但不包括 XER）可当作更现代 XML 的替代。ASN.1在传输过程中，比编译成简洁的二进制格式进行。

**（4）抽象性**

ASN.1 支持对数据的语义进行描述，所以它是比 XML 更为高级的语言。

正是由于这种数据类型的“抽象"特性，所以描述它的语法在OSI术语中被称为抽象语法（abstract syntax).抽象语法定义的数据类型，在传输时遵循的数据编码规则，称为传输语法(transfer syntax).

一种ASN.1数据类型对应的传输语法可以有多种，但只能使用其中的一种。

**（5）与编程语言的可转换性**

**ASN.1 的描述可以容易地被映射成 C 或 C++ 或 Java 的数据结构，并可以被应用程序代码使用，并得到运行时[程序库](https://baike.baidu.com/item/%E7%A8%8B%E5%BA%8F%E5%BA%93)的支持，进而能够对编码和解码 XML 或 TLV 格式的，或一种非常紧凑的压缩编码格式的描述。**

同时，**ASN.1也是一种用于描述结构化客体结构和内容的语言。**

**1984年，ASN.1 就已经成为了一种国际标准，它的编码规则已经成熟并在可靠性和兼容性方面拥有更丰富的历程。**

## 第3章 语法规则

### 3.1 基本语法规则

- 在ASN.1中，符号的定义没有先后次序：只要能够找到该符号的定义即可，而不必关心在使用它之前是否被定义过。
- 所有的标识符、参考、关键字都要以一个字母开头，后接字母（大、小写都可以）、数字或者连字符“-”。不能出现下划线“_”。不能以连字符“-”结尾，不能出现两个连字符（注释格式）。
- 关键字一般都是全部大写的，除了一些字符串类型（如PrintableString，UTF8String，等。因为这些都是由原类型OCTET STRING衍生出来的）。
- 在标识符中，只有类型和模块名字是以大写字母开头的，其它标识符都是以小写字母开头的。
- 带小数点的小数形式不能在ASN.1中直接使用，在ASN.1中实数实际定义为三个整数：尾数、基数和指数
- 注释以两个连字符“--”开始，结束于行的结尾或者该行中另一个双连字符。
- **如同大多数计算机语言**，ASN.1不对空格、制表符、换行符和注释做翻译。但是在定义符号（或者分配符号Assignment）“::=”中不能有分隔符，否则不能正确处理。

### 3.2 基本数据类型

![](https://img-blog.csdn.net/2018022515255454)

### 3.3 复合数据类型

| 类型         | 含义                    | 对应的C/C++语言 |
| ---------- | --------------------- | ---------- |
| CHOICE     | 选择类型,该字段可能有多重不同的类型来表示 | union      |
| SEQUENCE   | 由不同类型的值组成一个有序的集合      | 结构体        |
| SET        | 由不同类型的值组成一个无序的集合      | set        |
| SEQUENCEOF | 由相同类型的值组成一个有序的集合      | 数组         |
| SETOF      | 由相同类型的值组成一个无序的集合      | set        |

### 3.4 自定义数据类型

**（1）自定义数据类的基本表达式**

ASN.1语法遵循传统的巴科斯范式BNF风格．最基本的表达式如：　

<新类型的名字> ::= <类型描述>

​ 其中：

<新类型的名字>是一个以大写字母开头的标识符；是自定义的数据类型。

​ <类型描述> 自定义数据类型的值，通常是预定义的基本数据类型或其他已经已经定义的数据类型。

如：Age ::= INTEGER 定义了Age这个类型

**（2）子定义数据类型的默认值**

没说错，默认值是针对于数据类型的，而不是变量，也就是说，使用该数据类型定义的变量，其默认值都是一样的。

<新类型的名字> ::= <类型描述>（默认值）

如：Age ::= INTEGER（10） 定义了Age这个类型，其默认年龄为10岁。

### 3.5 自定义变量

值定义：<新的值的名字> <该值的类型> ::= <值描述>    =》》》**可以看出，这与普通的编程语言的定义方式正好相反，数据类型名在后，变量名在前。**

其中：

<新的值的名字>是以小写字母开头的标识符；是定义的变量名。

<该值的类型>可以是一个类型的名字，也可以是类型描述；

<值描述>是基于整数、字符串、标识符的组合；是变量的数值。

如：age Age ::= 18 创建Age的实例 。

### 3.6 ASN.1修改器

ASN.1定义了各种修改器，如可选(OPTIONAL),默认(DEFAULT),和选择(CHOICE). 

他们可以改变表达式的声明．

典型地用于定义一种要求编码灵活，而数据表达又不繁琐的数据类型。

它们是对自定义数据类型的一种附加说明：

**（1）可选(OPTIONAL)**

**顾名思义，**其表示，表示该数据类型的成员，是可选的，在编码（转换成二进制数值）时，编码器可以忽略这个成员元素，解码器不能假设一定存在。

**定义：** Name ::= Type  OPTIONAL

**例如：**

Float::= SEQUENCE {undefined  
             Exponent     INTEGER OPTIONAL,    --这个域可选的，不一定存在

             Mantissa     INTEGER,

             Sign            BOOLEAN

            }

**缺点：**

但当邻接的两个元素具有相同的类型时，会给解码器带来一些问题。

在本示例中，当解码器读取这个结构时，导致编码器它看来第一个整数(INTEGER)是，可能是Exponent,也有可能认为是Mantissa，当然，它可以根据后续的数据类型，来综合判断，第一个整型是Exponent还是Mantissa。

一般建议不使用这种容易造成误解的方式定义结构。

**（2）默认(DEFAULT)．**

默认修改器允许容器**包含默认值**．

如果待编码的数据值初始值或实际值，等同于它的默认值，那么它将在发送的数据流中被**忽略**。这样可以节省传送的比特数。**这就是DEFAULT关键字的作用！！！**

例如：

Command::= SEQUENCE {undefined  
                      Token         IA5String (NOP) DEFAULT,

                      Parameter   INTEGER

                      }

如果Token的值为NOP, 则表示其值是default值，收发双发都知道该域的default值，发送方就没有必要再发送了，那么该序列被编码为：

Command ::= SEQUENCE {undefined

                     Parameter    INTEGER

                     }

但如果Token的值不为NOP， 那就不能省略了。

**（3）选择(CHOICE)**

选择修改器允许一个元素在给定的实例中可以**有多种可能的数据类型**！类似与C语言的Union数据类型。

对于**CHOICE的数据类型，**解码器将尝试所有期望的解码算法，直到有一个数据类型符合为止。

当一个复杂的容器中包含**其他容器**时，时候CHOICE选择器就十分有用了

例如：

UserKey::= SEQUENCE {undefined  
                    Name            IA5String,

                   StartDate       UTCTIME,

                    Expire           UTCTIME,

                    KeyData        CHOICE {undefined

                                           ECCKey        ECCKeyType,

                                           RSAKey        RSAKeyType

                     }  
}

在上例中，KeyData可以是ECCKeyType类型的key，或者是RSAKeyType的key。

## 第4章 ASN.1实例分析

### 4.1 实例1：**基本类型的应用**

**--1.整数类型--**  
AGE::=INTEGER   --定义一个新的数据类型标识Age，其值是基本数据类型整型INTEGER。  
age AGE::=18      --定义一个新的变量实体标识age ，其类型为新定义的数据类型AGE, 数值为18.

VehicleType::=INTEGER {    
    car(0),  
    bus(1),  
    taxi(2)  
}     --定义个新的数据类型标识VehicleType，其值是INTEGER，范围是0，1， 2， 分别表示car，bus，taxi.

vehicleA VehicleType::=0  -- 定义一个新的变量实体标识vehicleA，其类型为新定义的数据类型VehicleType，初始值为0，即car。

**--2.布尔类型--**  
Flag::=BOOLEAN  -- 定义一个新的数据类型标识Flag，其值是基本数据类型BOOLEAN  。  
isCheck Flag::=FALSE   --定义一个新的变量实体isCheck ，其类型为新定义的数据类型Flag, 数值为FALSE.

**--3.枚举类型--**  
Week::=ENUMERATED{undefined  
    Monday(1),  
    Tuesday(2),  
    Wednesday(3),  
    Thursday(4),  
    FriDay(5),  
    Saturday(6),  
    Sunday(7)  
}     -- 定义一个新的数据类型标识Week，其值是基本数据类型ENUMERATED.  
bestWeek  Week::=Saturday   --定义一个新的变量实体bestWeek，其类型为新定义的数据类型Week, 数值为Saturday.

**--4.实数类型：**ASN.1对实数的精度没有限制。每个实数可以用M×BE表示,即三元组。{M,B,E}--AngleInRadians::=REAL  
pi AngleInRadians::={31415926,10,-7}  --定义一个新的变量实体pi，其数据类型为AngleInRadians，数值为31415926-10^-7=3.1415926  

**--5.位串类型--**  
Occupation::=BIT STRING{undefined  
    clerk (0),  
    editor (1),  
    artist (2),  
    publisher (3),  
    manager(4)  
}  --定义一个新的数据类型标识Occupation， 其值是bit串0-4，其中0标识clerk，1标识editor  
tom Occupation::={editor,artist}  --定义一个变量标识tom，其数据类型为Occupation，实际值为bit1与bit2，表示是editor和artist。

**--6.字节串类型：是N个8bit字节组成的数据类型**  
IpAdress::=OCTET STRING(SIZE(4))  --定义IpAdress数据类型标识，其值是4个字节的字节串（OCTET STRING），  
ip IpAdress::=11H1D001E001A  --每个字节

**--7.对象标识符：**从对象树派生出的一系列点分数字串的形式,用来表示对象。  
object OBJECT IDENTIFIER::={pc(1) mac(2) vm(3) mobile(4)}  
computer OBJECT IDENTIFIER::={object 1}  
macComputer OBJECT IDENTIFIER::={object 2}  
vmComputer OBJECT IDENTIFIER::={object 3}  
mobilePhone OBJECT IDENTIFIER::={object 4}

**--8.空值类型：**空值类型,当某时刻无法知道数据的标准值,可将值为NUL--  
RoomRenter::=SEQUENCE{undefined  
    name Visiblestring --取自IA5的图形字符组成,不含控制字符  
    roomNumber CHOICE{undefined  
        INTEGER  
        NULL  
    }   
    --代表可选项，可是INTEGER或者NULL  
}  
roomRenter1 RoomRenter::={name "Tom",roomNumber 301}  
roomRenter1 RoomRenter::={name "Jerry",roomNumber NULL}

**--9.字符串类型--**  
NumString::=NnmericString  --定义一个新的数据类型标识NumString，其值是基本数据类型NnmericString 

numStr NumString::="12345678"  --定义一个变量标识numStr ，其数据类型为NumString，实际值为"12345678"

UserName::=PrintableString --定义一个新的数据类型标识UserName，其值是基本数据类型PrintableString 

user UserName::="Tom"   --定义一个变量标识user ，其数据类型为UserName，实际值为"Tom"

### 4.2 实例2：复合数据类型**的应用**

**--1. SEQUENCE-- 不同数据类型的序列，等效为一个结构体**  
AirlineFlight::=SEQUENCE {     --定义一个新的数据类型标识AirlineFlight，其值是基本数据类型SEQUENCE。  
    airline IA5string,                     --定义[数据结构](https://so.csdn.net/so/search?q=%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84&spm=1001.2101.3001.7020)的成员airline，其数据类型为IA5string。  
    flight Numericstring,               --定义数据结构的成员flight ，其数据类型为Numericstring。  
    seats SEQUENCE {              --定义数据结构的成员seats，其实数据类型是另一个SEQUENCE  
        maximum INTEGER,         --定义数据结构的成员maximum，器数据类型是INTEGER  
        occupied INTEGER,          --定义数据结构的成员occupied ，器数据类型是INTEGER  
        vacant INTEGER               --定义数据结构的成员vacant ，器数据类型是INTEGER                
     },  
    airport SEQUENCE  {            --定义数据结构的成员airport ，其实数据类型是另一个SEQUENCE  
        origin IA5string,                  --定义数据结构的成员origin ，器数据类型是IA5string  
        stop1 [0] IA5string OPTIONAL,   --定义数据结构的成员stop1 ，器数据类型是IA5string  
        stop2 [1] IA5string OPTIONAL,   --定义数据结构的成员stop2 ，器数据类型是IA5string  
        destination IA5string                   --定义数据结构的成员destination ，器数据类型是IA5string  
    },  
    crewsize ENUMERATED {  --工作人员数目，枚举  
        six (6),  
        eight (8),  
        ten (10)   
    },  
    cancle BOOLEAN DEFAULT FALSE --定义数据结构的成员cancle ，器数据类型是BOOLEAN， 是否取消航班，默认否。  
}

airplane AirlineFlight::={undefined  
    airline "china",  
    flight "1106",  
    seats {320,280,40},  
    airport {origin "Beijing", destination  
    "Shanghai"},  
    crewsize ten  
}  
--**SEQUENCE OF、SET 和SET OF**基本类似，可以参考上面的--

**--11.标签数据类型--**  
Art::=[UNIVERSAL 0] INTEGER                          -- 定义一个新的数据类型标识Art，其值是基本数据类型INTEGER   
art Art ::=9

Gym::=[APPLICATION 1] INTEGER  
gym Gym::=10

Excont::= SET { type1 [0] INTEGER OPTIONAL,  
type2 [1] INTEGER OPTIONAL }

--**12.CHOICE 选择类型-**- **联合体数据类型**  
Prize::=CHOICE{   
    car IA5string,  
    cash INTEGER,  
    nothing BOOLEAN  
}  
peter Prize::=TRUE 或者  
John Prize::= "Lincoln" 或者  
Sam Prize::= 25000  
 

## 第5章 RRC消息实例分析

PER对齐方式（一般RRC消息采用非对齐方式）：

NGAP的 **NGSetUp**消息的编码：

**（1）RRC消息的解析工具**

**RRC消息的描述是文本，然而传输时是是二进制，因此，发送是需要把文本描述编译成二进制，接收展现时，需要通过解码器，翻译成文本。**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190216153451429.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzQwODk1Mg==,size_16,color_FFFFFF,t_70)

**（2）ASN.1消息的定义**

GlobalRANNodeID ::= **CHOICE** {undefined  
globalGNB-ID GlobalGNB-ID,  
globalNgENB-ID GlobalNgENB-ID,  
globalN3IWF-ID GlobalN3IWF-ID,  
choice-Extensions ProtocolIE-SingleContainer { {GlobalRANNodeID-ExtIEs} }  
}

GlobalGNB-ID ::= **SEQUENCE** {undefined  
pLMNIdentity PLMNIdentity,  
gNB-ID GNB-ID,  
iE-Extensions ProtocolExtensionContainer { {GlobalGNB-ID-ExtIEs} } OPTIONAL,  
…  
}

GNB-ID ::= **CHOICE** {undefined  
gNB-ID BIT STRING (SIZE(22…32)),  
choice-Extensions ProtocolIE-SingleContainer { {GNB-ID-ExtIEs} }  
}

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019021615411014.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzQwODk1Mg==,size_16,color_FFFFFF,t_70)

---

其他参考：

一般推荐阅读《ASN.1 Complete》这本书和 X.691 《ASN.1 encoding rules: Specification of Packed Encoding Rules (PER)》。
