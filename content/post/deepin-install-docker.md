---
title: "Deepin20安装docker"
date: 2020-11-24T17:34:56+08:00
lastmod: 2020-11-24T17:34:56+08:00
draft: false
keywords: [Deepin20安装docker]
description: "Deepin20安装docker"
tags: [deepin,docker]
categories: [deepin,docker]
---

### 深度操作系统 `Deepin`

`deepin` 属于 `debain` 系，跟 `Ubuntu` 相似，由国人开发。现在作为我的主力开发环境，使用下来还不错，虽不如 `windows， mac`  的整体流畅度，但是已经满足日常及开发的需求了，相信几年之后就会赶上 `windows` 到时候对我们使用者来说也是多了一种选择。毕竟大多数程序都跑在 `linux` 下，而使用 `windows` 开发进行上线时，可能会出现一些奇怪的错误。同样的 `windows` 的开发环境及工具比起 `mac，linux`也是少了些许，导致有时效率跟不上。

最令人讨厌的莫过于开发环境的配置了，`windows` 下一套，`linux` 又要部署一套，而团队成员的使用习惯也不总是那么统一，所以老是会有 ‘ 我这跑着没问题啊 ’ 的回答。但这大部分是这两个操作系统的设计跟约定不同造成。可是有些时候，在开发中，需要使用一个扩展包时发现该扩招包没有 `windows` 版本。这就让人非常的头大。倒不如直接使用 `linux` 的桌面发行版，如果一开始不熟悉可以先使用虚拟机安装，熟悉以后安装一个双系统。来强迫自己使用 `linux` 环境，从而提高自己对 `linux` 系统的熟悉度，以及开发的便利性。

当然 `windows` 也可以通过一些软件模拟 `linux` 系统，如 `wls， cgywin，gitbash` 等，但是使用过以后会发现还是无法代替原生，其中之一就是环境变量的错乱，如果环境变量冲突就要进行取舍。其二是功能的不完整。虚拟机也是解决方案，不过每次都要在两个环境之中来回切换，不免会有迷糊的时候，还有就是两个文件系统之间的差异以及权限。

在使用过比较长时间的 `n` 个替换方案后，恰巧赶上 `deepin20` 正式版发布，在之前也断断续续用过不少次 `linux` 桌面版，`manjaro、linuxmint、ubuntu、deepin` ，每次都是用不了多久，软件系统都配置了一遍发现很多细节、小工具或体验达不到要求，都被我放弃了。我还算是个比较挑剔系统的人，但这次 `deepin20`  发布后我又安装了回来，一方面确实被 `windows` 各种环境问题搞得头疼，强迫症。转过来之后用了一段时间觉得还挺稳定的。只是有些小问题或者软件安装、软件仓库问题，不过通过查询社区，百度谷歌后，基本没什么问题，看来以前使用坚持不下来还是没有好好的了解，现在是我的正常开发机器，其中配置的有 `php、nginx、node、go、composer、buffalo、zsh、mysql、hugo` 还有些日常用的软件。总的来说还是比较稳定，比较省心。

### 使用`Docker`

虽说使用 `linux` 配置环境比较方便，但是对于 `deepin` 仓库里不少软件的版本比较低，或者官方仓库中没有，就需要自己新增软件库。`docker` 便是需要这么操作的一个例子，所以为了其他软件的配置以及不污染本机，使用 `docker` 来配置开发环境还是十分有必要的。比如后面可能会安装的 `postgres` ，`postgres` 官方有 `debain` 的安装方法，但是安装过程却没那么容易，而且配置也相对比较麻烦。所以打算先安装 `docker` 使用 `docker` 来安装 `postgres` ，并且可以把 `php` 的环境也换到 `docker` 中，正好前面有一篇使用 `docker` 搭建 `lnmp` 的文章。

### 安装 `Docker`

1.  卸载旧版本或残留

```bash
sudo apt-get remove docker.io docker-engine
```

2.  安装密钥管理与下载相关的工具

```bash
sudo apt-get install apt-transport-https ca-certificates curl python-software-properties software-properties-common
```

3.  下载并安装密钥

```bash
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/debian/gpg | sudo apt-key add -
// 官方源，能否成功可能需要看运气。
// curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
```

4.  查看密钥是否安装成功

```bash
sudo apt-key fingerprint 0EBFCD88

# 成功返回下面内容
  pub   4096R/0EBFCD88 2017-02-22              Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88  
  uid     Docker Release (CE deb) <docker@docker.com>  
  sub   4096R/F273FCD8 2017-02-22
```

5.  在 `source.list` 中添加 `docker-ce` 软件源

```bash
sudo deepin-editor /etc/apt/sources.list.d/docker.list # 使用 deepin 默认的编辑器新建并打开 docker.list 文件
# 写入一条内容如下：
deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/debian buster stable # 这里 buster 是 debain 版本的代号，deepin20 是debain10 代号为 buster，编辑完成后保存
```

**注意： 使用 `cat /etc/debian_version` 查看自己的 `debain` 版本。**

6.  更新仓库

```bash
sudo apt update
```

7.  安装 `docker` 

```bash
apt install docker-ce docker-ce-cli containerd.io
```

8.  验证

```bash
docker version # 输出以下信息
Client: Docker Engine - Community
 Version:           19.03.13
 API version:       1.40
 Go version:        go1.13.15
 Git commit:        4484c46d9d
 Built:             Wed Sep 16 17:02:55 2020
 OS/Arch:           linux/amd64
 Experimental:      false
 ...
 
 sudo docker run hello-world # 正常下载并输出说明安装成功
```

9.  报权限错误

由于 `docker` 需要管理员启动，所以如果是普通用户使用 `docker` 命令会报错，加上 `sudo` 可以解决，当然每次这样比较繁琐，直接将账号加入 `docker` 用户组就可以正常使用了，需要注销后生效

```bash
sudo usermod -aG docker $USER
```

10.  更换国内镜像

`docker` 的国内镜像比较慢，使用国内的镜像进行加速，使用 `docker-cn` 提供的镜像源

```bash
sudo deepin-editor /etc/docker/daemon.json # 使用默认编辑器打开文件，不存在则创建文件

# 写入以下内容
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

重启 `docker` 生效 

```bash
sudo service docker restart
```

### 启用或关闭开启启动

使用 `chkconfig` 来管理开机启动项

```bash
sudo apt install chkconfig 
# 列出所有启动项
sudo chkconfig
# chkconfig --help 查看帮助命令
```

移除启动项

```bash
sudo chkconfig --del docker
```

