---
title: "Hugo Travis 完结!"
date: 2020-06-20T13:19:56+08:00
draft: false
tags: [hugo]
categories: [hugo]
---
### `hexo-hugo`平台的转移完成！

今天完成了博客遗留下来的一些问题，并配置好了自动推送及`webhooks`，有着`hexo`的基础，配置起来并不难，对于`hexo`的仓库和`webhooks`都选择了保留，只是代替了`github pages`，新建了`coding`仓库

### 自动部署及`webhooks`详情

此处贴出我的配置文件，可以根据自己的项目和目录进行更改，只做参考用

#### travis
```bash
language: go
sudo: required

cache:
  directories:
    - themes

install:
  - wget https://github.com/gohugoio/hugo/releases/download/v0.72.0/hugo_0.72.0_Linux-64bit.tar.gz
  - tar -xzvf hugo_0.72.0_Linux-64bit.tar.gz
  - chmod +x hugo
  - export PATH=$PATH:$PWD
  - hugo version

script:
  - hugo

after_script:
  - cd ./public
  - git config user.name "" # username替换为你的git username
  - git config user.email "" # email替换为你的coding邮箱
  - git init
  - git add .
  - git commit -m "Travis-CI 自动部署"
  # Coding Pages
  - git push --force "https://username:${WHnwvORRxx}@${CO_REF}" master:master # username替换为你的coding用户名
  - git push --set-upstream https://${GIT_TOKEN}@github.com/ayuayue/ayuayue.github.io.git master
  - git push --force "https://${GIT_TOKEN}@github.com/ayuayue/ayuayue.github.io.git" master:master
env:
  global:
    # Coding
    - CO_REF: e.coding.net/caoayu/hugo-blog.git # 这里填入你的coding pages仓库地址
    # Github
```

#### webhooks

```
#!/bin/bash
 

echo ""
#输出当前时间
date --date='0 days ago' "+%Y-%m-%d %H:%M:%S"
echo "Start"
#判断宝塔WebHook参数是否存在
if [ ! -n "$1" ];
then 
          echo "param参数错误"
          echo "End"
          exit
fi
gitPath="/www/wwwroot/hugo-blog"
#判断项目路径是否存在
if [ -d "$gitPath" ]; then
		echo "目录存在并进入目录wwwroot"
		cd $gitPath
		git fetch --all
		git reset --hard origin/master
		git pull
		echo "设置目录组合拥有者"
        sudo chown -R www:www $gitPath
        echo "设置目录权限"
		sudo chmod -R 755 $gitPath
        echo "End"
        exit
else
        echo "该项目路径不存在"
        echo "新建项目目录"
        cd /www/wwwroot/
        echo "克隆最新的项目文件"
		git clone git@e.coding.net:caoayu/hugo-blog.git ./hugo-blog
		echo "设置目录组合拥有者"
        sudo chown -R www:www ./hugo-blog
        echo "设置目录权限"
		sudo chmod -R 755 ./hugo-blog
		cd $gitPath
		git checkout master
        echo "End"
        exit
fi
```

### 关于webhooks

我在以前的文章中有写过`webhooks`的简单使用，这次的改动很小，只是改动了目录结构，并把`nginx`的网站目录改动。