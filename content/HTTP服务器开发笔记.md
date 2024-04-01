---
title: "HTTP服务器开发笔记"
date: 2024-03-16T23:09:45+08:00
draft: true
---

## HTTP1.x标准
HTTP标准消息

根据RFC7230的定义，标准消息构成主要有：起始行，标头，主体三个部分。在消息中，起始行后添加一个CRLF，然后对于标头的每一个单元也必须使用一个CRLF进行分割。最后，再次使用一个CRLF符号标识消息主体的开始。

```请求
POST /api/user/login HTTP/1.1       # 请求行
Host: example.com                   # 请求头部
Content-Type: application/json      # 请求头部
Content-Length: 50                 # 请求头部
Authorization: Bearer ABC123XYZ    # 请求头部

{                                 # 消息主体（请求体）
  "username": "testuser",
  "password": "secretpassword"
}
```

```响应
HTTP/1.1 200 OK                     # 状态行
Date: Wed, 21 Oct 2020 10:31:49 GMT  # 响应头部
Server: Apache/2.4.1                # 响应头部
Content-Type: application/json      # 响应头部
Content-Length: 27                  # 响应头部

{                                 # 消息主体（响应体）
  "status": "success",
  "message": "User logged in"
}
```
对于body本身内容并没有类似需要添加CRLF标识符等任何限制，在HTTP中通常使用Content-Length:body_length或Transfer-Encoding:chunked标头
## Golang自定义异常

1. 实现标准错误接口(必须)

2. 错误类型断言

   ```go
   func ErrHandler(e error) {
   	switch err := e.(type) {
   	case NetError:
   		//do
   	case ServerError:
   		//do
   	}
   }	
   ```

   

## Reactor架构
线程池+IO多路复用

## 定值结构体无法常量化
golang不同与其他语言，golang只能常量化值类型。对于结构体不能常量化，即使在结构体内容全部为确定值也不行。

## bufio太多坑

1. ### 原理：

      bufio是一种缓存手段，通过引入缓存，减少IO调用，提升系统性能。

2. ### 使用方法:

      bufio是一种手段，引入了缓存，自然操作对象就是缓存buffer，对buffer对象使用bufio进行处理写入创建writer，读取创建reader。


3. ### bufio.reader及其函数使用方法
​	golang中所有继承io.reader的reader都有一个特性，那就是读数据是会将数据消耗掉的，常见函数如readline，readString函数

实例

```go
func readRequest(r *Router,reader bufio.reader)
```

## 全局日志

全局日志文件坑：
通常使用读取文件时候，都会使用file.close来关闭文件避免内存泄漏。但是全局日志是在随时写入，只有主线程退出，才能关闭文件

## Context

使用context实现部分多线程环境下的参数传递和控制子线程退出还有超时控制。

context接口有几个默认实现：
`emptycontext`:
`cancelcontext`:
`deadlinecontext`：
`outtimecontext`:

context在项目中整体像一颗树，每一个context都是由上一级的context派生，根context通过background方法获取一个空context，所有context派生自根context



```go

```



## http1.1环境下tcp链接复用中如何获取请求和队头阻塞问题

http1.0环境下，对于服务端，tcp链接响应完就关闭，每个请求分别在不同的tcp处理线程，互不干扰。http1.1引入的链接复用来减少tcp的频繁创建与销毁，但是这样出现了一个问题是如何读取该连接上的信息？解决方案很简单，使用bufio.reader包装net.conn，由于bufio.reader本身的特性是：被消费（读取）的数据不会仍然存在于bufio的缓冲区