---
title: Nginx
date: 2022-04-03 15:36:44
tags:
---

# Nginx服务器

> Nginx是一款轻量级的Web 服务器反向代理服务器及电子邮件（IMAP/POP3）代理服务器，在BSD-like 协议下发行。其特点是占有内存少，并发能力强，事实上Nginx的并发能力在同类型的网页服务器中表现较好。

## 在Docker中安装

- 拉取Nginx镜像

- 启动并挂载Nginx的Html,Conf,logs目录到宿主机

```yaml
services:
  nginx:
    container_name: nginx
    image: nginx
    restart: always
    ports:
      - 80:80
    volumes:
      - /home/docker-nginx/conf.d:/etc/nginx/conf.d #配置文件夹
      - /home/docker-nginx/html:/usr/share/nginx/html #静态文件
```


## 反向代理

> 1. 保障应用服务器的安全，使用反向代理实现内部设备的隐藏
> 2. 实现负载均衡，常见于大型软件的集群式部署或微服务部署
> 3. 实现跨域