---
title: 组件间的通信—派发（dispatch)和广播（broadcast)
tags:
 - vue
---

根据前文，provide/inject的使用具有局限性，对于父子组件的通信必须有其他的办法。

### $on 和 $emit

在当前vue.js中，较常用的是$on 和 $emit。$emit主要用来触发事件，而$on用来监听被触发的事件。一般来说，子组件中使用$emit，父组件中使用$on。二者具体的用法如下：

```vue
<template>
 <button @click="handleClick">
   <slot>确定</slot>
 </button>
</template>
<script>
    export defalut{
        methods:{
            handleClick(){
                this.$emit('parent','123')
            }
        }
    }
</script>
```

```vue
<template>
	<i-btn @parent = 'test'/>
</template>
<script>
    export default{
        methods:{
            test(data){
                console.log(data)
            }
        }
    }
</script>
```

### dispatch 和 broadcast

上述写法一般只能用于一级的父子组件，那么对于多级，在1.x版本的vue.js中使用dispatch 和 broadcast。

* dispatch: 子组件向所有的父组件分发，从最近的向远处传播

* broadcast: 父组件向子组件广播，从近到远

他们传递的事件一旦被某组件通过$on监听到了，就会停止冒泡下去。可惜的是，在新版本vue.js中已经被废弃。

### 自主实现dispatch 和 broadcast

预计功能：可以向父级组件或子组件，由近到远传播查询，直到找到对应的组件（更据唯一组件name是否一致），然后在改组件中进行监听。

需要传的参数：目标组件名，事件名，事件中用到的参数

具体代码实现如下：

```javascript
// emmiter.js
/**
 * 父组件向子组件广播事件
 * @param cName 目标组件名
 * @param eName 事件名
 * @param eData 事件参数
 */
function broadCast(cName,eName,eData){
  let children = this.$children
  children.forEach(ele => {
    let name = ele.$options.name
    if(name === cName){
      ele.$emit.apply(ele,[eName].concat([eData]))
    } else{
      broadCast.apply(ele,[cName,eName].concat([eData]))
    }
  });
}
/**
 * 子组件向父组件分发事件
 * @param cName 目标组件名
 * @param eName 事件名
 * @param eData 事件参数
 */
function dispatch(cName,eName,eData){
  let parent = this.$parent || this.$root
  let name = parent.$options.name
  while(parent && (!name || name!== cName)){
    parent = parent.$parent
    if(parent){
      name = parent.$options.name
    }
  }
  if(parent){
    parent.$emit.apply(ele,[eName].concat([eData]))
  }
}
export default{
  methods:{
    broadCast(cName,eName,eData){
      broadCast.call(this,cName,eName,eData)
    },
    dispatch(cName,eName,eData){
      dispatch.call(this,cName,eName,eData)
    }
  }
}
```

```vue
// c-a.vue  
<tempalte>
  <button @click='handleClick'>确认</button>
</tempalte>
<script>
 import Emmiter from '../mixins/emmiter.js'
 export default{
   mixins: [Emmiter]
   name: 'c-a',
   methods:{
     handleClick(){
       this.broadCast('c-b','test','当前组件的数据传给子组件')
     }
   }
 }
</script>
```

```vue
// c-b.vue  
<template>
  <i-btn @test="out"/>
</template>
<script>
 import 'iBtn' from '../c-a'
 export default{
   components:{iBtn},
   name:'c-b',
  //  created(){
  //    this.$on('test',this.out)
  //  },
   methods:{
     out(data){
       console.log(data)
     }
   }
 }
</script>
```

