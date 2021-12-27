---
title: Vue性能优化
date: 2021-2-10 22:43:27
tags: [Vue, 性能]
categories: 前端
comment: true
---

## Vue代码层面

1. v-if和v-show分场景使用
    1. v-if 适用于在运行时很少改变条件，不需要频繁切换条件的场景。
    2. v-show 则适用于需要非常频繁切换条件的场景。
2. computed和watch分场景使用
    1. 当我们需要进行数值计算，并且依赖于其它数据时，应该使用 computed，因为可以利用 computed 的缓存特性，避免每次获取值时，都要重新计算；
    2. 当我们需要在数据变化时执行异步或开销较大的操作时，应该使用 watch，使用 watch 选项允许我们执行异步操作 ( 访问一个 API )，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。
3. v-for遍历时必须为item添加key，且避免同时使用v-if
    1. 在列表数据进行遍历渲染时，需要为每一项 item 设置唯一 key 值，方便 Vue.js 内部机制精准找到该条列表数据。当 state 更新时，新的状态值和旧的状态值对比，较快地定位到 diff 。
    2. v-for的优先级高于v-if，每一次都需要遍历整个数组，影响速度。
4. 长列表
    1. 如果只是纯粹的数据展示，可以使用Object.freeze()方法冻结一个对象。
5. 事件销毁
    1. 组件本身的事件会自动销毁，如果是手动添加的（addEventListener）则需要手动清除（removeEventListener）。
6. 图片懒加载
    1. vue-lazyload
7. 路由懒加载
    1. const comp = () => import('...')
8. 第三方插件按需引入
9. SSR

## Webpack层面优化

1. 对图片压缩
    1. image-webpack-loader
2. 提取公共css
3. 优化SourceMap

## Web技术优化

1. 开启gzip
    1. nginx配置，webpack配置
2. 浏览器缓存
    1. cache
3. CDN
    1. CDN 可以通过不同的域名来加载文件，从而使下载文件的并发连接数大大增加，且CDN 具有更好的可用性，更低的网络延迟和丢包率 。
4. Chrome Performance查看
