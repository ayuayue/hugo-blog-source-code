---
cssclass:
title: IDEA 连接远程 docker 容器,管理镜象
tags: [IT/Docker, IT/PHPSTORM]
image-auto-upload: true
date: 2022-10-03 20:21:46
lastmod: 2022-10-25 08:53:48
---
# IDEA 连接远程 docker 容器,管理镜象
1.  首先安装并启用 docker 插件
2.  启用 docker 的 tcp 监听 ,在 ExecStart 后追加

```bash
vim /lib/systemd/system/docker.service
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock -H tcp://0.0.0.0:2375

```

3.  重载配置

```SQL
systemctl daemon-reload
```

4.  重启docker

```SQL
systemctl restart docker
```

5.  查看监听是否启用 2375 被监听

```SQL
netstat -tlnpu
# 输出 tcp6       0      0 [::]:2375               [::]:*                  LISTEN      3055/dockerd
```

6.  配置 ide docker

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112201748989.png)