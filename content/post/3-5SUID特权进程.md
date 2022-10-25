---
cssclass:
title: 3-5SUID特权进程
tags: [IT/PHP, IT/PHP/多进程]
image-auto-upload: true
date: 2022-10-03 20:44:46
lastmod: 2022-10-25 00:57:49
---
# 3-5SUID特权进程
## SUID特权进程
**SUID SGID**

SUID : set user id 设置用户id

SGID : set group id 设置组id

当特殊标志 's' 出现在 文件拥有者x权限位的时候就叫 set uid,简称SUID,也叫SUID特殊权限

当特殊标志 's' 出现在 组x权限位的时候就叫 set uid,简称SUID,也叫SUID特殊权限

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202201061609143.png)

###### 用途,功能

一般来说,以root用户启动的程序都是超级进程,一般来说都是一些重要的服务程序

有时候我们经常是以普通用户来执行程序,如www用户

但有时候普通进程需要访问一些特殊的资源,这个时候我们就需要提升权限来访问

如passwd程序,普通用户也可以使用该程序修改密码,但是普通用户无法修改 /etc/shadow文件,可以通过程序passwd来修改此文件

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202201061610303.png)

###### 如何设置SUID

就在**可执行文件**的权限x位上设置 chmod u/g/o +s

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202201061610021.png)

**注意,是提升脚本解释器的权限,以至于可以在脚本文件中操作系统中当前用户无权的资源**

```PHP
<?php
$file = 'pwd.txt';
$uid = posix_getuid();
$euid = posix_geteuid();
fprintf(STDOUT,"uid=%d,euid=%d\n",$uid,$euid);
// 这样设置是不行的,要先设置可执行文件的权限 +s
posix_setuid($euid);
posix_seteuid($euid);
$uid = posix_getuid();
$euid = posix_geteuid();
fprintf(STDOUT,"uid=%d,euid=%d\n",$uid,$euid);
if(posix_access($file,POSIX_W_OK)){
    fprintf(STDOUT,"我能修改\n");
    $fd = fopen($file,'a');
    fwrite($fd,'test');
    fclose($fd);
}else{
    fprintf(STDOUT,"我不能修改\n");
}



```

root用户的文件,root可以读写

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202201061611689.png)

caoayu用户,无法直接修改root创建的文件,可以通过suid特权程序来操作文件

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202201061611920.png)

###### 提权访问资源后,一定要改回来,否则会有系统风险