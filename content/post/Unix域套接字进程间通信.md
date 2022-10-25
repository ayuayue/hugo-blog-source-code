---
cssclass:
title: Unix 域套接字进程间通信
tags: [IT/PHP, IT/PHP/多进程]
image-auto-upload: true

date: 2022-10-03 20:39:46
lastmod: 2022-10-25 08:01:55
---
# Unix 域套接字进程间通信
[[../../../管道通信]]
[[../../../信号量]]
[[IPC 消息队列]]
[[../../../SystemV IPC 共享存储]]
[[Unix 域套接字进程间通信]]

套接字 : socket 实际上是一种链接 套接字的通信域类型 :

ipv4 [AF_INET]

ipv6 [AF_INET6]

UNIX [AF_UNIX,AF_LOCAL] 也叫本地域,进程间通讯的时候不需要通过网络

ipv4,ipv6 通信要经过网卡,数据到达网卡时是一种数据帧 frame [ 目标网络物理地址| 源物理地址 ... | 数据 ]

unix 通信时不通过网卡

套接字类型 : TCP ,UTP, transmission control protocal, user datagram protocal

tcp : 需要连接 【 三次握手】 是可靠的,重传,有序,字节流服务,

udp : 不需要连接 , 不可靠,数据长度是固定的 【 unix 域的 udp 是可靠的 】

raw :

ipv4,ipv6 套接字作为服务进程的时候,是要绑定 ip,port 【 以便寻址，ip 主要用于确定进程间通信的时候知道网络上的确定的机器，port 用户确定进程间通信时，确定是哪个进程 】

Unix 套接字作为命名 Unix 域套接字时，一定要绑定地址【它的地址比较特殊，是一个文件，socket 文件】

1. 无命名的【创建好的 unix 域套接字不需要绑定地址】
2. 命名 unix 域套接字

### 无命名 unix 套接字

stream , socket 底层系统调用都一样

 [https://www.php.net/manual/zh/function.stream-socket-pair.php](https://www.php.net/manual/zh/function.stream-socket-pair.php)

 [https://www.php.net/manual/zh/function.socket-create-pair.php](https://www.php.net/manual/zh/function.socket-create-pair.php)

无命名 unix 域 用于具有血缘关系进程间通信 父子，兄弟

```PHP
<?php
// 无命名unix域 用于具有血缘关系进程间通信 父子，兄弟
// 全双工
$fd = stream_socket_pair(STREAM_PF_UNIX, STREAM_SOCK_STREAM, 0);
$pid = pcntl_fork();
if($pid == 0){
    while(1){
        $data = fread($fd[0], 128);
        if($data){
            fprintf(STDOUT, "recv data = %s\n",$data);
        }
        if(strncasecmp(trim($data), "quit",4) === 0){
            break;
        }
    }
    exit(0);
}
// $fd[0] 用于读
// $fd[1] 用于写
//print_r($fd);
while(1){
    $data = fread(STDIN, 128);
    if($data){
        fwrite($fd[1], $data);
    }
    if(strncasecmp(trim($data), "quit",4) === 0){
        break;
    }
}
$pid = pcntl_wait($status);
fprintf(STDOUT, "quit pid = %d\n",$pid);

```

### 命名 unix 套接字

在创建 unix 域套接字时，一定要绑定地址，地址是一个文件

服务器

1. 创建 socket 文件 【socket_create】
2. 绑定文件 【socket_bind】
3. 监听 socket 【socket_listen】
4. 阻塞接受 socket 【accept】
5. 接受内容 【socket_recv】

客户端连接

1. 创建 socket 文件 【socket_create】
2. 连接 socket 【socket_connect】
3. 发送内容 【socket_send】

服务端代码

```PHP
<?php
$file = "unix_domain";
$sockfd = socket_create(AF_UNIX, SOCK_STREAM, 0);
socket_bind($sockfd, $file);
socket_listen($sockfd,5); // 监听队列的长度

$connfd = socket_accept($sockfd);
if($connfd){
    while (1){
        $data = socket_read($connfd, 1024);
        if($data){
            fprintf(STDOUT, "recv from client data %s\n",$data);
            socket_write($connfd, $data,strlen($data));
        }
        if(strncasecmp(trim($data), "quit", 4) == 0){
            break;
        }
    }
}
socket_close($connfd);
socket_close($sockfd);

```

客户端连接代码

```PHP
<?php
$file = "unix_domain";
$sockfd = socket_create(AF_UNIX, SOCK_STREAM, 0);

if(socket_connect($sockfd, $file)){
    fprintf(STDOUT, "connect ok\n");
    $pid = pcntl_fork();
    if($pid == 0){
        while (1){
            $len = socket_recv($sockfd, $data, 1024, 0);
            if($len){
                fprintf(STDOUT, "recv from server %s\n",$data);
            }
            if(strncasecmp(trim($data), "quit", 4) == 0){
                break;
            }
        }
        exit(0);
    }
    while (1){
        $data = fread(STDIN, 128);
        if($data){
            socket_send($sockfd, $data, strlen($data), 0);
        }
        if(strncasecmp(trim($data), "quit", 4) == 0){
            break;
        }
    }
}
$pid = pcntl_wait($status);
fprintf(STDOUT, "quit pid %d\n",$status);
```

### udp unix 域套接字

udp 方式不需要 accept 一直登录连接，也不需要客户端使用 connect 进行连接

只需要创建 socket，并绑定到对应文件，然后就可以收发了

服务器代码

```PHP
<?php

$file = "unix_udp";
$sockfd = socket_create(AF_UNIX, SOCK_DGRAM, 0);
socket_bind($sockfd, $file);

while (1){
    $len = socket_recvfrom($sockfd, $data, 1024, 0, $unixFile);
    if($len){
        fprintf(STDOUT, "recv data %s,file %s\n",$data,$unixFile);
        socket_sendto($sockfd, $data, strlen($data), 0, $unixFile);
    }
    if(strncasecmp(trim($data), "quit", 4) == 0){
        break;
    }
}
unlink($file);
```

客户端代码

```PHP
<?php

$file = "unix_udp1";
$serverFile = "unix_udp";
$sockfd = socket_create(AF_UNIX, SOCK_DGRAM, 0);
socket_bind($sockfd, $file);
$pid = pcntl_fork();
if($pid == 0){
    while (1){
        $len = socket_recvfrom($sockfd, $data, 1024, 0, $serverFile);
        if($len){
            fprintf(STDOUT, "recv from server data %s ,file %s\n",$data,$serverFile);
        }
        if(strncasecmp(trim($data), "quit", 4) == 0){
            break;
        }
    }
    exit(0);
}
while (1){
    $data = fread(STDIN, 128);
    if($data){
        socket_sendto($sockfd, $data, strlen($data), 0,$serverFile);
    }
    if(strncasecmp(trim($data), "quit", 4) == 0){
        break;
    }
}
unlink($file);
```

```PHP
bin/gowatch run\
 --cmd "php"\
 --args "/www/hyperf-skeleton/bin/hyperf.php, start"\
 --folder "/www/hyperf-skeleton/app/, /www/hyperf-skeleton/config/"\
 --autoRestart=true
```
