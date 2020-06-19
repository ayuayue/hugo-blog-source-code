---
title: 自动部署hexo到自己的服务器并使用webhooks
date: 2020-05-10T23:41:50+08:00
tags: [hexo]
categories: [hexo]
---

​	前面使用了`webhooks`,以及`travisci`和自己的服务器,但是现在我的`github`有两个分支,当提交到除`master`分支以外的分支时也会发起回调,而且使用了`travis`,导致,刚刚手动`push`完成`travis`又自动`push`了一次,两次的时间间隔很短,而阿里云的服务器从`github clone or pull`等操作有时十分缓慢,这就导致一个回调还没完成,另一个回调就开始了,会出现最终的版本与远程仓库不一致的情况.

<!--more-->

所以我们设置`coding`的`master`分支为回调分支,不在使用`github webhooks`,速度也很快,而且也只有一个分支,不用担心频繁触发导致的问题,`coding`的配置根`github`大同小异,在项目设置里的开发者设置里开启`webhook`,回调开启后发起的请求还是在自己的服务器上处理,宝塔的`hooks`代码也可以响应的根据自己的情况自动更改.

最后测试通过
