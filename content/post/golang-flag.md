---
title: "Golang Flag包的参数格式问题"
date: 2020-07-04T21:15:52+08:00
draft: false
tags: [golang]
categories: [golang]
---

### 问题

使用`golang flag`包开发命令行程序或者读取命令行参数时,有时候参数为`bool`值会读取不到后面的参数

### 原因

`golang flag` 包对于命令行的参数格式有要求,如下

```GO
-flag 
-flag=x
-flag x //只支持非bool类型
```

所以在使用`bool`值的参数时,导致读取参数错误后面的参数就读取不出来

### 解决方案

1. 将`bool`值的参数写到最后面

   ![image-20200704212200790](http://qiniu.caoayu.xyz/img/20200704212202.png)

2. 改为`-s=true`方式

   ![image-20200704212222941](http://qiniu.caoayu.xyz/img/20200704212224.png)

