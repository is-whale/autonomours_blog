- [C-V2X之ASN.1 | XuQi's Blog (xuqi1987.github.io)](https://xuqi1987.github.io/2019/07/17/V2x之ASN.1/)

## 简介

ASN.1 全称 Abstract Syntax Natation One. 是一个用来描述抽象类型抽象数据的语法. 类似于 XML, JSON 等, 主要用于编码数据以便于在网络中交换数据.

> 在线解析工具：https://lapo.it/asn1js/

### ASN.1编码方式

#### ASN.1 中定义四种类型

- 简单类型 (Simple types) : 用于编码基本的数据类型. 如 整形,字符串,二进制数据等.

- 结构类型 (Structured types) : 用于编码复杂数据类型. 如, X509 证书中的公钥.

- 标记的类型 (Tagged types) : 用于编码从其他几种类型引申而来的类型.

- 其他类型 (Other types) : CHOICE 或 Any.

  

**简单类型**

| **Type**          | **Descrption**                         |
| :---------------- | :------------------------------------- |
| BIT STRING        | 由任意的 0 和 1 构成的字符串           |
| IA5String         | 任意的 ASCII 字符串                    |
| INTEGER           | 任意的整型                             |
| NULL              | 表示空值                               |
| OBJECT IDENTIFIER | 由一系列的整型序列组成                 |
| OCTET STRING      | 由八比特值构成的字符串, 类似于比特数组 |
| PrintableString   | 由任意的可打印字符构成的字符串         |
| T61String         | 由八比特字符构成的字符串               |
| UTCTime GMT       | 时间值                                 |

**结构类型**

| Type        | Descrption             |
| :---------- | :--------------------- |
| SEQUENCE    | 一个有序的类型集合     |
| SEQUENCE OF | 一个给定类型的有序集合 |
| SET         | 一个无序的类型集合     |
| SET OF      | 一个给定类型的无序集合 |

**ASN.1 中每个类型都对应与一个 class 和 非负的 tag number. 用来区别不同的 ASN.1 类型.**

#### ASN.1 中定义四种 class

- Universal: 用来表示在所有应用中都具有相同意义的类型. 这些类型定义在 X.208
- Application: 用来表示针对于具体应用的类型. 在不同的应用中这些类型的意义往往不同
- Private: 用来表示针对用于具体企业的类型
- Context-specific: 用来表示针对于具体上下文该类型意义会变化的类型

**Tag Numbers**

| **Type**                 | **Tag Number (hexadecimal)** |
| :----------------------- | :--------------------------- |
| INTEGER                  | 02                           |
| BIT STRING               | 03                           |
| OCTET STRING             | 04                           |
| NULL                     | 05                           |
| OBJECT IDENTIFIER        | 06                           |
| SEQUENCE and SEQUENCE OF | 10                           |
| SET and SET OF           | 11                           |
| PrintableString          | 13                           |
| T61String                | 14                           |
| IA5String                | 16                           |
| UTCTime                  | 17                           |

#### BER (Basic Encoding Rules)

#### 三种编码方法

规定了如何将 ASN.1 值表示为比特数组,针对不同给数据类型, BER 规定了如下三种编码方法:

##### Primitive, definte-length encoding

基本数据类型**除字符串类型外**均使用这种编码方式. 这种情况下我们的数据编码结果会是以下形式:

![1563332529996](https://xuqi1987.github.io/2019/07/17/V2x%E4%B9%8BASN.1/1563332529996.png)

其中不包含 End-of-contents 部分, 因为这里我们使用定长编码.

###### Identifier

- **low tag number form**: 这种形式下, Identifier 仅用一个字节表示. bit8 和 bit7 用来表示 class, bit6 为 0, 用来表示是基本类型. bit5-1表示 tag number.
- **high tag number form**: 这种形式下, Identifier 会占用一到多个字节. 第一个字节任然是 low tag number form 形式. 但是 bit5-1 应该全为 1. 其余比特用来表示 tag number, 高字节在前.

###### Length

- **Short form**: 这种形式下, Length 占用一个字节. bit 8 为 0, 其余七字节用于编码长度, 因此这种形式用于表示长度 0-127.
- **Long form**: 这种形式下, Length 占用的字节长度为 2-127 个字节. 第一个字节的 bit8 为 1, 其余字节用于表示紧接着有多少个字节用来编码长度. 高字节在前.

##### Contructed, definite-length encoding

这种编码方法用于编码复杂类型和简单类型中的字符串类型且**编码长度已知**.编码形式与 Primitive, definte-length encoding 类似. 除了 Identifier 字段的第一个字节的 bit6 为 1 (0x20), 用来表示是复杂类型.

##### Constructed, indefinite-length encoding

这种编码方法用于编码复杂类型和简单类型中的字符串类型且**编码长度未知**.编码形式如下:

![1563333599659](https://xuqi1987.github.io/2019/07/17/V2x%E4%B9%8BASN.1/1563333599659.png)

这里 Length 字段的值为 0x80, End-of-contents 字段的值为两个字节的 0. 表示该编码方式为不定长编码.其余字段的编码与 Contructed, definite-length encoding 类似.

#### DER(Distinguished Encoding Rules)

是 DER 编码的一个子集. 区别在于 BER 编码结果不唯一, 而 DER 编码结果唯一

- 当编码长度在 0-127 之间时, 必须使用 short form 形式编码 Length 字段
- 当编码长度大于 128 时, 必须使用 long form 形式且应使用最少的字节对长度进行编码,也就是说如果长度为192, 那么 Length 字段只能占两个字节. **第一个字节为0x81, 表示 Length 字段使用 Long form**, 且紧接着只有一个字节用于编码长度信息. **第二个字节为 0xc0, 用来表示长度为 192**
- 对于简单字符串类型, 必须使用 primitive, definite-length 进行编码
- 对于复杂类型, 必须使用 constructed definite-length 进行编码

#### 编码示例

##### BIT STRING (由任意的 0 和 1 构成的字符串)

对 “00000110011011100101110111000000” 进行编码 (**06 6E 5D C0**),

对于 BIT STRING 类型, Identifier 字段应该为 03(这里我们不考虑 class, 仅仅考虑 tag number). 对于 Length 字段, 因为每个1或者0代表一个bit, 因此该值需要 4 字节进行编码, 因此 Length 字段应该为 04.

编码结果为:

```
BER short form encoding/DER encoding: 03 04 06 6e 5d c0
BER long form encoding: 03 81 04 06 6e 5d c0
```

##### IA5String (任意的 ASCII 字符串)

1. 对 “[test1@rsa.com](mailto:test1@rsa.com)“ 进行编码, 编码结果为:

```
BER short form encoding/DER encoding: 16 0d 74 65 73 74 31 40 72 73 61 2e 63 6f 6d
BER long form encoding: 16 81 0d 74 65 73 74 31 40 72 73 61 2e 63 6f 6d
```

1. “test1@rsa.com” 编码为一个 IA5String 的集合, 该集合中由三个 IA5String 构成: “**test1**”, “**@**”, “**rsa.com**”.

这里我们要编码一个复杂类型, 那么 Identifier 的 bit6 应该为 1 (0x20), 代表复杂类型. 而 IA5String 的 tag number 为 0x16, 因此 Identifier 的值应该为 0x36. 因此编码结果如下:

```
36 13  					// 0x20 + 0x16  Length = 0x13                    
16 05 74 65 73 74 31  	// 16 IA5String  test1
16 01 40  				// 16 IA5String @
16 07 72 73 61 2e 63 6f 6d // 16 IA5String rsa.com
```

##### INTEGER

对 0 进行编码, 编码结果如下: `02 01 00`
对 127 进行编码, 编码结果如下: `02 01 7F`
对 128 进行编码, 编码结果如下: `02 02 00 80`

##### NULL

编码结果为: `05 00` 或 `05 81 00`

##### OCTET STRING (由八比特值构成的字符串, 类似于比特数组)

对 01 23 45 67 89 ab cd ef 进行编码, 编码结果如下: `04 08 01 23 45 67 89 ab cd ef`

##### UTCTime

将要进行 ANS1 编码的 UTCTime 应该是以下形式之一:

```
YYMMDDhhmmZ
YYMMDDhhmm+hh'mm'
YYMMDDhhmm-hh'mm'
YYMMDDhhmmssZ
YYMMDDhhmmss+hh'mm'
YYMMDDhhmmss-hh'mm'

where:

YY is the least significant two digits of the year
MM is the month (01 to 12)
DD is the day (01 to 31)
hh is the hour (00 to 23)
mm are the minutes (00 to 59)
ss are the seconds (00 to 59)
Z indicates that local time is GMT, + indicates that local time is later than GMT, and - indicates that local time is earlier than GMT
hh' is the absolute value of the offset from GMT in hours
mm' is the absolute value of the offset from GMT in minutes
```

对于时间 4:45:40 p.m. Pacific Daylight Time on May 6, 1991 进行编码,
它的所有表示形式中存在以下两种:

```
"910506164540-0700"
"910506234540Z"
```

对他们进行编码的结果为:

```
17 11 39 31 30 35 30 36 31 36 34 35 34 30 2D 30 37 30 30
17 0d 39 31 30 35 30 36 32 33 34 35 34 30 5a
```

##### OBJECT IDENTIFIER (由一系列的整型序列组成)

**编码规则：**

1. 编码后的**第一个字节**的值为 40 * value1 + value2. (value1 要求是 0, 1 或者 2. value2 要求是 0-39.)
2. 接下来的 value3, value4, …, valuen 以 128 为 base 进行编码, 高字节在前. 每个数字编码后除了最后一个字节外其他字节的 bit8 均为 1

对于序列：{ 1, 2, 840,113549 }进行编码:

第一字节 value1 = 1, value2 = 2 40 * 1 + 2 =42 = 0x2A ，编码为0x2A

第二字节840 = 6 * 128 + 72，第二字节应该编码的值是 value1 =0x06 且 bit8 为 1，编码为 0x06 + 0x80 = 0x86

第三字节 为前面的value2= 72，编码为0x48

第四字节113549 = 6 * 128 * 128 + 119 * 128 + 13 ，编码为0x80 + 0x06 = 0x86

第五字节 119=0x77 编码为 0x80+0x77 = 0xF7

第六字节 13 = 0x0d 编码为0x0d

最终编码为：

```
06 06 2a 86 48 86 f7 0d
```

### ASN.1的基本语法规则

| 符号 | 含义                                                         |
| :--- | :----------------------------------------------------------- |
| ::=  | 定义为                                                       |
| \|   | 或                                                           |
| –    | 后面是注释(行)                                               |
| {}   | 清单的开始和结束                                             |
| []   | 标签的开始和结束                                             |
| ()   | 子类型的开始和结束                                           |
| ..   | 范围                                                         |
| …    | 为了方便在新的版本中往现有类型中添加新成员，可用“…”来标记可能以后是其它类型的地方： |

#### 类型定义与类型

**<新类型的名字> ::= <类型描述>**

**值定义：<新的值的名字> <该值的类型> ::= <值描述>**

- <新的值的名字>是以小写字母开头的标识符；
- <该值的类型>可以是一个类型的名字，也可以是类型描述；
- <值描述>是基于整数、字符串、标识符的组合。

##### **示例**

```
TestModule DEFINITIONS ::= BEGIN -- Module parameters preamble
 Circle ::= SEQUENCE { -- Definition of Circle type
	position-x INTEGER, -- Integer position
 	position-y INTEGER, -- Position along Y-axis
 	radius INTEGER (0..MAX) -- Positive radius
 } -- End of Circle type
END -- End of TestModule
```

**BSM.asn**

```
-- 导入时，FROM后的名字
BSM DEFINITIONS AUTOMATIC TAGS ::= BEGIN

-- imports and exports
-- 导出的类型是BasicSafetyMessage
EXPORTS BasicSafetyMessage;
-- 导入类型，以下类型非本文件定义
IMPORTS AccelerationSet4Way FROM DefAcceleration
		BrakeSystemStatus FROM VehBrake
		VehicleSize FROM VehSize
		Position3D, PositionConfidenceSet FROM DefPosition
		DSecond FROM DefTime
		TransmissionState FROM VehStatus
		Speed, Heading, SteeringWheelAngle, MotionConfidenceSet FROM DefMotion
		MsgCount FROM MsgFrame
		VehicleClassification FROM VehClass
		VehicleSafetyExtensions FROM VehSafetyExt;
	
	-- BasicSafetyMessage是一个有序类型的集合
	BasicSafetyMessage ::=SEQUENCE {
		msgCnt MsgCount,
		-- vehicle ID 由八比特值构成的字符串, 类似于比特数组
		id OCTET STRING (SIZE(8)), 
		-- Reserved for Electronic Vehicle Identification，可选，大小在4~16字节
		plateNo OCTET STRING (SIZE(4..16)) OPTIONAL,
		
		secMark DSecond,
		pos Position3D,
		accuracy PositionConfidenceSet,
		transmission TransmissionState,
		speed Speed,
		heading Heading,
		angle SteeringWheelAngle OPTIONAL,
		motionCfd MotionConfidenceSet OPTIONAL,
		accelSet AccelerationSet4Way,
		brakes BrakeSystemStatus,
		size VehicleSize,
		vehicleClass VehicleClassification,
		-- VehicleClassification includes BasicVehicleClass and other extendible type
		safetyExt VehicleSafetyExtensions OPTIONAL,
		...
		-- 为了方便在新的版本中往现有类型中添加新成员，可用“…”来标记可能以后是其它类型的地方
	}
END
```

### ASN.1 编译开源工具

#### **下载地址**

https://github.com/vlm/asn1c/releases

#### **编译**

```
test -f configure || autoreconf -iv
./configure
make
make install
```

#### **生成.h和c文件**

**新建文件rectangle.asn**

```
RectangleModule DEFINITIONS::=BEGIN
Rectangle::=SEQUENCE{
height INTEGER,
width INTEGER
}
END
```

**生成.c和.h文件**

```
asn1c  rectangle.asn
```

#### 编写测试程序

**编码：**

```
//
// Created by root on 19-7-17.
//
#include <stdio.h>
#include <sys/types.h>
#include "Rectangle.h"

static int write_out(const void*buffer, size_t size,void *app_key) {
    FILE *out_fp = app_key;
    size_t wrote = fwrite(buffer,1,size,out_fp);
    return (wrote == size) ? 0 : -1;
}

int main()
{
    Rectangle_t *rectangle;
    asn_enc_rval_t ec;

    rectangle = calloc(1, sizeof(Rectangle_t));

    rectangle->height = 42;
    rectangle->width = 23;

   const char *filename = "./rect.txt";
   FILE *fp = fopen(filename,"wb");

   ec = der_encode(&asn_DEF_Rectangle,rectangle,write_out, fp);
   fclose(fp);

   xer_fprint(stdout, &asn_DEF_Rectangle,rectangle);

    return 0;
}
```

**解码：**

```
//
// Created by root on 19-7-17.
//
#include <stdio.h>
#include <sys/types.h>
#include "Rectangle.h"
int main()
{
    Rectangle_t *rectangle = 0;
    

    char buf[1024];
    size_t size;

    rectangle = calloc(1, sizeof(Rectangle_t));

    const char *filename = "./rect.txt";
    FILE *fp = fopen(filename,"rb");

    size = fread(buf,1,sizeof(buf),fp);
    fclose(fp);

    ber_decode(0, &asn_DEF_Rectangle,(void **)& rectangle,buf,size);

    xer_fprint(stdout, &asn_DEF_Rectangle,rectangle);

    return 0;
}
```

**输出：**

```
<Rectangle>
    <height>42</height>
    <width>23</width>
</Rectangle>
```