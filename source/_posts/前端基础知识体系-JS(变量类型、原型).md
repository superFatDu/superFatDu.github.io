---
title: 前端基础知识体系-JS(变量类型、原型)
date: 2021-4-16 15:12:53
tags: [知识体系,总结,JS]
categories: 前端
comment: true
---

## JS - 变量类型和计算

![js-type](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210416/js-type.3to061fo1uu0.png)

🚨 Notice：ES6新增值类型symbol

### typeof能判断哪些类型

1. 识别所有的值类型
2. 识别函数
3. 判断是否是引用类型（具体是什么不知道）

```js
const str = 'string';   typeof str;   // 'string'
const sy = Symbol('s'); typeof sy;    // 'symbol'

typeof function() {}   // 'function'

typeof null         // 'object'
typeof [2, 3]       // 'object'
typeof {x: 100}     // 'object'
```

### === 和 ==

== 比较的时值，=== 比较的是值和类型。

🚨 Notice：== 左右两边如果类型不同，则会进行类型转换。具体转换放下如下【类型转换】。

### 值类型和引用类型的区别

如上图

### 手写深拷贝

```js
/**
 * 简单深拷贝
 * @param {Object} obj 要拷贝的对象
 */
function deepClone (obj = {}) {
  if (typeof obj !== 'object' || obj == null) return obj
  let result
  obj instanceof Array ? result = [] : result = {}
  for(let key in obj) {
    if (obj.hasOwnProperty(key)) {
      result[key] = deepClone(obj[key])
    }
  }
  return result
}
```

### 类型转换

🚨 Notice：为了避免问题，除了 == null之外，其他的一律使用 ===

```js
const obj = { x: 100 }
if (obj.a == null) {}
// 上面条件相当于
if (obj.a === null || obj.a === undefined) {}
```

#### 类型转换流程

1. 判断类型是否相同，若相同：
    1. 相同进行===比较。
2. 类型不同时，会进行类型转换：
    1. 如果一个为null，一个为undefined，返回true。
    2. 如果一个为字符串，一个数值，那么在比较之前调用Number()函数将字符串转换成数值。
    3. 如果任一值值布尔值，则在比较之前调用Number()函数先转换成数值。
    4. 如果一个是对象，另一个是数值或者字符串，先将其转换成基础类型的值在比较。

#### 类型转换流程图

![x==y](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210416/x==y.4190g1w1x1o.svg)

## JS - 原型和原型链

### 原型链关系图1

![原型链关系图](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210419/原型链关系图.vpd.2y9mypmffn60.svg)

🚨 Notice：\_\_proto\_\_指向构造器的原型，即：\_\_proto\_\_ === constructor.prototype。

### 原型链关系图2

```js
// 父类
class People {
  constructor(name) {
    this.name = name
  }
  eat() {}
}

// 字类
class Student extends People {
  constructor(name, id) {
    super(name)
    this.id = id
  }
  study() {}
}

// 实例对象
let xiaoming = new Student('xiaoming', '041322)
```

![pic2](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210419/原型链图2.4m5sw8w49v20.svg)

### 如何判断一个变量是不是数组

```js
const a = []

a instanceof Array // 方法1
Array.isArray(a) // 方法2
Object.prototype.toString.call(a) === "[object Array]" // 方法3
```

### 手写一个简易的jQuery，考虑插件和扩展性

```js
// 基础
class jQuery {
  constructor(selector) {
    const result = document.querySelectorAll(selector)
    const length = result.length
    for(let i = 0; i < length; i++) {
      this[i] = result[i]
    }
    this.length = length
    this.selector = selector
  }
  get(index) {
    return this[index]
  }
  each(fn) {
    for(let i = 0; i < this.length; i++) {
      const ele = this[i]
      fn(ele)
    }
  }
  on(type, fn) {
    return this.each(ele => {
      ele.addEventListener(type, fn, false)
    })
  }
}

// 插件
jQuery.prototype.dialog = function (info) {
  console.log(info)
}

// 扩展性 => 造轮子
class myjQuery extends jQuery {
  constructor(selector) {
    super(selector)
  }
  // 扩展自己的方法
  addClass(className) {
    // TODO
  }
}

```

### class

> 语法糖，实际上还是function。

#### class基础使用

🚨 Notice：constructor方法是类的默认方法，通过new命令生成对象实例时，自动调用该方法。一个类必须有constructor方法，如果没有显式定义，一个空的constructor方法会被默认添加。

```js
// 类
class Demo {
  constructor(name, age) {
    this.name = name
    this.age = age
  }
  sayHalo() {
    console.log(`I'm ${this.name},${this.age} years old.`)
  }
}

// 通过类声明一下实例
const demo = new Demo('cheng5', 28)
demo.sayHalo() // I'm cheng5,28 years old.
```

#### class继承

🚨 Notice：实例的属性除非显示定义在本身（this）上（如name，age）,否则都定义在原型上（如sayHalo, eat）。

```js
demo.hasOwnProperty('name') // true
demo.hasOwnProperty('sayHalo') // false
demo.__proto__.hasOwnProperty('sayHalo') // true 方法在原型上
```

```js
// 父类
class Person {
  constructor(name) {
    this.name = name
  }
  eat () {
    console.log('干饭人')
  }
}

// 子类
class Worker extends Person {
  constructor(name, magor) {
    super(name)
    this.magar = magor
  }
  work() {
    console.log('打工魂')
  }
}

// 通过类声明一下实例
const worker = new Worker('小扳手', '搬砖')
student.eat() // 干饭人
student.work() // 打工魂
```

#### class的本质，怎么理解

```js
class Demo {}

typeof Demo // 'function'
```

本质是’function‘，ES6语法糖。

#### 类型判断

> instanceof：用来检测 constructor.prototype 是否存在于参数 object 的原型链上。

🚨 Notice：如果 A instanceof B，那么会满足：A.\_\_proto\_\_(一个或者n个) = B.prototype

```js
worker instanceof Worker // true => Worker.prototype在worker的原型链上
worker instanceof Person // true
worker instanceof Object // true

[] instanceof Array  // true
[] instanceof Object // true

{} instanceof Object // true
```

##### 写一个函数，标识instanceOf的原理

> 算法参考链表遍历

```js
const instanceOf = (A, B) => {
  let p = A
  while(p) {
    if (p === B.prototype) return true
    p = p.__proto__
  }
  return false
}
```

##### 看代码写出输出

```js
function foo() {}
let F = {}

Object.prototype.a = 'value a'
Function.prototype.b = 'value b'

conslole.log(foo.a)
conslole.log(foo.b)
conslole.log(F.a)
conslole.log(F.b)

// expected output
a
b // 函数的原型脸上能找到Object.prototype和Funciton.prototyp
a
undefined // 对象的原型脸上只能找到Object.prototype
```
