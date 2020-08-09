---
title: 使用 Acme 脚本申请 let's encrypt 证书配置 HTTPs
date: 2020-08-09T17:23:42+08:00
lastmod: 2020-08-09T17:23:42+08:00
draft: false
keywords: []
description: ""
tags: [HTTPs]
categories: [web]
---

### 宝塔申请`let's encrypt`

#### [给hexo申请证书并设置https](https://www.caoayu.xyz/post/hexo-04/)

在上面这篇文章中介绍了使用宝塔快速给网站申请`https`证书并布置到网站上面,但是证书只有3个月的有效期,昨天发现到期后并没有续签成功,打开计划任务的日志发现由于`python`环境配置除了问题

![image-20200809173021729](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200809173030.png)

根据提示搜索并处理了一段时间后发现比较麻烦,所以索性就自己重新手动的去生成新的证书并配置到`nginx`上,可靠性更高

### 使用 `Acme `脚本的`dns`方式进行证书的申请

#### 脚本的下载和安装

```Bash
cd ~
curl https://get.acme.sh | sh
```

![image-20200809173331350](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200809173333.png)

#### 使用`DNS`方式申请

1. 如果域名时在阿里云购买并备案,使用阿里云提供的`api`进行验证,[注册api](https://usercenter.console.aliyun.com/#/manage/ak), 如果没有用户`key`就申请一个,申请后会生成一个`id`和`key`,提示生成表格文件时,最好确认,生成`key`后只能看一次,后面再查看需要验证手机号,比较麻烦,所以还是推荐第一次生成后就保存起来.

   ![image-20200809173837039](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200809175015.png)

2. 新增环境变量

   再环境变量中新增 `Ali_Key` 和 `Ali_Secrect`两个变量,分别为上面的 `ID`,`Secret`.可以添加为一次性,也可以写入到用户的登录环境配置中

   一次性添加,临时生效,退出即失效

   ```Bash
   export Ali_Key="xxx"
   export Ali_Secret="xxx"
   ```

   永久生效根据你使用的`bash`,我使用的时`zsh`,所以在 `~/.zshrc`中导入变量

   ![image-20200809174335609](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200809174811.png)

3. 进行申请

   ```Bash
   cd ~/.acme.sh/  #进入到acme.sh脚本路径
   acme.sh --issue --dns dns_ali -d xxx.com  #生成 xxx.com域名的证书
   ```

   生成的证书目录 ` /~.acme.sh/xxx.com/`

   ![image-20200809174757894](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200809174759.png)

4. 申请完成后会自动生成一个计划任务,每天进行检测是否过期,过期自动续签.使用 `crontab -l`命令可以查看

   ![image-20200809180246019](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200809180247.png)

#### 配置`nginx`

打开或新建`nginx`的配置文件,新增或修改站点配置

![image-20200809175000958](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200809175002.png)

```Bash
server
{
    listen 80;
	listen 443 ssl http2;
    server_name caoayu.xyz www.caoayu.xyz;
    index index.php index.html index.htm default.php default.htm default.html;
    root /xxx; #站点目录
    
    #SSL-START SSL相关配置，请勿删除或修改下一行带注释的404规则
    #error_page 404/404.html;
    limit_conn perserver 300;
    limit_conn perip 25;
    limit_rate 512k;
    #HTTP_TO_HTTPS_START
    if ($server_port !~ 443){
        rewrite ^(/.*)$ https://$host$1 permanent;
    }
    #HTTP_TO_HTTPS_END
    ssl_certificate    /root/.acme.sh/www.caoayu.xyz/fullchain.cer;
    ssl_certificate_key    /root/.acme.sh/www.caoayu.xyz/www.caoayu.xyz.key;
    ssl_trusted_certificate /root/.acme.sh/www.caoayu.xyz/ca.cer;
    ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    error_page 497  https://$host$request_uri;

    #SSL-END
    
    #ERROR-PAGE-START  错误页配置，可以注释、删除或修改
    #error_page 404 /404.html;
    #error_page 502 /502.html;
    #ERROR-PAGE-END
    
    #PHP-INFO-START  PHP引用配置，可以注释或修改
    #SECURITY-START 防盗链配置
    location ~ .*\.(jpg|jpeg|gif|png|js|css)$
    {
        expires      30d;
        access_log /dev/null;
        valid_referers none blocked www.caoayu.xyz caoayu.xyz;
        if ($invalid_referer){
           return 404;
        }
    }
    #SECURITY-END
    include enable-php-00.conf;
    #PHP-INFO-END
    
    #REWRITE-START URL重写规则引用,修改后将导致面板设置的伪静态规则失效
    include /xxx/rewrite/caoayu.xyz.conf;
    #REWRITE-END
    
    #禁止访问的文件或目录
    location ~ ^/(\.user.ini|\.htaccess|\.git|\.svn|\.project|LICENSE|README.md)
    {
        return 404;
    }

    
    #一键申请SSL证书验证目录相关设置
    location ~ \.well-known{
        allow all;
    }
    
    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
    {
        expires      30d;
        error_log off;
        access_log /dev/null;
    }
    
    location ~ .*\.(js|css)?$
    {
        expires      12h;
        error_log off;
        access_log /dev/null; 
    }
    
    location ~* \.(css|js|png|jpg|jpeg|gif|gz|svg|mp4|ogg|ogv|webm|htc|xml|woff)$ {
    # 同上，通配所有以.css/.js/...结尾的请求
    access_log off;
    add_header    Cache-Control  max-age=360000;
   }
    access_log  /xxx/caoayu.xyz.log;
    error_log  /xxx/caoayu.xyz.error.log;
}
```



### 配置完成后重启`nginx`即可