---
title: "deepin下状态栏无法显示问题"
date: 2020-06-18T21:44:58+08:00
draft: false
tags: [deepin]
categories: [deepin]
---

1. 在`deepin`的使用中，如果使用了一些插件改变了状态栏，可能会使状态栏因配置更改而无法正常显示
2. 自己设置了`dde-dock`栏为一直隐藏

解决方法：

直接启动`dde-dock`,通过 `ctrl + atl + t` 键呼出终端，在终端输入 `dde-dock`来启动状态栏,如果状态栏已经启动过，则会报错并退出，返回一个进程的`pid`，我们拿到`pid`然后使用`kill -9 pid`将进程关闭，此时`dde-dock`就会重新启动，我们再右键进行调整
    如果是安装了插件，并且上面的方法无效，则重新安装`dde-dock`, ` sudo apt-get reinstall dde-dock`,或者先卸载在安装。

***小技巧***

在`debain`中，如果直接安装一个`deb`的安装包，但是因为依赖的问题，没有安装成功，我们可以点击查看详情，看是那些依赖没有安装，我们可以根据提示到终端自己安装，也可以使用 `sudo apt --fix-broken install` 自动处理未安装的依赖，但可能安装不完全，最后手动检查一下
