---
cssclass:
title: IDEA debug 远程调试 Docker容器中的 PHP
tags: [IT/PHP, IT/Docker, IT/PHPStorm]
image-auto-upload: true
date: 2022-10-03 20:12:46
lastmod: 2022-10-25 08:04:49
---
# IDEA debug 远程调试 Docker容器中的 PHP
1.  安装 debug 扩展并配置 php.ini, 重启 php-fpm 使配置生效 `kill -s USR2 1`

```SQL
[XDebug]
xdebug.remote_enable = 1
xdebug.remote_handler = "dbgp"
; Set to host.docker.internal on Mac and Windows, otherwise, set to host real ip
xdebug.remote_host = host.docker.internal
xdebug.remote_port = 9001
xdebug.remote_log = /var/log/php/xdebug2.log

xdebug.idekey="PHPSTORM"
xdebug.remote_autostart=1
xdebug.remote_connect_back=1
```

注意端口 9000 不要被 php-fpm 占用, host.docker.internal 是宿主机的 ip, 如未配置无需更改. docker自动映射

2.  配置 idea

端口与PHP配置要对应

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112201755062.png)

File/Directory 要配置对,本地项目地址与远程的文件路径映射

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112201756738.png)

添加一个 php web page

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112201759259.png)

选择之前配置的 server ,填写对应地址url

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112201800223.png)

监听

只使用 1 开启监听,查看php配置的日志文件,输出正常即可,打断点,访问断点的url即可

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112201805040.png)

使用 2 可以直接以debug 模式访问项目,会带上 debug 的参数在url上