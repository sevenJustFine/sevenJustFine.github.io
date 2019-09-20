---
title: 宝塔面板下 mysql 数据库密码错误
date: 2019-09-20 09:45:09
tags:
---
宝塔下的mysql安装后,用ssh工具连接之后使用myql -uroot 登录会报错"ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)",后来在宝塔使用phpmyadmin登录之后,手贱的修改了密码,之后不知道怎么操作就出错了.phpMyAdmin也登录不上了,之后所有的mysql操作也不行.

* 百度后找到的原文地址为: https://www.cnblogs.com/ivictor/p/9243259.html


*  进入一个目录.vim创建一个init.sql文件.写入修改密码的sql语句,:wq 保存退出
```
cd /
vim init.sql 
alter user 'root'@'localhost' identified by '123456';
```
* 使用--init-file参数
``` 
/www/server/mysql/bin/mysqld_safe --init-file=/init.sql &

service mysqld restart 
```
* 可以用比较简单的一步实现
``` 
service mysqld restart --init-file=/init.sql
```
* 然后就可以用宝塔后台的修改新的mysql密码

