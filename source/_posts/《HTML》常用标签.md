---
title: 《HTML》常用标签
date: 2019-08-27 13:43:14
categories: [学习笔记]
tags: [饥人谷, HTML]
---

## a标签

a标签一般用作制作链接，内容一般为文字，图片或者按钮，可以用来跳转到其他页面或者页面内部锚点，也可用来发邮件、打电话等

实现相应的功能，需要添加相应的属性及属性值。

### a标签的属性

a标签有以下几个常用属性

+ href(hyper reference)
  + 用来指定链接位置
+ target
  + 指定打开链接的方式
+ download
  + HTML5新增属性，用来下载指定url
+ rel=noopener
<!-- more -->
#### a标签href属性

+ 指定网址
  + `href="https://google.com"`
  + `href="http://google.com"`
  + `href="//google.com"`
    + **推荐使用，可自动跳转**
+ 指定路径
  + `href="/a/b/c"`或`href="a/b/c"`
    + http协议中，绝对路径指向网站根目录，相对路径可省略`./`
  + `href="index.html"`或`href="./index.html"`
+ 添加伪协议
  + `href="javascript:<代码>;"`
  + `href="mailto:<邮箱>"`
  + `href="tel:<电话>"`
+ 指定id
  + `href="#<id值>"`
    + 跳转到页面指定id元素的位置

#### a标签target属性

+ 内置名字
  + `target="_blank"`
    + 在新的tab页打开链接
  + `target="_top"`
    + 结合`iframe`标签使用，修改`iframe`标签最外层也就是tab页的地址
  + `target="_parent"`
    + 结合`ifarme`标签使用，修改`iframe`标签上一层`iframe`标签`source`属性的值
  + `target="_self"`
    + 默认值，当前tab页或者`iframe`标签打开

+ 程序员命名
  + `window`的`name`
    + 如果没有就创建新的tab页打开，如果已创建就使用该tab页打开
  + `iframe`标签的`name`
    + 使用指定`name`属性值的`iframe`标签打开

## img标签

img标签用来发送一个get请求，展示一张图片

### img标签的属性

+ alt
  + 用作图片无法显示时的占位文字，对无障碍阅读有帮助，推荐添加
+ height
  + 只指定高度时，宽度自适应
+ width
  + 只指定宽度时，高度自适应
+ src
  + 图片链接

### img标签的事件

+ onload
+ onerror

### 响应式优化

+ 可添加css样式`max-width: 100%;`来进行响应式布局

### 可替换元素

参考[MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Replaced_element)

## table标签

完整结构

```html
<table>
  <thead>
    <!-- table row -->
    <tr>
      <!-- table header -->
      <th></th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <!-- table data -->
      <td></td>
      <td></td>
    </tr>
  </tbody>

  <tfoot>
    <tr>
      <td></td>
      <td></td>
    </tr>
  </tfoot>
</table>
```

### 相关标签

+ 相关标签
  + table
  + thead
  + tbody
  + tfoot
  + th
  + tr
  + td

### 相关样式

+ table-layout
+ border-collapse
+ border-spacing

> 推荐将table标签的默认样式重置为`border-collapse: collapse; border-spacing: 0;`

## 其他

参见

[[HTML全解]HTML重难点](https://blingblingredstar.github.io/2019/08/27/HTML%E5%85%A8%E8%A7%A3-HTML%E9%87%8D%E9%9A%BE%E7%82%B9/)

[[HTML全解]HTML标签](https://blingblingredstar.github.io/2019/08/26/HTML%E5%85%A8%E8%A7%A3-HTML%E6%A0%87%E7%AD%BE/)
