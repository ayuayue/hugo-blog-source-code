---
title: "Windows 下 wsl2 的使用及问题记录"
date: 2020-12-30T17:54:43+08:00
lastmod: 2020-12-30T17:54:43+08:00
draft: false
keywords: [Windows,Wsl2]
description: "Windows 下 Wsl2 的使用及问题记录"
tags: [Windows ]
categories: [Windows ]
---

### 什么是适用于 Linux 的 Windows 子系统？

适用于 Linux 的 Windows 子系统可让开发人员按原样运行 GNU/Linux 环境 - 包括大多数命令行工具、实用工具和应用程序 - 且不会产生传统虚拟机或双启动设置开销。

这是来自微软官方文档中的解释

当然现在 `wsl2` 已经出来了，更改成为了完整的 `Linux` 内核。下面是两个版本的比较

![image-20201230192121705](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20201230192130.png)

### WSL2 的安装或升级

对于安装或升级，或是设置 `wsl2` 为默认的子系统，微软官方的文档十分的详细且明了。[官方wsl2地址](https://docs.microsoft.com/zh-cn/windows/wsl/install-win10)

这其中包括了其他的一些内容，比如安装需要的环境，常见的问题及疑难问题的解答。对于安装并运行 `wsl2` 已经绰绰有余。这里主要记录一些使用中的问题，及开发中的 技巧

### IP问题

由于子系统是通过虚拟网卡并设置到与宿主机同一网段中，所以可以互相访问，但是对于宿主机来说，并不能够完全的隔离。如果在子系统中开启了网站服务，那么在宿主机居然使用 `localhost` 来访问，这太让人迷惑了。并且另一个致命的问题是 每次重启 `wsl2` 都会自动分配一个 `ip` 地址，连同宿主机的虚拟网段中的 `ip`也会改变，所以每次都需要获取并更改服务的地址，如 ： 通过 `docker` 开启了一个服务并监听了一个端口，但是每次宿主机重启后也是一样的发现服务地址变了，可能会导致服务运行失败，最小的副作用是更改 `ip` 地址。

网上也有很多解决的方案，也有手动配置 `ip` 的，编写一些脚本设置为开机自启，但这样灵活度太低。接下来介绍一种动态获取的方式。

大概原理就是 ： 先获取 `wsl2` 的地址，然后在宿主机的 `hosts` 文件中加入一条虚拟主机的配置，即 ` 192.168.245.161  ubuntu2004.wsl` 的形式，做成一个服务，开机自启动，并且有可执行文件，可以手动卸载服务或重启服务进行 `ip` 的重新映射

1. 安装 `wls2host.exe` [工具下载地址](https://github.com/shayne/go-wsl2-host/releases)
2. 最好将工具放到环境变量中
3. 打开系统的服务，找到 `wsl2host` 设置为自动启动
4. 设置 `hosts` 文件的权限为当前用户所有控制权

如果重启了 `wsl` 服务，也要使用 `wsl2host stop ,wsl2host start` 进行重新启动映射服务。

重启 `wsl` 服务命令：

```bash
// 用管理员权限打开 powershell
#停止LxssManager服务
net stop LxssManager  
 
#启动LxssManager服务
net start LxssManager
# 如果执行失败再执行一次，两次还失败看报错进行百度
```

这样就把 `ip` 通过一个域名来映射到了，以后可以通过这个域名来进行访问子系统的各种服务，如 `postgresql，ssh，mysql` 等。

### 环境变量

在子系统中有一项特色，但也可以说是缺点，就是可以互相操作。在 `linux` 中可以使用 `windows` 中的应用来打开 `linux` 下的文件。比如在子系统中使用 `explorer .` 就会在当前目录打开 `Windows` 的文件管理器。当然在 `linux` 中也可以操作 `Windows` 下的应用，因为整个 `Windows`  就相当与挂载到了 `linux` 的目录中。当你使用 `echo $PATH` 命令的时候可能就发现了，打印出了所有的环境变量包括 `Windows` 。这也就是为什么可以互相操作的基础了。

虽然方便，但也带来了问题。

如果我们在两个系统中都安装了同一个应用，那么在子系统中，常常会调用到外面的环境变量，导致子系统中无法使用。这就是一个问题，当然 `Windows` 肯定也想到了这个问题，在官方文档中就提到了这个并提供了 **禁用互操性** 的方法 [地址](https://docs.microsoft.com/zh-cn/windows/wsl/interop).但我并没有使用这个方法

我的解决方法是在 `linux` 系统中,重置环境变量,将所有来自 `Windows` 中的变量全部替换为空.

我是用的是 `zsh` 所以在 `~/.zshrc` 中加入以下代码

```bash
setopt no_nomatch
export PATH=`echo $PATH | sed 's/:\/mnt\/c\/[^:]*//g'` # 替换c盘的环境变量
export PATH=`echo $PATH | sed 's/:\/mnt\/d\/[^:]*//g'` # 替换d盘的环境变量 ,我只有两个盘,推荐环境变量最好放在一个或两个盘中,不要太多
```

然后重载 `zshrc` 文件 ` source ~/.zshrc`

如果用的是自带的 `bash` 则修改 `bashrc` ,修改完同样进行重新加载配置

**建议** : 虽然 `Windows` 提供了这中互操的方式,比较方便.但是两个环境还是独立的好, `Windows` 官方也说了,不要让你的开发环境过于混乱,只要一个平台就可以了,可以把 `wsl2` 作为服务器, `Windows` 主要来远程连接控制,回归 `Windows` 办公娱乐的场景.不要因为 `Windows` 的终端不好用从而从` linux` 的终端中来操作 `Windows` 的文件,一个是权限问题,一个就是环境变量问题,要知道那个才是重要的,虽然 终端不好用,但还是有其他的方式进行改善的,这个以后会说,主要基于 `git-bash` ,因为不需要安装其他的环境,也足够简单,但跟 `linux` 的原生终端还是差了一些.

### 代理问题

`Windows` 平台有各种的代理客户端,但是在子系统中在使用一个客户端就有点多余了,况且本来子系统就跟宿主机在一个网段,可以直接使用宿主机的 `ip` 加 端口号来启用代理.

我使用的是 `clash` ,这里说一下 `clash` 的代理如何在子系统中使用.

1.  首先开启 `clash` 的 `LAN` 功能

   ![image-20201230201705872](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20201230201707.png)

2. 由于虚拟网卡的地址也是每次重启自动获取的,所以我们也要在子系统中每次去解析到宿主机的 `ip` 地址.

   解析方式

   ```bash
   `cat /etc/resolv.conf|grep nameserver|awk '{print $2}'` # 这个就可以打印出宿主机的 ip 地址了
   ```

   这下我们就可以设置代理了

   ```bash
   export http_proxy=http://ip:port # ip 就是上面获取到的,端口就是 clash 配置的
   export https_proxy=https://ip:port # 同上
   # 配置全部代理
   export ALL_PORXY=socks5://ip:port
   ```

   当然这里设置后只是临时的,所以我们可以将这些配置写入配置文件,同样打开 `~/.zshrc`,加入以下内容

   ```bash
   export WHOST=`cat /etc/resolv.conf|grep nameserver|awk '{print $2}'` # 设置一个环境变量,通过在终端中输入 echo $WHOST 即可输出 Windows 的 ip 地址
   export http_proxy=http://`cat /etc/resolv.conf|grep nameserver|awk '{print $2}'`:port # 配置 http 代理
   export https_proxy=https://`cat /etc/resolv.conf|grep nameserver|awk '{print $2}'`:port # 配置 https 代理
   alias ALL_PROXY="export ALL_PROXY=socks5://$WHOST:port" # 配置一个别名来临时开启所有代理,http,https以外的
   alias NONE_PROXY="unset ALL_PROXY"  # 别名取消所有的代理
   ```

   配置完成后通过 `curl -v www.google.com` 来检验,如果输出如下,则说明开启成功

   ```bash
   Uses proxy env variable http_proxy == 'xxx'
   *   Trying xxx...
   * TCP_NODELAY set
   ```

   ### 远程 `SSH`连接

   当我们使用 `xshell` 等工具想连接 `wsl` 时,往往是连接不上的,这个时候需要配置一下 `sshd` 服务并生成公钥并修改权限

   ```bash
   sudo apt-get install openssh-server # 确认 openssh 服务安装
   sudo vim /etc/ssh/ssh_config # 修改配置文件
   # 一下为文件内容
   PasswordAuthentication yes # 权限改为 yes,运行远程登录
   # 退出 vim
   ps -e |grep ssh # 查看 ssh 服务是否开启
   sudo /etc/init.d/ssh start # 没开启就使用此命令开启 ,stop 关闭
   
   # 如果报认证或者权限问题执行下面的命令
   sudo ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
   sudo ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
   sudo /etc/init.d/ssh stop
   sudo /etc/init.d/ssh start
   sudo chmod 600 /etc/ssh/ssh_host_dsa_key
   sudo chmod 600 /etc/ssh/ssh_host_rsa_key
   sudo /etc/init.d/ssh restart
   # 此时就可以通过上面配置 ip 到域名的那个域名来访问了.也就是 ubuntu2004.wsl,端口22 用户名密码是自己设置的
   ```

   由于 `wsl` 无法使用 `systemctl` ,所以如果要开机启动 `ssh` 服务,网上有一些方案可以解决,使用 `Windows` 脚本开机自启的时候开启 `wsl` 的 `ssh` 服务.不过我懒的弄了,直接弄个 命令别名来做,况且 `Windows Terminal` 可以直接连 `wsl` ,也不太需要再弄了.

   ```bash
   # ~/.zshrc 文件中加入
   alias myssh="/etc/init.d/ssh"
   # 退出vim
   source ~/.zshrc
   myssh status # 查看状态,start 启动,stop 停止,restart 重启
   
   ```

   ### Postgresql问题

   1. 安装 前面有篇文章写了安装,这里使用官方源的方式,不使用 `docker` [文章链接](https://caoayu.xyz/post/docker-install-postgres/)

   2. 本地登录,修改密码

      安装了 `postgresql` 后,在本地需要切换到 `postgresql` 用户来登录,然后修改密码,那篇文章中也有写更改密码等操作,这里不重复

   3. 设置远程登录

      1. 同样在本地连接到数据库

         ```bash
         sudo su - postgres
         vim /etc/postgresql/12/main/pg_hba.conf 
         # 在 IPV4 下面加入一行
         host    all             all             0.0.0.0/0               md5
         # 并将原来的 host all all 127.0.0.1/32 md5 进行注释
         # 在 vim 中进入普通模式 输入 :e /etc/postgresql/12/main/postgresql.conf # 跟再次使用 vim 打开这个文件一样
         # 在这个文件中,找到注释的一行 #listen_addresses = 'localhost' ,看到 use * for all
         # 所以在这行上面加一行
         listen_addresses = '*'
         # 退出 vim
         ```

         2. 重启 `postgresql` 服务

            ```bash
            sudo service postgresql restart
            ```

   4. 使用 `navicat` 等工具连接

      1. 地址使用上面的 `hosts` 文件中的域名,其他按实际情况填写即可连接

### 其他问题

#### 关于终端

`linux` 的终端工具比较多,使用其他更方便.这里推荐一些小工具

1. `zsh`
2. `zim` 一个 `zsh` 的框架,比 `oh-my-zsh` 更快,安装插件更简单,推荐插件 : `1. z; 2.p10k(一个终端主题)`
3. `fzf` 模糊查找工具,可以跳转到目录,快速定位目录,提供更易用的历史命令记录,还可以搭配 `vim` 使用
4. `bat` 一个类 `cat` 的工具,可以根据文件类型进行高亮显示,配合 `fzf` 可以预览文件内容并高亮显示
5. `fd-find` 一个代替 `find` 的工具,更快,搭配 `fzf`
6. `ag` 一个模糊搜索工具,这个主要根据文件内容进行匹配,搜索一些文本信息,同样可以搭配 `fzf,vim`
7. `cowsay` 一个牛喊出你输入的参数,不支持中文
8. `figlet` 终端艺术字,不支持中文
9. `tmux` 终端复用神器
10. `ranger`  终端文件管理器
11. ...



`Windows` 的终端就没那么多可玩性了,少了 `zsh` 就很不方便,而且 `cmd` 一套规则,`powershell` 一套规则,让人头大.

当然上面的小工具有些也是可以使用的,比如, `figlet` , `cowsay` `z`

**建议** 不推荐使用 `cmd,powershell` ,除非你是一个 `Windows` 开发者.可以根据[这篇文章](https://www.caoayu.xyz/post/homestead/#优化-git-bash-使用)来简单的配置 `git-bash`,使用 `git-bash` 来操作 `Windows` 的命令

上面的小工具有些不好找,推荐使用 `Windows` 下的包管理工具来安装  ---  `scoop`

相比 `winget` ,包更多,细节更透明.

**推荐终端方案** 也是目前我正在使用的方案,可能过一段时间就改了

`Windows` 下 使用 `cmder` 来复用 `git-bash`,将 `git-bash` 设置为 `cmder` 的默认终端,避免使用 `cmd,powershell`,并且可以设置一些别名,更灵活

子系统 `Linux` 下, 使用 `Windows Terminal` 来复用 `ubuntu`,将`Ubuntu` 设置为 `WindowsTerminal` 的默认终端,并且起始目录为用户家目录,打开 `WindowsTerminal` 的设置选项,在配置项中有文档地址,根据文档进行自定义

这样就把开发环境隔离开,一个平台运行,一个平台访问,加上 `Vscode` 的 `Remote Wsl` 插件,直接连接就可以开发,十分方便清晰.



**接下来贴下我的部分`WindowsTerminal` 的配置**

```bash
{
  "$schema": "https://aka.ms/terminal-profiles-schema",

  "defaultProfile": "{07b52e3e-de2c-5db4-bd2d-ba144ed6c273}", # 默认终端id

  // custom start
  "theme": "light",
  "tabWidthMode": "compact",
   "copyOnSelect": true,

  // If enabled, formatted data is also copied to your clipboard
  "copyFormatting": false,
  "profiles": {
    "defaults": {
      // Put settings here that you want to apply to all profiles.
    },
    "list": [
    {
    # wsl 子系统
        "guid": "{07b52e3e-de2c-5db4-bd2d-ba144ed6c273}",
        "hidden": false,
        "name": "Ubuntu-20.04",
        "source": "Windows.Terminal.Wsl",
        "fontFace": "Cascadia Code PL",
        "acrylicOpacity": 0.8,
        "alwaysShowTabs": true,
        "useAcrylic": true,
        "colorScheme": "One Half Dark",
        "startingDirectory": "//wsl$/Ubuntu-20.04/home/caoayu",
        "cursorShape": "bar",
        "cursorColor": "#fff"
      },
      {
      # git-bash
        "acrylicOpacity": 0.8,
        "closeOnExit": true,
        "colorScheme": "Campbell",
        "commandline": "C:\\Users\\caoayu\\scoop\\apps\\git\\current\\bin\\bash.exe",
        "cursorColor": "#FFFFFF",
        "cursorShape": "bar",
        "fontFace": "Cascadia Code PL",
        "fontSize": 12,
        "historySize": 9001,
        "name": "bash",
        "snapOnInput": true,
        "startingDirectory": ".",
        "useAcrylic": true
      },
    ]
  }
```

