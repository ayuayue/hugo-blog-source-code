---
cssclass:
title: Navicat无限试用
tags: [IT/Tools]
image-auto-upload: true
date: 2022-10-03 20:44:46
lastmod: 2022-10-25 08:20:50
---
# Navicat无限试用

### windows手动

1.  关闭Navicat
2.  Win + R，输入regedit回车
3.  删除HKEY_CURRENT_USER\Software\PremiumSoft\Data
4.  展开HKEY_CURRENT_USER\Software\Classes\CLSID
5.  展开每一个子文件夹，如果里面只包含一个名为Info的文件夹，就删掉它

打开系统注册表找到以下位置HKEY_CURRENT_USER\Software\Classes\CLSID**{FCABAC0C-4447-F047-51F3-7E27276ECA6F}**\Info

提醒一下：不同系统红色ID 可能不一样哦~！

直接将 Info 目录删除即可！

### windows脚本

```Bash
@echo off

echo Delete HKEY_CURRENT_USER\Software\PremiumSoft\NavicatPremium\Registration[version and language]
for /f %%i in ('"REG QUERY "HKEY_CURRENT_USER\Software\PremiumSoft\NavicatPremium" /s | findstr /L Registration"') do (
    reg delete %%i /va /f
)
echo.

echo Delete Info folder under HKEY_CURRENT_USER\Software\Classes\CLSID
for /f %%i in ('"REG QUERY "HKEY_CURRENT_USER\Software\Classes\CLSID" /s | findstr /E Info"') do (
    reg delete %%i /va /f
)
echo.

echo Finish

pause
```

[https://github.com/anhao/reset-navicat-premium/blob/main/reset.bat](https://github.com/anhao/reset-navicat-premium/blob/main/reset.bat)

### linux

```Bash
#!/usr/bin/env python3
# -*- coding:utf-8 -*-

import os
import re

# 试用时间重置的正则
ps = (
        re.compile(r'\[Software\PremiumSoft\Data\\{[^\}]*\}\Info\].*?\n[^\[]*'),
        re.compile(r'\[Software\Classes\CLSID\\{[^\}]*\}\Info\].*?\n[^\[]*')
    )

# user.reg 的路径
regfile = os.path.join(os.environ['HOME'], '.navicat64', 'user.reg')

# 正则替换
with open(regfile, 'r+') as f:
    regstr = f.read()
    for p in ps:
        regstr = p.sub(lambda m: '', regstr)

    f.seek(0, 0)
    f.truncate()
    f.write(regstr)
```