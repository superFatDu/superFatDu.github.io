---
title: 前端基础知识体系-JS-WEB-API(DOM、BOM)
date: 2021-5-8 15:34:27
tags: [知识体系,总结,JS,JS-WEB-API]
categories: 前端
comment: true
---

## DOM

> DOM（Document Object Model）：文档对象模型，是W3C组织推荐的处理可扩展置标语言的标准编程接口。

![dom-tree](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210511/htmlpage.2zv52ljd0cq0.png)

### DOM节点操作

#### 获取DOM节点

##### 通过元素类型获取

```js
document.getElementById() // id
document.getElementsByTagName() // tagName => array
document.getElementsByClassName() // className => array
document.getElementsByName() // name => array
document.querySelector()
document.querySelectorAll()
``` 

##### 通过关系树获取（遍历节点树）

DOM可以将任何HTML、XML文档描述称一个多层次的节点树。所有的节点都有nodeType属性，代表节点的不同类型。常用类型如下：

1. Element类型（元素节点）：nodeType为 1
2. Text类型（文本节点）：nodeType为 3
3. Comment类型（注释节点）：nodeType为 8
4. Document类型（document节点）：nodeType为 9

遍历属性：

1. parentNode // 父节点
2. childNodes // 子节点
3. firstChild // 第一个子节点
4. lastChild // 最后一个子节点
5. nextSibling // 后一个兄弟节点
6. previousSibling // 前一个兄弟节点

```js
document.getElementsByClassName('has-navbar')[0].childNodes

// output
NodeList(10)
0: text
1: a
2: text
3: div#top_nav.navbar.forpc
4: text
5: div#home
6: text
7: input#antiforgery_token
8: text
9: iframe#google_osd_static_frame_9713154175128
length: 10
```

##### 通过元素节点遍历

1. parentElement // 父元素节点
2. children
3. firstElementChild
4. lastElementChild
5. nextElementSibling
6. previousElementSibling

```js
document.getElementsByClassName('has-navbar')[0].children

// output
HTMLCollection(5)
0: a
1: div#top_nav.navbar.forpc
2: div#home
3: input#antiforgery_token
4: iframe#google_osd_static_frame_9713154175128
length: 5
```

##### 节点属性操作

1. attribute
    ```html
    <p class="p-test">这是测试</p>
    ```

    ```js
    const p = document.querySelectorAll('p')[0]
    p.setAttribute('data-name', 'ppppp')
    console.log(p.getAttribute('data-name')) // 'ppppp'
    ```
2. property
    ```html
    <p class="p-test">这是测试</p>
    ```

    ```css
    .p-test {
      width: 100px;
    }
    ```

    ```js
    const p = document.querySelectorAll('p')[0]

    console.log(p.style.width) // '100px'
    console.log(p.className) // 'p-text'
    console.log(p.nodeName) // p
    console.log(p.nodeType) // 1
    ```

### DOM结构操作

```html
<div id="divEle">
  <p id="oldEle">This is old element</p>
</div>
```

```js
const divEle = document.getElementById('#div')
```

#### 新增/插入节点

如果不是新增的节点，选中的节点存在于文档流中，执行parentEle.appendChild(existEle)时，会将existEle移动到parentEle中。

```js
// 添加节点
const newEle = document.createElement('p')
newEle.innerHTML = 'This is new element'
// 插入
divEle.appendChild(newEle)
```

#### 删除节点

```js
divEle.removeChild(newP)
```

#### 替换节点

用指定的节点替换当前节点的一个子节点，并返回被替换掉的节点。如果替换的节点存在于DOM树种，首先会被从原来的位置删除，然后replace被替换的节点。

```js
const oldEle = document.getElementById('#oldEle')

divEle.replaceChild(newEle, oldEle)
```

#### 插入节点

```js
divEle.insertBefor(newEle, oldEle) // 在oldEle之前插入newEle
```

#### 复制节点

element.cloneNode(boolean) => boolean: true深复制当前及所有子节点；boolean: false浅复制当前节点

```js
const cloneEle1 = newEle.cloneNode(true)
const cloneEle2 = newEle.cloneNode(false)
```

#### 移动节点

1. 方法一：如果要移动，肯定是存在的节点，所以appendChild(ele)就能移动节点
2. 方法二：克隆（cloneNode）原节点，删除（removeChild）原节点，插入（insertBefore）克隆的节点。

### DOM性能

DOM操作一方面会引起回流重绘，而且JS引擎和渲染引擎需要来回切换执行上下文，都很消耗性能，所以需要避免频繁操作DOM。

#### 提升方法

##### 对DOM查询做缓存

```js
// 不缓存
for(let i = 0; i < document.getElementsByTagName('p').length; i++) {
  // TODO
}

// 缓存
const len = document.getElementsByTagName('p').length
for(let i = 0; i < len; i++) {
  // TODO
}
```

##### 将频繁的操作改为一次性操作

```js
/** 将多个li标签插入到list中
  */

const list = document.getElementById('list')

// 创建文档片段，此时还没有插入到DOM树种
const frag = document.createDocumentFragment()

// 把需要插入的节点先放到文档片段中
for(let i = 0; i < 10; i++) {
  const li = document.createElement('li')
  frag.appendChild(li)
}

// 将文档片段插入到list中
list.appendChild(frag)
```

