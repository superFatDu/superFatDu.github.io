---
title: 封装一个全局Loading
date: 2021-2-20 17:56:05
tags: Vue
categories: 前端
comment: true
---

> 由于平时工作过程中使用element ui等比较多，所以这种插件就没有怎么动手做过。懒的同时，也显示出基础十分薄弱，没有深入思考过Vue.use()到底要做什么。

# Vue.use(plugin)

> 安装插件。

## 参数

```js
{Object | Function} plugin
```

## 用法

如果参数是Object，需要提供install方法，如果是Function，则不需要。

## 注意

需要在new Vue()之前调用。

## 梯子

[浅谈Vue.use](https://segmentfault.com/a/1190000012296163)

# 封装全局Loading

## tree

```js
src
 ├─components
 │  └─loading
 │     ├─index.js
 │     └─index.scss
```

## index.js

```js
const MY_LOADING = {
    show: false, // loading显示状态
    component: null // loading组件
}

export default {
    install (Vue) {
        /**
        * $loading(type, text)
        * @param type {String} 开关状态 open/close
        * @param text {String} loading提示语
        */
        Vue.prototype.$loading = (text = '加载中，请稍后', type = 'open') {
            if (type === 'close') {
                MY_LOADING.show = MY_LOADING.component.show = false
            } else {
                if (MY_LOADING.show) {
                    return
                }
                const LOADING_COMPONENT = Vue.extend({
                    data () {
                        return {
                            show: MY_LOADING.show
                        }
                    },
                    template: `<div class="loading-mask" v-show="show">
                                <div class="loading-content">
                                  <i class="loading-icon"></i>
                                  <span class="loading-text">${text}</span>
                                </div>
                              </div>`
                })
                MY_LOADING.component = new LOADING_COMPONENT()
                const el = MY_LOADING.component.$mount().$el
                document.body.appendChild(el)
                MY_LOADING.show = MY_LOADING.component.show = true
            }
        }
        // 在原型上定义close方法
        Vue.prototype.$loading.close = (text) => {
            return Vue.prototype.$loading(text, 'close')
        }
    }
}
```

## index.scss

```css
.loading-mask {
  width: 100%;
  height: 100%;
  position: fixed;
  top: 0;
  left: 0;
  background: rgba(0, 0, 0, 0.4);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;

  .loading-content {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;

    .loading-icon {
      width: 50px;
      height: 50px;
      background: url('../../assets/loading.svg') 50% 50% no-repeat;
      background-size: 100%;
      animation: circle 5s infinite linear;
    }

    .loading-text {
      color: #ffffff;
    }
  }

  @keyframes circle {
    from {
      transform: rotate(0deg);
    }

    to {
      transform: rotate(360deg);
    }
  }
}
```

## main.js

```js
import Loading from 'src/components/loading/index'
import 'src/components/loading/index.scss'

Vue.use(Loading)
```

## test.vue

```js
methods: {
    handleOpen () {
        this.$loading('请求中，请稍后……')
    },
    handleClose () {
        this.$loading.close()
    }
}
```

# Vue.extend(option)

## 参数

```js
{Object} options
```

## 用法

1. 使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象。
2. data 选项是特例，需要注意 - 在 Vue.extend() 中它必须是函数。
3. 使用时需要实例化。**new name()**

## 示例

```html
<div id="mount-point"></div>
```

```js
var Profile = Vue.extend({
  template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
  data: function () {
    return {
      firstName: 'Walter',
      lastName: 'White',
      alias: 'Heisenberg'
    }
  }
})
// 创建 Profile 实例，并挂载到一个元素上。
new Profile().$mount('#mount-point')
```

# 问题

[Vue warn]: You are using the runtime-only build of Vue where the template compiler is not available. Either pre-compile the templates into render functions, or use the compiler-included build.

## 解决方法

```js
vue.config.js

module.exports = {
    runtimeCompiler: true
}
```
