---
title: "博客整体风格更改"
date: 2020-08-06T22:54:55+08:00
draft: false
tags: [hugo]
categories: [hugo]
---

### 原主题问题

使用`even`的`github`最新主题,发现更改主题样式无法生效,很多样式的更改都无法失效 :sob: ,最终查阅`issuse`得知更改`src`目录后需要使用`npm run build`命令重新编译`scss`文件,但是`github`上最新的源码没有`src`文件夹,应该是配置好`package.json`就可以使用了,但我一开始将无用的文件都剔除了,所以直接到`release`里下载了一份,[下载地址](https://github.com/olOwOlo/hugo-theme-even/archive/v4.0.0.zip)这个是有`src` 目录的

**注意,修改了`src`目录下的文件后记得在`src`目录下执行`npm run build` 重新编译**

### 更改整体配色

代码风格跟主题颜色由原来的红色改为了薄荷绿 :joy: ,在高分辨率屏幕上看红色有种暗的感觉,换为绿色后背景有由原来的些许红色改为了白色,整个样式风格看起来素净了许多 :smile: 

### 更改文章的字体间距

原先的文章看起来总感觉密密麻麻全是字,在碰到文字居多的文章时更看的头皮发麻,所以更改了一下文章内容的字体间距

通过浏览器开发者工具找到文章的`class`,然后在源码中全局搜索,大概是在`src/css/_partial/_post/_content.scss`文件中,新增下面样式即可

```Sass
letter-spacing: .05em;
```

### 添加下拉子菜单

本主题不支持下拉子菜单,`hugo`的子菜单形式只需要在 `menu`属性中加入`parent`属性就可以归类为某一个菜单的子菜单,属性值必须是父级的`identifier`

#### 使用`css`添加下拉菜单样式

找到`custom.scss`文件,将下拉菜单的样式添加进去. 位置`even/src/css/_custom.scss`

```Scss
.dropbtn {
    background-color: white;
    color: black;
    padding: 16px;
    font-size: 16px;
    border: none;
    cursor: pointer;
}

.dropdown {
    position: relative;
    display: inline-block;
}

.dropdown-content {
    display: none;
    position: absolute;
    background-color: #f9f9f9;
    min-width: 160px;
    box-shadow: 0px 8px 16px 0px rgba(0, 0, 0, 0.2);
}

.dropdown-content a {
    color: black;
    padding: 12px 16px;
    text-decoration: none;
    display: block;
}

.dropdown-content a:hover {
    background-color: #f1f1f1
}

.dropdown:hover .dropdown-content {
    display: block;
}

.dropdown:hover .dropbtn {
    background-color: #3e8e41;
}
```

#### 更改前端样式菜单栏,加入子菜单逻辑

文件位置 `even/layouts/partials/header.html`

```HTML
<div class="logo-wrapper">
  <a href="{{ "/" | relLangURL }}" class="logo">
    {{- if .Site.Params.logoTitle -}}
    <img src="/images/jay.jpg" class="logo-img" alt="logo-img">
    {{ .Site.Params.logoTitle }}
    {{- else -}}
    {{ .Site.Title }}
    {{- end -}}
  </a>
</div>

<nav class="site-navbar">
  {{ range .Site.Menus.main -}}
  <div class="dropdown">
    <a href="{{ .URL | safeURL }}" class="menu">{{ .Name }}</a>
    <div class="dropdown-content">
      {{ if .HasChildren }}
      {{ range .Children }}
      <a href="{{ .URL | safeURL }}" class="menu-item">{{ .Name }}</a>
      {{ end }}
      {{ end }}
    </div>
  </div>
  {{- end }}
</nav>
```

#### 添加子菜单

添加子菜单有两种方式

1. 在配置文件中添加,修改`config.toml`

   ```TOML
   [[menu.main]]
   name = "搜索"
   weight = 400
   identifier = "search"
   url = "/search"
   parent = "about"
   
   [[menu.main]]
   name = "友情链接"
   identifier = "friends"
   url = "/about/friends"
   parent = "about"
   weight = 300
   
   [[menu.main]]
   name = "联系我"
   identifier = "call-me"
   url = "/about/call-me"
   parent = "about"
   weight = 200
   ```

2. 在`markdown`文件中添加`menu`标签, (官方文档是这样,但测试后并为生效)

   ```Markdown
   menu:
     docs:
    	parent: 'extras'
       	weight: 20
   ```
   

此时,将鼠标放在有子菜单的菜单上时, 即可以呼出下面的子菜单



> 参考文章: [css下拉菜单--菜鸟教程](https://www.runoob.com/css/css-dropdowns.html)

