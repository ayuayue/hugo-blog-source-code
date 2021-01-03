---
title: 用两个栈实现队列
date: 2020-05-23T18:51:12+08:00
tags: [php]
categories: [剑指Offer-PHP]
---

##### 	题目描述

用连个栈来实现一个队列,完成队列的 `push` 和 `pop` 操作,队列中元素为 `int` 类型

要用栈来实现对列首先要知道 什么是栈 和 队列, 栈和队列在数据结构上都是数组的表现, 只是有不同的特性,对于栈来说,站内的元素先进后出 (FILO),而队列的元素为先进先出 (FIFO)

<!--more-->

##### 开始实现

知道了底层数据结构都是数组那就好办了,`php`中有很多操作数组的函数可以使用

```php
<?php
class quequ {
    public function queue(){
        $stack = [];

        // 获取 10 个随机数，压入栈
        for ($i=0; $i < 10; $i++) {
            $random  = rand(0,100);      // 随机数
            $stack[] = $random;          // 等同于 array_push($stack, $random);
        }

        print_r($stack);                 // 输出数组

        while (!empty($stack)) {
            $pop = array_shift($stack);  // 先入先出，弹出队列首
            echo $pop .PHP_EOL;
        }
    }
}
$quequ = new quequ();
$quequ->queue();
```

结果:

​		![1590232692153](php-offer-01/1590232692153.png)
