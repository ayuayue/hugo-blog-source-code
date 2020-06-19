---
title: "deepin下安装向日葵依赖问题"
date: 2020-06-19T19:28:10+08:00
draft: false
tags: [deepin,向日葵]
categories: [deepin]
---

在`deepin`系统上安装`linux`的向日葵时，会提示依赖缺失，在官方网站上下载`ubuntu|deepin`的 向日葵 `deb`包，双击会显示依赖缺失，通过命令`dpkg -i 包名`同样会显示有一个依赖安装不了，并且不在源内，无法手动安装

解决方法：
    打开命令行，使用`sudo apt --fix-broken install`修复依赖问题没有安装的包，会把向日葵的包卸载掉，然后使用命令安装，强制不安装依赖，`sudo dpkg --force-depends -i 包名`，实际使用并不影响。

