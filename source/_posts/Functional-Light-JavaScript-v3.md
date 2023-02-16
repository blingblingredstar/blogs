---
title: Functional Light JavaScript v3
date: 2019-09-08 09:01:32
tags: [FrontEndMasters, JavaScript, 闭包, 高阶函数, 函数式编程, Point-Free, 异步编程, Functional Programming]
---
## Introduction

[Functional-Light-JS](https://github.com/getify/Functional-Light-JS)

### Why Functional Programming

将所有代码更改为函数式并不现实。所以也没有必要在工作中全部使用函数式风格，函数式有其特有的特性，我们可以在工作中逐渐将代码替换为函数式风格来获得收益。
<!-- more -->
#### Imperative vs Declarative

基本上我们大多数人都是从命令式编程开始学习。命令式编程代表着代码主要关注如何去实现一个功能。例如求一组数字的和，使用命令式编程，首先会声明一个变量，然后使用循环遍历数组，将每个元素加到声明的变量之上。命令式编程有许多有点，但是在code review时，不得不阅读每一行代码来确认是否有bug，并且在阅读完每一行代码前不能确定代码所实现的功能。

声明式编程的关注点不在于如何做，而在于输出结果。

在写代码注释时，应该着重于为什么要这样做，而不是代码做了什么。例如i++，应该主要说明为什么加一。一个坏的代码注释不如没有注释。通过函数式编程，我们可以将我们的代码从命令式变成声明式，从而使意图更加明显。

### Functional Programming Journey

一般来说，FP（Functional Programming）的学习曲线是一个先升后降在逐步提升的曲线。在曲线底部就是绝大多数人放弃FP的时候。在学习过程中，我们要脱离舒适区，以函数式范式进行思考，才能不断进步。

### Code is Provable

函数式编程试图用已经尝试并验证过多年的数学问题来解决编程问题，以减少程序的不确定性，试图在编写代码的同时来进行测试。既把复杂的编程问题拆解为简单的可验证的数学问题，来提高程序的可靠性及可读性（不用阅读每一段代码，只要确定每一个数学问题可靠，及理解每个数学问题的作用即可）。

一般我们使用单元测试及集成测试来测试程序的可靠性，但实际上，我们也可以使用经过严谨证明的数学公式来进行编程，因为这些数学公式进行过全面的分析论证，所以我们可以认为他们是可靠地，是“不需要”进行测试的，可以极大地简化我们的思维负担，并提高我们程序的可靠性。

LESS TO READ，最易读的代码是不需要进行分析的代码。

作为一名程序员，我们应该信任我们的代码。如果我们不信任我们的代码，那说明我们并没有理解我们代码的作用。函数式编程的目的就是尽量写出可信任的代码段，并将其组合在一起。

### Course Overview

+ Functions
+ Closure
+ Composition
+ Immutability
+ Recursion
+ Lists/Data Structure
+ Async
+ FP library

## Function Purity

### Functions vs Procedures

Functional Programming is not all about the `function` keyword.

```js
function addNums(x, y, z, w) {
  const total = x + y + z + w
  console.log(total)
}

function extraNums(x = 2, ...args) {
  return addNums(x, 40, ...args)
}

extraNums() //42
extraNums(3, 8, 11) //62
```

在上述代码中，addNums并不是一个“function"，是一个”procedure”。procedure是一系列操作的集合。我们要编程，就要有“procedure”。“function”是“procedure”的子集，“function”不止接收参数，还有输出数据。如果一个使用`function`关键字的代码没有显式的`return`，那么基本上就不是一个函数。

因为addNums不是function，所以extraNums也不是function。

```js
function tuple(x, y) {
  return [x+1, y-1]
}

const [a, b] = tuple(...[5, 10]) // a:6 b:9
```

### Function Naming Semantics

就像数学中的函数一样，函数反映了输入值与输出值的关系。

Function: the semantic relationship between input and computed output.

### Side Effects

我们有时会在函数内引用函数作用域外的变量，那么这个函数就是有副作用的。纯函数应该只使用参数进行计算，并返回一个显式的计算后的数据。

在js中，函数定义可能并不重要，最重要的是函数调用。

+ Side Effects:
  + I/O(console, files, etc)
  + Data Storage
  + Network Calls
  + DOM
  + Timestamps
  + Random Numbers
  + CPU Heat
  + CPU Time Delay

可以看到无论是现实中还是理论上无法避免副作用。那么所谓的避免副作用其实更应该称为最小化副作用(minimize side effects)。

No such thing as "no side effects"

Avoid them where possible, make them obvious otherwise.

### Pure Functions & Constants

FP中的纯函数应该有直接的输入和输出，并且没有副作用。

```js
const z = 1

function addTwo(x, y) {
  return x + y
}

function addAnother(x, y) {
  return addTwo(x, y) + z
}

addAnother(20, 21) // 42
```

在FP的定义中，很明显addTwo是一个纯函数而addAnother并不是，但是在上面的情况下，z变量是一个常量，所以可以视为一个纯函数，这也正是作者一直强调的判断函数是否为纯函数更关键在于函数调用而不是定义。在定义阶段，addAnother是不纯的，引用了外部的z变量。在调用时，因为z变量是一个常量，相同的输入总会有相同的输出，所以addAnother可以视作纯函数。

但是如果我们使用var定义变量呢？在上述代码中并不会改变分析的结果，但是我们需要分析全部代码，来确认z并没有被重新分配给其他值，这明显增加了分析的复杂性，而减少分析的复杂性也正是FP所能带给我们的好处之一。

### Pure Functions & Side Effects

纯粹的函数应该使用直接输入函数的参数和函数内部变量，而不是改变外部变量，并且直接输出一个返回值，而不是改变函数外部变量。

函数式编程的目的不是完全消灭副作用，事实上，这并不可能。函数式编程的目的是使副作用尽可能最小化，只在必要时使函数有副作用，这样，在程序出现问题时，我们可以首先集中精力排查函数副作用部分，而不是把我们的注意力放在已经严谨论证过的纯数学函数部分。

### Reducing Surface Area

```js
function addAnother(z) {
  return function addTwo(x, y) {
    return x + y + z
  }
}

addAnother(1)(20, 21) // 42
```

通过这种写法，我们减少了所需要分析的代码。

### Same Input, Same Output

```js
function getId(obj) {
  return obj.id
}

getId({
  get id() {
    return Math.random()
  }
})
```

纯函数提供同一个输入值，应该有同一个输出值。在上面的代码中，getId就不是一个纯函数。

### Level of Confidence

函数的纯粹性有时并不仅仅用是或者否就可以断定，更加合适的形容是在当前情况下，对此函数的纯粹性的信心是多少。还是要注意，在说明信心多少时，应该是函数调用而不是函数定义。

### Extracting Impurity

我们是无法避免副作用的，例如向数据库写入数据。此时我们需要将不纯的部分提取出来组织到一起。也就是将纯函数与procedure分开组织。

### Containing Impurity

我们没办法彻底避免副作用，但是我们可以尽可能的减少副作用所带来的影响。

### Impurity Exercise: Wrappers & Adapters

```js
var students = [
  { id: 260, name: 'Kyle' },
  { id: 729, name: 'Susan' },
  { id: 42, name: 'Frank' },
  { id: 74, name: 'Jessica' },
  { id: 491, name: 'Ally' }
]

function getStudentsByName(students) {
  students = students.slice() // 将形参students的引用更改为实参的副本。
  return sortStudentsByName()

  function sortStudentsByName() {
    // Don't modify this function
    students.sort(function byName(s1, s2) {
      if (s1.name < s2.name) return -1
      else if (s1.name > s2.name) return 1
      else return 0
    })
    return students
  }
}

function sortStudentsByID() {
  // Don't modify this function
  students.sort(function byID(s1, s2) {
    return s1.id - s2.id
  })
  return students
}

function getStudentsByID(curStudents) {
  var origStudents = students.slice(0) // 保存原数据的拷贝的引用值
  students = curStudents // 更改全局的students为实参的引用值
  var newStudents = sortStudentsByID() // 将排序后的结果保存下来
  students = origStudents // 将全局students值更改为原数据的拷贝
  return newStudents // 返回排序后数据的引用值
}
```

## Arguments Apapters

### Function Arguments

parameter（形参）是函数定义时的变量，是一个占位符。arguments（实参）是函数调用时传入的变量，有具体的值。

如果函数只有一个输入值并只返回一个输出值，那么可以称这个函数为一元(unary)函数，如果有两个输入值，一个输出值，则是二元(binary)函数。

```js
// unary
function increment(x) {
  return sum(x + 1)
}

// binary
function sum(x, y) {
  return x + y
}
```

### Arguments Shape Adapters

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
  return args
}

