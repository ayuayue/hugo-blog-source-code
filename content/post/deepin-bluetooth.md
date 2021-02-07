---
title: "Deepin 升级后蓝牙消失问题"
date: 2021-02-07T17:06:38+08:00
lastmod: 2021-02-07T17:06:38+08:00
draft: false
keywords: [Deepin 升级后蓝牙消失问题]
description: "Deepin 升级后蓝牙消失问题"
tags: [Deepin 升级后蓝牙消失问题]
categories: [Deepin 升级后蓝牙消失问题]
---

在一开始使用 `deepin` 时确认过蓝牙是正常的，但是在最近的更新后却发现设置中找不到了蓝牙，任务栏也没了这个图标，版本是 `20.01`，内核为 `5.10.5`。

看了论坛的一些帖子，说是内核太新不兼容之类的。在换到 `5.4、5.7、5.8` 版本的内核无果后，觉得不太是这个问题，开始寻找使用命令来管理蓝牙并连接蓝牙的方案，找到后开始试验，但无法正常创建设备。最后在一篇帖子中看到开启了蓝牙服务，就试着看看。结果直接图标就出来了。经回想，很可能是前段时间开机有点慢，就关闭了一些开机自启的服务。

### 解决方案

#### 一  使用工具软件

首先开启 `buletooth` 服务，开启后在设置中会出现蓝牙模块，并且任务栏图标会显示出来。

```bash
sudo service bluetooth start
```

接下来配置为开机自启

1.  安装星火商店 地址 https://spark-app.store/
2.  下载系统助手 

![image-20210207171646290](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210207171646290.png)

3.  在开始菜单中找到系统助手
4.  找到优化加速，开启 `buletooth` 的开机自启服务

![image-20210207171822060](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210207171822060.png)



#### 二  使用命令行

```bash
sudo systemctl enable bluetooth.service #开启开机自启
sudo systemctl disable bluetooth.service # 关闭开机自启
```



>   参考链接：
>
>   https://blog.csdn.net/peixiuhui/article/details/82738222
>
>   https://blog.csdn.net/qq_22227087/article/details/84528201

