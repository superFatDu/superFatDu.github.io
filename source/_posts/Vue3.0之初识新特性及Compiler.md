---
title: Vue3.0之初识新特性及Compiler
date: 2021-4-11 16:13:27
tags:  Vue3.0
categories: 前端
comment: true
---

## Vue3.0变化

1. 性能提升1.3 - 2x

![Vue3.0性能提升](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210407/vue3.0.3o9574s5sa40.png)

1.1 优化案例1

- 静态Node不再做更新处理（hoistStatic => SSR优化）
- 静态绑定class、id不再做更新处理
- 结合打包标记patchFlag，进行更新分析（动态绑定）
![案例1](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210407/Vue3.0案例1.6crcjf2jjac0.png)

1.2 优化案例2

- 事件监听器Cache缓存处理（cacheHandlers）
- hoistStatic自动针对多静态节点进行优化，输出字符串
- 按需加载（没有节点就是一个render函数，添加节点方法等在加载）
![案例2](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210407/vue3.0案例2.2ohmna29ooe0.png)

2. TS支持，新增：Fragment,Teleport,Suspense

2.1 Fragment - 不受根节点的限制，渲染函数可以接收Array
2.2 Teleport - 类似于Portal，随用随取，比如弹窗，actions
2.3 Suspense - 嵌套的一异步依赖，比如async setup()

3. 按需加载（配合Vite）& 组合API（composition-api）

## 为什么要使用Composition API

1. Vue2.x对于复杂逻辑组件，在后期变得无法维护（逻辑拆分很多，比如components;props;data;methods;life circle等，太复杂了）
2. Vue2.x中代码的复用方法，如Mixin，Filters都有缺陷（Mixin的命名冲突，逻辑冲突，不宜复用等；scoped slot作用域插槽配置项多，代码分裂，性能差等……）
3. Vue2.x对TS支持不充分
4. 总结：复杂组件逻辑进行分离；组件间的逻辑共享

## Vue3.0新特性

[Vue3.0新特性](http://www.liulongbin.top:8085/#/)

## Composition API

[composition-api](https://v3.cn.vuejs.org/guide/composition-api-introduction.html)