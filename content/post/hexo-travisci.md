---
title: 使用travisCI自动部署hexo博客并使用国内外不同dns
date: 2020-05-01T17:17:42+08:00
tags: [自动化部署]
categories: [travisCI]
---

### 	自动化部署博客到github和coding

#### 准备工作

>需要环境:
>
>1. 可正常使用域名一个
>2. github新建一个与自己账号名同名的仓库,如 ayuayue.github.io,最好除master分支外新建一个hexo分支用来保存hexo的源码,因为源码配置文件会上传到github,所以才会使用下面的变量的方式配置,不然的话token暴露出来很危险
>3. coding 中创建一个团队,继续创建一个devops仓库,仓库民跟自己的账号名相同,如 caoayu
>4. 进行coding管理员的认证, 在右上角团队管理中,进行团队管理员的认证
>5. 获取github和coding的token
>    1. coding中去个人账号,访问令牌中,新建一个令牌,名字为travis,只给第一个权限,然后生成一个token,记录下来,只有第一次可以看,刷新后就只能重新生成了,所以第一次最好保存一下token,github的token也是一样
>    2. github的token获取,右上角头像,settings,devoper setting /persional access token,生成一个token,gengrate new token,名字也设置为travis,生成后记得保存一下

<!--more-->
使用travisCI这个第三方平台进行自动化部署-[地址]( https://travis-ci.com/ )

1. 自动部署到github,coding

首先注册一个账号,使用github账号登陆,授权github,让travis可以有权限访问你的github仓库,

然后选择你`name.github.io`	的仓库 ![](/images/hexo-travisci/1588325073762.png)

点击more options 选择setting,进去配置环境便量

![](/images/hexo-travisci/1588325188064.png)

第一栏是变量名,github的可以自己起一个名字,coding的是固定的再coding里可以看到,

然后进去hexo的配置文件_config.yml

添加部署的远程仓库

![1588325767307](/images/hexo-travisci/1588325767307.png)

然后去配置主题文件夹下的 .travis.yml

```
# 指定语言环境
language: node_js
# 指定需要sudo权限
sudo: required
# 指定node_js版本
node_js: 
  - 10
# 指定缓存模块，可选。缓存可加快编译速度。
cache:
  directories:
    - node_modules

# 指定博客源码分支，因人而异。hexo博客源码托管在独立repo则不用设置此项
branches:
  only:
    - hexo 

before_install:
  - npm install -g hexo-cli

# Start: Build Lifecycle
install:
  - npm install
  - npm install hexo-deployer-git --save

# 执行清缓存，生成网页操作
script:
  - hexo clean
  # - hexo douban -bm
  - hexo generate

# 设置git提交名，邮箱；替换真实token到_config.yml文件，最后depoy部署
after_script:
  - git config user.name "name"
  - git config user.email "xxx@qq.com"
  # 替换同目录下的_config.yml文件中gh_token字符串为travis后台刚才配置的变量，注意此处sed命令用了双引号。单引号无效！
  - sed -i "s/gh_token/${GIT_TOKEN}/g" ./_config.yml
  - sed -i "s/cd_token/${WHnwvORRxx}/g" ./_config.yml
  - hexo deploy
# End: Build LifeCycle

```

其中的变量名以及travis中的变量名修改为你自己的变量名

### 原理及运行过程

我们将hexo的源码放在github仓库的hexo分支下,当我们每次push时 .travis.yml中命令就会被运行,根据文件内容不难理解,push完成后会安装node,以及hexo-cli到github上,然后hexo clean清除public文件,hexo g 生成静态文件,hexo d 部署到我们配置的两个仓库的master分支中去,部署的过程我们可以在 travisCI官网中的个人仓库中里看到 build的过程及结果,如果有错误则会停止构建.最后将public 文件上传到github和coding仓库

我们可以通过访问 http://name.github.io 访问到我们github用户名为仓库的index文件,也就完成了网页的部署

### 配置cname并且将域名国内访问使用国内的dns,国外的使用境外的dns

#### 开始域名的操作

首先解析域名: 

![1588327654033](/images/hexo-travisci/1588327654033.png)



`coding`

![1588327432709](/images/hexo-travisci/1588327432709.png)

`coding`部署完成会生成一个网址,我们也可以进入设置配置自己的域名进行访问

`github`

1. 配置`github`,进入到仓库,选择仓库的设置项

![1588327292883](/images/hexo-travisci/1588327292883.png)

2. 设置自定义域名

![1588327333329](/images/hexo-travisci/1588327333329.png)

最后在source文件夹中新建一个 CNAME文件

里面写上你的域名

____

到这里你就可以试试使用 git add . / git commit / git push 进入到travis看看自动部署的过程及结果,如果有错误根据提示进行修改

### 启示

使用travis可以实现博客的自动部署,那么一定也可以进行自动化的一些脚本,或者测试, 编写好测试脚本,每次push后就可以看到代码的准确性以及覆盖率,可以极大的提高开发的效率,也为后期调试打下了良好的基础.
