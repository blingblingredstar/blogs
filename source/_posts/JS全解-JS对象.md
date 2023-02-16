---
title: '[JS全解]JS对象'
date: 2019-09-18 09:42:57
categories: [学习笔记]
tags: [饥人谷, JavaScript, 数据类型]
---
> 本文章资料主要来自[饥人谷2020版前端体系课](https://mp.weixin.qq.com/s/nEzhoM75UCRMWReDEMnAfQ)，本文为课程笔记

## 语法

+ 对象的定义
  + 无序的数据集合
  + 键值对的集合

+ 声明对象
  + `const obj = { 'name': 'Frank', 'age': 18 }`
  + `const obj = new Object({ 'name': 'Frank' })`
  + `console.log({ 'name': 'Frank', 'age': 18 })`
<!-- more -->
+ 细节
  + 键名为字符串，不是标识符，可以包含任意字符
  + 引号可省略，省略后只能写标识符
  + **即使省略引号，键名仍为字符串**
  + 使用`Object.keys()`可获取对象的所有键名

+ 使用变量的值作为属性名
  + `const p1 = 'name'` 定义一个变量
  + `const obj = { p1: 'Frank' } // { 'p1': 'Frank' }` 直接书写，键名为p1
  + `const obj = { [p1]: 'Frank' } // { 'name': 'Frank' }` 使用方括号包裹，键名为变量p1的值

+ 对比
  + 不加[]的属性名会自动转换为字符串
  + 加了[]后会当做表达式求值

+ 对象的隐藏属性
  + JS中每个对象都有一个隐藏属性
  + 这个隐藏属性存储着其共有属性和方法组成的对象的地址
  + 这个由其公共属性和方法组成的对象叫做原型
  + 也就是说每个对象都有一个隐藏属性，存储着其原型的地址

+ Symbol也可作为属性名
  + 迭代时会使用
  + 可见[这里](https://blingblingredstar.github.io/2019/05/02/JavaScript-The-Recent-Parts/#Data-Structure-Without-Iterators)

## 对象属性的增删改查

### 删除属性

+ `delete obj.propertyName`或`delete obj['propertyName']`
  + 可以删除obj的propertyName属性
+ 不含属性名
  + `'propertyName' in obj === false`
+ 含有属性，但值为undefined
  + `'property' in obj && obj.property === undefined`

### 原型对象

+ 每个对象都有一个原型对象(使用Object.create(null)创建的除外)
  + 原型对象保存着实例对象的共有属性和方法

+ 实例对象的原型对象也是对象
  + 所以原型对象也有原型对象
  + `const obj = {}`，obj的原型对象也是所有Object的实例对象的原型对象
  + 这个原型对象包含了所有对象的公共属性和方法，是对象的根
  + 这个原型对象的原型指向了null(原型链的最终指向)

### 查看属性

+ 查看自身所有属性
  + `Object.keys(obj)`
+ 查看自身属性 + 共有属性
  + `console.dir(obj)`
  + 使用`Object.keys()`分别传入`obj`和`obj.__proto__`
+ 判断属性是否为自身属性
  + `obj.hasOwnProperty('toString')`
  + 使用`in`关键字，可以查看当前对象或者原型对象上是否有对应属性，使用`hasOwnProperty`，可以查看当前对象上是否有特定属性
+ 查看指定属性
  + 中括号语法
    + `obj['key']`
  + 点语法
    + `obj.key`
  + 两种语法功能上没有区别，使用中括号语法，可以使用变量来访问属性

### 修改/增加属性

+ 直接赋值
  + `const obj = { 'name': 'Frank' }`
  + `obj.name = 'Frank' // name是字符串`
  + `obj['name'] = 'Frank'`
  + `obj['na' + 'me'] = 'Frank'`
  + `const key = 'name'; obj[key] = 'Frank'`

+ 批量赋值
  + `Object.assign(obj, {'age': 18, 'gender': 'man'})`

+ 修改/增加原型对象属性
  + 无法通过自身修改或增加原型对象属性
  + 一般来说，不推荐修改原型
  + 如果要修改，通过构造函数的原型属性进行修改

+ 修改原型对象
  + 不推荐使用`__proto__`属性进行修改
  + 在创建时直接使用`Object.create()`，传入一个对象作为新建对象的原型对象
