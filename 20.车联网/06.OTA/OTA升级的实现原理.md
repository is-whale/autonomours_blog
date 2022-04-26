- [OTA升级的实现原理_aFakeProgramer的博客-CSDN博客_ota升级实现](https://blog.csdn.net/usstmiracle/article/details/123200234)

## 一、简介

### 1.1 概念

OTA：Over-the-Air Technology，即空中下载技术。

OTA升级：通过OTA方式实现固件或软件的升级。

只要是通过[无线通信](https://so.csdn.net/so/search?q=无线通信&spm=1001.2101.3001.7020)方式实现升级的，都可以叫OTA升级，比如网络/蓝牙。

通过有线方式进行升级，叫本地升级，比如通过[UART](https://so.csdn.net/so/search?q=UART&spm=1001.2101.3001.7020)，USB或者SPI通信接口来升级设备固件。

![img](https://img-blog.csdnimg.cn/img_convert/a3c0eb1899a285c15ae438db70e36bbb.png)

### 1.2 优点

> 1.通过OTA方式，可以对分布在各地的设备进行软件升级，而不必让运维人员各地奔波。
>
> 2.物联网平台支持通过OTA方式进行设备固件升级，是智能设备修复系统漏洞、实现系统升级的手段。
>
> 3.在迅速变化和发展的物联网市场，新的产品需求不断涌现，因此对于智能硬件设备的更新需求就变得空前高涨，设备不再像传统设备一样一经出售就不再变更。通过固件升级用户提供更好的服务。

### 1.3 实现原理

**核心流程：**

> 1.制作升级包
>
> 2.下载升级包
>
> 3.验签升级包
>
> 4.更新程序



![img](https://img-blog.csdnimg.cn/img_convert/950a712c72fa16121681f45514f34952.png)

**下载方式：**

不管采用OTA方式还是有线通信方式升级，下载升级包的方式包括后台式下载和非后台式下载两种模式。

**后台式下载：**

在升级的时候，新固件在后台悄悄下载，即新固件下载属于应用程序功能的一部分，在新固件下载过程中，应用可以正常使用，也就是说整个下载过程对用户来说是无感的，下载完成后，系统再跳到BootLoader程序，由BootLoader完成新固件覆盖老固件的操作。比如智能手机升级Android或者iOS系统都是采用后台式方式，新系统下载过程中，手机可以正常使用。

![img](https://img-blog.csdnimg.cn/img_convert/941f6f06c76b0e7feb228f0a04331483.png)

**非后台式下载：**

在升级的时候，系统需要先从应用程序跳入到BootLoader程序，由BootLoader进行新固件下载工作，下载完成后BootLoader继续完成新固件覆盖老固件的操作，至此升级结束。早先的功能机就是采用非后台来升级操作系统的，即用户需要先长按某些按键进入bootloader模式，然后再进行升级，整个升级过程中手机正常功能都无法使用。

![img](https://img-blog.csdnimg.cn/img_convert/cc381cd752e091ddfe68b9e9d99fd4e9.png)

**新旧固件覆盖模式：**

新固件替换老固件覆盖的两种方式：双区模式和单区模式。

**双区模式：**

双区模式中老固件和新固件在flash中各占一块bank（存储区）。假设老固件放在bank0（运行区）中，新固件放在bank1（下载区）中，升级的时候，应用程序先把新固件下载到bank1中，只有当新固件下载完成并校验成功后，系统才会跳入BootLoader程序，然后擦除老固件所在的bank0区，并把bank1的新固件拷贝到bank0中。

后台式下载必须采用双区模式进行升级。

> 优点：升级过程中出现问题或者新固件有问题，它还可以选择之前的老固件老系统继续执行而不受其影响。
>
> 缺点：多占用flash空间的一个存储区，在系统资源比较紧张的时候较为困难。

![img](https://img-blog.csdnimg.cn/img_convert/c3be27acb0f3ddfb7426cc6d9ee14baa.png)



**单区模式：**

单区模式的非后台式下载只有一个bank0（运行区），老固件和新固件共享这一个bank0。升级的时候，进入bootloader程序后先擦除老固件，然后直接把新固件下载到同一个bank中，下载完成后校验新固件的有效性，新固件有效升级完成，否则要求重来。

优点：

跟双区模式相比，单区模式节省了Flash空间的一个bank，在系统资源比较紧张的时候，单区模式是一个不错的选择。

缺点：

如果升级过程中出现问题或者新固件有问题，单区模式碰到这种情况就只能一直待在bootloader中，然后等待再次升级尝试，此时设备的正常功能已无法使用，从用户使用这个角度来说，可以说此时设备已经“变砖”了。

相比较，双区模式虽然牺牲了很多存储空间，但是换来了更好的升级体验。



![img](https://img-blog.csdnimg.cn/img_convert/d143cdd4facc9ad2de203985d9932e14.png)



## 二、MCU OTA升级

以MCU（微控制器）固件升级为例，讲解嵌入式裸机程序的OTA升级。由于裸机固件是固化在设备的存储器（如flash）中，即存储器中保存的是机器码，对MCU进行OTA固件升级，也就是要实现通过OTA方式将存储器中旧固件的机器码替换为新固件的机器码。



![img](https://img-blog.csdnimg.cn/img_convert/8a4f6720517fbae8d1bcbbee7a7c5098.png)

**数字签名**

**签名：**

A给B发送消息，A先计算出消息的消息摘要，然后使用自己的私钥加密消息摘要，被加密的消息摘要就是签名。

**验签：**

B收到消息后,也会使用和A相同的方法计算消息摘要，然后用A的公钥解密签名，并与自己计算出来的消息

摘要进行比较，如果相同则说明消息是A发送给B的，同时,A也无法否认自己发送消息给B的事实。

(B使用A的公钥解密签名文件的过程，叫做"验签")



![img](https://img-blog.csdnimg.cn/img_convert/47262874cfdd21524dea2fffd0e7e6d1.png)

密码学基础概念：

1.什么是消息摘要？

2.什么是非对称加解密？私钥与公钥？

3.什么是数字签名？



**数字签名的作用：**

保证数据完整性，机密性和发送方角色的不可抵赖性。



**消息摘要函数：**

MD4、MD5、SHA-1、SHA-256、SHA-384、SHA-512

数字签名算法：

RSA、Rabin方式、ElGamal方式、DSA



### 2.1 制作升级包

通过签名工具使用签名算法对固件进行数字签名，签名后的文件即为升级包。

升级包的内容一般包括firmware、header和signature value。

Firmware:固件

Header:头部信息。存放配置信息，如版本号、产品类型等。

Signature value:签名值。对firmware和header签名后的值。



![img](https://img-blog.csdnimg.cn/img_convert/05eb4ad506b17185031fa1c0041908c5.png)

**签名工具：**

上位机软件，能计算固件的签名值，并将固件打包为升级包的格式。



**固件签名：**

上位机软件先计算整个固件的消息摘要，使用非对称密码的私钥对摘要进行加密，

被加密后的消息摘要数据就是签名值。



**固件签名的意义：**

计算hash值可以识别固件是否被篡改和伪装，确保固件的完整性。

使用非对称秘钥签名方便后续验证升级包身份的合法性。



### 2.2 下载升级包

根据上位机软件和MCU设备约定的通信协议，上位机软件将升级包通过OTA方式发送给MCU设备，

MCU设备收到数据后，根据通信协议解析出升级包的数据，并将升级包的数据保存到存储器中。



**通信协议的作用：**

通讯双方约定俗成地用于数据交流的格式。

下载的方式：

1.在应用程序中下载：后台式

2.在BootLoader中下载：非后台式



### 2.3 验签升级包

MCU设备接收完所有的升级包后，先计算升级包中固件的摘要，然后使用非对称秘钥的

公钥解密升级包的签名值，如果解密出来的固件摘要与自己计算的摘要相同，则验签成功。



### 2.4 更新固件

验签成功保证了固件的完整性和合法性后，MCU设备从应用程序进入BootLoader程序，

在BootLoader程序中将flash中的新固件数据搬运到旧固件的存储区，将其覆盖。

然后BootLoader程序启动固件运行，此时固件为新固件。



**flash固件数据更新：**

擦除flash，写flash。





## 三、Linux OTA升级

**Linux系统的组成：**

主要由三大部分组成，包括uboot(引导启动程序)、kernel(内核)和rootfs(根文件系统)。



**三者在flash中的分区如下：**

应用程序存放于rootfs。



![img](https://img-blog.csdnimg.cn/img_convert/e5aa225aac210c3f99002a103e3fcb25.png)

**Linux系统的启动流程：**

![img](https://img-blog.csdnimg.cn/img_convert/c1c31fc9baf486ff9db647f3591b9a1b.png)

### 3.1 系统升级

Linux系统由uboot\kernel\rootfs三大部分组成，对Linux系统进行升级，也就是对flash中这三个分区的数据进行更新替换。

由于uboot\kernel\rootfs在flash分区中是以二进制数据存储的，与MCU固件在flash中存的是二进制数据一样，包括uboot\kernel\rootfs的升级文件也是以二进制数方式直接写入到对应的Flash分区。其升级方式与MCU固件的升级原理基本是一致的。

一般可在uboot中下载升级包来升级uboot\kernel\rootfs ，与MCU在BootLoader程序中完成升级类似。

![img](https://img-blog.csdnimg.cn/img_convert/e6fc7fb3f1e4795b76442b7eb5a58099.png)

### 3.2 应用程序升级

在Linux系统中，应用程序是存放在文件系统中，并以可执行程序文件的方式存在，其在系统中就是文件，这与MCU固件存放在flash分区的方式不同。

应用程序的升级流程与MCU固件、Linux系统升级基本一致。应用程序的升级除了可以升级可执行文件外，还可以升级配置文件等。

**应用程序升级流程：**

制作升级包（打包签名工具）、下载升级包（下载工具）、升级包验签、程序更新

**与MCU OTA升级区别：**

制作升级包：将应用程序相关的文件（可执行程序、库文件、配置文件等）打包为压缩包

作为一个整体再进行签名。

![img](https://img-blog.csdnimg.cn/img_convert/07616f2821f2205972fdaa2f32619166.png)

升级包下载和验签通过后，将压缩包解压，可以得到应用程序的相关文件。

应用程序的更新，可以通过启动应用程序的程序来更新，如启动脚本、启动程序，类似MCU升级的BootLoader程序作用。

**更新方式：**

1.直接覆盖旧程序；

2.保留旧程序，执行新程序；

**直接覆盖旧程序：**

![img](https://img-blog.csdnimg.cn/img_convert/cedd2f70ba7398c83783bb3d33cd2675.png)



**保留旧程序，执行新程序：**

如ping\pong操作

![img](https://img-blog.csdnimg.cn/img_convert/83a5418b69db778e6fb646b8498567df.png)

## 四、总结

**OTA升级的核心：**

![img](https://img-blog.csdnimg.cn/img_convert/86b1a3327b65b6325ac832fc7c1c34e2.png)