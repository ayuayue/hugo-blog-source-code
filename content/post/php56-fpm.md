---
cssclass:
title: php56-fpm
tags: [IT/PHP, IT/Docker]
image-auto-upload: true
date: 2022-10-03 20:50:46
lastmod: 2022-10-25 08:48:50
---
# php56-fpm
基于 alpine

```Bash
docker pull alpine:3.13 \
# run
docker run --name alpine -it alpine:3.13 \
# 改源
cp /etc/apk/repositories /etc/apk/repositories.bak \
sed -i "s/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g" /etc/apk/repositories 

apk add  autoconf make gcc g++ libxml2-dev pkgconf tzdata libaio libnsl libnsl-dev \
libc6-compat libmcrypt libxpm-dev libjpeg-turbo-dev libpng libvpx-dev \
freetype-dev net-tools

# 改时区
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
echo "Asia/Shanghai" > /etc/timezone



./configure --prefix=/usr/local/php56 \
--with-config-file-path=/usr/local/php56/etc \
--enable-fpm \
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
--with-gd \
--without-pear

export LD_LIBRARY_PATH=/usr/local/instantclient_11_2
export NLS_LANG="AMERICAN_AMERICA.AL32UTF8" 

#oci8
 
ln -s /usr/local/instantclient_11_2/libclntsh.so.11.1 \
 /usr/local/instantclient_11_2/libclntsh.so
ln -s /usr/local/instantclient_11_2/libocci.so.11.1 \
/usr/local/instantclient_11_2/libocci.so

ln -s /usr/lib/libnsl.so.2 /usr/lib/libnsl.so.1
# pdo_oci
./configure --with-pdo-oci=instantclient,/usr/local/instantclient_11_2,11.2

```