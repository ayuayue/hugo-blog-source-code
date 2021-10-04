---
title: "Gitbash 配置"
date: 2021-09-12T10:29:20+08:00
lastmod: 2021-09-12T10:29:20+08:00
draft: false
keywords: []
description: ""
tags: []
categories: []
---

配置好 git-bash 后可以代替绝大多数时候开发的需求.不需要额外折腾 powershell 等工具.大道至简👀

### fish

将 fish 以 子进程的方式在 Git-bash 中运行,可以进行命令提示,比 zsh 配置更简单,并且适用于 git-bash

#### 安装

1. 下载 https://github.com/hongwenjun/tmux_for_windows  其中有 tmux 和 fish
2. 将fish 解压到 git 的安装目录,也就是把 fish 的所有文件,复制到 git 的同级目录下进行扩展
3. 重启 git-bash 使用 fish 命令进入

### 用户环境配置文件

git-bash 的配置文件在 /etc/bash.bashrc 中. 可以先备份一份.

可以在 bash.bashrc 中定义 alias , 以及环境变量. 跟 linux 的 ~/.bashrc 同样的使用方法

```bash
....

alias ll="ls -al"
export HTTP_PROXY="http://127.0.0.1:7890"
export PATH=$PATH
```

修改后记得 刷新文件

```Bash
source /etc/bash.bashrc
```

#### 如何进入 windows 盘

```bash
cd /c/
cd /d/
cd ~
```