### DOM相关问题

#### DOM是那种数据结构

树

#### DOM常用的api

1. 节点查找
2. 属性操作
3. DOM结构操作（增、删、替换、克隆……）

#### attribute和property的区别

1. attribute：修改HTML属性，会改变HTML结构
2. property：修改对象属性，不会改变HTML结构

#### 一次性插入多个节点，考虑性能问题

如上DOM性能

## BOM

> BOM（Browser Object Model）：浏览器对象模型，）被广泛应用于 Web 开发之中，主要用于客户端浏览器的管理。

### BOM对象

1. window对象：JS的最顶层对象，其他BOM对象都是window对象的属性
2. document对象：文档对象
3. location对象：浏览器当前URL信息
4. navigator对象：浏览器本身信息
5. screen对象：客户端屏幕信息
6. history对象：浏览器访问历史信息

#### window对象

BOM对象的核心对象是window对象，他表示浏览器的一个实例。所有的JavaScript全局对象、函数以及变量均自动成为window对象的成员（全局变量是window对象的属性，全局函数是window对象的方法）。

##### window对象方法

调用时，省略window.

1. alert()
2. confirm()
3. open()
4. close()
5. setTimeout()
6. setInterval()
……

#### document对象

当浏览器加载文档后，会自动构建文档对象模型，把文档中每个元素都映射到一个数据集合中，然后以 document 进行访问。document 对象与它所包含的各种节点（如表单、图像和链接）构成了早期的文档对象模型（DOM 0级）。

```js
document.img.src

document.form.action
```

#### location对象

用于获得当前页面的地址 (URL)

##### location对象属性

```js
const RUL = "https://blog.csdn.net/lhjuejiang/article/details/79453407"

window.location

//output
Location: {
  ancestorOrigins: DOMStringList {length: 0},
  assign: ƒ assign(),
  hash: "",
  host: "blog.csdn.net",
  hostname: "blog.csdn.net",
  href: "https://blog.csdn.net/lhjuejiang/article/details/79453407",
  origin: "https://blog.csdn.net",
  pathname: "/lhjuejiang/article/details/79453407",
  port: "",
  protocol: "https:",
  reload: ƒ reload(),
  replace: ƒ replace(),
  search: "",
  toString: ƒ toString(),
  valueOf: ƒ valueOf(),
  Symbol(Symbol.toPrimitive): undefined,
  __proto__: Location,
}
```

#### navigator对象

存储了与浏览器相关的基本信息，如名称、版本和系统等。

##### navigator对象属性

```js
window.navigator

// output
Navigator: {
  appCodeName: "Mozilla",
  appName: "Netscape",
  appVersion: "5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.90 Safari/537.36",
  connection: NetworkInformation {onchange: null, effectiveType: "4g", rtt: 50, downlink: 10, saveData: false},
  cookieEnabled: true,
  doNotTrack: null,
  geolocation: Geolocation {},
  hardwareConcurrency: 4,
  language: "zh-CN",
  languages: (2) ["zh-CN", "zh"],
  maxTouchPoints: 0,
  mediaCapabilities: MediaCapabilities {},
  mediaSession: MediaSession {metadata: null, playbackState: "none"},
  mimeTypes: MimeTypeArray {0: MimeType, 1: MimeType, 2: MimeType, 3: MimeType, application/pdf: MimeType, application/x-google-chrome-pdf: MimeType, application/x-nacl: MimeType, application/x-pnacl: MimeType, length: 4},
  onLine: true,
  permissions: Permissions {},
  platform: "Win32",
  plugins: PluginArray {0: Plugin, 1: Plugin, 2: Plugin, Chrome PDF ,Plugin: Plugin, Chrome PDF Viewer: Plugin, Native Client: Plugin, length: 3},
  product: "Gecko",
  productSub: "20030107",
  scheduling: Scheduling {},
  userActivation: UserActivation {hasBeenActive: true, isActive: true},
  userAgent: "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/89.0.4389.90 Safari/537.36",
  vendor: "Google Inc.",
  vendorSub: "",
  webdriver: false,
  webkitPersistentStorage: DeprecatedStorageQuota {},
  webkitTemporaryStorage: DeprecatedStorageQuota {},
  __proto__: Navigator
}
```

#### screen对象

存储了客户端屏幕信息，这些信息可以用来探测客户端硬件配置。

##### screen对象属性

```js
window.screen

// output
Screen: {
  availHeight: 1040,
  availLeft: 1366,
  availTop: -139,
  availWidth: 1920,
  colorDepth: 24,
  height: 1080,
  orientation: ScreenOrientation {angle: 0, type: "landscape-primary", onchange: null},
  pixelDepth: 24,
  width: 1920,
  __proto__: Screen
}
```

#### history对象

存储了库互动浏览器的浏览历史。

##### history对象方法

1. history.back()：后退
2. history.forward()：前进
3. history.go(index)：前进、后退几页
4. history.pushState()：添加历史记录
5. history.replaceState()：修改历史记录