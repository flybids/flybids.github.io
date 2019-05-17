---
title: let 和 const
tags:
 - js
 - es6
---

### let 命令

#### 基本用法

let 变量的作用域是在其对应的代码块，var 的变量是全局变量，会挂载在顶层变量上。

对于for循环，设置循环变量的作用域是父作用域，循环体内部是单独的子作用域。同名变量在子作用域会覆盖父作用域传来的值,但是在父作用域对应变量值不受影响。具体如下：

```javascript
for(let i =0;i<3;i++){
    let i ='abc'
    console.log(i)
}
//abc
//abc
//abc
```

#### 变量提升

对于用let和const声明的变量不会发生变量提升，而使用var会发生变量提升。所以，var变量在声明前使用不报错，而let和const会报错。

变量提升：js从上到下执行时，分为词法分析和执行两个阶段。在词法分析阶段，包括分析形参，分析变量，分析函数声明三个部分。经过词法分析后，会将存在的变量声明和函数声明，进行一番处理，比如赋值等。具体代码如下：

```javascript
console.log(a)
var a =12
```

词法分析后的代码为：

```javascript
var a = undefined
console.log(a)
a=12
```

运行的结果是：

```
undefined
```

#### 暂时性死去TDZ

由于let/const不会变量提升，在作用域内，变量声明之前的部分，对于该变量都属于死区，也就是暂时性死区。

TDZ导致typeof运算符不再安全，当在变量X的暂时性死区中使用typeof检验X时，此时会抛出ReferenceError。

#### 不允许重复声明

### 块级作用域

使用\{ 和 \} 包裹的作用域

### let & const

const声明只读变量，必须赋值，一旦声明就不能改变它的值。但是如果声明的是个对象时，可以改变对象的属性值。本质上，不可改变的是指针指向的对象，或者说内存地址所保存的数据不得改动。对于字符串，数字等简单变量，内存地址保存的就是它的值，但是对于对象，数组等保存的是指向数据的地指针。

const变量的作用域只在声明的块作用域有效，没有变量提升，有TDZ.

#### 冻结对象

```javascript
const foo = Object.freeze({});

// 常规模式时，下面一行不起作用；
// 严格模式时，该行会报错
foo.prop = 123;
```

```javascript
// 将对象本身冻结，对象的属性也冻结
var constantize = (obj) => {
  Object.freeze(obj);
  Object.keys(obj).forEach( (key, i) => {
    if ( typeof obj[key] === 'object' ) {
      constantize( obj[key] );
    }
  });
};
```

#### 声明变量6种方式

* var
* function
* let
* const
* class
* import

