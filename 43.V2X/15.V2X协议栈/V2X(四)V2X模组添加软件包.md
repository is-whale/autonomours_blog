- [V2X(四)V2X模组添加软件包_lljwork2021的博客-CSDN博客](https://blog.csdn.net/qq_40723777/article/details/125337974?ops_request_misc=%7B%22request%5Fid%22%3A%22165571968716782184648013%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fcode.%22%7D&request_id=165571968716782184648013&biz_id=&utm_medium=distribute.pc_search_result.none-task-code-2~code~first_rank_ecpm_v1~code_v1-4-125337974-2-null-null.nonecase&utm_term=V2X)

## 1. 简述

同事在开发某厂商的V2X模组时，需要添加sqlite等一系列库，V2X模组提供了工具链。他们的方案是下载源码手动编译，有时候第三方软件有很多又依赖其他的库文件，导致移植过程非常的繁琐，问我有没有好的方案。我经常用yocto和buildroot配置根文件系统，但是对于这种情况，buildroot明显是最佳方案。

## 2. 问题

我查看厂商提供的文档，芯片为A73架构，64位，发现他们编译内核使用的工具链是aarch64-linux-gnu，而在应用开发提供的工具链是arm-linux，提供的开发库也是32位的。我之前开发imx8和rk3399，uboot、kernel、rootfs都是用的aarch64-linux-gnu工具链。查资料得知，在Linux内核中可以设置支持32位应用。V2X提供的内核已经配置此选项。

```c
linux$ make menuconfig
> Kernel Features
[*] Kernel support for 32-bit EL0

> Kernel Features > Kernel support for 32-bit EL0
---Kernel support for 32-bit EL0
[*] Enable kuser helpers page for 32-bit applications 
```

## 2. 芯片架构

```c
$ git clone git://git.buildroot.net/buildroot

buildroot$ make menuconfig
/* Targe options ---> */
Target Architecture (ARM (little endian) ) ---> /* 重点 */
Target Binary Format (ELF) --->
Target Architecture Variant (cortex-A73) --->
Target ABI (EABIhf) --->
Floating point strategy (NEON/VFPv4) --->
ARM instruction set (ARM) --->
```

V2X的模组是aarch64，这里我却选择了ARM，buildrot工具链会根据架构添加前缀。我们需要的是32位的库。

## 4. 工具链

在ARM Linux的开发中，人们趋向于使用Linaro工具链团队维护的ARM工具链。该模组提供的也是Linaro的工具链。知道工具链后，可以在buildroot指定我们的工具链或者选择buildroot提供的工具链。

```c
~/buildroot$ make menuconfig
    Toolchain type (External toolchain)  ---> 
    *** Toolchain External Options *** 
    Toolchain (Linaro ARM 2018.05)  --->
    Toolchain origin (Toolchain to be downloaded and installed)  --->
[ ] Copy gdb server to the Target
	  *** Host GDB Options *** 
[ ] Build cross gdb for the host
	  *** Toolchain Generic Options ***
[ ] Copy gconv libraries
()  Extra toolchain libraries to be copied to target
()  Target Optimizations
()  Target linker options
[ ] Register toolchain within Eclipse Buildroot plug-in
```

## 6. 软件库

选择我们需要的库进行编译，buildroot生成的根文件系统在buildroot/output/images/rootfs.tar，但是芯片模组只需要我们生成的库，而非整个根文件系统。当然我们可以解压rootfs.tar，在/usr/lib目录下找到。我们也可以在output/host/arm-buildroot-linux-gnueabihf/sysroot/下找到编译的库。

```c
yangleilei@yangleilei:~/buildroot-test/buildroot/output/host/arm-buildroot-linux-gnueabihf/sysroot$ find -name *sqlite*
./usr/lib/libsqlite3.so
./usr/lib/pkgconfig/sqlite3.pc
./usr/lib/libsqlite3.la
./usr/lib/libsqlite3.so.0
./usr/lib/libsqlite3.so.0.8.6
./usr/include/sqlite3ext.h
./usr/include/sqlite3.h
./usr/bin/sqlite3
./usr/share/man/man1/sqlite3.1
```