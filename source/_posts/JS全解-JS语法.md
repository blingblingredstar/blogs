---
title: '[JS全解]JS语法'
date: 2019-09-11 08:42:24
categories: [学习笔记]
tags: [饥人谷, JavaScript]
---
> 本文章资料主要来自[饥人谷2020版前端体系课](https://mp.weixin.qq.com/s/nEzhoM75UCRMWReDEMnAfQ)，本文为课程笔记

## JS语言

### JS版本

+ 历史版本
  + ES3 1999年
  + ES5 2009年
  + ES6 2015年

## JS语法

### 表达式与语句

+ 表达式
  + `1+2`这个表达式的值为3
  + `add(1, 2)`这个表达式的值为函数`add`的返回值
  + `console.log`表达式的值为函数本身
  + `console.log(3)`表达式的值为`console.log`的返回值，也就是`undefined`
<!-- more -->
+ 语句
  + `var a = 1`是一个语句

+ 表达式与语句的区别
  + 表达式一般都有值，语句不一定有
  + 语句一般会改变环境(声明、赋值)
  + 以上并不绝对

### 大小写敏感(Case Sensitive)

+ `var a`与`var A`声明了两个不同变量
+ `object`与`Object`为两个不同变量
+ `function`与`Function`也是两个不同变量

### 空格与换行

+ 不影响语义的情况下没有影响
  + return后不能加换行(因为自动分号插入机制)

### 标识符

+ 规则
  + 第一个字符，可以使Unicode字母或者&或_或者中文
  + 后面的字符可以再添加数字

### 注释

+ 语法
  + `//` 单行注释
  + `/**/` 多行注释

+ 不好的注释
  + 翻译代码的注释
  + 过时的注释
  + 无关的注释
+ 好的注释
  + 采坑注释
  + 为什么代码会这么写，遇到什么bug

### 区块block

+ 把代码包裹在一起

```js
{
  let a = 1
  let b = 2
}
```

+ 常与if/for/while/function合用

### if语句

+ 语法
  + `if(表达式) {语句} else {语句}`
  + `{}`在语句只有一句时可省略，但不建议

### switch语句

+ 语法

```js
switch(fruit) {
  case 'apple':
    //...
    break;
  case 'bananab':
    //...
    break;
  default:
    //...
}
```

### 三元表达式

+ 语法
  + 表达式 ？ 为真情况表达式 : 为假情况表达式

### &&短路逻辑

+ `a && b && c && d`取第一个假值或`d`

### ||短路逻辑

+ `a || b || c || d`取第一个真值或`d`

### while循环

+ 语法
  + while(表达式) {语句}
  + 首先判断表达式真假
  + 为真执行语句
  + 执行完语句，重新判断表达式真假
  + 表达式为假，跳出循环

### for循环

+ 语法糖
  + for是while的语法糖

+ 语法
  + `for(语句1; 表达式2; 语句三) { 循环体 }`
  + 先执行语句1
  + 判断表达式2的真假
  + 如果为真，执行循环体，然后执行语句三
  + 如果为假，跳出循环

### break和continue

+ break
  + 退出所有循环
+ continue
  + 退出当前一次循环

### label语句

+ 语法

```js
foo: {
  console.log(1)
  break foo
  console.log('本行不会输出')
}
```

+ 考点

```js
{
  foo: 1
}
// 上面是什么？
```

### 推荐书籍

[You-Dont-Know-JS](https://github.com/getify/You-Dont-Know-JS/tree/2nd-ed)

[阮一峰 - JavaScript 教程](https://wangdoc.com/javascript/)
