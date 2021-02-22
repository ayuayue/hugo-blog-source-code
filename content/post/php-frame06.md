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

[一 准备](https://www.caoayu.xyz/post/php-frame01)

[二 引入容器](https://www.caoayu.xyz/post/php-frame02)

[三 信息调试](https://www.caoayu.xyz/post/php-frame03)

[四 路由](https://www.caoayu.xyz/post/php-frame4)

[五 响应](https://www.caoayu.xyz/post/php-frame05)

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



