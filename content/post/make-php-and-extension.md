---
title: "编译安装php及扩展，多版本切换"
date: 2021-08-28T12:42:14+08:00
lastmod: 2021-08-28T12:42:14+08:00
draft: false
keywords: []
description: ""
tags: []
categories: []
---

### 编译安装 php

在 linux 上，搭建新环境需要使用编译安装的方式，也有很多集成环境，或者编译好的源码包，但是我们并不了解整个组织的过程，所以最好自己手动去编译安装一下，并且可以将编译好的 php 及扩展进行打包，可以到不同的机器上直接使用，更方便快捷。

首先下载要安装版本的 php， 可以到官网去下载，https://php.net，下载好之后 使用 tar 命令解压并进入目录中。

开始编译配置，使用命令 ./configure 跟 --prefix --enable --with命令来配置安装路径跟扩展。具体命令说明可以百度，使用 --prefix 等命令时，使用 tab 键会有补全提示，可以根据提示来选择想要安装的扩展，这个就比较方便了。下面是命令的演示。可以在源码的 ext 目录中查看默认的扩展，可以使用 --enable 开启，或者编译安装后在 php.ini 中配置开启，对于没有的扩展，则需要手动编译源码安装配置

```php
./configure \
--prefix=/home/caoayu/software/php74 \
--with-config-file-path=/home/caoayu/software/php74/etc
```

这里只是配置了 php 安装的路径及配置文件所在的路径

安装完成后会在指定的文件夹中生成 php 的可执行文件，包括其他一些辅助应用。在 bin 目录中

```
# tree ~/caoayu/software/php74/bin
bin
├── phar -> phar.phar
├── phar.phar
├── php
├── php-cgi
├── php-config
├── phpdbg
└── phpize
```

查看 php 的默认配置及开启扩展

```
php -i // 输出 phpinfo() 的内容
php -m // 输出 php 开启的扩展
php --ini // 输出 php 的配置文件及加载情况
```

在安装了 php 后，我并没有发现 php.ini 配置文件，版本为 php7.4 。那就手动到 php 源码目录将 php.ini.dev... 或者 php.ini.pro... 移动到设置的 php 配置文件目录，并改名为 php.ini ，重新执行 php --ini 便会发现已经有文件加载了出来。

```
# php --ini
Configuration File (php.ini) Path: /home/caoayu/software/php74/etc
Loaded Configuration File:         /home/caoayu/software/php74/etc/php.ini
Scan for additional .ini files in: (none)
Additional .ini files parsed:      (none)
```

### 安装源码中的扩展

由于安装时没有指定要开启的扩展，所以安装完成后默认开启一些扩展，可以使用 php -m 查看开启的扩展列表，php --ri 查看扩展的信息。安装完成后可以使用 php -i 查看扩展目录，以便后面自己手动安装扩展的时候直接放到这个目录。

```
php -i | grep extension # 查看扩展目录
php -m # 查看开启的扩展
php --ri curl # 查看 curl 扩展的信息
```

可以发现，默认只装了部分扩展，那么我们需要开启其他的一些扩展，可以先到源码的 ext 目录中查找，如果有的话就不需要在下载了。

php 源码 ext 目录

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20210828233311.png)

我们发现默认的扩展里没有开启 curl ，那么我们就来手动添加



进入到 ext/curl 目录下，使用 phpize 命令进行生成 configure 文件，代替我们手动的 ./configure ...

然后就可以使用 make && make install 命令编译扩展了，等待编译完成会在当前的 modules 目录下生成一个 .so 的扩展库文件，将这个文件移动到 php 的扩展目录即可，然后修改 php.ini 增加 extension=curl; 即可

```
cd ext
phpize
make && make install
cp modules/curl.so ~/software/php74/lib/php/no..../

# vim php.ini
extention=curl
# write and quit

php -m 
php --ri curl
```

最终使用  php -m , php --ri curl 查看扩展信息。

### 编译安装下载的扩展

如果需要的扩展不再源码包中，可以去官方扩展库下载，比如 xdebug，下载的源码跟上面 ext里的扩展源码没有区别，可以使用相同的方式进行安装并开启。



### 多版本共存

有时候我们开发多个项目，需要使用不同版本的 php，可以使用软链接的方式来指定版本，或者直接使用不同的版本目录来区别。如下：

在我的 ~/software 目录下，设置了 php/bin 目录为 php 的可执行程序目录，也就是把这个目录设置进了环境变量中，而我编译安装的是在 php74/bin 目录下，此时，使用 ln -s 生成一个链接，就可以直接使用 php 命令，并且如果有其他版本，比如 php56/bin，将这个目录链接到 php 中即可改变 当前环境的 php 版本，对于使用 nginx 或者 apache 来启动的 php 来说，是可以为每个项目配置单独的 php 解释器的。

目录结构

```
# ~/software/

❯ tree -L 1
.
├── node
├── php -> /home/caoayu/software/php74
└── php74

```







