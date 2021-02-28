---
title: "制作一个PHP简易框架(一)--准备工作"
date: 2021-02-20T10:45:32+08:00
lastmod: 2021-02-20T10:45:32+08:00
draft: false
keywords: [制作一个PHP简易框架]
description: "制作一个PHP简易框架"
tags: [php]
categories: [php]
---

![code](https://images.pexels.com/photos/276452/pexels-photo-276452.jpeg?auto=compress&cs=tinysrgb&dpr=2&w=500)

### 前言

制作一个极简版的 `php` 框架。串联一下 `web` 开发中需要注意的核心关键点，从零开始构建一个完整的系统，通过创建系统框架的过程，理解 `web` 开发中做核心的内容。

**并不是所有功能都自己完成，而是大量使用开发包，由自己来组织整个框架**



---

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



### 准备工作

1. 创建单入口文件。`index.php` 并进行简单输出。

```php
<?php 
var_dump('caoayu');
```
2. 使用 `php` 内置 `web` 服务器启动一个 `web` 服务，端口自定义。
```bash
php -S localhost:9527 -t index.php # -t 为单文件路径，默认为当前目录下的 index.php，如果目录正确可以不加 -t 参数。
```
3. 访问 `localhost:9527` 看到输出说明这个流程没有问题
4. 引入 `composer` 并配置自动加载映射
创建 `composer.json` 文件，使用 `psr-4` 标准配置自动加载,并创建 `app` 目录。

```json
{
  "autoload": {
    "psr-4": {
      "App\\": "app"
    }
  }
}
```
5. 生成自动加载文件,会生成 `vendor` 目录及自动加载文价。[自动加载系列文章](https://www.caoayu.xyz/post/composer-autoload/)
```bash
composer dump-autoload -o # -o 优化参数
```
6. 创建 `bootstrap` 目录，并在此目录下创建 `app.php`

```php
# app.php
<?php

require_once __DIR__ . '/../vendor/autoload.php';
```
7. 创建 `public` 目录，并将入口文件 `index.php` 放入到此目录中，该目录主要存放一些公共的资源，并编辑 `index.php`,实现入口文件的自动加载。

```php
# index.php
<?php
require_once __DIR__ . '/../bootstarp/app.php';
var_dump('caoayu');
```
8. 重启 `web` 服务，因为框架入口文件位置已经更改

```bash
# 在项目根目录下
php -S localhost:9527 -t public 
```
9. 新建一个数据库用来保存数据。

-------
到此为止，准备工作就算完成了。项目会同步到我的 `github`上。
[仓库地址](https://github.com/ayuayue/php-frame)














