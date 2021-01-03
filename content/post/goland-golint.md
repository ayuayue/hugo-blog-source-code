---
title: "Goland 中配置 go-lint 代码检查"
date: 2020-09-06T00:43:11+08:00
lastmod: 2020-09-06T00:43:11+08:00
draft: false
keywords: [Goland ]
description: "Goland "
tags: [Goland ]
categories: [Goland ]
---

### `Go-lint` 

`go-lint` 是由`go`官方提供的一个代码审查及问题提示的工具.在` vscode ` 中,如果安装了`go-lint` 则会在终端的问题中显示所有的代码不规范的地方及优化提示.

但是在 `goland`中我并没有发现这个提示,最近更新到 `2020-02`版本后也发现了一个`problem` ,里面也会进行代码提示.

### 配置`go-lint`

在低版本的`goland`中是没有`problem`这一项的,仅仅靠右侧的黄色标识来查看提示还是不够直观

而且这种是通过检查打开的`go`文件进行检查的,如果我们想直接检查一个包的代码问题呢

所以我们就要自己动手配置了,原理就是使用`ide`的工具来拼`go-lint` 的参数,`ide`绝大部分的操作的原理都是通过给可执行程序加上不同的参数来实现功能,比较方便,不用我们每次都手动拼长长的一堆参数.

### 安装`go-lint`

首先是`go`标准库及扩展工具的安装,一般是需要使用代理的,国内也有很多`goproxy`可以选择,此处省去安装步骤

### 配置`goland`

1. 打开`goland` 进入设置 setting --> Tools --> Extenal Tools 

   ![image-20200906005457698](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200906010122.png)

2. 点击 + 新建一个扩招工具

   ![image-20200906005659022](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200906010113.png)

3. 新建完成后应用,就会在右键的 `External Tools` 里找到`go-lint` ,右键文件夹即为对包执行命令

   ![image-20200906010026440](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200906010027.png)

**也会由对应的快捷键提示,根据提示可以使用快捷键进行操作**