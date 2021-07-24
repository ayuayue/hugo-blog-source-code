---
title: "使用Scrcpy在Linux操控安卓"
date: 2021-03-08T17:55:06+08:00
lastmod: 2021-03-08T17:55:06+08:00
draft: false
keywords: [使用Scrcpy投屏到Linux]
description: "使用Scrcpy投屏到Linux"
tags: [Linux]
categories: [Linux]
---

在 `Windows` 上系统自带了投屏功能，然而 `deepin` 也有 深度演示助手 ，不过我并没有在系统中找到。论坛中的反响都是在 `15` 版本，现在已经是 `20.2` 了。

寻找到了一个叫 `QTScrcpy` 的应用，可以连接手机并实现实时的控制，这比投屏的功能还要强大一点，更重要的是延迟很低、并且是原生的，很轻量。便到深度商店下载使用，根据说明进行连接。最后启动服务的时候就闪了一下没了。自觉是商店的软件打包又出问题了，就来到了星火商店下载重新下载并使用，发现依然如上次一样一闪而过。

最终直接使用 `Cli` 应用来启动这个服务，[github仓库地址](https://github.com/Genymobile/scrcpy) 。

### 编译安装

首先安装需要的依赖

```bash
# runtime dependencies
sudo apt install ffmpeg libsdl2-2.0-0 adb

# client build dependencies
sudo apt install gcc git pkg-config meson ninja-build \
                 libavcodec-dev libavformat-dev libavutil-dev \
                 libsdl2-dev
# server build dependencies
sudo apt install openjdk-11-jdk
```

设置环境变量

```bash
# Linux
export ANDROID_SDK_ROOT=/usr/lib/android-sdk # 该路径为 Android SDK的路径
export PATH=$PATH:$ANDROID_SDK_ROOT
```

编译服务，如果编译失败可以下载编译好的预编译服务，推荐使用这种方式。[`scrcpy-server-v1.17`](https://github.com/Genymobile/scrcpy/releases/download/v1.17/scrcpy-server-v1.17)

克隆仓库并进入目录

```bash
git clone https://github.com/Genymobile/scrcpy
cd scrcpy
```

编译

```bash
meson x --buildtype release --strip -Db_lto=true \
    -Dprebuilt_server=/path/to/scrcpy-server # 下载的预编译服务文件路径
ninja -Cx
```

运行

如果没有错误出现，此时就可以在当前目录进行运行了。注意手机需要开启 `USB` 调试的几个选项。

开启这几项后就可以在 `scrcpy` 目录下执行 `./run x` 启动服务。

![image-20210308211641602](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210308211641602.png)

![image-20210308211703414](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210308211703414.png)

### 安装到系统

如果不想每次都进入路径使用命令，可以直接安装到系统中，或者直接设置一个别名。

```bash
sudo ninja -Cx install # 安装到系统
alias scrcpy='cd xxx & ./run x'
```

安装到系统后会生文件成到 `/usr/local/bin` 和 `/usr/local/share/scrcpy` 中。

### 卸载

想要卸载可以直接删除这两个文件即可。