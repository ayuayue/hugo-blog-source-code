---
cssclass:
title: git-bash配置
tags: [IT/Bash, IT/Tools]
image-auto-upload: true
date: 2022-10-03 20:47:46
lastmod: 2022-10-25 08:42:48
---
# git-bash配置

配置好 git-bash 后可以代替绝大多数时候开发的需求.不需要额外折腾 powershell 等工具.大道至简👀

### fish

将 fish 以 子进程的方式在 Git-bash 中运行,可以进行命令提示,比 zsh 配置更简单,并且适用于 git-bash

#### 安装

1.  下载 [https://github.com/hongwenjun/tmux_for_windows](https://github.com/hongwenjun/tmux_for_windows) 其中有 tmux 和 fish
2.  将fish 解压到 git 的安装目录,也就是把 fish 的所有文件,复制到 git 的同级目录下进行扩展
3.  重启 git-bash 使用 fish 命令进入

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

## zsh zimfw

https://gitee.com/lvxc/zimfw/tree/master

wsl ubuntu20.04配置

## zsh oh-my-zsh

```text
# install
sh -c " $( curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh ) "

# .bashrc
# Launch Zsh
if [ -t 1 ]; then
exec zsh
fi

# theme
curl -fsSL https://raw.githubusercontent.com/oskarkrawczyk/honukai-iterm/master/honukai.zsh-theme -o ~ /.oh-my-zsh/custom/themes/honukai.zsh-theme
sed -i 's /ZSH_THEME="robbyrussell"/ZSH_THEME=" honukai  " /g'~/.zshrc

安装zsh-autosuggestions插件
git clone https://github.com/zsh-users/zsh-autosuggestions $ZSH/plugins/zsh-autosuggestions
source $ZSH/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
编辑~/.zshrc文件
plugins=( 
    ...
    zsh-autosuggestions
)


安装zsh-syntax-highlighting插件
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH/plugins/zsh-syntax-highlighting
source $ZSH/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
编辑~/.zshrc文件
plugins=( 
    ...
    zsh-syntax-highlighting
)


cd ~/.oh-my-zsh/plugins/zsh-autosuggestions
git checkout tags/v0.6.4 -b v0.6.4-branch

```