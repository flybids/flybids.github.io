---
title: 组件间的通信-provide/inject
tags:
 - vue
---

在实际开发中，对于比较复杂的组件间的通信，我们一般会引入vux等第三方库。但是在单独开发组件，不考虑引入第三方资源，如开发组件库时，我们可以使用vue内置的provide和inject。

provide相当于主动提供，而inject是依赖注入。

如何理解provide和inject?直接看代码。

```javascript
// 父级组件提供 'foo' 给所有子组件
var Provider = {
  provide: {
    foo: 'bar'
  },
  // ...
}

// 子组件注入 'foo'
var Child = {
  inject: ['foo'],
  created () {
    console.log(this.foo) // => "bar"
  }
  // ...
}
```

<i style="color:'red'">`provide` 和 `inject` 主要为高阶插件/组件库提供用例。并不推荐直接用于应用程序代码中</i>

他们的局限在于，多用于子组件获取父组件的状态，对于父子组件的通信就不适用。对此，见下文。