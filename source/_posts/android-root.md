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


开机状态重启进入fastboot
```shell
adb reboot bootloader
```




双参登录测试
````
e5589f47a5e84a1c873dbce83709d863-4752898956----dfb02eeb2c5b81d0eae4045f7dcf8300----ANDROID_52942458fbd55fbd', requestBody 'NOT SUPPORT'----DFP40694923474658F6D91DE87B2A1CC9FD2F8147EDED4A782A3C3A4700E9C3D----Cg9rdWFpc2hvdS5hcGkuc3QSoAEkYdJJ38uIUsKhAqsx-ItlDXlTJIi9gVxuJLIvDgOPmQmI6MyDjf8iyu3TxTbuvACOESZ7Zz1tIAKIf97e4Pfk9WNP6dOJUcZ8eK-4vmrnQ8WixfWZsS--O0SbopRPrWPRgzihcWFzW-7QgjANxuG6xhnrzMj_Tp7sa_s0iC5uaiAUbWlI5Pku1ZQAXQiqi-kTurxLWnTZXdsdqqEU8FC1GhJmQmJkX-pJEqgjQbtJv1UuVkwiICWRwU_RQ8MZ44YXX4PAAcGnzenga_0yx7hWzBom6szaKAUwAQ
````
安装jadx
```shell
brew install jadx
```
mac上打开jadx图形界面
```shell
jadx-gui
```
安装apktool
```shell
brew install apktool
```
反编译
```shell
apktool d douyin-19-8-0.apk -o dy_src -r
```

修改smail 代码后再打包
```shell
apktool b dy_src -o douyin_mod.apk -r
```
 

添加java到环境变量

```shell
vim ~/.zshrc

export JAVA_HOME="/opt/homebrew/opt/openjdk/libexec/openjdk.jdk/Contents/Home"
export PATH="$JAVA_HOME/bin:$PATH"

source ~/.zshrc
```

生成临时的keystore
```shell

keytool -genkeypair -v -keystore temp-keystore.jks -keyalg RSA -keysize 2048 -validity 10000 -alias temp-key
```

使用临时的key 给apk签名
```shell
/opt/homebrew/opt/openjdk/bin/jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 \
  -keystore temp-keystore.jks \
  -storepass 123456 \
  -keypass 123456 \
  -signedjar douyin_mod_signed.apk douyin_mod.apk temp-key
```
对apk对齐优化(可选)
```shell
zipalign -v 4 douyin_mod_signed.apk douyin_mod_aligned.apk
```





-----

## frida

安装frida
```shell
pip install frida-tools
```

查询cpu类型

```shell
adb shell getprop ro.product.cpu.abi
```

红米10A的cpu架构是armeabi-v7a

github下载frida-server
```shell
https://github.com/frida/frida/releases

frida-server-17.0.5-android-arm.xz
```

解压
```shell
xz -d frida-server-17.0.5-android-arm.xz
```

推送到手机
```shell
adb push frida-server-17.0.5-android-arm /data/local/tmp/frida-server
adb shell chmod 755 /data/local/tmp/frida-server
adb shell "/data/local/tmp/frida-server &"
```
直接adb shell "/data/local/tmp/frida-server &" 会没有权限   
在安装了magisk的情况下,使用adbshell 切换到root用户

```shell
abd shell
su
adb shell "/data/local/tmp/frida-server &"

```
上面的已root运行frida-server的方式会问题,会报错  
修改为安装magisk的模块,直接将frida注入  
firda-inject的magisk的模块下载地址  
```shell
https://github.com/ViRb3/magisk-frida/releases
```
确认 Frida-Server 在设备上正常运行（可跳过 frida-ps）：
```shell
adb forward tcp:27042 tcp:27042
adb forward tcp:27043 tcp:27043
```

```shell
frida -U -n com.ss.android.ugc.aweme -l bypass_ssl.js
```
bypass_ssl.js内容
```shell
Java.perform(function () {
    var X509TrustManager = Java.use('javax.net.ssl.X509TrustManager');
    var SSLContext = Java.use('javax.net.ssl.SSLContext');
    var TrustManager = Java.registerClass({
        name: 'dev.asd.trust.TrustManager',
        implements: [X509TrustManager],
        methods: {
            checkClientTrusted: function (chain, authType) {},
            checkServerTrusted: function (chain, authType) {},
            getAcceptedIssuers: function () { return []; }
        }
    });
    var TrustManagers = [TrustManager.$new()];
    var SSLContextInit = SSLContext.init.overload('[Ljavax.net.ssl.KeyManager;', '[Ljavax.net.ssl.TrustManager;', 'java.security.SecureRandom');
    SSLContextInit.implementation = function (keyManager, trustManager, secureRandom) {
        console.log('Bypassing SSL Pinning');
        SSLContextInit.call(this, keyManager, TrustManagers, secureRandom);
    };
});
```
### 失败,电脑上无法链接手机上frida-server
-----
 下载别人修改好的libsscronet.so文件   
 上传覆盖到抖音

so文件百度网盘地址
https://link.juejin.cn/?target=https%3A%2F%2Fpan.baidu.com%2Fs%2F1BuL8vtIyd34b0y60jY3qXg
提取码1005



 先上传到临时目录
```shell

adb push /Users/sevenli/Desktop/libsscronet.so /data/local/tmp/
```

查看抖音的存储路径
```shell
adb shell pm path com.ss.android.ugc.aweme
```
package:/data/app/~~6GFRK59nqVa2mAt8HxQ3tQ==/com.ss.android.ugc.aweme-LYq5WsM6oXhCSXuDutJpvw==/base.apk


再使用root 覆盖原始的so文件

```shell

adb shell
su   # 如果需要 root 权限

# 备份原始文件
cp /data/app/~~6GFRK59nqVa2mAt8HxQ3tQ==/com.ss.android.ugc.aweme-LYq5WsM6oXhCSXuDutJpvw==/lib/arm/libsscronet.so /data/app/~~6GFRK59nqVa2mAt8HxQ3tQ==/com.ss.android.ugc.aweme-LYq5WsM6oXhCSXuDutJpvw==/lib/arm/libsscronet.so.bak

# 覆盖替换
cp /data/local/tmp/libsscronet.so /data/app/~~6GFRK59nqVa2mAt8HxQ3tQ==/com.ss.android.ugc.aweme-LYq5WsM6oXhCSXuDutJpvw==/lib/arm/libsscronet.so

# 修改权限
chmod 755 /data/app/com.ss.android.ugc.aweme-1/lib/arm/libsscronet.so
chown root:root /data/app/com.ss.android.ugc.aweme-1/lib/arm/libsscronet.so
```

重启抖音 

```shell
adb shell am force-stop com.ss.android.ugc.aweme
adb shell monkey -p com.ss.android.ugc.aweme -c android.intent.category.LAUNCHER 1
```


### 失败,抖音19.8.0能抓包了,但是版本太低了.无法登录