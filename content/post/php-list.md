---
title: php List()函数及json_encode时无法转为数组的问题
date: 2020-05-25T18:34:42+08:00
tags: [php]
categories: [php]
---

​	

##### `list` 函数实现 `PHP`中返回多个值(list并不是一个真正的函数,而是一种语言结构)

在众多的编程语言中,有很多都可以在函数中返回多个值,如 `java,golang`, 但是`php`却是不支持,虽然在 7.0 版本之后可以设置返回值的类型,但还是无法返回多个值,估计后面 `php` 的升级中会考虑这个问题. 既然无法原生支持,那我们就自己实现,`php`内置了大量的函数可以使用,这也是`php`开发速度快的一个原因.

<!--more-->

##### 实现方式 : 以数组的方式将多个返回值`push`进去,使用`list`函数接受变量并自动赋值

先来介绍下 `list()` 函数: [官方介绍]( https://www.php.net/manual/zh/function.list.php )

list() 方法可以在一次操作中为 一组变量赋值.

在`php7`版本之前和之后会有一些差距,这里主要说7.0之后,关于差别官方介绍中有说.

在 7.0 之后的版本并不仅仅局限于 可以将一个数组以索引数组的方式给数组中的变量赋值,更可以使用关联数组的方式赋值给变量

**注意,list 方法对字符串不起作用**

例子: 关联数组(索引数组不需要键)

```php
function example(){
    return [
        "drink" => "coffer",
        "color" => "brown",
        "price" => 20
    ]; 
}
function recieve(){
    $result = example(); //接受返回值
    list(
    	"drink" => $Ldrink,
        "color" => $Lcolor,
        "price" => $Lprice
        
    )= $result;
    echo $Ldrink . PHP_EOL,$Lcolor. PHP_EOL,$Lprice. PHP_EOL;
}
//打印结果
coffer
brown
20
//键为返回值里的变量,值为生成的新值.
```

*同样的也可以使用 `list()` 方法结合 `each()` 方法 实现` foreach` 的功能*

##### `json_encode` 返回时的问题

当需要返回`json`数据时, 我们通常会使用 `json_encode` 将对象或数组转为 `json`对象或数组

```
json_encode($obj); //转为json对象
json_encode($obj, true); // 转为json 数组
```

##### 问题所在

当我们将数组转为`json`数组时,如果原数组的索引不连续或者直接就是一个关联数组,则不会转为数组,而是被转为了对象

##### 解决方法

解决方法有多种,原理上就是把 需要转为`json` 数组的 原数组 重构为一个索引连续的索引数组.

推荐方法

1. 可以直接将 原数组的变量 `push` 到 `[]`中
2. 使用 `array_value()` 函数, 创建一个只取原数组值,索引为连续索引的数组
3. 使用 `sort() `函数 ,同样返回 索引连续的数组
