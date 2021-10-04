---
title: "Vscode 自定义美化（亮色）"
date: 2021-07-24T12:11:34+08:00
draft: false
---

### 字体渲染

vscdoe 在 Windows 上的字体渲染一直是我想要吐槽的点，使用习惯了 ide，后在看 vscode 的字体感觉发虚，再后来直接比不上 sublime 的字体渲染了。

关闭 GPU加速可以解决这个问题，找到 vscode 的安装目录，找到 bin 目录，修改 code.cmd 跟 code , 在 exe 启动命令后添加 ` --disable-gui `

```
# bin/code

ELECTRON="$VSCODE_PATH/$NAME.exe --disable-gpu"
```

```
# bin/code.cmd

"%~dp0..\Code.exe --disable-gpu"
```

重新启动，字体推荐 jetbrain mono

### 亮色主题

在一年之前，编程或者编辑文件时喜欢使用暗色主题，感觉看起来比较清晰直观，在眼睛有一段时间不适的时候，换成了 ide 的亮色主题，习惯了之后发现原来亮色更加直观，而且暗色主题背景太暗，想要看清就下意识的要聚集眼部注意力，时间久了眼睛会很疲劳。

但之前由于 vscode 的字体渲染，很多时候都是使用 ide ，但是小文件或脚本使用 ide 打开就有点大材小用了，而且启动速度也慢。

禁用了 GUI 加速后，字体渲染明显变好了，但是亮色主题的侧边栏，跟底部状态栏不适配亮色主题，一个黑色一个蓝色，太突兀。没有 ide 的设计有工业感。

那就自己手动配置好了，打开 setting.json ， 加入一下配置

```
# settings.json

  "workbench.colorTheme": "Default Light+", # 默认亮色主题
  "workbench.colorCustomizations": {
    
    // 侧边栏颜色
    "activityBar.activeBackground": "#ddd",
    "activityBar.background": "#ddd",
    "activityBar.foreground": "#000",

    // 底部状态栏颜色
    "statusBar.foreground": "#000",
    "statusBar.background": "#ddd",
    "statusBar.noFolderBackground": "#ddd",
    "statusBar.debuggingBackground": "#ddd"
  }
```

修改保存后可以看到效果。我的效果图

![image-20210724134014436](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20210724134022.png)