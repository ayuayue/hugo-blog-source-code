---
title: "nginx 配置多个 PHP 项目为二级目录"
date: 2021-10-13T22:58:41+08:00
lastmod: 2021-10-13T22:58:41+08:00
draft: false
keywords: []
description: ""
tags: []
categories: []
---

## 说明

此方式使用与本地开发,线上环境还是要规范写, 本地开发主要是图个省事. 并且部署跟日志这些也只都是大部分在线上部署才会使用.

###  二级目录

有时候我们有几个不同的项目,但只有一个域名,我们可以使用子域名来对应不同的项目.更方便一点的话,我们不需要为每个项目都解析二级域名并配置 nginx, 我们可以通过配置二级目录来实现. 这种方法在开发时比较方便, 但线上最好还是使用二级域名, 提高用户的体验

前提是所有的项目在一个目录下. 例如:

├── c-code
├── chem
├── chem_web
├── chem_web_api
├── datagrip
├── dnmp
├── elasticsearch-head
├── fjnu
├── hainanu-haut
├── hugo-blog
├── jou
├── my-docker
├── xjtu
└── zjut

我们开启目录索引的功能, 直接从一个定义的 servername 进去就可以看到所有的项目,点击就可以进去, 这里的项目大多以 thinkphp 跟静态网页举例. 效果图

![image-20211013230614158](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/202110132306379.png)

### 具体配置

不需要为每个项目单独配置,只需要使用反向代理或者路由重写即可.

主要以 thinkphp 为例, 对于其他项目 如 laravel ,可以替换对应的路由重写规则

```nginx
server {
    listen       80;
    server_name  vm.com;
    root   /www;
    autoindex on;
    index   index.php index.html index.htm ;

    access_log  /var/log/nginx/nginx.localhost.access.log  main;
    error_log  /var/log/nginx/nginx.localhost.error.log  warn;

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
    location / {
        try_files $uri $uri/ /index.php?$query_string;
        index index.html index.htm index.php;
        if (!-e $request_filename) {
            rewrite ^/index.php(.*)$ /index.php?s=$1 last;
            rewrite ^/fjnu/public/(.*)$ /fjnu/public/index.php?s=$1 last;
            rewrite ^/jou/public/(.*)$ /fjnu/public/index.php?s=$1 last;
            rewrite ^/zheda/public/(.*)$ /fjnu/public/index.php?s=$1 last;


            rewrite ^(.*)$ /index.php?s=$1 last;
            rewrite ^/fjnu/(.*)$	/fjnu/public/index.php?s=/$1 last;
            rewrite ^/jou/(.*)$	/fjnu/public/index.php?s=/$1 last;
            rewrite ^/zheda/(.*)$	/fjnu/public/index.php?s=/$1 last;
            break;
        }
    }
    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
        fastcgi_pass   php56:9000;
        include        fastcgi-php.conf;
        include        fastcgi_params;
    }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    location ~ /\.ht {
       deny  all;
    }
}


```

重写规则可以自行添加, 不需要 使用 location 去匹配每个二级目录,直接使用顶级目录来匹配.

我们可以使用选中项目,在进入项目的入口文件, 比如常见的 public 目录. 就可以直接进入到项目

### 注意

因为是一个 server 所以配置的日志都在一个文件中, 所以如果多个项目都公用了一些服务器的资源, 比如 session, 可能会有些冲突, 建议切换项目的时候退出上一个项目, 或者使用无痕模式.
