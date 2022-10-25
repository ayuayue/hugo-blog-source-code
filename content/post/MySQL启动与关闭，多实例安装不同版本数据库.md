---
cssclass:
title: 启动与关闭，多实例安装不同版本数据库
tags: [IT/MySQL]
image-auto-upload: true
date: 2022-10-03 20:30:46
lastmod: 2022-10-25 08:05:50
---
# 启动与关闭，多实例安装不同版本数据库
启动

```Bash
mysqld # 默认配置启动
mysqld --defaults-file=/etc/my.cnf # 自定义配置文件启动
mysqld --defaults-file=/etc/my.cnf & #后台
mysqld_safe # shell脚本,守护进程，会监听mysql进程，如果挂掉会自动拉起
```

关闭

```Bash
>mysql shutdown 

mysqladmin -uroot -p -S /tmp/mysql.sock shutdown
kill -s SIGKILL 
/etc/init.d/mysql.server stop # 还是使用了 kill 信号机制
```

忘记密码

```Bash
# my.cnf

[mysqld]
skip_grant_tables


启动mysql，并连接
use mysql;

update db set Create_priv='Y' where user='caoayu';

select user,host,authentication_string from user;

update user set authentication_string = password('caoayu') where user = 'caoayu' and host = '%';
flush privileges;

重启mysql
去除配置
[mysqld]
#skip_grant_tables

```

多实例同版本

```Bash
[mysqld_multi]
mysqld     = /usr/bin/mysqld_safe
mysqladmin = /usr/bin/mysqladmin
user       = multi_admin
password   = my_password

[mysqld2]
socket     = /tmp/mysql.sock2
port       = 3307
datadir    = /var/lib/mysql2
user       = unix_user1
```

多实例多版本

```Bash
[mysqld-5.7]
port                    = 3306
user                    = mysql
log-error               = /usr/local/mysql57/data/mysql.error.log
datadir                 = /usr/local/mysql57/data
pid-file                = /usr/local/mysql57/data/mysql.pid

skip_grant_tables

[mysqld-8.0]
port                    = 3308
user                    = mysql
log-error               = /tmp/mysql80/mysql.error.log
datadir                 = /tmp/data80
pid-file                = /tmp/data80/mysql.pid

#skip_grant_tables


[client]
user                    = caoayu
password                = 'caoayu'


[mysql]
prompt="(\u@\h:\p) [\d] \\r:\\m:\\s > "


[mysqld_multi]
mysqld     = /usr/local/mysql57/bin/mysqld_safe
mysqladmin = /usr/local/mysql57/bin/mysqladmin
user       = caoayu
password   = caoayu

log = /var/lib/mysql/mysql_multi.log

[mysqld2]
socket     = /tmp/data/mysql.sock
port       = 3307
datadir    = /tmp/data
log-error = /tmp/data/mysql.error.log
pid-file   = /tmp/data/mysql.pid
user       = mysql
basedir    = /usr/local/mysql57




[mysqld8]
socket     = /tmp/data80/mysql.sock
port       = 3308
basedir    = /usr/local/mysql80/
datadir    = /tmp/data80
log-error  = /tmp/data80/mysql.error.log
user       = mysql
pid-file   = /tmp/data80/mysql.pid
plugin-dir = /usr/local/mysql80/lib/plugin
```

设置密码过期

```Bash
>mysql alter user 'caoayu'@'%' password expire;
```