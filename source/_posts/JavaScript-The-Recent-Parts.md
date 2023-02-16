---
title: 'JavaScript: The Recent Parts'
date: 2019-05-02 10:39:03
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, ES6, 解构赋值, 迭代器, 生成器, 正则表达式, 异步等待]
---
## Introduction

### Code is for Humans

来自灵魂的两个拷问：

有没有一段代码没有效果，但是就是不知道为什么没效果？
> Have you ever had a piece of code, that it broke but you just had no idea why it broke?

有没有一段代码起作用了，但是不知道为什么起作用了？
> Have you ever written a piece of code that worked and you had no idea why it worked?

如果你不知道你的代码为什么有作用，在他崩溃时你是无法修复的，除了寄希望于运气、猜测和魔法。
> If you don’t know why your code works, you have no hope of fixing it when it breaks, other than sheer luck and guessing and magic.

<!--more-->

### JavaScript New Feature Process

js现在是一个变化飞速的语言，每天都有新的功能与框架推出，这其中很大一部分原因是因为js在ES6之前已停滞多年。ES6为了解决这个问题将每年更新版本。

### Declarative JavaScript

js的发展方向之一就是js正在成为一门更具声明性的语言。

### Browser Support Transpilers

嗯，幸好我们有babel。

### Course Overview

JavaScript Versions(新版本)

+ ES6/ES2015
+ ES2016
+ ES2017
+ ES2018
+ ES2019

Features(新功能)

+ Template Strings(模板字符串)
+ String padding/trimming(字符串填充/修剪)
+ Destructuring(解构赋值)
+ Array find() / includes()(数组的find()和include()方法)
+ Array flat() / flatMap()(数组的flat()和flatMap()方法)
+ Iterators, Generators(迭代器，生成器)
+ RegExp Improvements(正则表达式的改进)
+ async .. await(异步)
+ async* .. yield await

## Strings

### Template Strings

改进了字符串拼接的体验。使用``代替要拼接的字符串的引号，使用${}包裹js表达式，可以是变量名，js语句甚至另一个模板字符串。注意，模板字符串的行为更像立即执行函数，既在执行时就立即替换变量语句为真正的字符串。

### Tagged Templates

```js
function formatCurrency(string, ...values) {
  var str = ''
  for(let i = 0; i < string.length; i++) {
    if(i > 0) {
      if(typeof values[i - 1] == 'number') {
        str += `$${values[i - 1].toFixed(2)}`
      }
      else {
        str += values[i - 1]
      }
    }
    str += string[i]
  }
  return str
}

var amount = 12.3

var msg =
  formatCurrency
`The total for your
order is ${amount}` // "The total for your order is $12.30"
```

### Applying Tagged Templates

```js
function logger(string, ...values) {
  var str = ''
  for(let i = 0; i < string.length; i++){
    if(i > 0) {
      if(values[i - 1] && typeof values[i - 1] == 'object') {
        if(values[i - 1] instanceof Error) {
          if(values[i - 1].stack) {
            str += values[i - 1].stack
            continue
          }
        }else {
          try {
            str += JSON.stringify(values[i - 1])
            continue
          }
          catch(err) {}
        }
      }
      str += values[i - 1]
    }
    str += string[i]
  }
  console.log(str)
  return str
}
```

```js
var v = 42
var o = { a: 1, b: [2, 3, 4] }

logger`This is my value: ${v} and another: ${o}`
// "This is my value: 42 and another: {"a":1,"b":[2,3,4]}"

try {
  nothing()
}
catch(err) {
  logger`Caught: ${err}`
}
// "Caught: ReferenceError: nothing is not defined at <anonymous>:2:3"
```

### Tagged Template Exercise

```js
function upper(strings,...values) {
  var str = ''
  for(let i = 0; i < strings.length; i++) {
    if(i > 0) {
      str += String(values[i - 1]).toUpperCase()
    }
    str += strings[i]
  }
  return str
}

var name = "kyle",
  twitter = "getify",
  topic = "JS Recent Parts";

console.log(
  upper`Hello ${name} (@${twitter}), welcome to ${topic}!` ===
  "Hello KYLE (@GETIFY), welcome to JS RECENT PARTS!"
);
```

