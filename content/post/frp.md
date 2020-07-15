---
title: "使用frp搭建内网穿透"
date: 2020-07-15T20:10:54+08:00
draft: false
tags: [linux]
categories: [linux]
---

### 什么是`Frp` 

`frp `是一个高性能的反向代理应用，可以帮助您轻松地进行内网穿透，对外网提供服务，支持 `tcp, http, https` 等协议类型，并且 `web` 服务支持根据域名进行路由转发

`frp`的用途很多，本文只做基础的内网穿透应用

### 如何搭建

想要搭建一个`frp`服务，需要服务器端和客户端安装`frp`应用，通过`frp`应用进行通信，安装在服务器的叫做`frps`，安装在客户端的叫`frpc`，**注意，客户端和服务端的版本要一致，因为同一个包中同时包含客户端和服务端，如果客户端和服务端的系统 一样，可以用同一个包，不然就要用同一个版本的包**

#### 流程

1. 在服务器安装`frpc`，本文使用宝塔进行安装和配置，更加快捷直观。

   ![20200715202503](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200715202503.png)

2. 安装后进入设置，去`github`按照自己服务器的架构下载离线包，导入进去，如果导入后发现无法执行，可以点击在线安装，如果没反应就多点几次

   ![image-20200715204242513](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200715205427.png)

3. 安装完成后进行配置

   ![image-20200715233324355](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200715233325.png)

   ![image-20200715233505096](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200715233507.png)

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200715233604.png)

 4. 也可以直接修改配置文件

    ![image-20200715234953623](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200715234954.png)

 5. 配置完成后，需要放行对应的端口才可以正常的使用，可以使用宝塔进行端口放行，如果是阿里云的服务器，还需要进入服务商的控制台进行再次放行

    放行端口为： `7000，7001，7500，808，909`

### 客户端使用

客户端下载好对应的版本解压后在命令行中进行启动，可以指定配置文件，默认是有一个配置文件`frpc.ini`，`local_port` 一般设置为 80

![image-20200715235434365](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200715235436.png)

登录服务器的`frp web`面板，可以看到连接者的信息

![image-20200715235643609](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200715235645.png)

本地跑一个80端口的服务，在服务器上就可以进行端口映射的访问了

![image-20200716001413919](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200716001536.png)