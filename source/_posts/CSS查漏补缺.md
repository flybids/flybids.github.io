---
title: CSS查漏补缺（一）
tags:
 - css
---

1. CSS样式叠加问题

2. 选择器的分类和权重计算

   * 权重计算
     * ID选择器 +100
     * 类 属性 伪类 +10
     * 元素 伪元素 +1
     * 其他 +0
     * 需要注意的是，<strong>计算的时候不进位，权重低的不能通过数量来战胜权重高的</strong>
     * ！important最高
     * 元素属性的优先级高
     * 相同权重 后面写的会覆盖前面写的

   * 分类：
     * 类选择器.name{}，
     * 元素选择器a{}，
     * 伪元素选择器::before{}, 真实存在的元素,<strong>老旧的IE用单冒号</strong>
     * 属性选择器[type=radio]{}
     * 伪类选择器 :hover{} 某种状态下
     * ID选择器
     * 组合选择器[type=checkbox]+label{}
     * 否定选择器 :not(.link){}
     * 通用选择器 *{}

3. 浏览器对css的解析方式是从右到左，反过来的；这是出于对性能的考虑，更快的找到元素。

4. 非布局样式

   * 字相关

     * 多字体 fallback,一个字体找不到，用下一个字体,<b>针对的是每一个字符</b>

     * 使用网络字体， 自定义字体 (用于字体图标，宣传品牌banner等固定文案)

       ```css
       @font-face{
           font-family:"IF",
           src: url("地址") // 可以本地，也可以远程
       }
       .test{
           font-family:IF;
       }
       // 使用远程字体时，需要对应远程字体运行跨域
       // 远程也可以先用link引入，然后直接使用
       ```

     * iconfont

       可以参考阿里矢量库

     * 字体族：一堆的字体；<strong>不能加引号</strong>

       * 衬线字体 宋体等 serif

       * 非衬线字体 笔画起收规则的，如黑体 sans-serif

       * 等宽字体  每个字符宽度相同 monospace

       * 非等宽字体

       * 手写体 cursive

       * 花体 fantasy

         ```css
         .name{
             font-family:"PingFang SC",serif;
         }
         // serif 是字体族 PingFang SC是字体名称
         ```

   * 背景和边框

     * 边框

       * 属性：线型 大小(solid dashed dotted)  颜色

         ```css
         border: 1px solid blue;
         ```

       * 边框背景图

         ```css
         border: 30px solid transprant;
         border-image:url('../test.png') 30 round  // 不常用
         ```

       * 边框衔接 三角形 斜切

         ```css
         border-bottom:30px solid red;
         border-right:30px solid blue; 
         ```

     * 背景

       * 背景颜色

         ```css
         test1{
             background: red; // 少用
         }
         test2{
             background:rgb(1,2,3) // 不适合人眼，rgba透明度
         }
         test3{
             background:hsl(0,100%,50%) // 颜色 饱和度 亮度，适合人眼 hsla透明度
         }
         test4{
             background:url('../img/1.png')
         }
         ```

       * 渐变色背景css3,渐变可以当做图片背景，可以使用background-size属性等

         ```css
         .c1{
             background:linear-gradient(to right,red,green) // 左到右
         }
         .c2{
             background:linear-gradient(0deg,red,green) // 下到上， 45左下角到右下角
         }
         .c3{
             // 左到右,可以多个颜色
             background:linear-gradient(to right,red 0,green 10%,yellow 100%) 
         }
         .c4{
             // 叠加
             background:linear-gradient(135deg,transparent 0,transparent 45%, red 45%, transparent 100%)，linear-gradient(135deg,transparent 0,transparent 45%, red 45%, transparent 100%)
         }
         ```

       * 多背景叠加css3

       * 背景图片和属性 雪碧图 性能优化,减少http请求数，提高加载性能

         ```css
         {
             background:red url('../q.png') // 图片平铺，改在颜色上面
             // background-repeat(no-repeat,repeat-x等值）
             // background-position(center,top,20px等) , 坐标位置（相对于容器，取反数，一般负数）
             // background-size 缩小放大等等，缩小可以用来适配移动端好分辨率，比如分辨率三倍，就图片像素增加三倍，size缩小三倍
         }
         ```

       * base64 和性能优化

         减少了http的请求

         将图片转换为base64文本，使用javascript或工具进行转换；增大了解码的开销，体积增大三分之一，所以一般用于小图标，小图片。

         项目中使用base64的时候，在打包的时候把图片转成base64

       * 多分辨率适配

   * 滚动 换行

     * 文字折行

       * overflow-wrap 兼容性不好，一般写word-wrap，通用换行（是否保留单词）

       * word-break 针对多字节文字（中文橘子也是单词）

       * white-space 空白处是否换行

         ```css
         {
             word-break: normal; 
             // break-word 对单词可以换行 break-all 所有单词都可以换行 keep-all 所有单词都保留完整
             overflow-wrap:normal;
             white-space:normal;
         }
         ```

     * 滚动： 内容比容器多

       滚动行为 滚动条 (visible超出可见  hidden超出隐藏  scroll有滚动条 auto看情况滚动条)

       ```css
       overflow:hidden; // 默认auto
       ```

   * 粗体 斜体 下划线

     这都是些装饰性属性。

     font-weight:lighter  normal=400  bold=700  bolder  100 (100到900，取x00,有兼容性)，一般normal或者bold, lighter bolder不同浏览器设备显示有差别

     font-style: itatic 斜体

     text-decoration

     cursor

   * 其他

     * 行高 line-height
       * 行高的构成: 一行的最大行高可以撑起外层盒子的高度（line-box包含inline-box)，可以基于这个实现垂直居中。
       * 行高相关的现象和方案： 行内元素默认baseline对齐；在设置对齐方式时，每个行内元素都要设置vertical-align,vertical-align也可以使用数字（此时是相对于baseline的位置）；

