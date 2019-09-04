---
title: centos服务器安装php swoole扩展
date: 2019-09-04 10:21:18
tags:
---


官方编译安装地址 https://github.com/swoole/swoole-wiki/blob/master/doc/1.2%20-%20%E7%BC%96%E8%AF%91%E5%AE%89%E8%A3%85.md


从github获取最新发行版本地址 https://github.com/swoole/swoole-src/releases
获取到的链接地址为：https://github.com/swoole/swoole-src/archive/v4.4.5.tar.gz


*  ssh登录服务器

* 下载swoole包
 ````
 wget https://github.com/swoole/swoole-src/archive/v4.4.5.tar.gz
  ````
* 解压

````
tar -zxvf  v4.4.5.tar.gz
````

* 进入文件夹 修改文件夹名字

````
cd swoole-src-4.4.5
mv  swoole-src-4.4.5  swoole-src
````

*  使用phpize来生成编译检测脚本 

````
phpize  

````


*  编译 ,这里需要注意，我的服务器安装了宝塔面板，所以php的安装路径不一样，需要用--with-php-config 指定php-config的路径

````
./configure \
--enable-coroutine \
--enable-openssl  \
--enable-http2  \
--enable-async-redis \
--enable-sockets \
--enable-mysqlnd \
--with-php-config=/www/server/php/73/bin/php-config
````

* 编译 安装

````
make clean && make && make install
````


* 修改配置文件，添加扩展

```` 
cd /www/server/php/73/etc

vim ./php.ini
````
在php.ini 末尾添加 extension=swoole.so ,保存退出


* 检查是否安装成功

显示php安装的模块
````
php -m
````
  或者
获取php扩展的配置信息
````
php --ri swoole
````