### Padding & Trimming

#### padStart()

```js
var str = 'hello'

str.padStart(5) // "hello"

str.padStart(8) // "   hello"

str.padStart(8, '*') // "***hello"

str.padStart(8, '12345') // "123hello"

str.padStart(8, 'ab') // "abahello"
```

#### padEnd()

```js
var str = 'hello'

str.padEnd(5) // "hello"

str.padEnd(8) // "hello   "

str.padEnd(8, '*') // "hello***"

str.padEnd(8, '12345') // "hello123"

str.padEnd(8, 'ab') // "helloaba"
```

#### trim() trimStart() trimEnd()

```js
var str = '    some  stuff  \t\t'

str.trim() // "some  stuff"

str.trimStart() // "some  stuff  制表符制表符"

str.trimEnd() // "    some  stuff"
```

## Array Destructuring(数组解构赋值)

### Destructuring(解构赋值)

将结构分解成各个部分
> decomposing a structure into its individual parts

[MDN解构赋值](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)

> 解构赋值语法是一种 Javascript 表达式，它使得将值从数组，或属性从对象，提取到不同的变量中，成为可能。

结构赋值可以使我们将如下代码:

```js
var tmp = getSomeData()

var first = tmp[0],
    second = tmp[1]

var firstName = first.name,
    firstEmail = first.email !== undefined ? first.email : 'nobody@none.tld'

var secondName = second.name,
    secondEmail = second.email !== undefined ? second.email : 'nobody@none.tld'
```

简写为如下代码:

```js
var [
  {
    name: firstName,
    email: firstEmail = 'nobody@none.tld'
  },
  {
    name: secondName,
    email: secondEmail = 'nobody@none.tld'
  }
] = getSomeData()
```

### Refactoring Code Using Destructuring(使用解构赋值重新组织代码)

```js
function data() {
  return [1, , 3, 4, 5]
}
//**********************
var tmp = data()

var first = tmp[0],
    second = tmp[1] !== undefined ? tmp[1] : 10, // 当对应值为undefined时设置默认值为10
    third = tmp[2],
    fourth = tmp.slice(3) // 剩下的所有数组元素存储到fourth这个数组中
//**********************
var [
  first,
  second = 10, // 当对应值为undefined时设置默认值为10
  third,
  ...fourth // 剩下的所有数组元素存储到fourth这个数组中
] = data()
```

### Spread Operator & Declaring Destructured Arrays(展开运算符和声明解构数组)

> 展开语法(Spread syntax), 可以在函数调用/数组构造时, 将数组表达式或者string在语法层面展开；还可以在构造字面量对象时, 将对象表达式按key-value的方式展开。(译者注: 字面量一般指 [1, 2, 3] 或者 {name: "mdn"} 这种简洁的构造方式)

展开运算符只能在赋值最后使用（代表了将剩余所有数据放在数组中）。

### Declaration & Assignment(声明和赋值)

结构赋值是一个赋值操作，而不是声明操作，所以可以给已经声明的变量赋值，同时也不能给未声明的变量赋值。（使用中注意未声明的变量前要使用`const`，`let`，`var`关键字声明，已经声明的变量注意在行首或上一行尾加`;`，因为**根据js的ASI(Automatic Semicolon Insert自动分号插入机制)并不会在括号，方括号，正则开头的斜杠，加号，减号前插入分号**。

```js
function data() {
  return [1, , 3, 4, 5]
}

var obj = {}
;[
  obj.first,
  obj.second = 10,
  obj.third,
  ...obj.fourth
] = data()
```

### Comma Separation(逗号分离)

```js
function data() {
  return [1, , 3, 4, 5]
}

var [
  first,
  , // 忽略data()返回的数组第二项
  third,
  ...obj.fourth
] = data() || [] // 提供一个后备空数组
```

交换变量的值

```js
var x = 1,
    y = 2
{
  let tmp = x
  x = y
  y = temp
}
//******************
;[x, y] = [y, x]
```

### Parameter Arrays(参数数组)

