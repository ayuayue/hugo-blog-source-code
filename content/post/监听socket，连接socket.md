---
cssclass:
title: 监听socket，连接socket
tags: [IT/PHP, IT/PHP/多进程]
image-auto-upload: true
date: 2022-10-03 20:32:46
lastmod: 2022-10-25 00:01:57
---
# 监听socket，连接socket

监听 socket 是指已经 【socket，bind，listen】 listen 会创建一个监听队列

连接 socket 是 accept 函数从监听队列中获取一个客户端连接，这个连接称为连接 socket

 [https://man7.org/linux/man-pages/man2/select.2.html](https://man7.org/linux/man-pages/man2/select.2.html)

 [https://man7.org/linux/man-pages/man3/select.3p.html](https://man7.org/linux/man-pages/man3/select.3p.html)

## I/O 复用 select 函数

PHP 函数 stream_select（）

内部调用 select（nfs,readfds,writefds,exceptfds,timeout)系统函数

select 参数解析

1. nfs → 被监听的文件描述符的总数 socket 文件
2. readfds → 读事件对应的文件描述符集合
3. writefds → 写事件对应的文件描述符集合
4. exceptfds → 异常事件对应的文件描述符集合
5. timeout → 超时时间，设置为 0 即立马返回，NULL 为阻塞到有事件发生

nfs 监听到对应的事件后，会修改对应事件的文件描述符并生成新的文件描述符返回，文件描述符 id+1，比如【3,4】两个客户端连接上来，【4】客户端发送消息，select 会监听到这个修改，修改【4】的文件描述符并命名为【5】，并返回【5】文件描述符

## 文件描述符产生就绪事件

可读事件

1. socket 内核接受缓冲区的字节数大于或等于 SO_RCVLOWAT 水位标记时，执行读操作时返回的字节数大于 0
2. 对端关闭时，此时的读操作返回 0
3. 监听 socket 上有新客户端连接时
4. socket 上有未处理错误，可使用 getsocketopt 来读取和清除错误

可写事件

1. socket 内核发送缓冲区的可用字节数大于等于 SO_SNDLOWAT 水位标记时，执行写操作，返回的字节数大于 0
2. 对端关闭时，写操作会触发 SIGPIPE 中断信号
3. socket 有未处理错误

异常事件

就是发送紧急数据【带外数据】时

当计算机网卡接受到数据时，会把数据写入到内存中，并向 CPU 发起硬件中断请求，CPU 会响应去执行中断服务程序，并把数据【会根据端口号找到 socket 文件描述符】写入到对应的 socket 内核接受缓冲区中，同时唤醒当前的进程【select 有返回值】

![](https://secure2.wostatic.cn/static/p22XtwTL2oNxMogbQyPjCq/image.png)

![](https://secure2.wostatic.cn/static/p2ft8TQy3UPe9Hy5naQfyC/image.png)