var g = unary(f)
var h = binary(f)

g(1, 2, 3, 4) // [1]
h(1, 2, 3, 4) // [1, 2]
```

HOF(high order function)就是可以接受函数作为参数或者将函数作为返回值的函数。

### Flip & Reverse Adapter

```js
function flip(fn) {
  return function flipped(arg1, arg2, ...args) {
    return fn(arg2, arg1, ...args)
  }
}

function f(...args) {
  return args
}

var g = flip(f)

g(1, 2, 3, 4) // [2, 1, 3, 4]
```

```js
function reverseArgs(fn) {
  return function reversed(...args) {
    return fn(...args.reverse())
  }
}

function f(...args) {
  return args
}

var g = reverseArgs(f)

g(1, 2, 3, 4) // [4, 3, 2, 1]
```

### Spread Operator

```js
function spreadArgs(fn) {
  return function spread(args) {
    return fn(...args)
  }
}

function f(x, y, z, w) {
  return x + y + z + w
}

var g = spreadArgs(f)

g([1, 2, 3, 4]) // 10
```

## Point Free

### Equational Reasioning

在函数式编程中，绝大多数组件已经存在于各个库中，我们只需要将问题拆解，并将组价组合起来即可。

```js
getPerson(function onPerson(person) {
  return renderPerson(person)
})
// 可以写为如下
getPerson(renderPerson)
```

### Point Free Refactor

```js
function isOdd(v) {
  return  v % 2 == 1
}

function isEven(v) {
  return !isOdd(v)
}

isEven(4) // true
```

```js
function not(fn) {
  return function negated(...args) {
    return !fn(...args)
  }
}

function isOdd(v) {
  return v % 2 == 1
}

var isEven = not(isOdd)
isEven(4) //true
```

### Exercise: Point Free

```js
'use strict'

const output = console.log.bind(console)
// function output(txt) {
// console.log(txt)
// }

// when(output)(isShortEnough)(msg1)
// printIf     (isShortEnough)(msg1)
function when(fn) {
  return function(predicate) {
    return function(...args) {
      if (predicate(...args)) {
        return fn(...args)
      }
    }
  }
}
const printIf = when(output)
// function printIf(shouldPrintIt) {
//   return function(msg) {
//     if (shouldPrintIt(msg)) {
//       output(msg)
//     }
//   }
// }

function isShortEnough(str) {
  return str.length <= 5
}

const not = (fn) => {
  return function negated(...args) {
    return !fn(args)
  }
}

const isLongEnough = not(!isShortEnough)
// function isLongEnough(str) {
//   return !isShortEnough(str)
// }

var msg1 = 'Hello'
var msg2 = msg1 + ' World'

printIf(isShortEnough)(msg1) // Hello
printIf(isShortEnough)(msg2)
printIf(isLongEnough)(msg1)
printIf(isLongEnough)(msg2) // Hello World
```

### Advanced Point Free

```js
function mod(y) {
  return function for(x) {
    return x % y
  }
}

function eq(y) {
  return function for(x) {
    return x === y
  }
}

const mod2 = mod(2)
const eq1 = eq(1)

function isOdd(x) {
  return eq1( mod2( x ) )
}
```

FP的使用者的关注点集中在函数的形状上。这也就意味着不止要关注函数所实际接收参数的具体数据，参数传入的顺序也很重要。

在上面代码中，我们把`mod2(x)`的返回值立即传给了`eq1`作为参数，这在函数式中叫做compose（组合），把这个操作抽象化就是如下代码

```js
function compose(fn2, fn1) {
  return function composed(value) {
    return fn2( fn1( value ) )
  }
}

// const isOdd = compose(eq1, mod2)
const isOdd = compose(eq(1), mod(2))
```

## Closure

Closure is when a function "remembers" the variables around it even when that function is executed elsewhere.

```js
function makeCounter() {
  let counter = 0
  return function increment() {
    counter++
    return counter
  }
}

const c = makeCounter()
c() // 1
c() // 2
```

### Exercise: Closure

```js
'use strict'

function strBuilder(str) {
  return function next(value) {
    if (typeof value == 'string') {
      return strBuilder(str + value)
    }
    return str
  }
}

var hello = strBuilder('Hello, ')
var kyle = hello('Kyle')
var susan = hello('Susan')
var question = kyle('?')()
var greeting = susan('!')()
```

### Lazy vs. Eager Execution

```js
function repeater(count) {
  return function allTheAs() {
    return ''.padStart(count, 'A')
  }
}

const A = repeater(10)

