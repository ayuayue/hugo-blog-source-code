---
title: hugo-站点变量
date: 2020-06-02T21:39:13+08:00
tags: [hugo]
categories: [hugo]
keywords: hugo
description:
---

#### 站点变量

##### 在站点的配置中定义了许多（但不是全部）站点范围的变量. 但是，Hugo 提供了许多内置变量，可方便地访问模板中的全局值.

1. `.Site.AllPages` : 获取所有页面 -- 数组
2. `.Site.Author`: 获取配置中定义的 作者名
3. `.Site.BaseUrl` : 获取配置中定义的网站 URL
4. `.Site.BuildDrafts` : 是否使用草稿 -- 布尔值
5. `.Site.Copyright` : 配置中的 版权信息
6. `.Site.Data` : 自定义的在 /data/目录下的数据
7. `.Site.DisqusShortname` : 配置中的段代码名
8. `.Site.GooGleAnalytics` : 配置中定义的 Google 分析码
9. `.Site.Home` : 首页对象
10. `.Site.IsMultiLingual` : 是否有不止一种语言
11. `.Site.IsServer` : 是否在使用内置服务器 -- 布尔值
12. `.Site.LastChange` : 一个字符串,网站最近的更改日期
13. `.Site.Menus` : 站点的所有菜单
14. `.Site.Pages` : 按日期排序的所有`page`的数组
15. `.Site.Sections` : 网站的顶级目录
16. `.Site.Taxonomies` : 整个站点的分类
17. `.Site.Title` : 网站标题
18. `.Site.Params` : 获取配置中的 `params` 下的数据
