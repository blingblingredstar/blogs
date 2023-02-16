---
title: '[HTML全解]HTML标签'
date: 2019-08-26 23:02:30
categories: [学习笔记]
tags: [饥人谷, HTML, CSS]
---
## 英语小课堂

| 英文      | 翻译   | 英文        | 翻译 |
| --------- | ------ | ----------- | ---- |
| heading   | 标题   | order       | 顺序 |
| body      | 正文   | ordered     | 有序 |
| paragraph | 段落   | unordered   | 无序 |
| section   | 章节   | description | 描述 |
| article   | 文章   | term        | 术语 |
| mian      | 主要的 | data        | 数据 |
| aside     | 旁边的 | quote       | 引用 |
| anchor    | 锚点   | block       | 块   |
| strong    | 强壮   | inline      | 行内 |
| emphasis  | 强调   | break       | 打断 |
<!-- more -->
## 学习工具

+ 推荐书籍《网道HTML教程》
  + 看书诀窍：三上（马山，枕上，厕上）- 顾名思义，源自欧阳修《归田录》
+ 提问技巧
  + 代码问题三要素
     1. 代码链接
     2. 期望效果
     3. 实际效果
  + 非代码问题三要素
     1. 原始需求
     2. 自己的思路
     3. 遇到的问题
+ 推荐网站
  + jsbin
  + codesandbox

## HTML起手式

```html
<!-- 文档类型 -->
<!DOCTYPE html>
<!-- 根标签，语言为中文 -->
<html lang="zh-CN">
  <!-- 头部信息 -->
  <head>
    <!-- 字符编码类型 -->
    <meta charset="UTF-8" />
    <!-- 视窗选项，根据设备缩放，初始倍率为1.0 -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <!-- 通知ie浏览器升级引擎 -->
    <meta http-equiv="X-UA-Compatible" content="ie=edge" />
    <!-- 标题 -->
    <title>Document</title>
  </head>
  <!-- 网页主体 -->
  <body></body>
</html>
```

## HTML标签

### 章节标签

+ 表示文章/内容层级
  + 标题 h1 - h6
  + 章节 section
  + 文章 article
  + 段落 p
  + 头部 header
  + 脚部 footer
  + 主体 main
  + 旁支 aside
  + 划分 div

### 全局属性

+ 所有标签都有的属性
  + class
  + contenteditable
    + 标签内容是否可编辑，可结合下方hidden属性将style标签显示在页面上进行样式调试
  + hidden
  + id
    + 不推荐使用
  + style
  + tabindex
    + 使用tab键切换时的顺序（0是最后一个，负数为禁用tab键切换）
  + title
    + 鼠标悬浮时显示属性值的全部内容

### 默认样式

+ 作为一个标记语言，HTML诞生之初就是为了方便排版，所以会有一些默认样式。为了定制我们自己页面的样式，需要CSS reset。

代码示例：

```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
*::before,
*::after {
  box-sizing: border-box;
}
a {
  color: inherit;
  text-decoration: none;
}
input,
textarea,
button {
  font-family: inherit;
}
ol,
ul {
  list-style: none;
}
table {
  border-collapse: collapse;
  border-spacing: 0;
}
```

这里讲了一下通配符选择器的问题，之前根据[这篇文章](https://www.paulirish.com/2012/box-sizing-border-box-ftw/)，也表明了直接使用通配符重置默认样式不会造成性能瓶颈。这里也在复习下在性能优化中学到的两个原则

1. 优化之前先测量
2. 在没有性能瓶颈前不需要优化

### 内容标签

| 标签         | 作用                                             |
| ------------ | ------------------------------------------------ |
| ol + li      | 有序列表(ordered list + list item)               |
| ul + li      | 无序列表(unordered list + list item)             |
| dl + dt + dd | 描述列表 (description list)                      |
| pre          | 可以展示HTML中的空格换行和tab(preformatted text) |
| hr           | 分割线(horizontal)                               |
| br           | 换行(break)                                      |
| a            | 锚点(anchor)                                     |
| em           | 语气上的强调，默认样式为倾斜(emphasis)           |
| strong       | 内容上的强调，默认样式加粗                       |
| code         | 代码块 默认为等宽字体                            |
| quote        | 引用                                             |
| blockquote   | 块级引用                                         |
