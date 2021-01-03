---
title: "使用Cygwin在windows下运行linux的一些工具"
date: 2020-11-07T14:37:16+08:00
lastmod: 2020-11-07T14:37:16+08:00
draft: false
keywords: [cygwin,windows]
description: "使用Cygwin在windows下运行linux"
tags: [windows]
categories: [windows]
---

### windows 终端

对于开发者而言,`windwos`下的终端实在是不如`linux,macos`下的效率更高,速度更快.虽然`Windows`下也有一些不错的第三方终端.如 `cmder,windows terminal,powshell`, 甚至还有子系统的终端.但是对于终端命令的提示还是不尽人意.

对于子系统`Ubuntu`,家庭版的功能是阉割了的.暂时也无法升级到2.0,而且子系统我们一般用来跑一些项目.配置太多太乱会让本就不稳定的系统更加危险.对于子系统的维护更是我们不想去经常自己操作.所以我们就可以使用 `cygwin`来跑一些`Linux`下的小工具来提升我们的效率.如 `fish,gcc,python`

### 安装`cygwin64`

进入官网,找到对应版本进行下载 [官网](http://www.cygwin.com/)

1. install  from internet  从网络上安装
2. 配置安装的目录以及使用的用户.默认即可
3. 配置本地包目录.默认即可
4. 代理.默认即可
5. use URL 使用镜像.可以选择阿里云的那个 `https://mirrors.aliyu.com`

安装`linux`工具跟库

1. view 根据分类查看目录.一般使用 `category`
2. 搜索我们需要安装的软件或者库,比如 `fish`
   1. ![image-20201107152214882](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20201107152223.png)
   2. 下一步就可以安装了
   3. 安装完成后会生成一个`cygwin`的工具.打开就是一个`linux`的系统命令终端,可以执行一些`Linux`命令.
   4. 如果命令报错可能是没有依赖.比如`python`.那就重新安装一次`cygwin`,再选择包的时候添加进`python`,原来的不用改变.即可

**注意事项** : 如果`windows`中安装了软件并设置了环境变量.会跟`cygwin`里的命令冲突.优先使用`windows`里的软件执行.所以如果冲突.我们可以把`windows`下的改下名字.或者取消环境变量(如果不用的话).

### 安装目录

安装后,安装目录里就是一个`linux`的目录解构了.我们通过`cygwin`安装的应用都在 `bin`目录下.所以如果想再`windows`中使用这些工具.我们可以给这个 `bin` 目录添加一个环境变量.这样就可以在外部调用了.

### `fish`替换其他`shell`

1. `vscode` 中 : 使用 `ctrl+,`打开设置.右上角找到设置文件`setting.json`打开.将 `temianl`的终端路径改为 `cygwin/bin/fish.exe` 即可启动一个`fsih`当作终端

2. `jetbrains` ide : 在设置中找到 `terminal`既可以通过目录查找到`fish`的路径.应用即可

3. `Windows terminal`中 : 打开设置文件,配置一个新的终端列表.设置为默认即可

   ```bash
   "defaultProfile": "{1caa0dad-35be-5f56-a8ff-afceeeaa6101}",
   {
           // Make changes here to the cmd.exe profile.
           "guid": "{1caa0dad-35be-5f56-a8ff-afceeeaa6101}",
           "name": "fish",
           "commandline": "fish",
           "hidden": false
         },
   ```

   