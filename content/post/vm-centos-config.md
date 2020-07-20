---
title: "虚拟机安装Centos后的一些配置"
date: 2020-07-15T21:35:42+08:00
draft: false
tags: [linux]
categories: [linux]
---

### `VMware` 安装 `Centos`

使用`vmware`安装了`centos7`，并去除了不必要的硬件，只保留了比用的几个，安装的是`server with gui`

![image-20200715213828424](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200715213830.png)

安装完成后，又上角链接网络

1. 更换为阿里云的软件源，备份本地源

   ```bash
   mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo_bak
   ```

2. 下载阿里云的源文件

   ```bash
   wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
   ```

 3. 更新缓存

    ```bash
    yum makecache
    ```

 4. 更新

    ```bahs
    yum -y update
    ```

    

### 解决在高分屏下虚拟机显示比例太小的问题

先关闭虚拟机,打开虚拟机的设置,进入显示器,将`监视器`改为指定监视器设置,最大分辨率自己做调整,默认的一般就是与本机相同,如果显示还是太小可以调整

​	![image-20200720210430586](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/20200720210431.png)