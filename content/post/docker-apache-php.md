---
title: "Docker 配置 apache+php环境"
date: 2021-10-04T18:36:51+08:00
lastmod: 2021-10-04T18:36:51+08:00
draft: false
keywords: []
description: ""
tags: []
categories: []
---

### 基于centos

```
docker pull centos:7
```

### 基础软件及apache

```Bash
yum install wget -y
# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum makecache

yum install epel-release  

yum install expat expat-devel sqlite-devel libpng libpng-dev \
libxml2 libxml2-devel libzip libzip-devel apr apr-devel pcre pcre-devel \
apr-util apr apr-devel apr-util-devel libmcrypt libmcrypt-devel \
telnet freetype freetyp-devel libzip libzip-devel t1lib t1lib-devel \
psmisc tree oniguruma oniguruma-devel libjpeg-turbo libjpeg-turbo-devel \
gcc g++ make automake vim file bison bison-devel

yum grouplist | more
#yum  groupinstall Development tools
rpm ql apr-util 

cd /usr/src/httpd-2.4.48
./configure --prefix=/usr/local/apache2 \
 --with-apr=/usr/bin \
 --with-apr-util=/usr/bin \
 --with-pcre=/usr/bin/pcre-config \
 --enable-so
 
vim .bashrc
 
export APACHE="/usr/local/apache"
export PHP="/usr/local/php"
export PATH="${HOME}/bin:${HOME}/.g/go/bin:$PATH:$APACHE/bin:$PHP/bin"

source .bashrc
ln -s /usr/local/apache2 /usr/local/apache
cp /usr/local/apache2/conf/httpd.conf /usr/local/apache2/conf/httpd.conf.bak
cp /usr/local/apache2/conf/extra/httpd-vhosts.conf httpd-vhosts.conf.bak
```

### php

```Bash
cd php-5.6.40
./configure --prefix=/usr/local/php56 \
--with-config-file-path=/usr/local/php56/etc \
--with-apxs2=/usr/local/apache2/bin/apxs \
--with-mcrypt \
--enable-calendar \
--enable-ftp \
--enable-mbstring \
--enable-opcache \
--enable-pcntl \
--with-pdo-mysql \
--enable-shmop \
--enable-soap \
--enable-sockets \
--enable-sysvmsg \
--enable-sysvsem \
--enable-sysvshm \
--enable-zip \
--without-pear

ln -s /usr/local/php56 /usr/local/php
```

### gd库

```Bash
./configure --with-gd --with-freetype-dir=/usr/lib64 \
 --with-png-dir=/usr/lib64 --with-jpeg-dir=/usr/lib64 \
 --with-zlib-dir --with-t1lib=/usr/lib64
```

### pdo_oci

https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html

下载如下两个文件（下载文件需注册）：并解压到一个文件夹中 如 instantclient_11_2

```
oracle-instantclient11.2-basic
oracle-instantclient11.2-sdk
export LD_LIBRARY_PATH=/usr/local/instantclient_11_2
export NLS_LANG="AMERICAN_AMERICA.AL32UTF8"  
source ~/.bashrc

yum install libaio libaio-devel -y

ln -s /usr/local/instantclient_11_2/libclntsh.so.11.1 \
 /usr/local/instantclient_11_2/libclntsh.so
ln -s /usr/local/instantclient_11_2/libocci.so.11.1 \
/usr/local/instantclient_11_2/libocci.so
```

先安装 oci8 , 在 php7版本的php-oci 安装时会提示安装 oci8

```Bash
cd oci8
/usr/local/php56/bin/phpize

./configure --with-php-config=/usr/local/php56/bin/php-config \
--with-oci8=instantclient,/usr/local/instantclient_11_2

make && make install
cp modules/oci8.so /usr/local/php56/lib/php/extensions/no-debug-zts-20131226/
cd pdo_oci
/usr/local/php56/bin/phpize

./configure --with-php-config=/usr/local/php56/bin/php-config \
 --with-pdo-oci=instantclient,/usr/local/instantclient_11_2,11.2

cp modules/pdo_oci.so /usr/local/php56/lib/php/extensions/no-debug-zts-20131226/
```

### pecl & pear

```Bash
cd /usr/local/src/
wget http://pear.php.net/go-pear.phar
php go-pear.phar
```

### 时区问题

PHP Warning:  Unknown: It is not safe to rely on the system's timezone settings. You are *required* to use the date.timezone setting or the date_default_timezone_set() function. In case you used any of those methods and you are still getting this warning, you most likely misspelled the timezone identifier. We selected the timezone 'UTC' for now, but please set date.timezone to select your timezone. in Unknown on line 0

```Bash
vi /usr/local/php56/etc/php.ini
date.timezone = Asia/Shanghai
```



### dockerhub

可以直接使用

```
docker pull caoayu/apache-php:v
```

