---
cssclass:
title: MySQL角色及密码SSL加密
tags: [IT/MySQL]
image-auto-upload: true
date: 2022-10-03 20:46:46
lastmod: 2022-10-25 00:47:57
---
# MySQL角色及密码SSL加密
MySQL 8.0支持角色的授权。

## SSL

使用status查看是否开启，show variables like '%ssl%';

![](https://secure2.wostatic.cn/static/6H7jBJULj52LA2RUhz4hRT/image.png)

![](https://secure2.wostatic.cn/static/fsGMGfqw15fQZMysZQyt5A/image.png)

### 开启SSL【仅对tcp/ip方式生效，socket不需要使用ssl】

执行 mysql_ssl_rsa_setup程序

![](https://secure2.wostatic.cn/static/a18mGT8zMxFoGhe7c2yi78/image.png)

会在mysql数据目录生成 pem文件，设置权限为 mysql用户

```Bash
chown mysql:mysql *.pem
```

重启mysql服务生效

### 修改配置文件修改用户ssl连接

```Bash
[mysqld]
require_secure_transport = ON

```

设置用户使用ssl

```Bash
create user 'caoayu'@'%' identified by 'caoayu' require ssl;

alter user 'caoayu'@'%' require ssl;

flush privileges;
exit;
```

### 需要远程用户的受信任的CA的证书(双向认证)

```Bash
alter user 'caoayu'@'%' require X509;
flush privileges;
exit;
```

此时登录只能使用mysql ssl生成的客户端证书。

```Bash
mysql -u caoayu -p -h vm.com --ssl-ca=~/client-ssl/ca.pem --ssl-cert=~/client-ssl/client-cert.pem --ssl-key=~
```

### 不使用ssl启动

```Bash
mysql -h mysql1 -utest -ptest --ssl-mode=disabled
```

## 密码插件

查看

```Bash
show plugins;
```

安装

[https://dev.mysql.com/doc/refman/5.7/en/plugin-loading.html](https://dev.mysql.com/doc/refman/5.7/en/plugin-loading.html)

1.  配置文件修改后需要重启服务

```Bash
[mysqld] 
[plugin-load-add=validate_password.so](http://plugin-load-add=myplugin=somepluglib.so)
```

2 命令

```Bash
INSTALL PLUGIN validate_password SONAME 'validate_password';
```

![](https://secure2.wostatic.cn/static/4kAtktJGFD9NmvN7g83fRM/image.png)

3 密码策略要求

[https://dev.mysql.com/doc/refman/5.7/en/validate-password-options-variables.html](https://dev.mysql.com/doc/refman/5.7/en/validate-password-options-variables.html)

![](https://secure2.wostatic.cn/static/hrsDh1EREXVumdxRcqb5Xp/image.png)

去掉复杂的安全策略

```Bash
set global validate_password_policy=0;
set global validate_password_length=1;
alter user 'caoayu'@'%' identified by 'caoayu';
```

### 角色 [8.0]

创建角色

```Bash
create role dba;
grant all on *.* to dba with grant option;

```

用户与角色绑定

```Bash
grant dba to caoayu@'%';
```