---
title: "Iptables"
date: 2021-09-12T10:11:49+08:00
lastmod: 2021-09-12T10:11:49+08:00
draft: false
keywords: []
description: ""
tags: []
categories: []
---

ip + tables

tables :

1. mangle
2. filter
3. nat

#### 清空规则链

先配置默认的规则链,否则清空后无法远程连接服务器

```bash
iptables -P INPUT ACCEPT # 默认运行
iptables -F # 清空
```

### 链

```bash
# root
iptables -L 
```

-L 参数查询所有的链

-n 带上行号

指定表，默认就是 filter

```bash
iptables -t filter -L
```

![img](https://secure.wostatic.cn/static/xwUCtNYrCb1tS6eBT9cY1X/image.png?auth_key=1631412985-6FdSVDtkJvkHYtnU7pCmrN-0-bc80a6695187ea603fbb1acb7e5d0ff2)

带 docker 的是 docker 创建的链

![img](https://secure.wostatic.cn/static/nXJqb28GWMc758iJUGc655/image.png?auth_key=1631413190-dkYq8oyVHGZSgeG5izNtxy-0-326492a0f0f57541dd277bebca0a6afd)

filter 表默认的链就是 input output forward

input : 输入

output: 输出

forward: 转发

![img](https://secure.wostatic.cn/static/o5pzCWGDM8dep2uLZiDa4V/image.png?auth_key=1631413211-gXCsuBpPXbaVhohQUXWTPT-0-454c60e40e1b0961561c446b655b9bdf)

policy 策略/规则 这条记录说明对于 input 的全部接收

targer : 处理方式，有 accept接收，drop 丢弃

prot : 协议 tcp/udp/all

source : 源地址

destination : 目的地址

### 编辑规则

#### 添加规则

```bash
iptables -t filter -A INPUT -j DROP -p tcp --dport 8081  # fiter 表规则新增一条入站规则，
# 丢弃 tcp 的8081 端口请求，所有的目的地址
```

![img](https://secure.wostatic.cn/static/w1wo6xHoYbxm8YQQRB9mLR/image.png?auth_key=1631413230-cqJpbcWsMvVtjnVebqYq8m-0-da35f6d257cf5aef020ecb5dc47e1e9f)

```bash
iptables -t filter -A OUTPUT -j DROP -p tcp -d 192.168.31.1 # 添加一条出站规则，禁止向 -d
# 后的 ip 发送 tcp 的包
```

#### 删除规则

```bash
iptables -t filter -D INPUT 1 # 1 代表第一条规则链的行号
iptables -nL --line-numbers # 此命令查看行号
iptables -L -nv # 更详细的信息
```

![img](https://secure.wostatic.cn/static/scMBLuSgVnoekjeKBNXMUk/image.png?auth_key=1631413248-eguuqeBn7DXuh9G5ZJkAzW-0-17dc4dd0c7ae037bdddbe90c66b56328)

### 保存规则

```Bash
iptables-save
```

### 恢复规则

```bash
iptablse-restore
```

### 