A() // AAAAAAAAAA
A() // AAAAAAAAAA
```

在上面的代码中，究竟是在调用`repeater`时还是`A`时创建了字符串`AAAAAAAAAA`？

很显然，是在调用`A`时，并且，我们可以多次调用来创建多次。

这就是lazy或者defer。我们将需要延迟执行的代码放入一个函数中，并延迟函数执行，直到需要执行时。

那么我们为什么要这么做，为什么要延迟函数执行？

假如我们有多个计算量很大的函数，但是需要协同进行才能获得最终结果，那么显然我们希望函数能够按照一定顺序执行。

但是像上面的代码，我们每执行一次就要创建一个新的字符串，在计算量较大的情况时很明显会浪费资源。

此时，我们可以使用类似以下的代码

```js
function repeater(count) {
  const str = ''.padStart(count, 'A')
  return function allTheAs() {
    return str
  }
}

const A = repeater(10)

A() // 'AAAAAAAAAA'
A() // 'AAAAAAAAAA'
```

在上面的代码中，我们在调用`repeater`时就创建了字符串，在调用`A`时返回创建好的字符串，这就是一个eager。

eager的好处在于只创建了一个字符串，当我们调用函数多次，也只共享一个值，但假如我们之后没有调用`A`，eager还是创建了一个字符串。

所以这就是lazy和eager的优劣之处。

lazy只在最终调用时创建实例，但是每次调用都创建新的实例。

eager在第一次调用时就创建实例，但是多次调用也只使用一个实例。

以上两种功能的实现都依托了闭包。

在lazy中，我们使用闭包保存了第一次调用时传入的参数，eager中，我们使用闭包保存了产生的字符串。

### Memoization

在lazy和eager中，各有优劣。那么有没有什么办法能够两全其美呢？

我们想要只做一次事情，并且只在需要的时候做一次。

我们可以写出如下代码

```js
function repeater(count) {
  let str
  return function allTheAs() {
    if(str == undefined) {
      str = ''.padStart(count, 'A')
    }
    return str
  }
}

const A = repeater(10)

A() // 'AAAAAAAAAA'
A() // 'AAAAAAAAAA'
```

我们在返回的函数中加入了判断条件，来判定是否创建过字符串。此时我们只在第一次调用`A`时创建了字符串，多次调用返回的是同一个字符串。

但是上面的函数是一个纯函数吗？很明显我们重新赋值了`str`变量，所以不是一个纯函数。

但是明显我们给定一个同样的参数，返回的一直是同样的结果，这符合纯函数的定义。

作为函数式编程的使用者，我们希望使用纯的函数式代码，但是假如代码可读性和可靠性变差，那么就违背了我们使用函数式的初衷。

在上面的代码中，我们违背了函数式原则，重新赋值了变量，但是在性能表现上，明显上面的代码要优于之前的纯函数式代码。

那么，有没有什么办法将上面的代码更符合函数式呢？

在函数式中，我们使用纯函数，既同样的输入只会有同样的输出，那么，有没有一种工具能使我们只输入一次变量，并只计算一次结果，并将结果缓存起来，每次只返回缓存的结果呢？

这就是`memoize`

```js
function repeater(count) {
  return memoize(function allTheAs() {
    return ''.padStart(count, 'A')
  })
}

const A = repeater(10)

A() // 'AAAAAAAAAA'
A() // 'AAAAAAAAAA'
```

这次我们调用了一个函数式工具`memoize`，这个工具包裹一个lazy，在`memoize`内部会将参数等变量保存在缓存中，一旦使用就调用缓存。这样使用`memoize`包裹lazy之后，明显符合函数式的定义。

`memoize`明显是个不错的工具，但是也会带来额外的性能开销。例如当每次输入值不同时，内存中会保存多个输入值，此时单独使用lazy更加合适。

### Referential Transparency

之前我们逐步探索了什么是纯函数，在这里，我们增加了一条，如果我们可以使用一个函数调用后的返回值代替一个值，那么这就是一个纯函数。更具体一些，像上面的代码，我们可以使用`A()`来代替字符串'AAAAAAAAAA'，所以可以称`repeater`为纯函数。

这个现象有个术语，叫做referential transparency(引用透明性)。

如果一个函数有referential transparency(引用透明性)就意味着这是一个纯函数。referential transparency意味着一个函数调用后的返回值可以用一个值来代替并且不会影响程序中的其他部分。

referential transparency在函数式编程语言中是一个核心概念。函数式语言利用同样的输入总会有同样输出这个特性来在编译时对性能进行优化。但是这并不仅仅可以帮助编译器，我们阅读代码时可以利用这一点，如果我们确认一个函数是纯函数，拥有referential transparency，那么每次调用时我们就可以直接将函数作为一个具体值来看待，而不用重新查看函数定义部分，这样我们就可以将关注点集中在其他更加重要的地方。

### Generalized to Specialized

```js
function ajax(url, data, cb) {
  /* do something*/
}

ajax(CUSTOMER_API, {id: 42}, renderCustomer)
```

我们可能会写出上面的代码，但这里要指出的是在`ajax`中，我们会有许多逻辑。在我们书写代码时，最好能将代码的功能更加具体，我们可以书写如下代码

```js
function getCustomer(data, cb) {
  return ajax(CUSTOMER_API, data, cb)
}

getCustomer({id: 42}, renderCustomer)
```

在上面我们使用了闭包来分为两次传入`ajax`所需的全部参数，更加具体的指明了函数的语义。

有可能我们只会调用上面的函数一次，在DRY原则中，会强调将多次重复的逻辑封装为函数。但为了未来阅读代码时所带来的心智负担，上面的操作也是有益处的。但这里也提出了一个将代码封装为函数的理由，就是能让代码更加语义化，函数的名称就可以标明代码的作用，代码的作用才是阅读者需要理解的部分。

所以我们可以将上面的代码更加语义化

```js
function getCurrentUser(cb) {
  return getCustomer({id: 42}, cb)
}

getCurrentUser(renderCustomer)
```

在上面的过程中，我们逐步将代码更加语义化，很明显最开始我们只有一行代码来实现功能，但也很明显最后的代码更加能够说明代码的作用。

我们并没有改变函数的功能，我们只是改变了编写代码的风格来使函数功能更加语义化。

在上面的代码中，从`ajax`到`getCustomer`再到`getCurrentUser`是一个specialized的过程，反之则是generalized的过程。

就像之前所说，在point free风格中，参数的顺序很重要，这里也一样。

一般而言，从第一个参数到最后一个参数的顺序应为从更广泛到更具体。

因为我们想要逐步具体化函数的功能，那么我们就要按照顺序传入参数。如果第一个参数是最为具体的参数，那么传参将会比较麻烦。

例如`map`方法，一般会将数组作为第一个参数，将`projection`函数作为第二个参数。但是函数式编程库会将`projection`作为第一个参数，将数组作为最后一个参数传入。因为在函数式编程中看来，数组也就是具体的数据是更为具体的参数，需要最后一个再传入。

### Partial Application & Currying

在上面的代码中，有一些术语来使功能更加具体，其中之一是partial application。

```js
function ajax(url, data, cb) {
  /* do something*/
}

