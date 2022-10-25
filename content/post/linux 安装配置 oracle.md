---
cssclass:
title: linux 安装配置 oracle
tags: [IT/Oracle]
image-auto-upload: true
date created: 2022-09-07 00:04:05
date modified: 2022-10-02 23:38:53
date: 2022-10-03 20:32:46
lastmod: 2022-10-25 08:43:49
---
# linux 安装配置 oracle
11g 文档地址 [https://docs.oracle.com/cd/E11882_01/nav/portal_11.htm](https://docs.oracle.com/cd/E11882_01/nav/portal_11.htm)

下载地址 [https://www.oracle.com/cn/database/enterprise-edition/downloads/oracle-db11g-linux.html#license-lightbox](https://www.oracle.com/cn/database/enterprise-edition/downloads/oracle-db11g-linux.html#license-lightbox)

### 修改|新增服务名

先使用 dba 账号登录连接 oracle

```Bash
show parameter name;
alter system register;
alter system set service_names=orcl,'chemdb.thinks.net.cn';
```

### 重启

```Bash
首先以 oracle 用户登录
停止 Oracle 监听服务

$ lsnrctl stop

停止 Oracle 服务

$ sqlplus /nolog

SQL > conn / as sysdba

SQL > shutdown immediate

SQL > exit

启动服务

 启动 Oracle 监听服务

$ lsnrctl start

启动 Oracle 服务

$ sqlplus /nolog

SQL > conn / as sysdba

SQL > startup

验证：

SQL > select * from dual;

SQL > exit

```

## 导入导出

1.  先创建逻辑目录

```
create directory dpdata1  as '/data/oracle/oradata/mydata';
create directory dpdump  as '/opt/oracle/dpdump';

```

2.  查看目录是否创建成功

```
select * from dba_directories;
```

### 导出数据库

```Bash
expdp fjnu/thinks5567 schemas=fjnu dumpfile=fjnu0929.dmp directory=dpdata1;
expdp jou/think5567 schemas=jou dumpfile=jou0929.dmp directory=dpdata1;
expdp haut/thinks5567 schemas=haut dumpfile=haut0929.dmp directory=dpdata1;

```

### 导入

```Bash
impdp system/oracle table_exists_action=replace \
schemas=fjnu dumpfile=fjnu0929.dmp \
directory=data_dump_dir REMAP_SCHEMA=fjnu:fjnu

impdp system/oracle table_exists_action=replace \
schemas=haut dumpfile=haut0929.dmp \
directory=data_dump_dir REMAP_SCHEMA=haut:haut

impdp system/oracle table_exists_action=replace \
schemas=jou dumpfile=jou0929.dmp \
directory=data_dump_dir REMAP_SCHEMA=jou:jou

impdp system/oracle table_exists_action=replace schemas=scut dumpfile=SCUT0709.dmp directory=data_dump_dir REMAP_SCHEMA=scut:scut

```

导入表

```
impdp caoayu/123456 directory=dpdata1 dumpfile=0508hiananu.dmp tables=hainanu.c2_chem_dict_chem remap_schema=hainanu:caoayu

导入用户的表 impdp ZJUT/thinks5567 directory=dpdata1 dumpfile=zjut202106.dmp REMAP_SCHEMA=ZJUT:ZJUT EXCLUDE=USER

使用系统帐号导入用户表并生成跟导入用户帐号密码一致的新用户（如果用户不存在） impdp system/Abc123456 directory=dpdata1 dumpfile=zjut202106.dmp REMAP_SCHEMA=zjuttest:zjuttest
```

使用 expdp 导出表

1.  `select * from dba_directories` 查目录
2.  使用 system 创建一个目录

```
create directory dpdata1  as '/data/oracle/oradata/mydata'
expdp scott/tiger@orcl schemas=scott dumpfile=expdp.dmp DIRECTORY=dpdata1;
expdp scott/tiger@orcl TABLES=emp,dept dumpfile=expdp.dmp DIRECTORY=dpdata1;
```

### 删除用户及关联的表

```Bash
drop user user_name cascade; 
```

### 修改时区

```bash
 ALTER DATABASE SET TIME_ZONE='+08:00';
```