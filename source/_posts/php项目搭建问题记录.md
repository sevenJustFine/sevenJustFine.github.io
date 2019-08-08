---
title: php项目搭建问题记录
date: 2019-08-07 18:21:49
tags:
---



### 1
##### 环境:
php7.2 ,nginx 

##### 错误:
 ``require_once(): Failed opening required '../fun/extend.php' (include_path='.:/www/server/php/72/lib/php') in   #####/index.php on line 8" while reading response header from upstream
 ``
##### 解决:
 ![](https://raw.githubusercontent.com/sevenJustFine/image/master/20190807_php.png?token=ADXOIQ5JORM5ALOFQ45APPS5JKUUA)
 ```(图片保存在github)```
 
### 2
php文件加密问题:

sg_load()函数

https://www.cnblogs.com/tangxuliang/p/9401122.html(解决方案地址)

安装sourceguardian加密php扩展:
https://www.sourceguardian.com/loaders/download.php

* 下载 ixed.5.6.lin 文件(链接:https://raw.githubusercontent.com/sevenJustFine/image/master/ixed.5.6.lin?token=ADXOIQ6TPHDAB5NKAJ4GFXS5JKWFG)
* 加到对应版本php的扩展库中(目录一般为:php/lib/php/extensions...)
* 在php配置文件配置扩展(目录一般为:php/etc/php.ini)  
添加一行 extension=ixed.5.6.lin
* 重启web server

