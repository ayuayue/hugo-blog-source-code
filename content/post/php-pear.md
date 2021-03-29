---
title: "PHP 扩展管理"
date: 2021-03-29T17:49:22+08:00
lastmod: 2021-03-29T17:49:22+08:00
draft: false
keywords: [PHP 扩展管理]
description: "PHP 扩展管理"
tags: [php]
categories: [php]
---

### 扩展安装方式

一般为 PHP 安装扩展有很多方式，比如直接编译安装，或者下载编译好的扩展库。但是还可以使用一些扩展管理的工具来管理，比如 pear，pecl 。

#### PEAR  --  PHP Extension and Application Repository。

PEAR 是 PHP 扩展和应用程序存储库，它具有用 php 编写的库和代码。您只需要下载，安装并包含在代码中即可。

#### **PECL** stands for **PHP Extension Community Library**

PECL 是 PHP 扩展社区库，它具有用 c 编写的扩展，可以将其加载到 PHP 中以提供其他功能，需要具有管理员权限，C 编译器和关联的工具链才能安装这些扩展。

他们是相似的，但又是如此不同

### 安装 PEAR

安装 PEAR 后，PECL 也会一同安装到系统中，但是使用 PECL 安装扩展需要 C 编译器及 phpize 等依赖，如果没有需要安装。

如果是使用 apt 的包管理器安装的 PHP ，一般是没有 phpize 的，需要安装 php-dev 包。

```bash
sudo apt install php7.3-dev # 根据自己 php 版本安装
```

下载 PEAR 安装包

```bash
wget https://pear.php.net/go-pear.phar
```

安装

```bash
php go-pear.phar
```

配置环境变量

默认安装位置在 ` ~/pear` 目录中。`bin` 目录下是可执行文件，包括 `pear、pecl、peardev` 。

将 `bin` 目录加入到环境变量中。

```bash
vim ~/.zshrc

export PEAR=/home/caoyu/pear/bin
export PATH=$PATH:$PEAR

# exit
source ~/.zshrc
```

配置加载 pear 包路径

修改 php.ini 文件，在文件最后加入下面内容

```ini
;***** Added by go-pear
include_path=".:/home/caoayu/pear/share/pear" # pear config-get php_dor 命令输出的路径
;*****
```



### 检测安装成功与否

1.  验证命令行工具

    1.  执行 `pear、pecl` 命令，输出参数信息说明安装成功。

2.  验证包含路径

    1.  首先检查 PEAR 在哪里安装 php 文件

        ```bash
        pear config-get php_dor
        ```

    2.  查找 php 安装所使用的配置文件

        ```bash
        php --ini
        ```

        如果要查看 php 在 web 服务器上使用的是哪个 php.ini ，请创建一个只有 `<?php phpinfo(); ?>  ` 作为内容的文件，并保存为 `check_php.php` ,文件中，使用 `php -S localhost` 命令开启 php 内置服务器，浏览网页 `http://localhost/check_php.php` 查看输出。

    3.  检查 php 命令行上的 include_path 设置

        ```bash
        php -c /etc/php/7.3/cli/php.ini -r 'echo get_include_path()."\n";'
        ```

        输出上面配置的 `.:/home/caoayu/pear/share/pear` 说明成功

    4.  尝试编写一个文件测试扩展库的使用

        1.  创建 php 文件 check_ext.php ，内容如下

            ```php
            <?php
            require_once 'System.php';
            var_dump(class_exists('System', false));
            ?>
            ```

        2.  验证

            ```bash
            php check——ext.php # 输出 true 说明扩展使用成功
            ```

            扩展文件位置如下图

            ![image-20210329183750409](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210329183750409.png)

### 后续

此时就安装配置完成了，可以使用 pecl 来安装或搜索一些扩展了。

```bash
pecl search swoole
```

![image-20210329183951544](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210329183951544.png)

使用 `install` 命令即可安装

```bash
sudo pecl install swoole
```

接下来 pecl 会使用 phpize 进行编译扩展，并将扩展放到 php 配置的扩展目录中。

查看 php 扩展目录

```bash
 php -ini | grep extension
```

最后修改 php.ini 配置文件，将扩展开启。

```php
extension=swoole
```

查看 php 扩展

```bash
php -m
```



注意 cli 跟 fpm 的配置可能不同

初始化的 cli 的配置文件与 fpm 配置文件是一致的，但是如果修改过 fpm 下的配置后需要重启 fpm 生效。

```bash
service php7.3-fpm reload # 或者 systemctl restart php7.3-fpm.service
```



