---
title: new&create
date: 2021-3-28 15:22:06
tags: JavaScript
categories: 前端
comment: true
---

我们总能看见如下代码：

```js
const obj = new Object()
const obj = Object.create(null)
```

那么new和create具体是怎么实现的呢？

## new

### 使用new调用函数，或者说发生构造函数的调用时，会执行如下步骤

1. 创建（或者构造）一个全新的对象
2. 将新对象的[[ptototype]]与构造函数的prototype连接
3. 新对象绑定构造函数this
4. 如果函数没有返回值，那么会自动返回新对象（如果有显示返回，且如果是引用类型，则返回这个引用，如果是基础类型，这返回新对象）

### new实现

```js
// const obj1 = new obj()

// 创建（或者构造）一个全新的对象 
const obj1 = new Object()
// 将新对象的[[ptototype]]与构造函数的prototype连接
obj1.__proto__ = obj.prototype

// 上面两步也可简化为
const obj1 = Object.create(obj.prototype)

// 绑定this
obj.call(obj1)

// 返回值
return obj1 // 默认返回

return ‘1’  // 基本数据类型，返回还是默认obj1
return { x: 'x' } // 引用类型，返回不是默认，为{ x: 'x' }
```

## create

### 语法

```js
Object.create(proto，[propertiesObject])
```

1. proto：新创建对象的原型对象
2. propertiesObject：传入对象的自有可枚举属性(即其自身定义的属性，而不是其原型链上的枚举属性)将为新创建的对象添加指定的属性值和对应的属性描述符

### create实现

> Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的 \_\_proto\_\_。

```js
// 实现
Object.prototype.myCreate = function(o) {
  function F() {}
  F.prototype = o
  return new F()
}

// 实例
const obj = {
  x: 'x'
}
let obj1 = Object.myCreate(obj)
console.log(obj1) // {}
console.log(obj1.x) // 'x'

// obj1 = new F() => {}
// 如上可知：obj1.__proto === F.prototype
// 如上可知：obj1.__proto === obj
// 如上可知：obj1可以沿着原型链找到obj,但没有连接到obj.prototype上，所以可以读取obj，读取不了原型（obj.prototype）上的属性
// 如是可知：obj1 === {}
// 如上可知：obj1.x === obj.x === 'x'
```

### 应用

1. 创建一个没有原型链的绝对空对象。

    ```js
    const obj = Object.create(null)

    // const obj = {} 相当于
    // obj = Object.create(Object.prototype) 
    ```

2. 如上new的实现。
3. 继承

    ```js
    // 父类
    function Super() {}
    Super.prototype.fn = function() {}
    
    // 子类
    function Sub() {
      Super.call(this)
    }

    // 原型链继承
    Sub.prototype = Object.create(Super.prototype) // Sub.prototype.__proto__ = Super.prototype
    Sub.prototype.constructor = Sub // 修正构造函数，由于上一步，导致Sub.prototype.constructor === Sup

    // 实例
    const sub1 = new Sub()
    sub1.fn()
    ```

### new Object()

```js
// 基础
const obj = new Object()
// 相当于
const obj = Object.create(Object.prototype)

// 参数
const obj1 = new Object({ x: 1})
// 相当于
const obj1 = Object.create(Object.prototype, {
  x: {
    writable:true,
    configurable:true,
    value: 1
  }
})
```
