---
title: 学习jQuery
date: 2019-10-28 10:42:40
categories: 学习笔记
tags: [饥人谷, jQuery]
---
## 为什么学习jQuery

在三大框架(Vue, React, Angular)日益盛行的今天，jQuery的热度似乎已经逐渐消减。但违反直觉的是，根据[Built width](https://trends.builtwith.com/javascript/jQuery)的统计，jQuery的使用数量仍在逐年上涨。仔细想想，作为前端开发者，难免要与DOM操作打交道，而因为历史原因，原生DOM的API既不好用，兼容性问题又多，如果把《JavaScript高级程序设计》中讲浏览器兼容问题的部分删掉，可能看起来也就没有那么厚，那么让人望而却步了。jQuery作为一个封装良好的DOM库，jQuery能够如此流行还要归功于其良好的设计模式，使用起来相当便利，虽然也及其容易写出一些[意大利面条似的代码](https://zh.wikipedia.org/wiki/%E9%9D%A2%E6%9D%A1%E5%BC%8F%E4%BB%A3%E7%A0%81)，但作为前端新人，通过学习jQuery，了解一些jQuery的设计思想，无论是对于今后学习其他框架，还是打牢js基础，都是很有必要的。
<!-- more -->
## jQuery的使用方法

### 使用jQuery选择DOM元素

作为一个封装良好的DOM库，使用jQuery往往从选择一个DOM元素开始，然后对其进行某种操作。

我们可以通过使用`jQuery()`或者更加简洁的`$()`来获取一个DOM元素。

```js
$('div') // 获取页面所有div元素

$('.red') // 获取所有class包含red的元素

$('#red') // 获取id为red的元素

$('input[name=firstname]') // 获取name属性为firstname的input元素

$('a:first') // 获取页面中第一个a元素

$('li:odd') // 获取奇数li
```

其他选择方式，参见[选择器](https://www.jquery123.com/category/selectors/)

### 操作DOM元素

jQuery的设计思想在于选择DOM元素，并对其进行操作，但由于DOM API的复杂性，使用jQuery选择DOM元素后并不会返回DOM对象，而是一个jQuery对象，通过操作jQuery对象，即可操作使用jQuery选中的DOM对象，这极大的方便了DOM元素的可操作性，也由于jQuery出色的封装，使得常用属性操作也非常方便。

#### 链式操作

正因为使用jQuery选择DOM后返回一个jQuery对象，所以我们可以使用链式操作，类似如下

```js
$('.red') // 找到指定元素集合
  .find('li') // 找到集合中特定部分
  .eq(2) // 选择指定元素
  .text('Hello') // 对其进行操作
```

#### getter/setter

在jQuery中，部分API会根据参数不同来改变API的行为，其中，比较方便的一项就是如果指定参数，就将API作为一个setter使用，将属性设置为指定的值；如果不传入参数，那就作为getter使用，返回元素指定属性的值

```js
$('h1').text() // 返回h1的文本内容

$('h1').text('hello') // 设置h1的文本内容为hello
```

#### 创建元素

使用jQuery，我们可以方便的创建元素

```js
$('<div>Hello world</div>') // 创建一个div元素
  .append($('.red')) // 插入指定元素集之后
```

关于使用jQuery创建DOM元素，更多信息可以查看[这里](https://www.jquery123.com/jQuery/)

其他DOM操作，可以查看[这里](https://www.jquery123.com/category/manipulation/)

#### 移动元素

通过jQuery提供的API的组合，我们可以移动一个DOM元素到指定的位置。

```js
$('.red').insertAfter($('.green')) // 将class包含red的元素插入class包含green的元素之后

$('.green').after($('.red')) // 将class包含red的元素放在class包含green的后面
```

类似还有`.insertBefore()`，`.before()`，`.appendTo()`，`.append()`，`.prependTo()`和`.prepend()`

#### 修改DOM属性

```js
$('div').addClass('.red') // 给所有div添加样式类red

$('div').css({width: '200px'}) // 将所有div宽度设置为200px
```

更多请查看[通用属性操作](https://www.jquery123.com/category/manipulation/general-attributes/)，[class 属性](https://www.jquery123.com/category/manipulation/class-attribute/)，[CSS 属性](https://www.jquery123.com/category/manipulation/style-properties/)
