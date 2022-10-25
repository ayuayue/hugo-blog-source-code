---
cssclass:
title: MySQL连接及授权
tags: [IT/MySQL]
image-auto-upload: true
date: 2022-10-03 20:03:47
lastmod: 2022-10-25 08:02:50
---
# MySQL连接及授权

官方说明

[https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)

### 用户权限管理

用户 → IP

库 → 表 → 列

![](https://secure2.wostatic.cn/static/k8aUEsymphYVTpQmCxjxvk/image.png)

![](https://secure2.wostatic.cn/static/9Sx37x9TLuhQeKebG5zBnT/image.png)

### 连接

u 用户

h 主机地址

P 端口

p 密码

#### 本地socket连接

```Bash
show variables like '%socket%';

mysql -S /var/run/mysqld/mysqld.sock -uroot -p
```

#### tcp/ip远程连接

```Bash
mysql -u root -h localhost -P 3306 -p
```

#### 查看连接状态

```Bash
mysql > status;
mysql > \s;
```

### 创建用户

```Bash
create user 'caoayu'@'%' identified by 'caoayu';
create user 'caoayu'@'192.168.3.%' identified by 'caoayu';

```

### 删除用户

```Bash
drop user 'caoayu'@'%';
drop user 'caoayu'@'192.168.3.%';

```

### 修改用户

```Bash
alter user 'caoayu'@'%' identified by 'caoayu';
```

### 查看用户权限

```Bash
show grants;

show grants for caoayu;
```

### 授权 【库.表】

这样授权后不需要刷新 [with grant option];

```Bash
grant select,update,delete,insert,index on *.* to 'caoayu'@'%';

grant select,update,delete,insert,index on test.* to 'caoayu'@'%';

grant all on *.* to 'caoayu'@'%';

```

### 撤销权限

```Bash
revoke  select,update,delete,insert,index on *.* from 'caoayu'@'%';
```

### 授予被授权用户可以授权其他用户的权限

```Bash
grant select,update,delete,insert,index on *.* to 'caoayu'@'%' with grant option;
```

### 查看用户信息【元数据表】

mysql数据库中

db

user

tables_priv

column_priv

四张表记录

![](https://secure2.wostatic.cn/static/fuLgPNYpouNAkMFe86RBSz/image.png)

注意：不要通过修改这几个表来创建用户或授权。

### 用户资源管理

resource_option

MAX_USER_CONNECTIONS

MAX_CONNECTS_PER_HOUR

MAX_UPDATES_PER_HOUR

MAX_QUERIES_PER_HOUR

默认不对上述资源控制

修改用户资源，对之前的不影响，新连接才会影响

```Bash
alter user 'caoayu'@'%' with MAX_USER_CONNECTIONS  2;
```