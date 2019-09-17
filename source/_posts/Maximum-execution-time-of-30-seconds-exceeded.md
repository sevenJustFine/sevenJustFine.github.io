---
title: Maximum execution time of 30 seconds exceeded
date: 2019-09-17 17:46:04
tags:
---
今天用tp5框架处理使用八爪鱼爬的数据的时候 ,将数据处理然后savaAll保存数据库的时候,由于数据太多执行时间太长 ,报错..
如图


刚开始以为是mysql数据库的执行时间问题

```mysql
;SHOW VARIABLES  WHERE Variable_name LIKE '%max_execution_time%'         #查询当前的max_execution_time,默认位0

;SET GLOBAL MAX_EXECUTION_TIME=1000     #重新设置时间
;SET SESSION max_execution_time=10000   #重新设置时间
```
再次尝试还是不行,最后发现是php的最大执行时间为30s,执行超时

可以修改php.ini 文件,我这里直接在php代码里,数据库操作前加一句,设置最大执行时间为不限时.
```php
     set_time_limit(0);
```
