---
title: "制作一个PHP简易框架(四)-- 路由系统"
date: 2021-02-21T15:47:56+08:00
lastmod: 2021-02-21T15:47:56+08:00
draft: false
keywords: [制作一个PHP简易框架]
description: "制作一个PHP简易框架"
tags: [php]
categories: [php]
---

![code](https://images.pexels.com/photos/4709285/pexels-photo-4709285.jpeg?auto=compress&cs=tinysrgb&dpr=2&w=500)

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

### 路由系统

对于一个 `web` 服务来说，路由系统基本是所有 `php` 开发框架中十分基本也是必需的功能了。

### 安装

包地址：https://github.com/thephpleague/route， 该包的作者也是前面 `container` 包的作者。

```bash
composer require league/route
composer require laminas/laminas-diactoros
composer require laminas/laminas-httphandlerrunner
```

### 使用

安装完毕后进行测试。新建一个 `route.php` ,在 `bootstrap/core/` 下。

```php
# bootstrap/core/route.php
<?php

use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;

$request = Laminas\Diactoros\ServerRequestFactory::fromGlobals(
    $_SERVER,
    $_GET,
    $_POST,
    $_COOKIE,
    $_FILES
);

$router = new League\Route\Router;

// map a route
$router->map('GET', '/', function (ServerRequestInterface $request): ResponseInterface {
    $response = new Laminas\Diactoros\Response;
    $response->getBody()->write('<h1>Hello, World!</h1>');
    return $response;
});

$response = $router->dispatch($request);

// send the response to the browser
(new Laminas\HttpHandlerRunner\Emitter\SapiEmitter)->emit($response);

```

可以看到 `$request` 变量中包含了一些全局变量，以后我们就可以直接从 `$request` 变量中，使用面向对象的方式查看请求的信息。

现在打开项目的首页，会返回一个 `Hello World` 在浏览器上。说明路由运行成功。

### 整合路由文件

新建一个 `routes` 目录，用来保存所有的路由文件，并创建 `web.php` 来保存 `web` 服务的路由。

```php
# route/web.php
<?php

// map a route
$router->get('/', function ($request) {
    $response = new Laminas\Diactoros\Response;
    $response->getBody()->write('<h1>Hello, World!</h1>');
    return $response;
});
$router->get('/home', function ($request) {
    $response = new Laminas\Diactoros\Response;
    $response->getBody()->write('<h1>Hello, Home Page!</h1>');
    return $response;
});

```

修改原来的 `route.php` 文件，将所有定义的路由引入进去。

```php
# bootstrap/core/route.php
<?php

include 'path/to/vendor/autoload.php';

$request = Laminas\Diactoros\ServerRequestFactory::fromGlobals(
    $_SERVER,
    $_GET,
    $_POST,
    $_COOKIE,
    $_FILES
);

$router = new League\Route\Router;

require_once __DIR__ . '/../../routes/web.php';
```

修改 `index.php` 将发送响应的逻辑放到最后。

```php
# public/index.php
<?php

require_once __DIR__ . '/../bootstrap/app.php';

$response = $router->dispatch($request);

// send the response to the browser
(new Laminas\HttpHandlerRunner\Emitter\SapiEmitter)->emit($response);
```

打开首页输出 `Hello, World!` ,进入 `/home` 路由输出 `Hello, Home Page!` ,则说明路由映射成功。