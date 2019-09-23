---
title: 宝塔 mysql 无法远程连接
date: 2019-09-20 15:18:27
tags:
---

宝塔下新建一个数据库,使用sqlyog无法远程连接.
#### 第一次尝试解决

* 权限设置为所有人,就是所有ip都可以连接
* 宝塔'安全'菜单,放行3306端口
* 云服务器安全组,防火墙添加规则放行3306端口


#### windows下使用telnet 监测服务器的端口是否开启
```
 telnet 123.56.78.123  3306       #telnet 加ip或者域名 加 端口号
```
#### 无法连接,所以centos7系统防火墙的问题
```shell
netstat -tlunp               #查看主机监听的所有端口
iptables-save                #查看iptables的所有规则
iptables -nL --line-number   #按行号显示iptables规则

#没有3306端口的规则,添加一条
iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT   
#或者
iptables -A IN_public_allow -p tcp -m tcp --dport 3306 -m conntrack --ctstate NEW,UNTRACKED -j ACCEPT

service iptables save        #保存
service iptables restart     #重启服务

#尝试还是不行,再次使用'iptables -nL --line-number'查看规则,发现在我新添加的3306规则上有一条 
-A INPUT -j REJECT --reject-with icmp-host-prohibited

#按行号删除这一条
iptables -D INPUT 11          #删除第11行的iptables规则,上面这条在所有规则的11行

#再重新添加这一行,然后他就排到3306规则后面了
iptables -A INPUT -j REJECT --reject-with icmp-host-prohibited

service iptables save        #保存
service iptables restart     #重启服务


```

#### 再次尝试.ok

