---
cssclass:
title: IPC消息队列
tags: [IT/PHP, IT/PHP/多进程]
image-auto-upload: true
date: 2022-10-03 20:58:46
lastmod: 2022-10-25 08:15:49
---
# IPC消息队列
sysvmsg 扩展

/proc/sysvipc/

消息队列: 实际就是一个队列,由操作系统维护, 使用msgget来创建一个消息队列,创建成功返回队列的ID

键key [对应内部数据结构的标识符ID]

PHP封装的消息队列 [https://www.php.net/manual/zh/book.sem.php](https://www.php.net/manual/zh/book.sem.php)

底层调用 msgrcv ,msgsnd , msgget, msgctl

使用 ipcs 可以查看linux IPC 信息

```Bash
-bash_4.2#> ipcs

------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages    

------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch     status      
0x00000000 2          gdm        777        16384      1          dest         
0x00000000 5          gdm        777        2129920    2          dest         

------ Semaphore Arrays --------
key        semid      owner      perms      nsems     
```

### 写入

[https://www.php.net/manual/zh/function.msg-send.php](https://www.php.net/manual/zh/function.msg-send.php)

不需要同时一端先读取,一端写入,一端进行操作就可以

每次写入都是都是独立的,不会拼接到一起

```PHP
<?php
$key = ftok("sleep.php", 'x'); // 就是把文件与id转化为一个key,根据inode
$msgid = msg_get_queue($key);

echo msg_send($msgid, 1, "hello"); // php会把数据序列化存储

echo $msgid;
```

### 接收

[https://www.php.net/manual/zh/function.msg-receive.php](https://www.php.net/manual/zh/function.msg-receive.php)

每次接收只接受一次上传的数据,多次发送需要多次接受,如果没有写入数据,读端会阻塞, 只能接受同种类型的消息队列, 接收类型与发送类型相同

```PHP
<?php
$key = ftok("sleep.php", 'x'); // 就是把文件与id转化为一个key,根据inode
$msgid = msg_get_queue($key);
msg_receive($msgid,0,$msgType,1024,$msg);
echo $msg;



```

### 注意

如果发送的时候关闭了PHP默认的序列化,那么接受的时候一样需要关闭序列化

如果开启了PHP默认的序列化,接受数据时的缓冲区要设置大于发送的数据长度,因为序列化之后数据会变多.

如果关闭了PHP默认的序列化,并且接受时的字节长度小于发送长度,会报错,可以使用参数 MSG_NOERROR 关闭报错,并且会截断发送的数据,超出接受长度的数据会被丢弃

```PHP
msg_receive($msgid,1,$msgType,1024,$msg,false,MSG_NOERROR);
```

### 非阻塞方式

如果使用非阻塞方式,该函数调用的次数非常高,占用CPU资源更高

阻塞方式 : 调用次数非常低,因为必须等消息队列有数据才返回

消息队列是有限制的,发送时如果不阻塞可能会导致超出系统的限制而报错

使用 ulimit 查看系统限制

```Bash
/www/php-mutiprocess # ulimit -a
core file size (blocks)         (-c) unlimited
data seg size (kb)              (-d) unlimited
scheduling priority             (-e) 0
file size (blocks)              (-f) unlimited
pending signals                 (-i) 23907
max locked memory (kb)          (-l) 64
max memory size (kb)            (-m) unlimited
open files                      (-n) 1048576
POSIX message queues (bytes)    (-q) 819200
real-time priority              (-r) 0
stack size (kb)                 (-s) 8192
cpu time (seconds)              (-t) unlimited
max user processes              (-u) unlimited
virtual memory (kb)             (-v) unlimited
file locks                      (-x) unlimited
```

```PHP
while (1){
        // MSG_IPC_NOWAIT msgrcv 该系统调用函数将立马返回,不会阻塞
        $res = msg_receive($msgid, 0, $msgType, 1024, $msg,true,MSG_IPC_NOWAIT,$error);
        if($error != MSG_ENOMSG){
            echo $msg.PHP_EOL;
        }
    }
```