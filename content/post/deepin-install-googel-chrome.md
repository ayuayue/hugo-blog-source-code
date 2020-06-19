---
title: deepin安装googel-chrome
date: 2020-04-26T11:05:54+08:00
tags: [linux]
categories: [deepin,chrome]
---
### deepin自带的是开源的chromium,更换为谷歌chrmoe

下载 [地址](https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb)
双击安装,等待安装完成即可使用

更新,编辑 /etc/default/google-chrome
将第一行false改为true
运行sudo apt update 即可自动更新

