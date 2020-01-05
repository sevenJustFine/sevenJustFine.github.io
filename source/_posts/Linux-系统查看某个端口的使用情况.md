---
title: Linux 系统查看某个端口的使用情况
date: 2019-11-03 20:14:58
tags:
---
Linux 下查看某个端口的使用情况的方法 

* 使用 netstat
```
#查看80端口的使用情况
netstat -tnlp | grep 80 
```
>netsta命令的参考地址:https://man.linuxde.net/netstat

---
* 使用 lsof
```
lsof -i: 80
```
>lsof命令的参考地址:https://man.linuxde.net/lsof