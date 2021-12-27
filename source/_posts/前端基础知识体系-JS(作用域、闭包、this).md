---
title: 前端基础知识体系-JS(作用域、闭包、this)
date: 2021-4-21 17:02:34
tags: [知识体系,总结,JS]
categories: 前端
comment: true
---

## JS - 作用域、闭包、this

### 作用域

> 作用域是在运行时代码中的某些特定部分中变量，函数和对象的可访问性。作用域是分层的，内层作用域可以访问外层作用域的变量，反之则不行。

![scope](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210421/scope.6tzdji2pbw00.png)

- 泡泡 1 是全局作用域，有标识符 foo；
- 泡泡 2 是函数作用域 foo，有标识符 a,bar,b；
- 泡泡 3 是函数作用域 bar，仅有标识符 c。

#### 作用域分类

1. 全局作用域
2. 函数作用域
3. 块级作用域（ES6）

#### 作用域链

##### 自由变量

1. 一个变量在当前作用域没有定义，但是使用了。
2. 会沿着作用域链一层一层寻找，知道找到为止。
3. 如果到全局作用域都没有找到，则会报错：Uncaught ReferenceError: xx is not defined。

##### 什么是作用域链

> 如上，这种一层一层的寻找，就是作用域链。

```js
var a = 10
function fn() {
  var b = 20
  function bar() {
    console.log(a + b) //30
  }
  return bar
}
var x = fn(),
b = 200
x() // 执行bar()
```

函数执行，内部变量的查找与函数执行的作用域没有关系，关键是函数定义的作用域。所以，如上x()执行查找b时，从fn()函数作用域中取，而不是全局作用域。

#### 作用域与执行上下文

### 闭包

当函数可以记住并访问所在的词法作用域时，就产生了闭包。——《你不知道的JavaScript上卷》

#### 产生场景1 - 函数作为返回值

```js
function create() {
  let a = 100
  return function() {
    console.log(a)
  }
}
let fn = crate()
let a = 200
fn() // 100 => 函数执行时只关注函数定义创建的作用域
```

#### 产生场景2 - 函数作为参数

```js
function print(fn) {
  let a = 200
  fn()
}
let a = 100
function fn() {
  console.log(a)
}
print(fn) // 100 => 函数执行时只关注函数定义创建的作用域
```

#### 闭包的使用场景

##### setTimeout传参

```js
function fn(opt) {
  return function() {
    console.log(opt)
  }
}
let fun = fn(2) // 原生的setTimeout传递的第一个函数不能带参数，通过闭包可以实现传参效果。
setTimeout(fun, 1000)
```

##### 回调

```js
function changeSize(size) {
  return function() {
    document.body.style.fontSize = size + 'px'
  }
}
let size14 = changeSize(14)
let size16 = changeSize(16)

document.querySelector('#size14').onclick = size14
document.querySelector('#size16').onclick = size16
```

##### 防抖

```js
function debounce(fn, delay) {
  let timer = null
  return function() {
    let args = arguments
    clearTimeout(timer)
    setTimerout(() => {
      fn.apply(this, args)
    }, delay)
  }
}
```

##### 封装私有变量（如模块化）

```js
function Modules() {
  let type = {
    add,
    minus
  }
  function add() {
    console.log(type.add, 'add')
  }
  function minus() {
    console.log(type.minus, 'minus')
  }
  return { add, minus }
}
let { add, minus } = Modules()
add() // 'add'
minus() // 'minus'
```

#### 总结

无论通过何种手段将内部函数传递到所在的词法作用域以外，它都会持有对原始定义作用域的引用，无论在何处执行这个函数都会使用闭包。——《你不知道的JavaScript上卷》

### this

#### this的基础使用场景

1. 普通函数
2. call/apply/bind
3. 对象方法
4. class
5. 箭头函数

🚨 Notice：this的指向在函数定义的时候是确定不了的，只有函数执行的时候才能确定this到底指向谁。

```js
// 普通函数
function fn1() {
  console.log(this)
}
fn1() // window
// call
fn1.call({ x: 100 }) // { x: 100 }
// 对象方法
const xiaoming = {
  name: 'xiaoming',
  sayHi() {
    console.log(this)
  },
  wait() {
    setTimout(function() {
      console.log(this)// xiaohong.wait() => window
    }, 1000)
  }
}
// class
class People {
  constructor (name) {
    this.name = name
  }
  sayHi() {
    console.log(this)
  }
}
// 箭头函数
const xiaohong = {
  name: 'xiaohong',
  wait() {
    setTimout(() => {
      console.log(this) // xiaohong.wait() => xiaohong {}
    }, 1000)
  }
}
```

#### this的理解

这里自己就不总结了，有很好的文章值得推荐。[彻底理解js中this的指向，不必硬背。](https://www.cnblogs.com/pssp/p/5216085.html)
