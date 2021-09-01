---
title: "Php 程序"
date: 2021-09-01T22:49:34+08:00
lastmod: 2021-09-01T22:49:34+08:00
draft: false
keywords: []
description: ""
tags: []
categories: [php]
---

### php 程序

###  程序

#### 什么是程序？

程序一般指可执行文件，在 linux 上按 ELF 格式进行存储，并不由后缀进行标识。可以使用 file 命令查看

![image-20210830230948828](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210830230948828.png)

#### ELF 文件

全称：Executable Linkable Format

#### ELF 文件的种类

1. EXEC 可执行文件
2. REL 可重定向文件
3. Share Object File 共享目标文件
4. core dump 文件

REL 在 linux 中为 *.o *.a 也成为静态库文件

*.o 成为目标文件，可被链接器链接成为可执行文件，静态 动态库文件，如php event.so sockets.so

core dump 文件用来存储进程产生的异常信息

#### ELF 文件的结构

可以使用 objdump / readelf 命令查看

![image-20210830232157875](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210830232157875.png)

### 进程

一个正在运行的程序就是一个进程，系统会分配一个唯一的标识ID

PHP 解释器如何解析 PHP 脚本

首先启动 php 可执行程序，也就是系统中安装的 php ，然后将命令的参数，包括当前终端的 环境变量都传递过去，由 php 解释器解析参数。

在 linux 下可以使用 strace 进行追踪一个程序或一个进程的运行，记录从开始到结束的所有系统调用

![image-20210830232827632](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210830232827632.png)

### 补充小知识

对于所有的脚本语言来说，在 linux 下，可以直接使用脚本名执行，常见的就是 sh 脚本，但是 php,python 等脚本一样可以实现这个功能。

只需要在写脚本的时候，开头定义解释器的路径即可，可以使用 which php 查看 php 所在的路径

![image-20210830233526960](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210830233526960.png)

```bash
#!/home/caoayu/software/php/bin/php
<?php
echo time();
```

