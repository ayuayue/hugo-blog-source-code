---
cssclass:
title: iptables
tags: [IT/Centos, IT/运维, IT/Linux]
image-auto-upload: true
date: 2022-10-03 20:53:46
lastmod: 2022-10-25 08:26:49
---
# iptables
ip + tables

tables :

1.  mangle
2.  filter
3.  nat

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

![](https://secure2.wostatic.cn/static/xwUCtNYrCb1tS6eBT9cY1X/image.png)

带 docker 的是 docker 创建的链

![](https://secure2.wostatic.cn/static/nXJqb28GWMc758iJUGc655/image.png)

filter 表默认的链就是 input output forward

input : 输入

output: 输出

forward: 转发

![](https://secure2.wostatic.cn/static/o5pzCWGDM8dep2uLZiDa4V/image.png)

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

![](https://secure2.wostatic.cn/static/w1wo6xHoYbxm8YQQRB9mLR/image.png)

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

![](https://secure2.wostatic.cn/static/scMBLuSgVnoekjeKBNXMUk/image.png)

### 保存规则

```Bash
iptables-save
```

### 恢复规则

```bash
iptablse-restore
```

## 动态给docker容器增加端口

[https://kubesphereio.com/post/the-iptables-directive-is-a-guide-to-mapping-ports-inside-containers-to-ports-on-external-hosts/](https://kubesphereio.com/post/the-iptables-directive-is-a-guide-to-mapping-ports-inside-containers-to-ports-on-external-hosts/)

查看端口映射

```Batch
sudo iptables -t nat -vnL
```

查看容器IP

```Batch
docker inspect containerId |grep IPAddress
```

增加映射

```Batch
iptables -t nat -A DOCKER -p tcp --dport 9501 -j DNAT --to-destination 172.18.0.5:9501
iptables -t nat -A POSTROUTING -j MASQUERADE -p tcp --source 172.18.0.5 --destination 172.18.0.5 --dport 9501
iptables -A DOCKER -j ACCEPT -p tcp --destination 172.18.0.5 --dport 9501

```

```Bash
iptables -t nat -A DOCKER -p tcp --dport ${YOURPORT} -j DNAT --to-destination ${CONTAINERIP}:${YOURPORT}

iptables -t nat -A POSTROUTING -j MASQUERADE -p tcp --source ${CONTAINERIP} --destination ${CONTAINERIP} --dport ${YOURPORT}

iptables -A DOCKER -j ACCEPT -p tcp --destination ${CONTAINERIP} --dport ${YOURPORT}
```

修改或删除,通过规则行号

```Bash
iptables -t nat -vnL DOCKER --line-number 
iptables -t nat -D DOCKER 3 # 删除

```

#### 背景：

docker run 某个容器，忘记了 - p/-P 映射端口操作时，怎么把容器端口映射到主机上呢？以下描述的是如何通过 iptables 指令把容器端口映射到外部宿主机端口操作，防止容器重新创建。 宿主机 docker 启了一个容器，在容器里面又部署了一个 pod，而部署 pod 这个服务是后续操作的，宿主机 docker 启容器时没有把端口映射出来，如何通过宿主机去访问 pod 服务。

## 1、相关联的认知

-   docker run -p: 指令中的小 p 为具体的宿主机端口映射到容器内部开放的网络端口上。
-   docker run -P: 指令中的大 P 为随机选择一个宿主机端口映射到容器内部开放的网络端口上。
-   docker run -p: 可以绑定多 IP 和端口（跟多个 - p）。
-   kubectl expose –type=nodePort: 指令将容器内部端口映射到主机上 (宿主机为随机端口，范围 30000-32767 / 在 service 中编辑修改为具体端口)。
-   kubectl expose –type=lb: 指令，为直接将容器服务暴露出去。
-   kubectl ingress: 它允许你基于路径或者子域名来路由流量到后端服务，7 层协议 http/https。
-   kubectl port-forward: 将容器端口转发至本地端口，也可以转发 TCP 流量。
-   kubectl kube-proxy: 只能转发 HTTP 流量。

## 2、docker 与 iptables 关系

源地址变换规则、目标地址变换规则、自定义限制外部 ip 规则、docker 容器间通信 iptables 规则、docker 网络与 ip-forward 和具体的用 iptables 指令将容器内部端口映射到外部宿主机端口操作指令。

### 2.1、源 ip 地址变换规则

-   Docker 安装完成后，将默认在宿主机系统上增加一些 iptables 规则，以用于 Docker 容器和容器之间以及和外界的通信，可以使用 iptables-save 命令查看。
    
-   其中 nat 表中的 POSTROUTING 链有这么一条规则
    
    -A POSTROUTING -s 172.17.0.0/16 ! -o docker0 -j MASQUERADE
    
    参数说明：
    
    -s ：源地址172.17.0.0/16
    
    -o：指定数据报文流出接口为docker0
    
    -j ：动作为MASQUERADE（地址伪装）
    
-   上面这条规则关系着 Docker 容器和外界的通信，含义是： 判断源地址为 172.17.0.0/16 的数据包（即 Docker 容器发出的数据），当不是从 docker0 网卡发出时做 SNAT（源地址转换）。 这样一来，从 Docker 容器访问外网的流量，在外部看来就是从宿主机上发出的，外部感觉不到 Docker 容器的存在。
    

### 2.2、目标地址变换规则

-   从 Docker 容器访问外网的流量，在外部看来就是从宿主机上发出的，外部感觉不到 Docker 容器的存在。那么，外界想到访问 Docker 容器的服务时，同样需要相应的 iptables 规则.
    
-   以启动 tomcat 容器，将其 8080 端口映射到宿主机上的 8080 端口为例，然后通过 iptables-save 查看：
    
    docker run -itd --name tomcat -p 8080:8080 tomcat:latest
    
    # iptables-save
    
    
    *nat
    
    -A POSTROUTING -s 172.18.0.2/32 -d 172.18.0.2/32 -p tcp -m tcp --dport 8080 -j MASQUERADE
    
    ...
    
    *filter
    
    -A DOCKER -d 172.18.0.2/32 ! -i docker0 -o docker0 -p tcp -m tcp --dport 8080 -j ACCEPT
    
-   可以看到，在 nat、filter 的 Docker 链中分别增加了一条规则
    
-   这两条规则将访问宿主机 8080 端口的流量转发到了 172.17.0.4 的 8080 端口上（即真正提供服务的 Docker 容器 IP 和端口）。所以外界访问 Docker 容器是通过 iptables 做 DNAT（目的地址转换）实现的。
    

### 2.3、自定义限制外部 ip 规则

-   Docker 的 forward 规则默认允许所有的外部 IP 访问容器
-   可以通过在 filter 的 DOCKER 链上添加规则来对外部的 IP 访问做出限制
-   只允许源 IP192.168.0.0/16 的数据包访问容器，需要添加如下规则： `iptables -I DOCKER -i docker0 ! -s 192.168.0.0/16 -j DROP`

### 2.4、docker 容器间通信 iptables 规则

-   不仅仅是与外界间通信，Docker 容器之间互个通信也受到 iptables 规则限制。
-   同一台宿主机上的 Docker 容器默认都连在 docker0 网桥上，它们属于一个子网，这是满足相互通信的第一步。
-   Docker daemon 启动参数–icc (icc 参数表示是否允许容器间相互通信) 设置为 false 时会在 filter 的 FORWARD 链中增加一条 ACCEPT 的规则（–icc=true）： `-A FORWARD -i docker0 -o docker0 -j ACCEPT`
-   当 Docker datemon 启动参数–icc 设置为 false 时，以上规则会被设置为 DROP，Docker 容器间的相互通信就被禁止，默认是 ACCEPT。
-   这种情况下，想让两个容器通信就需要在 docker run 时使用–link 选项。

### 2.5、docker 网络与 ip-forward

-   在 Docker 容器和外界通信的过程中，还涉及了数据包在多个网卡间的转发，如从 docker0 网卡转发到宿主机 ens160 网卡，这需要内核将 ip-forward 功能打开
-   即将 ip_forward 系统参数设 1：echo 1 > /proc/sys/net/ipv4/ip_forward
-   Docker daemon 启动的时候默认会将其设为 1（–ip-forward=true）
-   永久生效的 ip 转发 `vim /etc/sysctl.conf` `net.ipv4.ip_forward = 1` `sysctl -p /etc/sysctl.conf`

### 2.6、iptables 指令映射

-   需要执行三条指令，其中就修改两个参数:
    
    iptables -t nat -A DOCKER -p tcp --dport ${YOURPORT} -j DNAT --to-destination CONTAINERIP:{CONTAINERIP}:CONTAINERIP:{YOURPORT}
    
    iptables -t nat -A POSTROUTING -j MASQUERADE -p tcp --source ${CONTAINERIP} --destination ${CONTAINERIP} --dport ${YOURPORT}
    
    iptables -A DOCKER -j ACCEPT -p tcp --destination ${CONTAINERIP} --dport ${YOURPORT}
    
-   ${CONTAINERIP} 就是对应容器的 ip 地址，比如我的容器 ip 地址是 172.18.0.2 ，（容器的 IP 可以通过如下方式查看：a. 在容器中：ip addr;b. 在宿主机中: docker inspect 容器名 |grep IPAddress ）所以我就把上述的参数换成我的 IP 地址。
    
-   ${YOURPORT} 就是要映射出来的端口，我配置的是一个 console 平台，其端口是 30880
    

## 3、注意地方及参考

-   如果容器是 pod 形式启的，上面 iptables 指令映射不适合，其中有对 docker 链的操作。
-   [映射 port 至存在的 docker 容器](https://stackoverflow.com/questions/19335444/how-do-i-assign-a-port-mapping-to-an-existing-docker-container)
-   [k8s 如何访问](https://kubernetes.io/zh/docs/concepts/services-networking/service/)