---
title: vuejs组件相关
tags:
 - vue
---

### 基础

* 组件的data选项必须是个函数，这样每个实例都可以维护被返回对象的独立的拷贝。也就是，使用多个组件时(如组件复用时），彼此不会相互干扰。

  ```js
  // 错误用法
  data:{count : 0}
  // 推荐用法
  data: function(){
      return {
          count: 0;
      }
  }
  ```

* 通常一个应用会以一颗嵌套的组件树的形式组织。在使用组件时，必须先注册组件，注册组件的方式包括全局注册和局部注册两种。全局注册的组件可以用在其被注册之后的任何 (通过 `new Vue`) 新创建的 Vue 根实例，也包括其组件树中的所有子组件的模板中。![Component Tree](https://cn.vuejs.org/images/components.png)

```vue
// 全局注册
Vue.component('my-component-name', {
  // ... options ...
})
```

* props接收父组件传来的数据

  ```vue
  // 子组件
  Vue.component('blog-post', {
    props: ['title'],
    template: '<h3>{{ title }}</h3>'
  })
  
  // 父组件
  <blog-post title="My journey with Vue"></blog-post>
  <blog-post title="Blogging with Vue"></blog-post>
  ```

* 组件必须只能是单个根组件

* 监听子组件事件，也可以理解为子组件传数据到父组件，和props相反

  ```vue
  // 父组件 接受enlarge-text值，并 +0.1
  <blog-post
    ...
    v-on:enlarge-text="postFontSize += 0.1"
  ></blog-post>
  
  // 子组件 blog-post发送enlarge-text值
  <button v-on:click="$emit('enlarge-text')">
    Enlarge text
  </button>
  ```

* 插槽的使用

  ```vue
  // 使用组件
  <alert-box>
    Something bad happened.
  </alert-box>
  
  // 组件
  Vue.component('alert-box', {
    template: `
      <div class="demo-alert-box">
        <strong>Error!</strong>
        <slot></slot>
      </div>
    `
  })
  ```

  效果如下：

  <strong>Error! </strong> Something bad happened.

* 动态组件

  有的时候，在不同组件之间进行动态切换是非常有用的，比如在一个多标签的界面里，进行tab页的切换效果。

  ```vue
  <!-- 组件会在 `currentTabComponent` 改变时改变 -->
  <component v-bind:is="currentTabComponent"></component>
  ```

  currentTabComponent可以包括已注册组件的名字或者一个组件的选项对象。

### 深入组件

#### 组件的注册

* 注册组件是，需要给组件名

* 组件名的命名要求语义化，同时小写，推荐xxx-xxx-xxx形式

* 局部注册组件的使用示例：

  ```vue
  import ComponentA from './ComponentA.vue'
  export default {
    components: {
      ComponentA
    },
    // ...
  }
  ```

* 对于通用的基础组件，如按钮，输入框等等，可以使用 `require.context` 只全局注册这些非常通用的基础组件。

  ```javascript
  //在应用入口文件 (比如 src/main.js) 中全局导入基础组件的示例代码
  import Vue from 'vue'
  import upperFirst from 'lodash/upperFirst'
  import camelCase from 'lodash/camelCase'
  
  const requireComponent = require.context(
    // 其组件目录的相对路径
    './components',
    // 是否查询其子目录
    false,
    // 匹配基础组件文件名的正则表达式
    /Base[A-Z]\w+\.(vue|js)$/
  )
  
  requireComponent.keys().forEach(fileName => {
    // 获取组件配置
    const componentConfig = requireComponent(fileName)
  
    // 获取组件的 PascalCase 命名
    const componentName = upperFirst(
      camelCase(
        // 获取和目录深度无关的文件名
        fileName
          .split('/')
          .pop()
          .replace(/\.\w+$/, '')
      )
    )
  
    // 全局注册组件
    Vue.component(
      componentName,
      // 如果这个组件选项是通过 `export default` 导出的，
      // 那么就会优先使用 `.default`，
      // 否则回退到使用模块的根。
      componentConfig.default || componentConfig
    )
  })
  ```

#### Prop

* Prop的大小写，推荐kebab-case,也可以camelCase

  ```vue
  Vue.component('blog-post', {
    // 在 JavaScript 中是 camelCase 的
    props: ['postTitle'],
    template: '<h3>{{ postTitle }}</h3>'
  })
  ```

  ```html
  <!-- 在 HTML 中是 kebab-case 的 -->
  <blog-post post-title="hello!"></blog-post>
  ```

* props可以参数类型检查

  ```vue
  props: {
    title: String,
    // 多个可能的类型
    likes: [String, Number],
    isPublished: Boolean,
    commentIds: Array,
   // 必填的字符串
      propC: {
        type: String,
        required: true
    },
   // 带有默认值的数字
    propD: {
      type: Number,
      default: 100
    },
  // 自定义验证函数
      propF: {
        validator: function (value) {
          // 这个值必须匹配下列字符串中的一个
          return ['success', 'warning', 'danger'].indexOf(value) !== -1
        }
      }
    author: Object,
    callback: Function,
    contactsPromise: Promise // or any other constructor
  }
  ```

* 使用 title="value"的形式实现传递静态Prop,:title="value"传递动态Prop。prop可以是数字，布尔值，数组，对象。

* 单向数据流，从父组件到子组件。如果在子组件中修改prop，会发出警告。

#### 自定义事件

* 事件名始终使用kebab-case的形式，因为事件名不同于组件和prop，不会自动转换大小写，所以可能无法匹配。

* 自定义组件的v-model

  ```vue
  Vue.component('base-checkbox', {
    model: {
      prop: 'checked',
      event: 'change'
    },
    props: {
      checked: Boolean
    },
    template: `
      <input
        type="checkbox"
        v-bind:checked="checked"
        v-on:change="$emit('change', $event.target.checked)"
      >
    `
  })
  ```

  ```vue
  <base-checkbox v-model="lovingVue"></base-checkbox>
  ```

* 将原生事件绑定到组件的根元素上，可以使用.native,但是当这个根元素无法触发该事件，如label无法触发focus事件时，此时改绑定事件将失效。

  ```vue
  <test-input v-on:focus.native = "onFocus"></test-input>
  ```

* 某些情况下，需要对父组件传给子组件的prop进行双向绑定，此时就需要这样写：

  ```vue
  <text-document :title.sync = "doc.title"></text-document>
  ```

  ```vue
  this.$emmit('update:title',newTitle)
  ```

#### 插槽

##### 插槽内容 \<slot/>

```vue
<navigation-link url="/profile">
  可以是文本，HTML,组件等
</navigation-link>
```

```vue
<a
  v-bind:href="url"
  class="nav-link"
>
  <slot></slot>
</a>
```

##### 作用域

```vue
<navigation-link url="/profile">
  Clicking here will send you to: {{ url }}
  <!--
  这里的 `url` 会是 undefined，因为 "/profile" 是
  _传递给_ <navigation-link> 的而不是
  在 <navigation-link> 组件*内部*定义的。
  -->
</navigation-link>
```

父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。

#### 动态组件&异步组件

##### 动态组件中使用keep-alive

切换组件时，会保持这些组件的状态，避免重复渲染。

```vue
<!-- 失活的组件将会被缓存！-->
<keep-alive>
  <component v-bind:is="currentTabComponent"></component>
</keep-alive>
```

#### 处理边界情况

##### 访问元素&组件

* 根实例 this.$root

* 父级组件实例 this.$parent

* 子组件实例

  ```html
  <base-input ref="usernameInput"></base-input>
  ```

  ```javascript
  this.$refs.usernameInput
  ```