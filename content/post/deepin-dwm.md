---
title: "Deepin 极简主义 -- dwm"
date: 2021-02-09T10:58:52+08:00
lastmod: 2021-02-09T10:58:52+08:00
draft: false
keywords: [deepin,dwm]
description: "Deepin 极简主义 -- dwm"
tags: [deepin]
categories: [deepin]
---

### `dwm`

`dwm` 是X的动态窗口管理器。它管理平铺、单镜头和浮动布局的窗口。所有的布局都可以动态应用，优化了使用中的应用程序和执行的任务的环境。官网：https://dwm.suckless.org/

`dwm` 十分精简，源码只有几十kb，内存占用很低。由于 `deepin` 上使用的是 `kwin` 窗口管理器，有些软件风格不够统一，可以试试 `dwm` ，但是 `dwm` 与 `deepin` 上的好多软件也都有些不兼容，在软件的显示上也有些问题，但是如果你平时是使用命令行程序的话，使用 `dwm` 是可以大大的提高效率的。建议还是使用 `arch +dwm`，或者 `arch` 系的 `manjaro`。使用起来效果会更好一些。

#### 安装

当然在 `deepin` 上也是可以安装并正常使用的。不过在官方的仓库中是编译好的，配置也无法更改，但是在开机启动页可以检测到 `dwm` 的管理器。而自己使用源码编译则可以更好的配置，如字体、快捷键等，但是自己编译安装的需要额外增加一些配置才可以检测到 `dwm`并进入。

**官方仓库安装：**

```bash
sudo apt install dwm # install
pkill -kill -u caoayu # 注销当前用户
```

注销后就可以在启动页右下角看多出一个图标，可以进去选择要进入的窗口管理器。

**源码编译安装：**

首先到官网克隆 `dwm` 源码，不推荐直接下载，因为后面要手动修改配置并加入一些补丁。

```bash
git clone https://git.suckless.org/dwm # clone 
cd dwm
make 
sudo make clean install
```

此时也已经编译并安装过了，配置信息在 `config.h` 文件中，编译时的配置文件是 `config.mk` 其中可以定义安装路径等信息。

后面还在这个目录内进行操作就可以继续覆盖式安装。

#### 使用

注销后即可在登录页右下角看到切换窗口管理器的图标。点击进入即可。或者使用命令进入

```bash
startx dwm
```

使用命令注销用户

```bash
pkill -kill -u caoayu #caoayu为当前用户名
```

如果是通过手动编译安装的，在登录页是找不到的。需要添加一个文件到 `/usr/share/xsessions/`

命名为 `dwm.desktop` ,在 `xsessions` 目录中，可以看到存在了一个 `deepin.desktop` 文件，这个就是默认的 `deepin` 的启动，只要按着这个文件写启动 `dwm` 的脚本就可以了

```bash
# /usr/share/xsessions/dwm.desktop
# 写入一下内容
[Desktop Entry]
Name=dwm
Comment=dwm Desktop Environment
Exec=/usr/bin/dwm # dwm安装路径
TryExec=/usr/bin/dwm # dwm安装路径
```

此时注销后就可以找到 `dwm` 的启动选项了。

### 推荐补丁

手动编译安装的 `dwm` 是没有什么额外的功能的，需要通过打补丁来新增功能。关于补丁的安装，跟前面的 `st` 是相同的。[st](https://blog.caoayu.top/post/st/)

1.  `dmune` 一个快捷搜索程序的窗口，默认快捷键是 `alt+p` 。 地址：https://tools.suckless.org/dmenu/

2.  `autostart` 自动启动脚本，在 `dwm`启动后自动启动一些脚本或软件，不需要在加入系统服务中。地址：https://dwm.suckless.org/patches/autostart/

3.  `awesomebar` 更酷的任务栏。地址：https://dwm.suckless.org/patches/awesomebar/

4.  `fullscreen` 使某个单独的程序全屏。地址：https://dwm.suckless.org/patches/fullscreen/

5.  `slock` 屏幕锁。地址：https://tools.suckless.org/slock/

    补丁仓库地址：https://dwm.suckless.org/patches/ ，可以直接到这个地址查看所有的补丁。

----

一些问题

1.  跟 `deepin` 不是很兼容。
2.  需要配置一些自动启动的脚本，比如声音，蓝牙等。
3.  需要自定义任务栏，如显示内存占用，上传下载速度，时间，网络。
4.  默认是使用 `st` 作为终端模拟器，可以更改为 `alacritty`。如果要使用默认，也需要编译安装，甚至配置并添加补丁。