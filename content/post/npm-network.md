---
title: npm 使用问题
date: 2020-04-19T20:50:59+08:00
tags: [npm]
categories: [前端]
---
### node npm 安装
linux 可以使用二进制安装或者源码安装,还可以使用包管理器,我的安装方式
```
sudo apt install npm //会同时安装npm node
```
安装完成后可能使用的时候因为版本太低而报错

### npm更新
首先更换淘宝镜像
```
npm config set registry "https://registry.npm.taobao.org"
```
更新
```
sudo npm install npm -g
```
安装nrm管理镜像
```
sudo npm install nrm -g

nrm use  使用镜像
nrm ls //显示所有镜像
nrm test //测试速度
```
