---
title: 前端基础知识体系-JS(异步)
date: 2021-5-4 10:28:14
tags: [知识体系,总结,JS]
categories: 前端
comment: true
---

## JS运行机制

1）所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。

（2）主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。

（3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。

（4）主线程不断重复上面的第三步。

## 同步

在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务。

## 异步

不进入主线程，而进入任务队列中的任务，只有任务队列通知主线程，某个异步任务可以执行了，这个任务才会进入主线程执行。

### 为什么有异步

JS是单线程执行，所以每次只能执行一个任务，当遇到花费很长时间的任务（网络请求、定时任务等），如果没有异步的话会阻塞（blocking）后面的代码执行。

### 异步应用场景

1. 网络请求：如Ajax图片加载
2. 定时任务：如setTimeout

### 异步解决方案

#### callback

```js
// 异步请求
function fetchData(callback) {
  $.ajax('/fetch', {
    // TODO
    success: callback
  })
}
// 数据处理
function handleData(data) {
  // TODO
}
// 方法调用
fetchData(handleData)
```

#### Promise

ES6给我们提供了一个原生的构造函数Promise，Promise代表了一个异步操作，可以将异步对象和回调函数脱离开来，通过.then方法在这个异步操作上绑定回调函数，Promise可以让我们通过链式调用的方法去解决回调嵌套的问题。

![promise](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210511/promises.66hdfzrjevs0.png)

##### 特点

1. 三种状态：Pending; Fulfilled; Rejected
2. 状态一经改变就不能修改。
    - Pending -> Fulfilled
    - Pending -> Rejected

##### Promise用法

Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。

```js
let promise = new Promise((resolve, reject) => {
  let num = Math.random()
  if (num > 0.5) {
    resolve(num)
  } else {
    reject(num)
  }
})

promise.then(res => {
  console.log(res)
}).catch(res => {
  console.log(res)
})
```

##### Promise API

1. Promise.resolve(value)
    ```js
    Promise.resolve('test') === new Promise(resolve => resolve('test'))
    ```
2. Promise.reject(reason)
3. Promise.all(iterable): 这个方法返回一个新的promise对象，该promise对象在iterable参数对象里所有的promise对象都成功的时候才会触发成功，一旦有任何一个iterable里面的promise对象失败则立即触发该promise对象的失败。
    ```js
    let p1 = new Promise(resolve => resolve('p1'))
    let p2 = new Promise(resolve => resolve('p2'))
    let p3 = new Promise(resolve => resolve('p3'))
    
    Promise.all([p1, p2, p3]).then(res => {
      console.log('promiseAll', res) // promiseAll ['p1','p2','p3']
    }).catch(error => {
      // TODO
    })
    ```
4. Promise.allSettled(iterable): 等到所有promises都已敲定（settled）（每个promise都已兑现（fulfilled）或已拒绝（rejected））。
返回一个promise，该promise在所有promise完成后完成。并带有一个对象数组，每个对象对应每个promise的结果。
    ```js
    let p1 = new Promise(resolve => resolve('p1'))
    let p2 = new Promise((resolve, reject) => reject('p2'))
    let p3 = new Promise(resolve => resolve('p3'))
    
    Promise.all([p1, p2, p3]).then(res => {
      console.log('promiseAllSettled', res)
    }).catch(error => {
      // TODO
    })

    // expected
    promiseAllSettled [{
      status: 'fulfilled', value: 'p1'
    }, {
      status: 'rejected', reason: 'p2'
    }, {
      status: 'fulfilled', value: 'p3'
    }]

    ```
5. Promise.race(iterable): 返回一个 promise，一旦迭代器中的某个promise解决或拒绝，返回的 promise就会解决或拒绝。
    ```js
    const promise1 = new Promise((resolve, reject) => {
      setTimeout(resolve, 500, 'one');
    });

    const promise2 = new Promise((resolve, reject) => {
      setTimeout(resolve, 100, 'two');
    });

    Promise.race([promise1, promise2]).then((value) => {
      console.log(value); // 'two'
      // Both resolve, but promise2 is faster
    });
    ```
6. Promise.any(iterable): 接收一个Promise对象的集合，当其中的一个 promise 成功，就返回那个成功的promise的值。
    ```js
    let p1 = new Promise((resolve, reject) => reject('p1'))
    let p2 = new Promise(resolve => resolve('p2'))
    let p3 = new Promise(resolve => resolve('p3'))
    
    Promise.any([p1, p2, p3]).then(res => {
      console.log('promiseAny', res)
    }).catch(error => {
      // TODO
    })

    // expected => p1 reject and p2 is the first resolve,so output is p2
    promiseAny 'p2'
    ```
