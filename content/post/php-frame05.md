---
title: "制作一个PHP简易框架（五）-- 响应"
date: 2021-02-22T08:56:18+08:00
lastmod: 2021-02-22T08:56:18+08:00
draft: false
keywords: [制作一个PHP简易框架]
description: "制作一个PHP简易框架"
tags: [php]
categories: [php]
---

![code](https://images.pexels.com/photos/1342460/pexels-photo-1342460.jpeg?auto=compress&cs=tinysrgb&dpr=2&w=500)

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

### 从混乱到有序

整合逻辑与功能，将不同职责进行分离，避免在项目变得复杂时而变得异常混乱。一个有序的明了的项目结构可以更好的帮我们理解框架的运行原理，上手也更快速。

整理 `route` ，路由文件中只负责对路由的定义与映射，将 `request` 放到 `index.php` 中，在单文件入口中进行处理请求并返回响应，更能体现到单文件入口的概念。

```php
# bootstrap/core/route.php
<?php

$router = new League\Route\Router;

require_once __DIR__ . '/../../routes/web.php';

```

```php
# public/index.php
<?php

require_once __DIR__ . '/../bootstrap/app.php';

// dump($_SERVER);
$request = Laminas\Diactoros\ServerRequestFactory::fromGlobals(
    $_SERVER,
    $_GET,
    $_POST,
    $_COOKIE,
    $_FILES
);
$response = $router->dispatch($request);

// send the response to the browser
(new Laminas\HttpHandlerRunner\Emitter\SapiEmitter)->emit($response);
```

#### 创建全局的辅助函数

创建全局的响应函数，用来对请求进行响应。当然可以根据功能封装更多的函数。

首先项目根目录下创建 `helpers.php` 。

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
```

在 `composer` 中注册全局函数，使用 `files` 的自动加载方式 [关于自动加载](https://blog.caoayu.top/post/composer-other-autoload)

```json
# composer.json
"autoload": {
        "psr-4": {
            "App\\": "app"
        },
        "files": [
            "helpers.php"
        ]
    },
```

更新自动加载文件 `composer dump-autoload -o`

此时全局函数就已经生效了，现在修改路由文价，使用全局函数来返回数据。

```php
# routes/web.php
<?php

// map a route
$router->get('/', function ($request) {
    return response('hello world');
});
$router->get('/home', function ($request) {

    return response('hello home page');
});

```

此时访问系统首页和 `home` 路由，会输出响应的信息。说明流程没有问题。

