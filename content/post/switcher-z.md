---
title: "Windows 切换同窗口应用及git-bash的一些小工具"
date: 2021-01-01T12:19:21+08:00
lastmod: 2021-01-01T12:19:21+08:00
draft: false
keywords: [Windows ]
description: "Windows 切换同窗口应用及git-bash的一些小工具"
tags: [Windows ]
categories: [Windows ]
---

### 窗口切换

`Windows` 上切换窗口以及任务桌面的快捷键非常多，而且触摸板支持手势切换最近应用，但是缺少一个像 `Linux` 上面的那种切换同一个应用的不同窗口快捷键，在 `Linux` 下可以使用 `Alt + ` `` ` 来切换打开的两个浏览器窗口或者多个文件管理器窗口，不用在所有的任务窗口中来回切换，但是 `Windows` 上原生并不支持这种快捷键。

好在找到一款小应用可以启用这个快捷键，这个应用叫做 [easy window switcher](https://neosmart.net/EasySwitch/) 。软件只有 100 多`kb`。设置为开机启动就可以了。

### `Z` 目录跳转工具

**只能在git-bash下正常使用**

在 `Windows` 下， 如果不通过文件管理器的路径打开终端，想要跳转到某个目录十分困难，尤其是对于分区比较多的用户，往往需要跨越几个盘，在 `linux` 下可以使用 `z` 来进行目录的快速跳转，他会记录你通过命令行每次打开的目录名跟地址，一一进行匹配，如果记录中查找的到就会自动跳转到指定目录中去，不在需要一级一级的去填写，只需要运行命令 `z ` + 目录名即可。

通过 `z` 命令可以查看当前所有的目录记录情况，输出如下：

```bash
0.5        /c/Users/caoayu/.ssh
0.5        /c/Users/caoayu/scoop/apps
1          /c/tools/you-get
1          /d
1.5        /c/Users/caoayu/Downloads
1.5        /c/Windows/system32/drivers/etc
2          /c/Users/caoayu/scoop/apps/git/current/etc
19         /c/Users/caoayu/Desktop
38         /d/Github
240        /c/Windows/System32
```

然后就可以通过 `z github` 来跳转到 `/d/Github/`

#### 安装及使用

[GitHub 地址](https://github.com/rupa/z)

1. 将 `z.sh` 文件放到 `windows` 下的用户家目录

2. 在用户家目录新建一个 `.bashrc` 文件，加入下面内容

   ```bash
   source /etc/z.sh
   ```

3. 重新打开一个 `gitbash`
4. 使用 `cd` 命令进入一个目录，执行 `z` 查看记录的目录信息



### `Tmux` && `Fish`

`tmux` 是 `linux` 下的一款终端复用的神器，但是在 `Windows` 上无法使用，但是在 `GitHub` 上，有人把他单独抽离了出来，在 `git-bash` 上可以使用，这就可以用 `git-bash` 来复用终端了，几乎不用在使用其他的一些终端来复用 `git-bash` 了。

`fish` 也是 `Linux` 下一个不错的终端提示工具，可以记录你的终端输入信息，在下一次输入的时候进行隐式的提示，带来更好的体验

#### 安装及使用

[github地址](https://github.com/hongwenjun/tmux_for_windows)

在 `GitHub` 项目中，已经包含了安装说明，如果有不明白的，可以直接把那两个压缩包下载下来解压，将对应目录中的文件复制进 `git` 安装目录中的相应的目录中去，再重启 `git-bash` 就可以使用了。

**问题** 在使用过程中发现主要有两个问题，一个是只能通过 `gitbash` 来启动 `tmux` ，即使是通过 `git` 安装目录下的 `/bin/bash.exe` 来打开也是不行的，所以如果是使用 `WindowsTerminal、cmder` 来复用 `git` 的 `bash.exe` 的话是无法使用 `tmux` 的。

问题2 ，对于 `fish` 的一些命令无法使用，如 `fish_config` 无法调用浏览器来配置 `fish` 的样式、函数、别名等。但是有总比没有好。

#### 演示

![switcher](https://caoayu.xyz/file/switcher.gif)