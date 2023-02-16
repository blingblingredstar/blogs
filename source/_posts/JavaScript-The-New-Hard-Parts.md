---
title: 'JavaScript: The New Hard Parts'
date: 2019-06-28 08:55:43
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, Promise, iterator, generator, Async/await]
---

## Introduction

[sildes](https://static.frontendmasters.com/resources/2018-05-23-javascript-new-hard-parts/new-hard-parts-slides.pdf)

### The 5 capacities we look for in candidates

1. Analytical problem solving with code
   > 用代码解决问题的能力
2. Technical communication (can I implement your approach just from your explanation)
   > 技术沟通能力（我能仅仅通过你的解释就能实现你的方法吗）
3. Engineering best practices and approach (Debugging, code structure, patience and reference to documentation)
   > 编程的最佳实践和方法（调试，代码结构，耐心和文档引用）
4. Non-technical communication(empathetic and thoughtful communication)
   > 非技术沟通的能力（同理心和有思想的交流）
5. Language and computer science experience
   > 编程语言和计算机科学经验

#### Our expectations

+ Support each other - engineering empathy is the cirtical value at Codesmith
+ Work hard, Work smart
+ Thoughtful communication

<!--more-->

### JavaScript Code Execution

Frontend Masters - JavaScript the Hard Parts

1. **Foundations of JavaScript**
2. Asynchronous JavaScript(callbacks, promises)
3. Iterators
4. Generators & Async/await

#### Principles of JavaScript

In JSHP we start with a set of fundamental principles

These tools will enable us to problem solve and communicate almost any scenario in JavaScript

+ We will start with an essential approach to get ourselves up to a shared level of understanding
+ This approach will help us with the hard parts to come

#### What happens when javascript executes(runs) my code

```js
const num = 3
function multiplyBy2(inputNumber) {
  const result = inputNumber * 2
  return result
}

const name = 'will'

```

As soon as we start running our code, we create a *global execution context*.

+ Thread of execution(parsing and executing the code line after line)
+ Live memory of variables with data(known as a Global Variable Environment)

#### Running/calling/invoking a function

This is not same as defining a function

```js
const num = 3
function multiplyBy2(inputNumber) {
  const result = inputNumber * 2
  return result
}

const output = multiplyBy2(4)
const newOutput = multiplyBy2(10)
```

When you execute a function you create a new execution context comprising:

1. The thread of execution(we go through the code in the function line by line)
2. A local memory(`Variable Environment`) where anything defined in the function is stored

#### We keep track of the functions being called in JavaScript with a call stack

Tracks which execution context we are inthat is, what function is currently being run and where to return to

## Asynchronous JavaScript

1. Foundations of JavaScript
2. **Asynchronous JavaScript (callbacks, promises)**
3. Iterators 4. Generators

### Introduction Asynchronicity

#### Asynchornicity is the backbone of modern web development in JavaScript

JavaScript is single threaded(one command executing at a time) and has synchronous execution model(each line is executed in order the code appears)

So what if we need to **wait some time before we can execute certain bits of code**? Perhaps we need to wait on fresh data from an API/server request or for a timer to complete and then execute our code

We hava a conundrum - a tension between wanting to **delay some code execution** but **not wanting to block the thread** from any further code running while we wait

#### Solution 1

```js
function display(data) {
  console.log(data)
}

const dataFromAPI = fetchAndWait('https://twitter.com/will/tweets/1')
// ...user can do nothing here
// ...could be 300ms or longer
// they're just clicking and getting nothing

display(dataFromAPI)

console.log('Me later!')
```

##### Problems

+ Fundamentally untenable - blocks our single JavaScript thread from running any further code while the task completes

##### Benefits

+ It's easy to reason about

##### Goals

1. Be able to do tasks that take a long time to complete e.g. getting data from the server
2. Continue running our JavaScript code line by line without one long task blocking further JavaScript executing
3. When our slow task completes, we should be able to run functionality knowing that task is done and data is ready!

### Asynchronous Web Browser APIs

#### Solution 2 - Introducing Web Browser APIS/Node background threads

[MDN - `setTimeout()`](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/setTimeout)

```js
function printHello() {
  console.log('Hello')
}

setTimeout(printHello, 1000)

console.log('Me first!')
```

### Calling the Outside World

#### We're interacting with a world outside of JavaScript now - so we need rules

```js
function printHello() {
  console.log('Hello')
}

function blockFor1Sec() {
  //blocks in the JavaScript thread for 1 second
}

setTimeout(printHello, 0)

blockFor1Sec()

console.log('Me first!')
```
<!-- markdownlint-disable MD024 -->
### Wrapping Up Web Browser APIs

#### Problems

+ No problems!
+ Our response data is only available in the callback function - Callback Hell
+ Maybe it feels a little odd to think of passing a function into another function only for it to run much later

#### Benefits

+ Super explic once you understand how it works under-the-hood

### Asynchronous Exercise

#### Pair Programming

Answer these:

+ I know what a variable is
+ I've created a function before
+ I've added a CSS style before
+ I've implemented a sort algorithem(bubble, merge etc)
+ I can add a method to an object's prototype
+ I understand the event loop in JavaScript
+ I understand callback functions
+ I've implemented filter from scratch
+ I can handle collisions in hash tables

#### Challenges

[Asynchronicity & Promises](csbin.io/promises)

[Iterators, Generators & Async/await](csbin.in/iterators)

##### Challenge 1

Let's start by reviewing asynchronous functions! Using setTimeout, print the string 'Hello!' after 1000ms.

```js
function sayHello() {
  setTimeout(function(){
    console.log('Hello')
    }, 1000)
}

// Uncomment the line below when ready
sayHello(); // should log "Hello" after 1000ms
```

## Promises

### Introducing Promises

#### Introducing the readability enhancer - Promises

+ Speical objects built into JavaScript that get returned immediately when we make a call to a web browser API/feature(e.g. `fetch`) that's set up to return promises(not all are)
+ Promises act as a placeholder for the data we hope to get back from the web browser feature's background work
+ We also attach the functionality we want to defer running until that background work is done(using the built in `.then` method)
+ Promise objects will automactically trigger that functionality to run
  + The value returned from the web browser feature's work(e.g. the returned data from the server using `fetch`) will be that function's input/argument

### Promises

#### Solution 3 - Using two-pronged 'facade' functions that both initiate background web browser work and return a placeholder object(promise) immediately in JavaScript

```js
function display(data) {
  console.log(data)
}

const futureData = fetch('https://twitter.com/will/tweets/1')

futureData.then(display) // Attaches display functionality

console.log('Me first!')
```

[`fetch()` MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/WindowOrWorkerGlobalScope/fetch)

```js
promiseObject {
  value: undefined, // the result of data or fetch's background work is gonna stored when it comes back
  onFulfillment: [], // we can stick any functions we want auto trigger when the value gets updated
}
```

上面是一个promise对象的大致结构，当数据更新时，触发onFulfillment内的所有函数。

当使用`fetch`方法时，会立即返回一个`promise`对象（js范畴内），并且调用Web Browser API(XMLHttpRequset)在浏览器后台获取数据。当数据返回时，更新promise对象内的数据，执行方法。

### Promises & Microtask Queue

#### But we need to know how our promise - deferred functionality gets back into JavaScrpt to be run

```js
function display(data) {
  console.log(data)
}
function printHello() {
  console.log('hello')
}
function blockFor300ms() {
  // blocks JS thread for 300ms with long for loops
}

setTimeout(printHello, 0)

const futureData = fetch('https://twitter.com/will/tweets/1')
futureData.then(display)

blockFor300ms()

//Which will run first?

console.log('Me first!')
```

[job queue](https://www.ecma-international.org/ecma-262/7.0/#sec-jobs-and-job-queues)

JS除了callback queue，还有一个microtask(job) queue。`fetch`的XMLHttpRequest请求完成后会将回调函数推入microtask queue而不是callback queue。event loop会先将microtask queue内的回调执行完毕后才会将callback queue内的回调推入调用栈执行。

##### We need a way of queuing up all this deferred functionality

### Wrapping Up Promises

#### Problems

+ 99% of developers have no idea how they're working under the hood
+ Debugging becomes super-hard

#### Benefits

+ Cleaner readable sytle with pseudo-synchronous style code
+ Nice error handling process

#### We have rules for the execution of our asynchronously delayed code

1. Hold each promise-deferred functions in a microtask queue and each non-promise defered function in a task queue(callback queue) when the API 'completes'
2. Add the function to the Call Stack(i.e. execute the function) ONLY when the stack is totally empty(Have the Event Loop check this condition)
3. Prioritize tasks(callbacks) in the microtask queue over the regular task queue

Promises, Web APIs, the Callback & Microtask Queues and Event loop allow us to defer our actions until the 'work'(an API request, timer etc) is completed and continue running our code line by line in the meantime

Asynchronous JavaScript is the backbone of the modern web - letting us build fast 'non-blocking'

## Iterators

1. Foundations of JavaScript
2. Asynchronous JavaScript (callbacks, promises)
3. **Iterators**
4. Generators & Async/await

### Return Function Inside a Function

We regularly have lists or collections or data where we want to go through each item and do something to each element

```js
const nums = [4, 5, 6]

for(let i=0; i < nums.length; i++) {
  console.log(nums[i])
}
```

We're going to discover there's a new beautiful way of thinking about using each element one-by-one

#### Programs store data and apply functionnality to it.But there are two parts to applying functions to collections of data

1. The process of acessing each element
2. What we want to do to each element

Iterators automate the accessing of each element - so we can focus on what to do to each element - and make it avaliable to us in a smooth way

Imagine if we could create a function that stored numbers and each time we ran the function it would return out an element(the next one) from numbers. NOTE: It'd have to remember which element was next up somehow

But this would let us think of our array/list as a 'stream'/flow of data with our function returning the next element from our 'stream' - this makes our code more readable and more functional

##### But it starts with us returning a function from another function

#### Functions can be returned from other functions in JavaScript

```js
function createNewFunc() {
  function add2(num) {
    return num + 2
  }
  return add2
}

const newFunc = createNewFunc()

const result = newFunc(3)

```

How can we run/call add2 now? Outside of createNewFunc?

### Return Next Element with a Function

#### We want to create a function that holds both our array, the position we are currently at in our 'stream' of elements and has the abality to return the next element

```js
function createFunc(array) {
  let i = 0
  function inner() {
    const element = array[i]
    i++
    return element
  }
  return inner
}

const returnNextElement = createFunc([4, 5, 6])
```

How can we access the first element of our list?

#### By calling the returnNextElement

```js
function createFunc(array) {
  let i = 0
  function inner() {
    const element = array[i]
    i++
    return element
  }
  return inner
}

const returnNextElement = createFunc([4, 5, 6])
const element1 = returnNextElement() // 4
const element2 = returnNextElement() // 5
```

### Iterator Function

#### The bond

+ When the function inner is defined, it gets a bond to the surrounding Local Memory in which it has been defined
+ When we return out inner, that surrounding live data is returned out too - attached on the 'back' of the function definition itself(which we now give a new global label `returnNextElement`)
+ When we call `returnNextElement` and don't find `array` or `i` in the immediate execution context, we look into the function definition's 'backpack'(closure) of persiitent live data
+ The 'backpack' is offically known as the C.O.V.E.(Cloesd Over Variable Environment) or 'closure'

#### `returnNextElement` has everything we need all bundled up in it

1. Our underlying array itself
2. The position we are currently at in our 'stream' of elements
3. The ability to return the next element

This relies completely on the special property of fucntions in JavaScript that when they are born inside other functions and returned - they get a backpack(closure)

What is the posh name for `returnNextElement`?

#### So iterators turns our data into 'streams' of actual values we can access one after another

Now we have functions that hold our underlying array, the position we're currently at in the array, and return out the  next item in the 'stream' of elements from our array when run

This lets us have for loops that show us the element itself in the body on each loop and more deeply allows us to rethink arrays as flows of elements themselves which we can interact with by calling a function that switches that flow on to give us our next element

We have truly 'decoupled' the process of accessing each element from what we want to do to each element

### Iterators Exercise

[problems](http://csbin.io/iterators)

[solutions](https://gist.github.com/aegorenkov/2ae91cabf21223bddca8c5b3ef3ec6f6)

## Generators

1. Foundations of JavaScript
2. Asynchronous JavaScript (callbacks, promises)
3. Iterators
4. **Generators & Async/await**

### Generators

#### JavaScript built in iterators are actually objects with a next method that when called returns the next element from the 'stream'/flow - so let's restructure slightly

```js
function createFlow(array) {
  let i = 0
  const inner = {
    next: function() {
      const element = array[i]
      i++
      return element
    }
  }
  return inner
}

const returnNextElement = createFlow([4, 5, 6])
const element1 = returnNextElement.next()
const element2 = returnNextElement.next()
```

And the built in iterators actually produce the next element in the format: `{value: 4}`

#### Once we start thinking of our data as flows(where we can pick of an element one-by-one) we can rethink how we produce those flows - JavaScript now lets us produce the flows using a function

```js
function *createFlow() {
  yield 4
  yield 5
  yield 6
}

const returnNextElement = createFlow()
const element1 = retrunNextElement.next()
const element2 = retrunNextElement.next()
```

What do we hope `returnNextElement.next()` will return? But how?

[MDN yield](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/yield)

### Generator Functions with Dynamic Data

#### This allows us to dynamically set what data flows to us(when we run returnNextElement's function)

```js
function *createFlow() {
  const num = 10
  const newNum = yield num
  yield 5 + newNum
  yield 6
}

const returnNextElement = createFlow()
const element1 = returnNextElement.next() // 10
const element2 = returnNextElement.next(2) // 7
```

#### `returnNextElement` is a special object(a gengerator object) that when its next method is run starts(or continues) running `createFlow` until it hits `yield` and returns out the value being 'yielded'

```js
function *createFlow() {
  const num = 10
  const newNum = yield num  // yield可以看做一个特殊的return操作符，所以这行是 const newNum = return 10,此时执行上下文保留并返回10，newNum为undefined。当给element2赋值时，在returnNextElement.next(2)中传入实参2，此时数字2作为参数给newNum进行了赋值。
  yield 5 + newNum // 此时newNum因为上一行的newNum = 2,值为2
  yield 6
}

const returnNextElement = createFlow()
const element1 = returnNextElement.next() // 10
const element2 = returnNextElement.next(2) // 7
```

`returnNextElement.next()`的参数会作为上一个`yield`的执行结果的值
具体来说，`returnNextElement.next(2)`会使`*createFlow`的`const newNum = yield num`变为`const newNum = 2`

We end up with a 'stream'/flow of values that we can get one-by-one by running `returnNextElement.next()`

### Introducing Async Gererators

#### And most importantly, for the first time we get to pause('suspend') a function being run and then return to it by calling `returnNextElement.next()`

In asynchronous JavaScript we want to

1. Initiate a task that takes a long time(e.g. requesting data from the server)
2. Move on to more synchronous regular code in the meantime
3. Run some functionality once the requested data has come back

What if we were to `yield` out of the function at the moment of sending off the long-time task and return to the function only when the task is complete

### Async Generators

#### We can use the abality to pause createFlow's running and then restart it only when our data returns

```js
function doWhenDataReceived(value) {
  returnNextElement.next(value)
}

function *createFlow() {
  const data = yield fetch('http://twitter.com/will/tweets/1')
  console.log(data)
}

const returnNextElement = createFlow()
const futureData = returnNextElement.next().value

futureData.then(doWhenDataReceived)
```

We get to control when we return back to createFlow and continue executing - by setting up the trigger to do so(`returnNextElement.next()`) to be run by our function that was triggered by the promise resolution(when the value retruned from twitter)

## Final

### Async Await

#### Async/await simplifies all this and finally fixes the inversion of control problem of callbacks

```js
async function createFlow() {
  console.log('Me first!')
  const data = await fetch('https://twitter.com/will/tweets/1')
  console.log(data)
}

createFlow()

console.log('Me second!')
```

No need for a triggered function on the promise resolution, instead we auto trigger the resumption of the `createFlow` execution(this functionality is still added to the microtask queue though)
