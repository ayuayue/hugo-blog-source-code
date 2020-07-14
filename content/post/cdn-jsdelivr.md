---
title: "使用 JsDelivr作为CDN 加速服务"
date: 2020-07-14T19:22:40+08:00
draft: false
tags: [CDN]
categories: [CDN]
---

### 什么是` JSDelivr ` 

`jsdelivr` 是一个免费的开源的快速的 `cdn`, [官方地址](https://www.jsdelivr.com/)

### 为什么使用 ` jsdelivr`

对于个人用户而言,使用`cdn`的场景的场景和需求并不多,图床,`web`端的文件访问.这些功能当然也有很多`cdn`的服务商提供服务,首先`jsdelivr`的优势就是 免费, 速度对于个人用户来说是完全够用的,而且还是`https`的资源,避免了我们在`https`的站点上引用一些`http`的资源时由于浏览器对于安全的问题而将资源屏蔽掉,从七牛云转过来就是因为七牛云的`https`服务是收费的

### 如何使用

官方有三种使用方式,官方推荐的是第一种,本文使用的是第二种,也就是`github`,做前端开发的朋友喜欢使用`npm`也可以将包发布出去

#### 首先创建一个`Github`仓库

创建完成后去个人中心设置里,新建一个 `token`

![image-20200714193939644](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200714193941.png)

![image-20200714194028166](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200714194030.png)

权限只给一个 `repo` 即可,保存生成的`token`,以后不能再显示出来了

### 使用 `PicGo` 配置`Github` 图床

按照图片进行配置

![image-20200714194220407](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200714194221.png)

配置完成后上传一个图片进行测试,测试完成后即可正常使用

### 配合`Typora`使用 

前面几篇文章讲到过使用`picgo + typora`, 这里展示下具体的效果  


演示视屏

{{< video src="http://qiniu.caoayu.xyz/picgo-typora-1.mp4" >}}


### 上传视频时遇到的问题

由于录制的是`MP4`格式的视频,在上传到`cdn`后发现只能播放音频,没有视频,原因是: `MP4 AVC` 格式的视频才能在网页上正常显示,使用转码工具转码就可以了

##### 参考文章

1. [Github+jsDelivr+PicGo 打造稳定快速、高效免费图床](https://www.jianshu.com/p/a36e9c64361d)
2. [网页mp4视频在线播放只有声音没有画面解决办法](https://www.yaxi.net/2017-06-16/1455.html)