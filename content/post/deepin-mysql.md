---
title: "Deepin LNMP环境搭建"
date: 2020-12-05T11:26:37+08:00
lastmod: 2020-12-05T11:26:37+08:00
draft: false
keywords: [Deepin LNMP环境搭建]
description: "Deepin LNMP环境搭建"
tags: [Deepin]
categories: [Deepin]
---

### `LNMP`

`lnmp` 是一个常见的`web`开发的环境，尤其对于 `phper` 来说，基本是必须要使用的一套环境。但是在 `Linux` 下的安装和配置并不是那么容易，所以近些年涌现了不少的一键安装方案，比如 `lnmp`、宝塔等。基本满足了日常的使用，但是如果不自己动手打通每个软件直接的配置与关联，我们就无法深入的去理解各个软件之间是如何协作的。而且往往集成环境的各种细节也不太清楚，如果想要扩展或是有出现错误，有时也很难短时间的解决。所以独立安装会让我们更清晰的了解整个的流程，对于错误也更好的去分析是那个环节出现了问题。

### `Linux`

本文基于 `deepin` 环境，所以不需要再安装配置 `linux` 环境。

### `Nginx`

仓库中的 `nginx` 就可以使用，一般 `nginx` 不需要安装太新的版本。

如果安装过可以不用安装，或者卸载重新安装

```bash
sudo apt remove nginx --purge #
```

安装

```bash
sudo apt install nginx
nginx -v # 验证是否安装成功
service nginx status # 查看 nginx 的运行状态 stop停止 restart重启
```

### `PHP`

仓库中的 `php` 是 `7.3`版本，直接安装仓库中的 `php7.3-fpm` 

同样，安装过想要重新安装可以先卸载

```bash
sudo apt remove php7.3-fpm --purge
```

安装

```bash
sudo apt install php7.3-fpm
# 安装扩展，有时运行项目提示没有扩展，需要使用下面命令安装
sudo apt install php7.3-xml php7.3-curl php7.3-mbstring php7.3-xml
# 如果还需要扩展可以使用 sudo apt install php7.3- 加Tab键自动提示所有的扩展，然后选择需要的扩展进行安装
```

### `MYSQL`

`deepin` 环境中看到社区很多反映的就是 `mysql` 的安装问题，要不然是版本太低，要不然是配置失败

其实这些软件，在官网都会有每个发行版，每个架构的安装和使用方法，只要耐心查看，安装和使用是没有问题的。

[各个发行版下载页](https://dev.mysql.com/downloads/)

这里我们使用的是 `deepin` 所以选择 `mysql apt repository` 进入

这里可以点击下载，使用 `No thinks` 那个选项下载，下载 `mysql` 的官方源 `deb` 包，也可以复制链接 使用 `wget` 下载

```bash
wget https://dev.mysql.com/get/mysql-apt-config_0.8.16-1_all.deb 
```

安装

```bash
sudo dpkg -i mysql-apt-config_0.8.16-1_all.deb  # 就会进入安装源的过程
# 选择 mysql-server 
# 版本 根据自己需要进行选择
# 最后选择 ok

sudo apt update # 更新 安装源成功就会看到有 mysql 的源输出
sudo apt install mysql-server # 安装 mysql 服务
# 输入 y
# 会弹框提示输入密码，输入两次即可
# 会让认证插件， 一般选择第二个，第一个可能兼容性不好。
```

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20201205122825.png)

验证

```bash
mysql -uroot -p
# 输入密码
# 成功进入mysql终端
```

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20201205122901.png)

### composer安装

[官方安装](https://pkg.phpcomposer.com/#how-to-install-composer)

安装完如果有问题，记得加入执行权限

```bash
sudo chmod a+x /usr/bin/composer
```

### 配置阿里云镜像源

```bash
composer config -g repo.packagist composer mirrors.aliyun.com/composer/
```

### 配置 `nginx` 与 `php`

新增 `nginx` 配置文件，默认配置文件在 `/etc/nginx/nginx.config`

可以看到默认配置文件的引入文件内容

```bash
include /etc/nginx/conf.d/*.conf;
include /etc/nginx/sites-enabled/*;
```

所以我们不需要更改默认的配置，只需要在者两个目录下新建配置文件就可以被加载进来，推荐将文件新建到 `/etc/nginx/sites-enabled/` ,可以看到有个默认的 `default` 文件，为了避免冲突，可以将这个文件的内容全部注释，或者将文件移动到其他不会加载的位置。推荐将文件名设置为 项目名，后缀为 `.conf` 即可。比如新建 `caoayu.xyz.conf` 内容如下

```bash
server {
    listen 80;
    server_name example.com;
    root /example.com/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.html index.htm index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/run/php/php7.3-fpm.sock; # fpm的sock地址
        fastcgi_index index.php;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

关于 `php-fpm` 的地址，也就是 `fastcgi_pass` 那一项的值，我们找到 `php-fmp` 的配置文件，在 `/etc/php/7.3/fpm/php-fpm.conf` ,可以看到 `sock` 的地址

```bash
[global]
; Pid file
; Note: the default prefix is /var
; Default Value: none
pid = /run/php/php7.3-fpm.pid
```

所以配置为 `fastcgi_pass unix:/run/php/php7.3-fpm.sock`

关于 `nginx` 与 `php` 的配置可参考 [laravel官方文档](https://laravel.com/docs/5.5/deployment)