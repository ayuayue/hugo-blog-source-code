---
title: "composer (一) -- 依赖管理"
date: 2021-01-02T22:18:17+08:00
lastmod: 2021-01-02T22:18:17+08:00
draft: false
keywords: [Composer]
description: "Composer 依赖管理"
tags: [Composer]
categories: [Composer]
---

## **composer** 

`composer` 是 `PHP` 用来管理依赖（`dependency`）关系的工具。你可以在自己的项目中声明所依赖的外部工具库（`libraries`），`Composer` 会帮你安装这些依赖的库文件。类似于 `go` 的 `go mod`。

### **composer init -- 初始化**
`composer init ` : 初始化一个`composer`项目,根据提示进行创建包名及依赖信息。一路回车就可以创建一个没有什么依赖的 `composer.json` 文件。
```json
# composer.json
{
    "name": "caoayu/composer",
    "description": "composer",
    "authors": [
        {
            "name": "ayuayue",
            "email": "1401262639@qq.com"
        }
    ],
    "require": {}
}
```
### **composer required -- 安装依赖**
[php依赖包仓库](https://packagist.org/) ,一般找第三方扩展包都在这里，这里以 `illuminate/contracts` 包为例。[illuminate/contracts 包地址](https://packagist.org/packages/illuminate/contracts)

![简单信息介绍](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/简单信息介绍.png)

根据所需运行环境选择一个最适合自己的版本安装，我这里是 `php7.3` 选择`v8.23.1`进行安装。通过包介绍中的命令进行安装 `composer require illuminate/contracts`
通过输出可以看到，他也会自动去安装一些自己依赖的包，最后会重新生成自动加载文件。现在查看 `composer` 文件的更改。
```json
{
    "name": "caoayu/composer",
    "description": "composer",
    "authors": [
        {
            "name": "ayuayue",
            "email": "1401262639@qq.com"
        }
    ],
    "require": {
        "illuminate/contracts": "^8.23"
    }
}
```
并且生成了一个 `composer.lock` 文件及 `vender` 目录。打开 `vender` 目录，其中是项目依赖的包及包依赖的其他包的源码，还有一个自动加载文件 -- `autoload.php`


### **install and update 安装更新项目所有依赖**
并不是每个项目都是新创建的项目，在平常的开发中，往往会更换机器或者对项目进行部署，通常都是使用代码版本管理工具，如 `git、svn` 等。通过对项目进行拉取，往往都含有一个`composer.json composer.lock` 文件，这是为了保证每个开发人员都有同样的环境及依赖，更加的规范与合理。
对于一个刚刚拉取下来的项目，一般会把 `verder` 目录设置为忽略上传的目录，所以我们需要安装所有的依赖，由于 `composer.json` 文件已经包含了所有依赖的信息，我们再使用 `composer require` 的方式是不行的。

#### **安装所有依赖**
 `composer install` 

存在 `composer.lock` 文件，安装依赖会以这个文件为依据，如果不存在，以 `composer.json` 文件为依据。

#### **更新依赖版本**
`composer update` 会根据当前 `composer.json` 文件来更新依赖，并生成新的 `composer.lock` 文件。通常用来更新依赖包的版本或增加了新的依赖时使用

#### **删除依赖**

只需要修改 `composer.json` 文件的 `require` 中的包键值对，再进行 `composer update`即可删除依赖。

#### **composer.lock 文件**
`composer.lock` 是通过 `composer` 进行安装或更新依赖后生成的一个文件，会记录上次操作的记录，如果改动了 `composer.json` 文件直接安装并不会生效，还会根据`composer.lock` 文件来安装，所以在第一次执行 `composer install` 后，如果更改了 `composer.json` 文件，需要使用 `composer update` 来更新 `composer.lock` 文件，才可以达到更新包依赖的目的

## **开发环境和线上环境**

### **开发模式才使用依赖**

有些依赖包我们并不是任何时候都要使用，比如 测试包，debug的包，在生产环境是不需要安装的，并且 debug包有时候还会有些漏洞，最近 `laravel` 就 爆出一个 `debug` 包的漏洞，所以尽量对包进行区分。

当我们确定了包的使用范围时：可以使用 --dev 来标识为开发模式专用。

```bash
composer require --dev phpunit/phpunit 
```

此时，在 `composer.json` 中会多出这样的内容

```json
	"require-dev": {
		"phpunit/phpunit": "^9.5"
	}
```

`require-dev` 就是一个标识，用来标记该包在 `dev` 模式下使用。

仅仅这样是无法进行区分的，还需要配合 `--no-dev` 来使用

在线上的项目中进行更新依赖

```bash
composer install --no-dev 
# or
composer update --no-dev
```

这样就会忽略 `require-dev` 中的包，从而达到分离不同环境的作用。

### **使用composer创建项目**

如果没有配置 `composer` 的国内源，安装依赖会比较慢，[配置阿里云源](https://www.caoayu.xyz/post/composer-source/)

使用 `composer init` 只会初始化出一个 `composer.json` 文件，并不会创建文件及项目。使用`composer` 也可以直接创建一个项目，如 `laravel tp` 等框架现在大部分都支持。

`composer create-project -h` 可以输出所有的创建项目参数及帮助信息

以 `laravel` 项目为例：

```bash
# 最基础的参数
composer create-project laravel/laravel blog # blog 为项目名，默认创建最新的laravel框架
# 指定版本
composer create-project --prefer-dist laravel/laravel blog "5.5.*" #将会创建5.5最新的laravel框架

# --prefer-dist 首先下载压缩包并解压到缓存到本地，下次安装会首先从本地缓存安装，速度较快
# --prefer-source 直接下载源码包括项目的版本管理信息，如 .git 目录跟分支信息。比较慢，适合直接修改源码时使用。
```

## **版本号**

常见的版本号通常有：`^ ~` 。

>   ^  :  代表当前大版本的最新版本。如 `^5.2.0` , 则会下载 `5.7.27`, 也就是`5`版本最新的一个版本。
>
>   ~ :  代表大于当前版本号最后的那位。 如 `~5.2.1` ,则会下载 `5.2.12`，不会超过 `5.2.2`