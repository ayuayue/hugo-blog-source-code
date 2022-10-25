---
cssclass:
title: 3-2进程exec
tags: [IT/PHP, IT/PHP/多进程]
image-auto-upload: true
date: 2022-10-03 20:50:46
lastmod: 2022-10-25 00:21:49
---
# 3-2进程exec
## 进程exec
exec函数的功能 : 用来执行一个程序,PHP中使用 pcntl_exec [https://www.php.net/manual/zh/function.pcntl-exec.php](https://www.php.net/manual/zh/function.pcntl-exec.php), 内部调用时 execve [https://man7.org/linux/man-pages/man2/execve.2.html](https://man7.org/linux/man-pages/man2/execve.2.html)

一般的用法: 父进程先创建一个子进程,然后子进程调用这个函数, 正文段[代码段]+数据段 会被新程序替换,它的一些属性会继承父进程, PID并没有发生变化

```PHP
$pid = pcntl_fork();
if($pid == 0){
    pcntl_exec("/usr/local/php/bin/php",["sleep.php"],["test"]);
//    pcntl_exec("bash.php",['a','b','c']);
}
```

上面代码参照 2-2命令行参数与环境表2-3解释器文件这两个知识点

注: **当调用exec函数执行的程序执行完后,将不在继续往下执行,如果父进程调用wait阻塞回收,此时会直接被父进程回收,程序退出**

```PHP
$pid = pcntl_fork();
if($pid == 0){
    pcntl_exec("/usr/local/php/bin/php",["sleep.php"],["test"]);
//    pcntl_exec("bash.php",['a','b','c']);
    echo "hello world"; // 如果 sleep.php 执行完成后,此处不会执行到
}
pcntl_wait($status);
```