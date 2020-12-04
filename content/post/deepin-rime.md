---
title: "Deepin 安装及使用Rime输入法--中州韵输入法"
date: 2020-12-04T19:42:43+08:00
lastmod: 2020-12-04T19:42:43+08:00
draft: false
keywords: [Deepin 安装及使用Rime输入法 -- 中州韵输入法]
description: "Deepin 安装及使用Rime输入法 -- 中州韵输入法"
tags: [deepin]
categories: [deepin]
---

### 中州韵

聪明的输入法 -- [中州韵](https://rime.im/)

三个平台有三个不同的名字，但使用跟配置都相同。

`Windows` -- 小狼毫

`Mac` -- 鼠鬚管

`Linux` -- 小狼毫

鬼知道为什么起这么多名字 「手动滑稽」😅

### 为什么使用它

正如作者所说的那样，一个输入法不应该只是通过界面的点击就能够简单设置一些操作。而是应该可以根据每个人的使用习惯进行定制，包括每个按键、快捷键、符号。

对于大多数人来说，一个简单的输入法，再加上在线的词库跟同步的用户词库和设置就足够使用。最多也就是寻求一些新鲜主题。但是对于特定场景的用户来说，如果能定制一个根据自己按键习惯的输入法，那么输入也将成为一种享受，更可以提升我们的效率，毕竟人生苦短。

但是，正由于他是可配置的，并且配置文件及词库文件都是通用的，对于那些想要使用，但又不懂的配置的用户来说是一种福音。可以直接把配置好的常用设置跟词库拷贝下来就可以使用。

### 首先当然是安装

**所有安装环境均再 `Linux-deepin` 下进行，`Windows`，`Mac`请到官方文档查看[下载地址](https://rime.im/download/)**

####  `fcitx` or `ibus`

在 `deepin` 上，常见的有两种输入法管理工具，`ibus`、`fcitx` 。

`ibus-rime` 的安装地址 [ibus-rime](http://wiki.deepinos.org.cn/deepin%E6%8A%98%E8%85%BE%E7%AC%94%E8%AE%B0/%E7%AC%AC%E5%85%AD%E7%AB%A0/6.5.html)

`fcitx-rime` 安装地址 [fcitx-rime](http://wiki.deepinos.org.cn/deepin%E6%8A%98%E8%85%BE%E7%AC%94%E8%AE%B0/%E7%AC%AC%E5%85%AD%E7%AB%A0/6.4.html)

安装后体验：

>   1.  `ibus-rime` 的配置跟启动不太好（在我机器上），输入法切换状态栏没有提示（可能是跟ibus的配置冲突，以 `ibus` 的为最高优先级了）。致命问题，自带应用无法输入中文，`Chrome` 浏览器可以输入中文。所以被放弃了，本来是从 `fcitx` 过来的，结果...
>   2.  `fcitx-rime` 用着还算可以，没什么大问题。只是没有找到更改主题跟字体大小的方法，每次更改字体大小，重启或注销后又会恢复原样。

**推荐使用 `fcitx-rime`**

### 接下来就是配置了

用户目录在 ： `$USER/.config/fcitx/rime/`

里面存放自定义配置文件和部署后的数据文件

#### 配置增强包

增强包包括了输入法配置文件跟词典文件与源配置文件，我们可以对比增强包的配置文件跟源文件，来修改为符合自己使用习惯的配置。还包括说明文件 增强包地址[百度云](https://pan.baidu.com/s/1jI1a8eE) [在线下载](https://www.caoayu.xyz/file/rime_pro.zip)

将压缩包解压到 `$user/.cofig/fcitx/rime/` 里，重新部署一下就好了。

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20201204203021.png)



如果是 `ibus` ，用户目录是 ： `/$user/.config/ibus/rime/`

### 自定义配置

如果需要自定义配置，直接更改 用户目录下的 `$user/.config/fcitx//rime/default.costum.yaml` 文件。

对比 `f-customize.txt` 文件来进行修改。`ori_default.yaml`是默认的配置文件，如果改坏了，可以把这个按照这个文件的内容更正回来

官方的一些文档，十分详细明了。

*   [打字入门](https://github.com/rime/home/wiki/UserGuide)
*   [定制指南](https://github.com/rime/home/wiki/CustomizationGuide)
*   [方案设计](https://github.com/rime/home/wiki/RimeWithSchemata)

#### 小问题的解决

1.  字体太小

通过更改 `rime` 的配置文件发现每次重启或注销都会变回来。可以更改 `/$user/.config/fcitx/config/fcitx-classic-ui.config`文件

```bash
[ClassicUI]
...
# 字体大小
FontSize=16
...
# 皮肤名称
SkinType=classic
```

2.  切换中英文，输入的中文不会自动上屏，而是消失

这是因为 `rime` 配置的上屏规则被 `fcitx` 覆盖掉了。通过更改掉`fcitx` 的配置可以解决。

`rime`配置文件中的上屏规则

```bash
 ascii_composer:
    good_old_caps_lock: true
    switch_key:
      Shift_L: commit_code # 左 Shift 上屏，并更改中英文
      Shift_R: commit_code
      Control_L: noop # 左 Ctrl 禁用
      Control_R: noop
      Caps_Lock: noop
      Eisu_toggle: clear # esc 清屏
```

更改 `fcitx ` 配置

首先打开配置，找到全局配置> 额外的激活输入法快捷键，把 默认的 `Shift` 改掉，或直接禁用。

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20201204204759.png)

这里也是可以更改字体大小的，不过我的环境重启会失效。

3.  无法更改皮肤

    只能使用 `fcitx` 默认的皮肤，这个目前没有办法，网上有制作的皮肤放到 `fcitx/skin/` 里的，但有反馈说并不能加载出来。需要自行实验

    推荐一个在线的皮肤定制工具，不需要自己写配置，只需要选择色彩搭配即可[链接](https://bennyyip.github.io/Rime-See-Me/)

如果有解决方法可以评论指教下，十分感谢。

如果有疑问也可以联系我，互相帮助。

