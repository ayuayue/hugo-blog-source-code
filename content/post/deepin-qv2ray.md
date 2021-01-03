---
title: "Deepin Qv2ray"
date: 2020-11-29T18:46:45+08:00
lastmod: 2020-11-29T18:46:45+08:00
draft: false
keywords: [deepin install qv2ray]
description: "deepin install qv2ray"
tags: [deepin]
categories: [deepin]
---

### 下载 `Qv2ray`

1.  从 [release](https://github.com/Qv2ray/Qv2ray/releases)  发布页下载适合自己电脑架构的可执行文件

2.  从 `deepin` 的星火应用商店下载 [星火应用商店](https://www.spark-app.store/)

    ​	![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20201129212008.png)

### 配置 `v2ray` 核心

`Qv2ray` 并没有包含 `v2ray` 的核心软件包，所以需要自行下载，并配置进去。

[下载地址](https://hub.fastgit.org/v2fly/v2ray-core/releases) ,同样的找到自己电脑架构的包下载.这里我下载的是 `v2ray-linux-64.zip` 

下载太慢可以使用这个,我上传的原文件[链接](https://www.caoayu.xyz/file/v2ray-linux-64.zip)

#### 配置路径

`Qv2ray` 的配置地址可以在软件中看到,打开 `Qv2ray` ,找到 首选项>内核设置,可以看到默认的配置路径,进入到这个路径,将下载好的压缩包解压到这里,然后命名为 `vcore`,或者你更改软件里的路径.

**注意可执行文件路径是指向一个`v2ray`的可执行文件,并不是一个目录,在解压后的包里可以看到**

1.  核心文件可执行文件路径

2.  资源目录 -- 包含 `geoip.dat` 和 `geosite.dat` 的 **目录**

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20201129203542.png)

3.  检查 `v2ray` 核心设置,点击即可检查配置是否成功

### 添加订阅

配置好之后就可以添加自己的订阅了

首先点击分组,然后选订阅设置,添加订阅就可以看到所有的连接了

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20201129203747.png)

### 芜湖~起飞 :cool:

