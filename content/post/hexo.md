---
title: hexo 图片显示问题及使用typora设置图片路径
date: 2020-04-19T22:54:32+08:00
tags: [hexo]
categories: [前端]
---
### 	`hexo`本地图片显示问题

使用`hexo`生成静态资源后,由于`url`的问题会出现图片加载的问题,现在网上的文章及官方的解决方案大概分为三种:

1. 将图片放入`source/images`目录下,每次`generate`都会生成图片,在使用相对或绝对路径进行引用

    <!--more-->

2. 配置`hexo`的`_config.yml`文件, 将 `post_asset_folder` 设置为`true`, 这样每次new 生成一个文章时都会同步生成一个同名的文件夹,然后设置相对或绝对路径.

3. 使用`hexo`官方的解决方案,使用模版变量, `{% asset_img slug [title] %} `

但是在配置过程中发现这三种方式都多多少少存在一些问题,前两中首页跟内容页会有一个加载失败的问题,而第三种则失去了`markdown`的意义.

解决方法:

```
设置post_asset_folder  为 true, 安装插件 asset-image
npm install https://github.com/CodeFalling/hexo-asset-image
设置图片为相对路径
hexo clean && hexo generate && hexo s 运行查看
```

效果如下:

![1587305726201](/images/hexo/1587305726201.png)

---
`hexo s` 后测试

![1587393432704](/images/hexo/1587393432704.png)

----

配置`typora`进行本地图片的粘贴及正常显示

设置`typora`,图像

![1587393533151](/images/hexo/1587393533151.png)

​	以后直接粘贴图片就可以自动保存到 hexo 配置的 post_asset_folder 文件夹里,自动渲染了

**注意修改图片路径中的 \ 为 / ,并且不带 `.`或者`./` 直接写`目录/图片`**

**此种方式如有文件夹中存在中文则会`url`转码,也会显示不正常,解决方法: `new `文件时使用英文名,手动设置文章标题即可**
