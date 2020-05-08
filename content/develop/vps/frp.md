---
title: 使用frp实现内网穿透
date: 2019-11-18
categories: develop
tags:
  - vps
---

> 有时我们的计算机没有公网ip，则可以使用内网穿透技术来实现外网访问我们的计算机的服务。

### 准备

1. 一台有公网ip的机器做frp的服务器。这里使用我的vps。环境为centos7
2. [https://github.com/fatedier/frp](https://github.com/fatedier/frp)
3. 内网机器。这里用的是我的笔记本。环境为ubuntu18

### ssh登录内网机器

1. 服务器运行frps

  * 下载frp
  ```
  # 下载frp
  wget https://github.com/fatedier/frp/releases/download/v0.27.0/frp_0.27.0_linux_amd64.tar.gz
  tar -zxvf frp_0.27.0_linux_amd64.tar.gz
  ```

  * frps.init 服务器配置文件
  ```
  # frps.ini
  [common]
  bind_port = 7000 # 修改为你喜欢的端口号
  ```
  * 开放7000端口或者关闭防火墙
  ```
  firewalld-cmd --add-port=7000/tcp
  firewalld-cmd --reload
  ```

  * 进入frp目录，运行
  ```
  ./frps -c frps.ini
  ```

2. 客户端运行frpc

  * 下载同上
  * 确认开启ssh服务
  ```
  # 启动ssh服务
  systemctl start ssh
  ``` 

  * frpc.ini配置文件

  ```
  # frpc.ini
  [common]
  server_addr = x.x.x.x
  server_port = 7000

  [ssh]
  type = tcp
  local_ip = 127.0.0.1
  local_port = 22 # ssh端口
  remote_port = 6000 # 服务器端口，也要让服务器开启该端口
  ```

  * 运行frpc
  ```
  ./frpc -c frpc.ini
  ```

3. 登录,test是上面客户端的user，x.x.x.x为服务器ip
```
ssh -oPort=6000 test@x.x.x.x
```
