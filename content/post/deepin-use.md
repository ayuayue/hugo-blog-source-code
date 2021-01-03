---
title: deepin使用
date: 2020-04-25T18:49:45+08:00
tags: [deepin]
categories: [linux]
---


### 安装

下载官方镜像,使用官方镜像软件或者使用rufus,以dd的方式刻录,分区,主要分/,以及efi,其他分区可自定义,如 `home` ,`opt`,和交换分区,设置时区,键盘,用户,密码,等待安装

<!--more-->

### 初始化

进入设置,网络帐号,进行登陆或注册,同步设置,设置更新,切换软件源

```
sudo vi /etc/apt/source.list
# 加入一下内容
deb [by-hash=force] https://mirrors.aliyun.com/deepin/ panda main contrib non-free

```
更新软件仓库及安装的软件

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get full-upgrade
```

移除软件

```
sudo apt remove
sudo apt audoremove
//重新安装
sudo apt reinstall
```

搜索软件

```
apt search
```

修复软件安装依赖问题

```
sudo apt install --fix-borken
```

### 常用软件安装(自带应用商店)

1.  wine QQ   最新版有截图,贴图,长截图,录屏,识图功能,不需要使用其他工具,快捷键可能会冲突,进设置里更改一下,或者看最下面重新定义的快捷键
2.   wine 微信
3.  网易云音乐
4.  vscode
5.  typora
6.  finalshell
7.  有道云笔记网页版
8.  libreoffice
9.  git

### 输入法配置

使用linux自带的sunpinyin输入法,也有谷歌输入法和搜狗可以代替,配置上一页下一页的按键以及候选词的个数

### git配置
```
git config --global user.name ""
git config --global user.email ""
git config --list

//生成密钥
ssh-keygen 一直回车,
查看公钥 cat ~/.ssh/id_rsa.pub
```
### 安装zsh
```
sudo apt install zsh
//查看bash
cat /etc/shells
//切换bash
sudo chsh -s /usr/bin/zsh
//安装oh-my-zsh 
浏览器输入 
https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh
ctrl s 保存
进入目录,增加运行权限,运行即可
