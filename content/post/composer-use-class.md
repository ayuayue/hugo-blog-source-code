---
title: "Composer （二） -- 使用第三方包"
date: 2021-01-19T17:44:58+08:00
lastmod: 2021-01-19T17:44:58+08:00
draft: false
keywords: [Composer]
description: "Composer"
tags: [Composer]
categories: [Composer]
---

[composer 依赖管理](https://www.caoayu.xyz/post/composer/)  这篇文章写了对于第三方包的安装及使用，以及 `composer` 项目配置文件的说明，接下来说说 `composer` 安装第三方包后的使用。

首先，以 `monolog` 这个包为例。[github地址](https://github.com/seldaek/monolog)

安装

```bash
composer require monolog/monolog
```

创建文件并编码

```php
# log.php
<?php
require_once 'vendor/autoload.php';

use Monolog\Logger;
use Monolog\Handler\StreamHandler;

// create a log channel
$log = new Logger('name');
$log->pushHandler(new StreamHandler('test.log', Logger::WARNING));

// add records to the log
$log->warning('Foo');
$log->error('Bar');

echo "log end\n";
```

运行

```bash
php log.php
```

结果会输出 `log end` ,并生成一个 `test.log` 文件，文件中会有空错误和空警告的日志信息。说明已经调用成功了。

核心

```php
require_once 'vendor/autoload.php';
```

核心就是引入了自动加载文件，而这个文件会帮我们将所有的类找到并在使用时调用。[关于自动加载](https://www.caoayu.xyz/post/composer-autoload/)

一般在安装依赖或者更新 `composer.json` 文件后，会自动更新自动加载文件

![image-20210203190217624](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210203190217624.png)

如果在调用时找不到类，那么就手动再更新下自动加载文件

使用命令

```bash
composer dump-autoload # 或 composer dumpautoload
```

![image-20210203190426539](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210203190426539.png)