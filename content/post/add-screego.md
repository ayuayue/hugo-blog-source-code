---
title: "加入在线服务--在线多人共享屏幕"
date: 2020-10-16T15:06:59+08:00
lastmod: 2020-10-16T15:06:59+08:00
draft: false
keywords: [screego,go,在线多人共享屏幕]
description: "加入在线服务--在线多人共享屏幕"
tags: [go]
categories: [go]
---

### `Screego`

[**Screego**](https://github.com/screego/server) 是一个共享屏幕的应用.使用`golang`进行开发,在`readme`中作者吐槽了微软等公司的延迟问题,然后就开源了这个项目.:+1:

这个支持多人共享屏幕,通过浏览器就可以进行共享.所以要在一台服务器上配置好服务以供使用.

> 过去，在使用Microsoft Teams等公司聊天解决方案与同事共享屏幕时，我遇到了一些问题。 我想向他们展示一些我的代码，但是要么流落后了几秒钟，要么质量太差了，以致于我的同事们无法阅读代码。 或两者。

当配置好服务后,通过浏览器就可以打开服务页,通过创建一个房间就可以进行屏幕的共享了,可以选择共享的区域.

将当前房间的链接发送给他人,他人就可以加入进这个房间,从而进行多人的屏幕共享.[在线体验](https://www.caoayu.xyz:5050)

**注意一个房间`id`如果被创建了则无法再创建一个同名的房间**

*一般默认勾选退出后销毁房间*

### 配置

首先下载应用,可以通过 克隆编译的方式安装(需要本地配置好`golang`的环境,也可以直接下载各平台的二进制可执行文件直接运行.

为了屏蔽运行环境的问题,这里使用下载编译完成的文件直接运行.[下载地址](https://github.com/screego/server/releases) -- 根据自己的机器进行选择不同的版本

#### 需要`HTTPS`支持

默认需要 `TLS` 链接,所以需要配置证书. [关于申请并配置证书](https://www.caoayu.xyz/post/acme/)

官方的配置中是使用了 `nginx apache`的反向代理.但是没有配置证书.

这里直接使用了配置文件中证书的位置,不需要配置`nginx等`.但是因为是使用了`80`端口之外的,所以想要配置为`80`端口还是要配置反向代理来监听`5050`端口.

#### 配置文件

下载解压后会有几个文件

![image-20201016152535767](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20201016152544.png)

其中 `screego.config.example` 是配置文件的例子,我们可以新建一个文件名为 `screego.config` 的文件,将`example`文件内的内容粘贴过来更改一些活动项.

```bash
# The external ip of the server.
# Execute the following command on the server you want to host Screego
# to find your external ip.
#   curl 'https://api.ipify.org'
SCREEGO_EXTERNAL_IP= #自己外网服务器ip地址

# A secret which should be unique. Is used for cookie authentication.
SCREEGO_SECRET= # 必填,随机字符串

# If TLS should be enabled for HTTP requests. Screego requires TLS,
# you either have to enable this setting or serve TLS via a reverse proxy.
SCREEGO_SERVER_TLS=true #开始 https
# The TLS cert file (only needed if TLS is enabled)
SCREEGO_TLS_CERT_FILE=/.../fullchain.cer # 证书的位置
# The TLS key file (only needed if TLS is enabled)
SCREEGO_TLS_KEY_FILE=/.../x.key # 证书key的位置

# The address the http server will listen on.
SCREEGO_SERVER_ADDRESS=0.0.0.0:5050 # 端口

# The address the TURN server will listen on.
SCREEGO_TURN_ADDRESS=0.0.0.0:3478

# Limit the ports that TURN will use for data relaying.
# Format: min:max
# Example:
#   50000:55000
SCREEGO_TURN_PORT_RANGE=50000:55000

# If reverse proxy headers should be trusted.
# Screego uses ip whitelisting for authentication
# of TURN connections. When behind a proxy the ip is always the proxy server.
# To still allow whitelisting this setting must be enabled and
# the `X-Real-Ip` header must be set by the reverse proxy.
SCREEGO_TRUST_PROXY_HEADERS=false

# Defines when a user login is required
# Possible values:
#   all: User login is always required
#   turn: User login is required for TURN connections
#   none: User login is never required
SCREEGO_AUTH_MODE=turn

# Defines origins that will be allowed to access Screego (HTTP + WebSocket)
# Example Value: https://screego.net,https://sub.gotify.net
SCREEGO_CORS_ALLOWED_ORIGINS=

# Defines the location of the users file.
# File Format:
#   user1:bcrypt_password_hash
#   user2:bcrypt_password_hash
#
# Example:
#   user1:$2a$12$WEfYCnWGk0PDzbATLTNiTuoZ7e/43v6DM/h7arOnPU6qEtFG.kZQy
#
# The user password pair can be created via
#   screego hash --name "user1" --pass "your password"
SCREEGO_USERS_FILE=./user.txt # 用户文件

# The loglevel (one of: debug, info, warn, error)
SCREEGO_LOG_LEVEL=info

# If screego should expose a prometheus endpoint at /metrics. The endpoint
# requires basic authentication from a user in the users file.
SCREEGO_PROMETHEUS=false

```

一般配置好`https`跟`ip`就可以正常使用了.

#### 增加用户

默认是以游客的身份登录的,可以添加自己定义的用户来登录

```bash
SCREEGO_USERS_FILE=./user.txt # 用户文件
```

这个配置项就是配置用户文件路径,文件内容格式为 : `user1:$2a$12$WEfYCnWGk0PDzbATLTNiTuoZ7e/43v6DM/h7arOnPU6qEtFG.kZQy`

当然应用也提供了生成这种格式的命令

```bash
./screego hash --name "test" --pass "test"
```

就会生成一串字符串,拷贝到配置好的路径文件中即可使用账号密码登录,从而创建房间

```
user.txt # user.txt 的内容如下 -- 每行放一个用户的信息
user:$2a$12$6pjpAwsobIbFZibZM461OdB27a41rQg7nCU7iZQlER6fjmNPFEs.
test:$2a$12$swW1UfZVcNNHBXooA6Tv7e.ZUimlws8f0zJ1dDqp2cU0ZLY8CgVm
```

### 运行

```bash
./screego serve # 监听5050端口运行
```

我们就可以通过浏览器访问了

### 细节优化

1. 将`screego`加入环境变量,方便调用
2. 使用`screen`命令将程序改为后台运行
   1. `screen -R name`  : 开启一个`name`的后台任务,并进入新`bash` ,在此`bash`中运行程序会被放在后台运行
   2. `ctrl + a +ctrl +d ` : 退出当前`bash` 进入到上一个`bash`,可以理解为进入手机中的`home`页
   3. `screen -R  name ` : 可以恢复到后台任务中,进入后台任务那个`bash`环境中去,即手机的多任务页切换.如果 `name` 后台任务不存在则会创建,所以使用 `-R` 指令就可以实现任务的创建和回复,不需要使用 -S 命令.简化操作.
3. 配置反向代理或重定向域名,隐藏端口号