---
title: Markdown语法学习
date: 2019-03-20 14:12:25
categories: 学习笔记
tags: [Markdown]
---

>参考文章
[Markdown基本语法](https://www.jianshu.com/p/191d1e21f7ed)
<!--more-->
## 标题

## 这是二级标题

### 这是三级标题

#### 这是四级标题

##### 这是五级标题

###### 这是六级标题

## 文字效果

**这是加粗的文字**
*这是倾斜的文字*`
***这是斜体加粗的文字***
~~这是加删除线的文字~~

## 引用内容

>这是引用的内容
>>这是引用的内容
>>>这是引用的内容

## 分割线

---
----
***
*****

## 图片

![blockchain](https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/
u=702257389,1274025419&fm=27&gp=0.jpg "区块链")

## 链接

[简书](http://jianshu.com)
[百度](http://baidu.com)

## 列表

- 列表内容
+ 列表内容
* 列表内容

### 注意：- + * 跟内容之间都要有一个空格

1. 列表内容
2. 列表内容
3. 列表内容

### 注意：序号跟内容之间要有空格


## 代码：

单行代码：代码之间分别用一个反引号包起来

`代码内容`


## 代码块：

代码之间分别用三个反引号包起来，且两边的反引号单独占一行

```js
  代码...
  代码...
  代码...
```

## 流程图

### 看来并不支持

```flow
st=>start: 开始
op=>operation: My Operation
cond=>condition: Yes or No?
e=>end
st->op->cond
cond(yes)->e
cond(no)->op
&```