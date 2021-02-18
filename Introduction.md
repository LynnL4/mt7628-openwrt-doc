## Introduction

The Seeed MT7628 development board is a replacement for the [MediaTek LinkTt 7688](https://labs.mediatek.com/en/platform/linkit-smart-7688) and is fully compatible with the LinkIt 7688(Pin to Pin).

The Seeed MT7628 development board is based on the release development platform of the [OpenWrt Linux](https://github.com/openwrt) distribution and has extensive third-party software support.

## Compiling to Generate Firmware

This chapter describes how to compile the firmware and bootloader for the Seeed MT7628 development board.

Seeed MT7628 development board development platform supports Makefile compilation, you can use make command to compile the firmware.

1. Install [Ubuntu Linux 20.04](https://ubuntu.com/)

   - [Virtual Machine](http://virtualbox.org/)
   - [WSL (recommended)](https://docs.microsoft.com/en-us/windows/wsl/compare-versions#whats-new-in-wsl-2)

2. Install the necessary toolkit
   
   ```shell
   $ sudo apt-get install git g++ libncurses5-dev subversion libssl-dev gawk libxml-parser-perl unzip
   ```

3. Download the OpenWrt source code that supports the Seeed MT7628 development board

   ```shell
   $ git clone https://github.com/Seeed-Studio/seeed-linux-openwrt
   ```

4. Create a configuration   file `feeds.conf`, copied from the default template file feed.conf.default

   ```shell
   $ cd seeed-linux-openwrt
   $ cp feeds.conf.default feeds.conf
   ```

5. Add Seeed MT7628 development board feed path to `feeds.conf`

   ```shell
   $ echo "src-git linkit https://github.com/LynnL4/linkit-smart-7688-feed.git;mtk7628" >> feeds.conf
   ```

6. Update the feed information in feeds.conf

   ```
   $ ./scripts/feeds update
   ```

7. Install the feed package into the openwrt project

   ```shell
   $ ./scripts/feeds install –a
   ```

8. Prepare the openwrt compilation configuration file, derived from the default template file seard-mt7628.config

   ```shell
   $ cp seeed-mt7628.config .config
   $ make menuconfig
   ```

9.  Select the correct target configuration file, and add and remove modules as required

   - Target System:  Ralink RT288x/RT3xxx
   - Subtarget:  MT7628 based boards
   - Target Profile: Seeed MT7628 development board

10. Save and exit

11. Use make command to start compiling openwrt firmware

    ```shell
    $ make V=99
    ```

12. fter compiling, the resulting firmware file is in the following path.

***bin/ramips/openwrt-ramips-mt7628-Seeed-MT7628-squashfs-sysupg.bin***