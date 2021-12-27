---
title: 你知道的Vue Router
date: 2021-6-12 9:33:19
tags: [你知道的系列, JavaScript, Vue Router]
categories: 前端
comment: true
---

## Vue Router

> Vue Router 是 Vue.js 官方的路由管理器。

### 使用概览

#### router.js

```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import DashBoard from '../views/DashBoard.vue'

Vue.use(VueRouter)

const routes = [
  {
    path: '/',
    name: 'HomePage',
    component: () => import('../views/home.vue')
  },
  {
    path: '/dashboard',
    name: 'DashBoard',
    component: DashBoard,
    children: []
  }
]

const router = new VueRouter({
  mode: 'hash' || 'history',
  base: process.env.BASE_URL,
  routes,
  scrollBehavior (to, from, savePosition) {
    return savePosition || { x: 0, y: 0 }
  }
})

// 解决重复点击跳转router时报错问题
// Avoided redundant navigation to current location
const originalPush = VueRouter.prototype.push
VueRouter.prototype.push = function push (location) {
  return originalPush.call(this, location).catch(err => err)
}

export default router
```

#### 挂载 - main.js

```js
import Vue from 'vue'
import router from './router'

new Vue({
  router
}).$mount('#app')
```

### mode选择

> 默认的是hash模式，我们可以配置成history模式。

```js
mode = options.mode || 'hash'
```

#### 具体实现类

参数mode字符串只是一个标记，实际起作用的是history的实现类:

🚨 Notice：如果是**非浏览器环境**，会**强制**性转换成‘**abstract**’模式

```js
switch (mode) {
  case 'hash':
    // fallback：如果浏览器不支持，'history'模式回滚回'hash'
    // this.fallback = mode === 'history' && !supportsPushState
    // if (this.fallback) mode = 'hash'
    this.history = new HashHistory(this, options.base, this.fallback)
    break;
  case 'history':
    this.history = new HTML5History(this, options.base)
    break;
  case 'abstract':
    this.history = new AbstractHistory(this, options.base)
    break;
  default:
    if (process.env.NODE_ENV !== 'production') {
      assert(false, `invalid mode: ${mode}`)
    }      
}
```

#### push()/replace()/onReady()

VueRouter类中暴露出来的这些方法，其实只是一个代理，实际调用的是具体的this.history对象的对应方法。

```js
push (location: RawLocation, onComplete?: Funciton, onAbort?: Function) {
  this.history.push(location, onComplete, onAbort)
}
replace (location: RawLocation, onComplete?: Funciton, onAbort?: Function) {
  this.history.replace(location, onComplete, onAbort)
}
```

#### hash

利用URL中的hash(#)，例如：www.test.com/#/test

##### hash特点

1. hash虽然出现在URL中，但**不会包括在HTTP请求中**，它是用来**指导浏览器动作**的，对服务器端无用，因此，**hash改变不会重新加载页面**。
2. 每一次**改变hash**（window.location.hash），都会在浏览器的**访问历史中新增一条记录**。
3. 可以为hash改变添加监听事件

    ```js
    window.addEventListener('hashchange', funcRef, false)

    setupListeners () {
      window.addEventListener('hashchange', () => {
        if (ensureSlash()) return
        this.transitionTo(getHash(), route => {
          replaceHash(route.fullPath)
        })
      })
    }
    ```

利用以上特点，实现前端路由“更新视图但不重新加载页面”。

#### history

利用HTML5 History interface新增的方法。（pushState()/replaceState()）

例如：www.test.com/test

```js
// @param stateObj 当浏览器跳转到新的状态时，将触发popState事件，该事件将携带stateObj参数的副本
// @param title 所添加记录的标题
// @param URL 所添加记录的URL
window.history.pushState(stateObj, title, URL)
window.history.replaceState(stateObj, title, URL)
```

##### history特点

1. 当调用他们修改浏览器历史纪录栈后，虽然当前URL改变了，但浏览器不会立即发送请求该URL(The browser won't attempt to load this URL after a call to pushState()/replaceState())。
2. 这就为history模式前页面应用前端路由“更新视图但不重新请求页面”提供了基础。

##### history模式刷新404处理

1. 后端处理
2. Nginx配置

```js
location / {
  root html;
  index index.html index.htm;
  // 尝试读取$uri(当前请求路径)
  // 读取不到在读取$uri/这个文件夹下的首页
  // 还是读取不到就返回 根目录下的index.html
  try_files $uri $uri/ /index.html;
}
```

### history比hash的优势

1. hash会带着#，**很丑**
2. pushState设置的新的URL可以时**当前URL同源的任意URL**；而hash只能修改#后面的部分，故只能设置于当前**同文档的URL**
3. pushState设置的URL**可以与当前的URL一摸一样**，这样也可以添加到浏览器的历史纪录栈中；而hash设置的值必须与原来的**不一样**才能触发添加
4. pushState可以通过stateObj添加**任意类型的数据**到记录中；hash只能添加**短字符串**
5. pushState可额外**设置title**属性供后续使用

### 源码

[vue-router github地址](https://github.com/vuejs/vue-router)

![vue-router-tree](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210609/vue-router-tree.1ic04gm1ghfk.png)

### 模拟实现

#### 实现思路

1. 创建VueRouter插件，静态方法install
    1. 判断插件是否已经被加载
    2. 当Vue加载时把传入的router对象挂载到Vue的实例上（只执行一次）
2. 创建VueRouter类
    1. 在构造函数中初始化、options、routeMap、app(简化操作，创建Vue实例作为响应式数据记录当前路径)
    2. initRouteMap()遍历所有路由信息，把组件和路由的映射记录到routeMap对象中
    3. 注册popstate事件，当路由地址发生变化，重新记录当前的路径
    4. 创建router-link和router-view组件
    5. 当路径改变的时候通过当前路径在routeMap对象中找到对应的组件，渲染router-view

#### 代码实现

##### 创建VueRouter插件

```js
export default class VueRouter {
  static install(Vue) {
    // 判断插件是否已经被加载
    if (VueRouter.install.installed && _Vue === Vue) return
    // 没加载时，初始化
    VueRouter.install.installed = true
    _Vue = Vue

    Vue.mixin({
      beforeCreate() {
        // 当Vue加载时把传入的router对象挂载到Vue的实例上
        if (this.$options.router) {
          _Vue.prototype.$router = this.$options.router

          // 初始化插件，调用init
          this.$options.router.init()
        }
      }
    })
  }
  constrctor(options) {
    // 存储参数
    this.options = options
    this.routeMap = {}
    this.app = new _Vue({
      data: {
        current: '/'
      }
    })
  }
  // 组件和路由的映射
  initRouteMap() {
    this.options.routes.forEach(route => {
      this.routeMap[route.path] = route.component
    })
  }
  // 实现router-link和router-view组件
  initComponents() {
    _Vue.component('RouterLink', {
      props: {
        to: String
      },
      render(h) {
        return h('a', {
          attrs: {
            href: '#' + this.to
          }
        }, [this.$slots.default])
      }
    })

    const self = this
     
    _Vue.component('RouterView', {
      render(h) {
        const component = self.routerMap(self.app.current)
        return h(component)
      }
    })
  }

  // 事件绑定
  initEvents() {
    window.addEventListener('hashchange', this.onHashChange.bind(this))
    window.addEventListener('load', this.onHashChange.bind(this))
  }
  onHashChange() {
    this.app.current = window.location.hash.substr(1) || '/'
  }

  // 初始化init**
  init() {
    initRouteMap()
    initComponents()
    initEvents()
  }
}
```
