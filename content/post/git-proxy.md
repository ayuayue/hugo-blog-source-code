---
title: "Git配置代理"
date: 2020-10-19T17:40:53+08:00
lastmod: 2020-10-19T17:40:53+08:00
draft: false
keywords: [Git配置代理]
description: "Git配置代理"
tags: [Git配置代理]
categories: [git]
---

### `Git` clone 太慢

国内大部分的`git`速度还是挺慢的,前面也提到过使用 [镜像或下载站](https://www.caoayu.xyz/post/github-fast/)的方式来加速.

这次通过配置`git` 的代理方式来加速

### `http` 方式

`git clone`   的方法分为 `https | ssh` 两种,而这两种的代理配置方式也是不同用的.

对于 `https | http` 方式的链接

#### 全局配置

```bash
# sock 方式
git config --global http.proxy socks5://127.0.0.1:1080
git config --global https.proxy socks5://127.0.0.1:1080
# http 方式
git config --global https.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080
```

#### 取消配置

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

#### 项目配置

```bash
# 首先需要进入一个 git 的本地仓库
git config  https.proxy http://127.0.0.1:1080
git config  https.proxy https://127.0.0.1:1080
```

### `ssh`方式

windows 平台配置

修改 `~/.ssh/config`文件,如果没有创建(没有后缀). 如果创建不了,请将 `.ssh` 文件夹的权限设置为当前用户完全控制.

添加如下

```bash
Host github.com
ProxyCommand connect -H 127.0.0.1:7890 %h 22 #更改 7890 为你自己的本地代理端口
```

### 冲冲冲

接下来就可以去 `clone`测试一下速度了.推荐平时使用`ssh`的方式进行`clone`和配置代理.