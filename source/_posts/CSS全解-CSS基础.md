---
title: '[CSS全解]CSS基础'
date: 2019-08-28 11:18:53
categories: [学习笔记]
tags: [饥人谷, CSS, 盒模型, 文档流, margin合并]
---
本文章资料主要来自[饥人谷2020版前端体系课](https://mp.weixin.qq.com/s/nEzhoM75UCRMWReDEMnAfQ)，本文为课程笔记。

## CSS的历史

由哈肯·维姆·莱(Håkon Wium Lie)与1994年提出css的概念。

CSS(Cascading Style Sheets)中文为层叠样式表。

### CSS版本

当前使用最广泛的为CSS2.1，最新为CSS3，分模块不停升级。

### CSS版本兼容性

手工测试

[can i use](https://caniuse.com/)
<!-- more -->
### CSS学习原则

+ 所见即所得
+ 不需要理性思维。。。
  + 不要问为什么
  + 而是说原来是这样子
  + 浏览器显示出什么样就是什么样

## CSS体系化学习

+ 学习语言必须学会
  + 语法
  + 调试方法
  + 文档
  + 标准制定者是谁

+ 如何学习
  + Copy
  + Run
  + Modify

### CSS语法

#### 语法一

```css
选择器 {
  属性名: 属性值;
  /* 注释 */
}
```

+ 注意事项
  + 只支持英文
  + 大小写敏感
  + 使用分号换行

#### 语法二：@语法

```css
@charset "utf-8";
@import url(2.css);
@media (min-width: 100px) and (max-width: 200px) {
  语法一
}
```

+ 注意事项
  + `@charset`必须放在第一行，指定文件编码而不是字符集
  + 前两个@语法必须以`;`结尾

### 调试方法

+ 使用w3c验证器在线验证
+ 编辑器警告
+ 使用Chrome DevTools

#### Boder调试法

顾名思义，给要调试的元素加边框，调试结束再去掉

CSS的`console.log`

### 推荐资料

+ MDN
+ CSS Tricks
+ 张鑫旭的博客

### 推荐素材

+ PSD
  + Freepik搜索PSD web
    + 注意添加版权信息
  + 365PSD内的UI套件

+ 效果图
  + dribble
  + behance

### 权威标准

w3c CSS spec

## CSS基本概念

+ 重要概念
  + 文档流Normal Flow
  + 块、内联、内联块
  + margin合并
  + 两种盒模型

### 文档流(Normal Flow)

+ 文档流指元素正常的流动方向
  + 流动方向
    + inline元素从左到右，到达最右边换行
    + block元素从上到下，每个元素一行
    + inline-block也是从左到右，换行时不会切断元素
  + 宽度
    + inline元素宽度为内部inline元素宽度的总和，不能用width指定
      + **注意：不推荐在inline元素内添加block元素**
    + block元素默认自动计算宽度，可用width指定
      + **注意：不推荐指定宽度为100%**
    + inline-block结合两者，可用width指定
  + 高度
    + inline高度由line-height间接确定，跟height无关
    + block高度由内部文档流(Normal Flow)元素决定，可指定height
    + inline-block跟block类似，可指定height

#### overflow 溢出

+ 内容高度或宽度大于容器元素时发生
+ 可使用`overflow`属性指定是否显示滚动条
  + `auto`为灵活展示
  + `scroll`为一直展示
  + `hidden`为隐藏超出部分内容
  + `visible`为显示超出内容
  + `overflow`属性可具体指定`overflow-x`与`overflow-y`

#### 脱离文档流

+ block元素高度由内部文档流元素决定，可以指定高度
  + 那么，元素也可以不在文档流中
+ 哪些元素脱离文档流？
  + `float`
  + `position: absolute/fixed`
+ 如何保证属性不脱离文档流
  + 不使用以上两个属性

### 盒模型

+ 共有两种盒模型
  1. content-box 内容盒: 内容为盒子边界
  2. border-box 边框盒: 边框为盒子边界

+ 计算方式
  1. content-box: width = 内容宽度
  2. border-box: width = 内容宽度 + padding + border

+ 推荐使用border-box

#### margin合并

+ 哪些情况margin会合并
  + 父子margin合并
    + 只在上下方向存在，左右不会合并
  + 兄弟margin合并
+ 如何阻止合并
  + 父子合并
    + 使用`padding/border`挡住
    + 使用`overflow: hidden;`挡住
    + 使用`display: flex;`挡住
  + 兄弟合并是符合预期的
    + 可用`inline-block`消除
