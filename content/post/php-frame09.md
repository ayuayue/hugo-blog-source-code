---
title: "制作一个PHP简易框架（九）-- ORM"
date: 2021-02-25T18:01:32+08:00
lastmod: 2021-02-25T18:01:32+08:00
draft: false
keywords: [制作一个PHP简易框架]
description: "制作一个PHP简易框架"
tags: [php]
categories: [php]
---

![code](https://images.pexels.com/photos/270348/pexels-photo-270348.jpeg?auto=compress&cs=tinysrgb&dpr=2&w=500)

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

从现在开始进行对数据库的交互，在大部分框架中，都不会直接使用 `sql` 来直接进行数据库操作，但是也都保留了这种原始的方式，基本都是使用 `ORM` 的方式来进行操作，这可以增加项目的兼容性，即使更换了数据库版本或者使用了新的数据库，大部分的操作还是不变的。

### 安装

这次使用 `illuminate/database` 这个 `orm` ,[项目地址](https://packagist.org/packages/illuminate/database)。这个开发包也是 `laravel` 框架使用的 `orm`

```bash
composer require "illuminate/database"
composer require "illuminate/events"
```

### 使用

首先创建 `orm` 的服务提供者,然后注册到容器中,使用容器来管理此服务.

```php
# app/Providers/DatabaseServiceProvider.php
<?php

namespace App\Providers;

class DatabaseServiceProvider extends AbstractServiceProvider
{
    public $provides = [
        'db'
    ];

    public function register()
    {
        $container = $this->getContainer();
        $container->add('db', $this->illuminate()); # 方便更换orm
    }

    private function illuminate()
    {
        $database = config('database.mysql');
        $capsule = new Capsule();
        $capsule->setEventDispatcher(new Dispatcher(new Container));
        $capsule->addConnection($database);
        $capsule->setAsGlobal();
        $capsule->bootEloquent();
        return $capsule;
    }
    
}
```

在配置文件中加入该服务

```php
# config/app.php
<?php

return [
    'name' => env('APP_NAME','XXXX'),
    'test' => 'app',
    'debug' => env('APP_DEBUG',false),
    'providers' => [
        App\Providers\AppServiceProvider::class,
        App\Providers\ViewServiceProvider::class,
        App\Providers\DatabaseServiceProvider::class,
    ]
];
```

配置数据库信息到 `env` 文件中,并使用 `config` 来调用 `env` 中配置信息.

```php
# config/database.php
<?php
return [
    'database' => [
        'mysql' => [
            'driver' => env("DB_CONNECTION",'mysql'),
            'host' => env("DB_HOST",'127.0.0.1'),
            'database' => env('DB_DATABASE'),
            'username' => env('DB_USERNAME'),
            'password' => env('DB_PASSWORD'),
            'charset' => 'utf8',
            'collation' => 'utf8_unicode_ci'
        ]
    ]
];
```

`env` 文件中加入配置信息

```php
# .env
APP_DEBUG=true
APP_CACHE=false
APP_NAME=caoayu

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=php-frame
DB_USERNAME=root
DB_PASSWORD=root

```

#### 创建模型 `Model`

首先创建数据库,并插入一条语句。语句如下

```sql
create table if not exists `users` ( `id` int unsigned auto_increment,`email` varchar(50) not null,`name` varchar(50) not null,`password` varchar(64) not null,primary key (`id`)) engine=innodb default charset=utf8;
```

使用 `schema` 创建表。

```php
# app\Models\Tables.php
<?php

namespace App\Models;

class Tables
{
    public function createUser()
    {
        app('db')::schema()->create('users',function ($table){
            $table->increments('id');
            $table->string('name', 50)->nullable($value = false);
            $table->string('email',50)->nullable($value = false);
            $table->string('password',50)->nullable($value = false);
        });
    }
}
```

生成

```php
# app\Controllers\HomeController.php

use App\Models\Table;

$table = new Table;
$table->createUser();
```

#### 测试查询构造器

```php
# app\Controllers\HomeController.php

$user = app('db')::table('users')->insert([
    'name' => 'zs',
    'email' => '123',
    'password' => '123456'
]);
dump(app('db')::table('users')->get());
```

#### 创建助手函数

```php
# helpers.php
if (!function_exists('db')) {
    function db($name)
    {
        return app('db')::table($name);
    }
}
```

简化后的使用方式

```php
db('users')->get();
db('users')->find(1);
db('users')->insert([
    'key' => 'value',
]);
```



