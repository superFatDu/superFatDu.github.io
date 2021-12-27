---
title: 简易Promise
date: 2021-5-6 3:12:05
tags: JavaScript
categories: 前端
comment: true
---

我一定不会告诉你，我失眠了，这大半夜的，怎么就睡不着呢？

## Promise基础用法

```js
let promise = new Promise((resolve, reject) => {
  console.log('Promise执行中……')
  setTimeout(() => {
    resolve('thenable')
  }, 200)
})

promise.then(val => {
  console.log('success', val)
}, err => {
  console.log('error', err)
})
```

## 基础Promise实现

### Promise基础结构

```js
// 定义三种状态
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

// 构造函数
function Promise(executor) {
  // 初始数据
  let _this = this
  this.state = PENDING // 状态
  this.value = undefined // 成功结果
  this.reason = undefined // 失败原因

  function resolve(value) {} // 成功时函数
  function reject(reason) {} // 失败时函数

  // let promise = new Promise((resolve, reject) => { console.log('executor执行了') })
  // 实例化Promise时，构造函数会立即调用executor，所以：
  try {
    executor(resolve, reject)
  } catch (e) {
    reject(e)
  }
}

// then函数 => 由于原型链继承，所以写在Promise的原型上
Promise.prototype.then = function(onFilfilled, onRejected) {}

// 导出构造函数
export default Promise
```

### resolve/reject函数

promise/A+规范中规定，当Promise对象已经由等待态（Pending）改变为执行态（Fulfilled）或者拒绝态（Rejected）后，就不能再次更改状态，且终值也不可改变。

```js
function resolve(value) {
  if(_this.state === PENDING) {
    _this.state = FULFILLED
    _this.value = value
  }
}
function reject(reason) {
  if (_this.state === PENDING) {
    _this.state = REJECTED
    _this.reason = reason
  }
}
```

### then函数

```js
Promise.prototype.then = function(onFulfilled, onRejected) {
  if (this.state === FULFILLED) {
    typeof onFulfilled === 'function' && onFulfilled(this.value)
  }
  if (this.state === REJECTED) {
    typeof onRejected === 'function' && onRejected(this.reason)
  }
}
```

### Have a try

```js
let promise = new Promise((resolve, reject) => {
  console.log('executing')
  setTimeout(() => {
    resolve('execute resolve()')
  })
})
promise.then(val => {
  console.log(val)
})

// output:
executing
```

🚨 Notice：从输出来看，executor()确实是执行了，但then()函数没有输出结果。原因是执行then()函数的时候，setTimeout()的回调还在任务队列排队，也就是说还没有执行resolve()函数，state的值也就是默认的‘pending’。总结一下，就是没有处理异步问题。

### 异步支持

#### 思路

执行then()函数的时候，如果state的状态是‘pending’，那我们就把回调放在对调数组里，然后等任务从任务队列到执行栈执行时，再处理回调。

#### 修正

```js
// 增加回调数组
this.fulfilledArr = [] // 异步resolve回调
this.rejectedArr = [] // 异步reject回调

// 修正resolve/rejec函数
function resolve(value) {
  // 增加处理回调
  _this.fulfilledArr.forEach(fn => fn(value))
}
function reject(reason) {
  // 增加处理回调
  _this.rejectedArr.forEach(fn => fn(reason))
}

// then()函数处理异步情况
Promise.prototype.then = (onFulfilled, onRejected) {
  // 增加state为‘pending’的情况
  if (this.state === PENDING) {
    typeof onFulfilled === 'function' && this.fulfilledArr.push(onFulfilled)
    typeof onRejected === 'function' && this.rejectedArr.push(onRejected)
  }
}
```

#### 完整代码

```js
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

function Promise(executor) {
  let _this = this

  this.state = PENDING
  this.value = undefined
  this.reason = undefined
  this.fulfilledArr = []
  this.rejectedArr = []

  function resolve(value) {
    if (_this.state === PENDING) {
      _this.state = FULFILLED
      _this.value = value
      _this.fulfilledArr.forEach(fn => fn(value))
    }
  }
  function reject(reason) {
    if (_this.state === PENDING) {
      _this.state = REJECTED
      _this.reason = reason
      _this.rejectedArr.forEach(fn => fn(reason))
    }
  }

  try {
    executor(resolve, reject)
  } catch (e) {
    reject(e)
  }
}

Promise.prototype.then = function(onFulfilled, onRejected) {
  if (this.state === PENDING) {
    typeof onFulfilled === 'function' && this.fulfilledArr.push(onFulfilled)
    typeof onRejected === 'function' && this.rejectedArr.push(onRejected)
  }
  if (this.state === FULFILLED) {
    typeof onFulfilled === 'function' && onFulfilled(this.value)
  }
  if (this.state === REJECTED) {
    typeof onRejected === 'function' && onRejected(this.reason)
  }
}
```

## 进阶Promise实现(链式调用)

对照promise/A+规范，then 方法必须返回一个 promise 对象。

```js
let promise = new Promise(resolve => {
  resolve('resolve')
})
promise.then(val => {
  console.log(val)
})
// output => 没有显式return，默认返回一个Promise
'resolve'
Promise {
  __proto__: Promise,
  [[PromiseState]]: 'fulfilled',
  [[PromiseResult]]: undefined
}

let promise1 = new Promise(resolve => {
  resolve('resolve')
})
promise1.then(val => {
  console.log(val)
  return 'hahaha'
})
// output => 有先事return，会包装成Promise再返回，可供下一个then()函数使用
'resolve'
Promise {
  __proto__: Promise,
  [[PromiseState]]: 'fulfilled',
  [[PromiseResult]]: 'hahaha'
}

```

### 链式调用示例

```js
let promise = new Promise((resolve, reject) => {
  resolve('first')
})
promise.then(val => {
  console.log(val)
  return 'second'
}).then(val => {
  console.log(val)
  return 'third'
}).then(val => {
  console.log(val)
})
```

### 进阶实现

[这里就不自己写了，好麻烦的，看文章](https://zhuanlan.zhihu.com/p/144058361)