5. 布局 layout

   * 简介

     * 早期以table为主，但是用户等待时间更长，语义不明确；后期以技巧性地布局为主（本来不是用来布局的东西）

     - 现在有flexbox， grid, 响应式布局

     - 常见布局方法： table, float+margin, inline-block,flexbox

   * 盒模型 content padding border margin (height,width指content)

     * display(block,inline,inline-block)  position(static, absolute脱离文档流,fixed脱离文档流,relative,<i>sticky</i><span style="color:'red'">新的属性</span>,还有inherit) 
     * **z-index只对定位元素有效**，什么是定位元素呢？说简单点就是设置了position属性的元素，position的属性值如下：absolute-绝对定位、relative-相对定位、fixed-固定定位、inherit-继承父元素定位，static-静态定位。这里要注意，并不是所有的定位设置都有效果，absolute、relative和fixed是肯定有效果的，inherit取决于父元素，如果父元素没有设置定位则z-index无效，注意低版本IE浏览器不支持这个值。最后说下static这个静态定位，其实这是默认值，表示当前元素不进行定位，所以如果元素设置了这个属性值，其实是和没有设置是一样的，会使元素忽略掉z-index属性，使其不起作用。

     * 盒模型

       content padding border margin (height,width指content)

   * flexbox

     * 弹性盒子

     * 盒子本来就是并列的，指定宽度就可

       ```css
       .item0{
           flex:1; 占据box的一份 
       }
       .itm1{
           flex: none;
           width:70px; 占70px宽带
       }
       .box{
           display:flex;
       }
       ```


     * flexbox还有其他的属性,由于兼容性的问题，还没有被大规模使用，移动端兼容性更好

   * float布局

     * 元素浮动，脱离文档流（不对其他元素布局起作用），但是不脱离文本流（会排挤其他元素的文本）

     * 形成块（BFC),宽和高自己定，位置尽量（上，左（右））

     * 相当于从父级消失，可能导致父级的高度塌陷

     * 可以使用伪元素清楚浮动

       ```css
       cls::after{
           display:block;
           content:'';
           visibity: hidden;
           height:0;
           clear:both;
       }
       ```

     * 问题：float + margin 三栏布局

   * inline-block 布局

     * 像文本一样排block元素，但是需要处理间隙问题

     * 解决间隙问题：父元素字体大小设为0，里面的字体再设回来；或者再代码里面，两个inline-block之间没有空格，或者加上注释占用空格

       ```less
       test1{
           font-size :0;
           .child1{
               font-size:14px;
               display:inline-block;
           }
           .child2{
               font-size:13px;
               display:inline-block;
           }
       }
       ```

       ```html
       <div style="display:inline-block;">
           123
       </div><div style="display:inline-block">
           456
       </div>
       ```

   * 响应式布局

     * 再不同设备上正常使用，一般针对屏幕大小。主要的方法包括：

       * 隐藏一些东西 + 折行 + 留下自适应空间

       * rem, viewport , media query

         ```html
         <meta name="viewport" content="width=device-width,initial-scale=1.0">
         ...
         <meta name="viewport" content="width=320">
         ....
         <style>
             @media (max-width:640px){
         		.left{
         			display:none;
         		}
         	}
         </style>
         
         ```

   * 12

   * table 布局

     * 直接用表格

     * 仿照表格的样式

       ```css
       display: table-cell;
       display: table-row;
       ```

6. CSS Hack 兼容浏览器，不合法但是生效的写法，难理解，难维护，容易失效，hack属性要写在正常属性后面

   ```css
   .test{
       width:200px;
   	width:100px\9;
   }
   ```

   替代方案：特性检测，针对性加class,更推荐

7. 如何优化checkbox

   * label[for] 和 id
   * 隐藏元素input
   * :checked + label

8. 12
