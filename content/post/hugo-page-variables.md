---
title: hugo-页面变量
date: 2020-06-03T21:55:59+08:00
tags: [hugo ]
categories: [hugo ]
keywords:
description:
---
#### 页面变量

#####  页面级变量在内容文件的首页中定义，从内容的文件位置派生或从内容主体本身提取. 

1. ` .Aliases ` : 当前页面的别名
2. ` .Content ` : 当前页面的内容
3. ` .Data`   :  当前页面的Data数据
4. ` .Date ` : 与页面相关的日期
5. ` .Descriptin ` : 页面描述
6. ` .Dir ` : 当前内容所在的文件路径
7. ` .Draft ` : 布尔值,是否为草稿
<!--more-->
8. ` .ExpiryData ` : 过期时间
9. ` .File ` : 当前内容相关的数据文件
10.  ` .FuzzyWordCount ` : 内容中的大概字数
11. ` .IsHome ` : 是否在主页
12. ` .IsNode ` : 是否是节点页面,常规页面一直为 `false`
13. ` .IsPage ` : 是否是页面,常规页面一直是 `true`
14. ` .IsTranslated ` : 是否有翻译要显示
15. ` .Keywords ` :  内容的关键词
16. ` .Kind ` : 页面内容,可能是 `page` ,`home` , `section`, `taxonomy`
17. ` .Language ` : 语言
18. ` .Lastmod` : 最后修改的日期
19. ` .Next ` : 下一个
20. ` .NextInSection ` : 下一节
21. ` .Permalink ` : 永久链接
22. ` .Prev ` : 上一个
23. ` .PrevInSection ` : 上一节
24. ` .PublishDate ` : 发表日期
25. ` .ReadingTime ` : 阅读时间
26.  `.Resources ` : 与此页面相关的资源, `css, image ,js `
27.  ` .RelPermalink ` : 这个页面的相对永久链接
28. ` .Site ` : 网站
29. ` .Summary ` : 生成内容摘要
30. ` .Title` : 标题
31. ` .Truncated `: 截断,布尔值  为 `true` 时 显示 `read more`
32. ` .Weight ` : 页面的权重
