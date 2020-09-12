---
title: linux 安装并配置zsh
date: 2020-06-21T10:24:08+08:00
tags: [zsh,linux]
categories: [linux]
---

#### linux下zsh的安装及配置

安装

```bash
sudo apt-get install -y zsh
```

安装 oh-my-zsh 三种方式

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"

wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O - | sh
```

**如果因为网络问题安装失败,可以使用代理,或者直接把那个文件下载到本地,添加权限后执行就好了**

如果都不行可以在这里 [下载install.sh_bak](https://www. caoayu.xyz/file/install.sh_bak),下载完后把`_bak`后缀去掉

安装完成 oh-my-zsh 后会自动提示你是否更改当前的shell，选择y即可更改

配置文件在 ~/.zshrc 中，这里可以配置一些插件和主题，以及环境变量

```bash
ZSH_THEME="agnoster"  	//主题配置
plugins=(
	git
	zsh-autosuggestions
	zsh-syntax-highlighting
)	 
//插件配置，常用插件，git分支显示，关键字高亮，自动建议
source "$ZSH_CUSTOM/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh"
alias s=screenfetch
alias mkdir='sudo mkdir' 	//配置命令别名
export GOPROXY=https://goproxy.cn
export PATH=$PATH:$MYVIMRC:$GOPROY 	//配置环境变量
```

#### 插件的安装

对于上面配置中的两个插件安装

1. 进入 `~/.oh-my-zsh/custom/plugins/

   ```zsh
   cd ~/.oh-my-zsh/custom/plugins
   ```

2. 将对应插件的`git`仓库`clong`下来即可(这里使用了镜像源,git官方站比较慢) [关于github镜像](https://www.caoayu.xyz/post/github-fast/)

   ```zsh
   git clone https://github.com/zsh-users/zsh-autosuggestions.git
   git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
   ```

配置更改后需要重载才能生效

```bash
source ~/.zshrc
```