const getCustomer = partial(ajax, CUSTOMER_API)
const getCurrentUser = partial(getCustomer, {id: 42})
getCustomer({id: 42}, renderCustomer)
getCurrentUser(renderCustomer)
```

使用partial是使函数更加具体的方法之一，另一个在函数式编程中更加常见的方法就是currying。

```js
// const ajax = (url) => (data) => (cb) => {/* do something */}
function ajax(url) {
  return function getData(data) {
    return function getCb(cb) {
      // do something
    }
  }
}

ajax(CUSTOMER_API)({id: 42})(renderCustomer)

const getCustomer = ajax(CUSTOMER_API)
const getCurretnUser = getCustomer({id: 42})
getCurrentUser(renderCostomer)
```

在上面的代码，我们手动的将`ajax`柯里化，来分次调用`ajax`实现specialized。在函数式中，我们使用curry工具来帮助我么做到分次给一个函数传参。

```js
const ajax = curry(
  3,
  function ajax(url, data, cb) {
  /* do something*/
  }
)

const getCustomer = ajax(CUSTOMER_API)
const getCurretnUser = getCustomer({id: 42})
getCurrentUser(renderCostomer)
```

### Partial Application & Currying Comparison

可以看到，在partial application中我们定义了多个函数来实现功能的语义化，但在currying中，我们只调用了一次curry函数，并将ajax函数的参数分别传入到不同变量中来实现语义化。

基本所有的函数式编程库都有curry，并且所有函数默认都进行了柯里化，因为函数式编者关注函数的形状，并且希望所有函数都是一元而不是多元的。

Partial Application vs Currying:

1. Both are specialization techniques
2. Partial Application presets some arguments
now, receives the rest on the next call
3. Currying doesn't preset any arguments,
receives each argument one at a time

### Changing Function Shape with Curry

```js
const add = (x, y) => x + y

[0, 2, 4, 6, 8].map(function add1(v) {
  return add(1, v)
})
// [ 1, 3, 5, 7, 9 ]
```

在上面的代码中，add是一个二元函数，而map的回调是一个一元函数，所以我们使用了一个包裹函数改变了函数的形状。那么我们可以使用curry来改变函数形状，达到同样的目的。

```js
const add = (x, y) => x + y
const curriedAdd = curry(add)

[0, 2, 4, 6, 8].map(curriedAdd(1))
// [ 1, 3, 5, 7, 9 ]
```

## Composition

### Composition Illustration

```js
function minus2(x) { return x - 2 }
function triple(x) { return x * 3 }
function increment(x) { return x + 1 }

let tmp = increment(4)
tmp = triple(tmp)
const totalCost = basePrice + minus2(tmp)
```

上面我们实现了一个计算价格的代码，可以分为两个部分，一部分是计算税率，另一部分就是把税率与价格相加，我们通过一个tmp变量作为中间值来分次计算税率，那么是否可以不使用临时变量直接计算？代码如下

```js
const totalCost = basePrice + minus2(triple(increment(4)))
```

上面代码中计算税率部分就是组合(composition)，我们通过将一个函数的计算结果传入另一个函数来实现组合。

但是上面的代码看起来似乎不太好理解，我们可以进一步语义化为

```js
function shippingRate(x) {
  return minus2(triple(increment(x)))
}

const totalCost = basePrice + shippingRate(4)
```

### Declarative Data Flow

我们可以将上面计算税率的函数提取出来作为一个组合三个一元函数并计算结果的函数，代码如下

```js
function composeThree(fn3, fn2, fn1) {
  return function composed(v) {
    return fn3(fn2(fn1(v)))
  }
}
```

使用此工具函数，我们可以将计算税率的代码改造如下

```js
function minus2(x) { return x - 2 }
function triple(x) { return x * 3 }
function increment(x) { return x + 1 }

const shippingRate = composedThree(minus2, triple, increment)

const totolCost = basePrice + shippingRate(4)
```

在上面的计算税率的函数中，数据从`shippingRate`的右边流向左边，最终计算出结果。

我们编程时，其实就是计算数据的过程，我们向程序输入数据，并最终返回计算好的数据。通过函数式编程，我们可以让数据的流动更加声明式，更加语义化。

### Piping vs Composition

在上面的代码中，composedThree是一个高阶函数，既将函数作为参数并返回一个新的函数，我们可以将不同的三个一元函数组合在一起，并将不同的数字作为参数，返回不同结果。

那么假如我们有一个pipe版本的pipeThree呢？

在上面的composedThree中，我们的数据流向为从右到左，pipe将为从左到右。

### Exercise: Piping & Composition

```js
function pipe(...fns) {
  return piped(v) {
    for(let fn of fns) {
      v = fn(v)
    }
    return v
  }
}

function compose(...fns) {
  return pipe(...fns.reverse())
}
```

```js
function compose(...fns) {
  return function composed(v) {
    for(let i = fns.length - 1; i >= 0; i--) {
      v = fns[i](v)
    }
    return v
  }
}
function pipe(...fns) {
  return compose(...fns.reverse())
}
```

### Associativity

我们都学过数学的结合律，既操作符相同，变量的顺序不同也会有同样的结果(1 + 2 = 2 + 1)。结合律在composition也适用，那么我们就可以根据结合律，通过柯里化和组合来特殊化函数。

### Composition with Currying

```js
function sum(x, y) { return x + y }
function triple(x) { return x * 3 }
function divBy(y, x) { return x / y }

divBy(2, triple(sum(3, 5)))
```

```js
sum = curry(2, sum)
divBy = curry(2,divBy)

composeThree(
  divBy(2),
  triple,
  sum(3)
)(5)
```

## Immutability

不变性(immutability)不仅仅指值得不变化，而是指值不会在不预期的情况下改变。因为在我们的程序中，我们做的就是状态的不停改变，最后返回一个最终的状态。在编程中的不变性意味着只有在我们需要时才会改变状态，而不是在没有预期的情况下改变。这也就意味着我们是如何管理我们的状态，如何对控制状态的变异(mutation)。

这里我们需关注两种不变性：

第一种是赋值的不变性，含义是如果我们将一个值赋给一个变量，那么这个变量就不应被重新赋值。

```js
var basePrice = 100
const shippingCost = 6.50

basePrice += 5 // allowed
shippingCost *= 1.1 // not allowed
```

这里强调下，在js中，我们可以直接改变一个字符串的一个字符，这是允许的，但是如果将一个新的字符串赋给一个变量就是另一回事了。

在上面的代码中，我们将新的数字赋给了变量，根据js的语法规则，使用`var`定义的变量是可以重新赋值的，使用`const`是不可以的。

函数式编程如果改变税率和价格，可能会写出如下代码

```js
var basePrice = 100
const shippingCost = 6.50

