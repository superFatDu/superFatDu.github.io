---
title: 前端基础知识体系-JS-WEB-API(Ajax)
date: 2021-5-11 15:34:57
tags: [知识体系,总结,JS,JS-WEB-API]
categories: 前端
comment: true
---

## Ajax

> Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。

### 原生实现

1. get

    ```js
    const xhr = new XMLHttpRequest()
    xhr.open('get', '/get', true)
    xhr.onreadystatechange = function() {
      if(xhr.readyState === 4 && xhr.status === 200) // TODO
    }
    xhr.send()
    ```

2. post

    ```js
    // post传递的数据
    const data = { name: 'cheng5' }

    const xhr = new XMLHttpRequest()
    xhr.open('post', '/post', true)
    // 设置请求头
    // xhr.setRequestHeader = { 'Content-Type': 'application/x-www-form-urlencoded' }
    xhr.setRequestHeader = { 'Content-Type': 'application/json' }
    xhr.onreadystatechange = function() {
      if(xhr.readyState === 4 && xhr.status === 200) // TODO
      console.log(xhr.responseText) // { 'name': 'cheng5' }
    }
    xhr.send(data)
    ```

#### 方法参数解析

##### open

open(method: string, url: string, async: boolean)

async = true => 异步；async = false => 同步

##### setRequestHeader

如Accept-Charset、Accept-Encoding、Connection、Host等等一些类都是浏览器控制，我们不能手动设置。

一般情况下，我们能设置的是Content-Type：

1. 'application/x-www-form-urlencoded'
2. 'application/json'
3. 'multipart/form-data'

##### onreadystatechage

存有处理服务器响应的函数，每当 readyState 改变时，onreadystatechange 函数就会被执行。

##### readyState

0：【未初始化】请求未初始化（代理被创建，但尚未调用send()方法）

1：【载入】服务器连接已建立（send()方法已调用，正在发送请求）

2：【载入完成】请求已接收（send()方法已执行完毕，已接收全部响应内容）

3：【解析】请求处理中（解析响应内容）

4：【解析完成】请求已完成，且响应已就绪（响应内容解析完成，可在客户端调用）

##### status

1**：请求收到，继续处理

2**：操作成功，分析

3**：完成此请求必须进一步处理

4**：请求包含一个语法错误或不能完成

5**：服务器执行请求失败

##### send

send(data)

将请求发送到服务器。且参数data的格式，需要在setRequestHeader中设置。

### 同源政策

同源策略是一种安全协议，是客户端脚本中中重要的安全度量标准，指一段脚本只能读取同一来源的窗口和文档的属性。

同源：协议、域名、端口 三者必须一致。

#### 同源检测

以http://a.com/test/index.html检测：

http://a.com/dir/page.html // 成功

http://child.a.com/test/index.html // 失败，域名不同

https://a.com/test/index.html // 失败，协议不同

http://a.com:9090/test/index.html // 失败，端口不同

http://domain.com/a.js // 失败，虽然ip相同，但域名不同

#### img/css/js可无视同源政策

```html
<img src=跨域的图片地址 />
<link href=跨域的css地址 />
<script src=跨域的js地址 />
```

### 跨域

当一个请求url的协议、域名、端口三者之间任意一个与当前页面url不同即为跨域。

#### 跨域解决方案

1. 通过jsonp跨域
2. document.domain + iframe跨域
3. location.hash + iframe
4. window.name + iframe跨域
5. postMessage跨域
6. 跨域资源共享（CORS）
7. nginx代理跨域
8. nodejs中间件代理跨域
9. WebSocket协议跨域

##### jsonp

```js
let script = document.createElement('script')
script.type = 'text/javascript'

script.src = 'http://domain.com/login?user=cheng5&callback=onBack'
document.head.appendChild(script)

// 回调函数
function onBack(res) {
  // TODO
}
```

##### CORS

普通跨域请求：只服务端设置Access-Control-Allow-Origin即可，前端无须设置，若要带cookie请求：前后端都需要设置。

1. 前端设置

    ```js
    xhr.withCredentials = true
    ```

2. 后端设置（Node）

    ```js
    res.writeHead(200, {
      'Access-Control-Allow-Credentials': 'true',     // 后端允许发送Cookie
      'Access-Control-Allow-Origin': 'http://www.domain1.com',    // 允许访问的域（协议+域名+端口）
      'Set-Cookie': 'l=a123456;Path=/;Domain=www.domain2.com;HttpOnly'   // HttpOnly:脚本无法读取cookie
    })
    ```

##### 其他

[前端常见跨域解决方案](https://segmentfault.com/a/1190000011145364)

### 封装简易的Ajax

```js
function myAjax(method, url, isAsync, data = null, contentType = null) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest()
    xhr.open(method, url, isAsync)
    if (contentType) xhr.setRequestHeader('Content-Type', contentType)
    xhr.onreadystatechange = function() {
      if (xhr.readyState === 4 && xhr.status === 200) {
        resolve(xhr.responseText)
      } else {
        reject('fail')
      }
    }
    xhr.send(data)
  })
}

myAjax('post', '/api', true).then(res => {
  // TODO
})
```
