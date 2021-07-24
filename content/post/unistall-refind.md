---
title: "deepin use Refind"
date: 2021-02-08T11:14:51+08:00
lastmod: 2021-02-08T11:14:51+08:00
draft: false
keywords: [deepin use refind]
description: "deepin use refind"
tags: [deepin]
categories: [deepin]
---
### 安装 `refind`
```bash 
sudo apt install refind 
```
### 使用 `refind`
```bash 
sudo refind-install
```

### 卸载 `refind`

```bash
sudo apt remove refind --purge

cd /boot/efi/EFI 
sudo rm -r refind
cd /boot/efi/EFI
sudo apt autoremove refind
cd /boot
sudo rm -f refind_linux.conf 
cd /etc
sudo rm -rf refind.d
```
