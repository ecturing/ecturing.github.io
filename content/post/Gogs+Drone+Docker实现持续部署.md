---
title: Gos+Drone+Docker实现持续部署
date: 2023-04-11 12:14:45
categories:
- 
tags:
- CI/CD
- 后端
- docker
---
# Gos+Drone+Docker实现持续部署

使用Docker-Compose实现Gos+Drone+DockerRunner的部署

```yaml
version: "3"

services:
  gogs:
    restart: always
    image: gogs/gogs
    container_name: gogs
    ports:
      - "6022:22"
      - "6023:3000"
    volumes:
      - /var/gogs:/data/gogs #配置文件映射
      - /home/gogs/data:/data/git/gogs-repositories #Git仓库映射

  drone-server:
    image: drone/drone:2
    container_name: drone-server
    environment:
      - TZ=Asia/Shanghai
      - DRONE_GOGS_SERVER=http://gogs:3000 #Gogs服务地址
      - DRONE_GIT_ALWAYS_AUTH=false #可选的布尔值将 Drone 配置为在克隆公共存储库时进行身份验证
      - DRONE_RPC_SECRET=XXXXXXXXXXXXXXX
      - DRONE_SERVER_HOST=drone.ecturing.com #提供外部主机名称
      - DRONE_SERVER_PROTO=http #协议方案
    restart: always
    ports:
      - "8080:80"
      - "4443:443"

  drone-runner:
    image: drone/drone-runner-docker:1
    container_name: drone-runner
    environment:
      - DRONE_RPC_PROTO=http
      - DRONE_RPC_HOST=drone.ecturing.com #Drone地址
      - DRONE_RPC_SECRET=XXXXXXXXXXXXXXX #与Droen通讯的密钥
    restart: always
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    depends_on:
      - drone-server

```

使用服务器IP+6023端口进行远程登录，登录后会进行首次配置，我选择内置数据库为sqlite，注意HTTP克隆地址与SSH克隆地址的地址，填写服务器内网IP地址+对应映射的端口号。若填写错误，进入宿主机的/var/gogs文件夹删除所有文件，再次启动进行重新配置。

## 常见问题

1. docker-compose执行down操作后为何Git仓库数据丢失？

   docker-compose执行的是删除容器操作，未持久化的容器数据会丢失！挂载Gogs容器的/data/git/gogs-repositories文件到宿主机器，实现数据持久化

2. docker-compose执行down操作后Drone登录显示未授权用户？

   进入Gogs管理页面，点击用户设置，授权应用，点击删除令牌，然后在Drone登录界面再次申请登录，即可成功