7. Promise.prototype.then(onFulfilled,onRejected)
8. Promise.prototype.catch(onRejected)
9. Promise.prototype.finally(onFinally): 添加一个事件处理回调于当前promise对象，并且在原promise对象解析完毕后，返回一个新的promise对象。回调会在当前promise运行完毕后被调用，无论当前promise的状态是完成(fulfilled)还是失败(rejected)。=> finally的回调函数中不接收任何参数，它仅用于无论最终结果如何都要执行的情况。

##### 手写Promise

[简易Promise - cheng5.du](https://www.duchengwu.top/2021/05/06/简易Promise/)

##### then/catch

then/catch/finally是Promise原型上的方法，所以Promise的实例是可以访问的。但比如resolve/reject/all/allSettled/any都是Promise本身的方法，实例无法访问，只能通过Promise.resolve()这样调用。

🚨 Notice:
1. then 正常情况返回resolved，里面报错返回rejected
2. catch 正常情况返回resolved，里面报错返回rejected8

```js
Promise.resolve().then(() => {
  console.log(1)
}).catch(() => {
  console.log(2)
}).then(() => {
  console.log(3)
})

// output => 第一个then正常返回resolved，所以后面执行下一个then，忽略catch
1
3
```

```js
Promise.resolve().then(() => {
  console.log(1)
  throw new Eroor('error1')
}).catch(() => {
  console.log(2)
}).catch(() => {
  console.log(3)
})

// output => 第一个then报错返回rejected，所以接下来执行catch，catch里面没有报错，所以返回resolved，所以最后一个catch不执行
1
2
```

```js
Promise.resolve().then(() => {
  console.log(1)
  throw new Eroor('error1')
}).catch(() => {
  console.log(2)
}).then(() => {
  console.log(3)
})

// output => 第一个then报错返回rejected，所以执行第一个catch，catch没有报错返回resolved，下面继续执行then
1
2
3
```

#### async/await

归根结底，async/await是Promise的语法糖，本质上任然是Promise。

[如何正确合理使用 JavaScript async/await ！ - 前端小智](https://segmentfault.com/a/1190000017718513)

##### 执行顺序

await下一行及后面的代码会被推到微任务队列，当然await后面函数的同步代码会执行。

```js
async function awaitFn() {
  console.log('Fn start')
  setTimeout(() => {
    return Promise.resolve('Fn await')
  }, 0)
  console.log('Fn end')
}

console.log('script start')
await awaitFn()
console.log('script end')

// output
script start
Fn start
Fn end
script end
```

```js
async function async1() {
  console.log('async1 start')// 2  主线程async1()调用
  await async2()
  console.log('async1 end')  // 6 主线程内同步代码执行完毕后接着执行await函数内后面的代码（至此主线程内同步代码都执行完毕）
}

async function async2() {
  console.log('async2')// 3 主线程async1() => async2()  await 会先执行完async2()内的代码
}

console.log('script start')// 1  主线程同步代码
setTimeout(function() { 
  console.log('setTimeout') // 8 setTimeout放入宏队列中下次轮询执行
}, 0)

async1(); 
  
new Promise( function( resolve ) {
  console.log('promise1') // 4 promise内的同步代码会在主线程内先执行
  resolve();
} ).then( function() {
  console.log('promise2') // 7 promise回调会放入微队列中，主线程代码执行完毕后执行
} )

console.log('script end') // 5 主线程同步代码

// output
script start
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
```

```js
console.log('AAAA') // 同步1
setTimeout(() => console.log('BBBB'), 1000) // 异步-宏任务1
const start = new Date() 
while (new Date() - start < 3000) {} // waiting 3s
console.log('CCCC') // 同步2
setTimeout(() => console.log('DDDD'), 0) // 异步-宏任务2
new Promise((resolve, reject) => {
  console.log('EEEE') // 同步3
  foo.bar(100) // foo is not defined报错，后面可以catch
})
  .then(() => console.log('FFFF'))
  .then(() => console.log('GGGG'))
  .catch(() => console.log('HHHH')) // 异步-微任务1
console.log('IIII') // 同步4

// expected output
AAAA 同步1
waiting 3s
CCCC 同步2
EEEE 同步3
IIII 同步4
HHHH 异步-微任务1
BBBB 异步-宏任务1
DDDD 异步-宏任务2
```
