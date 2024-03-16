---
title: Shiro安全权限控制框架
date: 2021-11-03 21:21:04
draft: true
categories:
- 框架
tags:
- Java
---
# Shiro安全权限控制框架

> Apache Shiro是一个全面的、蕴含丰富功能的安全框架。

Shiro框架模型

![Shiro](https://atts.w3cschool.cn/attachments/image/wk/shiro/1.png)

## Subject

> Subject：当前参与应用安全部分的主角。可以是用户，可以试第三方服务，可以是Cron 任务，或者任何东西。主要指一个正在与当前软件交互的东西。
>  所有Subject都需要Security Manager，当你与Subject进行交互，这些交互行为实际上被转换为与Security Manager的交互

## Security Manager

> Security Manager：安全管理员，Shiro架构的核心，它就像Shiro内部所有原件的保护伞。然而一旦配置了Security Manager，Security Manager就用到的比较少，开发者大部分时间都花在Subject上面。
>  请记得，当你与Subject进行交互的时候，实际上是Security Manager在背后帮你举起Subject来做一些安全操作。

## Realms

> Realms作为Shiro和你的应用的连接桥，当需要与安全数据交互的时候，像用户账户，或者访问控制，Shiro就从一个或多个Realms中查找。
>  Shiro提供了一些可以直接使用的Realms，如果默认的Realms不能满足你的需求，你也可以定制自己的Realms.

Realms 相当与一个所有合法用户的集合，他的数据可以来源于数据库，内存(Ps: Redis..)

## 工作原理

![](https://atts.w3cschool.cn/attachments/image/wk/shiro/2.png)

从应用程序传入用户的身份和证明，将其封装为UserPasswordToken，调用Subject的login，方法进行校验，然后Security Manager会在Realm中查找相关的用户，并根据查找结果返回结果或抛出异常
