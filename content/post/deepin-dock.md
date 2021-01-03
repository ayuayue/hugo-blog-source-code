---
title: deepin 任务栏消失问题
date: 2020-04-25T19:34:49+08:00
tags: [deepin]
categories: [deepin]
---
### deepin dde-dock 任务栏消失问题
在使用了任务栏的一些插件后,如果卸载的话,可能会把dde-dock的一些依赖也卸载掉,所以会导致dock栏出现问题,在查看了很多帖子后无果,也重新安装了几次还是不行,解决方案: 
```
删除 ~/.config/dconf 下的缓存文件, 找到 /usr/lib/dde-dock/plugins/ 删除所有的插件 重启
重启后只显示壁纸,使用快捷键 ctrl alt T 呼出终端
安装dde-dock
```
<!--more-->
sudo apt install dde-dock
即可恢复



