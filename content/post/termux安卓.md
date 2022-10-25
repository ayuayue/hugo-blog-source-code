---
title: termux安卓
tags: [IT/termux]
cssclass:
image-auto-upload: true

date: 2022-10-04 21:10:22
lastmod: 2022-10-25 08:33:54
---
# termux安卓
## Termux

## 换源
```bash
sed -i 's@^\(deb.*stable main\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/termux-packages-24 stable main@' $PREFIX/etc/apt/sources.list
sed -i 's@^\(deb.*games stable\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/game-packages-24 games stable@' $PREFIX/etc/apt/sources.list.d/game.list
sed -i 's@^\(deb.*science stable\)$@#\1\ndeb https://mirrors.tuna.tsinghua.edu.cn/termux/science-packages-24 science stable@' $PREFIX/etc/apt/sources.list.d/science.list

apt update && apt upgrade
```

## 安装，卸载，搜索软件
```bash
pkg install git openssh
pkg uninstall git
pkg search git
```

其余操作于Linux一致
~目录下storage为手机目录，shared为文件。