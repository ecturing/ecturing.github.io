---
title: Linux端Clash
date: 2023-07-15 13:02:05
categories:
- Linux
tags:
- 容器化
---


## 安装Clash

使用wget或curl下载Github最新的可执行文件，然后给clash添加可执行权限，并运行文件，等待clash自动配置环境

```shell
chmod +x clash
./clash
```

## 注入系统服务，长期运行

使用systemctl管理。进入/etc/systemd/system目录，新建clash.service文件

```service
[Unit]
Description=simulator

[Service]
User=常使用用户的用户名
Type=simple
ExecStart=可执行文件路径

[Install]
WantedBy=multi-user.target
```

systemctl进行管理

```shell
systemctl enable clash.service
systemctl start clash.service
```

