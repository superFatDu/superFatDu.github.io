---
title: 前端基础知识体系-JS-WEB-API(存储)
date: 2021-5-12 11:14:02
tags: [知识体系,总结,JS,JS-WEB-API]
categories: 前端
comment: true
---

## 存储

在[前端缓存策略](https://www.duchengwu.top/2021/02/12/前端缓存策略/)中，已经着重讨论过HTTP缓存，这里主要讨论浏览器存储。为了对比cookie，这里会加入session。

![Front-end Cache](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210427/前端缓存策略.5j8tmy109680.svg)

### cookie

储存在用户本地终端上的数据，cookie实际上就是一小段的文本信息。

客户端请求服务器，如果服务器需要记录该用户状态，就使用response向客户端浏览器颁发一个cookie。客户端浏览器会把cookie保存起来。当浏览器再请求该网站时，浏览器把请求的网址连同该cookie一同提交给服务器。服务器检查该cookie，以此来辨认用户状态。

#### cookie机制

![cookie机制](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210517/cookie机制.6zmb6ee1bow0.jpg)

#### cookie属性

![cookie属性](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210517/cookie属性.4gj2cmj3t0q0.png)

##### Name/Value

cookie的名称和值，比如{ dotcom_user: 'superFatDu' }。

##### Domain

决定cookie在哪个域是有效的，也就是决定在向该域发送请求时是否携带此cookie，Domain的设置是对子域生效的。

如Doamin设置为 .a.com,则b.a.com和c.a.com均可使用该cookie，但如果设置为b.a.com,则c.a.com不可使用该cookie。

##### Path

表示cookie所在的目录，默认在根目录'/'，所有页面都能访问。

如果cookie1的path设置为'/test'，cookie2的path设置为'/test/sub'，那么'/test'下的所有页面都能访问cookie1，但'/test'和除'/test/sub'的子页面都不能访问cookie2。

##### Expires/Max-Age

cookie的有效期。

Expires是该cookie被删除时的时间戳（服务器时间）；Max-age也是cookie的有效期，但它的单位为秒，即多少秒之后失效。

##### Size

cookie的大小。各个浏览器的最大Size不尽相同，大约为4kb左右。

在所有浏览器中，任何cookie大小超过限制都被忽略，且永远不会被设置。

##### HttpOnly

如果cookie中设置了HttpOnly属性为true，那么通过js脚本将无法读取到cookie信息，这样能有效的防止XSS攻击，窃取cookie内容，这样就增加了cookie的安全性，即便是这样，也不要将重要信息存入cookie。

##### Secure

cookie的安全属性，若设置为true，则浏览器只会在HTTPS和SSL等安全协议中传输此cookie，不会在不安全的HTTP协议中传输此cookie。

##### SameSite

用来限制第三方 cookie，从而减少安全风险。

1. Strict：完全禁止第三方cookie，跨站点时，任何情况下都不会发送cookie。
2. Lax：稍稍放宽，大多数情况也是不发送第三方 cookie，但是导航到目标网址的 Get 请求除外。
3. None：网站可以选择显式关闭SameSite属性，将其设为None。不过，前提是必须同时设置Secure属性（cookie 只能通过 HTTPS 协议发送），否则无效。

##### Priority

优先级，chrome的提案。当cookie数量超出时，低优先级的cookie会被优先清除。

属性值：Low、Medium（默认值）、High

### session

称为“会话控制”，Session对象存储特定用户会话所需的属性及配置信息。

session是另一种记录客户状态的机制，不同的是cookie保存在客户端浏览器中，而session保存在服务器上。客户端浏览器访问服务器的时候，服务器把客户端信息以某种形式记录在服务器上。

#### session机制

![session机制](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210517/session机制.w0xik787i0w.jpg)

### web Storage

web Storage这里可以分成localStorage和sessionStorage。

web Storage的概念和cookie相似，但是cookie的大小受限制，而web Storage就是为了更大的容量设计的，大约为5M左右。

#### localStorage & sessionStorage

1. localStorage：可持久化数据，除非手动删除，否则一直存在。
2. sessionStorage：仅在当前窗口关闭之前存在。

#### API

1. setItem()
2. getItem()
3. removeItem()
4. clear()

### cookie与session的区别

1. 存放位置：cookie客户端浏览器；session服务器。
2. 安全程度：session比较安全。
3. session在一定时间放在服务器上，访问增多，会占用服务器性能，cookie在客户端则不会。
4. cookie有大小限制，session则没有。
