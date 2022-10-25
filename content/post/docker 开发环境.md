---
cssclass:
title: docker 开发环境
tags: [IT/Docker, IT/Linux]
image-auto-upload: true
date: 2022-10-03 20:44:46
lastmod: 2022-10-25 08:44:47
---
# docker 开发环境
### 命令
相关笔记

[[php56-fpm]]
[[apache-php]]

---

```Bash
# 删除已退出的容器
docker rm `docker ps -q -f status=exited`
# 空间占用
docker system df
# 批量删除镜象
docker rmi $(docker images | grep image-b | grep -v "v1d0-7" | awk  '{print $3}')
docker rmi $(docker images -f "dangling=true" -q)
# 查看容器ip
docker inspect nginx | grep "IPAddress"
# 查看所有容器ip
docker inspect --format='{{.Name}} -- {{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -aq)

# 开启/关闭自动启动
docker update --restart always|none chemweb2

```

### 安装 oracle11g

## 安装oracle 11g

```Bash
docker pull jaspeen/oracle-11g
```

#### 下载oracle 11g安装文件

11g 文档地址 [https://docs.oracle.com/cd/E11882_01/nav/portal_11.htm](https://docs.oracle.com/cd/E11882_01/nav/portal_11.htm)

下载地址 [https://www.oracle.com/cn/database/enterprise-edition/downloads/oracle-db11g-linux.html#license-lightbox](https://www.oracle.com/cn/database/enterprise-edition/downloads/oracle-db11g-linux.html#license-lightbox)

从oracle 官网 下载所需要的安装包，这里我们以oracle 11g 为例子，分别下载 linux.x64_11gR2_database_1of2.zip 和 linux.x64_11gR2_database_2of2.zip两个压缩包，下载完成后解压到home目录(如下目录结构)

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

```Bash

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

```

```Bash
impdp system/oracle directory=DATA_PUMP_DIR dumpfile=FJNU0918.DMP REMAP_SCHEMA=fjnu:fjnu
```

### PHP 扩展 pdo_oci,oci8

将 linux 的 base ,sdk 解压并配置软链接,

```Bash
  mv instantclient_11_2/ /usr/local/
  ln -s /usr/local/instantclient_11_2/ /usr/local/instantclient \
  ln -s /usr/local/instantclient_11_2/libclntsh.so.11.1 /usr/local/instantclient/libclntsh.so \
  ln -s /usr/local/instantclient_11_2/libocci.so.11.1 /usr/local/instantclient/libocci.so

ln /usr/local//instantclient_11_2/libclntsh.so.11.1 /usr/lib/libclntsh.so && \
ln /usr/local/instantclient_11_2/libocci.so.11.1 /usr/lib/libocci.so && \
ln /usr/local/instantclient_11_2/libociei.so /usr/lib/libociei.so && \
ln /usr/local/instantclient_11_2/libnnz11.so /usr/lib/libnnz11.so

  vi /etc/profile
    export LD_LIBRARY_PATH="/usr/local/instantclient_11_2"
    export ORACLE_BASE="/usr/local/instantclient_11_2"
    export ORACLE_HOME="/usr/local/instantclient_11_2"
    export TNS_ADMIN="/usr/local/instantclient_11_2"

 
 source /etc/profile 
apk add libnsl libnsl-dev libaio libaio-dev file
wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub
wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.28-r0/glibc-2.28-r0.apk
apk add glibc-2.28-r0.apk

docker-php-source extract
docker-php-ext-configure oci8 --with-oci8=share,instantclient,/usr/local/instantclient && docker-php-ext-install -j$(nproc) oci8
docker-php-ext-configure pdo_oci --with-pdo-oci=instantclient,/usr/local/instantclient,11.2 && docker-php-ext-install -j$(nproc) pdo_oci 
```

### 使用 alpine 镜像配置一个 php5.6 的镜象,包含源码包

```Bash
# 拉
docker pull alpine:3.13 \
# run
docker run --name alpine -it alpine:3.13 \
# 改源
cp /etc/apk/repositories /etc/apk/repositories.bak \
sed -i "s/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g" /etc/apk/repositories \
# 装软件
apk add autoconf make gcc g++ libxml2-dev pkgconf tzdata
apk add libaio libnsl libc6-compat
vi /etc/apk/keys/sgerrand.rsa.pub
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEApZ2u1KJKUu/fW4A25y9m
y70AGEa/J3Wi5ibNVGNn1gT1r0VfgeWd0pUybS4UmcHdiNzxJPgoWQhV2SSW1JYu
tOqKZF5QSN6X937PTUpNBjUvLtTQ1ve1fp39uf/lEXPpFpOPL88LKnDBgbh7wkCp
m2KzLVGChf83MS0ShL6G9EQIAUxLm99VpgRjwqTQ/KfzGtpke1wqws4au0Ab4qPY
KXvMLSPLUp7cfulWvhmZSegr5AdhNw5KNizPqCJT8ZrGvgHypXyiFvvAH5YRtSsc
Zvo9GI2e2MaZyo9/lvb+LbLEJZKEQckqRj4P26gmASrZEPStwc+yqy1ShHLA0j6m
1QIDAQAB
-----END PUBLIC KEY-----




wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/2.30-r0/glibc-2.30-r0.apk && \
apk add  glibc-2.30-r0.apk && \
rm -rf glibc-2.30-r0.apk
# 改时区
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
echo "Asia/Shanghai" > /etc/timezone
# 下载PHP源码并编译安装
mkdir /usr/src/php -p \
cd /usr/src/php \
wget https://www.php.net/distributions/php-5.6.40.tar.gz \
tar -xzvf php-5.6.40.tar.gz \
cd php-5.6.40/ \

./configure --prefix=/usr/local/php56 --with-config-file-path=/usr/local/php56/etc \
--enable-fpm \
make distclean \ 
make && make install \
cp php.ini-development /usr/local/php56/etc/php.ini \
# 配置软链接,环境变量
ln -s /usr/local/php56 /usr/local/php \
echo "export PHP=/usr/local/php \n export PATH=$PATH:$PHP/bin" >> /etc/profile \
# 刷新配置
source /etc/profile \
# 输出信息
php -v && php -m && php --ini && php -i | grep extension \



```

### docker生成自己的镜象

```Bash
docker commit -a "caoayu" alpine php56-alpine



```

[apache-php](https://www.wolai.com/pqnojXFq6yJdbQeN3n8p3F)

[[php56-fpm](php56-fpm.md)](https://www.wolai.com/sQC7KCsBCBzTRcoReV2vWP)

docker update --restart always chemweb2