---
title: "Composer (三) -- Psr 自动加载"
date: 2021-01-22T12:44:19+08:00
lastmod: 2021-01-22T12:44:19+08:00
draft: false
keywords: [Composer,自动加载]
description: "Composer (二) -- 自动加载"
tags: [Composer]
categories: [Composer]
---

## **自动加载**

[composer (一) -- 依赖管理](https://blog.caoayu.top/post/composer/) 前面这篇文章介绍了 `composer` 对依赖的安装及更新。

[composer (二) -- 第三方包使用](https://blog.caoayu.top/post/composer-use-class/) 这篇文章记录了如何使用一个第三方类。

这次介绍下 `composer` 的自动加载机制。

自动加载机制到目前有了很多个标准,目前使用最多的是 `PSR-4` 标准，4 版本之前的标准已经被弃用了。[psr-4自动加载规范](https://learnku.com/docs/psr/psr-4-autoloader-meta/1610)

当我们安装了依赖或者进行更新以及创建一个新的项目时，在 `vendor` 包中总是会生成一个 `autoload.php` 文件，这个文件就是用来记录并规范自动加载的配置文件。

查看 `autoload.php` 文件

```php
# autoload.php
require_once __DIR__ . '/composer/autoload_real.php';

return ComposerAutoloaderInitc5a08d4f09af4fb49c6b4dce16426448::getLoader();
```

可以看到加载了真正的 `autoload_real.php` 文件，并返回一个加载器方法。

继续追溯这个 `getLoader()` 方法

```php
# composer/autoload_real.php
# getLoader()
public static function getLoader()
    {
        if (null !== self::$loader) {
            return self::$loader;
        }
    ...
        $map = require __DIR__ . '/autoload_namespaces.php';
            foreach ($map as $namespace => $path) {
                $loader->set($namespace, $path);
            }

            $map = require __DIR__ . '/autoload_psr4.php';
            foreach ($map as $namespace => $path) {
                $loader->setPsr4($namespace, $path);
            }

            $classMap = require __DIR__ . '/autoload_classmap.php';
            if ($classMap) {
                $loader->addClassMap($classMap);
            }
    ...
```

其中最关键的就是 `$map` 那几行代码，定义了三种自动加载的方案。

1.`namespace` 2.`psr4` 3 `classmap`

所以在使用所有的第三方包或者框架时，都会加载这个文件，大部分框架已经在初始化时加载进来了，不需要我们在使用时每次都手动加载 `require_once __DIR__ . 'vendor/autoload.php' `

### Psr4 加载

首先打开 `vendor/composer/autoload_psr4.php` ,这里是 `psr4` 规范所对应的自动加载规则

```php
# autoload_psr4.php
return array(
    'Psr\\Log\\' => array($vendorDir . '/psr/log/Psr/Log'),
    'Monolog\\' => array($vendorDir . '/monolog/monolog/src/Monolog'),
);
```

可以看到，`psr` 通过关联数组的方式将类名与包源码所对应起来。

那 `monolog` 是如何自动加载到这个数组中的呢？

我们找到 `monolog/src/monolog/composer.json` 文件，可以看到 `autoload` 属性。

```json
# composer.json
 "autoload": {
        "psr-4": {"Monolog\\": "src/Monolog"}
  },
 "autoload-dev": {
        "psr-4": {"Monolog\\": "tests/Monolog"}
  },
```

可以看到，这里指定了自动加载的方式以及对应关系。当安装或更新依赖时，就会自动更新这些依赖的加载。

#### 使用 `psr4` 引入自定义 `class`

通过上面的 `monolog` 可以发现，只要在 `composer.json` 文件中定义自动加载方式及对应关系，生成自动加载文件就可以了。开始试验。

首先在项目目录下新建目录和文件：

```bash
App
├── Controllers
│   └── UserController.php
└── Models
    └── User.php
...
├── composer.json
├── test.php
```

```php
# UserController.php
<?php

namespace App\Controllers;

class UserController
{
    function __construct()
    {
        echo "user controller autoload";
    }
}
```

```php
# User.php
<?php

namespace App\Models;

class User
{
    function __construct()
    {
        echo "user model autoload";
    }
}

```

```php
# test.php
<?php

require 'vendor/autoload.php';

use App\Controllers\UserController;

$usercontroller = new UserController();
```



修改 `composer.json` 文件：

```json
{
	"name": "caoayu/composer",
	"description": "composer",
	"require": {
		"monolog/monolog": "^2.2"
	},
	"autoload": {
		"psr-4": {"App\\":"App"}
	}
}
```

更新自动加载文件

```bash
composer dump-autoload
```

运行 `test.php` 文件：

```bash
php test.php
# user controller autoload
```

**注意,psr4 的方式规定类的命名空间一定要与类的目录结构对应，在 Linux 上，文件名是区分大小写的，所以命名空间的大小写也是不一样的。尽量在每个平台都保持统一**