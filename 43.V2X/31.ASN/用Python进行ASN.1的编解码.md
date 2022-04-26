- [用Python进行ASN.1的编解码_gzroy的博客-CSDN博客_asn1 python](https://blog.csdn.net/gzroy/article/details/102824659)

记录一下如何用Python来进行ASN.1的编[解码](https://so.csdn.net/so/search?q=解码&spm=1001.2101.3001.7020)。

首先定义一个ASN.1的[Schema](https://so.csdn.net/so/search?q=Schema&spm=1001.2101.3001.7020)，并保存为visioncontrol.asn文件

```puppet
VISIONUPLOAD DEFINITIONS AUTOMATIC TAGS::= BEGIN 
 
-- Message from the platform
ITSMessageVisionUpload ::= SEQUENCE {
   imageType        ImageType,  
   imageData        OCTET STRING
}
 
ImageType ::= ENUMERATED {
   jpg                              (0),  
   png                              (1)
}
 
END
```

有两种方法来进行编解码

**方法一：**

安装asn1ate， git clone https://github.com/kimgr/asn1ate.git， python setup.py build, python setup.py install

执行命令asn1ate visioncontrol.asn > visioncontrol.py， 来生成对应的Python类

安装pyasn1，git clone http://www.github.com/etingof/pyasn1

之后就可以进行编解码了，非常简便易用。

```python
import visioncontrol
from hexdump import hexdump
from pyasn1.codec.der.encoder import encode
 
vc = visioncontrol.ITSMessageVisionUpload()
vc['imageType']='jpg'
vc['imageData']=b'10100000'
dercode = encode(vc)
 
hexdump(dercode) 
```

这个方法有个缺点，就是pyasn1支持的编码格式比较少，只有BER，DER几种。如果我们想编码其他的格式，例如UPER的话就不行，因此我又找到了方法二。

**方法二：**

安装asn1tools, pip install asn1tools

```python
import asn1tools
 
visionupload = asn1tools.compile_files('cvc-vds-its-visionupload.asn', 'uper')
 
#Read the image as bytesarray
pic = open('test2.jpg', 'rb')
picdata = pic.read()
pic.close()
 
encoded = visionupload.encode('ITSMessageVisionUpload', {'imageType':'jpg', 'imageData':picdata})
 
vision_decoded = visionupload.decode('ITSMessageVisionUpload', encoded)
```

这个方法支持的编码格式多，而且也不需要预先把编码格式转换为Python类，使用更加简单。