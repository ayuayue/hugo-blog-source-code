---
title: "制作一个PHP简易框架（八）-- 配置中心"
date: 2021-02-24T08:24:44+08:00
lastmod: 2021-02-24T08:24:44+08:00
draft: false
keywords: [制作一个PHP简易框架]
description: "制作一个PHP简易框架"
tags: [php]
categories: [php]
---

![code](https://images.pexels.com/photos/546819/pexels-photo-546819.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260)

### 配置中心

对于一个需要灵活更改配置的项目来说，配置中心是必不可缺的一个功能。它可以帮我们统一管理配置项，降低了更改配置时对系统造成误操作的风险。

### 安装

同样使用开发包来集成这个服务。项目地址：https://github.com/hassankhan/config

```bash
composer require hassankhan/config
```

### 使用

首先将配置服务注册到容器中，方便我们使用容器来管理。创建 `CacheServiceProvider.php` 

```php
# app/Providers/CacheServiceProvider.php
<?php

namespace App\Providers;
use League\Container\ServiceProvider\AbstractServiceProvider;
use Noodlehaus\Config;
class ConfigServiceProvider extends AbstractServiceProvider
{
    protected $provides = [
        'config'
    ];

    public function register()
    {
        $container = $this->getContainer();
        $container->add('config',new Config(base_path('config')));
    }
}
```

添加到容器中

```php
# bootstrap/core/container.php
$container->addServiceProvider(new \App\Providers\ConfigServiceProvider());
```

新建配置目录及配置文件，来测试配置是否开启成功

```php
# config/app.php
<?php

return [
    'name' => 'caoayu',
    'debug' => true
];

```

```php
# config/cache.php
<?php

return [
    'enable' => true,
    'twig' => [
        'cache_enable' => false,

    ]
];

```

对配置信息的引用，测试使用

```php
# app/Controllers/HomeController.php
<?php

namespace App\Controllers;

class HomeController
{
    public function index($request)
    {
        $name = app('config')->get('name');
        return view('index.twig', compact('name'));
    }
}
```

打开浏览器，`app.php` 中配置的 `name` 渲染成功说明流程没有问题

#### 定义缓存助手函数更方便的获取配置信息

```php
# helpers.php
if (!function_exists('config')) {
    function config($key = '')
    {
        return app('config')->get($key);
    }
}
```

#### 自定义是否使用页面缓存

修改视图的服务提供者

```php
# app/Providers/ViewServiceProvider.php
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
        $this->viewPath = base_path('resources/views');
        $this->cachePath = base_path('storage/views');
    }

    public function register()
    {
        $container = $this->getContainer();
        $container->add('twig', function () {
            $loader = new \Twig\Loader\FilesystemLoader($this->viewPath);

            $twig = new \Twig\Environment($loader, [
                'cache' => config('twig.cache_enable') ? $this->cachePath : false, # 使用注册的全局助手函数获取配置信息
            ]);
            return $twig;
        });
    }
}
```

在 `register` 中的 `cache` 中判断是否配置开启了缓存服务。

修改视图文件，如果没有改变则说明已经被缓存成功。第一次访问时由于开启缓存并且找不到缓存，是会发生更改，当缓存文件生成后在修改就不会看到更改了，缓存文件会生成在 `storage/view` 目录中。

