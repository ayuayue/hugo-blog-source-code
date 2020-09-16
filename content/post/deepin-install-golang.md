---
title: deepin安装golang
date: 2020-04-27T19:16:50+08:00
tags: [golang]
categories: [golang]
---
### deepin 二进制文件安装golang

先下载二进制文件[下载地址](https://studygolang.com/dl/golang/go1.14.2.linux-amd64.tar.gz)

<!--more-->
解压到/usr/local
```
sudo tar -C /usr/local/ -zxf go1.14.2.linux-amd64.tar.gz
```

```
设置环境变量

sudo vim ~/.zshrc

添加一行代码 
export PATH=$PATH:/usr/local/go/bin
重载文件
source ~/.zshrc

```
### 配置代理
go 1.13版本之后的操作
```
go env -w GOPROXY=https://goproxy.cn,direct
// 如果报警告先取消掉 GOPROXY的设置
unset GOPROXY
// 然后再设置
```

