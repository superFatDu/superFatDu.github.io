---
title: 前端基础知识体系-JS-WEB-API(事件)
date: 2021-5-10 10:11:57
tags: [知识体系,总结,JS,JS-WEB-API]
categories: 前端
comment: true
---

## 事件

1. 事件是用户或浏览器自身执行的某种动作，如click,load和mouseover都是事件的名字。
2. 事件是javaScript和DOM之间交互的桥梁。

### 事件绑定

#### DOM直接绑定

```html
<button id="btn" onClick="alert('DOM直接绑定')">click</button>
```

#### JS中绑定

```js
const btn = document.getElementById('btn')
btn.onClick = function() { alert('JS中绑定') }
```

#### 绑定事件监听函数

elementObject.addEventListener(eventName,handle,useCapture);

useCapture参数是用来指定事件捕获还是事件冒泡。true => 捕获；false => 冒泡。默认为false。

```js
const btn = document.getElementById('btn')
btn.addEventListener('click', () => { alert('绑定事件监听函数') })
```

#### 简易通用绑定函数

```js
function EventBind(element, eventType, selector, fn) {
  if(!fn) [selector, fn] = [null, selector] // 三个参数是普通调用，四个参数是代理调用
  element.addEventListener(eventType, event => {
    const target = event.target
    // 使用call可以在事件绑定的回调函数里面使用this，但是需要注意的是回调函数不能用箭头函数！！！
    if(selector) {
       if (target.matches(selector)) fn.call(target, event)
    } else {
      fn.call(target, event)
    }
  })
}

const btn = document.getElementById('btn')
EventBind(btn, 'click', function(e) { alert(this.innerHTML) })
```

### 事件流

![event](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210514/event.1qatug31uj6o.jpg)

```html
// 伪代码
<div id="wrap">
  <div id="parent">
    <button id="btn"></button>
  </div>
</div>
```

```js
const wrap = document.getElementById('wrap')
const parent = document.getElementById('parent')
const btn = document.getElementById('btn')

wrap.addEventListener('click', () => { console.log('外层冒泡') }, false)
parent.addEventListener('click', () => { console.log('父级捕获') }, true)// useCapture为true，可以被捕获
parent.addEventListener('click', () => { console.log('父级冒泡') }, false)
btn.addEventListener('click', () => { console.log('目标捕获') }, true) // useCapture为true，可以被捕获
btn.addEventListener('click', () => { console.log('目标冒泡') }, false)

// output
父级捕获
目标捕获
目标冒泡
父级冒泡
外层冒泡
```

#### 事件捕获

从DOM最外层一层层往里捕获事件，直到目标元素。

#### 事件冒泡

从目标元素由内到外冒泡。

### 事件委托/事件代理

事件委托就是利用事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。

#### 示例

```html
<div class="wrap"></div>
```

```js
const wrapEle = document.querySelector('.wrap')
  window.onload = function () {
    const frag = document.createDocumentFragment()
    for (let i = 0; i < 5; i++) {
      let ele = document.createElement('p')
      ele.innerHTML = `This is ele ${i}`
      frag.appendChild(ele)
    }
    wrapEle.append(frag)
    console.log(frag)
  }
  wrapEle.addEventListener('click', (e) => {
    console.log(e.target.innerHTML)
  })
```

#### 回调函数参数e

```js
MouseEvent: {
  altKey: false,
  bubbles: true,
  button: 0,
  buttons: 0,
  cancelBubble: false,
  cancelable: true,
  clientX: 63,
  clientY: 104,
  composed: true,
  ctrlKey: false,
  currentTarget: null,
  defaultPrevented: false,
  detail: 1,
  eventPhase: 0,
  fromElement: null,
  isTrusted: true,
  layerX: 63,
  layerY: 104,
  metaKey: false,
  movementX: 0,
  movementY: 0,
  offsetX: 55,
  offsetY: 14,
  pageX: 63,
  pageY: 104,
  path: (6) [p, div.wrap, body, html, document, Window],
  relatedTarget: null,
  returnValue: true,
  screenX: 1429,
  screenY: 68,
  shiftKey: false,
  sourceCapabilities: InputDeviceCapabilities {firesTouchEvents: false},
  srcElement: p,
  target: p,
  timeStamp: 5829.630000051111,
  toElement: p,
  type: "click",
  view: Window {window: Window, self: Window, document: document, name: "", location: Location, …},
  which: 1,
  x: 63,
  y: 104,
  __proto__: MouseEvent
}
```

##### 回调函数参数e.target

