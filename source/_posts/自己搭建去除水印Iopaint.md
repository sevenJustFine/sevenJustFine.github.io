---
title: 自己搭建去除水印Iopaint
date: 2025-05-27 22:13:42
tags:
---

### 1.安装Iopaint
pip3安装Iopaint

```shell
pip3 install iopaint
```

直接命令行测试启动

```shell
iopaint start --model=lama --device=cpu --port=8080
```

启动的时候会默认下载模型  
如果下载不了,可以自己用魔法下载再上传到默认的目录  
文件名为big-lama.pt

```shell
/root/.cache/torch/hub/checkpoints
```

启动成功后网页端会监听本地的8080端口

### 2.创建systemd服务

```shell
vim /etc/systemd/system/iopaint.service
```
配置文件
```shell
[Unit]
Description=IOPaint Service
After=network.target

[Service]
User=root
WorkingDirectory=~
ExecStart=iopaint start --model=lama --device=cpu --port=8080
Restart=always

[Install]
WantedBy=multi-user.target
```

启动服务

```shell
sudo systemctl daemon-reload
sudo systemctl start iopaint
sudo systemctl enable iopaint
```
查看状态
```shell
sudo systemctl status iopaint
```
如果启动失败,监听日志
````shell
journalctl -u iopaint -xe -f
````

### 3.创建网站,反向代理本地8080端口,添加bash auth 密码验证