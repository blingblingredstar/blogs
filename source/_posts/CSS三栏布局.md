---
title: CSS三栏布局
date: 2019-04-03 22:46:43
categories: 学习笔记
tags: [CSS,Flex]
---

>两边宽度固定，中间区域自适应宽度布局。
<!--more-->
## float布局

1. 利用左浮动与有浮动得到左右两栏；
2. 此时左右两栏应为浮动已经脱离文档流，在中间栏添加左右外边距即可；

```html
<style>
  .left,.right{
    width: 200px;
    min-height: 540px;
    background-color: #333;
  }
  .main{
    min-height: 540px;
    background-color: #666;
    margin-left: 200px;
    margin-right: 200px;
  }
  .left{
    float: left;
  }
  .right{
    float: right;
  }
</style>
```

+ 优点：简单
+ 缺点：中间部分最后加载
  
## BFC 规则

BFC(块格式化上下文)规则规定：BFC不会和浮动元素重叠。所以如果将main元素设定为BFC元素即可

```html
<style>
  .left,.right{
    width: 200px;
    min-height: 540px;
    background-color: #333;
  }
  .main{
    min-height: 540px;
    background-color: #666;
    overflow: hidden;
  }
  .left{
    float: left;
  }
  .right{
    float: right;
  }
</style>
```

+ 优缺点同上

## 圣杯布局

1. 左中右三栏左浮动，相对定位。
2. 给最外层容器设置左右外边距，大小为左右栏宽度。
3. 中间区域宽度100%。
4. 左边栏先给一个-100%外边距，使其位于最左侧，再给一个负左边栏宽度的left值。
5. 右边栏先给一个负右边栏宽度的外边距，使其位于最右侧，再给一个负右边栏宽度的right值。

```html
<style type="text/css">
  .content{
      margin: 0 220px 0 200px;
  }
   .main,.left,.right{
       min-height: 540px;
       float: left;
       position: relative;
   }
   .main{
       width: 100%;
       background: #333333;
   }
   .left{
       width: 200px;
       margin-left: -100%;
       left: -200px;
       background: #0099cc;
   }
   .right{
       width: 220px;
       margin-left: -220px;
       right: -220px;
       background: #404060;
   }
</style>

<div class="content">
    <div class="main">main</div>
    <div class="left">left</div>
    <div class="right">right</div>
</div>
```

## 双飞翼布局

核心同圣杯布局，不过为了不遮挡中间栏，不使用给左右边栏加相对定位加left值或right值，
而是给中间栏内添加一个内容区，给这个内容区设置左右外边距即可。

```html
<style type="text/css">
  .main,
  .left,
  .right {
    min-height: 540px;
    float: left;
  }
  .main {
    width: 100%;
    background: #333333;
  }
  .main-content {
    margin: 0 220px 0 200px;
  }
  .left {
    width: 200px;
    margin-left: -100%;
    background: #0099cc;
  }
  .right {
    width: 220px;
    margin-left: -220px;
    background: #404060;
  }
</style>

<div class="container">
  <div class="main">
    <div class="main-content">main-content</div>
  </div>
  <div class="left">left</div>
  <div class="right">right</div>
</div>
```

## flex布局

1. 给最外层容器设置display: flex，方向row。
2. 为确保中间容器最先加载，main位于上方，并给其flex属性设置为auto(只设置flex-grow: 1也可)，确保宽度自适应。
3. 给左边栏设置order值为-1，使其位于最左方。flex值为0(flex-grow,定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。),1(flex-shrink,定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。),边栏宽度(flex-basis,定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。)。
4. 给右边栏设置flex值(0,1,边栏宽度)即可

```html
<style>
  .content {
    display: flex;
    flex-flow: row nowrap;
    color: #fff;
  }
  .main {
    min-height: 540px;
    background: #666;
    flex: auto;
  }
  .left {
    min-height: 540px;
    background: #333;
    order: -1;
    flex: 0 1 200px;
  }
  .right {
    min-height: 540px;
    background: #333;
    flex: 0 1 220px;
  }
</style>
<div class="content">
  <div class="main">main</div>
  <div class="left">left</div>
  <div class="right">right</div>
</div>
```

+ 优点：布局简单
+ 缺点：兼容性问题
