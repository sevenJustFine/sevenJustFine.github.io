---
title: 宝塔面板下 使用的Let’s Encrypts 安装免费SSL证书 使网站支持https
date: 2019-08-27 15:43:03
tags:
---
### 登录lets encrypts 网站

1.1 登录lets encrypts网站 点击get started

![微信图片_201908271606402](https://image.li-n.cn/2019/08/27/726adf05bbfebe562b39720d1f4074b7.png)

1.2 选择中文

![微信图片_201908271606402](https://image.li-n.cn/2019/08/27/12b6abd7c606d0b78fc7862258d597c0.png)

1.3 我们有服务器权限 点击链接使用certbot

![微信图片_201908271606404](https://image.li-n.cn/2019/08/27/1ec795006ec48c75ecba9665d76ad5d4.png)

### 安装certbot
2.1 选择当前http网站的linux服务器和web服务器类型

![微信图片_201908271606405](https://image.li-n.cn/2019/08/27/52471c0ea120bf24dce815a9bae64a89.png)

当前我选择nginx和CentOS

![1566954842(1)](https://image.li-n.cn/2019/08/28/f8104bb7cbf2d8329c27b0ccb0a81229.png)


2.2 根据提示安装certbot

![微信图片_201908271606406](https://image.li-n.cn/2019/08/27/6cd45f70e2b629384076408358165f0e.png)
 
 2.2.1 ssh链接服务器
 2.2.2 开启epel。当前为centos 直接 
 
````
 yum install epel-release
````

![微信图片_201908271606407](https://image.li-n.cn/2019/08/27/59023ed9c73791094972f818423b70ff.png)

2.2.3 开启 optional channel

````
yum -y install yum-utils
yum-config-manager --enable rhui-REGION-rhel-server-extras rhui-REGION-rhel-server-optional
````

2.2.4 安装 certbot

````
sudo yum install certbot python2-certbot-nginx
````


### 使用certbot 安装证书

3.1 使用命令安装
````
sudo certbot --nginx
````

因为安装了宝塔界面，会出现报错，按默认的目录没有找到nginx的配置文件

![b5ddb3f1c454ca8dc6c6f5dc96b93e7](https://image.li-n.cn/2019/08/27/8fa02a6eb00e4c82802b3a7d4d23cbc9.png)

通过查询知道，应该用下面的指令,--nginx-server-root后面带上正确的nginx配置文件路径
````
certbot --nginx-server-root /www/server/nginx/conf 

````

![微信图片_201908271606401](https://image.li-n.cn/2019/08/27/e23be8f7a9acdad335839e54881d0075.png)

按数字选择你要安装的网站的域名

选择是否自动从http跳转到https

然后就会完成

证书安装的过程中,可能会让你输入邮箱，可以跳过邮箱：

````
certbot --nginx-server-root /www/server/nginx/conf --register-unsafely-without-email
````

安装完成之后，就可以测试自己的网站是否支持https

如果不能访问，可能是云服务器安全组没有开启443端口，linux服务器的443端口也需要打开

###  卸载证书

certbot revoke  --cert-path 加证书地址

````
certbot revoke  --cert-path /etc/letsencrypt/live/www.test.com/cert.pem 
````


![微信图片_20190827160640](https://image.li-n.cn/2019/08/27/400422406a0873a45d04413dbbd8ccad.png)


### 安装中可能出现的问题

1.cryptography TypeError: from_buffer() cannot return the address........


解决：

````
pip uninstall cryptography -y && \
yum -y remove python-cryptography && \
yum -y autoremove && \
pip install --upgrade cryptography

````

2. UnicodeDecodeError: 'ascii' codec can`t decode byte 0xe7 in position 14 ......

>>![4e179ff3a1ffaae42560f34528adb37](https://image.li-n.cn/2019/08/27/5931014f2dd6a9678d05e037f2bd2afd.png)

 修改 /usr/lib64/python2.7 下的 mimetypes.py 文件 在文件前面添加下面的代码
```` 
if sys.getdefaultencoding() != 'utf-8': reload(sys) sys.setdefaultencoding('utf-8')
````

3. ImportError: 'pyOpenSSL' module missing required functionality. Try upgrading to v0.14 or newer.




