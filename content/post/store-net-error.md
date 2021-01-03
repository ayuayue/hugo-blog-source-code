---
title: "解决开启代理无法访问微软商店的问题"
date: 2020-11-18T19:54:05+08:00
lastmod: 2020-11-18T19:54:05+08:00
draft: false
keywords: [解决开启代理无法访问微软商店的问题]
description: "解决开启代理无法访问微软商店的问题"
tags: [windows]
categories: [windows]
---

以前对于 `Windows store` 使用的不多,没发现这个问题,最近使用商店后发现无法进行网络连接.原因是开启了代理.将代理关闭之后久可以正常的使用了.但是每次都手动开启关闭比较的麻烦.经过一系列的搜索之后找到了一个最终的解决方法

### `fiddler` 

`fiddler` 是一个 `windows` 平台的抓包工具.这里我们使用它来解决这个问题.

首先下载和安装这个软件.推荐去官网或者大的软件商店平台下载.

打开软件,找到 `winconfig` 项进去

![image-20201118200052823](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20201118200936.png)

全选保存后就可以正常使用了.如果不想配置可以全不选然后保存就可以取消了.

[视频地址](https://www.bilibili.com/video/bv1pf4y1v7Eq)