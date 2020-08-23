---
title: "加速Github的访问及下载速度"
date: 2020-08-23T15:57:54+08:00
lastmod: 2020-08-23T15:57:54+08:00
draft: false
keywords: [Github]
description: "Github"
tags: [Github]
categories: [Github]
---

### `Github`的痛

国内访问`github`的速度一直被广大网友及开发者所诟病,为了缓解这一现象,本文介绍几种方式加速,由于中国开发者人群越来越大,`github`也被微软收购,相信这个问题今后肯定会慢慢的被解决.期待我们再不久的将来国内的体验也能像访问`百度`之类的网站一样访问`github`. 如果你有一个速度还不错的代理,就不需要再使用下面的方案了.

### 修改`CDN`或`HOST`

常用的方式是修改`host`,对应的`cdn`也可以自行查找,不过这两种方式都不太稳定,一旦节点更换就会失效

1. 首先我们进入 [ipaddress](https://www.ipaddress.com/)

   ![image-20200823160858904](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200823160900.png)

2. 搜索我们要查找的域名后查找

   ![image-20200823160949906](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200823160951.png)

3. 修改` host ` 文件,进入目录 `C:\Windows\System32\drivers\etc\`,找到`hosts` 文件,使用编辑器打开并添加一条记录

   ![image-20200823161315987](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200823161317.png)

4. 配置完成后就可以了,此方式速度提升不固定,有较大的波动,同样,对于其他打开速度缓慢的域名都可以使用这种方式,但是如果确定不使用了最好记得清楚那条记录,免得起到干扰作用

### 访问加速,使用镜像站

`github`也有很多的镜像站,可以用来搜索项目,克隆项目.但是不可以登录,登录会报错.所以仅支持使用`https`的方式进行克隆

当然也可以再使用`HTTPs`克隆的时候直接换成镜像站的域名,比如

```bash
git clone https://github.com/Molunerfinn/PicGo.git #原链接
git clone https://hub.fastgit.org/Molunerfinn/PicGo.git #加速链接
```

**使用此方法克隆的项目,链接的远程地址也是镜像站的,如果需要进行代码的推送合并,需要先更改关联的远程分支链接**

```bash
git remote get-url --all origin # 查看远程链接
git remote set-url --delete origin url # 删除与远程链接的关联
git remote set-url --add origin url #添加一个远程链接
```



> 我常用的镜像站
>
> 1. https://hub.fastgit.org/
> 2. https://git.sdut.me/
> 3. https://github.wuyanzheshui.workers.dev/
> 4. https://github.com.cnpmjs.org/

当然不至于这四个,如有其他更好用的可以评论中留下链接

### 下载站加速下载

通常我们需要下载`release`里的源码或者编译好的程序,但是如果使用前面的方法,下载的链接是一样的,所以速度依旧无法提升.

使用`github`的下载站,将`github`的下载链接提交就可以返回一个临时的下载链接,会比`github`上下载快许多

1. 首先进入下载站 [https://d.serctl.com/](https://d.serctl.com/)

2. 提交链接

   ![image-20200823162339265](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200823163849.png)

3. 选择一个线路下载

   ![image-20200823162440542](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200823163900.png)

最省事就是拥有一个代理服务器,就省去很多折腾的时间,而且网速也不会让心态爆炸,我常用的做法是使用镜像站看一些项目的介绍和`issues`,不需要登录的功能会使用镜像站来完成