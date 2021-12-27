---
title: 一些面试题（JavaScript）
date: 2021-1-1 21:12:42
tags: [JavaScript,面试题]
categories: 前端
comment: true
sticky: 998
---

## 面试题

### 下面代码输出什么

```js
var a = 10
!(function() {
  console.log(a)
  a = 5 // 本身作用域没有定义a，所以相当于定义全局作用域，并赋值5
  console.log(this.a)
})()
```

```js
var a = 10
!(function() {
  console.log(a)
  a = 5 // 本身作用域定义a，所以赋值本身作用域的a = 5
  var a = 20
  console.log(this.a)
})()
```

```js
var a = 10
!(function(a) {
  console.log(a)
  a = 5
  var a = 20
  console.log(a)
})(a)
```

```js
var a = 10
!(function(b) {
  console.log(a)
  a = 5
  var a = 20
  console.log(b)
})(a)
```

```js
var a = 10
!(function a() {
  a = 5
  console.log(a)
  console.log(this.a)
})()
```

```text
题1
10 =》沿着作用域链，在全局作用域找到10
5 =》this是window，a = 5后，在全局作用域找到5

题2
undefined =》var a = 20,变量提升，所以打印undefined
10 =》由于a = 5赋值的是局部作用域的变量，所以全局作用域的a = 10

题3
解析：立即执行函数的参数为a，传入的参数值为10，所以相当于局部作用域的参数a = 10；如果这里不传参数，第一个console.log(a)还是undefined
10 =》a在参数中已经定义，而且执行时传入了10
20 =》var a = 20最后赋值，所以a取到的值为20

题4
解析：立即执行函数的参数为b，传入的参数为10，所以相当于局部b = 10
undefined =》var a = 20变量提升
10：如上b = 10

题5
解析：具名自执行函数名：只能在函数内部访问；相当于常量，不可更改。具体如下链接。
ƒ a() {
  a = 5
  console.log(a)
  console.log(this.a)
}
10
```

