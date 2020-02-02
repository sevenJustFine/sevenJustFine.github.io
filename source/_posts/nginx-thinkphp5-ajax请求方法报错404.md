---
title: nginx thinkphp5 ajax请求方法报错404
date: 2020-02-02 19:39:22
tags:
---

报错环境: nginx,thinkphp5.1


nginx配置文件添加

```
   if (!-e $request_filename) {
 	 rewrite ^(.*)$ /index.php?s=/$1 last;
  	}

```