---
title: "Php 进程知识"
date: 2021-09-01T22:48:50+08:00
lastmod: 2021-09-01T22:48:50+08:00
draft: false
keywords: []
description: ""
tags: []
categories: [php]
---

### php 操作进程知识

linux 手册  [man7.org](https://man7.org/linux/man-pages/)

php pcntl ，posix扩展。封装了 linux 系统 api 可以操作进程。

#### php 内置函数

1. getmypid 获取当前进程的pid
2. getmygid 获取当前进程组的id
3. getmyuid 获取当前用户的id

#### php 常量

STDOUT 标准输出
STDIN 标准输入

posix 接口 意义同内置函数

1. posix_getpid(); 获取当前进程的pid
2. posix_getppid(); 获取父进程id
3. posix_getgrp(); 获取进程组长进程id
4. posix_getuid(); 获取实际用户id
5. posix_getsid(posix_getpid()); 获取进程会话id
6. posix_geteuid(); 获取有效用户id
7. posix_getegid(); 

查看进程

1. pstree 
   参数：

   ![image-20210829233507050](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20210830230247.png)

   1. a 显示完整指令
   2. p 显示进程号和进程id
   3. u 显示用户名称
      常用参数 pstree -ap, pstree -p pid

2. ps

   ![image-20210829233546714](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20210830230320.png)

   参数：

   1. a 显示终端下所有执行的程序
   2. u 以用户为主的格式显示
   3. x 显示所有程序
   4. e 显示环境变量
   5. j 采用工作控制的格式显示
      常用参数 ps aux, ps exj

进程的状态

![image-20210829233734687](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210829233734687.png)

1. R 运行中
2. Z zomdie 僵尸进程
3. S sleeping 可被唤醒
4. T stop
5. D 无法唤醒
6. I 闲置

会话进程
一般指命令执行的终端进程，一般默认是 bash 进程，可以是用 echo $$ 来输出 bash 的进程id，也可以使用 posix_getsid(pid) 获取;

![image-20210829233424797](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210829233424797.png)

用户 id 和组 id

是在用户创建的时候分配的，可以在 /etc/passwd 文件中看到用户对应的信息，以及用户对应的默认终端

![image-20210829233352076](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210829233352076.png)



### 创建子进程

函数 pcntl_fork

fork 后，子进程会 copy 父进程的数据段和代码段，由操作系统随机先执行其中一个，如果父进程被杀掉，子进程仍然运行，此时进程属于孤儿进程，由 1 号进程（系统进程）接管。所以一般先结束子进程，父进程来回收子进程的资源。

孤儿进程的危害：进程进入后台，占用系统资源。

当返回小于 0 ，创建失败

等于0 ， 此时进入子进程的逻辑

大于0，进入父进程的逻辑

### 进程退出

退出的情况

1. 运行到最后一句
2. 运行时遇到 return
3. 运行时遇到 exit 函数
4. 异常产生
5. 进程接受到中断信号

一个进程，要么是正常退出，要么是异常退出，但是不管哪种方式都会有一个终止状态码，但此时进程并不会完全退出，而是驻留在内存中，父进程可以使用 pcntl_wait 函数来获取子进程的状态信息，并会释放子进程的内存空间，此时才是完全退出。否则会产生大量僵尸进程

僵尸进程：子进程已经结束，但是父进程还没使用 wait/wait_pid 来回收

程序示例

```php
<?php

$pid = pcntl_fork();

if($pid === 0){
    fprintf(STDOUT,'我是子进程：%d', posix_getppid());
}else{
    fprintf(STDOUT,'我是父进程：%d',posix_getpid());
    sleep(3);
    while(1){
        ;
    }
}
```

子进程运行后结束，父进程不结束。使用 pstree 先查看父子进程的信息，在使用 ps 命令查看进程状态

```bash
echo $$ // 获取当前 bash 的进程
pstree -ap pid // pid 为上一步输出的id
```

![image-20210901231939794](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210901231939794.png)

可以看到下面 子进程 使用 （） 包裹，这就是一个僵尸进程

![image-20210901232030620](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210901232030620.png)

可以看到父进程的状态为 S，子进程为 Z （僵尸进程）

而对于一个运行中的进程，会在系统的 /proc/pid 目录下保留进程的相关信息

![image-20210901232310218](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210901232310218.png)

这里面存储关于进程的一些信心，僵尸进程虽然退出，但是仍然保留有系统文件，如果僵尸文件过多，会导致系统的内存不足。

#### 进程的回收

pcntl_wait 函数

使用 pcntl_wait 的情况

1. 没有子进程，可能返回错误
2. 子进程没有退出，会阻塞
3. 传递第三个参数，可以不阻塞

阻塞方式

```php
<?php

$pid = pcntl_fork();

if($pid === 0){
    fprintf(STDOUT,"我是子进程：%d\n", posix_getpid());
}else{
    fprintf(STDOUT,"我是父进程：%d\n",posix_getpid());

    $exitPid = pcntl_wait($status);
    if($exitPid > 0){
        fprintf(STDOUT,"child process pid %d, exit code: %d\n", $pid,$status);
    }else{
        fprintf(STDOUT,"wait error\n");
    }
    while(1){
        fprintf(STDOUT,"i am father\n");
        sleep(3);
    }
}
fprintf(STDOUT,"process exit %d\n",getmypid());
```

此时运行该脚本，子进程退出后会被父进程回收，可以使用上面的 pstree ps 命令查看，只有父进程一个，并且子进程的进程目录也是不存在的。