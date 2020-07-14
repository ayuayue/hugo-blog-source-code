---
title: "自定义短标签"
date: 2020-07-14T22:23:23+08:00
draft: false
tags: [hugo]
categories: [hugo]
---

### `Hugo`无法渲染`video`标签

在`markdown`文件中可以使用`video`标签,来完成视频的内嵌,但是`hugo`无法将该标签渲染成为正常的`h5`的`video`标签

### 使用`shortcode` 嵌入视频

`hugo`提供了短标签的形式,可以自定义标签内容,`even`主题自带了几个短标签,其中有 网易云音乐的短标签,使用效果如下:

```html
\{\{< music id="32507039" auto="1" >\}\}  # / 为了转义,不然会渲染
```

{{< music id="32507039" auto="0" >}}

定义文件, 在主题文件夹下 `even/layout/shortcodes/` 该目录下存放的都是短标签,文件名即为标签名

看一下`music` 标签怎么实现的

```html
  {{/*
      ## Music 163
  
      ### Params:
  
      - `id`
  
        required param
        you can extract from music url
        url format "http://music.163.com/#/song?id=3950552"
  
      - Fiddle `auto`
  
        optional param
        default value 0
        you can overwrite it with 1
  
      ### Examples:
  
      - Simple
  
        {{% music "3950552" %}}
        {{% music "3950552" "1" %}}
  
      - Named Params
  
        {{% music id="3950552" %}}
        {{% music id="3950552" auto="1" %}}
  
  */}}
  
  {{- /* DEFAULTS */ -}}
  {{ $auto := "0" }}
  
  {{- if .IsNamedParams -}}
  
    <iframe style="max-width: 100%"
      class="music163"
      frameborder="no"
      border="0"
      marginwidth="0"
      marginheight="0"
      width="330"
      height="86"
      src="//music.163.com/outchain/player?type=2&id={{ .Get "id" }}&auto={{ or (.Get "auto") $auto }}&height=66">
    </iframe>
  
  {{- else -}}
  
    <iframe style="max-width: 100%"
      class="music163"
      frameborder="no"
      border="0"
      marginwidth="0"
      marginheight="0"
      width="330"
      height="86"
      src="//music.163.com/outchain/player?type=2&id={{ .Get 0 }}&auto={{ if isset .Params 1 }}{{ .Get 1 }}{{ else }}{{ $auto }}{{ end }}&height=66">
    </iframe>
  
  {{- end -}}
  
```

### 自定义标签

自己写几个简单的短标签,可以有 b站,h5视频,音频,YouTube,YouTube好像官方支持.

这里以`h5` 的视频标签为例

1. 新建 `video.html`

2. 编辑内容

   ```html
     {{/*
         ## Video mp4 AVC
     
         ### Params:
     
         - `src`
     
           required param
           you can extract from video url
           url format "http://qiniu.caoayu.xyz/picgo-typora-1.mp4"
     
         ### Examples:
     
         - Simple
   
       {{< video src="http://qiniu.caoayu.xyz/picgo-typora-1.mp4" >}}
     
     */}}
   
   
   <iframe src="{{ .Get "src" }}"
       scrolling="no" height="485px" width="930px" frameborder="no" framespacing="0" allowfullscreen="true">
   </iframe>
   ```

3. 使用 ,参数只需要一个`src`, 注释中也写出了用法.测试后是可以正常使用的,不过宽高需要自己调整