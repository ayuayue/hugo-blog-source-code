---
title: "制作一个PHP简易框架（六）-- 视图模板"
date: 2021-02-22T09:30:09+08:00
lastmod: 2021-02-22T09:30:09+08:00
draft: false
keywords: [制作一个PHP简易框架]
description: "制作一个PHP简易框架"
tags: [php]
categories: [php]
---

![code](https://images.pexels.com/photos/4974914/pexels-photo-4974914.jpeg?auto=compress&cs=tinysrgb&dpr=2&w=500)

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

### 视图

使用 `Twig` 开发包来提供视图的功能。项目地址 ： https://github.com/twigphp/Twig 。

#### 安装

```bash
composer require "twig/twig:^3.0"
```

#### 使用

在 `web.php` 文件中进行测试

```php
# routes/web.php
$router->get('/', function ($request) {

    $view_path = __DIR__ . '/../resources/views';

    $loader = new \Twig\Loader\FilesystemLoader($view_path);

    $twig = new \Twig\Environment($loader, [
        'cache' => false,
    ]);

    $content = $twig->render('index.twig', ['name' => 'Caoayu']);
    return response($content);
});
```

新建 `resource/views` 目录，并添加 `index.swig` 模板文件

```php
# resource/views/index.swig
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body style="color:red;font-size: 30px;">
    {{ name }} 
</body>
</html>
```

打开项目首页，可以看到输出了传递的 `name` 变量值。

#### 测试缓存

开启缓存，创建并设置缓存目录

```php
# routes/web.php
$router->get('/', function ($request) {

    $view_path = __DIR__ . '/../resources/views';

    $loader = new \Twig\Loader\FilesystemLoader($view_path);

    $twig = new \Twig\Environment($loader, [
        'cache' => __DIR__ . '/../../storage/views',
    ]);

    $content = $twig->render('index.twig', ['name' => 'Caoayu']);
    return response($content);
});
```

更改 `index.swig` 文件，然后访问首页，会发现并没有更改，说明缓存成功。在 `storage/views` 目录下会有缓存文件。

#### 整合重构

1.  整合服务

现在视图功能已经可以使用，但是把视图的逻辑写到路由文件中使得逻辑比较混乱，现在把各部分逻辑分离。

解决的方式便是前面几篇文章提到的容器功能，使用 `ServiceProvider` 来提供视图服务。

新建 `ViewServiceProvider.php` 在 `app/Providers/` 。

```php
# app/Providers/ViewServiceProvider
<?php

namespace App\Providers;

use League\Container\ServiceProvider\AbstractServiceProvider;

class ViewServiceProvider extends AbstractServiceProvider
{
    protected $viewPath;
    protected $cachePath;
    protected $provides = [
        'twig'
    ];

    public function __construct()
    {
        $this->viewPath = __DIR__ . '/../../resources/views';
        $this->cachePath = __DIR__ . '/../../storage/views';
    }

    public function register()
    {
        $container = $this->getContainer();
        $container->add('twig', function () {
            $loader = new \Twig\Loader\FilesystemLoader($this->viewPath);

            $twig = new \Twig\Environment($loader, [
                'cache' => false,
            ]);
            return $twig;
        });
    }
}
```

然后在 `bootstrap/app.php` 文件中进行添加该服务到容器中。

```php
# bootstrap/app.php
<?php
$container = new League\Container\Container;

$container->addServiceProvider(new App\Providers\ViewServiceProvider());
```

此时，我们就可以使用容器来获取模板服务了。也就是 `$container->get('swig')` 的方式来获取视图对象。

2.  整合路由文件

这个时候如果在路由文件中使用视图，需要在路由文件中进行视图的渲染，但这明显不应该在路由文件中被定义。所以，我们创建一个助手函数来帮助我们进行视图的渲染及返回。

**在定义视图全局助手前先创建全局获取容器对象的助手函数，更方便的获取容器对象。**

```php
# bootstrap/core/container.php
<?php

$container = new League\Container\Container;

$container->addServiceProvider(new App\Providers\AppServiceProvider());
$container->addServiceProvider(new App\Providers\ViewServiceProvider());

$GLOBALS['container'] = $container; # 定义超全局变量保存 container 对象
```

```php
# helpers.php
if (!function_exists('app')) {
    function app($server = null)
    {
        $container = $GLOBALS['container']; # 使用超全局函数获取 container 对象
        if ($server) {
            return $container->get($server); # 获取 container 容器中注册过的服务
        }

        return $container; # 服务不存在则返回容器对象

    }
}
```

接下来继续编写视图函数

```php
# helpers.php
if (!function_exists('view')) {
    function view($tql, $data)
    {
        $twig = app('twig'); # 上面定义的 app 函数，获取容器服务
        $content = $twig->render($tql, $data);
        return response($content); # 定义的响应函数
    }
}
```

目前为止完整的 `helpers.php`

```php
# helpers.php
<?php

if (!function_exists('response')) {
    function response($content = '')
    {
        $response = new Laminas\Diactoros\Response;
        $response->getBody()->write($content);
        return $response;
    }
}
if (!function_exists('app')) {
    function app($server = null)
    {
        $container = $GLOBALS['container'];
        if ($server) {
            return $container->get($server);
        }

        return $container;

    }
}
if (!function_exists('view')) {
    function view($tql, $data)
    {
        $twig = app('twig');
        $content = $twig->render($tql, $data);
        return response($content);

    }
}

```

3.  路由文件调用全局函数

```php
# routes/web.php
$router->get('/', function ($request)  {
    $name = 'caoayu';
    return view('index.twig',compact('name'));
});
```

打开浏览器首页，渲染 `index.twig` 成功说明流程正确。此时路由文件就简洁了不少。



 

