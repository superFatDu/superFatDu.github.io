---
title: 浅话事件循环（Event Loop）
date: 2021-4-8 23:48:12
tags: JavaScript
categories: 前端
comment: true
---

## 概述

首先，我们需要明确：

1. JS是单线程（single threaded）运行的（所以无论同步还是异步都需要排队）。
2. 异步(asynchronous)要基于回调（callback）来实现。
3. event loop就是异步回调的实现原理。

🚨 Notice：进程是 cpu 资源分配的最小单位（是能拥有资源和独立运行的最小单位）;线程是 cpu 调度的最小单位（线程是建立在进程的基础上的一次程序运行单位，一个进程中可以有多个线程）。

### 一些概念

#### JS是单线程执行（JS引擎线程）

> 在浏览器环境中，有JS 引擎线程和渲染线程，且两个线程互斥。Node环境中，只有JS 线程。

##### 主线程、JS引擎线程、渲染线程

一般浏览器中，页面渲染和 JS 执行虽然是2个工作，用不同的模块和引擎去做，但是他们都是放在一个叫做主线程（Main Thread）的线程里执行的。

主线程中即可以执行 JS ，也可以执行渲染工作，还可以执行其他工作，但是由于是主线程是单线程，所以各个工作不能同时执行，只能按照一定的先后次序执行。

如果在你的 JS 代码执行的过程中，使得文档或者样式发生了改变，那么在你的这串 JS 代码执行完成后，才会去触发渲染，使得页面呈现新的效果。

🚨 Notice：每一个网页有一个主线程，来执行他的JS、渲染。但是浏览器是多线程的，它可以同时开多个网页，因此对于包含多个标签页的浏览器来说，它是可以开多个主线程的。不同标签页里不同主线程是平行的。浏览器中每个网页的 JS 都靠同一个 JS 引擎来执行，但是他们的主线程是分离的，执行上下文也是分离的。

#### 宿主

JS运行时的环境，一般指的是浏览器或者Node。

#### 执行栈

1. JS 代码在运行时都是在执行上下文中进行的。
    - 全局执行上下文，默认的，在浏览器中是 window 对象，并且 this 在非严格模式下指向它。
    - 函数执行上下文，JS 的函数每当被调用时会创建一个上下文。
    - Eval 执行上下文，eval 函数会产生自己的上下文。
2. 栈，是一种数据结构，具有先进后出的原则。

那么，执行栈其实就是push/pop执行上下文的数据结构。

```js
function foo() {
  bar()
}
funciton bar() {}
foo()
```

![ec](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210508/ec.3d4vz5mhgrw0.png)

## Event Loop

![EVENT LOOP1](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210507/eventloop.6zm0jk6zu140.svg)

### Event Loop执行顺序（以如上代码为例）

![eventloopintro](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210508/eventloopintro.4d2pe0a0z800.png)

🚨 Notice：输出undefined的原因是：同步函数在调用栈中都执行完毕弹出，且最后一个函数没有返回值时，默认返回undefined。

1. JS引擎会检查整段代码的语法错误，如果没有错误，就从头开始深度解析
2. 首先遇到setTimeout函数调用，把它推入执行栈顶
3. 解析函数体，发现setTimeout函数是Web API的一种，因此就把它分发到Web API模块然后推出栈
4. 因为定时器设置了0ms延迟，因此Web API模块立即把它的匿名回调函数推入到回调函数函数队列。事件循环检测执行栈是否是空闲，但是当前栈并不空闲，因为还有其他的代码。
5. 当setTimeout函数一被分发到Web API模块，JS引擎发现了两个函数声明，把它们存储在堆内存里，然后遇到了sayHi函数的调用，就把它推入了栈顶
6. sayHi函数调用了console.log函数，因此console.log就被推入了栈顶
7. JS引擎开始解析console.log的函数体，它接收了一个消息去打印‘Hi’，然后被弹出栈
8. JS引擎返回到函数sayHi的执行，遇到函数的结束符号}之后，把它弹出栈
9. sayHi函数一出栈，紧接着sayBye函数被调用，它就被推入栈顶，被解析，调用console.log，把console.log推入栈顶，打印一条消息，弹出栈。然后sayBye函数弹出栈
10. 事件循环检测到执行栈终于空闲了，通知回调队列，然后回调队列把其中的匿名函数推入执行栈
11. 匿名函数（就是setTimeout的回调函数）被解析、调用console.log，console.log推入栈顶
12. console.log执行完毕、再出栈
13. 匿名函数再被推出栈，程序结束。

### 宏任务和微任务

> ES6 规范中，microtask 称为 jobs，macrotask 称为 task宏任务是由宿主发起的，而微任务由JavaScript自身发起。

执行栈清空 => 执行微任务 => 尝试DOM渲染 => 执行宏任务

![EVENT LOOP](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210508/eventloop2.7gj42zx2oo40.webp)

#### 宏任务（macrotask）和 微任务（microtask）区别

![diff](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210511/hongweirenwu1.3r3clo6ocra0.svg)

#### 执行步骤

1. 执行宏任务（script(整体代码)）。
2. 有同步任务直接执行，遇到异步任务划分为宏任务和微任务。
3. 然后有微任务执行微任务，反之执行宏任务（微任务的优先级大于宏任务）。

![执行步骤](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210508/宏任务微任务.3edhq6w4i7k0.jfif)

#### 执行顺序

宏任务（script(整体代码)）> 微任务 > 宏任务