```js
accessKey: ""
align: ""
ariaAtomic: null
ariaAutoComplete: null
ariaBusy: null
ariaChecked: null
ariaColCount: null
ariaColIndex: null
ariaColSpan: null
ariaCurrent: null
ariaDescription: null
ariaDisabled: null
ariaExpanded: null
ariaHasPopup: null
ariaHidden: null
ariaKeyShortcuts: null
ariaLabel: null
ariaLevel: null
ariaLive: null
ariaModal: null
ariaMultiLine: null
ariaMultiSelectable: null
ariaOrientation: null
ariaPlaceholder: null
ariaPosInSet: null
ariaPressed: null
ariaReadOnly: null
ariaRelevant: null
ariaRequired: null
ariaRoleDescription: null
ariaRowCount: null
ariaRowIndex: null
ariaRowSpan: null
ariaSelected: null
ariaSetSize: null
ariaSort: null
ariaValueMax: null
ariaValueMin: null
ariaValueNow: null
ariaValueText: null
assignedSlot: null
attributeStyleMap: StylePropertyMap {size: 0}
attributes: NamedNodeMap {length: 0}
autocapitalize: ""
autofocus: false
baseURI: "file:///G:/blog/source/_posts/index.html"
childElementCount: 0
childNodes: NodeList [text]
children: HTMLCollection []
classList: DOMTokenList [value: ""]
className: ""
clientHeight: 21
clientLeft: 0
clientTop: 0
clientWidth: 1275
contentEditable: "inherit"
dataset: DOMStringMap {}
dir: ""
draggable: false
elementTiming: ""
enterKeyHint: ""
firstChild: text
firstElementChild: null
hidden: false
id: ""
innerHTML: "This is ele 2"
innerText: "This is ele 2"
inputMode: ""
isConnected: true
isContentEditable: false
lang: ""
lastChild: text
lastElementChild: null
localName: "p"
namespaceURI: "http://www.w3.org/1999/xhtml"
nextElementSibling: p
nextSibling: p
nodeName: "P"
nodeType: 1
nodeValue: null
nonce: ""
offsetHeight: 21
offsetLeft: 8
offsetParent: body
offsetTop: 90
offsetWidth: 1275
onabort: null
onanimationend: null
onanimationiteration: null
onanimationstart: null
onauxclick: null
onbeforecopy: null
onbeforecut: null
onbeforepaste: null
onbeforexrselect: null
onblur: null
oncancel: null
oncanplay: null
oncanplaythrough: null
onchange: null
onclick: null
onclose: null
oncontextmenu: null
oncopy: null
oncuechange: null
oncut: null
ondblclick: null
ondrag: null
ondragend: null
ondragenter: null
ondragleave: null
ondragover: null
ondragstart: null
ondrop: null
ondurationchange: null
onemptied: null
onended: null
onerror: null
onfocus: null
onformdata: null
onfullscreenchange: null
onfullscreenerror: null
ongotpointercapture: null
oninput: null
oninvalid: null
onkeydown: null
onkeypress: null
onkeyup: null
onload: null
onloadeddata: null
onloadedmetadata: null
onloadstart: null
onlostpointercapture: null
onmousedown: null
onmouseenter: null
onmouseleave: null
onmousemove: null
onmouseout: null
onmouseover: null
onmouseup: null
onmousewheel: null
onpaste: null
onpause: null
onplay: null
onplaying: null
onpointercancel: null
onpointerdown: null
onpointerenter: null
onpointerleave: null
onpointermove: null
onpointerout: null
onpointerover: null
onpointerrawupdate: null
onpointerup: null
onprogress: null
onratechange: null
onreset: null
onresize: null
onscroll: null
onsearch: null
onseeked: null
onseeking: null
onselect: null
onselectionchange: null
onselectstart: null
onstalled: null
onsubmit: null
onsuspend: null
ontimeupdate: null
ontoggle: null
ontransitioncancel: null
ontransitionend: null
ontransitionrun: null
ontransitionstart: null
onvolumechange: null
onwaiting: null
onwebkitanimationend: null
onwebkitanimationiteration: null
onwebkitanimationstart: null
onwebkitfullscreenchange: null
onwebkitfullscreenerror: null
onwebkittransitionend: null
onwheel: null
outerHTML: "<p>This is ele 2</p>"
outerText: "This is ele 2"
ownerDocument: document
parentElement: div.wrap
parentNode: div.wrap
part: DOMTokenList [value: ""]
prefix: null
previousElementSibling: p
previousSibling: p
scrollHeight: 21
scrollLeft: 0
scrollTop: 0
scrollWidth: 1275
shadowRoot: null
slot: ""
spellcheck: true
style: CSSStyleDeclaration {alignContent: "", alignItems: "", alignSelf: "", alignmentBaseline: "", all: "", …}
(...)
timeStamp: 5829.630000051111
```

### 事件类型

#### UI事件

1. load
2. resize
3. scroll

#### 焦点事件

1. focus
2. blur
3. focusin => 等价focus，但是它会冒泡
4. blurout => 等价blur，但是它会冒泡

#### 鼠标与滚轮事件

1. click
2. dbclick
3. mousedown
4. mouseup
5. mouseenter
6. mouseleave
7. mouseout

#### 键盘与文本事件

1. keydown
2. keypress
3. keyup

#### 变动事件

1. beforeunload：用户关闭标签页
2. DOMContentLoaded：在形成完整DOM树之后会触发，不会理会图像、js、css等资源是否已经下载完毕

#### 设备事件

1. orientationchange：屏幕转动

#### 触摸与手势事件

##### 触摸

1. touchstart
2. touchend
3. touchmove
4. touchcancel

🚨 Notice：以上事件event 对象中包含的常见 DOM 属性有：bubbles、cancelable、view、clientX、clientY、screenX、screenY、detail、altKey、shiftKey、ctrlKey 和metaKey。

##### 手势

1. gesturestart：当一根手指在屏幕上，另一根手指有触摸屏幕时
2. gesturechange：触摸屏幕的任何一根手指位置发生变化
3. gestureend：任何一根手指从屏幕上移开
