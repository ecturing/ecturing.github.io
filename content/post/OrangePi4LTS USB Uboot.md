---
title: "OrangePi4LTS USB Uboot"
date: 2024-05-28T22:55:28+08:00
categories:
    - 服务器运维
tags:
    - Uboot
---

# OrangePi-Build下载
在GitHub中搜索[xunlong-OrangePi-Build](https://github.com/orangepi-xunlong/orangepi-build)，这是OrangePi官方提供的构建工具。主要用于构建Uboot，Kernel和编译内核,该构建系统有版本限制要求，推荐Ubuntu20.04版本，版本过高或过低还有wsl均不支持编译，配置好环境然后下载完成后命令行运行./build.sh进行编译。进入以下界面
![](https://pic.imgdb.cn/item/66557c76d9c307b7e98a3958.png)
选择Uboot单元，并选择自己的开发板后，系统会自动下载依赖然后进行编译。
## 可能遇到的故障
如果仅仅是升级Uboot，编译好后直接会产生deb文件，直接参考手册安装，运行即可，但是对于需要修改启动项来说，需要串口工具进行连接，进入Uboot命令行。官方推荐ch340的USB转串口工具，可是我使用的ch340工具在mobaxterm中存在输入乱码，SecureCRT直接无法输入，流模式已经关闭仍然无效，在网络查询均没有结果，遂尝试降低波特率，OrangePi4LTS的Uboot和系统波特率均为1500000，高波特率有部分USB转串口工具支持不全。波特率有两个阶段修改，1是在Uboot阶段，2是系统启动后阶段，我们修改Uboot阶段波特率以便能进入Uboot命令行。

首先上面的构建工具先要完整构建一遍，构建后进入userpatches文件夹，打开config-default.conf文件，修改IGNORE_UPDATES=""的值为INGORE_UPDATES="yes"，然后进入u-boot文件夹，进入编译的Uboot版本文件夹，然后进入configs文件夹，搜索自己的主板_defconfig配置文件，找到后搜索CONFIG_BAUDRATE字段，修改波特率，然后重新进入编译界面进行编译Uboot。由于OrangePi本身不支持U盘中系统的直接启动，所以我们通过再SD卡或Emmc中烧录Uboot，从Uboot引导U盘的系统。所以，进入u-boot文件夹，进入生成的uboot资源文件夹，拷贝出idbloader.img和u-boot.itb文件，然后进行烧录。
# USB系统准备
使用OrangePi官方提供的系统以及刷写系统教程进行系统刷入到USB设备，此处不再赘述
# 烧录Uboot
对于烧录程序，我们通过对构建系统产生的deb文件进行解包，发现内部存在一个sh脚本存在着对uboot的烧录程序

```shell
DIR=/usr/lib/linux-u-boot-next-orangepi4-lts_3.1.0_arm64
write_uboot_platform ()
{
    if [[ -f $1/rksd_loader.img ]]; then
        dd if=$1/rksd_loader.img of=$2 seek=64 conv=notrunc status=none > /dev/null 2>&1;
    else
        if [[ -f $1/u-boot.itb ]]; then
            dd if=$1/idbloader.img of=$2 seek=64 conv=notrunc status=none > /dev/null 2>&1;
            dd if=$1/u-boot.itb of=$2 seek=16384 conv=notrunc status=none > /dev/null 2>&1;
        else
            if [[ -f $1/uboot.img ]]; then
                dd if=$1/idbloader.bin of=$2 seek=64 conv=notrunc status=none > /dev/null 2>&1;
                dd if=$1/uboot.img of=$2 seek=16384 conv=notrunc status=none > /dev/null 2>&1;
                dd if=$1/trust.bin of=$2 seek=24576 conv=notrunc status=none > /dev/null 2>&1;
            else
                echo "[write_uboot_platform]: Unsupported u-boot processing configuration!";
                exit 1;
            fi;
        fi;
    fi
}
write_uboot_platform_mtd ()
{
    if [[ -f $1/rkspi_loader.img ]]; then
        dd if=$1/rkspi_loader.img of=$2 conv=notrunc status=none > /dev/null 2>&1;
    else
        echo "SPI u-boot image not found!";
        exit 1;
    fi
}
setup_write_uboot_platform ()
{
    if grep -q "ubootpart" /proc/cmdline; then
        local tmp=$(cat /proc/cmdline);
        tmp="${tmp##*ubootpart=}";
        tmp="${tmp%% *}";
        [[ -n $tmp ]] && local part=$(findfs PARTUUID=$tmp 2>/dev/null);
        [[ -n $part ]] && local dev=$(lsblk -n -o PKNAME $part 2>/dev/null);
        [[ -n $dev ]] && DEVICE="/dev/$dev";
    fi
}

```
此段shell主要有三个函数，一个是烧录Uboot到指定设备吗，一个是烧录到SPI里，还有一个是检查写入的设备，这里我们主要参考第一个函数，我们下面的修改后的命令进行烧录uboot,device_name是写入设备的名称，我的是sd卡，在任意版本Linux下使用dd命令烧录Uboot。
```sh
dd idbloader.img of={device_name} seek=64
dd u-boot.itb of={device_name} seek=16384 
```
烧录完成后将sd卡插入到开发板，启动开发板，你会遇到下面的情况，这是你还没有插入带系统的设备
![alt text](https://pic.imgdb.cn/item/66557c76d9c307b7e98a397c.png)
如下面图所示，这个打印的是Uboot中所有的环境变量，从中我们可以看出，bootcmd依赖于一系列环境变量，同时环境变量中提供了几种启动方式，如mmc，pxe，usb，sf等，从这可以看出，这个uboot默认支持USB启动，所以我们在sd卡中烧录好uboot后，直接将刷写好的系统U盘插入USB接口，就可以实现USB启动了
# ![alt text](https://pic.imgdb.cn/item/66557c76d9c307b7e98a39f5.png)