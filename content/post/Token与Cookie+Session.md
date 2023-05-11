---
title: Token与Cookie+Session区别
date: 2023-04-20 9:21:30
categories:
- 后端
tags:
- Java
- 后端
---
# Token与Cookie+Session区别

在了解Token与Cookie+Session技术的直接区别之前，需要了解HTTP协议，以下是MDN的介绍：

> HTTP 是无状态的：在同一个连接中，两个执行成功的请求之间是没有关系的。这就带来了一个问题，用户没有办法在同一个网站中进行连续的交互，比如在一个电商网站里，用户把某个商品加入到购物车，切换一个页面后再次添加了商品，这两次添加商品的请求之间没有关联，浏览器无法知道用户最终选择了哪些商品。而使用 HTTP 的标头扩展，HTTP Cookie 就可以解决这个问题。把 Cookie 添加到标头中，创建一个会话让每次请求都能共享相同的上下文信息，达成相同的状态。
>
> HTTP 最早期的模型和 HTTP/1.0 的默认模型，是短连接。每一个 HTTP 请求都由它自己独立的连接完成；这意味着发起每一个 HTTP 请求之前都会有一次 TCP 握手，而且是连续不断的。

在传统Cookie+Session中，当向服务器请求验证后，当登录成功服务端会生成Session，并将SessionId通过Set-Cookie发送给客户端，然后客户端将Cookie保存后，后续每次进行请求时，会携带Cookie一起发送给服务端，服务端验证Cookie中的SessionId后(即鉴权)再予以相应操作。

在Token请求中，与Cookie+Session类似，在向服务器请求验证后，服务器会对当用户生成一个唯一Id即token，这个token即代表着这个用户，服务端将token传输给客户端，后面客户端请求时带上这个token就行，后续请求时，服务端只需要验证token库中是否存在当前token即可完成验证。

注意MDN这一句话：同一个链接中，两个执行成功地请求之间是没有关系的。正因为HTTP的无状态，请求之间无关，在类似WebSocket的长连接中，通常使用Session来管理服务端与客户端的链接，在HTTP下是不需要的。因为无状态，所以每次请求都会携带token，使用在服务端只需要验证token的状态即可实现鉴权，同时使用Token与Session相比，Session中参数更多，保存需要更多的空间，而Token可以是一个字符串或数字，空间占用远小于session所占用的空间。
