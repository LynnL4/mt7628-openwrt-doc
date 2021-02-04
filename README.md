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

- GPIO 操作

```shell
$ ls /sys/class/gpio
$ echo [n] /sys/class/gpio/export # 初始化GPIO [n] 是引脚序号， 例如 0，1，2..
$ echo [out/in] /sys/calss/gpio[n]/direction # 配置GPIO工作模式，out:输出 in:输入
$ echo 1 /sys/class/gpio[n]/value # 将gpio[n]拉高
$ echo 0 /sys/class/gpio[n]/value # 将gpio[n]拉低
$ cat /sys/class/gpio[n]/value # 读取gpio[n]的值
$ echo [n] > /sys/class/gpio/unexport # 关闭gpio[n]
```

请参考[mraa: libmraa - Low Level Skeleton Library for Communication on GNU/Linux platforms (intel.com)](http://iotdk.intel.com/docs/master/mraa/)

- GPIO
- I2C
- Uart
- SPI



##### 2. python

- GPIO

To control the GPIO pins, initialize the pin as GPIO pin and set its *mode*. The simplest operation mode is OUTPUT — set the pin to **HIGH** or **LOW** to enable or disable external switches or to form signal patterns.

```python
import mraa  
pin = mraa.Gpio(2)    
# Initialize GPIO2 (P10 on LinkIt Smart 7688 board)  
pin.dir(mraa.DIR_OUT) # set as OUTPUT pin  
```

Then, call *pin.write(0)* to set the pin state to **LOW** or call *pin.write(1)* to set the pin state to **HIGH**. To make the Wi-Fi LED blink periodically, set pin 44 (*WLED_N*) to GPIO mode and execute the following code:

```python
import mraa  
import time    
# Refer to the pin-out digram for the GPIO number to silk print mapping  
# in this example the number 44 maps to Wi-Fi LED.  
pin = mraa.Gpio(44)  
pin.dir(mraa.DIR_OUT)    
while True:
    pin.write(1)
    time.sleep(1)
    pin.write(0)
    time.sleep(1) 
```

There’s another GPIO mode which is **INPUT** and it takes a digital signal input from the pin and maps to 1s and 0s. This example continuously prints the value received from **P10** on the board. You can short **3V3** and **P10** to observe the changes in values.

```python
import mraa  
import time    
# Refer to the pin-out diagram for the GPIO number to silk print mapping  
# in this example the number 2 maps to P10 on LinkIt Smart 7688 board  
pin = mraa.Gpio(2)  
pin.dir(mraa.DIR_IN)    
while True:
    print "P10 state:", pin.read()
    time.sleep(0.3)
```

Finally, an interrupt service routine (ISR) can be installed to the pin and invoked when the value of the input pin **P10** (**GPIO2**) is changed. Call ISR API with the trigger type you want to register and the function to be called. Note that the function runs in a different thread.

```python
import mraa  
import time    
def callback(userdata):      
    print "interrupt triggered with userdata=", 
userdata    
pin = mraa.Gpio(2)  
pin.dir(mraa.DIR_IN)  
pin.isr(mraa.EDGE_BOTH, callback, None)    
while(True):      
    time.sleep(1)      
    # simply wait for interrupt  
```

**API Reference**



- PWM

Use PWM module to generate a pulse width modulated signal patterns. This is useful to control actuator peripherals such as servo motors. To use PWM, initialize it on a certain pin such as GPIO. Note that only **GPIO18, GPIO19, GPIO20** and **GPIO21** support PWM on the LinkIt Smart 7688 development board. To control the PWM pattern, several parameters are required, including:

**Period**
This defines the carrier frequency of the modulation. It’s controlled by period(), period_ms() and period_us() APIs.

**Duty cycle or pulse width**
These two parameters are related to each other and usually you only need to set one of them. Duty cycle is controlled by write API with a value from 0.0 to 1.0, where 0.0 is 0% of duty cycle and 1.0 is 100% of duty cycle. Pulse width also defines the pattern in a different unit: the "uptime" of the signal in time units. This is defined by pulsewidth(), pulsewidth_ms(), and pulsewidth_us() APIs.

The following example generates a 500Hz PWM signal with 25% duty cycle on pin P26.

```python
import mraa    
pin = mraa.Pwm(18)  # initialize on GPIO18 (pin P26)
pin.period_ms(2)    # set PWM frequency to 500Hz (2ms period)
pin.enable(True)    # enable PWM output
pin.write(0.25)     # set duty cycle to 25%  
```

- I2C

Inter-Integrated Circuit (I2C) is a widely used protocol among peripherals. It consists of 2 signal pins — usually named SDA and SCL. LinkIt Smart 7688 development board has one set of I2C on **GPIO4** (**P21**) and **GPIO5** (**P20**) as SCL and SDA, respectively. The way I2Cs are initialized is slightly different from GPIO modules. Instead of using pins, I2Cs are initialized based on the **device index**. Since there is only one set of I2C master device on the board, you can simply pass **0**, and it's always on pins **GPIO4** and **GPIO5**.

```python
import mraa 
i2c = mraa.I2c(0) 
```

I2C is capable of connecting multiple slave devices to a single I2C master. Each slave device is identified by a 7-bit address. The following example scans for a Seeed Studio 3-Axis Digital Accelerometer attached to the LinkIt Smart 7688 development board by reading the device ID from its registers.

```python
import mraa
 
i2c = mraa.I2c(0)
# Grove - 3-Axis Digital Accelerometer(+-16g)
# is a ADXL345 configured to I2C address 0x53.
i2c.address(0x53)
# The device ID should be 
if 0xE5 == i2c.readReg(0x00):
 print "Grove - 3-Axis Digital Accelerometer found on I2C Bus"
else:
 print "Grove - 3-Axis Digital Accelerometer not found" 
```

For more details on the I2C API, refer [here](http://iotdk.intel.com/docs/master/mraa/python/mraa.html#i2c). To drive an I2C device, you need to operate multiple I2C sequences. The LinkIt Smart 7688 development board also has pre-installed [libUPM](http://labs.mediatek.com/site/global/developer_tools/mediatek_linkit_smart_7688/training_docs/peripherals_7688/using_upm_python/index.gsp) — a set of driver repository built upon libmraa. Please see [here](https://github.com/intel-iot-devkit/upm/blob/master/examples/python/adxl345.py) for a driver example of Seeed Studio 3-Axis Digital Accelerometer.

- SPI

Serial Peripheral Interface (SPI) can also be used to control peripheral devices. LinkIt Smart 7688 development board consists of 4 pins — **SPI_MOSI** (**P22**), **SPI_MISO** (**P23**), **SPI_CLK** (**P24**) and **SPI_CS1** (**P25**).

It’s important to note that the SPI device is also used for communicating with the on-board internal flash storage. Therefore, developers should access the SPI functionality through SPI modules only and avoid treating the SPI pins as general GPIO. Otherwise, the flash storage may not operate correctly.

The SPI module in libmraa is initialized by the **device index** instead of pin number:

```python
import mraa  
spi = mraa.Spi(0)
spi.frequency(200000)
while True:
    spi.writeByte(0x55)
```

**More Detail refer [API Referance](http://iotdk.intel.com/docs/master/mraa/python/mraa.html)**