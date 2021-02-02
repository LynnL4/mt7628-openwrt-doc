



![image-20210202144229582](G:\Seeed\desktop\mt7628-openwrt-doc\更新固件\res\image-20210202144229582.png)

### 1. 通过USB转UART连接到MT7628的Uart0（P17 P18）

- **打开串口调试软件， 波特率设置115200**

### 2. 通过USB-micro C线连接到MT7628的USB Power

可以看到系统启动信息在串口调试软件串口输出

系统启动后，输入iwinfo, 可以看到

 ![](G:\Seeed\desktop\mt7628-openwrt-doc\更新固件\res\微信图片_20210202145244.png)



1）通过PC连接到该AP WT-LEDE

2）使用tftpd32工具（也可以用其它代替）在PC搭建一个tftp server

 ![](G:\Seeed\desktop\mt7628-openwrt-doc\更新固件\res\image-20210202145919627.png)

Current Directory 就是存放固件的文件路径，点击show Dir可以看到路径的地址

3）在串口调试工具terminal下键入以下启动运行更新uboot功能

```shell
cd /tmp
tftp -g 192.168.20.xxx -r sysupgrade.bin #ip 地址可以在PC端通过 ipconfig查看
sysupgrade -n /tmp/sysupgrade.bin
```

系统更新完成后系统会重启，或者手动断电重启

4）重复以上1）, 2）操作，在串口调试工具terminal下键入更新uboot功能

```shell
cd /tmp
tftp -g 192.168.1.xxx -r uboot.bin  #ip 地址可以在PC端通过 ipconfig查看
mtd write uboot.bin u-boot 
sync
```

完成更新后

uboot断电重启, 接下来继续更新固件

5）更新固件

- 重复操作2）

- 将USB转UART接入MT7628 UART2(P8 P9)， 通过网线将mt7628与PC进行连接。

- 配置PC端IP地址

![](G:\Seeed\desktop\mt7628-openwrt-doc\更新固件\res\微信截图_20210202163651.png)

- 重新上电

  - 上电后，立即在串口调试串口键入2

  ```
  Please choose the operation:
     1: Load system code to SDRAM via TFTP.
     2: Load system code then write to Flash via TFTP.
     3: Boot system code via Flash (default).
     4: Entr boot command line interface.
     7: Load Boot Loader code then write to Flash via Serial.
     9: Load Boot Loader code then write to Flash via TFTP.
     
     You choosed 2
  ```

  - 键入y进入网口更新固件模式

    **device IP还有Server IP采用默认配置直接回车**

     **Linux Kernel filename 键入 lks7628.img**

  ```
  2: System Load Linux Kernel then write to Flash via TFTP.
   Warning!! Erase Linux in Flash then burn new one. Are you sure?(Y/N)
   Please Input new ones /or Ctrl-C to discard
          Input device IP (10.10.10.123) ==:10.10.10.123 #回车
          Input server IP (10.10.10.3) ==:10.10.10.3 #回车
          Input Linux Kernel filename () ==:lks7628.img #输入固件名称lks
  ```

  此时出现以下信息升级完成后出现， 升级完成后就会重启

  ![](G:\Seeed\desktop\mt7628-openwrt-doc\更新固件\res\微信截图_20210202163557.png)

  

  

  

  

