---
cssclass:
title: scoop
tags: [IT/Tools, IT/Windows]
image-auto-upload: true

date: 2022-10-03 20:05:47
lastmod: 2022-10-25 08:38:53
---
# scoop

## 安装到其他盘


```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

$env:SCOOP='D:\software\scoop' # 后手动加入到系统变量中
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')


iwr -useb get.scoop.sh | iex
scoop config proxy 127.0.0.1:7890
```

### bucket

```PowerShell
Name        Source                                             Updated            Manifests
----        ------                                             -------            ---------
42wim       https://github.com/42wim/scoop-bucket              2022/8/16 12:33:39        15
dodorz      https://github.com/dodorz/scoop                    2022/7/29 22:32:31         0
dorado      https://github.com/h404bi/dorado                   2022/8/16 8:15:57        215
echo        https://github.com/echoiron/echo-scoop             2022/8/16 12:47:34        84
extras      https://github.com/ScoopInstaller/Extras           2022/8/16 8:38:20       1651
jetbrains   https://github.com/Ash258/Scoop-JetBrains          2022/8/15 19:06:59        99
main        https://github.com/ScoopInstaller/Main             2022/8/16 8:36:53       1069
mochi       https://github.com/Qv2ray/mochi                    2022/8/16 10:04:17        15
MorFans-apt https://github.com/Paxxs/Cluttered-bucket.git      2022/8/10 1:14:14         66
nerd-fonts  https://github.com/matthewjberger/scoop-nerd-fonts 2022/8/13 23:13:35       203
nirsoft     https://github.com/kodybrown/scoop-nirsoft         2022/6/22 0:00:55        271
nonportable https://github.com/TheRandomLabs/scoop-nonportable 2022/8/8 17:07:59         80
scoop-zapps https://github.com/kkzzhizhou/scoop-zapps.git      2022/8/16 6:08:38        169
scoopet     https://github.com/ivaquero/scoopet                2022/8/13 18:05:39        49
versions    https://github.com/ScoopInstaller/Versions         2022/8/16 10:46:27       349

```

```PowerShell
scoop bucket add scoopet https://github.com/ivaquero/scoopet
scoop bucket add jetbrains https://github.com/Ash258/Scoop-JetBrains 
scoop bucket add echo https://github.com/echoiron/echo-scoop 
scoop bucket add dodorz https://github.com/dodorz/scoop 
scoop bucket add dorado https://github.com/chawyehsu/dorado 
scoop bucket add 42wim https://github.com/42wim/scoop-bucket 
scoop bucket add mochi https://github.com/Qv2ray/mochi
scoop bucket add MorFans-apt https://github.com/Paxxs/Cluttered-bucket.git
scoop bucket add scoop-zapps https://github.com/kkzzhizhou/scoop-zapps.git

```

```PowerShell

scoop bucket add extras
main
extras
versions
nirsoft
php
nerd-fonts
nonportable
java
games

```

## 软件安装

```bash
scoop checkup

scoop install 7zip aria2 ccleaner gsudo neteasemusic picgo potplayer trafficmonitor
scoop install sublime-text scoop-completion gow busybox dos2unix gcc
scoop install imhex WXQQ_RevokeMsgPatcher telegram neofetch another-redis-desktop-manager
```