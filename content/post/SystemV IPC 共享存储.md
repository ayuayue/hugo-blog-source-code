---
cssclass:
title: SystemV IPC 共享存储
tags: [IT/PHP, IT/PHP/多进程]
image-auto-upload: true

date: 2022-10-03 20:34:46
lastmod: 2022-10-25 08:27:54
---
# SystemV IPC 共享存储
sysvshm 扩展

shmget shmat shmdt shmctl

/proc/sysvipc/

共享存储实际上就是系统会开辟一块存储空间,进程会使用相关函数 shmget 来映射(连接)到进程的地址空间 virtual space address

内存 : 内存分配出来的是一块连续的存储空间(在分配存储空间的时候可以指定大小,一般按页分配 4096 8192 字节

PHP实现,封装了shm 以及 Shared Memory函数,但是内部实现一模一样

[https://www.php.net/manual/zh/ref.sem.php](https://www.php.net/manual/zh/ref.sem.php)

[https://www.php.net/manual/zh/ref.shmop.php](https://www.php.net/manual/zh/ref.shmop.php) shmop扩展

shmat表示将创建好的共享存储区域关联到进程的地址空间