```js
function data(tmp) { // 接收参数数组后在函数内部赋值
  var [
    first,
    second,
    third
  ] = tmp

  console.log(first, second, third)
}
//********************
function data([ // 直接赋值接收参数数组中对应的值为参数的值
  first,
  second,
  third
] = []) { // 提供一个后备空数组
  // .....
  console.log(first, second, third)
}
```

### Nested Array Destructuring(套嵌数组解构赋值)

```js
function data() {
  return [1, [2, 3], 4]
}

var [
  first,
  [
    second,
    third
  ] = [], // 设置默认值为一个空数组
  fourth
] = data() || []
```

## Object Destructuring(对象解构赋值)

### Object Destructuring(对象的解构赋值)

```js
function data() {
  return {
    a: 1,
    // b: 2,
    c: 3,
    d: 4
  }
}

var tmp = data()

var first = tmp.a,
    second = tmp.b,
    third = tmp.c
// *******************
var { // 使用对象的解构赋值，键值对应即可，顺序无所谓
   a: first,
   b: second = 2, // 源对象属性不存在时设置默认值
   ...third // {c: 3, d: 4}
} = data()
```

### Object Assignment Destructuring(声明变量时对象的解构赋值)

在使用对象的解构赋值时，如果前面没有`const`、`let`或`var`关键字，直接使用{}，那么会被解析为一个块级作用域，为了说明这是一个解构赋值语句，需要将语句用()包裹起来，代码如下：

```js
function data() {
  return {
    a: 1,
    // b: 2,
    c: 3,
    d: 4
  }
}

var first, second, third

;({// 整个语句需要用()包裹，因为ASI机制，需要在语句前加;或上一句尾加;
  a: first,
  b: second = 2,
  ...third
} = data())
```

### Object Default Assignment(对象的默认值)

```js
function data() {
  return {}

var {
  a: first,
  b: second = 2,
  ...third
} = data() || {} // 为赋值提供备用对象，防止报错
```

```js
function data() {
  return {
    a: 1,
    // b: 2,
    c: 3,
    d: 4
  }
}

var {
  a, // a = obj.a
  b = 2
} = data() || {} // 为赋值提供备用对象，防止报错
```

### Nested Object Destructuring(套嵌对象的解构赋值)

```js
function data() {
  return {
    a: 1,
    // b: 2,
    c: {
      d: 4,
      e: 5
    }
  }
}

var {
  a: first,
  b: second = 2,
  c: {
    d: fourth,
    e: fifth
  } = {} // 设置默认值
} = data() || {} // 为赋值提供备用对象，防止报错
```

使用数组或对象解构赋值时最好设置一个默认值（空数组或空对象）

### Parameter Objects(参数对象)

```js
function data(obj = {}) { // 先接受一个对象，在函数内声明变量解构赋值为对象属性值
  var {
    a,
    b
  } = obj

  console.log(a, b)
}
//***************************
function data({ // 直接在接收对象时将对象的对应属性的值赋值给函数的两个参数
  a,
  b
} = {}) {
  console.log(a, b)
}
```

### Nested Object & Array Destructuring(套嵌对象与数组的解构赋值)

```js
var obj = {
  a: 1,
  b: {
    x: 2
  },
  c: 3
}

var {
  a,
  b, // {x: 2}
  c,
  b: {  // 把obj.b的值赋值给变量e
    x: e // 2
  } = {} // 设置默认值为空对象
} = obj || {}
```

## Further Destructuring(进一步了解解构赋值)

### Named Arguments(命名参数)

```js
function lookupRecord(store = 'person-records', id = -1){ // 之前设置函数参数的做法
  //.................
  console.log(store, id)
}

function lookupRecord({ // 把参数设置为对象解构赋值的形式
  store = 'person-records',
  id = -1
} = {}) {
  // ............
  console.log(store, id)
}

lookupRecord({id = 42})
// 调用时直接传入对象，因为对象解构赋值与顺序无关，只要参数名称一致，顺序随意，可以不用像第一个函数中那样确保参数顺序对应，但同时就是要确保与定义函数是的参数名称一致
```

### Destructuring & Restructuring(解构赋值与重构)

