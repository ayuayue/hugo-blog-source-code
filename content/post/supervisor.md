---
title: "使用 Supervisor 管理进程"
date: 2021-03-21T18:30:31+08:00
lastmod: 2021-03-21T18:30:31+08:00
draft: false
keywords: [使用 Supervisor 管理进程]
description: "使用 Supervisor 管理进程"
tags: [PHP]
categories: [PHP]
---

Linux 的后台程序运行有很多中方法，例如 `nohub、screen 、tmux` 等，但是有些业务对进程的要求比较严格，要成为守护进程，并且可以监控运行状态，最好还可以意外结束后自动重启，`supervisor` 就可以满足这个需求。[官方文档](http://supervisord.org/introduction.html)

**使用 supervisor 监护进程时，被监护的进程不能是守护进程。**

### 安装

```bash
sudo apt install supervisor
```

安装后自动安装 `supervisorctl` 工具，用来管理 `supervisord`  进程。

输出 `supervisorctl` 帮助文档

>add             -- Activates any updates in config for process/group                                           
>avail           -- Display all configured processes                                                            
>clear           -- Clear single/multiple/all process log files                                                 
>fg              -- Connect to a process in foreground mode                                                     
>help            -- Show help                                                                                   
>maintail        -- tail of supervisor main log file                                                            
>open            -- Connect to a remote supervisord process. (for UNIX domain socket, use unix:///socket/path)  
>pid             -- Get the PID of process/supervisord                                                          
>quit      exit  -- Exit the supervisor shell                                                                   
>reload          -- Restart the remote supervisord                                                              
>remove          -- Removes process/group from active config                                                    
>reread          -- Reload the daemon's configuration files                                                     
>restart         -- Restart process, group or all                                                               
>shutdown        -- Shut the remote supervisord down                                                            
>start           -- Start process, group or all                                                                 
>status          -- Get process/group status info                                                               
>stop            -- Stop process, group or all                                                                  
>tail            -- tail of process stdout                                                                      
>update          -- Reload config and add/remove as necessary                                                   
>version         -- Show the version of the remote supervisord process                                          



### 配置文件

安装成功后会在 `/etc/supervisor` 下生成两个目录

>   ├── conf.d
>
>   └── supervisord.conf

```conf
# supervisord.conf
; supervisor config file

[unix_http_server]
file=/var/run/supervisor.sock   ; (the path to the socket file)
chmod=0700                       ; sockef file mode (default 0700)

[supervisord]
logfile=/var/log/supervisor/supervisord.log ; (main log file;default $CWD/supervisord.log)
pidfile=/var/run/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
childlogdir=/var/log/supervisor            ; ('AUTO' child log dir, default $TEMP)

; the below section must remain in the config file for RPC
; (supervisorctl/web interface) to work, additional interfaces may be
; added by defining them in separate rpcinterface: sections
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl=unix:///var/run/supervisor.sock ; use a unix:// URL  for a unix socket

; The [include] section can just contain the "files" setting.  This
; setting can list multiple files (separated by whitespace or
; newlines).  It can also contain wildcards.  The filenames are
; interpreted as relative to this file.  Included files *cannot*
; include files themselves.

[include]
files = /etc/supervisor/conf.d/*.conf
```

这是 `supervisor` 的配置文件，其中定义了日志文件位置、进程位置等信息，最后面的 `include` 就是要添加的进行配置文件位置的定义。

### 添加一个自定义进程

进入到 `/etc/supervisor/conf.g/`  目录下，创建一个进程为名，`conf` 结尾的文件。并输入以下内容

```bash
sudo vim app.conf
[program:tp6] # tp6 为进程名
command=php -S localhost:8000 # 进程命令
directory=/home/caoayu/Github/tp6/public # 目录
user=caoayu # 用户
stdout_logfile=/tmp/tp.out.log # 输出日志位置
stderr_logfile=/tmp/tp.err.log # 错误日志位置
```

关于配置文件的全部内容

>   ```
>   [program:cat]
>   command=/bin/cat
>   process_name=%(program_name)s
>   numprocs=1
>   directory=/tmp
>   umask=022
>   priority=999
>   autostart=true
>   autorestart=unexpected
>   startsecs=10
>   startretries=3
>   exitcodes=0
>   stopsignal=TERM
>   stopwaitsecs=10
>   stopasgroup=false
>   killasgroup=false
>   user=chrism
>   redirect_stderr=false
>   stdout_logfile=/a/path
>   stdout_logfile_maxbytes=1MB
>   stdout_logfile_backups=10
>   stdout_capture_maxbytes=1MB
>   stdout_events_enabled=false
>   stderr_logfile=/a/path
>   stderr_logfile_maxbytes=1MB
>   stderr_logfile_backups=10
>   stderr_capture_maxbytes=1MB
>   stderr_events_enabled=false
>   environment=A="1",B="2"
>   serverurl=AUTO
>   ```

### 运行

首先重载下 `supervisor` 

```bash
sudo supervisorctl reload
```

运行进程

```bash
sudo supervisorctl start tp6 # or sudo supervisorctl start all
```

暂停

```bash
sudo supervisorctl stop tp6 
```

重启

```bash
sudo supervisorctl restart tp6
```

### 使用 `web` 网页可视化管理

首先更改 `supervisor` 的配置文件，更改如下：

```bash
# /etc/supervisor/supervisord.conf
# 加入以下内容
[inet_http_server]
port=127.0.0.1:9001
username=admin
password=123456
```

重载并更新配置

```bash
sudo supervisorctl reload 
sudo supervisorctl update
```

打开配置好的地址与端口查看输出

![image-20210321190540904](https://cdn.jsdelivr.net/gh/ayuayue/cdn/img/image-20210321190540904.png)

用这个可以更直观的对进程进行查看与管理。

