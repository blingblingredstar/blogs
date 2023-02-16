---
title: '[CSS全解]CSS布局'
date: 2019-08-30 22:06:37
categories: [学习笔记]
tags: [饥人谷, CSS, Float, Flex, Grid]
---
本文章资料主要来自[饥人谷2020版前端体系课](https://mp.weixin.qq.com/s/nEzhoM75UCRMWReDEMnAfQ)，本文为课程笔记。

## 布局

### 布局分类

1. 固定宽度布局
   + 一般宽度为960/1000/1024px
2. 不固定宽度布局
   + 依托文档流原理布局
3. 响应式布局
   + PC上固定宽度，手机上不固定宽度
   + 是一种混合布局
   + 三个特征：媒体查询(Media queries)，自适应图像(Images that resize)与网格布局(Flexible grid-based layout)
<!-- more -->
### 布局思路

+ 从大到小
  + 先规划整体布局
  + 然后完善细节
+ 从小到大
  + 先完成各部分
  + 组合为整体

### 布局所需要用到的属性

+ 如果需要兼容IE9
  + 使用float布局
  + 必要时使用负margin
+ 如果只兼容最新浏览器
  + 使用grid布局
+ 否则使用flex布局
  + 必要时使用负margin

## float布局

+ 步骤
  + 子元素添加`float: left;`与`width`属性
  + **父元素加`.clearfix`清除浮动**
+ 经验
  + 留一些空间，或者最后一个元素不设定宽度
  + 不需要做响应式，因为专为IE浏览器布局
  + IE6/7存在双倍`margin`bug，可用两种方式解决
    + 针对IE6/7添加`_margin`属性将`margin`减半，
    + 添加`display: inline-block;`
+ 实践
  + `display: block;`会让元素尽可能宽，`display: inline-block`会让元素尽可能窄
  + 当`img`元素对齐方式有问题时，添加`vertical-align: top/middle`属性。
  + `border`干扰宽度时，可以使用`outline`属性替换
  + 宽度固定的block元素，水平居中时，分别设置`margin-left`与`margin-right`
  + 设置平均布局时，可以添加一个div包裹，给div设置负margin来增加宽度。
  + IE8时伪类元素只有一个冒号

## flex布局

[A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

+ flex contain属性
  + `display: flex/inline-flex;`
  + `flex-direction` 控制方向
  + `flex-wrap` 是否换行
  + `flex-flow` 以上两个属性合并书写。
  + `justify-content` 主轴对齐方式
  + `align-items` 次轴对齐方式
  + `align-content` 多行时 对齐方式

+ flex items属性
  + `order` 顺序
    + 默认为0
  + `flex-grow` 控制宽度，有多余空间时如何分配
    + 默认为0，既元素本身的宽度
  + `flex-shrink` 控制宽度，空间不足时如何分配
    + 默认为1，一般设置为0，防止空间不足时缩放
  + `flex-basis` 控制基准宽度
    + 推荐使用这个属性替代宽度，`width`设置的是绝对宽度，通过`flex-basis`，可以理解为相对宽度，弹性盒模型会将元素尽量变为此宽度，而不是绝对是这个宽度，这会增加布局的适应能力，但显然也会带来一定的不确定性。
  + `flex` 以上三个属性合并书写
  + `align-self` 指定次元素的次轴方向对齐方式。

+ 实践
  + 通常不写死高度和宽度，除非特殊说明
  + 可以将flex与`margin-xxx: auto;`结合使用
  + 多行平均布局时结合负margin技巧

## grid布局

二维布局方式

[A Complete Guide to Grid](https://css-tricks.com/snippets/css/complete-guide-grid/)