```js
// 定义两个对象，保存了默认设置与当前设置
var defaults = {
  url: 'http://some.base.url/api',
  method: 'post',
  headers: [
    'Content-Type: text/plain'
  ]
}

var settings = {
  url: 'http://some.other.url/',
  data: 43,
  callback: function(response) { /* ... */ }
}
//***************************************************************

// 使用underScore的extend方法混合两个设置对象，传递给ajax进行调用
ajax(_.extend({}, defaults, settings))

// 或者使用原生方法
ajax(Object.assign({}, defaults, settings))

//***************************************************************

// 直接设置一个函数，返回默认参数对象
function ajaxOptions({ // 传参时直接把参数对象修改为目标结构并设置默认值
  url = 'http://some.base.url/api',
  method = 'post',
  data,
  callback,
  headers: [
    header0 = 'Content-Type: text/plain',
    ...otherHeaders
  ] = []
} = {}) {
  return {  // 返回按指定格式定义好的对象
    url, method, data, callback,
    headers: [
      headers0,
      ...otherHeaders
    ]
  }
}

var default = ajaxOptions() // 声明变量保存默认设置
ajax(default) // 传入默认设置

var settings = {
  url: 'http://some.other.url/',
  data: 43,
  callback: function(response) { /* ... */ }
}

ajax(ajaxOptions(settings)) // 传入当前设置替换默认设置，并调用api
```

## Array Methods(数组方法)

### find, findIndex. & includes

#### Array.prototype.find() & Array.prototype.findIndex

```js
var arr = [{a: 1}, {a: 2}]

arr.find(v => v && v.a > 1) // {a: 2}

arr.find(v => v && v.a > 10) // undefined

arr.findIndex(v => v && v.a > 10) // -1
```

#### Array.prototype.includes()

```js
var arr = [10, 20, NaN, 30, 40, 50]

arr.includes(20) // true

arr.includes(60) // false

arr.includes(20, 3) // false

arr.includes(10, -2) // false

arr.includes(40, -2) // true

arr.includes(NaN) // true
```

### flat & faltMap

> flat() 方法会按照一个可指定的深度递归遍历数组，并将所有元素与遍历到的子数组中的元素合并为一个新数组返回。
> flatMap() 方法首先使用映射函数映射每个元素，然后将结果压缩成一个新数组。它与 map 和 深度值1的 flat 几乎相同，但 flatMap 通常在合并成一种方法的效率稍微高一些。

```js
var nestedValues = [1, [2, 3], [[]], [4, [5]], 6]

nestedValues.flat(0) // [1, [2, 3], [[]], [4, [5]], 6]
nestedValues.flat(/* 默认值为1 */) // [1, 2, 3, [], 4, [5], 6]
nestedValues.flat(2) // [1, 2, 3, 4, 5, 6]
```

```js
[1, 2, 3].map(v => [v * 2, String(v * 2)])
// [[2, '2'], [4, '4'], [6, '6']]
[1, 2, 3].map(v => [v * 2, String(v * 2)]).flat() // 先调用map在调用flat
// [2, "2", 4, "4", 6, "6"]
[1, 2, 3].flatMap(v => [v * 2, String(v * 2)]) // 直接调用flatMap
// [2, "2", 4, "4", 6, "6"]
```

```js
[1, 2, 3, 4, 5, 6].flatMap(function doubleEvens(v) { // 使用flatMap删除了奇数项，添加了偶数项
  if(v % 2 === 0) {
    return [v, v * 2]
  } else {
    return []
  }
})
// [2, 4, 4, 8, 6, 12]
```

## Iterators + Generators(迭代器+生成器)

### Iterators(迭代器)

[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Iterators_and_Generators)

```js
var str = 'hello',
    world = ['w', 'o', 'r', 'l', 'd']

var iterator1 = str[Symbol.iterator]()
    iterator2 = world[Symbol.iterator]()

iterator1.next() // {value: "h", done: false}
iterator1.next() // {value: "e", done: false}
iterator1.next() // {value: "l", done: false}
iterator1.next() // {value: "l", done: false}
iterator1.next() // {value: "o", done: false}
iterator1.next() // {value: undefined, done: true}

iterator2.next() // {value: "w", done: false}
iterator2.next() // {value: "o", done: false}
iterator2.next() // {value: "r", done: false}
iterator2.next() // {value: "l", done: false}
iterator2.next() // {value: "d", done: false}
iterator2.next() // {value: undefined, done: true}
```

