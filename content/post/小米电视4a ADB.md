---
cssclass:
title: 小米电视4a ADB
tags: [IT/小米, IT/ADB]
image-auto-upload: true
date: 2022-10-03 20:46:46
lastmod: 2022-10-25 08:24:44
---
# 小米电视4a ADB
adb命令

adb connect ip:port

adb disconnect

adb devices

adb push

adb shell

adb shell am monitor 监听启动的软件

adb shell pm uninstall --user 0 com.catchingnow.icebox 删除软件

删除升级

adb shell pm uninstall --user 0 com.xiaomi.mitv.upgrade

删除广告

adb shell pm uninstall --user 0 com.miui.systemAdSolution

adb shell pm uninstall --user 0 com.xiaomi.mitv.advertise