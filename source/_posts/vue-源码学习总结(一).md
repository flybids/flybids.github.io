---
title: vuejs运用机制概览
tags:
 - vue
---

#### 序言

在断断续续做过一段时间的vue项目之后，不久又掘金上看到的vue学习小册，简单浏览一遍，受益良多。所以简单的通过摘录，笔记的形式，边学习便记忆，加深自己对vue原理的理解，并帮助自己日后进一步的研究vue的源码。

## 总览vue

![内部流程图](/img/vue)

### 初始化和加载

![初始化及加载](/img/vue_1)

在 new Vue() 之后，会调用 vue._init() 函数进行初始化，包括生成生命周期，事件，props, data, methods, computed, watch 等等。

最主要的是通过Object.defineProperty设置setter和getter函数，实现响应式和依赖采集；

初始化之后，通过$mount 挂载组件，存在template时，需要进行编译。

### 编译

![编译](/img/vue_2)

1. parse阶段

   借助正则表达式解析template中的css,class,style,指令等，生成AST

2. optimize阶段

   标记静态节点，这样以后update页面需要通过patch来比较渲染dom的时候，会跳过静态节点，从而减少比较的时间，提升patch的性能，对编译过程实现优化。

3. generate阶段

   将AST转换为render function字符串等。

经过以上三个阶段之后，组件中就会存在渲染VNode节点需要的函数。

### 响应式

![响应式](/img/vue_3)

在初始化阶段，我们设置了getter和setter方法，分别在读取和赋值的时候触发。

渲染组件的时候，会进行读取操作，触发getter函数，实现依赖收集，将观察者watcher收集放入当前闭包的订阅者Dep里面；

对组件进行修改时，会触发setter函数，setter会通知之前收集的所有观察者，让他们重新渲染视图。在渲染过程中，有用到update和patch等。

### Virtual DOM

render function 生成 vNode 节点，这些节点组成 vDom(js对真实DOM的抽象)

### 更新视图
