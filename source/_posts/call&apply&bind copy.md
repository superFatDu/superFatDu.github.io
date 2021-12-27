---
title: call&apply&bind
date: 2021-3-12 20:35:46
tags: JavaScript
categories: 前端
comment: true
---
## 概述

1. call、apply、bind这三个方法属于显示绑定，这三个方法都能修改this的指向。
2. call和apply相对比较特殊，他们在修改this指向的同时，还会执行方法；但是bind在修改this后会返回一个待执行的方法。
3. call和apply另一个不同之处，就是参数的接收形式。

    ```js
    function fn() {}

    fn.call(this, arg1, arg2, arg3 , ...) // 散列

    fn.apply(this, [arg1, arg2, arg3, ...]) // 数组
    ```

### call

```js
Function.prototype.myCall = funciton(context, ...params) {
  // 判断context的类型
  // if (typeof context === 'object') {
  //   context = context || window
  // } else {
  //   context = Object.create(null)
  // }
  typeof context === 'object'
    ? context = context || window
    : context = Object.create(null)

  // 确保fn是独一无二的属性
  let fn = Symbol()

  // 绑定this
  context.fn = this

  // call的返回是一个执行过后的返回值
  const result = context.fn(...params)

  // 删除context添加的属性
  delete context.fn

  // 返回值
  return result
}
```

### apply

```js
Function.prototype.myApply = funciton(context, params) {
  // 判断context的类型
  typeof context === 'object'
    ? context = context || window
    : context = Object.create(null)

  // 确保fn是独一无二的属性
  let fn = Symbol()

  // 绑定this到context的fn属性上，context.fn()执行时，this就指向了context
  context.fn = this

  // 获取返回值
  const result = context.fn(...params)

  // 删除新增的属性
  delete context.fn

  // 返回值
  return result  
}
```

### call/apply 使用eval方式实现

🚨 Notice1：主要的问题在于参数的处理，ES6有...展开运算符，但是ES5没有，所以这里使用eval()，eval函数执行时把变量转换成了字符串。

```js
function add(a, b, c) {
  console.log(a + b + c)
}
let arr = [1, 2, 3]

add(...arr) // 6
eval('add('+arr+')') // 6
```

🚨 Notice2：传递的参数，如果不全是数字，就不能提前解析，需要传入如‘str[0]’这样的字符串。eval()处理数组时，数组的每一项会被拿掉外层的‘’然后被当成一个变量当做参数。

```js
function print(a, b, c) {
  console.log(a + b +c)
}
let arr = ['我', '是', '杜木土']

// 正常传递参数
print(arr[0], arr[1], arr[2]) //我是杜木土
// 同
print('我', '是', '杜木土')

// 错误使用
eval('print('+arr+')') // Uncaught ReferenceError: 我 is not defined
// 同
eval('print('+['我', '是', '杜木土']+')')
// 同
print(我, 是, 杜木土) // 我不是参数，所以未定义：引用错误

// 正确使用
eval('print('+['arr[0]', 'arr[1]', 'arr[2]']+')') //我是杜木土
// 同
print(arr[0], arr[1], arr[2]) 
```

#### 实现如下

##### eval - call

```js
Funciton.prototype.myCall = function(context) {
  typeof context === 'object'
    ? context = context || window
    : context = Object.create(null)

  context.fn = this
  let args = []
  for(let i = 1, len = arguments.length; i < len; i ++) {
    args.push('arguments['+ i +']')
  }

  let result = eval('context.fn('+args+')')
  delete context.fn
  return result
} 
```

##### eval - apply

```js
Function.prototype.myApply = function(context, arr) {
  typeof context === 'object'
    ? context = context || window
    : context = Object.create(null)

  context.fn = this
  const args = []
  const result

  if(arr.length) {
    result = context.fn()
  } else {
    for(let i = 0, len = arr.length; i < len; i++) {
      args.push('arr['+ i +']')
    }
    result = eval('context.fn('+ args +')')
  }
  delete context.fn
  return result
}
```

### bind

```js
Function.prototype.myBind = function() {
  // 获取参数
  // let args = Array.prototype.slice.call(arguments) 方法1
  // let args = [].slice.call(arguments) 方法2
  const args = Array.from(arguments) // 方法3
  // 获取this
  const context = args.shift()
  // 让return出去的函数不丢失this
  const self = this
  return function() {
    self.apply(context, [].concat.call(args, Array.from(arguments))) // 考虑bind后的函数传参
  }
}
function print(a, b, c) {
  console.log(a, b,c)
}
fn.myBind(null, 1, 2, 3)() // 1 2 3
fn.myBind(null, 1, 2)(3)   // 1 2 3
```