### Declarative Iterators

```js
var str = 'hello'

for( // 可以写成这样的循环拿到数据的每一个值，但是太繁琐，不好理解
  let it = str[Symbol.iterator](), v, result;
  (result = it.next()) && !result.done && (v = result.value || true);
){
  console.log(v)
}
// h e l l o
```

```js
var str = 'hello',
    it = str[Symbol.iterator]()
for(let v of it) { // 使用for of迭代数据的迭代器，此时可以把每次的值作为参数传递给其他函数
  console.log(v)
}
// h e l l o
for (let v of str) { // 可以直接使用for of迭代数据本身
  console.log(v)
}
// h e l l o
```

```js
var str = 'hello'

var letters = [...str] // ...展开运算符与迭代器行为类似
letters // ["h", "e", "l", "l", "o"]
```

### Data Structure Without Iterators

js对象没有原生的内置迭代器

```js
var obj = { // 对象没有内置迭代器
  a: 1,
  b: 2,
  c: 3
}

for(let v of obj) {
  console.log(v)
} // TypeError

[...obj]
// Uncaught TypeError: object is not iterable (cannot read property Symbol(Symbol.iterator))
```

使用一个工厂函数制作对象内的迭代器

```js
var obj = {
  a: 1,
  b: 2,
  c: 3,
  [Symbol.iterator]: function() { // 自定义对象的迭代器
    var keys = Object.keys(this)
    var index = 0
    return {
      next: () => (index < keys.length) ?
                    { done: false, value: this[keys[index++]] } :
                    { done: true, value: undefined }
    }
  }
}

[...obj] // [1, 2, 3]
```

### Generators

> GeneratorFunction 是一个可以作为迭代器工厂的特殊函数。当它被执行时会返回一个新的Generator对象。 如果使用function*语法，则函数将变为GeneratorFunction。

```js
function *main() {
  yield 1
  yield 2
  yield 3
  return 4
}

var it = main()

it.next() // {value: 1, done: false}
it.next() // {value: 2, done: false}
it.next() // {value: 3, done: false}
it.next() // {value: 4, done: true}

[...main()] // [1, 2, 3]
```

通过上面代码可以发现，在使用扩展运算符进行赋值时，return后的值并没有出现在数组中，所以使用generator时使用yeild赋值，return最好不要进行赋值操作。

使用generator给对象新进迭代代码如下：

```js
var obj = {
  a: 1,
  b: 2,
  c: 3,
  *[Symbol.iterator]() {
    for(let key of Object.keys(this)) {
      yield this[key]
    }
  }
}

[...obj] // [1, 2, 3]
```

## Regular Expressions(正则表达式)

### Look Ahead & Behind

[RegExp MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp)

```js
var msg = 'Hello World'

msg.match(/(l.)/g) // ["ll", "ld"]

msg.match(/(l.)$/g) // ["ld"]
//********************************
msg.match(/(l.)(?=o)/g) // ["ll"]

msg.match(/(l.)(?!o)/g) // ["lo", "ld"]
//*******************************
msg.match(/(?<=e)(l.)/g) // ["ll"]

msg.match(/(?<!e)(l.)/g) // ["lo", "ld"]
```

### Named Capture Groups

```js
var msg = 'Hello World'

msg.match(/.(l.)/)
// ["ell", "ll", index: 1, input: "Hello World", groups: undefined]
msg.match(/([jkl])o Wor\1/)
// ["lo Worl", "l", index: 3, input: "Hello World", groups: undefined]
msg.match(/(?<cap>l.)/).groups
// {cap: "ll"}
msg.replace(/(?<cap>l.)/g, '-$<cap>-')
// "He-ll-o Wor-ld-"
msg.replace(/(?<cap>l.)/g, function re(...args) {
  var [,,,,{cap}] = args
  return cap.toUpperCase()
})
// "HeLLo WorLD"
```

### dotAll Mode

> [dotAll 属性表明是否在正则表达式中一起使用"s"修饰符（引入/s修饰符，使得.可以匹配任意单个字符）。dotAll 是一个只读的属性，属于单个正则表达式实例。](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/RegExp/dotAll)

