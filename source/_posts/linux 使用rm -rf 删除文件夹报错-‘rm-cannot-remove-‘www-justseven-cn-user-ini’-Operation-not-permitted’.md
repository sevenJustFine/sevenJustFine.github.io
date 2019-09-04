---
title: >-
  linux 删除文件夹报错 ‘rm: cannot remove ‘www.justseven.cn/.user.ini’: Operation not
  permitted’
date: 2019-09-04 11:28:13
tags:
---

想要删除一个不用的网站目录文件夹，使用rm -rf www.justseven.cn ，结果出现报错‘rm: cannot remove ‘www.justseven.cn/.user.ini’: Operation not permitted’。


百度获取的解决方案地址：
https://blog.csdn.net/sinat_35861727/article/details/79040755



````
cd www.justseven.cn  ##进入文件夹

ls -a               ##显示所有包括隐藏的文件

lsattr -a           ##显示文件属性

chattr -i .user.ini  ##修改文件属性，去除 ‘不得任意更动文件或目录 ’属性

ls attr -a    

cd ..

rm -rf www.justseven.cn   ##再次删除文件夹

````