function increasePrice(price) {
  return price + 5
}
increasePrice(basePrice)

function increaseShipping(shipping) {
  return shipping * 1.05
}

increaseShipping(shippingCost)
```

### Rethinking const Immutability

`const`关键字定义的变量是不能重新赋值的，但是如果变量本身可以变异，例如对象属性值得改变，或者给对象添加方法，是可以的。

所以与其关心变量的不变性，我应该将更多的注意力集中在值的不变性上。

### Value Immutability

我们绝大多数问题可能来自于程序中的某个值意料外的改变，例如全局对象的某个方法被重新赋值等。

### Object.freeze

很多情况下，当我们需要一个不可数据结构时，我们需要的是一个只读的数据结构，我们可以通过`Object.freeze()`来实现只读。

### Don't Mutate, Copy

Read-Only Data Structures: Data structrues that **never** need to be mutated

但是如果我们的所有值都是只读，那么许多功能也就无从实现，例如

```js
function processOrder(order) {
  if(!("status" in order)) {
    order.status = "complete"
  }
  saveToDatabase(order)
}
```

但是为了减少编译变异，也就是绝大多数bug产生生的原因，我们可以假设所有值都是只读的，那么上面的代码我们可以改写如下

```js
function processOrder(order) {
  const processedOrder = {...order}
  if(!("status" in order)) {
    processedOrder.status = "complete"
  }
  saveToDatabase(processedOrder)
}
```

### Immutable Data Structure

Treat all data structures as read-only whether they are or not

但我们需要一个不变的数据结构时，我们需要的其实是一个可变的数据结构，这个数据结构的值默认是不可变的，只有在你使用特定操作时，会创建一个新的数据结构，来代替之前的数据结构。

### Immutable.js Overview

[immutable.js](https://immutable-js.github.io/immutable-js/)

[mori](https://swannodette.github.io/mori/)

```js
const items = Immutable.List.of(
  textbook,
  supplies
)

const updatedItems = items.push(calculator)

items === updatedItems // false
items.size // 2
updatedItems.size // 3
```

### Exercise: Immutability

```js
'use strict'

function lotteryNum() {
  return (Math.round(Math.random() * 100) % 58) + 1
}

function recordNumber(num, numbers) {
  if (!numbers.includes(num)) {
    numbers = [...numbers, num]
    numbers.sort(function asc(x, y) {
      return x - y
    })
  }
  return numbers
}

var luckyLotteryNumbers = []

const NUM_COUNT = 6

while (luckyLotteryNumbers.length < NUM_COUNT) {
  puckyLotteryNumbers = recordNumber(
    lotteryNum(),
    Object.freeze(luckyLotteryNumbers)
  )
}

console.log(luckyLotteryNumbers)
```

## Recursion

```js
function isVowel(char) {
  return ['a', 'e', 'i', 'o', 'u'].includes(char)
}

function countVowels(str) {
  var count = 0
  for(let i = 0; i < str.length; i++) {
    if(isVowel(str[i])) {
      count++
    }
  }
  return count
}

countVowels('The quick brown fox jumps over the lazy dog')
```

```js
function countVowels(str) {
  if(str.length === 0) return 0
  var first = (isVowel(str[0] ? 1 : 0))
  return first + countVowel(str.slice(1))
}
```

### Base Condition Location

```js
function countVowels(str) {
  const first = (isVowel(str[0]) ? 1 : 0)
  if(str.length <= 1) return first
  return first + countVowels(str.slice(1))
}
```

### Exercise: Recursion

```js
function isPalindrome(str) {
  if(str.length < 2) return true
  const first = str[0]
  const last = str[str.length -1]
  if (first === last) {
    return isPlaindrome(str.substring(1, str.length - 1))
  }
  return false
}
```

### Stack Frames & Memory Limits

每次函数执行时，就会开辟一块内存保存函数的执行状态，这就是stack frame。之所以叫stack frame而不是memory frame是因为函数在调用栈中执行，每执行一次函数就会在调用栈顶端推入一次。

### Optimization: Tail Calls

在上面的代码中，我们每次调用函数都返回了`first`变量，然后调用自身计算新的结果，这就需要调用栈保存此次执行结果来记录`first`。

但是如果我们在返回值中只调用函数自身，那么似乎就不需要保存此次的函数执行状态了，因为在当前函数执行上下文我们已经做完了所有工作，那么可不可以直接将这个执行上下文弹出调用栈，然后在调用自身呢？这样我们就可以在调用栈中只保留一个函数执行上下文，避免栈溢出。

### Proper Tail Calls

PTC(Proper Tail Calls)是关于内存使用优化的，将递归的空间复杂度从O(n)降为O(1)。

TCO(Tail Call Optimization)是建立在众多优化手段之上的(包括PTC)的引擎优化措施。

```js
"use strict"

function decrement(x) {
  return sub(x, 1)
}

function sub(x, y) {
  return x - y
}

decrement(43) // 42
```

PTC只有在严格模式下才生效，必须有`return`关键字，并且返回一个尾递归的函数调用，或者一个二元表达式。

```js
"use strict"

function diminish(x) {
  if(x > 90) {
    return diminish(Math.trunc(x / 2))
  }
  return x - 3
}

diminish(367) // 42
```

### Refactoring to PTC Form

```js
"use strict"

function countVowels(count, str) {
  count += (isVowel(str[0]) ? 1 : 0)
  if (str.length <= 1) return count
  return countVowels(count, str.slice(1))
}

countVowels(
  0,
  "The quick brown fox jumps over the lazy dog"
)
```

在之前我们通过当前执行上下文的`first`变量来记录此次执行的结果，为了使用PTC，我们可以将结果直接保存在参数中，将结果作为参数传入下一次函数执行上下文，这样就没有必要保存此次执行结果。

但是我们需要在调用时传入一个初始参数`0`，这在有些情况下看起来可能不太好理解，为了简化使用时的负担，我们可以使用curry改写如下

```js
"use strict"

const countVowels = curry(2, function countVowels(count, str) {
  count += (isVowel(str[0]) ? 1 : 0)
  if (str.length <= 1) return count
  return countVowels(count, str.slice(1))
})(0)

countVowels(
  "The quick brown fox jumps over the lazy dog"
)
```

### Continuation-Passing Style

因为尾递归在浏览器的优化暂时还没有普遍实现，这里提供了两个其他方法来优化递归。第一个只需要理解，第二个为推荐方法。

第一个就是CPS(Continuation Passing Style)。

```js
"use strict"

function countVowels(str, cont = v => v) {
  const first = (isVowel(str[0]) ? 1 : 0)
  if(str.length <= 1) return cont(first)
  return countVowels(str.slice(1), function f(v) {
    return cont(first + v)
  })
}

