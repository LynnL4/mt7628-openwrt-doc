### 简介

Seeed MT7628 development board 是 [MediaTek LinkTt 7688](https://labs.mediatek.com/en/platform/linkit-smart-7688) 的替代产品，可以完全兼容LinkIt 7688。

Seeed MT7628 development board 基于 [OpenWrt Linux](https://github.com/openwrt) 发行版的开发时开发平台，拥有大量的第三方软件支持。

## 编译生成固件

这一章介绍如何编译生产 LinkIt Smart 7628的固件和启动引导程序（bootloader）

### 编译固件

Seeed MT7628 development board开发平台支持Makefile编译，您可以使用make命令来编译固件。

1）安装[Ubuntu Linux 20.04](https://ubuntu.com/)

- [虚拟机](http://virtualbox.org/)

- [WSL(推荐)](https://docs.microsoft.com/en-us/windows/wsl/compare-versions#whats-new-in-wsl-2)

  

2）安装必要的工具包

```shell
$ sudo apt-get install git g++ libncurses5-dev subversion libssl-dev gawk libxml-parser-perl unzip
```

3）下载支持Seeed MT7628 development board的OpenWrt 源码

```shell
$ git clone https://github.com/Seeed-Studio/seeed-linux-openwrt
```

4）创建配置文件feeds.conf, 由默认的模板文件feed.conf.default复制而来

```shell
$ cd seeed-linux-openwrt
$ cp feeds.conf.default feeds.conf
```

5）添加Seeed MT7628 development board feed路径到feeds.conf

```shell
$ echo "src-git linkit https://github.com/LynnL4/linkit-smart-7688-feed.git;mtk7628" >> feeds.conf
```

6）更新feed.conf中的feed信息

```shell
$ ./scripts/feeds update
```

7）将feed包安装到openwrt工程中

```shell
$ ./scripts/feeds install –a
```

8）准备openwrt编译配置文件，来源于默认的模板文件seeed-mt7628.config

```shell
$ cp seeed-mt7628.config .config
$ make menuconfig
```

9）选择正确的目标配置文件，以及根据需求增删模块

- 目标系统（Target System）:  Ralink RT288x/RT3xxx
- 子系统（Subtarget）:  MT7628 based boards
- 目标配置文件（Target Profile）: Seeed MT7628 development board

10）保存并且退出

11）使用make命令开始编译openwrt固件

```shell
$ make V=99
```

12）编译完成后，生成的固件文件在以下路径：

***bin/ramips/openwrt-ramips-mt7628-Seeed-MT7628-squashfs-sysupg.bin***



### Seeed MT7628 development board 编程开发

Seeed MT7628 development board 支持C/C++、Python、Node.js进行设备应用程序开发。

#### 环境搭建

##### 1. C/C++

| 软件包                                                       | 版本  |
| ------------------------------------------------------------ | ----- |
| [OpenWrt SDK for C/C++ for Linux](https://labs.mediatek.com/en/download/AGSSkG38) | 0.9.1 |
| [OpenWrt SDK for C/C++ for OS X](https://labs.mediatek.com/en/download/9iTMnGPU) | 0.9.1 |

1）根据实际情况下载对应的SDK，以Linux为例

```shell
wget https://labs.mediatek.com/en/download/AGSSkG38 -O SDK.tar.bz2
sudo tar -xvjf SDK.tar.bz2 && cd SDK
```

2) 下载 [example package file](https://labs.mediatek.com/en/download/kJWJnDta) ， 解压并把示例工程复制到  ***SDK/package/*** 目录下

```shell
SDK/package
   +helloworld              # Name of the package
        -Makefile           # This Makefile describes the package
        +src
            -Makefile       # This Makefile builds the binary
            -helloworld.c   # C/C++ source code
```

3)  编译示例程序

- 查看示例程序helloworld.c

```C
/****************
* Helloworld.c
* The most simplistic C program ever written.
* An epileptic monkey on crack could write this code.
*****************/
#include <stdio.h>

int main(void)
{
	printf("Hell! O' world, why won't my code compile?\n\n");
	return 0;
}

```

- 在**SDK/**目录下，键入

  ```shell
  $ make package/helloworld/compile
  ```

- 编译完成后进入 ***SDK/bin/ramips/packages/base*** 目录

  找到*helloworld_1.0.0-1_ramips_24kec.ipk.*

- 将 *helloworld_1.0.0-1_ramips_24kec.ipk* 拷贝到 Seeed MT7628 development board. 方法参考TO-DO[x-x]

- 在 Seeed MT7628 development board上找到*helloworld_1.0.0-1_ramips_24kec.ipk* 所在的位置

  ```shell
  $ opkg install helloworld_1.0.0-1_ramips_24kec.ipk.
  ```

- 在 Seeed MT7628 development board 的 Terminal 上键入 **helloworld**

  TO-DO:[图片]

4）在 Seeed MT7628 development board 上

- 安装libmraa (如已经安装, 请忽略此步)

  ```shell
  $ opkg update
  $ opkg install libmraa
  ```

5）更多 C/C++ 应用程序开发请参考

- [https://wiki.openwrt.org/doc/howto/obtain.firmware.sdk](https://wiki.openwrt.org/doc/howto/obtain.firmware.sdk)

- http://gargoyle-router.com/old-openwrt-coding.html
- TO-DO: C/C++开发 

##### 2. python

目前python的应用开发方式仅支持python2

1）安装libmraa (如已经安装, 请忽略此步)

```shell
$ opkg update
$ opkg install libmraa
```

2）安装python2

```shell
$ opkg install python2
```

3）在terminal下启动python2的 console

```shell
$ python2
```

4）加载 libmraa

```python
import mraa # 引入libmraa
import time

x = mraa.Gpio(44)
x.dir(mraa.DIR_OUT)

while True:
    x.write(1)
    time.sleep(0.2)
    x.write(0)
    time.sleep(0.2)

```

可以见到 Wi-Fi LED 以0.2S的间隔闪烁

4）更多mraa使用方法请参考

- [API — mraa v2.0.0-4-gd320776 documentation (intel.com)](http://iotdk.intel.com/docs/master/mraa/python/mraa.html)

- TO-DO:python开发

##### 3. node.js

1）安装libmraa (如已经安装, 请忽略此步)

```shell
$ opkg update
$ opkg install libmraa
```

2）安装node.js

```shell
$ opkg install node
```

3）创建node.js工程

```shell
$ mkdir app  
$ cd app  
$ npm init 
```

- [ ] TO-DO issue：不能实现对硬件的操作

#### 开发示例

##### 1. C/C++

请参考[mraa: libmraa - Low Level Skeleton Library for Communication on GNU/Linux platforms (intel.com)](http://iotdk.intel.com/docs/master/mraa/)

- GPIO
- I2C
- Uart
- SPI



