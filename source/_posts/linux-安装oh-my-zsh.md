---
title: linux 安装oh my zsh
date: 2019-09-20 08:48:07
tags:
---
* 查看linux系统支持的shell
```shell
cat /etc/shells
```
* 没有zsh 就先安装zsh
```shell
yum install zsh
```

* 切换默认shell为zsh
```shell
chsh -s /bin/zsh 
```

* 安装oh my zsh
```shell
wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O - | sh
```


* 使用zsh
```
zsh
```
