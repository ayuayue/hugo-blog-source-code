---
title: "Windows 安装使用Homestead 遇到的问题总结"
date: 2020-12-27T17:44:18+08:00
lastmod: 2020-12-27T17:44:18+08:00
draft: false
keywords: [Homestead]
description: "Windows 安装使用Homestead"
tags: [Homestead]
categories: [Homestead]
---

### 安装

本环境使用 `VirtualBox6.1` `Vagrant2.2` 首先下载并安装这两个应用.

#### 添加 `homestead`

```bash
vagrant box add laravel/homestead
```

根据提示,选择 `VirtualBox` 的选项,由于国内的大多网络环境,无法进行快速的下载.我们可以根据输出的 `box` 地址来手动下载,通过使用浏览器访问输出的 `https` 链接来下载到本地的其他位置.

下载完成后,我们手动进行添加 `box`

```bash
vagrant box add laravel/homestead ~/Download/cdb...(下载的文件)
```

然后使用命令 `vagrant box list` 检查安装的 `box`
输出如下: `laravel/homestead (virtualbox, 0)` 后面的 `0` 为版本,可能是手动导入的缘故,没有正常的读取版本信息.

#### 拉取 `homestead`

```bash
git clone https://github.com/laravel/homestead.git ~/Desktop/Homestead
cd Homestead
git checkout v7.16.1
# Mac / Linux... / git-bash
bash init.sh

# Windows...
init.bat
```

#### 配置信息

```bash
folders:    # 目录映射
    - map: ~/Desktop/code
      to: /home/vagrant/code  # 将本地的桌面code目录映射到虚拟机的 /home/vagrant/code中

sites:     # 虚拟主机配置
    - map: homestead.test   #  配置虚拟主机名
      to: /home/vagrant/code/Laravel/public   # 主机映射地址
```

#### 启动 vagrant

```bash
vagrant up
```

可能会报错,或者发现没有启动,而是又去那个地址下载 `box` 去了,这就是因为版本没有读取到的原因,这里我们去更改一个文件.

```bash
vim ~/Desktop/Homestead/scripts/homestead.rb   # 拉取的homestead的仓库中的文件
# 查找到下面的一句进行更改
config.vm.box_version = settings['version'] ||= '>= 0' # 0是更改后的内容,因为输出的版本是0.

```

更改后再次使用 `vagrant up` 即可启动

### php 版本的替换

`homestead` 中安装了几个不同的 `php` 版本,如果需要替换执行下面的操作

```bash
sudo passwd root # 重设 root 密码
update-alternatives --display php 查看所有 php 版本和当前版本
sudo update-alternatives --config php # 根据输出提示,输入对应版本的序号即可替换
php -v # 验证版本信息
```

### 外部工具链接

1. xshell

```bash
ip : 192.168.10.10
port : 22
name : vagrant
passwd : vagrant
```

2. xftp 通过 xshell 上的快捷按钮即可自动添加并打开
3. navicat

```bash
ip : 192.168.10.10
port : 3306
name : homestead
passwd : secret
dbname : homestead
```

4. 命令行 `ssh` 连接

```bash
vagrant ssh
```

#### 关闭销毁重载

```bash
vagrant halt # 关闭
vagrant reload --provision # 重载配置,修改过配置后需要重载生效
vagrant destory # 销毁一个 box
```

### 优化 `git-bash` 使用

注意:**由于权限的原因,一些操作在 `git-bash` 中无法使用.所以,我们在配置之前需要先将 `git-bash.exe 和 bash.exe` 以管理员的身份打开.在 `git` 的安装路径下,`bash.exe` 在安装目录的 `bin` 下.**
** git-bash 下的配置文件都相对于 git 的安装目录来说的.**

#### host 同步系统

创建虚拟主机后,需要同步更改系统中的 `hosts` 文件,如果不行,需要清除 `dns,浏览器缓存`.
这里通过修改 `/etc/hosts` 文件并设置别名来覆盖系统 `hosts` 文件,`linux` 系统中`hosts` 就是在 `/etc/hosts`,保持一个统一.

```bash
vim /etc/hosts
# 加入需要配置的虚拟主机地址域名映射
192.168.10.10 coco.test
# 退出vim
alias host="cp /etc/hosts /c/Windows/System32/drivers/etc/hosts"
# 此时设置了一个别名,使用host就会同步修改系统 hosts文件
host
```

跟 `linux` 相同,这只是临时的修改,如果想要持久化,需要更改 `git-bash` 的配置文件

```bash
vim /etc/profile
alias host="cp /etc/hosts /c/Windows/System32/drivers/etc/hosts"
# 关闭vim
source /etc/profile # 刷新配置
host # 此时在别的会话中也可以使用这个别名了,同理可以根据自己的需要设置更多的别名.
```

#### 修改 `git-bash` 的 `vim` 配置

`vim` 的原始配置还是比较难用的,`git-bash` 中无法使用扩展,所以简单设置一些快捷键跟操作就行了.

```bash
vim /etc/vimrc
# 加入下面内容
set nocompatible " 不兼容 vi
syntax on " 高亮解析

set number " 设置行号
set wildmenu " 下部菜单
set hlsearch " 高亮搜索
set incsearch
set ignorecase
set smartcase
set cursorline " 行光标
set backspace=2
set nrformats=
set autoindent
set scrolloff=15 " 距离屏幕底部行数
let mapleader=',' " leader 键
noremap = nzz
noremap - Nzz
inoremap jj <Esc>`^
map Q :q<CR> " Q 退出
map S :w<CR> " S 保存
map R :source $MYNVIMRC<CR> " R 重载 vim 配置文件,需要在 profile 中设置该变量
inoremap <Leader><CR> :nohls<CR>
nnoremap <Leader><CR> :nohls<CR>
noremap <C-f> <C-f>zz " 翻页
noremap <C-u> <C-u>zz

```

#### profile

我目前的 `/etc/profile` 文件

```bash
# 新增内容
export MYNVIMRC=/etc/vimrc

# 通过 homestead up 等命令来启动 vagrant
function homestead() {
    ( cd ~/Desktop/Homestead && vagrant $* )
}

alias host="cp /etc/hosts /c/Windows/System32/drivers/etc/hosts"

```
#### composer 问题
**`laravel` 安装器好像是安装的最新版的 `laravel` ,并且我没有找到自定义版本的方法,目前使用 `composer` 进行 `laravel` 的项目新建即可**
1. 镜像源问题
```bash
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```
2. 创建 laravel 项目时 ` composer create-project laravel/laravel coco --prefer-dist "5.6.*"` :
```bash
[RuntimeException]                                                                            
  Could not delete /home/vagrant/code/coco/vendor/kylekatarnls/update-helper/src/UpdateHelper:

  # 进入项目不使用插件安装
  cd coco 
  composer install --no-plugins
```
3. App key 
```bash
php artisan key:gen
```

