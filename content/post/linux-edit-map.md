---
title: "Linux 下更改大写锁定键为ESC"
date: 2020-12-11T20:37:12+08:00
lastmod: 2020-12-11T20:37:12+08:00
draft: false
keywords: [Linux 下更改大写锁定键为ESC]
description: ""
tags: [linux]
categories: [linux]
---
Caps_Lock
大写锁定键，一般用的很少。但是它的位置又十分容易按到，而 `esc` 用的频率比较高，但是，位置却很尴尬。尤其在 `vim` 或一些终端之中，所以我们可以将键位进行更换。

一般有两种方法进行替换，一种使用软件内的自定义键位映射，一种直接将系统键位进行替换。
1. 第一种方式比较灵活，但是软件使用频率长会和操作系统的设定搞混。
2. 第二种方法在更换电脑后可能会不适应。

当然在 `vim` 中，我们可以定义其他的按键来代替 `esc` ，并不需要替换 `capslock`。也是因为在 `vim` 中，无法表示 `<CapsLock>` 键，所以无法替换。

#### 更换系统键位（linux）
在 `deepin` 中，可以使用 `xmodmap` 来进行键位替换
```bash
xmodmap -help # 查看帮助命令
```
首先在 `~` 目录下，创建一个 `.xmodmap` 的文件，文件内容如下
```bash
remove Lock = Caps_Lock
keycode 9 = Caps_Lock NoSymbol Caps_Lock
keycode 66 = Escape NoSymbol Escape
add Lock = Caps_Lock
```
使用命令 ` xmodmap ~/.xmodmap` 即可将更改生效

如果想要更改回来，只删除文件是不行的，需要替换过来在重载配置。
**重置配置**
```bash
remove Lock = Caps_Lock
keycode 66 = Caps_Lock NoSymbol Caps_Lock
keycode 9 = Escape NoSymbol Escape
add Lock = Caps_Lock
```
再执行 `xmodmap ~/.xmodmap` 即可
----
在 `vim` 中进行替换
```bash
inoremap jj <Esc>`^ # 在插入模式将 jj 按键映射为 `esc`,并返回到上次修改的地方.

```

