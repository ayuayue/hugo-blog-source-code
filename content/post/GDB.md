---
cssclass:
title: GDB
tags: [IT/GDB, IT/Programing]
image-auto-upload: true
date: 2022-10-03 20:14:47
lastmod: 2022-10-25 08:17:48
---
# GDB
是一个程序调试器。

### 启动方式

#### 1 直接使用gdb 后跟一个ELF可执行文件

```Bash
GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-120.el7
Copyright (C) 2013 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-redhat-linux-gnu".
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>...
Reading symbols from /usr/local/php74/bin/php...done.
(gdb) 
```

### 2只使用gdb命令

1.  使用 gdb | gdb -silent
2.  进入到要调试程序的目录 cd
3.  使用 file 命令加载 ELF可执行文件
4.  使用start指令启动程序，程序会停在入口函数

### 常用命令

普通断点：breakpoints

观察断点：watchpoints

条件断点：condition

捕捉断点：catch events

display：用来打印一些变量或表达式的值，程序暂停会自动打印变量的值

#### 调试指令

s：一行一行的执行【step】

p：用来打印变量或表达式的值【print】

whatis：打印变量的类型

list：列出函数中的代码

info frame：查看当前的调用堆栈

bt：上级调用者，调用链