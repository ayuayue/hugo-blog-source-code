---
title: "Linux 安装PicGo问题"
date: 2020-06-19T22:14:22+08:00
draft: false
tags: [deepin,PicGo]
categories: [linux]
---

使用`deepin`安装`PicGo`时，由于时制作的`AppImage`包,我们可以直接在终端加上执行权限后运行，但是运行的时候报错

```bash
[1827:0619/220927.597934:FATAL:
setuid_sandbox_host.cc(157)] The SUID sandbox helper binary was found, but is not configured correctly. 
Rather than run without sandboxing I'm aborting now. 
You need to make sure that /tmp/.mount_PicGo-okct0x/chrome-sandbox is owned by root and has mode 4755.
```
查阅资料说时内核的问题，有两种比较简单的方式解决
1. 每次启动使用沙箱，加上参数 `--no-sandbox`
2. 开机时自启动用户名称空间：`echo 'kernel.unprivileged_userns_clone=1' > /etc/sysctl.d/userns.conf`,不推荐，可能会因为权限问题无法设定，所以可以写一个bash脚本发送到桌面来启动

```bash
cd /path/to/app
./app.AppImage --no-sandbox
```