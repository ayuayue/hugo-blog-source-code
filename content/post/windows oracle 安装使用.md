---
cssclass:
title: windows oracle 安装使用
tags: [IT/Windows, IT/Oracle]
image-auto-upload: true

date: 2022-10-03 20:29:46
lastmod: 2022-10-25 08:26:55
---
# windows oracle 安装使用
下载地址 [https://www.oracle.com/cn/database/technologies/microsoft-windows.html](https://www.oracle.com/cn/database/technologies/microsoft-windows.html)

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/202109152132714.png)

下载两个文件,将2的对应文件解压到同一个文件夹中

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/202109152133113.png)

管理员打开 setup.exe

稍等一会,如果提示不满足最低要求

修改 stage\cvu\cvu_prereq.xml 对应位置添加

```Bash
     <OPERATING_SYSTEM RELEASE="6.2">
        <VERSION VALUE="3"/>
      <ARCHITECTURE VALUE="64-bit"/>
      <NAME VALUE="Windows 10"/>
      <ENV_VAR_LIST>
        <ENV_VAR NAME="PATH" MAX_LENGTH="1023" />
      </ENV_VAR_LIST>
     </OPERATING_SYSTEM>

  
```

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/202109152137533.png)

1.  关闭电子邮件提醒
2.  创建和配置数据库
3.  桌面类
4.  安装

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/202109152141503.png)

5.  完成后将 安装目录的 bin 目录配置到环境变量

### 使用默认配置连接

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/202109152155046.png)

web 管理地址 [http://localhost:1158/em](https://localhost:1158/em)

口令 sys 密码安装时设置的

### 创建用户并授权登录

```Bash
create user caoayu identified by "caoayu";

grant all privileges to "CAOAYU";

```

连接

```Bash
sqlplus caoayu/caoayu@localhost:1521/orcl
```

### 新建服务名

#### 方法一

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/202109152213839.png)

1.  填入 [chemdb.thinks.net.cn](http://chemdb.thinks.net.cn)
2.  TCP/IP
3.  主机名 caoayu
4.  服务名 [chemdb.thinks.net.cn](http://chemdb.thinks.net.cn) 数据库默认设置
5.  可以测试,也可以取消

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/202109152218179.png)

右上角保存配置

重启oracle服务或者电脑

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/202109152220335.png)

````
lsnrctl start | stop | start  # 监听启动
net stop | start oracleserviceorcl # orcl 服务

```

#### 检测

```Bash
tnsping chemdb.thinks.net.cn
sqlplus caoayu/caoayu@localhost:1521/chemdb.thinks.net.cn
```

#### 方法二

修改tnsnames.ora文件

````