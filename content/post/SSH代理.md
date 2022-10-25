---
cssclass:
title: SSH代理
tags: [IT/Command, IT/SSH-Proxy]
image-auto-upload: true

date: 2022-10-03 20:04:47
lastmod: 2022-10-25 08:09:54
---
# SSH代理
## 命令解释

ssh -L 3305:172.17.0.2:1521 [root@202.196.16.23](mailto:root@202.196.16.23)

命令：

ssh -NCPf [root@111.111.111.111](mailto:root@111.111.111.111) -L 3388:192.168.1.1:3306

参数：

-C    使用压缩功能，是可选的，加快速度。

-P    用一个非特权端口进行出去的连接。

-f    一旦SSH完成认证并建立port forwarding，则转入后台运行。

-N    不执行远程命令。该参数在只打开转发端口时很有用（V2版本SSH支持）

这里的root@111.111.111.111是连接远程服务器的用户名和IP

-L 3388:192.168.1.1:3306  这里表示建立的隧道，3388是本机端口，通过3388和远程服务器建立隧道，192.168.1.1是内网ip，可以用127.0.0.1，当然也可以用外网ip，这里要注意数据库要给这个ip赋权，因为隧道连接实际上就是数据库连接的这个ip ，后面的是远程数据库的端口

这个命令在本地运行

eg ： 代理 oracle

本地端口没被占用

映射后本地使用哥127.0.01 访问

ssh -L 3307:172.17.0.4:1521 [root@202.196.16.23](mailto:root@202.196.16.23)

3307 本地端口

172.17.0.4 docker 内 oracle 服务内部ip  端口1521

（端口未开放，开放可以直接使用服务器ip）

root@...  服务器ssh连接

本地访问

sqlplus user_haut/thinks5567@127.0.0.1:3307/chemdb.thinks.net.cn

user_haut 远程数据库用户

thinks5567 密码

127.0.0.1 本地ip

3307 本地端口

[chemdb.thinks.net.cn](http://chemdb.thinks.net.cn) 远程服务名

---
## 命令示例


1.  将远程服务器端口转发到本地
    -   -C 压缩传输
    -   -f 将SSH传输转入后台执行
    -   -N 建立静默连接
    -   -g 允许远程主机连接本地用于转发的端口
    -   -L 本地端口转发
    -   1234 本地端口
    -   82.157.131.35:22 目标主机：目标端口

```
ssh -L <local port>:<remote host>:<remote port> <SSH hostname>

ssh -CfNg -L 22:82.157.131.35:22 root@82.157.131.35
# 
ssh -C -L 1234:82.157.131.35:22 root@82.157.131.35

```

2.  将本地端口代理到远程

![](https://secure2.wostatic.cn/static/3fnXgsvKu1L58NBWGcjAAs/image.png)

```
ssh -R <local port>:<remote host>:<remote port> <SSH hostname>

ssh -R 22:82.157.131.35:8080 root@82.157.131.35

```


