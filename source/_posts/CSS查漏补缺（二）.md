---
title: CSS查漏补缺（二）
tags:
 - css
---

### CSS效果

1. 效果属性

   * box-shadow

     ```css
     box-shadow: 6px 6px 10px 10px rgba(1,2,3,4) 偏移量x,y+模糊区域z+扩展l+颜色c 外阴影
     box-shadow: inset x y z l color; 内阴影
     ```

     阴影不会占位置，也就是不影响其他元素的布局。box-shadow后面可以跟多组值，从而有多个阴影。

     * 营造立体感，层次感
     * 充当没有宽度的边框
     * 做一些特殊效果，如机器猫等

   * test-shadow

     text-shadow的值可以有多组，类似box-shadow

     ```css
     .text{
         text-shadow:1px 1px 1px #ddd;  // 右下角(1,1) 模糊（1） 颜色（#ddd)
     }
     ```

     * 立体感
     * 印刷的感觉

   * border-radius

     圆角=》圆角矩形，原先，扇形，其他的图案

     ```css
     .test{
         border-radius:20%;  // 值可以是百分数，具体的像素
     }
     。test2{
         border-top-left-radius:100px;
     }
     .test3{
         border-radius: 10px 10px 10px 10px / 20px 20px 20px 20px; // 水平和垂直方向
     }
     ```

   * background

     * 纹理 图案 渐变

     * 雪碧图动画

     * 背景图尺寸适应

       ```css
       background-position : center top;
       background-size: 200px 100px; // 或者百分比 或者cover 或者contain
       background-repeat: repeat-x;
       ```

   * clip-path 

     * 对容器显示进行裁剪，不影响占位

     * 常见的几何图形

     * 自定义路径进行裁剪

     * 兼容性不好

       ```css
       .div{
           clip-path: inset(100px 50px) 方形 居中保留
           // （100,100）的r =50 圆
           cliP-path: circle(50px at 100px 100px)
           //多边形几个重要的节点连接
           clip-path: polygon(50% 0,100% 50%,50% 100%, 0% 50%) 
           // 使用svg
           clip-path:url(#clipath)
       }
       ```

2. 3D/2D 变换 ,不是动画

   * translate 位移
   * scale 缩放
   * skew 斜切 斜着拉扯
   * rotate 旋转

3. 问题

   * 一个div画出叮当猫： box-shadow无限投影+::before+::after

   * 不占空间的边框

     * box-shadow
     * outline 定制性不高

   * 实现圆形 圆角矩形等 border-raius

   * 实现ios图标的圆角 clip-path:(对应svg)

   * 实现3d效果

     ```css
     perspective: 500px; 透视角度
     transform-style: preserve-3d; 保留3d效果
     transform: translate rotate ......
     ```

### CSS动画

* 动画给人以愉悦舒适的感觉，可以用来引起注意，对我们的操作进行反馈

* questions

  * css动画性能
    * 性能不坏
    * 部分情况下优于js
    * js可以做到更好
    * 部分属性兼容性差，如box-shadow

  * 过渡动画和关键帧动画区别
    * 过渡动画有状态变化
    * 关键帧动画不需要状态变化
    * 关键帧动画控制更精细

* 动画类型： transition补间动画  keyframe关键帧动画  逐帧动画（帧数从少到多）

  * 补间动画

    * 位置-平移 left/right/margin/transform

    * 方位-旋转 transform

    * 大小-缩放  transform

    * 透明度 opacity

    * 其他-线性变换(如宽度 高度等) transform

      ```css
      transition : width 21s，height background 2s; //全部 all 10s
      transition-timing-function: linear // 动画进度和时间的关系,值有很多，也可以用贝塞尔曲线
      ```

  * 关键帧动画

    * 相当于多个补间动画，与元素状态的变化无关，定义更加灵活

      ```css
      .test{
          animation:run 1s; 或者 run 1s linear;
          animation-direction: reverse; // 反向
          animation-iteration-count: infinite; // 次数，无限
          animation-play-state: paused; //暂停
          animation-fill-mode: forwards; //来回变
      }
      @keyframes run{
          0%{
              width :100px;
          }
          100%{
              width: 1000px;
          }
      }
      ```

  * 逐帧动画

    没有自动补充帧的动画，关键帧动画的特殊用法之一（无法补帧,如多张图片在短时间内连续变换）

    * 适用于无法补间计算的动画，资源较大，使用steps()，动画面积小，时间短

      ```css
      .test{
          animation: run 1s linear;
          animation-iteration-count:infinite;
          animation-timing-function:steps(1); 关键帧之间有几个画面
      }
      @keyframes run{}
      ```

