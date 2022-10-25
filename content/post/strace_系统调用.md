---
cssclass:
title: strace系统调用
tags: [IT/PHP, IT/PHP/多进程]
image-auto-upload: true

date: 2022-10-03 20:44:46
lastmod: 2022-10-25 08:18:54
---
# strace系统调用
[https://strace.io/](https://strace.io/)

linux strace 在线手册 [https://man7.org/linux/man-pages/man1/strace.1.html](https://man7.org/linux/man-pages/man1/strace.1.html)

应用程序调用操作系统提供的函数库或函数 system call

**-s** _strsize_

**--string-limit**=_strsize_

Specify the maximum string size to print (the default is

32). Note that filenames are not considered strings and

are always printed in full.

**-f**

**--follow-forks**

Trace child processes as they are created by currently

traced processes as a result of the [fork(2)](https://man7.org/linux/man-pages/man2/fork.2.html), [vfork(2)](https://man7.org/linux/man-pages/man2/vfork.2.html) and

[clone(2)](https://man7.org/linux/man-pages/man2/clone.2.html) system calls. Note that **-p** _PID_ **-f** will attach

all threads of process _PID_ if it is multi-threaded, not

only thread with _thread_id_ = _PID_.

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112262052742.png)