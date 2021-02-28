---
title: "制作一个PHP简易框架(三)-- 信息调试"
date: 2021-02-21T15:36:48+08:00
lastmod: 2021-02-21T15:36:48+08:00
draft: false
keywords: [制作一个PHP简易框架]
description: "制作一个PHP简易框架"
tags: [php]
categories: [php]
---

![code](https://images.pexels.com/photos/270557/pexels-photo-270557.jpeg?auto=compress&cs=tinysrgb&dpr=2&w=500)

项目地址：https://github.com/ayuayue/php-frame

[制作一个PHP简易框架(一)--准备工作](https://www.caoayu.xyz/post/php-frame01)

[制作一个PHP简易框架(二)-- 引入容器](https://www.caoayu.xyz/post/php-frame02)

[制作一个PHP简易框架(三)-- 信息调试](https://www.caoayu.xyz/post/php-frame03)

[制作一个PHP简易框架(四)-- 路由系统](https://www.caoayu.xyz/post/php-frame04)

[制作一个PHP简易框架（五）-- 响应](https://www.caoayu.xyz/post/php-frame05)

[制作一个PHP简易框架（六）-- 视图模板](https://www.caoayu.xyz/post/php-frame06)

[制作一个PHP简易框架（七）-- 路由及路径优化](https://www.caoayu.xyz/post/php-frame07)

[制作一个PHP简易框架（八）-- 配置中心](https://www.caoayu.xyz/post/php-frame08)

[制作一个PHP简易框架（九）-- ORM](https://www.caoayu.xyz/post/php-frame09)

[制作一个PHP简易框架（十）-- Session and Cookie](https://www.caoayu.xyz/post/php-frame10)

----

### 信息调试

使用 `php` 自带的 `var_dump、 print_r` 等方法来打印输出并不美观，尤其在打印的数据比较多的时候，非常不易于观看，本次加入 `symfony/var-dumper` 开发包来进行命令的打印。包地址：https://github.com/symfony/var-dumper

### 安装

```bash
composer require --dev symfony/var-dumper # 在开发模式下开启
```

线上模式如果想过滤掉这个包，可以在安装的时候使用参数 `--no-dev` 来进行过滤。

命令如下

```bash
composer install --no-dev
```

### 使用

直接使用 `dump` 函数，将需要打印的参数传递进去即可。

```php
# public/index.php
<?php

require_once __DIR__ . '/../bootstrap/app.php';

dump($_SERVER);
```

![image-20210221154602101](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210221154602101.png)

这样就清晰了很多。