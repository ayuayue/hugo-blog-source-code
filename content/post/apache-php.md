---
cssclass:
title: apache-php
tags: [IT/Centos, IT/Apache, IP/PHP, IT/Linux]
image-auto-upload: true
date: 2022-10-03 20:46:46
lastmod: 2022-10-25 08:57:46
---
# apache-php
### 基础软件及apache

```Bash
yum install wget -y
# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum makecache

yum install epel-release \ 

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

[https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html](https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html)

下载如下两个文件（下载文件需注册）：并解压到一个文件夹中 如 instantclient_11_2

`oracle-instantclient11.2-basic`

`oracle-instantclient11.2-sdk`

```Bash
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
```

```Bash
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

PHP Warning: Unknown: It is not safe to rely on the system's timezone settings. You are _required_ to use the date.timezone setting or the date_default_timezone_set() function. In case you used any of those methods and you are still getting this warning, you most likely misspelled the timezone identifier. We selected the timezone 'UTC' for now, but please set date.timezone to select your timezone. in Unknown on line 0

```Bash
vi /usr/local/php56/etc/php.ini
date.timezone = Asia/Shanghai

```
安装完成后,会在 /usr/local/apache2/modules/ 生成一个 libphp5 的文件,并且会自动更改 http.conf 增加

```
LoadModule php5_module        modules/libphp5.so
```

#### 配置环境变量及软链接

同上  apache的配置

使用 php -v 检测安装结果

### 配置 apache 解析 php

首先 新建一个 php 脚本用来测试

```php
# vim /usr/local/apache2/htdocs/index.php
<?php
echo phpinfo();
# vim /usr/local/apache2/conf/http.conf

<IfModule dir_module>
    DirectoryIndex index.html index.htm index.php
</IfModule>

AddType application/x-compress .Z
AddType application/x-gzip .gz .tgz

AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .php5

<Directory />
    Options FollowSymLinks
    AllowOverride none
    #Require all denied
    Require all granted
    Satisfy all
</Directory>
```

### 配置虚拟主机

修改 httpd.conf 文件,引入 虚拟主机文件

```conf
# Virtual hosts
Include conf/extra/httpd-vhosts.conf
```

然后更改 httpd-vhosts.conf 文件, 在 httpd.conf 文件中配置的 默认站点配置会失效

```bash
# vim conf/extra/httpd-vhosts.conf
<VirtualHost *:80>
    #ServerAdmin webmaster@dummy-host.example.com
    DocumentRoot "/mnt/hgfs/development/github/hugo-blog/public"
    ServerName hugo.com
    #ServerAlias www.dummy-host.example.com
    ErrorLog "logs/hugo.com.error_log"
    CustomLog "logs/hugo.com.access_log" common
</VirtualHost>

<VirtualHost *:80>
    #ServerAdmin webmaster@vm.com
    DocumentRoot "/usr/local/apache2/htdocs/"
    ServerName vm.com
    ErrorLog "logs/vm.com-error_log"
    CustomLog "logs/vm.com-access_log" common
</VirtualHost>
```

这里添加了两个站点.相应的在 Windows 下也要配置 hosts 文件映射这两个站点

```bash
# vim /c/Windows//System32/drivers/etc/hosts

192.168.190.128 vm.com hugo.com
```

### 注意事项

1. 如果使用浏览器跟命令行都打不开,可以清除下 Windows 的 dns ,使用 cmd

```bash
ipconfig /flushdns
```

1. 如果只是 浏览器打不开
2. 可能是缓存,清下浏览器缓存
3. 可能是浏览器默认打开为 https ,但是并没有配置 https,换成 http打开即可

### PHP 7

如果是配置 php7 及以上的php跟apache,流程一样,编译php时同样要带上 —with-apxs2 会生成 [libphp7.so](http://libphp7.so) 文件到 apache的modules 里.其他都是一样
