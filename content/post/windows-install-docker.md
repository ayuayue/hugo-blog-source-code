---
title: "Windows家庭版安装Docker"
date: 2020-09-13T15:42:09+08:00
lastmod: 2020-09-13T15:42:09+08:00
draft: false
keywords: [Windows,docker]
description: "Windows"
tags: [Windows]
categories: [Windows]
---

### `Docker`在`Windows`上使用

目前,`docker`还只能在专业版或者企业版的`windows`中安装使用.在家庭版中进行安装的过程中会提示

![image-20200913154741203](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200913160539.png)

我们使用修改注册表中`windows`的版本信息就可以完成安装了.修改完成后重启注册表会自动被修改回来,但`docker`仍然可以正常使用

### 安装步骤

1. 首先进入到`bois`中开启CPU虚拟化.各电脑型号开启的方式可以百度.

2. 开启`hype-v`,家庭版默认没有,可以通过脚本安装.

   ```bash
   # 将下面的脚本保存到一个文本文件中,后缀改为 .bat 使用管理员身份运行即可安装.安装完成后重启
   pushd "%~dp0" 
   
   dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt 
   
   for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
   
   del hyper-v.txt
   
   Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
   ```

3. 重启后开启`hype-v`,脚本可能已经帮你开启了.再次确认一下

   进 控制面板 -> 程序 -> 启用或关闭`windows`功能,找到`hype-v` 打勾开启应用,根据提示进行判断是否需要重启

   ![image-20200913155337482](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200913160551.png)

4. 伪装成专业版(如果不熟悉,可以先将所有注册表信息导出到磁盘中,如果系统崩溃可以进行还原,不乱操作是不会有问题的)

   1. 进入注册表. `win + R 输入 regedit`, 根据图中指引找到`EditionID`这一项

      ![image-20200913155529501](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200913160559.png)

   2. 右键修改,将原来的全部删除.修改为 `Professional`

5. 下载并安装`docker`

重启后可以发现注册表有被改了回来

使用命令行查看`docker`的版本 确保安装成功 `docker version`