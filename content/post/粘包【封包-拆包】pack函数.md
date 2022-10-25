---
cssclass:
title: 粘包【封包-拆包】pack函数
tags: [IT/PHP, IT/PHP/多进程]
image-auto-upload: true
date: 2022-10-03 20:36:46
lastmod: 2022-10-25 08:46:45
---
# 粘包【封包-拆包】pack函数

![](https://secure2.wostatic.cn/static/76VuRLf8ibdduhL2xU1yu/image.png)
![](https://secure2.wostatic.cn/static/76VuRLf8ibdduhL2xU1yu/image.png)
多个数据包粘在一起，无法确定数据的边界

tcp在调用 send/fwrite/stream_socket_sendto

在接收时：

1.  第一个数据包未接收完全，剩下部分与其他数据包粘在一起了
2.  第一个数据包粘住了剩下的一部分，多余了一部分
3.  一次读取到了多个数据包
4.  要多次才能读取到完整的数据包
5.  正常接受每个包的数据

### Nagle算法（默认开启）

先不发送，先把小块数据缓存起来组成大块数据，在一定的时机下发送，不会造成网络拥塞，数据丢失，但是会延迟。

关闭时：

早期带宽很小，每次发送数据都要经过每一层【传输层，网络层，数据链路层】的封装，很容易造成网络拥塞，甚至造成数据包丢失“丢包”

现在网络带宽越来越好了，可以关闭这个算法，从而让消息急时的可以发送出去。

如果使用了Nagle算法，发送端就会把小块数据组合成大块数据所谓的“封包”，进行发送，接收端是不知道数据的边界【字节流是没有数据边界的，你得自己确定数据的边界，这个过程叫做“拆包”】

### pack函数

[https://www.php.net/manual/zh/function.pack.php](https://www.php.net/manual/zh/function.pack.php)

pakc(format,$args)

将args通过format方式打包成二进制

### 字节序

只有超过一个字节才存在字节序【顺序】

大端字节序：网络字节序，在传输时，值的高位放在字节流的开始出，低位放在字节流的末尾上

小端字节序：主机字节序，在传输时，值的低位放在字节流的开始出，高位放在字节流的末尾上

主机字节序【一般在本地存储】在网络传输时会转成网络字节序

![](https://secure2.wostatic.cn/static/cApCTcQf13ByhFrTnVV2v1/image.png)