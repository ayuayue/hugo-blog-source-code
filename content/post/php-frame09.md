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

从现在开始进行对数据库的交互，在大部分框架中，都不会直接使用 `sql` 来直接进行数据库操作，但是也都保留了这种原始的方式，基本都是使用 `ORM` 的方式来进行操作，这可以增加项目的兼容性，即是更换了数据库版本或者使用了新的数据库，大部分的操作还是不变的。

### 安装

这次使用 `doctrine` 这个 `orm` ,[项目地址](https://www.doctrine-project.org/projects/doctrine-orm/en/2.8/tutorials/getting-started.html#getting-started-with-doctrine)。这个开发包是使用注解的方式来解析类与数据库实体的映射关系的。

```bash
composer require doctrine/orm
```

### 使用

首先创建 `orm` 的服务提供者,然后注册到容器中,使用容器来管理此服务.

```php
# app/Providers/DatabaseServiceProvider.php
<?php

namespace App\Providers;

use Doctrine\ORM\Tools\Setup;
use Doctrine\ORM\EntityManager;
use League\Container\ServiceProvider\AbstractServiceProvider;

class DatabaseServiceProvider extends AbstractServiceProvider
{
    public $provides = [
        'db'
    ];

    public function register()
    {
        $container = $this->getContainer();
        $container->add('db', function () {
            $isDevMode = true;
            $config = Setup::createAnnotationMetadataConfiguration(array(base_path('app')), $isDevMode);
            $conn = config('database.mysql'); 
            return EntityManager::create($conn, $config);
        });
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
            'driver' => 'pdo_mysql',
            'host' => env('DB_HOST', '127.0.0.1'),
            'port' => env('DB_PORT', '3306'),
            'dbname' => env('DB_DATABASE'),
            'user' => env('DB_USERNAME'),
            'password' => env('DB_PASSWORD')
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

```sql
insert into users (name,email,password) values ('zs','123','123');
```

项目中，创建 `Models` 目录，并创建 `user.php` 文件

```php
# app/Models/user.php

```



