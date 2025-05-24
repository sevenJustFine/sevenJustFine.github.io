---
title: install_socks5_on_ubuntu
date: 2025-05-24 12:35:06
tags:
---

#  在ubuntu虚拟机上安装socks5客户端



## 1.安装clash.meta

github地址
````shell
https://github.com/MetaCubeX/mihomo
````
---
下载解压linux-amd64版本
```shell
chmod +x clash
sudo mv clash /usr/local/bin/
```
---

创建配置文件目录
```shell
mkdir -p ~/.config/clash
```
添加配置文件
```shell
vim config.yaml
```
配置文件格式
```shell
mode: rule
log-level: info
external-ui: ui
external-controller: 127.0.0.1:9090

proxies:
  - name: sk1
    type: socks5
    server: 116.31.103.35
    port: 1212
    username: cai123
    password: cai123
  - name: sk2
    type: socks5
    server: 116.31.103.35
    port: 7777
    username: cai123
    password: cai123

proxy-groups:
  - name: auto
    type: load-balance
    proxies:
      - sk1
      - sk2

rules:
  - MATCH,auto
```
### 1.2 创建服务
```shell
vim   /etc/systemd/system/clash.service
```

写入配置文件
```shell
[Unit]
Description=Clash (Mihomo) Service
After=network.target

[Service]
User=root
Restart=always
ExecStart=/root/.config/mihomo/clash -f /root/.config/mihomo/config.yaml
WorkingDirectory=/root/.config/mihomo
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```
启用并启动服务
```shell
sudo systemctl daemon-reload          # 重新加载 systemd
sudo systemctl enable clash.service   # 设置开机自启动
sudo systemctl start clash.service    # 启动 Clash 服务
sudo systemctl status clash.service   # 查看运行状态
```


---
## 2.安装 Yacd（Web UI 控制面板）

 在配置文件添加web ui 路径
 ```shell
external-ui: ui
external-controller: 0.0.0.0:9090
secret: ""
 ```
控制面板的地址就是虚拟机的ip的9090端口
````shell
http://192.168.107.3:9090
````

### 2.2 在ubuntu上开启端口


安装ufw
```shell
sudo apt update
sudo apt install ufw
```

开启防火墙
```shell
sudo ufw enable
```

开启端口
```shell
sudo ufw allow 7890
sudo ufw allow 9090
sudo ufw allow ssh     # 开放 22
sudo ufw allow http    # 开放 80
sudo ufw allow https   # 开放 443
```
查看状态和开放端口
```shell
sudo ufw status verbose
```


### 2.3 直接下载yacd的前端代码

下载地址,下载yacd.tar.xz
```shell

https://github.com/haishanh/yacd/releases
```
---
 将文件解压,里面有index.html文件,直接用这个目录当网站的根目录创建网站

 进入网站输入 http://192.168.107.3:9090 和密码,进入管理界面