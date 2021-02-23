---
title: "制作一个PHP简易框架(二)-- 引入容器"
date: 2021-02-21T08:44:20+08:00
lastmod: 2021-02-21T08:44:20+08:00
draft: false
keywords: [制作一个PHP简易框架]
description: "制作一个PHP简易框架"
tags: [php]
categories: [php]
---

[一 准备](https://www.caoayu.xyz/post/php-frame01)



---

项目地址：https://github.com/ayuayue/php-frame

---



### 引入容器

使用容器来管理服务包的依赖问题，避免在使用某个第三方包时由于依赖太多而造成项目的复杂度增大与难以管理的问题。

并没有手动去编写容器服务，而是使用第三方包来继承。这里使用 https://github.com/thephpleague/container 这个包。

#### 安装

```bash
composer require league/container
```

#### 项目中使用

首先先创建一个简单的服务，然后使用 `container` 进行管理。

创建 `app/Service/Alarm.php` 文件。

```php
<?php

namespace App\Service;

class Alarm
{
    public function wakeup()
    {
        var_dump('Alarm Service');
        echo '<br>';
    }
}

```

在 `bootstrap` 目录下创建 `core` 目录，并在 `core` 目录下创建 `container.php` 文件。加入以下代码；

```php
# bootstrap/core/container.php
<?php

use App\Service\Alarm;

$container = new League\Container\Container;

$container->add('alarm', new Alarm());

$container->get('alarm')->wakeup();
```

打开浏览器，访问首页会打印出 `wakeup` 函数的输出。说明测试成功。

#### 创建 `Provider` 

如果每个类都要手动在 `container.php` 中进行手动添加，就会十分的繁琐，借鉴 `laravel` 等框架的做法，创建一个 `Provider` 用来帮我们完成这个步骤。

创建 `AppServiceProvider.php` 文件，在 `app/Providers` 目录中。编辑

```php
# app/Providers/AppServiceProviders
<?php

namespace App\Providers;

use App\Service\Alarm;
use League\Container\ServiceProvider\AbstractServiceProvider;

class AppServiceProvider extends AbstractServiceProvider
{
    protected $provides = [
        'alarm'
    ];
    public function register()
    {
        $this->getContainer()->add('alarm', new Alarm());
    }
}
```

只要在这个类中将需要添加的包类注册进去就可以了，统一管理，更加直观。`$providers` 数组用来保存类的别名，使用数组来加快对于类的查找，通过遍历数组的方式。

在 `container` 中进行添加

```php
# bootstrap/core/container.php
<?php

$container = new League\Container\Container;

$container->addServiceProvider(new App\Providers\AppServiceProvider());
```

调用测试

```php
# bootstrap/core/container.php
$container->get('alarm')->wakeup();
```

正常输出说明 `AppServiceProvider` 添加成功。