---
title: "deepin Picgo 配置并配合 Typora 使用"
date: 2021-02-07T14:51:25+08:00
lastmod: 2021-02-07T14:51:25+08:00
draft: false
keywords: [deepin]
description: "deepin"
tags: [deepin]
categories: [deepin]
---

`deepin` 下使用 `picgo` ，在 `deepin` 下，有 `picgo` 的 `AppImage` 程序，用了一段时间不太理想，而且本来就是个小工具，只需要配置一次就可以了，就放弃了 `AppImage` 这种可视化的操作。改用 `node` 做的一个 `picgo` 程序。地址：https://www.npmjs.com/package/picgo

### 安装

需要安装 `node、npm` ，最好也配置了 `npm` 的国内源。关于国内源配置，博客中也有介绍，可以在搜索页搜索 `npm、node` 等关键词查询。

```bash
npm install picgo -g
picgo -h # 帮助
```

#### 配置

直接使用 `picgo u + 图片路径` ，或者当前剪贴板 刚刚复制了一张图片，可以使用 `picgo u` 自动上传剪贴板的图片。

但是可能会上传失败，因为默认使用的是 `smss` 的图床。应该是要设置 `token` 等。所以我们使用 `github` 的图床来做，可以更好的对原图进行一个管理。

修改配置文件 `~/.picgo/config.json` ，如果不太清楚可以对比 [这篇文章的图形化的配置信息](https://blog.caoayu.top/post/cdn-jsdelivr/)

```json
{
  "picBed": {
    "current": "github", #当前选择为 github 图床
    "uploader": "github", # 上传到GitHub
    "smms": {
      "token": ""
    },
    "list": [
      {
        "name": "SM.MS图床",
        "type": "smms",
        "visible": false
      },
      {
        "name": "腾讯云COS",
        "type": "tcyun",
        "visible": false
      },
      {
        "name": "微博图床",
        "type": "weibo",
        "visible": false
      },
      {
        "name": "GitHub图床",
        "type": "github",
        "visible": true
      },
      {
        "name": "七牛图床",
        "type": "qiniu",
        "visible": false
      },
      {
        "name": "Imgur图床",
        "type": "imgur",
        "visible": false
      },
      {
        "name": "阿里云OSS",
        "type": "aliyun",
        "visible": false
      },
      {
        "name": "又拍云图床",
        "type": "upyun",
        "visible": false
      }
    ],
    "github": {
      "branch": "master", #分支名
      "customUrl": "... ", #自定义的链接可以设置cdn链接
      "path": "img/", #图片在仓库中的路径
      "repo": "ayuayue/cdn", #github仓库
      "token": "*" # GitHub的 token
    }
  },
  "picgoPlugins": {}
}

```

#### 配置后

配置后使用命令就可以直接上传图片到 `GitHub` 图床了 ，如果上传失败，查看日志是什么问题，日志文件在 `~/.picgo/picgo.log` 。最好上传时配置好 `git` 代理，以免由于国内网络问题，导致上传失败。关于配置 `git` 代理 https://blog.caoayu.top/post/git-proxy/

----

### 配置 `typora` 使用

打开 `typora` ，选择文件，偏好设置，找到图像。根据下图进行配置

![image-20210207175307211](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210207175307211.png)

![image-20210207175340400](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210207175340400.png)

关于自定义命令的填写，找到 `picgo` 的安装目录。

```bash
which picgo
```

关于报错 `/usr/bin/node: env` 找不到，这是由于自己安装 `node` 是并没有在系统环境变量中，而是自己设置了新的环境变量，只需要添加一个 `node` 软链接到系统环境变量目录中即可

```bash
sudo ln -s /usr/local/node/bin/node /usr/bin/node # 必须都是绝对路径，前面那个地址是 node 可执行程序的位置
```

