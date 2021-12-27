---
title: 你知道的Vuex
date: 2021-6-5 15:22:13
tags: [你知道的系列, JavaScript, Vuex]
categories: 前端
comment: true
---

## Vuex

> Vuex是一个专门为Vue.js应用程序开发的**状态(state)管理模式**。它采用**集中式存储管理应用的所有组件的状态，并以相应的规则保证以一种可预测的方式发生变化**。

### Vuex 和单纯的全局对象的区别

1. **Vuex的状态存储是响应式的**。当Vue从store中读取状态的时候，若store中的状态发生变化，那么相应的组件也会相应的高效更新。
2. **你不能直接修改store中的状态**。改变store中的状态的唯一途径是显示地提交（**commit**）**mutation**。

### 结构

```js
└── store
    ├── index.js              // store入口文件 
    ├── state.js              // 状态
    ├── getters.js            // state衍生状态（store的计算属性）
    ├── actions.js            // 提交mutation方法，不改变state
    ├── mutation-type.js      // 定义mutation名称
    ├── mutation.js           // 提交mutation，改变state
    └── Modules               // store分割成模块
        ├── moduleA.js        // moduleA模块
        └── moduleB.js        // moduleB分割成模块
```

### 不同模式实践

#### 普通模式

##### index.js

```js
import Vue from 'vue'
import Vuex from 'vuex'
import state from './state'
import mutations from './mutations'
import * as actions from './actions'
import * as getters from './getters'

Vue.use(Vuex)

export default new Vuex.Store({
  state,
  mutations,
  actions,
  getters
})
```

##### state.js

```js
const state = {
  name: 'cheng5'
}

export default state
```

##### mutation-types.js

> mutations.js中键名的映射，防止拼写错误等。

```js
const PUSH_DATA_TO_STATE = 'PUSH_DATA_TO_STATE'
const GET_NAME_BY_KEY = 'GET_NAME_BY_KEY'

export { PUSH_DATA_TO_STATE, GET_NAME_BY_KEY }
```

##### mutations.js

> store.commit(mutationName)触发。

```js
import * as types from './mutation-types'

// 变量的值作为属性名，需要用[]包起来，等同于：
// const mutations = {} mutations[types.ADD_MY_AGE] = 'xxx'
const mutations = {
  [types.PUSH_DATA_TO_STATE] (state, param) {
    // TODO
  }
}
```

##### actions.js

> store.dispatch(actionName)触发。

actions.js支持异步操作，然后通过actions提交mutation改变状态。如果mutations支持异步操作，那就没有办法知道状态时何时更新的，无法很好的追踪状态，给调试带来麻烦。

```js
import * as types from './mutation-types'

export const handleAsyncData = function ({commit}, prarm) {
  Promise.resolve(param).then(res => {
    commit(types.PUSH_DATA_TO_STATE, res)
  })
}
```

##### getters.js

> 有时候我们需要从state派生出一些状态，我们可以使用getters，可以接受state，和其他getters作为参数，返回一个派生的state。getters相当于store的computed属性。

```js
export const getName = function (state) {
  return state.name
}
```

##### 使用

> mapGetters在computed中，mapActions和mapMutations在methods中。

```html
<p>{{$store.state.name}}</p>

<p>{{ getName }}</p>

<button @click='handleAsyncData(18)'>triggerAction</button>
<button @click='putDataToState(18)'>triggerMutation</button>
```

```js
import { mapGetters, mapActions, mapMutations }

methods: {
  ...mapActions(['handleAsyncData']),
  ...mapMutations({
    putDataToState: 'PUSH_DATA_TO_STATE'
  })
},
computed: {
  ...mapGetters(['getName'])
}

```

#### 模块模式

##### module - index.js

```js
import Vue from 'vue'
import Vuex from 'vuex'
import moduleA from './modules/a'
import moduleB from './modules/b'

Vue.use(Vuex)

export default new Vuex.Store({
  state: {},
  mutations: {},
  actions: {},
  getters: {},
  modules: {
    moduleA,
    moduleB
  }
})
```

##### module - a.js

```js
const moduleA = {
  namespaced: true,
  // state: {...},
  state: () => ({...}),
  actions: {...},
  mutations: {...},
  getters: {...}
}

export default moduleA
```

##### module - b.js

```js
const moduleB = {
  namespaced: true,
  // state: {...},
  state: () => ({...}),
  actions: {...},
  mutations: {
    setAge (state, param) {
      state.age = param
    }
  },
  getters: {...}
}

export default moduleB
```

🚨 Notice：因为分割模块了，所以为了更高的封装和可复用性，我们**给每个module加上了namespaced属性**，如果不加的话，每个模块包括更节点的同名方法都会触发。使用的时候**加上module名称**，如‘**moduleA/setAge**’进行区分。

##### module - 使用

```html
<p>{{ store.state.a.name }}</p>
<button @click="handleAge(18)">click</button>
```

```js
methods: {
  handleAge (age) {
    this.$store.commit('moduleA/setAge', age)
  }
}
```

### 挂载store

```js
// main.js
import store from './store'

new Vue({
  store
}).$mount('#app')
```

### 规则

1. 应用层级的状态应该集中到单个store对象中。
2. 提交mutation是更改状态的唯一方法，并且这个过程是同步的。
3. 异步逻辑都应该封装到action里面。

### 插件

Vuex的store接受plugins选项。如下持久化处理。

```bash
yarn add vuex-persistedstate 
```

```js
import createPersistedState from 'vuex-persistedstate'

// 默认存储到localStorage，可以配置sessionStorage
new Vuex.Store({
  // TODO
  plugins: [createPersistedState({
    storage: window.sessionStorage
  })]
})
```
