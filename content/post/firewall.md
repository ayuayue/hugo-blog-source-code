---
title: "Firewall"
date: 2021-09-12T10:28:27+08:00
lastmod: 2021-09-12T10:28:27+08:00
draft: false
keywords: []
description: ""
tags: []
categories: []
---

服务相关命令

1. 运行停止 systemctl status | start | stop | restart firewalld
2. 开机自启/禁止   systemctl disable | enable firewalld

#### 常用命令

1. firewall-cmd --state  # 显示状态
2. firewall-cmd --get-active-zones  # 查看区域信息
3. firewall-cmd --get-zone-of-interface=ens33  # 查看指定接口所属区域
4. firewall-cmd --reload  # 更新防火墙规则
5. firewall-cmd --list-all  # 查看防火墙，添加的端口也可以看到

临时打开一个端口

```bash
firewall-cmd --add-port=443/tcp
```

永久打开一个窗口

```bash
firewall-cmd --permanent --add-port=3306/tcp
```

查询一个端口是否开放

```bash
firewall-cmd --query-port=80/tcp
```

阻止一个端口

```Bash
firewall-cmd --remove-port=80/tcp
```

开放或阻止后最好使用 reload 重新加载下, 如果还是不生效可能是浏览器缓存,可以使用 telnet curl 等检测
