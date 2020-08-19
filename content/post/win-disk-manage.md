---
title: "Windows下新建和更改分区"
date: 2020-08-19T21:43:56+08:00
lastmod: 2020-08-19T21:43:56+08:00
draft: false
keywords: [Windows]
description: "Windows"
tags: [Windows]
categories: [Windows]
---

### 分区

分区只是逻辑上的概念,实际都是在一块硬盘上.分区可以使文件存放更规范也可以分离一些系统级别的概念,防止用户操作系统分区导致系统出现问题

### 如何分区

`windows`自带了一个磁盘管理器,可以满足基本的需要,本文主要介绍使用磁盘管理器进行下面操作

- [x] 分区新建
- [x] 分区初始化
- [x] 分区格式化及删除
- [x] 更改卷标

### 分区长啥样

![image-20200819215748158](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200819215920.png)

#### 从一个分区中新建一个分区

![image-20200819220407923](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200819220410.png)

#### 压缩卷

![image-20200819220706891](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200819233004.png)

![image-20200819220907551](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200819233354.png)

#### 分区初始化及设置卷标

![image-20200819220958204](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200819233338.png)

一直下一步,直到下图

![image-20200819221057171](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200819233012.png)

最后就初始化了一个分区

![image-20200819221151220](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200819233024.png)

#### 格式化及删除分区

![image-20200819221253236](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200819233232.png)

### 关于分区扩容

自带的磁盘管理器无法进行分区扩容的操作

可以使用 `diskgenius` 进行更高级的操作

比如 :  [使用diskgenius合并分区](https://www.caoayu.xyz/post/disk-dilation/)

