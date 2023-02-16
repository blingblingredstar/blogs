---
title: Functional Light JavaScript v2
date: 2019-05-10 08:37:58
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, 闭包, 高阶函数, 函数式编程, Point-Free, 异步编程, Functional Programming]
---
## Functional Programming Introduction

### Functional Programming

[Functional-Light-JS](https://github.com/getify/Functional-Light-JS)

### Provable & Readable

函数式编程试图使用已经尝试过过、验证过多年的的模式来解决问题。函数式编程试图用已经验证过的数学问题来解决编程问题，以减少程序的不确定性，试图在编写代码的同时来进行测试。通过把复杂的编程问题拆解为简单的可验证的数学问题，来提高程序的可靠性及可读性（不用阅读每一段代码，只要确定每一个数学问题可靠，及理解每个数学问题的作用即可）。

一般我们使用单元测试及集成测试来测试程序的可靠性，但实际上，我们也可以使用经过严谨证明的数学公式来进行编程，因为这些数学公式进行过全面的分析论证，所以我们可以认为他们是可靠地，是“不需要”进行测试的，可以极大地简化我们的思维负担，并提高我们程序的可靠性。

LESS TO READ，最易读的代码是不需要进行分析的代码。

作为一名程序员，我们应该信任我们的代码。如果我们不信任我们的代码，那说明我们并没有理解我们代码的作用。函数式编程的目的就是尽量写出可信任的代码段，并将其组合在一起。
<!--more-->
### Pure Functions & Side Effects

纯粹的函数应该使用直接输入函数的参数和函数内部变量，而不是改变外部变量，并且直接输出一个返回值，而不是改变函数外部变量。

函数式编程的目的不是完全消灭副作用，事实上，这并不可能。函数式编程的目的是使副作用尽可能最小化，只在必要时使函数有副作用，这样，在程序出现问题时，我们可以首先集中精力排查函数副作用部分，而不是把我们的注意力放在已经严谨论证过的纯数学函数部分。

### Evolving Understanding of Impurity

当函数输入值固定时，输出值也是固定的，就可以称这个函数为纯函数。

```js
function foo(bar) {
  return function (x){
    return bar(x)
  }
}

foo(v => v * 2)(3)
```

```js
function getId({
  id
  } = {}) {
  return id
}
getId({ // 将此对象传入getId时，每次会得到一个随机数，这是因为传入对象的id属性的副作用
  get id() {
    return Math.random() // Math.random利用了副作用，既每次输出值是不同的
  }
})
```

## Managing Function Inputs

### Arguments(实参)

```js
// unary 一元函数
function increment(x) {
  return sum(x, 1)
}
// binary 二元函数
function sum(x, y) {
  return x + y
}
```

```js
function unary(fn) {
  return function one(arg) {
    return fn(arg)
  }
}

function binary(fn) {
  return function two(arg1, arg2) {
    return fn(arg1, arg2)
  }
}

function f(...args) {
  console.log(args)
}

var g = unary(f)
var h = binary(f)

g(1, 2, 3, 4) // [1]
h(1, 2, 3, 4) // [1, 2]
```

```js
function flip(fn) {
  return function fliped(arg1, arg2, ...args) {
    return fn(arg2, arg1, ...args)
  }
}

function f(...args) {
  console.log(args)
}

var g= flip(f)

g(1, 2, 3, 4) // [2, 1, 3, 4]
```

```js
function reverseArgs(fn) {
  return function reversed(...args) {
    return fn(...args.reverse())
  }
}

function f(...args) {
  console.log(args)
}

var g = reverseArgs(f)

g(1, 2, 3, 4) // [4, 3, 2, 1]
```

### No Points(point-free)

```js
function isOdd(v) {
  return v % 2 == 1
}

function isEven(v) {
  return !isOdd(v)
}

isEven(4) // true
/******** point free style *********/
function not(fn) {
  return function negated(...args) {
    return !fn(args)
  }
}

function isOdd(v) {
  return v % 2 == 1
}

var isEven = not(isOdd)

isEven(4) // true
```

## Composing Functions

### Compostion Introduction

```js
function sum(x, y) {
  return x + y
}
function mult(x, y) {
  return x * y
}
function pipe(fn1, fn2) {
  return function piped(arg1, arg2, arg3) {
    return fn2(fn1(arg1, arg2), arg3)
  }
}

var multAndSum = pipe(mult, sum)
// (3 * 4) + 5
multAndSum(3, 4, 5) // 17
```

composing的出发点就是利用高阶函数，将不同功能的简单函数组合起来，实现一个复杂功能的函数。

```js
foo(bar(baz(2)))

compose(foo, bar, baz)(2)

pipe(baz, bar, foo)(2)
```

composition与pipe功能上一样，传递作为参数的函数的执行顺序不一样

```js
function composeRight(fn2, fn1) {
  return function composed(...args) {
    return fn2(fn1(...args))
  }
}
//*************************************
function increment(x) {
  return x + 1
}
function double(x) {
  return x * 2
}

var f = composeRight(increment, double)
var p = composeRight(double, increment)

f(3) // 7(3 * 2 +1)
p(3) // 8((3 +1 ) * 2)
```

## Immutability

### Immutability Introduction

## Closure

### Closure & Side Effects

> Closure is when a function “remembers” its lexical scope even when the function is executed outside that lexical scope.

函数式编程建立在闭包这个特性之上。

> [闭包是函数和声明该函数的词法环境的组合。](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures)
> 闭包是由函数以及创建该函数的词法环境组合而成。这个环境包含了这个闭包创建时所能访问的所有局部变量。

## Partial Application

### Generalized to Specialized

```js
function add(x, y) {
  return x + y
}

function partial(fn, ...firstArgs) { //Partial Application
  return function applied(...lastArgs) {
    return fn(...firstArgs, ...lastArgs)
  }
}

var addTo10 = partial(add, 10)

addTo10(32) // 42
```

Partial Application和curring是两种可以特殊化广义功能(specialing a generalize function)的不同的技术。

Partial Application首先接收一些参数，使得广义函数特殊化，然后接收剩余所有参数完成广义函数的功能（接收两次参数）。

Curring可以分多次（一般一次一个）接收参数，最终完成广义函数的功能。

```js
var add3 = curry(function add3(x, y, z) {
  return x + y + z
})

var f = add3(3)

var p = f(4)

p(5) // 12

add3(3)(4)(5) // 12
```

## Recursion

### Recursion Introduction

for循环实现累加

```js
function sumInter(...nums) {
  var sum = 0
  for(let i = 0; i < nums.length; i++) {
    sum = sum + nums[i]
  }
  return sum
}

sumInter(3, 4, 5, 6, 7, 8, 9) // 42
```

```js
function sumInter(sum, ...nums) {
  for(let i = 0; i < nums.length; i++) {
    sum += nums[i]
  }
  return sum
}

sumInter(3, 4, 5, 6, 7, 8, 9) // 42
```

```js
function sumRecursion(sum, ...nums) {
  if (nums.length === 0) return sum
  return sum + sumRecursion(...nums)
}

sumRecursion(3, 4, 5, 6, 7, 8, 9) // 42
```

```js
function sumRecursion(sum, num, ...nums) {
  if (nums.length === 0) return sum + num
  return sum + sumRecursion(num, ...nums)
}

sumRecursion(3, 4, 5, 6, 7, 8, 9) // 42
```

### Proper Tail Calls

在传统的递归中，我们每次执行递归调用都会在调用栈上创建一个新的调用帧来存储当前的调用，当调用次数过多时，很明显会用尽内存，出现栈溢出的情况。为了解决这个问题，提出了可以在每次调用完当前帧时记录结果（可以将结果作为参数传递给下次执行的函数），将结果传入下一帧，推出当前帧，这样，就可以只用有限的内存来实行递归，这就是利用尾调用实现尾递归以节省内存。

尾递归优化只有在严格模式下才有效（严格模式下禁用了可以追踪函数调用栈的`arguments`和`caller`变量）。

```js
"use strict"

function foo(x) {
  if (x < 10) return x
  return bar(x)
}

function bar(x) {
  return x / 2
}

foo(42) // 21
```

```js
"use strict"

function foo(x) {
  if (x % 2 == 1) {
    x = Math.round(x / 3)
  } else {
    x = x / 2
  }

  if (x < 10) return x
  return foo(x)
}

foo(42) // 7
```

```js
"use strict"

function sumRecur(...nums) {
  return recur(...nums)

  function recur(sum, num, ...nums) {
    sum += num
    if (nums.length === 0) return sum
    return recur(sum, ...nums)
  }
}

sumRecur(3, 4, 5, 6, 7, 8, 9) // 42
```

```js
"use strict"

var sumRecur = (function() {
  return function(...nums) {
    return recur(...nums)
  }

  function recur(sum, num, ...nums) {
    sum += num
    if (nums.length === 0) return sum
    return recur(sum, ...nums)
  }
})()

sumRecur(3, 4, 5, 6, 7, 8, 9) // 42
```

```js
"use strict"

function sumRecur(sum, num, ...nums) {
  sum += num
  if (nums.length === 0) return sum
  return sumRecur(sum, ...nums)
}

function sumRecur(sum, num, ...nums) {
  if (num == undefined) return sum
  return sumRecur(sum + num, ...nums)
}

sumRecur(3, 4, 5, 6, 7, 8, 9) // 42
```

### Continuation Passing Style

```js
"use strict"

var sumRecur = (function(...nums) {
  return function(...nums) {
    return recur(nums, v => v)
  }

  function recur([sum, ...nums], cont) {
    if (nums.length == 0) return cont(sum)
    return recur(nums, function(v) {
      return cont(sum + v)
    })
  }
})()

sumRecur(3, 4, 5, 6, 7, 8, 9) // 42
```

### Trampolines

```js
var sumTrampolined =
      trampoline(function f(sum, num, ...nums) {
        sum += num
        if (nums.length === 0) return sum
        return function() {
          return f(sum, ...nums)
        }
      })

function trampoline(fn) {
  return function trampolined(...args) {
    var result = fn(...args)

    while (typeof result == 'function') {
      result = result()
    }

    return result
  }
}

sumTrampolined(3, 4, 5, 6, 7, 8, 9) // 42
```

## Data Structures

### List Transformations

#### Map: Transformation

函数式编程一个概念就是提升，既在不改变当前数据结构前提下改变每项的值；

使用for循环实现如下：

```js
function doubleIt(v) {
  return v * 2
}

function transform(arr, fn) { // for循环方案解决
  var list = []
  for (let i = 0; i < arr.length; i++){
    list[i] = fn(arr[i])
  }
  return list
}

transform([1, 2, 3, 4, 5], doubleIt) // [2, 4, 6, 8, 10]
```

使用map方法如下（此时可以称数组[1, 2, 3, 4, 5]为一个functor）：

```js
function doubleIt(v) {
  return v * 2
}

[1, 2, 3, 4, 5].map(doubleIt) // [2, 4, 6, 8, 10]
```

#### Filter: Exclusion

```js
function isOdd(v) { return v % 2 == 1 } // 谓词函数,返回布尔值

function exclude(arr, fn) {
  const list = []
  for(let i = 0; i < arr.length; i++) {
    if(fn(arr[i])) {
      list.push(arr[i])
    }
  }
  return list
}

exclude([1, 2, 3, 4, 5], isOdd) // [1, 3, 5]
```

```js
function isOdd(v) { return v % 2 == 1 } // 谓词函数,返回布尔值

[1, 2, 3, 4, 5].filter(isOdd) // [1, 3, 5]
```

#### Reduce: Combining

map和filter每次只能操作一个值，reduce每次同时操作两个值，并将两个值合并为一个值。

```js
function mult(x, y) { return x * y }

function combine(arr, fn, initial) {
  let result = initial
  for(let i = 0; i < arr.length; i++) {
    result = fn(result, arr[i])
  }
  return result
}

combine([1, 2, 3, 4, 5], mult, 1) // 120
```

reduce可以称作函数式编程中的瑞士军刀，因为reduce可以将两个值合并为一个值，所以可以扩展出许多用法。

```js
function acronym(str, word) {
  return str + word.charAt(0)
}

['Functional', 'Light', 'JavaScript', 'Stuff'].reduce(acronym, '') // "FLJS"
```

### Fusion

一下代码中，为了转换数组，使用了3次map方法，创建了3次新的数组：

```js
const addBy1 = v => v + 1
const mulBy2 = v => v * 2
const divBy3 = v => v / 3

var list = [2, 5, 8, 11, 14, 17, 20]

list
  .map(addBy1)
  .map(mulBy2)
  .map(divBy3)
// [2, 4, 6, 8, 10, 12, 14]
```

那么，有没有办法将3次转换合并为一次呢？

```js
const addBy1 = v => v + 1
const mulBy2 = v => v * 2
const divBy3 = v => v / 3

function composeRight(fn1, fn2) {
  return function(...args) {
    return fn1(fn2(...args))
  }
}

var list = [2, 5, 8, 11, 14, 17, 20]

list.map(
  [divBy3, mulBy2, addBy1].reduce(composeRight)
) // [2, 4, 6, 8, 10, 12, 14]
```

### Transducing

```js
const add1 = v => v + 1
const isOdd = v => v % 2 === 1
const sum = (total, v) => total + v

var list = [2, 5, 8, 11, 14, 17, 20]

list
  .map(add1)
  .filter(isOdd)
  .reduce(sum) // 48
```

```js
function mapWithReduce(arr, mappingFn) {
  return arr.reduce(function reducer(list, v) {
    list.push( mappingFn(v) )
    return list
  }, [])
}

function filterWithReduce(arr, predicateFn) {
  return arr.reduce(function reducer(list, v){
    if(predicateFn(v)) list.push(v)
    return list
  }, [])
}

var list = [2, 5, 8, 11, 14, 17, 20]

list = mapWithReduce(list, add1)
list = filterWithReduce(list, isOdd)
list.reduce(sum) // 48
```

```js
function mapReducer(mappingFn) {
  return function reducer(list, v) {
    list.push(mappingFn(v))
    return list
  }
}

function filterReducer(predicateFn) {
  return function reducer(list, v) {
    if(predicateFn(v)) list.push(v)
    return list
  }
}

var list = [2, 5, 8, 11, 14, 17, 20]

list
  .reduce(mapReducer(add1), [])
  .reduce(filterReducer(isOdd), [])
  .reduce(sum) // 48
```

```js
function listCombination(list, v) {
  list.push(v)
  return list
}

function mapReducer(mappingFn) {
  return function reducer(list, v) {
    return listCombination(list, mappingFn(v))
  }
}

function filterReducer(predicateFn) {
  return function reducer(list, v) {
    if(predicateFn(v)) return listCombination(list, v)
    return list
  }
}


list
  .reduce(mapReducer(add1), [])
  .reduce(filterReducer(isOdd), [])
  .reduce(sum) // 48
```

```js
function listCombination(list, v) {
  list.push(v)
  return list
}

function curry(fn) {
  return function(arg1) {
    return function (arg2) {
      return fn(arg1, arg2)
    }
  }
}

// curry = fn => arg1 => arg2 => fn(arg1, arg2)

var mapReducer = curry(function mapReducer(mappingFn, combineFn) {
  return function reducer(list, v) {
    return combineFn(list, mappingFn(v))
  }
})

var filterReducer = curry(function filterReducer(predicateFn, combineFn) {
  return function reducer(list, v) {
    if (predicateFn(v)) return combineFn(list, v)
    return list
  }
})

var list = [2, 5, 8, 11, 14, 17, 20]

list
  .reduce(mapReducer(add1)(listCombination), [])
  .reduce(filterReducer(isOdd)(listCombination), [])
  .reduce(sum) // 48
```

```js
function listCombination(list, v) {
  list.push(v)
  return list
}

const curry = fn => arg1 => arg2 => fn(arg1, arg2)

var mapReducer = curry(function mapReducer(mappingFn, combineFn){
  return function(list, v) {
    return combineFn(list, mappingFn(v))
  }
})

var filterReducer = curry(function filterReducer(predicateFn, combineFn) {
  return function reducer(list, v) {
    if(predicateFn(v)) return combineFn(list, v)
    return list
  }
})

function compose(fn1, fn2) {
  return function composed(...args) {
    return fn1(fn2(...args))
  }
}

var transducer = compose(mapReducer(add1), filterReducer(isOdd))

var list = [2, 5, 8, 11, 14, 17, 20]

list
  .reduce(transducer(listCombination), [])
  .reduce(sum) // 48
```

```js
function curry(fn) {
  return function(arg1) {
    return function(arg2) {
      return fn(arg1, arg2)
    }
  }
}

var mapReducer = curry(function mapReducer(mappingFn, combineFn) {
  return function reducer(list, v) {
    return combineFn(list, mappingFn(v))
  }
})

var filterReducer = curry(function filterReducer(predicateFn, combineFn) {
  return function reducer(list, v) {
    if(predicateFn(v)) return combineFn(list, v)
    return list
  }
})

function compose(fn1, fn2) {
  return function composed(...args) {
    return fn1(fn2(...args))
  }
}

var transducer = compose(mapReducer(add1), filterReducer(isOdd))

var list = [2, 5, 8, 11, 14, 17, 20]

list.reduce(transducer(sum), 0) // 48
```

```js
function transduce(transducer, combineFn, initialValue, list) {
  var reducer = transducer(combineFn)
  return list.reduce(reducer, initialValue)
}

var transducer = compose(mapReducer(add1), filterReducer(isOdd))

transduce(transducer, sum, 0, [2, 5, 8, 11, 14, 17, 20]) // 48
```

## Data Structure Operations

### Data Structure Operations Introduction

```js
function mapObj(mapperFn, obj) {
  var newObj = {}
  var keys = Object.keys(obj)
  for(let key of keys) {
    newObj[key] = mapperFn(obj[key])
  }
  return newObj
}

var obj = {
  a: 'hello',
  b: 'world'
}

mapObj(str => str.toUpperCase(), obj) // {a: "HELLO", b: "WORLD"}
```

## Functional Programming Utility

### FPO.js

[FPO (/ˈefpō/) is an FP Library for JavaScript. The main aesthetic difference is that the FPO.* core API methods are all styled to use named-arguments (object parameter destructuring) instead of individual positional arguments.](https://github.com/getify/FPO)

```js
// the classic/traditional method style
// (on the `FPO.std.*` namespace)
FPO.std.reduce(
    (acc,v) => acc + v,
    undefined,
    [3,7,9]
);  // 19

// FPO named-argument method style
FPO.reduce({
    arr: [3,7,9],
    fn: ({acc,v}) => acc + v
}); // 19
```

可见该库主要使用了对象解构赋值作为命名参数，优点为可忽视顺序既数量，保证属性名与定义时参数属性名一致即可，缺点同样为属性名必须一致，否则参数无效。

## Async Programming

### Lazy Arrays

```js
var a = [1, 2, 3]

var b = a.map(num => num * 2)

b // [2, 4, 6]
```

如上代码运行良好的原因之一是数组a已经存在于当前环境中，但是如果我们要对一个网络请求返回的数据进行操作呢？我们可以称上述代码为一个eager compute，通过函数式编程，我们可以实现lazy compute。

#### Functional Programming Over Time

```js
var a = [] // 初始化一个空数组

var b = mapLazy(a, num => num * 2) // 定义一个懒更新函数

a.push(1) // 更新数组

a[0] // 1
b[0] // 2

a.push(2) // 更新数组

a[1] // 2
b[1] // 4
```

```js
var a = new lazyArray()

setInterval(function everySecond() {
  a.push(Math.random())
}, 1000)

// **************************

var b = a.map(num => num * 2)

b.forEach(function onValue(v) {
  console.log(v)
})
```

**LazyArray == Observable**.

```js
var a = new Rx.Subject()

setInterval(function everySecond() {
  a.push(Math.random())
}, 1000)

// **************************

var b = a.map(num => num * 2)

b.subscribe(function onValue(v) {
  console.log(v)
})
```
