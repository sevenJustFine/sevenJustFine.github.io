---
title: hexo+github.io搭建的静态博客 'jQuery is not defined' 问题解决
date: 2019-08-28 11:26:47
tags:
---
我的这个博客就是hexo+github.io搭建的，加载会很慢，F12发现cosole报错 ‘jQuery is not defined， 发现是因为有query.min.js文件加载失败。因为用的是googleapis.com的域名，可能是被和谐了，查了下，然后修改juery的引用地址，解决问题。

![微信图片_20190828112750](https://image.li-n.cn/2019/08/28/9d9740d5b51da95135403dcc67d51a1e.png)

* 在主题下搜索juqery的地址，找到是引用地址在 ‘themes/landscape/layout/_partial/after-footer.ejs ’文件下

![微信图片_201908281127501](https://image.li-n.cn/2019/08/28/b043c2b3ee2b458f9a015d2b3068243b.png)


![微信图片_201908281127502](https://image.li-n.cn/2019/08/28/44c47b77d6cf2f75b5acbdf95fd82d10.png)

* 登录 https://www.bootcdn.cn/，搜索jquery.获取一个最新版本的jqury.min.js文件地址
![微信图片_201908281127503](https://image.li-n.cn/2019/08/28/5e9ac2a4ce01ab37df097c1fed6a606a.png)

* 修改之前的after-footer.ejs文件下的jQuery地址，这里需要注意不能使用http的地址，因为在https下面的文件是访问http需要解决跨域问题。默认是https，所以直接填写为‘//cdn.bootcss.com/jquery/3.4.1/jquery.min.js’
![微信图片_201908281127504](https://image.li-n.cn/2019/08/28/9dc04e640ae4743ccf8d3c12291936d3.png)

* 然后hexo clean  ,hexo g 之后，你会发现pulic下的其它文件的jquery地址已经修改

![微信图片_201908281127505](https://image.li-n.cn/2019/08/28/4764808d382beb08edd6a8ab4af8e821.png)

* 接着 hexo d 提交部署。再打开网站，会发现jquery地址已经更新，而且不会报错了。
![微信图片_201908281127506](https://image.li-n.cn/2019/08/28/5462ab0ed0ca2be4d39203b7a1a5224a.png)
