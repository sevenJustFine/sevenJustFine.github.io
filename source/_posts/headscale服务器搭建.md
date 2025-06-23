---
title: headscale服务器搭建
date: 2025-06-21 11:27:25
tags:
---

## 一、安装 Headscale（推荐使用二进制方式）


```shell

# 1. 安装依赖
sudo apt update
sudo apt install curl unzip sqlite3 -y

# 2. 下载最新版 Headscale（从 GitHub）
curl -L https://github.com/juanfont/headscale/releases/latest/download/headscale_amd64 -o /usr/local/bin/headscale

# 3. 添加执行权限
chmod +x /usr/local/bin/headscale

# 4. 验证
headscale version
```

或者直接从github下载再上传到/usr/local/bin  
https://github.com/juanfont/headscale/releases


## 2.创建headscale的配置文件

https://headscale.net/stable/ref/configuration/#__tabbed_1_1

示例配置文件地址
https://github.com/juanfont/headscale/blob/v0.26.1/config-example.yaml

将配置文件放在/etc/headscale/config.yaml   
修改其中的server_url 为自建服务的域名,如hs.xxxx.com


## 3.创建服务启动headscale

在/etc/systemd/system/中创建配置文件headscale.service
```shell
[Unit]
Description=Headscale coordination server
After=network.target

[Service]
ExecStart=/usr/local/bin/headscale serve --config /etc/headscale/config.yaml
WorkingDirectory=/var/lib/headscale
Restart=on-failure
RestartSec=5s
User=root

[Install]
WantedBy=multi-user.target
```
启动命令 
```shell
systemctl daemon-reload
systemctl enable --now headscale
systemctl start headscale
```

## 4.在hs.xxxx.com域名中反向代理本地的8080端口

可以直接在bt面板中添加
```shell

#PROXY-START/

location ^~ /
{
    proxy_pass http://127.0.0.1:8080;
    proxy_set_header Host 127.0.0.1;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header REMOTE-HOST $remote_addr;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;
    proxy_http_version 1.1;
    # proxy_hide_header Upgrade;

    add_header X-Cache $upstream_cache_status;

    #Set Nginx Cache
    
    
    set $static_filegP1e2FBI 0;
    if ( $uri ~* "\.(gif|png|jpg|css|js|woff|woff2)$" )
    {
    	set $static_filegP1e2FBI 1;
    	expires 1m;
        }
    if ( $static_filegP1e2FBI = 0 )
    {
    add_header Cache-Control no-cache;
    }
}

#PROXY-END/
```


## 部署自己derp服务

### 使用golang编译derper
 
安装golang   
Debian/Ubuntu：  
```shell
sudo apt update
sudo apt install -y golang
```
CentOS  
```shell
sudo yum install -y golang
```
使用git 领取源码并构建
```shell
git clone https://github.com/tailscale/tailscale.git
cd tailscale/cmd/derper
go build
```
成功后,会生成一个可执行的文件 derper
 
### 使用systemd 服务启动derper
 创建配置文件/etc/systemd/system/derper.service
```shell
[Unit]
Description=DERP relay server
After=network.target

[Service]
ExecStart=/usr/local/bin/derper \
  --hostname=derp.xxxx.com \
  --certdir=/www/server/panel/vhost/cert/derp.xxxx.com \
  --a=:3443 \
  --http-port=3080 \
  --stun
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
启动derper
```shell
systemctl daemon-reload
systemctl enable --now derper
systemctl start derper
```

创建一个网站 derp.xxxx.com  
在网站的根目录下创建一个derp.json的配置文件
```shell
{
  "Regions": {
    "900": {
      "RegionID": 900,
      "RegionCode": "mt_derp",
      "RegionName":"China",
      "Nodes": [
        {
          "Name": "1",
          "RegionID": 900,
          "HostName": "derp2.xxxx.com",
          "IPv4": "xx.xxx.xxx.xx",
          "IPv6": "2001:db8::1"
        }
      ]
    }
  }
}
```


derp网站添加伪静态中配置  


```shell
location =/derp.json {
  root /www/wwwroot/derp.xxxx.com;
}

location  =/derpmap/default {
  default_type application/json;
  alias /www/wwwroot/derp.xxxx.com/derp.json;
}


```
derp网站添加反向代理本地3443端口
```shell

#PROXY-START/

location ^~ /
{
    proxy_pass http://127.0.0.1:3443;
    proxy_set_header Host 127.0.0.1;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header REMOTE-HOST $remote_addr;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;
    proxy_http_version 1.1;
    # proxy_hide_header Upgrade;

    add_header X-Cache $upstream_cache_status;

    #Set Nginx Cache
    
    
    set $static_file0D4f5j1b 0;
    if ( $uri ~* "\.(gif|png|jpg|css|js|woff|woff2)$" )
    {
    	set $static_file0D4f5j1b 1;
    	expires 1m;
        }
    if ( $static_file0D4f5j1b = 0 )
    {
    add_header Cache-Control no-cache;
    }
}

#PROXY-END/
```

修改/etc/headscale/config.yaml的
在derp下urls 中添加 https://derp.xxxx.com/derp.json

重启headscale
```shell
systemctl restart headscale
```

验证derp服务是否已经安装成功
在安装了tailscale 并登录了自建的服务的本地命令行运行
```shell
tailscale netcheck
```
结果类似
```shell
Report:
	* Time: 2025-06-23T06:39:37.609746Z
	* UDP: false
	* IPv4: (no addr found)
	* IPv6: no, but OS has support
	* MappingVariesByDestIP:
	* PortMapping:
	* CaptivePortal: false
	* Nearest DERP: China
	* DERP latency:
		- mt_derp2: 10.5ms  (China)
```


## headscale 后台管理页面
项目地址:   
https://github.com/gurucomputing/headscale-ui

下载之后上传解压到/www/wwwroot/headscale_ui/web

在hs.xxxx.com的网站中配置伪静态
```shell

location /web {
        alias /www/wwwroot/headscale_ui/web;
        index index.html;
    }
```
然后访问https://hs.xxxx.com/web

需要先在setting 中填入apikey   

在安装headscale的服务器上执行命令创建apikey

```shell
headscale apikeys create
```
填入apikey,然后保存