countVowels(
  "The quick brown fox jumps over the lazy dog"
)
```

### Trampolines

```js
function trampoline(fn) {
  return function trampolined(...args) {
    const result = fn(...args)
    while(typeof result == 'function') {
      result = result()
    }
    return result
  }
}
```

```js
let countVowels =
  trampoline(function countVowels(count, str) {
    count += (isVowel(str[0]) ? 1 : 0)
    if(str.length <= 1) return count
    return function f() {
      return countVowels(count, str.slice(1))
    }
  })

countVowels = curry(2, countVowels)(0)
```

在上面的代码中，我们将`countVowels`内通过条件判断返回结果或者一个包裹函数。使用包裹函数的目的是通过闭包记录上次结果。并将`countVowels`传入蹦床函数，这样在蹦床函数内，我们判断`countVowels`返回的是函数还是结果，如果是结果就直接返回，如果是函数就调用函数。通过这个方法，我们绕过了函数调用自身，而是通过闭包来记录上次结果，并调用包裹函数，此时调用栈会将此次执行上下文推出调用栈，然后调用蹦床函数，蹦床函数判断返回结果，调用包裹函数。包裹函数在调用`countVowels`。公国这样来将空间复杂度从O(n)变为O(1)。

## List Operations

### Map: Transfromation

函数式编程中，map和filter等操作并不仅仅局限于array。在函数式编程中，这些是通用操作，可以用来操作任何一种数据结构。

在函数式编程中，functor是一个值(可以是一个多个值得集合，例如对象和数组)，这个值或者这个值里面的所有值可以被map(或者可以说是一个有map方法的数据结构)。

那么什么是map？

基本上是一个转换操作。也就是可以将数据集合中的每一个值转化为另一个值得一种操作。当然数据集合中并不一定要有多个值，即使只有一个值，也可以有map方法，那么也就是一个functor。

当然在函数式编程中我们要使用纯函数，也就是不改变输入值而产生一个新的值，所以map方法并不会直接操作原来的数据结构，而是返回一个转换后的新的数据结构(数据结构与原来相同，既之前是数组返回数组，之前是对象返回对象)。

```js
function makeRecord(name) {
  return { id: uniqID(), name }
}

function map(mapper, arr) {
  const newList = []
  for (let elem of arr) {
    newList.push(mapper(elem))
  }
  return newList
}

map(makeRecord, ["kyle", "Suzy"])
```

使用ES内置方法，为如下

```js
function makeRecord(name) {
  return { id: uniqID(), name }
}

['Kyle', 'Suzy'].map(makeRecord)
```

### Filter: inclusion

在编程中，filter代表了过滤一个数据结构并返回一个由所有符合条件的元素组成的新的数据结构。

```js
function isLoggedIn(user) {
  return user.session != null
}

function filterIn(predicate, arr) {
  const newList = []
  for (let elem of arr) {
    if (predicate(elem)) {
      newList.push(elem)
    }
  }
  return newList
}

filterIn(isLoggedIn, [
  { userID: 42, session: 'lkjsldfjlkjsdf'},
  { userID: 13 },
  { userID: 12, session: 'ldasdasdasdsdf'}
])
```

使用原生的数组的filter方法如下

```js
function isLoggedIn(user) {
  return user.session != null
}

[
  { userID: 42, session: 'lkjsldfjlkjsdf'},
  { userID: 13 },
  { userID: 12, session: 'ldasdasdasdsdf'}
].filter(isLoggedIn)
```

### Reduce: Combination

map方法转换数据结构中的值，filter过滤值，reduce结合值。

在map和filter中，我们单独操作每一个值，所以我们可以使用多个线程来分别处理每一个值而不互相影响。但是reduce并不可以这样，因为reduce作用于多个值。

```js
function addToRecord(record, [key, value]) {
  return {...record, [key]: value}
}

function reduce(reducer, initialVal, arr) {
  const ret = initialVal
  for(let elem of arr) {
    ret = reducer(ret, elem)
  }
  return ret
}

reduce(addToRecord, {}, [
  ['name', 'Kyle'],
  ['age', 39],
  ['isTeacher', true]
])
```

使用原生数组reduce方法如下

```js
function addToRecord(record, [key, value]) {
  return {...record, [key]: value}
}

[
  ['name', 'Kyle'],
  ['age', 39],
  ['isTeacher', true]
].reduce(addToRecord, {})
```

在map和filer的参数函数中(projection和predicate)，我们只需要一个参数，既数据结构当前值；在reducer中，我们需要两个值，accumulate和current。所以map和filter需要一个一元函数作为参数，reduce需要一个二元函数作为参数。换句话说，任何一个二元函数，并且只返回一个参数，都有可能作为reducer。

### Composition with Reduce

reduce从左到右处理数据集合中的值，那么reduceRight就是从右到左处理。之前我们提到pipe和compose也是类似，同样，compose也是一个二元函数并且只返回一个值，那么，compose也可以作为reducer。

```js
const add1 = v => v + 1
const mul2 = v => v * 2
const div3 = v => v / 3

function composeTwo(fn2, fn1) {
  return function composed(v) {
    return fn2(fn1(v))
  }
}

const f = [div3, mul2, add1].reduce(composeTwo)
const p = [add1, mul2, div3].reduceRight(composeTwo)

f(8) // 6
p(8) // 6
```

```js
function compose(...fns) {
  return function composed(v) {
    return fns.reduceRight(function invoke(fn, val) {
      return fn(val)
    }, v)
  }
}

const f = compose(div3, mul2, add1)

f(8) // 6
```

### Exercise: List Operation

```js
'use strict';

// Put your code here! :)
const num1 = () => 1;
const num2 = () => 2;

const add = (x, y) => x + y;
const result1 = add(num1(), num2());
console.log(result1); // 3

const add2 = (fn1, fn2) => add(fn1(), fn2());
const result2 = add2(num1, num2);
console.log(result2); // 3

const constant = (v) => () => v;
const five = constant(5);
const nine = constant(9);
// add2(five, nine)

// 循环实现
// function addn(...fns) {
//   while (fns.length > 2) {
//     const [first, second, ...rest] = fns
//     fns = [
//       function f() {
//         return add2(first, second)
//       },
//       ...rest
//     ]
//   }
//   return add2(fns[0], fns[1])
// }

// 递归实现
// function addn([first, second, ...rest]) {
//   if (rest.length === 0) return add2(first, second)
//   return addn([() => add2(first, second), ...rest])
// }

// reduce实现
function addn(fns) {
  return fns.reduce(function reducer(composedFn, fn) {
    return () => add2(composedFn, fn);
  })();
}
addn([constant(1), constant(3), five, nine]); // 18

const numbers = [1, 2, 5, 2, 1, 5, 16, 9];

