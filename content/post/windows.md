---
cssclass:
title: Windows 问题
tags: [IT/Windows]
image-auto-upload: true

date: 2022-10-03 20:59:46
lastmod: 2022-10-25 08:15:55
---
# Windows 问题
1.  无法粘贴复制
    
    关闭有道词典 | 向日葵 等远程软件,系统粘贴板被占用
    
2.  扩展屏幕时,打开软件屏幕范围内看不到
    
    导致以为没有打开,其实任务栏是有图标,也会闪一下
    
    使用 alt + 空格 ,调出菜单,可以使用鼠标或键盘最大化即可
    
3.  微信多开脚本
    

```bash
# bat
TASKKILL /F /IM wechat.exe
start "" "C:\Program Files (x86)\Tencent\WeChat\WeChat.exe"
start "" "C:\Program Files (x86)\Tencent\WeChat\WeChat.exe"


```

4.  windows 更新安装失败

````
sudo dism /online /cleanup-image /startcomponentclaenup
```





vivetool

```Bash
35908098
37634385
39145991


```

ViveTool是强制启用Win11隐藏功能的作用。

有时开启的隐藏功能会被关闭（在某一次重启后，或蓝屏后，或切换用户后，或使用中被关闭），那你可能需要禁用“ReconcileFeatures”计划任务，没有此类问题的就不需要禁用“ReconcileFeatures”了。

禁用“ReconcileFeatures”方法：

1. Win+R > 运行 > taskschd.msc > 回车
2. 定位到：

任务计划程序库 > Microsoft > Windows > Flighting > FeatureConfig

1. 在窗口右侧的名称为：ReconcileFeatures，右击此项选择禁用。



## win+G开启录屏

修改注册表

\HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\GameDVR

将AppCaptureEnabled的值改为1

````