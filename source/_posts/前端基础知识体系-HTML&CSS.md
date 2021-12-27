---
title: 前端基础知识体系-HTML&CSS
date: 2021-4-15 16:55:34
tags: [知识体系,总结,HTML,CSS]
categories: 前端
comment: true
---

## HTML

### 如何理解语义化？

1. 让人更容易读懂（增加代码可读性）
2. 让搜索引擎更容易读懂（SEO）

### 默认情况下，哪些标签式块级元素？哪些式内联元素？

1. 块级：div h1 table ul ol p ……
2. 内联：span img input button ……

## CSS

### CSS - 布局

#### 盒子模型的宽度如何计算？

1. IE：width + padding + border
2. 标准：width

#### clientWidth/offsetWidth

1. clientWidth: width + padding
2. offsetWidth: width + padding + border

![width](https://cdn.jsdelivr.net/gh/superFatDu/blogPics@main/20210414/width.6u75kmtp1iw0.gif)

#### margin纵向重叠问题

1. margin-top和margin-bottom在纵向会发生重叠，取大值
2. BFC可以消除重叠问题

#### margin负值问题

1. margin-top/margin-left为负值，元素向上/向左移动
2. margin-right为负值，右侧元素左移，自生不受影响
3. margin-bottom为负值，下侧元素上移，自生不受影响

#### BFC的理解和应用

1. Block Format Context，块级格式化上下文。一块独立的渲染区域，内部元素的渲染不会影响边界以外的元素。
2. 常见BFC形成条件
    - float不是none
    - position是absolute/fixed
    - overflow不是visible；display是flex/inline-block
3. 常见应用：清除浮动；清除折叠边距

#### float布局的问题，以及clearfix

1. 圣杯布局
    - 使用float和padding撑开两边
2. 双飞翼布局
    - 使用float和margin撑开两边
3. clearfix实现

    ```css
    .clearfix::after {
      content: '';
      display: table;
      clear: both;
    }

    /* 兼容ie低版本 */
    .clearfix {
      *zoom: 1;
    }
    ```

#### flex布局的问题

##### flex概念

> flex属性是flex-grow、flex-shrink、flex-basis属性的简写。

```css
.flex {
  flex: flex-grow flex-shrink flex-basis|auto|initial|inherit;
}
```

1. flex-grow是增长系数，接收一个数字。
2. flex-shrink是收缩系数，接收一个数字。
3. flex-basis是 元素在主轴方向上的初始大小，接收像素值或者auto/initial/inherit。

```css
.flex {
  flex: 1; // flex-grow: 1
  flex: 1 1; // flex-grow: 1 & flex-shrink: 1
  flex: 1 30px; // flex-grow: 1 & flex-basis: 30px;
  flex: 2 2 50px; // flex-grow: 2 & flex-shrink: 2 & flex-basis: 50px
}
```

##### flex语法

1. flex-direction
2. flex-wrap
3. justify-content
4. align-items
5. align-self

### CSS - 定位

#### relative/absolute/fixed分别根据什么定位

1. relative：依据自身定位
2. absolute：依据最近一层的定位元素定位
    - absolute relative fixed
    - body（如果外层没有以上定位元素时）
3. fixed：依据浏览器窗口定位

#### 居中对齐有哪些方式

##### 水平居中

```css
/* inline元素 */
text-align: center;

/* block元素 */
margin: 0 auto;

/* 定位 */
position: absolute;
left: 50%;
transform: translateX(-50%);
```

##### 垂直居中

```css
/* inline元素 */
line-height: 10px;

/* 定位 */
position: absolute;
top: 50%;
transform: translateY(-50%);
```

##### 水平垂直居中

```css
/* 方法一 */
position: absolute;
top: 50%;
left: 50%;
transform: translate(-50%, -50%);

/* 方法二 */
position: absolute;
top: 0;
bottom: 0;
left: 0;
right: 0;
margin: 0;
```

### CSS - 图文样式

#### line-height如何继承

##### p标签的行高是多少

```css
body {
  font-size: 20px;
  line-height: 200%;
}
p {
  font-size: 16px;
}
```

> 40px

##### 总结

1. 如果时像素值，就直接继承像素值
2. 如果时数字，就直接 本身font-size * line-height
3. 如果百分比，就需要在定义的地方进行计算完成，然后继承给字类。

### CSS - 响应式

#### rem/em

1. rem：相对长度单位，相对于根元素。
2. em：相对长度单位，相对于父元素。

#### vw/vh

1. vw：视口（浏览器窗口的大小）单位，1vw = window.innerWidth / 100
2. vh：视口（出去导航bar的高度）单位，1vh = window.innerHeight / 100

#### 常见的解决方案

1. media-query：根据不同的屏幕宽度设置根元素font-size。
2. rem：基于根元素的相对单位。
3. vw/vh
