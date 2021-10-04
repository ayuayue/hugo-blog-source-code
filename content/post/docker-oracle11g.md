---
title: "Docker 安装 Oracle11g"
date: 2021-10-04T18:30:56+08:00
lastmod: 2021-10-04T18:30:56+08:00
draft: false
keywords: []
description: ""
tags: []
categories: []
---

### 常用命令

```Bash
# 删除已退出的容器
docker rm `docker ps -q -f status=exited`
# 空间占用
docker system df
# 批量删除镜象
docker rmi $(docker images | grep image-b | grep -v "v1d0-7" | awk  '{print $3}')
# 查看容器ip
docker inspect nginx | grep "IPAddress"
# 查看所有容器ip
docker inspect --format='{{.Name}} -- {{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -aq)
```

## 安装oracle 11g

```Bash
docker pull jaspeen/oracle-11g
```

#### 下载oracle 11g安装文件

11g 文档地址 https://docs.oracle.com/cd/E11882_01/nav/portal_11.htm

下载地址  https://www.oracle.com/cn/database/enterprise-edition/downloads/oracle-db11g-linux.html#license-lightbox

从oracle 官网 下载所需要的安装包，这里我们以oracle 11g 为例子，分别下载 linux.x64_11gR2_database_1of2.zip 和 linux.x64_11gR2_database_2of2.zip两个压缩包，下载完成后解压到home目录(如下目录结构)

```Bash
    home
    └─database
        ├─doc
        ├─install
        ├─response
        ├─rpm
        ├─sshsetup
        ├─stage
        ├─runInstaller
        └─welcome.html
        
```

```
mkdir /home/data/oracle/dbdump -p
docker run -d --privileged --name oracle11g -p 1521:1521 -v /home:/install -v /home/data/oracle/dbdump:/opt/oracle/dpdump jaspeen/oracle-11g
```

#### 配置

```Bash
docker exec -it oracle11g /bin/bash

su - oracle

mkdir /opt/oracle/dpdump

sqlplus / as sysdba
alter user scott account unlock;
commit;
conn scott/tiger
```

#### 创建用户并授予所有权限

```sql
create user caoayu  identified by "caoayu";
  
alter user "CAOAYU" account unlock;

grant all privileges to CAOAYU;
```

### 更改导入导出目录

```bash
create or replace directory data_dump_dir as '/opt/oracle/dpdump'
```

### 导入导出命令

```Bash
docker exec -it oracle11g impdp ..
docker exec -it oracle11g expdp ..

impdp system/oracle directory=DATA_PUMP_DIR dumpfile=test.DMP REMAP_SCHEMA=test:test
```

