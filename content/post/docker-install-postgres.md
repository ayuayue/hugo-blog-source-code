---
title: "Deepin 安装Postgres"
date: 2020-11-24T23:44:35+08:00
lastmod: 2020-11-24T23:44:35+08:00
draft: false
keywords: [Docker 安装Postgres]
description: "Docker 安装Postgres"
tags: [Docker]
categories: [Docker]

---

### `Postgres` 

`PostgreSQL: The World's Most Advanced Open Source Relational Database`

### 使用 `Deepin` 本机安装

`deepin20` 官方仓库中默认的是 `postgres11` 版本

1.  安装 `postgres` 

```bash
# 首先卸载旧版本或配置错误的版本
sudo apt remove postgresql --purge # purge 连同配置文件一起删除
sudo apt install postgresql
# psql version 检查安装是否成功 
# service psotgresql status 检查 postgres 状态
```

2.  连接

`postgres` 安装完成默认存在一个 `postgres` 数据库

```bash
psql -U postgres -h 127.0.0.1 -p 5432 -d postgres
# -U 用户名 -h 连接地址 -p 端口 -d 数据库名
```

如果连接失败或者说用户不存在则使用 `postgres` 用户登录

```bash
sudo su - postgres # 切换到 postgres 用户
psql # 登录
\password postgres # 更改 postgres 用户的密码，然后输入两次密码
\q # 退出控制台
```

3.  再次连接

这时，我们再通过上面的命令或者使用 `dbeaver` 进行连接都可以连接成功了。

4.  新建用户

```bash
adduser dbuser # 系统中新建一个用户
psql -U postgres -h 127.0.0.1 -p 5432 -d postgres # 登录进控制台
CREATE USER dbuser WITH PASSWORD 'dbuser'; # 创建数据库用户 dbuser 为密码
CREATE DATABASE mydb OWNER dbuser; # 创建数据库，并指定所有者为 dbuser
GRANT ALL PRIVILEGES ON DATABASE mydb TO dbuser # 将 mydb 的所有权限赋予 dbuser 用户
\q # 退出
psql -U dbuser -h 127.0.0.1 -p 5432 -d mydb # 登录验证
```

此时我们可以使用 `dbeaver` 连接，使用 `dbuser` 用户名 `mydb` 数据库。

5.  导入 `sql` 文件

    1.  导出 `sql` 文件

    ```bash
    pd_dump -U postgres -h 127.0.0.1 -p 5432 -d postgres > ./postgres.sql
    ```

    2.  导入 `sql` 文件

    ```bash
    psql -U postgres -d postgres -h 127.0.0.1 -p 5432 < ./postgres.sql
    ```

### 使用 `postgres` 官方源安装 `postgres12`

首先卸载旧版本或配置错误的版本

`sudo apt remove postgresql --purge # purge 连同配置文件一起删除`

1.  创建官方源配置文件

```bash
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt buster-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
```

2.  导入签名

```bash
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```

3.  更新本地软件仓库

```bash
sudo apt update
```

4.  安装

```bash
 apt-get install postgresql-12
```

`postgres` 官方的仓库包含下面的一些软件

*   `postgresql-client-12`
*   `postgresql-12`
*   `postgresql-contrib-9.x`
*   `libpq-dev`
*   `postgresql-server-dev-12`
*   `pgadmin4`

`pgadmin` 是 `postgres` 的一个客户端，可以在 `deepin` 中使用。不过推荐一个 `linux` 上更好用的数据库管理软件 `dbeaver` ，可以连接大多数关系型数据库跟非关系型数据库，但是对于导入导出自我感觉没有 `navicat` 好用，不过可以使用命令行的导入导出来代替是一样的。

### 使用`Docker`安装

1.  拉取 `postgres` 镜像

```bash
docker pull postgres
# 检查镜像是否拉取
docker images # 或者 docker image ls
```

2.  运行

```bash
docker run -d --name postgres-server -p 5432:5432 -e "POSTGRES_PASSWORD=progres"  postgres
# --name 容器运行的名称 -p 端口映射 -e 环境变量 postgres 镜像名
```

3.  停止 | 重启

```bash
docker stop postgres-server
docker restart postgres-server
```

4.  持久化

如果容器被删除，那么容器中 `postgres` 的数据也会一并被删除，所以我们要将数据保存到本机中，方便数据的备份与恢复。

验证如果不开启持久化是否会丢失数据

4.1 首先以交互模式进入 `docker` 中的 `postgres` ，以上文运行的容器为依据。

```bash
docker exec -it postgres-server bash
```

此时已经进入了容器中的  `postgres` ，使用的是 `bash` 这个`shell` ，接下来我们使用命令行进入数据库中，并添加几条数据，然后将容器停止运行，查看数据是否还在，如果数据还在，那么删除掉这个镜像再查看数据是否还在，如果不在则恰恰说明了容器中的数据并没有被持久化，所以我们需要配置本地的文件对应到容器中的数据存放文件，来实现持久化，保护我们的数据。

### 实验 `docker` 容器的非持久化

