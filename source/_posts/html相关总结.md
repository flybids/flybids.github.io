---
title: html相关总结
tags: 
 - html
---

## 常见元素

* 适配移动端

  第一步在head的meta上添加viewwidth=mediawidth

* form表单

  form表单元素提交方式有get和post两种形式，其中对于post，又有encode和formData两种数据格式，后者一般在需要上传文件时使用。

  在使用表单元素的时候，可以不用form进行包裹，但是建议使用，原因包括：

  * form表单中可以使用按钮的reset,submit等功能
  * jQuery等库中有使用到form表单
  * 在一些框架中，使用form可以更好的进行数据校验
  * 有form的时候，用户可以通过浏览器记住密码等。
  * 其他

* 如何理解HTML

  * 是一个文档
  * 
  * 结构
  * 语义化

## 版本

* html5新增元素 语义化
  * 块： section<article nav aside
  * 语义化： header footer
  * form:  新增校验 autofocus
  * em strong
  * i icon 图标 
* html5的特性
  * 回归html4, 更宽松，更语义化
  * 更多的特性反映在动态特性中，需要结合js才能体现，如本地存储，websocket等等

## 元素分类

* 根据样式分类
  * block
  * inline
  * inline-block
* 根据内容分类，彼此互相交叉
  * Flow 可见的
  * Heading 标题
  * section 分区
  * Interactive 可交互动
  * Phrasing 短语
  * Metadata 资源

## 元素嵌套关系

* 块元素包含行内元素
* 块元素不一定可以包含块元素
* 行内元素一般不可以块元素，但是a例外

## 元素默认样式和定制化

* 在不同的浏览器中，元素的默认样式可能有所不同，这就需要我们定制化样式。

### 需要注意的问题

* 浏览器中读取的是dom，dom是根据html解析获取

