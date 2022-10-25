---
cssclass:
title: 3-1进程标识与fork
tags: [IT/PHP, IT/PHP/多进程]
image-auto-upload: true
date: 2022-10-03 20:42:46
lastmod: 2022-10-25 00:52:47
---
# 3-1进程标识与fork
## 进程标识与fork
一个程序被加载到内存中运行,系统会为这个进程分配相应的标识信息,比如 pid,ppid,uid,euid,pgid,sid,gid,egid...

php对应的获取进程标识信息的函数(依赖posix扩展) [https://www.php.net/manual/zh/book.posix.php](https://www.php.net/manual/zh/book.posix.php)

posix_getpid() 获取进程的pid

posix_getppid() 父进程pid

posix_getgrgid() 进程组组长的id

posix_getsid(posix_getpid()) 会话id

posix_getuid() 用户标识id,当前登录用户 实际用户id

posix_getgid() 组id

posix_geteuid() 有效用户id

posix_getegid() 有效组id

**使用pstree可以查看进程间的关系,使用ps查看进程运行的信息**

进程状态

"R (running)", /* 0 */

"S (sleeping)", /* 1 */

"D (disk sleep)", /* 2 */

"T (stopped)", /* 4 */

"t (tracing stop)", /* 8 */

"X (dead)", /* 16 */

"Z (zombie)", /* 32 */

R : running 正在运行的进程

Z : zombie 僵尸进程

S : sleep 睡眠进程 可被系统唤醒

T : stop 暂停的进程 可被系统唤醒

D : 也是暂停,但不回被系统唤醒

查看当前用户信息

1.  `who` 命令
2.  cat /etc/passwd

#### fork

fork创建一个子进程,依赖pcntl扩展 [https://www.php.net/manual/zh/book.pcntl.php](https://www.php.net/manual/zh/book.pcntl.php)

linux 手册[https://man7.org/linux/man-pages/man2/fork.2.html](https://man7.org/linux/man-pages/man2/fork.2.html)

PHP创建一个子进程

**pcntl_fork()** 函数创建一个子进程，这个子进程仅PID（进程号） 和PPID（父进程号）与其父进程不同,

成功时，在父进程执行线程内返回产生的子进程的PID，在子进程执行线程内返回0。失败时，在 父进程上下文返回-1，不会创建子进程，并且会引发一个PHP错误。

```PHP
$pid = pcntl_fork();
//父进程和子进程都会执行下面代码
if ($pid == -1) {
    //错误处理：创建子进程失败时返回-1.
     die('could not fork');
} else if ($pid) {
     //父进程会得到子进程号，所以这里是父进程执行的逻辑
     pcntl_wait($status); //等待子进程中断，防止子进程成为僵尸进程。
} else {
    die('i am child bye');
     //子进程得到的$pid为0, 所以这里是子进程执行的逻辑。
}
```

从fork函数之后,两个进程都会运行,但是可以通过返回的$pid是否为0来分别使用不同的进程执行不同逻辑. 子进程会共享fork之前的所有函数和变量,也就是复制父进程的代码段和数据段

如果同时运行多个进程,一般父进程先运行,但是还是由操作系统的进程调度决定

如果父进程先运行先结束,子进程就没有了父亲,成为了孤儿进程,这时子进程由系统1号进程接管,并运行在后台,所以我们一般让子进程先运行先结束

**子进程遵从COW机制,父子进程共享变量,当子进程要修改父进程中的数据时,会先复制一份,修改复制的那份**

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112302315303.png)

###### ps aux

O：进程正在处理器运行,这个状态从来没有见过.  
S：休眠状态（sleeping）  
R：等待运行（runable）R Running or runnable (on run queue) 进程处于运行或就绪状态  
I：空闲状态（idle）  
Z：僵尸状态（zombie）  
T：跟踪状态（Traced）  
B：进程正在等待更多的内存页  
D: 不可中断的深度睡眠，一般由IO引起，同步IO在做读或写操作时，cpu不能做其它事情，只能等待，这时进程处于这种状态，如果程序采用异步IO，这种状态应该就很少见到了