---
title: '[HTML全解]HTML重难点'
date: 2019-08-27 13:38:39
categories: [学习笔记]
tags: [饥人谷, HTML]
---
## 英语小课堂

| 英文      | 翻译 | 英文   | 翻译 |
| --------- | ---- | ------ | ---- |
| hyper     | 超级 | blank  | 空白 |
| target    | 目标 | parent | 父母 |
| reference | 引用 | self   | 自己 |
| frame     | 框架 | load   | 加载 |
| error     | 错误 | canvas | 画布 |

## HTML预览方式

+ http-server
  + 全局安装http-server
    + `yarn global add http-server`
  + 启动http-server
    + `http-server . -c-1`或`hs . -c-1`
+ parcel
  + 全局安装parcel
    + `yarn global add parcel`
  + 启动parcel
    + `parcel <filenanme>`
<!-- more -->
## a标签

+ a标签属性
  + href(hyper reference)
  + target
    + 如何打开链接，新tab页/同一tab页
  + download
    + HTML5新属性，可用来下载url
  + rel=noopener
+ a标签作用
  + 跳转外部页面
  + 跳转到内部锚点
  + 跳转到邮箱或电话等

### a标签href属性的值
<!-- markdownlint-disable md034 -->
+ 网址
  + https://google.com
  + http://google.com
  + //google.com
    + **推荐使用，可自动跳转**
+ 路径
  + /a/b/c及a/b/c
    + http协议中绝对路径指向网站根目录
  + index.html及./index.html
+ 伪协议
  + javascript:代码;
  + mailto:邮箱
  + tel:电话
+ id
  + 指定id值，跳转到页面中id值相同的元素

### a标签target属性的值

+ 内置名字
  + _blank
    + 新tab页打开
  + _top
    + 结合iframe使用，修改iframe页面最外层页面的地址
  + _parent
    + 结合iframe使用，修改iframe页面上一层页面的地址
  + _self
    + 当前tab页或者iframe页面打开
+ 程序员命名
  + window的name
    + 如果没有就创建一个窗口打开，如果有就使用这个窗口打开
  + iframe的name
    + 使用具有同样name属性值得iframe打开

## iframe标签

+ 内嵌窗口
  + 很少使用

## table标签

### table语法

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

+ 相关样式
  + table-layout
  + border-collapse
    + 最好重置为collapse
  + border-spacing
    + 最好重置为0

## img标签

+ 作用
  + 发送一个get请求，展示一张图片
+ 属性
  + alt
    + 提高页面可读性
  + height
    + 只指定高度，宽度自适应
  + width
    + 只指定宽度，高度自适应
  + src
+ 事件
  + onload
  + onerror
+ 响应式
  + max-width: 100%
+ 可替换元素
  + [MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Replaced_element)

## form标签

+ 作用
  + 发送get/post请求，然后刷新页面
+ 属性
  + action
    + 请求页面地址
  + autocomplete
    + 自动填充，需要标签添加name属性及值
  + method
    + 指定请求方式
  + target
    + 与a标签target属性类似
+ 事件
  + onsubmit

> form标签内必须添加submit按钮(input或button)

## input标签

+ 作用
  + 让用户输入内容
+ 属性
  + 类型type:
    + button
    + checkbox
    + email
    + file
    + hidden
    + number
    + password
    + radio
    + search
    + submit
    + tel
    + text
  + 其他
    + name
    + autofocus
    + checked
    + disabled
    + maxlength
    + pattern
    + value
    + placeholder
+ 事件
  + onchange
  + onfocus
  + onblur
+ 验证器
  + HTML5新功能

> `<input type="submit" />`与`<button type="submit"></button>`区别：input为自闭和标签，中间无法添加其他标签，button可以

## 其他输入标签

+ 标签
  + select + option
  + textarea
    + 可以通过css样式resize值为none来禁用手动缩放
  + label
+ 注意事项
  + 一般不监听input的click事件
  + form内的input标签应添加name属性
  + from标签内需要一个input标签或button标签且type属性为submit才会触发submit事件

## 其他标签

+ 标签
  + video
  + audio
  + canvas
  + svg

+ 注意事项
  + 注意查看文档，确认兼容性
