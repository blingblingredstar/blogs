---
title: '[CSS全解]CSS定位'
date: 2019-09-01 10:14:37
categories: [学习笔记]
tags: [饥人谷, CSS, position, 层叠上下文]
---
本文章资料主要来自[饥人谷2020版前端体系课](https://mp.weixin.qq.com/s/nEzhoM75UCRMWReDEMnAfQ)，本文为课程笔记。

## 布局 vs 定位

布局是基于平面空间的二维坐标，定位基于垂直于屏幕的三维坐标。

## 文档流与盒模型

之前我们知道，文档流(Normal Flow)正常从上到下，从左到右根据盒模型依次排列，但是一个盒模型其实有多个图层，从下到上依次为：

1. background
2. border
3. 块级子元素
4. 浮动元素
5. 内联子元素(文字内容)

通过上面的模型，可知浮动元素脱离文档流，就是从第三层的块级子元素变为了第四层的浮动元素。
<!-- more -->
## `position`属性

+ `position`属性值
  + `static` 默认值，元素在文档流中
  + `relative` 相对定位，元素图层上浮，但不脱离文档流
  + `absolute` 绝对定位，基于父级元素`position`属性值为非`static`进行定位
  + `fixed` 固定定位，定位基准为viewport
  + `sticky` 粘滞定位
+ 经验
  + `absolute`与`relative`结合使用
  + `position`值为`absolute`或`fixed`时，添加`top`或`left`属性
  + `sticky`兼容性较差，面试时可以提到

### `position: relative;`

+ 使用场景
  + 用于做位移(很少使用)
  + 用于做于`position: absolute;`元素的父级元素
+ 配合`z-index`属性
  + `z-index: auto;` 默认值，不新建层叠上下文
  + `z-index: 0/1/2;` 图层排列顺序，数字越大图层越靠上
  + `z-index: -1/-2;` 图层排列顺序，数字越小图层越靠下
+ 经验
  + 不推荐`z-index: 9999;`
  + **学会管理`z-index`**

### `position: absolute;`

+ 使用场景
  + 元素脱离原来的位置，另起一层，比如对话框关闭按钮
  + 鼠标提示
+ 配合`z-index`
+ 经验
  + 不是相对于`relative`进行定位的，是相对于父类元素最近一个定位元素(`position`属性值为非`static`的元素)进行定位的
  + **某些浏览器如果没有`top`/`left`属性位置会错乱**
  + 善用`left: 100%`/`bottom: 100%`
  + 善用`left: 50%`，并添加负外边距

### `position: fixed;`

+ 使用场景
  + 广告
  + 回到顶部按钮
+ 配合`z-index`
+ 经验
  + **移动端最好不要使用**，bug较多
  + 可以搜索关键字"移动端fixed"

### `position: sticky;`

+ 使用场景
  + 导航
+ 注意
  + can i use上一片红。。。

## 层叠上下文

+ 当内部元素设置`position`等属性时，会浮动到外部元素最上方，并且`z-index: 0;`
+ 当内部元素的`z-index`为负数时，会位于外部元素background下方
+ 但此时的`z-index`仍作用在当前层叠上下文中

+ 层叠上下文(堆叠上下文)
  + `z-index`只作用在当前层叠上下文中
  + 每一个层叠上下文就是一个新的作用域
  + 当前作用域的`z-index`与其他作用域无关
  + 处在同一作用域的`z-index`才能进行比较
  + 那些属性可以创建层叠上下文
    + [MDN](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Understanding_z_index/The_stacking_context)
    + 重点属性`z-index/flex/opacity/transform`