addn(
  numbers
    .reduce(function unique(acc, curr) {
      if (!acc.includes(curr)) {
        return [...acc, curr];
      } else {
        return acc;
      }
    }, [])
    .filter(function isEven(v) {
      return v % 2 === 0;
    })
    .map(constant)
);
```

### Fusion

```js
const add1 = v => v + 1
const mul2 = v => v * 2
const div3 = v => v / 3

const list = [2, 5, 8, 11, 14]

list
  .map(add1)
  .map(mul2)
  .map(div3)
// [2, 4, 6, 8, 10]
```

我们有时为了方便会在map方法中使用不属于projection中的变量，这违背了函数式编程的原则，也就无法拥有函数式编程的益处。

在上面的代码中，我们使用了三次map，这明显会带来性能上的消耗。通过之前的课程，我们知道可以将`add1`，`mul2`，`div3`结合在一起，代码如下

```js
list
  .map(
    compose(div3, mul2, add1)
  )
// [2, 4, 6, 8, 10]
```

如过我们视觉化上面的代码，将list视为一个流，list中的每一个值首先流向add1，然后是mul2，最后是div3。

在函数式编程库中，并不使用js内置的map等方法，因为原生方法将数组作为隐式参数传入了map等方法。这违背了函数式编程原则，使得map等方法无法进行组合。所以一般的函数式编程库中都将map等方法提取出来，并将数据作为显式的参数进行传入，以便进行组合。

### Transduction

上面我们顺利的将三个map方法组合在一起，但是我们能不能将filter和reducer也组合在一起呢？这就是transducing。

```js
function add1(v) { return v + 1 }
function isOdd(v) { return v % 2 === 1 }
function sum(total, v) { return total + v }

const list = [1, 3, 4, 6, 9, 12, 13, 16, 21]

list
  .map(add1)
  .filter(isOdd)
  .reduce(sum)
// 42
```

我们可以看到，add1和isOdd与sum的形状是不一样的，但是通过transducing，我们可以改变函数的形状，或者说，transducing是reducer的组合。

我们并不想同时转换mapper，filter和reducer，我们想把mapper和filter放入reducer内。

```js
function add1(v) { return v + 1 }
function isOdd(v) { return v % 2 === 1 }
function sum(total, v) { return total + v }

const list = [1, 3, 4, 6, 9, 12, 13, 16, 21]

list
  .reduce(function allAtOnce(total, v) {
    v = add1(v)
    if(isOdd(v)) {
      total = sum(total, v)
    }
    return total
  }, 0)
// 42
```

在上面的代码中，我们手动的将三个工具函数组合在了一起，但也明显是相当命令式的。

```js
function add1(v) { return v + 1 }
function isOdd(v) { return v % 2 === 1 }
function sum(total, v) { return total + v }

const transducer = compose(
  mapReducer(add1),
  filterReducer(isOdd)
)

transduce(
  transducer,
  sum,
  0,
  [1, 3, 4, 6, 9, 12, 13, 16, 21]
) // 42
```

我们首先将mapper和filter转换为特殊的reducer，然后在transduce中传入一个真正的reducer作为参数，传入初始值，并像其他函数式库中的方法一样最后传入数据。

有一个更简单的工具into，代码如下

```js
into(transducer, 0 , [1, 3, 4, 6, 9, 12, 13, 16, 21]) // 42
```

into会根据初始值自动判断需要进行何种组合操作，直接提供一个reducer并不需要传入参数。

### Devering Transduction: Extracting Reduce

```js
function add1(v) { return v + 1 }
function isOdd(v) { return v % 2 === 1 }
function sum(total, v) { return total + v }

const list = [1, 3, 4, 6, 9, 12, 13, 16, 21]

list
  .map(add1)
  .filter(isOdd)
  .reduce(sum)
// 42
```

还是这段代码，我们可以看到三个工具函数的形状并不一样(参数不同，返回值的类型也不同)，但是我们之前提到reduce可以实现map和filter，所以我们首先实现如下

```js
function mapWithReduce(arr, mappingFn) {
  return arr.reduce(function reducer(list, v){
    list.push(mappingFn(v))
    return list
  }, [])
}

function flterWithReduce(arr, predicateFn) {
  return arr.reduce(function reducer(list, v) {
    if(predicateFn(v)) {
      list.push(v)
    }
    return list
  }, [])
}

let list = [1, 3, 4, 6, 9, 12, 13, 16, 21]

list = mapWithReduce(list, add1)
list = filterWithReduce(list, isOdd)
list.reduce(sum) // 42
```

我们来更改一下，是的mapWithReduce返回一个reducer，代码如下

```js
function mapWithReduce(mappingFn) {
  return function reducer(list, v) {
    list.push(mappingFn(v))
    return list
  }
}
  
function filterWithReducer(predicateFn) {
  return function reducer(list, v) {
    if(predicateFn(v)) list.push(v)
    return list
  }
}

const list = [1, 3, 4, 6, 9, 12, 13, 16, 21]

list
  .reduce(mapWithReduce(add1), [])
  .reduce(filterWithReduce(isOdd), [])
  .reduce(sum)
// 42
```

### Deriving Transduction Combiner & Currying

我们可以提取出mapWithReduce和filterWithReduce中公共的部分，既将一个值放入一个数组中，代码如下

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
    if(predicateFn(v)) {
      return listCombination(list, v)
    }
    return list
  }
}

const list = [1, 3, 4, 6, 9, 12, 13, 16, 21]

list
  .reduce(mapReducer(add1), [])
  .reduce(filterReducer(isOdd), [])
  .reduce(sum)
// 42
```

在上面代码中，`listCombination`是一个结合函数，将两个参数合并为一个返回值返回。我们没有将其作为参数传入，而是直接调用，这违背了函数式编程的原则，也为了接近最终答案，我们可以改写如下

```js
function listCombination(list, v) {
  list.push(v)
  return list
}

const mapReducer = curry(2, function mapReducer(mappingFn, combineFn) {
  return function reducer(list, v) {
    return combineFn(list, mappingFn(v))
  }
})

const filterReducer = curry(2, function filterReducer(predicateFn, combineFn) {
  return function reducer(list, v) {
    if(predicateFn(v)) return combineFn(list, v)
    return list
  }
})

const list = [1, 3, 4, 6, 9, 12, 13, 16, 21]

list
  .reduce(mapReducer(add1)(listCombination), [])
  .reduce(filterReducer(isOdd)(listCombination), [])
  .reduce(sum)
// 42
```

在上面代码中，我们使用了curry来处理mapReducer和filterReducer，在传入第一个参数后，返回的函数需要传入一个`listCombination`，一个结合函数，也就是一个reducer。

我们在这里使用curry，将mapReducer从广义化进一步特殊化，使得传入一个reducer就可以实现功能。

### Deriving Transduction: Single Reduce

