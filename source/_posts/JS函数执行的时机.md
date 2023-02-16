---
title: JS函数执行的时机
date: 2019-09-25 10:29:20
categories: [学习笔记]
tags: [饥人谷, 作用域, 闭包, event loop]
---
## 一段代码

在讨论JS函数执行的时机之前，我们先来看一段代码

```js
let i = 0
for(i = 0; i<6; i++){
  setTimeout(()=>{
    console.log(i)
  },0)
}
```

请问，执行上述代码，最终控制台输出结果是什么？

1. setTimeout的id, 0, 1, 2, 3, 4, 5
2. setTimeout的id, 6, 6, 6, 6, 6, 6

可能有人会写出上述代码，并期望得到结果1，但实际控制台会输出结果2。
<!-- more -->
本篇文章，我会分析js的执行过程，来分析为什么会输出结果2，涉及到了调用栈，作用域与闭包，以及event loop与macro task queue，并在最后提出几种能够得到结果2的方法。

如果有小伙伴完全理解上述过程与结果，那么下面的内容其实可以不看，或者挑挑毛病。如果有小伙伴有任何疑问，也可以联系我，欢迎指教讨论。

## JS的执行过程

下面我会用伪代码的形式来描述我所理解的js执行过程
<!-- markdownlint-disable md033 -->
<details>
<summary>分析过程较长，如已了解可略过</summary>

```js
let i = 0 // 全局执行上下文内声明一个变量，赋值为数字0

// 此时变量区有一个全局变量，值为数字0

for(i = 0; i < 6; i++){} // 换种形式理解，可以转换为如下

i = 0
while(i < 6) {
  //for循环的{}内代码
  i++
}

// 这样转换为了方便说明js的执行过程

// js会首先查找i变量，根据作用域，for循环位于全局作用域，作用域内已经存在一个声明过的i变量，并将i变量重新赋值为数字0

// 此时全局作用域内的变量i被重新赋值为0

// js会进入条件判断，判断变量i的值是否小于6，结果为真，进入花括号内

{
  setTimeout(() => console.log(i), 0)
}

// 这里可能也是好多小伙伴理解错误的地方，此时js只会执行setTimeout，并将一个函数作为参数传递给setTimeout。
// setTimeout并不属于js的范畴，而是由js宿主环境提供的api接口，其作用为调用其他线程开始计时，调用setTimeout，会立即给js返回一个id，此时假设为1
// 计时结束后将回调函数推入macro task queue。而macro task queue内的回调函数不会立即执行，而是由event loop来判断执行的时机。
// 因为event loop模型由于宿主环境（浏览器或Node.js等)而有所不同，但在这里我们简化event loop的处理模型，event loop会不断检查js主线程内有没有未执行完的代码，如果有就执行，如果没有才会执行macro task queue内的代码。
// 所以此时不会执行id为1的setTimeout函数传入的回调函数，也就是() => console.log(i)，而是会将其推入macro task queue的队尾等待执行

// 此时macro task queue类似如下

[
  () => {console.log(i)} // id值为1的setTimeout的回调函数
]

// js会继续执行代码，既i++。此时查找i，代码此时位于全局作用域内执行，作用域内有变量i，值为0，将值加1，为1

// 全局作用域内i变量值重新赋值为1

// 此时js主线程没有执行完毕，继续执行，进入条件判断，判断i的值是否小于6，结果为真，执行下列代码

{
  setTimeout(() => console.log(i), 0)
}

// 过程与之前类似，但此时重新执行了setTimeout，并传递了一个新的函数作为参数，此时setTimeout会返回一个id，假设为2，并将回调函数推入macro task queue末尾，此时macr task queue类似如下

[
  () => {console.log(i)}, // id值为1的setTimeout的回调函数
  () => {console.log(i)}  // id值为2的setTimeout的回调函数
]

// event loop会持续检查主线程是否执行完毕，此时检查，会发现js主线程仍为执行完，会继续执行主线程内代码，既i++

// 此时全局作用于变量i值更新为2

// 此轮与上轮类似，因为是一个不断循环的过程，中间几轮暂且略过

// 当i值更新为6时，重新进行条件判断，结果为假，此时js会跳出循环，此时没有其他待执行代码，js主线程执行完毕。

// 此时event loop会检查macro task queue内有没有排队执行的回调，此时的macro task queue类似如下

[
  () => {console.log(i)}, // id值为1的setTimeout的回调函数
  () => {console.log(i)}, // id值为2的setTimeout的回调函数
  () => {console.log(i)}, // id值为3的setTimeout的回调函数
  () => {console.log(i)}, // id值为4的setTimeout的回调函数
  () => {console.log(i)} // id值为5的setTimeout的回调函数
]

// 根据队列先进先出的原则，此时会将队列第一项推出，并将其添加到js的执行线程中。
// 此时macro task queue更新如下

[
  () => {console.log(i)}, // id值为2的setTimeout的回调函数
  () => {console.log(i)}, // id值为3的setTimeout的回调函数
  () => {console.log(i)}, // id值为4的setTimeout的回调函数
  () => {console.log(i)} // id值为5的setTimeout的回调函数
]

// 此时js开始执行

() => { console.log(i) } // id值为1的setTimeout的回调函数

// js执行函数时，会创建一个新的执行上下文。此执行上下文内只涉及了一个i变量。
// js会首先在当前执行上下文内进行查找，发现并没有i变量。因为此时的执行上下文位于全局执行上下文内，js会在全局执行上下文内查找i，发现其值为6

// 此时控制台输出6

// 此时js主线程执行完毕，event loop会检查macro task queue，发现队列不为空，将队列第一项推出，此时macro task queue如下

[
  () => {console.log(i)}, // id值为3的setTimeout的回调函数
  () => {console.log(i)}, // id值为4的setTimeout的回调函数
  () => {console.log(i)} // id值为5的setTimeout的回调函数
]

// js主线程开始执行

() => {console.log(i)}, // id值为2的setTimeout的回调函数

// js执行函数，创建一个新的执行上下文，在这个新的上下文内，没有声明新变量，使用了一个i变量，根据作用域链查找，发现了全局作用域内的i变量，此时i变量值为6

// 控制台输出6

// 此过程不断重复，直到macro task queue队列为空
```

