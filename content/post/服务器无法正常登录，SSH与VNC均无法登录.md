---
title: 由于切换Shell导致服务器无法正常登录，SSH与VNC均无法登录
date: 2022-11-26 21:43:53
categories:
- 服务器运维
tags:
- Ubuntu
---
## 故障原因

​	使用chsh命令切换Shell,提示输入密码，输入正确密码后提示错误

## 故障表现

​	SSH端退出再次登录，SSH无法登录，提示PublicKey校验失败。

​	进入服务器管理页面，进入上次使用后未关闭的VNC连接，更换SSH的key，仍然无果。SSH日志内容如下
![](https://pic.imgdb.cn/item/6382246d16f2c2beb1319514.png)

​	注销VNC登录后，进入登录页面，再次登录，登录成功画面闪烁一次，再次提示输入用户名密码登录，重复如此，重启无效

## 解决方法

服务器VNC界面采用VNC登录，发送命令Ctrl+Alt+Del命令重启服务器，登录界面选择Advanced options for Ubuntu

![](https://pic.imgdb.cn/item/6382246d16f2c2beb1319518.png)

进入系统恢复模式

![image-20221126221225147](https://pic.imgdb.cn/item/6382246d16f2c2beb131951d.png)

选择进入超级用户模式

![image-20221126221304081](https://pic.imgdb.cn/item/6382246d16f2c2beb1319525.png)

​	输入以下命令

```bash
vim /etc/passwd
```

​	首行登录shell目录错误，这是系统无法进入的根本问题，因为设置shell命令失效，给予了操作系统启动shell错误位置，导致系统无法启动。更换正确启动shell目录，系统恢复正常
