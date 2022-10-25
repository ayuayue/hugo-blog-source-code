---
cssclass:
title: PHP网络编程
tags: [IT/PHP, IT/PHP/多进程]
image-auto-upload: true

date: 2022-10-03 20:58:46
lastmod: 2022-10-25 08:30:51
---
# PHP网络编程
[[../../计算机基础【字节序，内存，补码】]]
[[../../监听socket，连接socket]]
[[../../粘包【封包-拆包】pack函数]]

### tcp/ip协议簇

#### 数据链路层：

接收到上层传来的数据，首先先拿到IP，再使用ARP程序发起一个请求，他会把IP包括自己的IP，自己的MAC地址进行封装后发送出去

网络上的主机接收到ARP请求时，判断IP是不是自己的，然后进行相应，把自己的IP，MAC发给对方。

然后把数据，MAC地址进行封装为数据帧 iframe 【 目的物理地址，源物理地址，数据。。。】

#### 网络层：

ICMP：ping，iptables

接收数据时，判断数据是不是发给自己的，如果是，会传递到传输层，最终传递到应用层某个具体进程【进程具有数据读写能力。如果不是发给自己，会进行转发，ip_forward【LVS】

发送数据时：他会进行封装为IP数据报，传递到数据链路层

#### 传输层

TCP/UDP

tcp：基于流stream的服务，特点：要进行连接，是双向的全双工，数据可靠，有序，超时重传

应用程序在收发数据时需要自己去判断数据的边界，会涉及到数据粘包和少报问题

udp：数据报服务，传输数据是固定的，但是不可靠，发送放发送一次，接收方必须及时接受，否则数据丢弃recvfrom

#### 应用层

HTTP，MQTT，WEBSOCKET

应用层在实现的时候是调用操作系统提供的系统调用函数，socket api。

抓包：tcpdump工具

```Bash
-bash_4.2#> tcpdump -i ens33 port 80
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on ens33, link-type EN10MB (Ethernet), capture size 262144 bytes
22:57:44.252852 IP 192.168.190.1.7804 > 192.168.190.128.http: Flags [S], seq 2853597369, win 64240, options [mss 1460,nop,wscale 8,sackOK,TS val 1341016658 ecr 0], length 0
22:57:44.254039 IP 192.168.190.128.http > 192.168.190.1.7804: Flags [S.], seq 2954183642, ack 2853597370, win 28960, options [mss 1460,sackOK,TS val 325271560 ecr 1341016658,nop,wscale 7], length 0
22:57:44.254290 IP 192.168.190.1.7804 > 192.168.190.128.http: Flags [.], ack 1, win 514, options [nop,nop,TS val 1341016660 ecr 325271560], length 0
22:57:44.254640 IP 192.168.190.1.7804 > 192.168.190.128.http: Flags [P.], seq 1:436, ack 1, win 514, options [nop,nop,TS val 1341016660 ecr 325271560], length 435: HTTP: GET / HTTP/1.1
22:57:44.254685 IP 192.168.190.128.http > 192.168.190.1.7804: Flags [.], ack 436, win 235, options [nop,nop,TS val 325271561 ecr 1341016660], length 0
22:57:44.258058 IP 192.168.190.128.http > 192.168.190.1.7804: Flags [P.], seq 1:970, ack 436, win 235, options [nop,nop,TS val 325271564 ecr 1341016660], length 969: HTTP: HTTP/1.1 200 OK
22:57:44.302716 IP 192.168.190.1.7804 > 192.168.190.128.http: Flags [.], ack 970, win 510, options [nop,nop,TS val 1341016706 ecr 325271564], length 0
```

TCP报文结构

【S】 SYN 同步报文【连接报文】

【seq】 ISN序号值

【ack】确认报文

【P】

【FIN】结束报文

三次握手

```PHP
22:57:44.252852 IP 192.168.190.1.7804 > 192.168.190.128.http: Flags [S], seq 2853597369, win 64240, options [mss 1460,nop,wscale 8,sackOK,TS val 1341016658 ecr 0], length 0

22:57:44.254039 IP 192.168.190.128.http > 192.168.190.1.7804: Flags [S.], seq 2954183642, ack 2853597370, win 28960, options [mss 1460,sackOK,TS val 325271560 ecr 1341016658,nop,wscale 7], length 0

22:57:44.254290 IP 192.168.190.1.7804 > 192.168.190.128.http: Flags [.], ack 1, win 514, options [nop,nop,TS val 1341016660 ecr 325271560], length 0

```

服务端调用LISTEN函数处于listen状态

客户端调用connect函数发送SYN同步报文

服务器使用ACK确认报文，双方就处于ESTABLEISH状态