</details>

## 使用let关键字更改输出

这里的关键点，我认为有以下几点

首先是setTimeout内回调函数的执行时间，其次是setTimeout内回调函数使用的i变量的作用域

其实搞清i的作用域我们就知道，整个执行过程中，我们使用了一个i变量，并在最后输出6次i。

那么如果不更改setTimeout内的代码，有没有办法记录下i的值，并在之后输出呢，也就是实现控制台输出0, 1, 2, 3, 4, 5的效果呢

很显然，我们需要6个额外的变量来保存i每次循环时的值

在ES6之前，js并没有块级作用域，但在ES6新出的`let`和`const`中，使用这两个关键字会自动将其所在的{}变更为一个新的作用域。

而且为了记录for循环每次的i的值，js还额外做了一些工作，会将for循环的{}变成一个新的作用域。

此时我们改写代码如下

```js
for(let i = 0; i < 6; i++) {
  setTimeout(() => console.log(i))
}
```

为了理解，我会将代码转化如下

```js
{
  let _i = 0
  while(_i < 6) {
    const i = _i
    setTimeout(() => { console.log(i) })
    _i++
  }
}
```

此时在js执行完毕后，根据作用域链，查找到for循环的{}内，因为每次执行都创建新的作用域，所以每一轮循环都会有一个“新的i”，也就是类似于js保存了i变量此时的快照，所以总共有6个i，并且在for循环外是访问不到的，也就是setTimeout内的`console.log(i)`携带了一个闭包。

关于闭包的定义及使用，如果有不清楚的小伙伴可以多查找一些资料。

这里也推荐一个[系列视频](https://www.youtube.com/playlist?list=PLWrQZnG8l0E4kd1T_nyuVoxQUaYEWFgcD)

既然我们通过使用`let`关键字，来实现效果，并且其核心原理就是增加新的作用域，那么其实我们也可以使用`let`和`const`来声明新变量的方式来实现同样的效果

```js
let i
for (i = 0; i < 6; i++) {
  const j = i
  setTimeout(() => {
    console.log(j)
  })
}
```

这里我使用了`const`关键字来声明j变量，以便更明确的标明j变量是一个新的变量。

## 使用立即执行函数来创建作用域，更改输出

那么，如果不适用`let`和`const`关键字呢？

我们可以使用立即执行函数来创建作用域

```js
let i
for (i = 0; i < 6; i++) {
  ;(function() {
    var j = i
    setTimeout(() => {
      console.log(j)
    })
  })()
}
```

如果我们不想修改`console.log(i)`，还可以写为如下

```js
let i
for (i = 0; i < 6; i++) {
  ;(function(i) {
    setTimeout(() => {
      console.log(i)
    })
  })(i)
}
```

此时每执行一次循环，就会将i的值作为参数传递给立即执行函数，`console.log(i)`中i的值也就在进入macro task queue前就已确定。

以上就是我所想到的几种解决方案。

文章如有不对之处，希望看完的小伙伴批评指正。
