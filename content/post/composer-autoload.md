---
title: "Composer (二) -- 自动加载"
date: 2021-01-22T12:44:19+08:00
lastmod: 2021-01-22T12:44:19+08:00
draft: true
keywords: [Composer,自动加载]
description: "Composer (二) -- 自动加载"
tags: [Composer]
categories: [Composer]
---

## **自动加载**

[composer (一) -- 依赖管理](https://www.caoayu.xyz/post/composer/) 前面这篇文章介绍了 `composer` 对依赖的安装及更新。这次介绍下 `composer` 的自动加载机制。

自动加载机制到目前有了很多个标准,目前使用最多的是 `PSR-4` 标准，4 版本之前的标准已经被弃用了。[psr-4自动加载规范](https://learnku.com/docs/psr/psr-4-autoloader-meta/1610)

当我们安装了依赖或者进行更新以及创建一个新的项目时，在 `vendor` 包中总是会生成一个 `autoload.php` 文件，这个文件就是用来记录并规范自动加载的配置文件。

查看 `autoload.php` 文件

```php
# autoload.php
require_once __DIR__ . '/composer/autoload_real.php';

return ComposerAutoloaderInitc5a08d4f09af4fb49c6b4dce16426448::getLoader();
```

可以看到加载了真正的 `autoload_real.php` 文件，并返回一个加载器方法。

所以在使用所有的第三方包或者框架时，都会加载这个文件，大部分框架已经在初始化时加载进来了，不需要我们在使用时每次都手动加载 `require_once __DIR__ . 'vendor/autoload.php' `