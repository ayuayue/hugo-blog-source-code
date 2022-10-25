---
cssclass:
title: nginx配置
tags: [IT/Nginx]
image-auto-upload: true
date: 2022-10-03 20:57:46
lastmod: 2022-10-25 08:30:50
---
# nginx配置
二级目录反向代理

例如：easyswoole

```Bash
location  /easyapi/ {
      rewrite ^/easyapi/(.*)$ /$1 break;
        proxy_pass  http://82.157.131.35:9501/;  # 反向代理至http://127.0.0.1:9503
        proxy_redirect     off;
        proxy_set_header   Host             $host;
        proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
    }
```

php-fpm

```Bash
    location ~ \.php$ {
        fastcgi_pass   php:9000;
        include        fastcgi-php.conf;
        include        fastcgi_params;
    }
```

通用url重写 laravel tp

```Bash
location / {
        try_files $uri $uri/ /index.php?$query_string;
        index index.html index.htm index.php;
        if (!-e $request_filename) {
            rewrite ^/index.php(.*)$ /index.php?s=$1 last;
            rewrite ^(.*)$ /index.php?s=$1 last;
            break;
        }
    }
```

配置反向代理后某些静态资源请求路径失败，重写url为项目目录

```Bash
location ~ /tp/.*\.(gif|jpg|jpeg|bmp|png)$

{

  rewrite '^/tp/(.*)/(.*).(png|jpg|gif)$' /tp6/public/$1/$2.$3 last;

}
```

二级目录重写

```Bash
     location / {
         try_files $uri $uri/ /index.php?$query_string;
         index index.html index.htm index.php;
         if (!-e $request_filename) {
             #   二级目录URL重写
             rewrite ^/fjnu/public/(.*)$ /fjnu/public/index.php?s=$1 last;
             #   一级目录 URL 重写
             rewrite ^/index.php(.*)$ /index.php?s=$1 last;
             #   重写所有
             rewrite ^(.*)$ /index.php?s=$1 last;
             break;
         }
     }
```

easyswoole静态文件

```Bash
  location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|woff2|woff|ttf|ico)$     # 这个要全
  {
      root /www/easyswoole-admin/App/Static;  # 写这个项目静态文件夹的绝对地址
      expires      30d;
  }
  location ~ .*\.(js|css|map)?$
  {
      root /www/easyswoole-admin/App/Static; # 写这个项目静态文件夹的绝对地址
      expires      12h;
  }
```