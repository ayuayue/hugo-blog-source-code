---
title: "Chrome 小技巧"
date: 2020-09-15T23:49:15+08:00
lastmod: 2020-09-15T23:49:15+08:00
draft: false
keywords: [Chrome 小技巧]
description: "Chrome 小技巧"
tags: [Chrome 小技巧]
categories: [Chrome 小技巧]
---

### 记录log中的值到一个临时变量中

通常我们在`chrome`的`devtools`的工具中使用`console.log`来打印一些值,有时候是一个对象.但是我们想对这个值进行一个后续的操作,比如`json`转字符串,或者字符串的筛选操作,我们可以使用下面的操作自动创建一个临时变量进行保存

选中log输出的值,鼠标右键,保存为全局变量.自动会赋值给一个`tmp`的变量,序号是变量的个数.然后我们就可以使用`tmp`变量来代替log中的值来进行一些操作了.

![image-20200915235620134](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200916224411.png)

### 使用快捷键

`ctrl + L ` : 可以对控制台进行清屏

鼠标聚焦到浏览器中,使用 `ctrl + L`可以聚焦到`url`栏

`alt + enter`  : 新窗口打开当前标签页,前提是已经聚焦到了`url`上,可以配合 `ctrl +L alt + enter`使用

`ctrl + shift + c`   : 呼出选择`dom`元素的工具

`ctrl + shift + m` :  切换设备类型

` esc` : 在控制台中,使用 `esc`可以再打开一个控制台,再次使用则关闭

### 复制控制台的一些内容到系统粘贴板

使用 `copy` 复制某个对象或对象的某些元素到粘贴板

![image-20200916000607058](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200916224358.png)

```json
{
  "name": "lili",
  "age": 18
}
```

### 模拟各种网络条件

在`devtools`的网络工具中,提供了几种默认的上网方式

1. 在线. 2. 离线. 3. 3G快速. 4. 3G慢速. 自定义

   ![image-20200916001600652](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200916224350.png)

### 打印输出为表格形式

```json
console.table([{name:"lili",age:18},{name:"lisa",age:19}])
```

![image-20200916001851890](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200916224343.png)

### 引用所选dom节点

当我们使用`devtools`中的工具选中了一个页面元素后,当前元素对象会被赋值为 `$0`,我们可以使用`$0`来获取`dom`的一些属性

![image-20200916224327136](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200916224328.png)

我的博客即将同步至腾讯云+社区，邀请大家一同入驻：https://cloud.tencent.com/developer/support-plan?invite_code=rzt19gu8our3