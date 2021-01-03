---
title: "Go 多版本管理"
date: 2020-10-13T20:34:06+08:00
lastmod: 2020-10-13T20:34:06+08:00
draft: false
keywords: []
description: ""
tags: []
categories: []
---

### `Go`版本

跟其他编程语言一样,`go`的项目也会有对版本的要求.所以这个时候我们就需要进行对`go`的版本进行管理.

管理`go`版本的方式比较多,这里介绍一种比较简单的方式 -- [**g**](https://github.com/voidint/g)

### `g`的方式

从`g`的 [**github**](https://github.com/voidint/g) 源码及介绍中,可以大概了解到进行版本控制的方法.

> 通过在官网下载二进制包并进行解压,解压后放入不同的文件夹中,使用软连接的方式创建一个目录,将当前`go`的目录链接进去,配置为环境变量即可

### 使用方式

从`release`中下载并配置到环境变量中,通过终端运行命令

```bash
> g
NAME:
  g - Golang Version Manager

 USAGE:
  g.exe  command [arguments...]

 VERSION:
  1.2.0

 AUTHOR:
  voidint <voidint@126.com>

 COMMANDS:
    ls         List installed versions
    ls-remote  List remote versions available for install
    use        Switch to specified version
    install    Download and install a version
    uninstall  Uninstall a version
    clean      Remove files from the package download directory
    help, h    Shows a list of commands or help for one command

 GLOBAL OPTIONS:
  --help, -h     show help
  --version, -v  print the version

 COPYRIGHT:
  Copyright (c) 2019-2020, voidint. All rights reserved.
```

一般通过`ls-remote`来获取远程的所有版本.想要过滤其他版本,只获取稳定版可以使用一下命令

```bash
❯ g ls-remote stable
* 1.14.9
  1.15.2
```

显示已安装的版本 `g ls`

安装命令 `g install 1.15.2`

卸载命令 `g uninstall 1.15.2`

切换版本 `g use 1.15.2`

清除下载的源码包 `g clean`

### 注意事项

1. `g`作为独立管理`go`版本的存在,不能识别其他方式安装的`go`,并且安装后会在 `g` 的目录下.

2. 如果获取远程版本列表失败,由于网络的问题,可以使用代理,并且新增环境变量

   ```
   名 : G_MIRROR 
   值 : https://golang.google.cn/dll/
   ```

3. 如果用其他方式安装过`go`,并配置过代理跟其他.可以直接更改环境变量中的`GOROOT` 指向 `g` 生成的目录即可