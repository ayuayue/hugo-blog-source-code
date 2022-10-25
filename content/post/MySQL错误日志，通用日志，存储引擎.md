---
cssclass:
title: MySQL错误日志，通用日志，存储引擎
tags: [IT/MySQL]
image-auto-upload: true
date: 2022-10-03 20:37:46
lastmod: 2022-10-25 00:09:54
---
# MySQL错误日志，通用日志，存储引擎
存储引擎都是以插件方式嵌入的，除innodb外的引擎不在开发。

数据库与数据库实例之间的关系就想是 类和对象的关系。

oracle 多个实例可以用一个文件目录

mysql 一个实例对应一个文件目录

mysql database → schema 一对一对应。

oracle database → schema 一对多

配置文件

```Bash
!include # 包含一个文件

[client]


[mysql]


[mysqld-5.6]
# 5.6版本才会读取

[mysqldump]

[mysqldadmin]

[mysqld_multi]

[mysqld1]


```

错误日志

```Bash
[mysqld]
log_error = mysql.err
[mysqld_safe]
syslog # 错误日志配置到系统日志中
syslog_tag = stock 
```

表结构定义文件

每创建一张表，都会有一个 frm文件。表结构定义文件。

```Bash
>mysql desc user;

mysqlfrm --diagnostic user.frm # mysql-utilities
```