---
title: 将hexo文章头转为hugo格式
date: 2020-06-07T20:34:48+08:00
tags: [hexo]
categories: [hexo]
---
由于`hexo`的语言限制,在博客文章数量达到一定的数量后,生成静态内容的速度比较的缓慢,最近打算转到`hugo`的框架上,`hugo`使用的是`golang`编写的,号称是世界上最快的静态页面生成器

但是由于 `golang` 语言的一些语法及特性,在一些变量的表现上跟`hexo`有所不同,`markdown`文件虽然可以复用,但是文章内容的`front matter`,即文章头定义的那些标签会有所不同,首先是时间的格式会不同

`go`默认的格式是`xxxx-xx-xxTxx-xx-xx+08:00` [`+08:00`代表的是时区.还有就是`tags,categories`等标签的定义,在`go`里定义多个要用数组,长度不确定需要使用切片,切片的形式为 `[]type{}`,所以在`hugo`中定义多个`tags`跟`hexo`会有不同]

hexo中

    [tags: ]
        - hexo
        - blog

hugo 中

    [tags: [hexo,blog]]

<!--more-->

为了解决`markdown`文件的复用问题,我们使用`golang`写一个脚本,将`hexo`文章的头标签更改为`hugo`需要的格式

思路:
    1. 获取目录下的所有`markdown`类型的文件
 [   2. 读取内容,并查找`tags,categories`标签]
    3. 进行替换
    4. 将替换的内容重新写进文件


项目地址: [hexo2hugo](https://github.com/ayuayue/hexo2hugo)


说明:

 [   当前只实现了对 `tags,categories`单标签单属性的替换,如有其他需求可以自行克隆增加]

待优化项(功能):

1. 命令行参数获取,通过参数里的路径获取文件列表并更改

2. 增加其他更多的标签

3. 增加对于标签多属性的支持

4. 将修改后的文件生成到一个单独的文件夹而不是覆盖原文件

  

