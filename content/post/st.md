---
title: "Linux Simple Terminal"
date: 2021-01-06T20:53:11+08:00
lastmod: 2021-01-06T20:53:11+08:00
draft: false
keywords: [Linux]
description: "Linux Simple Terminal"
tags: [Linux]
categories: [Linux]
---
### Terminal
`linux` 下有许多优秀的 `Termainl`，我在用的有`deepin-terminal`,`alacritty`,'simple terminal'.`alacritty` 是一款使用显卡渲染的终端模拟器，非常的快并且流畅，并且支持终端显示图片，所以比 `deepin-terminal` 更让我喜欢，然而 `simple terminal` 确实一款十分简单的终端模拟器，虽然简单但功能却一个不少，体积更小。甚至连配置文件都没有，每次更改配置都要修改源代码并且编译生成程序，实在是够简单。
但是在 `deepin` 上无法直接安装，需要安装几个依赖的软件。
```bash 
sudo apt install libx11-dev
sudo apt install libxft-dev
```
#### install
官方地址 ：https://st.suckless.org/ 。最后有一个 `Download` 但是不建议直接下载，可能版本比较老，上面有个 git 链接，可以直接克隆源仓库。
```bash 
git clone https://git.suckless.org/st
```

如果是 deepin 操作系统，还需要更改源代码中的 `config.mk` 文件，更改刚才安装的依赖软件位置
```bash
# 更改如下
X11INC = /usr/X11R6/X11                                                                                     
X11LIB = /usr/include/X11
```
编译安装：`sudo make clean install`
此时通过 st 命令即可打开。

#### 更改配置
前面也说了，st 没有配置文件，所以我们直接进源码目录，找到 config.h 文件，通过注释来更改自己的内容，一般更改字体跟窗口大小即可，后面可以通过打补丁的方式增加更多的功能。
```bash 
# config.h 
static char *font = "JetBrains Mono:pixelsize=24:antialias=true:autohint=true"; # 更改字体跟大小

sudo make clean install # 重新编译并安装，使用 st 命令即可打开。
```

#### 添加补丁
在官方地址左侧存在一列 Patch, 即为补丁列表，其中有更改外观的，比如透明度，颜色。也有增加功能的。
[官方补丁地址](https://st.suckless.org/patches/) 
1. 下载补丁到本地，可以使用 `wget ` + 链接来直接下载到源码目录下。
2. 安装补丁： 
```bash 
patch < fillname # 补丁文件
```
3. 设置配置文件，因为 `config.xxx.h` 文件是模板文件，真正的配置文件是 `config.h` ,推荐删除那个模板文件，当补丁执行后询问配置文件时输入 `config.h` 即可。
如果报权限问题使用 `sudo patch <` 来打补丁。

* 成功：如果补丁成功，输出全部是 `success`，直接编译运行即可查看补丁的实际效果。
* 失败：如果失败，也会响应的输出，打开补丁文件可以发现，所有的补丁文件都是一个 `diff` 文件，文件描述了补丁文件与配置文件的差异，+ 符号代表是需要添加的内容，- 代表需要删除的内容，根据文件描述来手动修改 `config.h` 文件即可，出现错误的原因就是没有自动的完成替换，那就手动完成。

## 推荐补丁 
1. st-alpha : 设置终端透明度
2. st-anysize : 设置终端大小为占满屏幕
3. st-copyurl : 对于终端输出的url，使用 alt + l 快捷键来回选择，回车复制。
4. st-dracula : 终端主题
5. st-blinking_cursor : 终端光标闪烁
6. st-desktopentry : 添加终端的应用图标及应用desktop文件
7. st-font2 : 字体代替，如果缺少字体，可以设置多个字体来逐级渲染
8. st-hidecursor : 终端输入时隐藏光标，防止光标遮挡住字符
9. st-opencliphboard : 结合 copyurl 使用，将复制的链接直接用浏览器打开
10. st-rightclickpaste : 右键粘贴
11. st-scrollback : 快捷键滚动屏幕，默认 shift pageon / shift pageup
12. st-scrollback-mouse : 设置鼠标滚动屏幕输出

更多补丁可以在官方的补丁网站上查看，并且都有说明。如果打补丁失败就看下说明，然后手动改下文件即可。