[在JavaScript的立即执行的具名函数A内修改A的值时到底发生了什么？](https://segmentfault.com/q/1010000002810093)

### DOM与虚拟DOM

[简单解说DOM和虚拟DOM的区别](https://zhuanlan.zhihu.com/p/55985627)

### clientWidth/offsetWidth和clientX/pageX/screenX/offsetX

![clientX/pageX/offsetX和clientWidth/offsetWidth](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210603/位置关系.5260i7hqfug0.svg)

#### clientWidth/offsetWidth

1. clientWidth = content + padding
2. offsetWidth = content + padding + border

#### clientX/pageX/screenX/offsetX

1. clientX：浏览器左侧到点击位置距离
2. pageX：clientX + scrollX
3. screenX：显示器左侧到点击位置距离
4. offsetX：点击元素的左侧到点击位置的距离

### 箭头函数与普通函数（function）的区别是什么？构造函数（function）可以使用 new 生成实例，那么箭头函数可以吗？为什么？

#### 区别

1. 箭头函数没有 this，它会从自己定义时的执行上下文继承 this（因此无法使用 apply / call / bind 进行绑定 this 值）
2. 不绑定arguments
3. 不能使用yield命令，所以箭头函数不能当作Generator函数

#### 箭头函数不能使用new生成实例

1. 箭头函数没有 this，它会从自己定义时的执行上下文继承 this（因此无法使用 apply / call / bind 进行绑定 this 值）
2. 箭头函数没有prototype，无法进行原型链的继承

#### 拓展

JavaScript内部有两个方法：[[Call]]，[[Construct]]

1. 正常调用执行使用的是[[Call]]方法
2. new调用执行[[Construct]]方法（箭头函数没有[[Construct]]方法，所以不能new生成实例）

### 值和引用

```js
function foo(x) {
  x.push(4)
  x = [5, 6, 7]
  x.push(8)
  return x
}

const a = [1, 2, 3]
console.log('foo(x)', foo(a))
console.log('original a', a)

// expected output
'foo(x)' [5, 6, 7, 8]
'original a' [1, 2, 3, 4]

// 将a当参数传递时，a和x都是[1, 2, 3]的引用
// x.push(4)，a和x的引用都改变为[1, 2, 3, 4]
// x = [5, 6, 7]，x重新赋值意味着x不再是[1, 2, 3, 4]的引用，但a没有改变引用
// x.push(8)，x指向[5, 6, 7, 8]，a还是指向[1, 2, 3, 4]
```

![值与引用](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210609/值与引用.1lkhiatufxcw.svg)

🚨 Notice：**不能通过引用x来改变引用a的指向，只能修改x和a共同指向的值！！！**

### 输出代码结果

```js
var a = {}, b = 123, c = '123'
a[b] = 'b'
a[c] = 'c'
console.log(a[b])

// expected output
c
```

```js
var a = {}, b = Symbol('123'), c = Symbol('123')
a[b] = 'b'
a[c] = 'c'
console.log(a[b])

// expected output
b
```

```js
var a = {}, b = {key: '123'}, c = {key: '123'}
a[b] = 'b'
a[c] = 'c'
console.log(a[b])

// expected output
c
```

```js
var a = {}, b = function() {return 123}, c = function() {return '123'}
a[b] = 'b'
a[c] = 'c'
console.log(a[b])

// expected output
b
```

#### 解析

这道题考察的是**对象键名的转换**。

1. 对象的键名只能是**字符串**和**Symbol类型**。
2. 其他类型的字符串会**转换成字符串**。
3. 对象默认回调用**toString()方法**。

🚨 Notice：对象类型中的很多封装函数都重写了toString()方法，比如Function，Date等，所以第四题中的b和c是不同的字符串，a[b]不会被a[c]覆盖。

### 关于var/let

1. var的话会直接在栈内存里预分配内存空间，然后等到实际语句执行的时候，再存储对应的变量，如果传的是引用类型，那么会在堆内存里开辟一个内存空间存储实际内容，栈内存会存储一个指向堆内存的指针。
2. let的话，是不会在栈内存里预分配内存空间，而且在栈内存分配变量时，做一个检查，如果已经有相同变量名存在就会报错。

[我用了两个月的时间才理解 let](https://fangyinghang.com/let-in-js/)

### 中文输入截断问题

> compositionstart/compositionend

[中文输入截断问题](https://segmentfault.com/a/1190000009246058)

### 类型转换-代码输出什么

```js
String('str') == new String('str')
String('str') === new String('str')

// expected output
true
false
```

#### 类型转换-代码输出什么-解析

1. 当不用 new 运算符调用 String() 时，它只把 s 转换成原始的字符串，并返回转换后的值。
2. 当 String() 和运算符 new 一起作为构造函数使用时，它返回一个新创建的 String 对象，存放的是字符串 s 或 s 的字符串表示。

🚨 Notice：String()返回的值类型是**String（字符串）**，new String()返回的值的类型是**Object（对象）**。

1. 第一题，类型不一致时，new String('str')的返回值会调用toString()方法，所以最后是String: 'str'与String: 'str'的比较，为true。
2. 第二题，类型不一致，返回false。

### 属性为null,undefined,function(){}的对象经过JSON.stringify()后的输出

> JSON.stringify(value, ?replacer, ?space)，将一个JavaScript对象转换成文本化的JSON。不能被文本化的属性会被忽略。

```js
const obj = {
  a: null,
  b: undefined,
  c: function() {}
}

JSON.stringify(obj)
```

```js
// expected output
"{\"a\":null}"


```

#### 参数

1. replacer是一个过滤函数或则一个数组包含要被stringify的属性名。如果没有定义，默认所有属性都被stringify。

    ```js
      const obj = {
        a: 1,
        b: false,
        c: 'hahaha'
      }
      // 改变属性值；删除属性
      JSON.stringify(obj, function(key,value) {
        if (key === 'a') {
          return 0
        }
        if (key === 'c') {
          return undefined // 返回undefined说明属性被删除
        }
      })
      // "{\"a\":0,\"b\":false}"

      // 过滤 => 使用数组过滤出需要的属性  
      JSON.stringify(obj, ['a', 'c'])
      // "{\"a\":1,\"c\":\"hahaha\"}"
    ```

2. 默认的JSON.stringify返回的值只要一行，而且完全没有空格，space可以添加多格，比如‘\t’。

    ```js
    const user = {name: 'andy', isDead: false, age: 11, addr: 'shanghai'};
    console.log(JSON.stringify(user, function(key, value){
        if(key === 'isDead'){
            return value === true ? 1 : 0;
        }
        return value;
    }, '\t'))
    // {
    //  "name": "andy",
    //  "isDead": 0,
    //  "age": 11,
    //  "addr": "shanghai"
    // }
    ```

#### 不能被转换的情况

1. 循环引用

    ```js
    const bar = {
      a: {
        c: foo
      }
    }
    const foo = {
      b: bar
    }
    ```

2. Symbol，undefined,Function（对象啥都不返回，但如果是数组的话有例外，返回null）

    ```js
    const obj = {
      a: null,
      b: false,
      c: Symbol(),
      d: undefined,
      d: function() {}
    }
    JSON.stringify(obj)
    // "{\"a\":null,\"b\":false}"

    const arr = [Symbol(), 2, undefined, 3, function(){}]
    JSON.stringify(arr)
    // [null, 2, null, 3, null]
    ```
