---
title: 'php 删除字符串中的汉字,保留数字和字母'
date: 2019-09-17 17:57:56
tags:
---
需要从爬虫爬取的n多字符串中获取微信号

微信号规则:
* 微信号是微信的唯一凭证,只能设置1次
* 可使用6-20个字母,数字,下划线和减号
* 必须以字母开头(字母不区分大小写)
* 不支持设置中文


```php
   $Model = new Wechat(); 
        $data = $Model->select();

        $new_data_arr = [];
        foreach ($data as $v) {
            $temp = [];
            $result = preg_replace('/([\x80-\xff]*)/i', '', $v['data']); //删除汉字
            $result = trim($result);//删除空格
            $result = str_replace('~', '', $result);
            $result = str_replace('?', '', $result);
            $result = str_replace('.', '', $result);
            $result = str_replace(':', '', $result);
            $result = str_replace('/', '', $result);
            $result = str_replace('\\', '', $result);
            $result = str_replace(';', '', $result);
            $result = str_replace('+', '', $result);
            $result = str_replace('(', '', $result);
            $result = str_replace(')', '', $result);
            $result = str_replace('*', '', $result);
            $result = str_replace(',', '', $result);
            $result = str_replace('%', '', $result);
            $result = str_replace(' ', '', $result);

            if ($result && mb_strlen($result, 'utf-8') > 5 && mb_strlen($result, 'utf-8') < 21) {
                $temp['id'] = $v['id'];
                $temp['data1'] = trim($result);
                $new_data_arr[] = $temp;
            }
        }

        set_time_limit(0);
        $result = $Model->saveAll($new_data_arr);
```
这样处理这后还是会有遗漏,会有不符合要求的微信号,还需要改进.