### 预处理器

基于css的另一种语言。可以借助工具编译成css,添加了很多css不具备的特性。它可以提升css文件的组织，如实现模块化等。主要包括两种，less , scss或者sass.

less: 基于nodejs，编译快

scss: 基于ruby,编译慢

* 介绍
  * 嵌套 反应层级和约束
  * 变量 计算 减少重复代码
  * Extend Mixin 代码片段
  * 循环 适用于复杂有规律的样式
  * import CSS文件模块化

- 嵌套 

  * less

    ```less
    .wrapper{
        background: red;
        .nav{
            font-size:12px;
        }
        .content{
            font-size:21px;
            &:hover{
                // 平级
                color: gray;
            }
        }
    }
    ```

  * scss

    ```scss
    .wraper{
        background: white;
        .nav{
            font-size:12px;
        }
        .contend{
            font-size:21px;
            &hover{
                backgroud: red;
            }
        }
    }
    ```

- 变量 计算 减少重复代码

  * less

    ```less
    @fontSize: 12px;
    @bgColor: gray;
    .nav{
        font-size: @fontSize + 10px;
        background: lighten(@bgColor, 40%)
    }
    ```

  * scss

    ```scss
    $fontSize: 12px;
    $bgColor: gray;
    .nav{
        font-size: $fontSize + 10px;
        background: lighten($bgColor,40%)
    }
    ```

- mixin 对于共同代码，编译的css中是直接复制，重复率高，体积相对偏大

  * scss

    ```scss
    $fontSize: 12px;
    $bgColor: gray;
    @mixin block($fontSize){
        font-size: $fontSize;
        color: red;
    }
    .nav{
        @inclue block($fontSize+10px)
    }
    ```


  * less

    ```less
    @fontSize: 12px;
    @bgColor: gray;
    .block(@fontSize,@bgColor){
        // 需要复用的代码
        font-size: @fontSize + 10px;
        border: 10px solid red;
        background: lighten(@bgColor, 40%)
    }
    .box{
        color: blue;
    }
    .nav{
        .block(@fontSize+2px,@bgColor);
        .color();
    }
    ```

- extend 编译的结果更小

  * less

    ```less
    @fontSize: 12px;
    .block{
        font-size:@fontSize;
        color: red;
    }
    .wraper{
        .nav:extend(.block){
            &:hover{
                color:yellow
            }
        }
    }
    ```

  * scss

    ```scss
    $fontSize: 12px;
    .block{
        font-size:$fontSize;
        color: red;
    }
    .box{
        .nav{
            @extend .block;
            &:hover{ color: red; }
        }
    }
    ```

- loop

  * less 不支持循环，所以使用递归的方式实现

    ```less
    .gen-col(@n) when (@n>0) {
        .gen-col(@n - 1);
        .col-@{n}{
            width:1000px/12*@n;
        }
    }
    .gen-col(12);
    ```

  * scss 支持循环

    ```scss
    @mixin gen-col($n){
        @if $n>0{
            @include gen-col($n-1);
            .col-#{$n}{
                width: 1000px/12*$n;
            }
        }
    }
    @include gen-col(12)
        
    // 简单写法
        @for $i from 1 through 12{
            .col-#{$i}{
                width: 1000px/12*$i;
            }
    }
    ```

- import

  * less

    ```less
    @import "./1-import-variabel"; //1
    @import "./2-import-variable"; //2 
    ```

    ```less
    // 1 定义变量的文件
    @fontSize: 20px;
    ```

    ```less
    // 2 模块文件
    .mdel{
        .box{
            font-size:@fontSize + 20px;
        }
    }
    ```

  * scss 类似less

    ```scss
    @import "./1-variabel" 
    @import "./2-mdelss"
    ```

- CSS预处理框架

  * SASS-Compass 处理兼容性等

  * Less-Lesshat/EST

    ```less
    @import "est/all" // 按需编译，用到的才会引入编译
        .left{
            float:left;
            .clearfix();
    }
    ```

  * 提供现成的mixin,类似JS类库 封装常用功能

### 其他



