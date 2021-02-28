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

----

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

### 对容器内的服务使用配置方式注册

如果每次添加一个服务提供者，我们都要更改 `container.php` 的内容，那么对于不熟悉这个模块的开发者来说可能会误操作引起系统的崩溃，所以，将这块加入配置中，来减少外部对容器服务的更改是非常有必要的。

首先在 `config/app.php` 中定义所有要注册的服务。

```php
# config/app.php
'providers' => [
        App\Providers\AppServiceProvider::class,
        App\Providers\ViewServiceProvider::class
    ]
```

在 `container.php` 文件中遍历所有的服务并注册进去

```php
# bootstrap/core/container.php
<?php

$container = new League\Container\Container;

$container->addServiceProvider(new App\Providers\ConfigServiceProvider());

$GLOBALS['container'] = $container;

foreach(config('providers') as $provider){
    $container->addServiceProvider($provider);
}
```

### 配置信息的隐私问题--引入`ENV`

在项目中，有些配置是私人的，比如密钥。或者一些配置是动态更改的，每个人的环境可能不同，所以就需要使用一个另外的方式来保存配置，这个方式我们使用 `.env` 文件来做。类似与使用每个机器的环境变量。这个文件一般在代码的版本管理中是被忽略上传的。如 `git svn coding gitee` 等。

#### 安装开发包

项目地址：https://github.com/vlucas/phpdotenv

```bash
composer require vlucas/phpdotenv
```

#### 使用

创建 `env` 服务。在 `bootstrap/core` 下创建 `env.php`

```php
# bootstrap/core/env.php
<?php

try {
    $dotenv = Dotenv\Dotenv::createUnsafeImmutable(base_path());
    $dotenv->load();
}catch (\Dotenv\Exception\InvalidPathException $exception){
    dump('env path invaild');
}
```

在 `bootstrap/app.php` 中引用

```php
# bootstrap/app.php
require_once base_path('bootstrap/core/env.php'); # 注意这个引用要防止自动加载文件之下，也就是自动加载后第一个加载的文件，防止 env 服务未初始化完成就调用
```

#### 定义助手函数来方便定义默认值

由于 `env` 中的值都会解析为字符串，所以进行转换，并去除两边的引号

```php
# helpers.php
if (!function_exists('env')) {
    function env($key, $default = null)
    {
        $value = getenv($key,true)?:getenv($key);
        switch ($value) {
            case "true":
                return true;
            case "false":
                return false;
            case "":
                return $default;
            case "null":
                return null;
            default :
                $valueLength = strlen($value);
                if (strlen($value) > 1 && $value[0] === '"' && $value[$valueLength - 1] === '"') {
                    return substr($value, 1, -1);
                }
                return $value;
        }

    }
}
```

#### 调用

例如在 `conifg/cache.php` 中

```php
# config/cache.php
<?php

return [
    'enable' => true,
    'twig' => [
        'cache_enable' => env('APP_CACHE',false),
    ]
];
```

这样将配置分为两层可以更灵活的控制配置的值，也更加隔离，减少不必要的更改。