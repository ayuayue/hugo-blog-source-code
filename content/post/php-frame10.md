---
title: "制作一个PHP简易框架（十）-- Session and Cookie"
date: 2021-02-28T14:15:18+08:00
lastmod: 2021-02-28T14:15:18+08:00
draft: false
keywords: [制作一个PHP简易框架]
description: "制作一个PHP简易框架"
tags: [php]
categories: [php]
---

![吴孟达](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fimg.mp.sohu.com%2Fupload%2F20170527%2Fb972760b8c124fc68fa076d273eb16a1_th.png&refer=http%3A%2F%2Fimg.mp.sohu.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1617084896&t=5e4a257b09138bd85912227f69b5f7f7)

### 状态管理

对于一个 `web` 系统来说，一个用户的状态管理基本是必须的，但由于 `http` 连接是无法保存状态的，所以 `session , cookie` 便被设计了出来。

`php` 默认的 `session` 存储方式是 文件存储。但对于现代的大型应用，一般都是将 `session` 存入到 `db ,redis` 中进行持久化。

### 项目开启 `Session`

```php
# bootstrap/app.php
session_save_path(__DIR__ . '../storage/sessions');
session_start();
```

#### 测试是否开启成功。

```php
# app\Controller\HomeController.php
public function index($request)
    {
        $_SESSION['name'] = config('name');
        dump($_SESSION['name']);
    }
```

如果获取到 `session` 说明开启成功。

#### 定义 `session` 接口

`sessin` 的存储方式因项目需求可能会改变，所以要定义一个统一的接口，用来规范每个存储类需要完成的操作。

```php
# app\Service\Session\SessionInterface.php
<?php

namespace App\Service;

interface SessionInterface
{
    public function has($key);

    public function set($key,$value);

    public function get($key,$value);

    public function del(...$key);
}
```

定义文件存储方式，实现接口

```php
# app\Serice\Session\FileSession.php
<?php


namespace App\Service\Session;

class FileSession implements SessionInterface
{

    public function has($key)
    {
        return isset($_SESSION[$key]) && !empty($key);
    }

    public function set($key, $value = null)
    {
        if(is_array($key)){
            foreach ($key as $k=>$v){
                $_SESSION[$k] = $v;
            }
        }else{
            $_SESSION[$key] = $value;
        }
    }

    public function get($key, $default = null)
    {
        if ($this->has($key)){
            return $_SESSION[$key];
        }
        if ($default != null){
            $_SESSION[$key] = $default;
            return $default;
        }else{
            return $default;
        }
    }

    public function del(...$key)
    {
        foreach($key as $v){
             unset($_SESSION[$v])
        }
    }
}
```

### 创建 `Session` 服务提供者

注册到容器中，使用容器统一管理

```php
# app\Providers\SessionServiceProvider.php
<?php


namespace App\Providers;


use App\Service\Session\FileSession;
use League\Container\ServiceProvider\AbstractServiceProvider;

class SessionServiceProvider extends AbstractServiceProvider
{
    protected $provides = [
        'session'
    ];
    public function register()
    {
        $container = $this->getContainer();
        $container->add('session',function(){
            return new FileSession();
        });
    }
}
```

#### 注册到容器中

```php
# config/app.php
'providers' => [
        App\Providers\AppServiceProvider::class,
        App\Providers\ViewServiceProvider::class,
        App\Providers\DatabaseServiceProvider::class,
        App\Providers\SessionServiceProvider::class,
    ]
```

#### 调用测试

```php
# app\Controller\HomeController.php
class HomeController
{
    public function index($request)
    {
        dump(app('session')->get('name'));
        return view('index.twig', compact('user'));
    }
}
```

#### 定义助手函数

```php
# helpers.php
if (!function_exists('session')) {
    function session()
    {
        return app('session');
    }

```

#### 测试

```php
# app\Controllers\HomeController.php
class HomeController
{
    public function index($request)
    {
        dump(session()->get('name'));
        session()->del('name');
        return view('index.twig', compact('user'));
    }
}
```

### `Cookie` 服务

有了 `session` ，就少不了 `cookie` ，下面来封装 `cookie` 的方法。

```php
# app\Service\Cookie\Cookie.php
<?php


namespace App\Service\Cookie;


class Cookie
{
    protected $path = '/';
    protected $domain;
    protected $secure = false;
    protected $httponly = true;
    public function set($key,$value = null,$minutes = 120)
    {
        $expires = time() + $minutes *60;
        if (is_array($key)){
            foreach ($key as $k => $v){
                setcookie($k,$v,$expires,$this->path,$this->domain,$this->secure,$this->httponly);
            }
        }else{
            setcookie($key,$value,$expires,$this->path,$this->domain,$this->secure,$this->httponly);
        }
    }
    public function has($key)
    {
        return isset($_COOKIE[$key]) && !empty($key);
    }

    public function get($key,$default = null)
    {
        if ($this->has($key)){
            return $_COOKIE[$key];
        }
        if ($default){
            $this->set($key,$default);
            return $default;
        }else{
            return $default;
        }
    }


    public function forever($key,$value)
    {
        $this->set($key,$value,-3600000000);
    }

    public function del(...$key)
    {
        foreach($key as $v){
            $this->set($v);
        }
    }
}
```

#### 创建服务提供者并注册到容器

```php
# app\Providers\CookieServiceProvider.php
<?php


namespace App\Providers;


use App\Service\Cookie\Cookie;
use League\Container\ServiceProvider\AbstractServiceProvider;

class CookieServiceProvider extends AbstractServiceProvider
{
    protected $provides = [
        'cookie'
    ];
    public function register()
    {
        $container = $this->getContainer();
        $container->add('cookie',function(){
           return new Cookie();
        });
    }
}
```

```php
# config/app.php
'providers' => [
        App\Providers\AppServiceProvider::class,
        App\Providers\ViewServiceProvider::class,
        App\Providers\DatabaseServiceProvider::class,
        App\Providers\SessionServiceProvider::class,
        App\Providers\CookieServiceProvider::class,
    ]
```

#### 创建助手函数

```php
# helpers.php
if (!function_exists('cookie')) {
    function cookie()
    {
        return app('cookie');
    }
}

```

#### 测试

```php
# app\Controllers\HomeController.php
class HomeController
{
    public function index($request)
    {
        cookie()->set([
            'name'=>'1',
            'a'=>1
        ]);
        cookie()->del('a','name','age');
        $user = 123;
        return view('index.twig', compact('user'));
    }
}
```

----

系列完结。

后面有时间可能还会增加第二阶段的文章。主要包括

1.  用户
2.  数据验证
3.  异常处理
4.  中间件
5.  网络安全
6.  重定向
7.  CSRF