---
title: vuejs基础拾遗（一）
tags:
 - vue
---

### 序言

在学习并使用vue框架开发了几个项目后，发现自己对vue的一些基本属性和概念有所疏漏。闲暇之余，便重拾vue官网文档，见有不熟悉之处，便摘录余下。以后时有更删。

### Vue实例

#### 创建实例

```vue
new Vue({})
```

#### 数据与方法

可以使用Object.freeze()冻结一个对象，使对象的属性无法被修改，但是响应式系统也无法追踪变化。

```vue
let obj = {foo : 'bar'}
Object.freeze(obj)
new Vue({el:'#app', data:obj})
```

#### 生命周期

* 生命周期函数不建议使用箭头函数，否则会有this的指向问题
* 生命周期
  1. beforeCreate
  2. created
  3. beforeMount
  4. mounted
  5. beforeUpdate
  6. updated
  7. beforeDestroy
  8. destroyed

### 模板语法

#### 插值

* 原始HTML,把插值按照HTML进行解析

  动态渲染任意HTML比较危险，容易导致xss攻击。<strong>只对可信内容使用HTML插值，绝不对用户提供的内容使用插值</strong>。

  ```vue
  <p>
      Using: <span v-html="rawHtml"></span>
  </p>
  ```

* 使用v-once指令，一次性插值，当数据更新时，插值处的内容不更新。

  ```vue
  <span v-once>{{ msg }}</span>
  ```

#### 指令

* v-if  
* v-show
* v-bind  等同于 :  绑定值
* v-on 等同于@ 绑定dom事件

### 计算属性和侦听器

#### 计算属性

* 对于任何复杂逻辑，都应当使用计算属性

  ```vue
  <p>
      {{ reverseMsg }}
  </p>
  <script>
  var vm = new Vue({
  el:'#example',
  data:{
  msg: 'Hello'
  },
  computed:{
  reverseMsg: function(){
  return this.msg.split('').reverse().join('')
  }
  }
  })
  </script>
  ```

* vs methods

  computed中的方法动态绑定相关的值，如上述的msg.当msg变化时，会重新执行渲染。同时，只要msg没有变化，即便多次使用reverseMsg也不会重复执行多次reverseMsg，相当于缓存。

* vs watch

  可以使用computed的话，就不要使用watch.使用watch可能代码重复等。

* setter

  ```vue
  computed: {
    fullName: {
      // getter
      get: function () {
        return this.firstName + ' ' + this.lastName
      },
      // setter
      set: function (newValue) {
        var names = newValue.split(' ')
        this.firstName = names[0]
        this.lastName = names[names.length - 1]
      }
    }
  }
  ```

  #### 侦听器

  当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。除了 `watch` 选项之外，您还可以使用命令式的 [vm.$watch API](https://cn.vuejs.org/v2/api/#vm-watch)。

### Class与Style绑定

#### 绑定HTML Class

* 对象语法

  ```vue
  <div v-bind:class="classObject"></div>
  <script>
  data: {
    isActive: true,
    error: null
  },
  computed: {
    classObject: function () {
      return {
        active: this.isActive && !this.error,
        'text-danger': this.error && this.error.type === 'fatal'
      }
    }
  }
  </script>
  <style>
      .active{}
      .text-danger{}
  </style>
  ```

* 数组语法

  ```vue
  <div v-bind:class="[activeClass, errorClass]"></div>
  data: {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
  ```

  渲染为：

  ```html
  <div class="active text-danger"/>
  ```

* 用在组件上

  当在一个自定义组件上使用 `class` 属性时，这些类将被添加到该组件的根元素上面。这个元素上已经存在的类不会被覆盖。

#### 绑定内联样式

* 对象语法

  ```vue
  <div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
  <script>
  data: {
    activeColor: 'red',
    fontSize: 30
  }
  </script>
  ```

  更推荐：

  ```vue
  <div v-bind:style="styleObject"></div>
  data: {
    styleObject: {
      color: 'red',
      fontSize: '13px'
    }
  }
  ```

* 数组语法

  将多个样式对象应用到同一个元素上

  ```vue
  <div v-bind:style="[baseStyles, overridingStyles]"></div>
  ```

* 自动添加前缀

  当 `v-bind:style` 使用需要添加[浏览器引擎前缀](https://developer.mozilla.org/zh-CN/docs/Glossary/Vendor_Prefix)的 CSS 属性时，如 `transform`，Vue.js 会<strong>自动侦测并添加相应的前缀</strong>。

* 多重值

  v2.3.0+

  为 `style` 绑定中的属性提供一个包含多个值的数组，常用于提供多个带前缀的值。

  ```vue
  <div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
  ```

  在本例中，如果浏览器支持不带浏览器前缀的 flexbox，那么就只会渲染 `display: flex`。

### 条件渲染

#### v-if vs v-show

* v-if

  `v-if` 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。

  `v-if` 也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

* v-show

  不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

* 如何选择

  `v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。

#### 不推荐同时使用 v-if v-for 

`v-for` 具有比 `v-if` 更高的优先级

### 列表渲染

#### v-for

1. v-for = "item in items"
2. v-for = "item of items"

可以遍历对象和数组，在遍历对象时，是按 `Object.keys()` 的结果遍历，但是不能保证它的结果在不同的 JavaScript 引擎下是一致的。

#### 对象更改检测注意事项

由于 JavaScript 的限制，**Vue 不能检测对象属性的添加或删除**。

```vue
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})
```

对于已经创建的实例，Vue 不能动态添加根级别的响应式属性。但是，可以使用 `Vue.set(object, key, value)` 方法向嵌套对象添加响应式属性。

```vue
Vue.set(vm.userProfile, 'age', 27)
```

你还可以使用 `vm.$set` 实例方法，它只是全局 `Vue.set` 的别名。

```vue
vm.$set(vm.userProfile, 'age', 27)
```

#### 过滤和排序

有时，我们想要显示一个数组的过滤或排序副本，而不实际改变或重置原始数据。在这种情况下，可以创建返回过滤或排序数组的计算属性。

在计算属性不适用的情况下 (例如，在嵌套 `v-for` 循环中) 你可以使用一个 method 方法。

#### 一段取值范围

`v-for` 也可以取整数。在这种情况下，它将重复多次模板。

```vue
<div>
  <span v-for="n in 10">{{ n }} </span>
</div>
```

渲染为：

```html
1 2 3 4 5 6 7 8 9 10
```

#### v-for on a \<template>

类似于 `v-if`，你也可以利用带有 `v-for` 的 `<template>` 渲染多个元素。

```vue
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```