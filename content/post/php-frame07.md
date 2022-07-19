---
title: "制作一个PHP简易框架（七）-- 路由及路径优化"
date: 2021-02-23T11:07:36+08:00
lastmod: 2021-02-23T11:07:36+08:00
draft: false
keywords: [制作一个PHP简易框架]
description: "制作一个PHP简易框架"
tags: [php]
categories: [php]
---

![code](https://images.pexels.com/photos/34140/pexels-photo.jpg?auto=compress&cs=tinysrgb&dpr=2&w=500)

项目地址：https://github.com/ayuayue/php-frame

[制作一个PHP简易框架(一)--准备工作](https://blog.caoayu.top/post/php-frame01)

[制作一个PHP简易框架(二)-- 引入容器](https://blog.caoayu.top/post/php-frame02)

[制作一个PHP简易框架(三)-- 信息调试](https://blog.caoayu.top/post/php-frame03)

[制作一个PHP简易框架(四)-- 路由系统](https://blog.caoayu.top/post/php-frame04)

[制作一个PHP简易框架（五）-- 响应](https://blog.caoayu.top/post/php-frame05)

[制作一个PHP简易框架（六）-- 视图模板](https://blog.caoayu.top/post/php-frame06)

[制作一个PHP简易框架（七）-- 路由及路径优化](https://blog.caoayu.top/post/php-frame07)

[制作一个PHP简易框架（八）-- 配置中心](https://blog.caoayu.top/post/php-frame08)

[制作一个PHP简易框架（九）-- ORM](https://blog.caoayu.top/post/php-frame09)

[制作一个PHP简易框架（十）-- Session and Cookie](https://blog.caoayu.top/post/php-frame10)

----

### 优化

这篇是对前面的一些功能的优化和整理，在软件设计上有个著名的理论：

>   不要过早的优化

这里所说的优化是相对于比较完全的优化，不推荐在初期或者功能未完善是进行大规模的优化整理，是为了防止后面加入的功能对前面的功能产生影响而导致功能没什么进展，倒是一直把精力放在了优化上。有点头重脚轻，但也不是就说不能进行优化，适当的整理和优化会加快项目的进度，不要追求过分的优化，项目完善后怎样进行优化也没有关系。

### 路由使用控制器代替闭包

前面我们都是使用闭包的方式来处理请求的逻辑，现在使用控制器来进行请求的处理，我们使用的路由开发包中是有这个功能的。所以直接使用就可以了。

新建控制器文件及目录。 `app/Controllers/HomeController.php`

```php
# app/Controllers/HomeController.php
<?php


namespace App\Controllers;


class HomeController
{
    public function index($request) # request 即是请求的对象
    {
        $name = 'caoayu';
        return view('index.twig', compact('name'));
    }
}
```

简化路由文件

```php
# routes/web.php
<?php

// map a route
$router->get('/', 'App\Controllers\HomeController::index');
```

开启服务，浏览首页查看输出。

### 路径优化

目前的所有文件中，进行引入外部的文件时都要使用 `__DIR__ ` 来根据自身的相对路径来引入外部文件

优化方案：

使用全局函数，传递一个路径参数，将项目根目录设置为初始目录 (因为 `helpers.php` 文件在根目录下，可以根据全局函数所在的路径进行更改)

```php
# helpers.php
if (!function_exists('base_path')){
    function base_path($path = ''){
        return __DIR__ . ($path?DIRECTORY_SEPARATOR.$path:'');
    }
}
```

设置更多的路径函数，方便访问常见的目录

```php
# helpers.php
if (!function_exists('public_path')){
    function public_path($path = ''){
        return base_path('public') . ($path?DIRECTORY_SEPARATOR.$path:'');
    }
}
if (!function_exists('storage_path')) {
    function storage_path($path = '')
    {
        return base_path('storage') . ($path ? DIRECTORY_SEPARATOR . $path : '');
    }
}
```

更改引入时的路径。注意除了 `index.php` `bootstrap/core/app.php` 中的两个引入。

```php
# public/index.php
require_once __DIR__ . '/../bootstrap/app.php'; # 整个框架的启动服务，此时还没有加载助手函数，所以这个不能更改
```

```php
# bootstrap/core/app.php
require_once __DIR__ . '/../vendor/autoload.php'; # 自动加载文件，也不能使用助手函数
require_once base_path('bootstrap/core/container.php');
require_once base_path('bootstrap/core/route.php');
```

