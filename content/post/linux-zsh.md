---
title: linux 安装并配置zsh
date: 2020-06-21T10:24:08+08:00
tags: [zsh,linux]
categories: [linux]
---



#### linux下zsh的安装及配置

安装

>
>
>sudo apt-get install -y zsh

安装 oh-my-zsh 三种方式

>
>
>sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
>
>sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
>
>wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O - | sh
>

安装完成 oh-my-zsh 后会自动提示你是否更改当前的shell，选择y即可更改

配置文件在 ~/.vimrc 中，这里可以配置一些插件和主题，以及环境变量



>ZSH_THEME="agnoster"  	//主题配置
>
>plugins=(
>	git
>	zsh-autosuggestions
>	zsh-syntax-highlighting
>)	 //插件配置，常用插件，git分支显示，关键字高亮，自动建议
>
>plugins=(
>	git
>	zsh-autosuggestions
>	zsh-syntax-highlighting
>)
>
>alias mkdir='sudo mkdir' 	//配置命令别名
>
>
> export GOPROXY=https://goproxy.cn
> export PATH=$PATH:$MYVIMRC:$GOPROY 	//配置环境变量
>
>

配置更改后需要重载才能生效

> source ~/.zshrc

