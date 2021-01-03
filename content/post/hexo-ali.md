---
title: hexo部署到阿里云服务器
date: 2020-05-07T10:49:01+08:00
tags: [hexo]
categories: [hexo]
---
前面几篇文章说过将`hexo`部署到`github`及`coding`,但是使用过程中发现`github`国内访问速度慢,不允许百度爬虫爬取,`coding`的`page`服务也是部署在国外,虽然做了`cdn`速度还不错,但有时会挂掉,时间有短有长,不稳定这一因素也驱使这我放弃这个方式.最终打算部署到自己的服务器上,阿里云还是很稳定的,自己不乱操作也不回出什么问题
<!--more-->
首先我删除了`coding`上的仓库,但是保留了`github`的仓库,为了让服务器可以通过`github pull`获取最新的更改,这样也可以更好的进行版本控制,以及跨平台,跨设备的支持,后面使用了`gitalk`做为评论系统,会用到`token`,又不需要再使用`travisCI`的服务,为了安全,就把仓库设置为私人仓库.

修改配置文件,将域名改为服务器绑定的域名,或者使用`nginx`开启方向代理,将 4000 端口映射到 外部80,也可以直接`hexo s -p 80`直接指定.
去除根下的 `.travis.yml`配置文件,并将`/.config.yml`中的 `deploy` 设置为空,不需要部署到远程仓库了

为了使用起来更加方便以达到自动化的效果,可以使用`linux`的计划任务,或者使用宝塔面板的可视化操作,新建`shell`脚本,设置运行时间,如:` cd /root/git/resp_name/ && git pull`,`cd /root/git/resp_name/ && npm run deploy`

`npm run deploy`命令的由来 : 在`package.json` 中定义 
```javascript
	"scripts": {
	    "deploy": "hexo clean && hexo generate && hexo s"
	  },
```
也可以根据自己的情况自定义脚本

到这里就差不多完成了将`hexo`转到阿里云的操作了
