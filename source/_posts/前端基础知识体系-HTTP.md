---
title: 前端基础知识体系-HTTP
date: 2021-5-13 14:46:08
tags: [知识体系,总结,HTTP]
categories: 前端
comment: true
---

## HTTP

### 一些概述

#### 协议族

<!-- ![协议族](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210519/协议族.67t12xe7dqc0.jpg) -->
![协议与关系](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210609/tcpip模型.7chl0ezw1yk0.png)

#### TCP/IP分层关系

![TCP_IP分层管理](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210519/TCP_IP分层管理.54t88fkfpqo0.svg)

#### HTTP-域名DNS

![域名DNS](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210519/域名-DNS.4d5r9wqscuk0.svg)

#### TCP-三次握手

![三次握手](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210519/三次握手.fkv7jm9382w.svg)

#### TCP-四次挥手

![TCP-四次挥手](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210519/TCP-四次挥手.359b88pd8y00.svg)

#### TCP/UDP的区别

##### TCP

> 传输控制协议（Transmission Control Protocol）。

两台设备通讯时，通讯畅通且可靠，才能保证数据正确发送接收，于是用到了TCP。

![三次握手](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210609/tcpgif.78u1abq6cq40.gif)

TCP的连接和断开需要三次握手和四次挥手（TCP是全双工所以双向需要FIN/ACK），具体见上图。

特点：

1. **面向连接**。
2. **仅支持单播**。
3. **面型字节流**。
4. **可靠**。
5. **提供拥塞控制**。
6. **提供全双工通信**。

##### UDP

> 用户数据报协议（User Datagram Protocol）。UDP又不提供数据报分组、组装和不能对数据报排序的缺点，也就是说，当报文发送之后，无法得知是否安全送达。

特点：

1. **面向无连接**。（不像TCP需要三次握手啥的，UDP协议在发送端加一个UDP头，接收端去掉UDP头，不做任何拼接）
2. **支持单播，多播，广播**。（UDP支持一对多，多对多，多对一）
3. **不可靠**。（首先没有连接；然后发送方不关系接收方是否正确收到数据；网络较差时容易丢包）
4. **UDP面向报文**，（UDP对应用层叫下来的报文，既不合并也不拆分，只是加一个UDP头，所以应用程序必须选择合适大小的报文。）
5. **头部开销小，传输效率高**。（所以在**实时性**要求较高要用**UDP**而非TCP。）

##### 总结

![tcp/udp区别](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210609/tcpudp.5a6m17xhecc0.png)

#### ARP中转

在**以太网环境**中，**数据的传输**依赖的是**MAC地址**而非IP地址。而将IP地址转换为MAC地址的工作由ARP协议完成。

![IP-ARP中转](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210519/IP-ARP中转.33p6qwujdxo0.svg)

#### HTTP协议关系

![协议与HTTP关系](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210519/协议与HTTP关系.53dqcqqibck0.svg)

### HTTP协议

HTTP是一种不保存状态，即无状态（stateless）协议。协议对于发送过的请求或者响应都不做持久化处理。

### HTTP方法

1. GET：获取资源
2. POST：传输实体主体
3. PUT：传输文件（像FTP上传文件；204 No Content）
4. HEAD：获取报文首部（确认URI的有效性，资源更新时间等）
5. DELETE：删除文件（204 No Content）
6. OPTIONS：询问支持的方法
7. TRACE：追踪路径
8. CONNECT：要求用隧道协议连接代理（要求在代理服务器进行通信时建立隧道，实现用隧道协议进行TCP通信（SSL、TLS））

### HTTP持久化连接

