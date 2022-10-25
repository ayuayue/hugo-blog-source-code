---
cssclass:
title: MySQL安装配置及参数
tags: [IT/MySQL]
image-auto-upload: true
date: 2022-10-03 20:57:46
lastmod: 2022-10-25 08:50:49
---
# MySQL安装配置及参数
### 官方文档手册

[https://dev.mysql.com/doc/refman/5.7/en/](https://dev.mysql.com/doc/refman/5.7/en/)

[安装](https://www.wolai.com/8DorM3XKe5FGvhPEtjE7KL)

### 安装及升级

[https://dev.mysql.com/doc/refman/5.7/en/installing.html](https://dev.mysql.com/doc/refman/5.7/en/installing.html)

[https://dev.mysql.com/doc/refman/5.7/en/binary-installation.html](https://dev.mysql.com/doc/refman/5.7/en/binary-installation.html)

### 配置文件

修改 prompt 提示符、添加一个默认的客户端登录、配置错误日志目录、时间格式及数据存放目录

```Bash
[client]
user                    = caoayu
password                = 'caoayu'


[mysql]
prompt="(\u@\h:\p) [\d] \\r:\\m:\\s > "


[mysqld]
port                    = 3306
user                    = mysql
log-error               = /var/lib/mysql/mysql.error.log
datadir                 = /www/data/mysql_data
log-timestamps          = SYSTEM

```

### 配置文件的替换原则

配置文件目录

```Bash
mysqld --help --verbose | grep my.cnf


```

/etc/my.cnf

/etc/mysql/my.cnf

~/.my.cnf

默认是后面的配置文件中的项会替换前面的配置文件中的配置项

### 参数

从作用域上分为，global 和session

从类型上分为 可修改和只读参数

用户可在线修改非只读参数

只读参数只能通过配置文件修改并重启生效

所有参数的修改都不支持持久化

【mysqld】中是设置的mysql启动时的参数

```Bash
[mysqld]
log-error               = /var/lib/mysql/mysql.error.log
```

#### 查看

show [global|session] variables [like 'pattern' | where expr] ;

```Bash
show variables like 'log_error';
show variables like 'innodb%';
```

#### 修改

set [global | session] variables = xxx

默认不填是session级别

```Bash
set long_query_time=2;
```

### 全局和会话参数

设置或修改session的参数：

只对当前session生效。退出重新登录或者其他用户登录使用的还是全局参数

设置或修改global的参数：

对已经创建的连接是不生效的，已经创建的连接还是使用原来session或global的值。

对新的连接，则global会生效。

读取规则：

默认先读取session中的参数，如果修改了global中的参数，session中的参数不会变

### 查看其他连接|会话的参数

数据库performance_schema 中的 user_variables_by_thread、variables_by_thread 表中记录

```Bash
select * from variables_by_thread where variable_name = 'long_query_time';
```

![](https://secure2.wostatic.cn/static/w9qkTxC9R5CJTDfbntN1Kc/image.png)

获取连接ID

```Bash
show processlist;
```

![](https://secure2.wostatic.cn/static/MSkBa46As69BAXppK7Zr6/image.png)

通过THREAD_ID获取process_id,和thread_os_id操作系统对应的线程

```Bash
select * from threads where thread_id = '54'\G
```

![](https://secure2.wostatic.cn/static/5SPH9Bz5vQ8BVshuWepJrS/image.png)

关联查出参数对应的会话id及线程id

```Bash
select * from variables_by_thread vbt
inner join  threads t on vbt.thread_id = t.thread_id
where vbt.variable_name = 'long_query_time' and t.thread_id = '54';

```

