---
cssclass:
title: MySQL多实例安装
tags: [IT/MySQL]
image-auto-upload: true
date: 2022-10-03 20:04:47
lastmod: 2022-10-25 08:56:49
---
# MySQL多实例安装
一台服务器安装多个MySQL实例，充分利用硬件资源

通过 mysqld_multi程序即可

修改配置文件

```Bash
mysqld_multi --example # 生成格式

[mysqld_multi]
mysqld = /usr/bin/mysqld_safe
mysqladmin = /usr/bin/mysqladmin
log = /var/lib/mysql/mysql_multi.log

[mysqld1]
port = 3307
datadir = /data
socket = /data/mysql.sock1
log_error = /data/mysql.error.log
```

初始化数据库

```Bash
mysqld --initialize-insecure --user=mysql --basedir=/opt/mysql-5.7.30 --datadir=/data
```

查看

```Bash
mysqld_multi report
```

启动

```Bash
mysqld_multi start 1
```