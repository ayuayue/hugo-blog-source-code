---
cssclass:
title: rime 配置 onedrive 同步
tags: [IT/Tools, IT/Rime]
image-auto-upload: true
date: 2022-10-03 20:30:46
lastmod: 2022-10-25 08:19:53
---
# rime 配置 onedrive 同步
修改 installation.yaml 文件
installation_id 为设备 ID
sync_dir 为 onedrive 路径

配置完成后点击同步即可上传到 onedrive ,只会同步合并不同设备的字典信息,配置信息手动复制到 rime 用户配置目录中

恢复
配置好 sync_dir 后，同样点击同步用户资料后自动合并,并在词典管理中导入词典.

```yaml

installation_id: "windows11-caoayu-pc-i512400"

sync_dir: "C:\\Users\\caoayu\\OneDrive\\rime-save"

```