[HTTP长连接--Keep-Alive](https://www.cnblogs.com/thomson-fred/p/10197456.html)

[HTTP keep-alive和TCP keepalive的区别，你了解吗？](https://zhuanlan.zhihu.com/p/224595048)

### 提升传输速率

#### 内容编码

1. gzip(GNU zip)
2. compress(UNIX 系统的标准压缩)
3. deflate(zlib)
4. identity(不进行编码)

#### 分块传输编码

Chunked Transfer Coding

### HTTP首部

HTTP协议的请求和响应报文中必定包含HTTP首部。首部内容为客户端和服务器分别处理请求和响应提供所需的信息。

![HTTP首部](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210519/报文首部.5hkgqftqqi80.svg)

#### 通用首部字段

![通用首部字段](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210519/通用首部字段.5flyq58hxzg0.png)

#### 请求首部字段

![请求首部字段](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210519/请求报文字段.2sikga81itk0.png)

#### 响应首部字段

![响应首部字段](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210519/响应首部字段.5tkmfvh600c0.png)

#### 实体首部字段

![实体首部字段](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210519/实体首部字段.66kx1xun6r80.png)

### HTTP状态码

#### 1xx（临时响应）

1. 100：请求者应当继续提出请求
2. 101：切换协议。请求者已要求服务器切换协议，服务器已确认并准备切换。

#### 2xx（成功）

1. 200：请求成功并返回正确接口结果
2. 201：请求成功并成功创建了资源
3. 202：服务器已接收请求，但尚未处理。（可轮训继续请求）
4. 203：非授权信息，服务器已成功处理请求，但返回的结果可能来自另一个源
5. 204：无内容，服务器已成功处理请求，但没有返回任何内容（PUT）
6. 205：成功处理了请求，无内容，且要求请求者重置文档视图
7. 206：服务器已经处理了部分GET请求。（Range）

#### 3xx（重定向）

1. 300：Multiple Choices，请求成功，请求的资源可包括多个位置
2. 301：请求成功，但资源被永久转移到新位置
3. 302：临时转移，请求的网页临时跳转到其他页面，临时重定向
4. 303：对应当前请求的响应可以在另一个 URI 上被找到，且使用GET访问新的地址来获取资源
5. 304：请求的资源没有被修改过（缓存）
6. 305：使用代理，请求者应该使用代理访问该页面
7. 306：临时转移，在最新的规范中，已经不使用

#### 4xx（请求错误）

1. 400：请求出现错误（语义有误或者请求参数有误）
2. 401：没有提供认证信息（Authorization）
3. 402：预留状态码
4. 403：服务器理解请求，但拒绝执行
5. 404：请求的资源未在服务器上发现
6. 405：方法禁用，服务器禁用了请求指定的方法（Option）
7. 406：请求的资源的内容特性无法满足请求头中的条件，因而无法生成响应实体
8. 407：请求者需要代理授权（Proxy-Authenticate）
9. 408：服务器请求超时
...

#### 5xx（服务器错误）

1. 500：服务器内部错误，无法完成请求
2. 501：请求还没有被实现，服务器不具备完整的请求功能
3. 502：错误网关，服务器作为网关或者代理，从上游服务器收到无效响应
4. 503：服务不可用（超载或维护）
5. 504：网关超时，服务器作为网关或者代理，没有及时从上游收到请求
6. 505：HTTP协议版本不支持

#### 其他

查看完成一些的请挪步：[HTTP状态码](https://www.php.cn/course/1020.html)

### HTTPS

HTTPS被称为：HTTP Secure（超文本传输安全协议）或HTTP Over SSL。

#### HTTP的缺点

1. 通信使用明文（不加密），内容容易被窃听 =》SSL/TLS
2. 不验证 通信方的身份，可能遭遇伪装 =》证书
3. 无法验证 报文完整性，有可能已经被篡改 =》PGP/MD5/SHA-1

#### 通信加密

1. SSL(Secure Socket Layer，安全套接层)
2. TLS(Transport Layer Security，安全传输层协议)

#### 总结

HTTP + 加密（SSL/TLS） + 认证（证书）+ 完整性保护（PGP/MD5）= HTTPS

![HTTPS通信](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210525/HTTPS通信.ow4xdx27a1c.svg)

![xxx](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210603/位置关系.4a8awb9ipbg0.svg)
