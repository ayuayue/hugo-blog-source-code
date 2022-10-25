---
cssclass:
title: Bash会话
tags: [IT/PHP, IT/PHP/多进程]
image-auto-upload: true
date: 2022-10-03 20:18:47
lastmod: 2022-10-25 08:10:47
---
# Bash会话

1.  会话就是一个进程组,或是多个进程组的集合
2.  一个会话至少可以有一个控制终端(物理终端/伪终端)
3.  一个会话至少有一个前台进程组(前台就是能输入的bin/bash),其他就是后台进程组
4.  一个会话如果连接了一个控制终端,就叫控制进程,如 bin/bash进程,创建的子进程也会继承父进程的控制终端

### 创建一个会话

posix_setsid()

1.  不能使用组长进程调用,调用会出错
2.  我们一般先创建一个子进程,让父进程exit,由子进程,由子进程调用setsid
3.  调用setsid之后,该进程会变成组长进程,同时也会变成会话首进程
4.  同时该进程没有控制终端,在终端输入任何数据都没有反应

```PHP
<?php
function showPID(){
    fprintf(STDOUT, "pid =%d,ppid=%d,pgid=%d,sid=%d\n",posix_getpid(),posix_getppid(),posix_getpgid(posix_getpid()),posix_getsid(posix_getpid()));
}
showPID();
// create
$pid = pcntl_fork();

if($pid > 0){
    exit(0);
}
if(posix_setsid() == -1){
    echo pcntl_strerror(pcntl_errno());
}else{
    echo "会话创建成功\r\n";
}
showPID();
while(1){
    sleep(1);
}
```