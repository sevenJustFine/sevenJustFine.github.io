---
title: android_root
date: 2025-05-24 19:43:09
tags:
---


### 1.开启开发者模式

    链接点击手机系统版本号,开启开发者模式
    开启开发者模式中的允许OEM解锁

### 2 下载Magisk

github 地址
```shell
https://github.com/topjohnwu/Magisk
```

### 3.小米手机秒解锁BL

1.下载unlocktool,,网址,www.unlocktool.net

2.咸鱼购买临时账号 

3.手机开启开发者模式,开启OEM解锁 ,usb链接手机

4.登录进入unlocktool,点击右侧CONFIG,下载安装libusb 驱动
选择上面的芯片品牌MEDIATEK
 点击,左下方的菜单 PERM | UNLOCKV BL

5.先关机,再同时按开机键+音量减,进入fastboot 模式,这时候在右侧的COM接口能看到手机 

6.再同时按 开机键+音量加 ,看到右侧开始有输出就是在解锁了

### 4.下载红米10A 线刷包

```shell
https://xiaomirom.com/download/redmi-9a-9i-9at-9a-sport-10a-10a-sport-dandelion-stable-V12.5.21.0.RCDCNXM/#china-fastboot
```
从中获取images文件夹中获取文件boot.img

### 5.修补boot.img
上传boot.img到手机

•	打开 Magisk，点击“安装” > “选择并修补一个文件”，选择 boot.img；

•	修补后，会生成一个 magisk_patched-xxxx.img 文件。

### 6.刷入修补后的镜像

将magisk_patched-29000_SH1FS.img 文件上传到电脑  
手机进入 Fastboot 模式  ,
在电脑终端,进入放置img文件的文件夹,然后运行： 
```shell
fastboot flash boot magisk_patched-29000_SH1FS.img
```
再重启验证是否安装成功
```shell
fastboot reboot
```
打开magsik ,显示了版本号,则是root成功