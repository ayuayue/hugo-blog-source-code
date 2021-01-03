---
title: hexo初始化
comment: true
date: 2020-04-19T19:11:42+08:00
tags: [hexo]
categories: [前端]
---
1. 安装`hexo`
```
需要先安装npm,git,配置好npm的淘宝镜像
```
2. 生成`hexo`项目
```
hexo init <folder>
```
<!--more-->
3. 新建一篇文章
```
hexo new [布局文件] "文章标题" //布局文件默认使用 config.yml 中的 default_layout 参数代替
```
4. 新建一个页面
```
hexo new page "title" 
参数: 
    -p : 指定页面路径,如-p about/me
    -r 如果存在同名文章替换
    -s 作为文章发布后的文件名和url
```
5. generate
```
hexo generate 或者 hexo g
参数: 
    -d 文件生成后立即部署网站
    -w 监视文件变动
    -b 生成过程中如果发生任何未处理的异常则抛出异常
    -f 强制重新生成文件
    -c 最大同时生成文件的数量,默认无限制
```
6. publish
```
hexo publish [布局文件] <filename> 发表草稿
```
7. server
```
hexo server //启动服务器,hexo 3.0版本将服务器单独分离,先安装server 才能使用
安装:
    npm install hexo-server --save

参数:
    -p 指定端口,默认4000
    -s 只使用静态文件
    -l 启动日志记录
```
8. deploy
```
hexo deploy //部署网站 简写 hexo d
参数
    -g 部署之前预先生成的静态文件
```
9. render
```
hexo render <file1> [<file2>] ...
参数
    -o 设置输出路径
```
10. migrarte [详细说明](https://hexo.io/zh-cn/docs/migration)
```
hexo migrarte <type> //从其他博客迁移内容
```
11. clean
```
hexo clean //清理缓存文件 (db.json) 和已生成的静态文件(public),更换主题如果不生效,运行此命令
```
12. list
```
hexo list <type> //列出网站资料
```
13. version
```
hexo version //显示版本
```
14. 模式
```
安全模式 hexo --safe //不会载入插件和脚本
调试模式 hexo --debug 终端显示调试信息并记录到debug.log
简洁模式 hexo --silent 隐藏终端信息
```
15. 自定义配置文件路径
```
hexo server --config custom.yml
如果加载多个配置文件 hexo会合并成一个文件,并覆盖掉相同的配置
```
16. 显示草稿
```
hexo --draft //显示source/_drafts文件夹中的草稿文章
```
17. 自定义当前工作目录
```
hexo --cwd /path/to/cwd
```
18. 开启server的问题
```
由于hexo更新去除了一些服务,需要自己检测,手动补充
npm audit fix //检测依赖
按照提示进行安装
```
