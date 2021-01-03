---
title: 解决deepin下安装sqlite3依赖过新的问题
date: 2020-04-28T08:16:48+08:00
tags: [sqlite3]
categories: [sqlite3]
---
### 安装sqlite3依赖过新的问题
在安装sqlite3时提示需要安装其他依赖,当手动安装依赖时显示当前依赖安装过了,而且版本太新导致无法安装
解决方法:
```
sudo dpkg --purge --force-depends libsqlite3-0
sudo apt-get install libsqlite3-0
sudo apt-get install -f
sudo apt-get install libsqlite3-dev
sudo apt-get install sqlite3
```
<!--more-->
安装完成使用sqlite3命令验证,用sqlite3 user.db 创建并use数据库,然后使用SQL进行表的创建及其他操作即可