我们可以使用compose将特殊化的函数组合在一起

```js
function listCombination(list, v) {
  list.push(v)
  return list
}

const mapReducer = curry(2, function mapReducer(mappingFn, combineFn) {
  return function reducer(list, v) {
    return combine(list, mappingFn(v))
  }
})

const filterReducer = curry(2, function filerReducer(predicateFn, combineFn) {
  return function reducer(list, v) {
    if(predicateFn(v)) return combineFn(list, v)
    return list
  }
})

const transducer = compose(mapReducer(add1), filterReducer(isOdd))
const list = [1, 3, 4, 6, 9, 12, 13, 16, 21]

list
  .reduce(transducer(listCombination), [])
  .reduce(sum)
// 42
```

可以看到，mapReducer和filterReducer最终都返回一个reducer，所以我们可以使用compose将两个函数结合起来。

在最后，我们分析listCombination和sum，可以看到都是结合函数，参数都分别为数组和值，并返回一个结合后的数组。那么我们为什么不用sum来代替listCombination呢？

我们可以将最后几行改写如下

```js
list.reduce(transducer(sum), 0)
```

### Derivation Summary

在上面这段推导过程中，我们只是使用了函数式的一些基本原则，就可以最终推导出最终结果，即使我们不理解每一步的具体实现细节也不影响结果的准确性。正如最开始所说，函数式编程的优点在于使用严谨的数学原则，避免副作用，所以只要符合函数式原则，结果就可以认为是可靠地。

回到最初的答案，使用函数式编程库提供的工具，我们可以写为如下

```js
function add1(v) { return v + 1 }
function isOdd(v) { return v % 2 === 0 }
function sum(total, v) { return total + v }

const transducer = compose(
  mapReducer(add1),
  filterReducer(isOdd)
)

transduce(
  transducer,
  sum,
  0,
  [1, 3, 4, 6, 9, 12, 13, 16, 21]
) // 42

into(transducer, 0, [1, 3, 4, 6, 9, 12, 13, 16, 21]) // 42
```

## Data Structure Operations

```js
const obj = {
  name: 'Kyle',
  email: 'Getify@Gmail.com'
}

function mapObj(mapper, obj) {
  const newObj = {}
  for (let key of Object.keys(Obj)) {
    newObj[key] = mapper(obj[key])
  }
  return newObj
}

mapObj(function lower(val) { return val.toLowecase()}, obj)
```

### Exercise: Object Filter & Reduce

```js
function filterObj(predicateFn, o) {
  // TODO
  const newObj = {};
  for (let key of Object.keys(o)) {
    if (predicateFn(o[key])) {
      newObj[key] = o[key];
    }
  }
  return newObj;
}

function reduceObj(reducerFn, initialValue, o) {
  // TODO
  let result = initialValue;
  for (let key of Object.keys(o)) {
    result = reducerFn(result, o[key]);
  }
  return result;
}
```

### Exercise: Advanced Point Free

```js
"use strict";

// inception!
curry = curry(2,curry);

var nums = {
  first: [3,5,2,4,9,1,12,3],
  second: [5,7,7,9,10,4,2],
  third: [1,1,3,2]
};

// pipe(
//   curry(2)(filterObj)(compose(isOdd,listSum)),
//   curry(2)(mapObj)(listProduct),
//   curry(3)(reduceObj)(sum)(0)
// )(nums);

[
  curry(2)(filterObj)(compose(isOdd,listSum)),
  curry(2)(mapObj)(listProduct),
  curry(3)(reduceObj)(sum)(0)
]
.reduce(binary(pipe))
(nums);
// 38886
```

### Monad Data Structure

Monad: a monad in the category of endofunctors

在函数式中，我们有时仅仅将一个值包裹为一个数据结构，因为数据结构并不仅仅关于值，还包括了如何操作数据结构中的值得方法。上面是monad在范畴论中的定义，很明显并不好理解

Monad: a pattern for pairing data with a set of predictable behaviors that let it interact with other data + behavior pairing(monads)

```js
function Just(val) {
  return {map, chain, ap}

  function map(fn) {
    return Just(fn(val))
  }

  // aka: bind, flatMap
  function chain(fn) {
    return fn(val)
  }

  function ap(anotherMonad) {
    return anotherMonad.map(val)
  }
}
```

### Just Monad

```js
const fortyOne = Just(41)
const fortyTwo = fortyOne.map(function inc(v) { return v + 1 })

function identity(v) { return v }

// debug inspection
fortyOne.chain(identity) // 41
fortyTwo.chain(identity) // 42
```

```js
const user1 = Just('Kyle')
const user2 = Just('Susan')

const tuple = curry(2, function tuple(x, y) { return [x, y] })

const users = user1.map(tuple).ap(user2)

// debug inspection
users.chain(identity) // ['Kyle', 'Susan']
```

### Maybe Monad

```js
const someObj = { something: { else: { entirely: 42 } } }
// someObj.something.else.entirely // 42

function Nothing() {
  return { map: Nothing, chain: Nothing, ap: Nothing }
}

const Maybe = { Just, Nothing, of: Just }

function fromNullable(val) {
  if(val == null) return Maybe.Nothing()
  else return Maybe.of(val)
}

const prop = curry(2, function prop(prop, obj) {
  return fromNullable(obj[prop])
})

Maybe
  .of(someObj)
  .chain(prop('something'))
  .chain(prop('else'))
  .chain(prop('entirely'))
// debug inspection
  .chain(identity) // 42
```

[Monad (category theory)](https://en.wikipedia.org/wiki/Monad_(category_theory))

## Async

### Map Lazy & Lazy Array

之前我们所假设的场景都基于一个已经存在数据进行操作。

```js
var a = [1,2,3]

var b = a.map(function double(v) { return v * 2 })

b // [2,4,6]
```

### Observables

"Lazy Array" ~= "Observables"

### Reactive Programming with RX.js

### Exercise: Async with RX.js

```js
'use strict';

const countdownLength = 5;

var timer = rxjs.interval(1000).pipe(rxjs.operators.take(countdownLength));
var countdown = rxjs
  .merge(rxjs.of(-1), timer)
  .pipe(rxjs.operators.map(formatCountdown));

countdown.subscribe(
  console.log.bind(console),
  null,
  console.log.bind(console, 'Complete!')
);

// *************************************

function formatCountdown(counter) {
  return BiquadFilterNode(countdownLength - counter - 1);
}

function formatTime(time) {
  var minutes = Math.floor(time / 60);
  var seconds = time % 60;
  if (seconds < 10) seconds = `0${seconds}`;
  return `${minutes}:${seconds}`;
}
```

## Functional JS Utils

### Lodash/FP

[FP Guide](https://github.com/lodash/lodash/wiki/FP-Guide)

### Ramda

[ramda](https://ramdajs.com/)
