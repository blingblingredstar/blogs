---
title: 《HTML》入门笔记1
date: 2019-08-27 08:47:22
categories: [学习笔记]
tags: [饥人谷, HTML]
---
## HTML的由来

+ HTML于1990年左右由Tim Berners Lee发明
+ 全称为Hypertext Markup Language(超文本标记语言)
+ 功能是结构化页面信息及一定程度上标识外观及语义
<!-- more -->
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
  + 标题 h1-h6
  + 章节 section
  + 文章 article
  + 段落 p
  + 头部 header
  + 脚部 footer
  + 主体 main
  + 旁支 aside
  + 划分 div

### 全局属性
<!-- markdownlint-disable md033 -->
+ 所有标签都有的属性
  + class
    + 类，用来标识标签所述，以便控制样式及获取元素
  + contenteditable
    + 内容是否可编辑
  + hidden
    + 标签是否可见
  + id
    + 标签唯一标识，因为不确保唯一性，不推荐使用
  + style
    + 内联样式，优先级较高，使用js控制时会覆盖原有样式
  + tabindex
    + 使用<kbd>tab键</kbd>时切换的顺序
  + title
    + 鼠标悬浮时显示的内容

### 内容标签

| 标签         | 作用                                         |
| ------------ | -------------------------------------------- |
| ol + li      | 有序列表(ordered list)                       |
| ul + li      | 无序列表(unordered list)                     |
| dl + dt + dd | 描述列表(description list)                   |
| pre          | 展示HTML中的空格换行和tab(preformatted text) |
| hr           | 分割线(horizontal line)                      |
| br           | 换行(break)                                  |
| a            | 链接/锚点(anchor)                            |
| em           | 预期上的强调，默认样式为倾斜(emphasis)       |
| strong       | 内容上的强调，默认样式为加粗                 |
| code         | 代码块，默认字体为等宽                       |
| quote        | 引用                                         |
| blockquote   | 块级引用                                     |
