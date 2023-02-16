---
title: '[CSS全解]CSS动画'
date: 2019-09-07 08:12:13
categories: [学习笔记]
tags: [饥人谷, CSS, 动画, transition, animation, transform]
---
> 本文章资料主要来自[饥人谷2020版前端体系课](https://mp.weixin.qq.com/s/nEzhoM75UCRMWReDEMnAfQ)，本文为课程笔记

## 什么是动画

+ 定义
  + 由许多静止画面组成(帧)
  + 以一定速度播放
  + 人眼因视觉残像产生错觉
  + 误以为是活动画面

+ 概念
  + 帧： 每个静止的画面为一帧
  + 播放速度：每秒24帧（影视）或每秒30帧（游戏）

+ JS实现方法(使用setInterval与left)
  + 使用setInterval周期性改变元素left或top值，会使浏览器进行repaint
  + 性能及其他问题，可以看[这个视频](https://www.youtube.com/watch?v=cCOL7MC4Pl0)

+ 使用transform实现
  + 使用transform修改transformX或transformY的值来实现
  + 直接修改会被合并到当前的requestAnimationFrame，需要设置延时器延时修改
  + 添加transition属性来自动添加中间帧，没有repaint
<!-- more -->
## 浏览器渲染原理

+ 谷歌开发者文档的说明
  + [渲染树构建、布局及绘制](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-tree-construction)
  + [渲染性能](https://developers.google.com/web/fundamentals/performance/rendering/)
  + [坚持仅合成器的属性和管理层计数](https://developers.google.com/web/fundamentals/performance/rendering/stick-to-compositor-only-properties-and-manage-layer-count)
+ 相关视频课程
  + [Udacity 浏览器渲染优化](https://classroom.udacity.com/courses/ud860)

+ 渲染步骤
  + 根据HTML构建DOM树
  + 根据CSS构建CSS树(CSSOM)
  + 将以上两棵树合并为渲染树(render tree)
  + Layout布局(文档流、盒模型、计算大小及位置)
  + Paint绘制(绘制边框颜色、文字颜色、阴影等)
  + Composite合成(根据层叠关系展示画面)

+ 更新样式的方法
  + 一般使用js更新样式
    + `div.style.background = 'red'`
    + `div.style.display = 'none'`
    + `div.classList.add('red)` 常用方法
    + `div.remove`
  + 以上这些方法会费别造成浏览器的reflow，repaint和composite。

+ 三种更新方式
  1. JS/CSS => 样式 => 布局 => 绘制 => 合成
     + JavaScript => Style => Layout => Paint => Composite
  2. JS/CSS => 样式 => 绘制 => 合成
     + JavaScript => Style => Paint => Composite
  3. JS/CSS => 样式 => 合成
     + JavaScript => Style => Composite

+ 三种更新方式举例
  1. 全部发生
     + `div.remove()`将元素移出DOM树，改变页面元素几何属性，整个文档重新布局
  2. 跳过Layout
     + 改变背景颜色，直接repaint + composite
  3. 跳过Layout和paint
     + 改变transform属性值，只需composite

+ CSS属性触发事件查看方法
  + [css triggers](https://csstriggers.com/)
  + vscode css-triggers插件

+ CSS动画优化方式
  + 文档
    + [google 开发者文档](https://developers.google.com/web/fundamentals/performance/rendering/)
  + JS优化
    + 使用`requestAnimationFrame`替代`setTimeout`和`setInterval`
  + CSS优化
    + 使用`will-change`或`translate`
  + 其他方式可以参考[之前的笔记](https://blingblingredstar.github.io/2019/07/25/JavaScript-Performance/#Rendering-Performance)

## transform全解

+ [MDN tranform](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform)

+ 四个常用[`<transform-function>`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transform-function)
  + `translate` 位移
  + `scale` 缩放
  + `rotate` 旋转
  + `skew` 倾斜
+ 经验
  + 一般需要配合`transition`使用，进行过渡
  + `inline`元素不支持`transform`，需要先将其转换为`block`元素

## transition

+ [MDN transition](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transition)

+ 作用
  + 补充中间帧
  + 语法
    + `transition: <需要过渡的属性名> <过渡时间> <过渡曲线> <延迟时间>;`
+ 注意
  + 不是所有属性都能过渡
    + `display: none;` => `display: block;`不可以
    + 可以使用`visibility: hidden;` => `visibility: visible`
  + 过渡必须要有起始

## Animation

+ [MDN animation](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation)
+ `@keyframes`关键帧
  + 语法
    + [MDN `@keyframes`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@keyframes)
+ animation
  + 缩写语法
    + `animation: <动画时间> <过渡方式> <延迟> <次数> <方向> <填充方式> <是否暂停> <动画名称>`
    + 动画时间：秒或毫秒
    + 过渡方式：与transition值一样
    + 次数：数字或infinite
    + 方向：reverse | alternate | alternate-reverse
    + 填充方式：none | forwards | backwards | both
    + 是否暂停：paused | running