```js
var msg = `
The quick brown fox
jumps over the
lazy dog`

msg.match(/brown.*over/)
// null
msg.match(/brown.*over/s)
// ["brown fox↵jumps over", index: 11, input: "↵The quick brown fox↵jumps over the↵lazy dog", groups: undefined]
```

## Async Await

### Async Functions

#### promise chains: yunk

使用promise的回调函数写法：

```js
fetchCurrentUser()
  .then(function onUser(user) {
    return Promise.all([
      fetchArchivedOrders(user.id),
      fetchCurrentOrders(user.id)
    ])
  })
  .then(function onOrders(
    [archivedOrders, currentOrders]
  ) {
  // ......
  })
```

#### sync-async(with generators)

使用generator和iterator的写法:

```js
runner(function *main() {
  var user = yield fetchCurrentUser()

  var [archivedOrders, currentOrders] =
    yield Promise.all([
      fetchArchivedOrders(user.id),
      fetchCurrentOrders(user.id)
    ])
})
```

#### async functions

> [async function 声明用于定义一个返回 AsyncFunction 对象的异步函数。异步函数是指通过事件循环异步执行的函数，它会通过一个隐式的 Promise 返回其结果。但是如果你的代码使用了异步函数，它的语法和结构会更像是标准的同步函数。](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/async_function)

```js
async function mian() {
  var user = await fetchCurrentUser()

  var [archivedOrders, currentOrders] =
    await Promise.all([
      fetchArchivedOrders(user.id),
      fetchCurrentOrders(user.id)
    ])
  
  //.........
}

mian()
```

### Async Iteration

```js
async function fetchFiles(files) {
  var prs = files.map(getFile)

  prs.forEach(function each(pr) {
    console.log(await pr) // 这里会报错，await关键字只能在async函数中使用，不能再普通函数中使用
  })
}
```

#### library fasy: better async FP iterations

[getify/fasy](https://github.com/getify/fasy)

```js
async function fetchFiles(files) {
  var prs = await FA.concurrent.map(getFile, files)

  await FA.serial.forEach(async function each(pr) {
    console.log(await pr)
  }, prs)
}
```

### Async Function Problems

+ await Only Promises(await关键字只能处理Promise对象)
+ Scheduling (Starvation)(可能会造成微任务队列拥堵，产生无限循环问题)
+ External Cancelation(一旦开始就几乎不可能从外部取消)

#### library CAF: cancelable async functions

[etify/CAF](https://github.com/getify/caf)

```js
var token = new CAF.cancelToken()

var main = CAF(function *main(signal, url) {
  var resp = yield fetch(url, {signal})
  //....
  return resp
})

main(token.signal, 'http://some.tld/other')
  .then(onRestonse, onCancelOrError)

setTimeout(function onElapsed() { // 5秒后取消异步函数
  token.abort('Request took too long!')
}, 5000)
```

### Async Generators with yield

#### async* .. yield await

async all-at-once:

```js
async function fetchURLs(urls) {
  var results = []
  for (let url of urls) {
    let resp = await fetch(url)
    if (resp.status == 200) {
      let text = await resp.text()
      results.push(text.toUpperCase())
    } else {
      results.push(undefined)
    }
  }
  return results
}
```

overloaded yield:

```js
function *fetchURLs(urls) {
  for (let url of urls) {
    let resp = yield fetch(url)
    if (resp.status == 200) {
      let text = yield resp.text()
      yield text.toUpperCase()
    } else {
      yield undefined
    }
  }
}
```

async generators:

```js
async function *fetchURLs(urls) {
  for (let url of urls) {
    let resp = await fetch(url)
    if (resp.status == 200) {
      let text = await resp.text()
      yield text.toUpperCase()
    } else {
      yield undefined
    }
  }
}
```

### Async Generators Iteration

```js
async function main(favoriteSites) {
  var it = fetchURLs(favoriteSites)

  while(true) {
    let res = await it.next()
    if(res.done) break
    let text = res.value

    console.log(text)
  }
}
// *********************************
async function main(favoriteSites) {
  for await(let text of fetchURLs(favoriteSites)) {
    console.log(text)
  }
}
```