```bash
# 我们进入 docker 中的 bash， 登录到 postgres数据库
psql  # 输入这个命令会报错，说没有 root 用户，跟上面在本机上安装是一样的。我们直接切换到 postgres 用户，然后登录，密码是 镜像 -e配置的那个环境变量。
su - postgres
psql # 此时可以进入到数据库,此时会提醒我们使用 help 查看帮助命令
\l # 查看当前的所有数据库
\c postgres # 进入到 postgres 数据库中
\dt # 查看所有的表，第一次查询为空
CREATE TABLE first (id int,name char(50)); # 创建一张表
\dt # 现在可以查询到存在一张 first 表
select tabelname form pg_tables where schemaname='public' # 同样查询 public 下的表，更加直观
insert into first (id,name) values (1,'lili'); # 插入一条记录
```

现在镜像中已经存在了一个表，其中有一条数据，我们可以在宿主机使用命令登录进去查询，也可以使用可视化工具，如 `dbeaver,pgadmin` 等。

现在停止掉运行的容器

```bash
docker stop postgres-server 
docker ps -a # 检查所有的容器，	exited说明已经停止，通过命令或者可视化工具也会连接失败。
```

现在重新启动容器

```bash
docker start postgres-server
```

此时发现，数据跟表都还在。所以容器的停止并不会丢失数据，现在我们将容器删除掉试试，因为当前容器被创建后有自己的存储位置，而这些文件的生命周期是与 `docker` 容器相同的，即时被停止也依旧报存在容器中，当容器被销毁则数据也被删除。所以对于临时的应用使用默认的存储方式即可，对于要求数据的完整及稳定性的应用，应当使用持久化的方式进行报存。

```bash
# 删除运行的容器会报错，首先将容器停止
docker stop postgres-server
docker rm postgres-server # 删除容器
docker ps -a # 发现 postgres-server 没有了，说明删除成功。
```

这次我们重新运行一个新容器，再次查看容器內数据，进而验证标题。

```bash
docker run -d --name postgres-server -p 5432:5432 -e "POSTGRES_PASSWORD=progres"  postgres # 返回一串 id 说明运行成功
```

此时，我们使用可视化工具或者命令行，又或者进入 `docker` 的交互 `bash` ，都可以看到创建的数据不在了，因为容器已经改变了。是一个新的容器。

### 进行持久化

持久化数据也就是使用本地的一个目录来存放容器中的数据，因为数据在本地，所以容器的删除不会影响本地的数据，但是如果本地文件删除掉那就没办法了，所以对于重要数据要定时进行备份。

1.  创建本地卷

```bash
docker volume create pgdata
```

2.  启动容器

```bash
docker run -d --name postgres-server -v pgdata:/var/lib/postgresql/data -p 5432:5432 -e "POSTGRES_PASSWORD=root" postgres
# -v 指定本地卷，用容器中的 /var/lib/postgresql/data 目录来盛放数据，因为这是 postgres 的数据存放文件，我们需要将这个文件映射到我们的主机上，实现持久化。
docker volume inspect pgdata # 使用这个命令我们可以查看 pgdata 卷的详细信息
# 一下是输出
[
    {
        "CreatedAt": "2020-11-26T22:03:19+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/pgdata/_data",
        "Name": "pgdata",
        "Options": null,
        "Scope": "local"
    }
]
# Mountpoint 就是我们主机上的目录，与 docker 容器共享，并且当容器被删除，主机上的数据依旧存在，我们可以在主机中查看 /var/lib/docker/volumes/pgdata/_data 文件中的内容，我们对此目录內进行文件的操作在容器中是会同步更新的。所以可以实现容器內数据库操作，删除容器数据保留。
```

3.  测试持久化

```bash
docker exec -it postgres-server bash
su - postgres
\c postgres
postgres=# create table test1(id int,name char(50));
postgres=# insert into test1 (id,name) values (1,'lisa');
```

现在跟上面测试一样，添加了数据。然后继续删除容器。

```bash
# 删除运行的容器会报错，首先将容器停止
docker stop postgres-server
docker rm postgres-server # 删除容器
docker ps -a # 发现 postgres-server 没有了，说明删除成功。
```

这时容器已经被删除了，但是我们发现主机上的文件依旧是存在的，所以我们运行新容器时指定这个卷就可以使用以前的数据了。

然后使用指定卷持久化的方式进行运行容器

```bash
docker run -d --name postgres-server -v pgdata:/www/docker/volumes/pgdata -p 5432:5432 -e "POSTGRES_PASSWORD=root" postgres
```

哇塞，数据库跟文件又回来了 :smile:。 it 's :sunglasses:

#### 删除持久化文件

对数据进行持久化后，我们可能已经备份了数据，想要删除到主机上的文件，可以执行下面的命令

在 `docker` 中 卷 `volume` 是一等公民，可以直接使用命令操作。

```bash
docker volume rm pgdata
```

这个时候主机上的目录也没有了，数据就被删除了。 :sweat:

**我们如果需要使用持久化的方式复原一个容器是可以实现的，但是不要删除卷 `volume` 否则持久化的数据就会丢失。**