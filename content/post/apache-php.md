---
title: "Apache + php 配置"
date: 2021-09-12T16:08:50+08:00
lastmod: 2021-09-12T16:08:50+08:00
draft: false
keywords: []
description: ""
tags: []
categories: []
---

使用 apache + php 来作为服务器解析 php 脚本的话,需要 `libphp5 的一个库文件, 这个库文件需要编译安装 php 的时候   `--with-apxs2=/usr/local/apache2/bin/apxs` 将会在 apache 的 modules 目录中生成 libphp 的库文件并在 httpd.conf 文件中开启配置

### 安装前

编译安装 apache 需要 先 安装 `APR APR_Util PCRE GCC GCC-C++ ` 等包.

```bash
yum install expat-devel -y
```

#### 编译安装 APR

```bash
wget http://archive.apache.org/dist/apr/apr-1.5.2.tar.gz
tar -zxvf  apr-1.5.2.tar.gz
cd apr-1.5.2/
./configure --prefix=/usr/local/apr
make && make install
```

#### 编译安装 APR-Util

```bash
wget http://archive.apache.org/dist/apr/apr-util-1.5.4.tar.gz
tar -zxvf apr-util-1.5.4.tar.gz
cd apr-util-1.5.4/

./configure --prefix=/usr/local/apr-util \
-with-apr=/usr/local/apr/bin/apr-1-config

make && make install
```

#### 编译安装 pcre-conf

```bash
wget https://sourceforge.net/projects/pcre/files/pcre/8.39/pcre-8.39.tar.gz
tar -zxvf pcre-8.39.tar.gz
cd pcre-8.39
./configure --prefix=/usr/local/pcre
make && make install
```

### 进入正题,编译 apache

```bash
wget http://archive.apache.org/dist/httpd/httpd-2.4.23.tar.gz
tar -zxvf httpd-2.4.23.tar.gz
cd httpd-2.4.23

./configure --prefix=/usr/local/apache2 \
--with-apr=/usr/local/apr \
--with-apr-util=/usr/local/apr-util/ \
--with-pcre=/usr/local/pcre
```

#### 配置环境变量,及软连接,适应多版本 apache

1. 修改 .bashrc 加入

```Bash
export APACHE="/usr/local/apache"
export PHP="/usr/local/php"
export PATH="${HOME}/bin:${HOME}/.g/go/bin:$PATH:$APACHE/bin:$PHP/bin"
```

可以看到 编译时配置的路径是 apache2 ,而环境变量是 apache, php

所以我们在环境中保留一个版本的软件,名称并不带版本号.

1. 创建软连接

```Bash
ln -s /usr/local/apache2 /usr/local/apache
```

这样,系统的apache环境就变成了 2, 如果编译安装了 1 版本的,那么用 1 的生成软连接即可.

同理, PHP及其他软件相同

1. 刷新 ` source ~/.bashrc`
2. 检查 `apchectl  httpd` 命令 的 -v 参数如果输出了版本信息那么就 OK了 😎

#### 备份并修改配置文件

备份

```bash
cp /usr/local/apache2/conf/httpd.conf /usr/local/apache2/conf/http.conf.bak
```

修改

```bash
vim ...apache2/etc/httpd.conf

#ServerName www.example.com:80
ServerName 0.0.0.0:80
```

#### 启动暂停重启

```bash
apacheclt stop | start | restart | status

httpd
```

#### 检测配置文件是否正确

```bash
httpd -t
apachectl -t
```

启动后访问虚拟机的 80 端口返回 it works

### 编译安装 php

至于下载,可以到官网下载 常用的版本  `5.6  7.0  7.4`  http://www.php.net/downloads.php

```Bash
cd php-5.6.40/

./configure --prefix=/usr/local/php56 \
                                --with-apxs2=/usr/local/apache2/bin/apxs \
                                --with-mysql=mysqlnd \
                                --with-gd \
                                --without-pear \
                                --disable-phar
                                
 make && make install
```

#### 报错 configure: error: xml2-config not found. Please check your libxml2 installation.

系统中未安装 libxml 库

```bash
yum install libxml2

yum install libxml2-devel -y
```

#### 报错  configure: error: png.h not found.

```bash
yum install libpng

yum install libpng-devel
```

直到提示 php 信息 后可以使用 `make && make install`

![img](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/202109121245345.png)

#### 重新编译

如果要重新编译, 在更改了 .configure 命令或者配置的情况下,最好清空一下上次编译好的文件

```Bash
make clean
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
