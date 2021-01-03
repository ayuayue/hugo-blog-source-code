---
title: "Windows 查找被占用端口并结束程序"
date: 2020-09-09T23:06:26+08:00
lastmod: 2020-09-09T23:06:26+08:00
draft: false
keywords: [Windows ]
description: "Windows "
tags: [Windows]
categories: [Windows]
---

![image-20200909231107569](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200909231116.png)

### `Windows!!!` 

常常由于开启某项服务而端口已经被其他服务或者同服务的应用所占用,导致我们无法再成功开启一个应用服务.在`linux`中对进程的查找以及"杀掉",都是比较容易记忆的操作.

可以使用 `lsof -i:port` 或使用 `ps` 与 管道符 `| ` 组合使用

----

下面介绍在`Windows`中进行进程的查找及`kill`

1. 通过端口号查找进程号

   ```powershell
   netstat -ano | findstr "1080"
   ```

   ![image-20200909232441215](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200909233509.png)

2. 通过进程号查询程序信息

   ```powershell
   tasklist | findstr 5108
   ```

   ![image-20200909232540365](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200909233520.png)

3. `kill`掉进程

   ```powershell
   taskkill /f /t /im nvcontainer.exe
   ```

   ![image-20200909232633724](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200909233528.png)

----

对比:

`linux`  通过`  | grep`进行查找,`windows`使用`| findstr `进行查找