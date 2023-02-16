---
title: 'JavaScript The Hard Parts, v2'
date: 2020-04-06 00:42:47
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, 闭包, 高阶函数, 面相对象, 原型机制, Promise]
---
## Introduction

[slides](https://static.frontendmasters.com/resources/2019-09-18-javascript-hard-parts-v2/javascript-hard-parts-v2.pdf)

+ Overview
  + JavaScript principles
  + Callbacks & High Order functions
  + Closure
  + Classes/Prototypes & Asynchronicity

+ What to focus on in the workshop?
  + Analytical problem solving
  + Engineering approach
  + Technical communication
  + Non-technical communication
  + JavaScript and programming experience
<!-- more -->
+ Junior engineer takes any feature they're given to build. And if they've seen technology or maybe the solution before, they can solve it.
+ Mid-level engineer takes any feature they're given to build. And even if they've not seen the technology or solution before, they can figure it out because they've learned how to learn, they're problem solving strong.
+ Senior engineer is somebody who can take any given feature, and not only just figure for themselves, but enable the rest of their team to figure it out. Because they have technical communication, the ability to explain what their code is doing to somebody else in their team, in a clear and cordial manner.

## JavaScript Principles

### Thread of Execution

+ When JavaScript code runs, it:
  + Goes through the code line-by-line and runs/'executes' each line - known as **thread of execution**
  + Saves 'data' like strings and arrays so we can use that data later - in its **memory**
    + We can even save code('functions')

```js
const num = 3

function multiplyBy2 (inputNumber) {
  const result = inputNumber * 2
  return result
}

const output = multiplyBy2(num)
const newOutput = multiplyBy2(10)
```

### Functions

+ Code we save ('define') functions & can use (call/invoke/execute/run) later with the function's name & ()

+ Execution context
  + Created to run the code of a function - has 2 parts(we've already seen them)
    + Thread of execution
    + Memory

### Call Stack

+ JavaScript keeps track of what function is currently running(where's the thread of execution)
+ Run a function - add to call stack
+ Finish running the function - JS removes it from call stack
+ Whatever is top of the call stack - that's the function we're currently running

## Functions & Callbacks

### Generalized Functions

+ Why do we even have functions?
  + Let's see why...
  + Create a function 10 squared
    + Takes no input
    + Returns 10 * 10
  + What is the syntax(the exact code we type)?

```js
function tenSquared() {
  return 10 * 10
}

tenSquared() // 100
```

+ What about a 9 squared function?
+ What about a 8 squared function? 125 squared function?
+ What principle are we breaking?
+ DRY

+ We can generalize the function to make it reuseable

```js
function squareNum(num) {
  return num * num
}

squareNum(10) // 100
squareNum(9) // 81
squareNum(8) // 64
```

+ Generalizing functions
  + 'Parameters'(placeholders) mean we don't need to decide what data to run our functionality on until we run the function
    + Then provide an actual value('argument') when we run the function
  + High order functions follow this same principle
    + We may not want to decide exactly what some of our functionality is until we run our function

### Repeating Functionality

+ Now suppose we have a function `copyArrayAndMultiplyBy2`

```js
function copyArrayAndMultiplyBy2(array) {
  const output = []
  for(let i = 0; i < array.length; i++) {
    output.push(array[i] * 2)
  }
  return output
}

const myArray = [1, 2, 3]
const result = copyArrayAndMultiplyBy2(myArray)
```

+ What if we want to copy array and divide by 2?
+ Or add 3?

### High Order Functions

+ We could generalize our function - So we pass in our specific instruction only when we run copyArrayAndManipulate!

### Higher Order Functions Example

```js
function copyArrayAndManipulate(array, instructions) {
  const output = []
  for(let i = 0; i < array.length; i++) {
    output.push(instructions(array[i]))
  }
  return output
}
function multiplyBy2(input) { return input * 2 }
const result = copyArrayAndManipulate([1, 2, 3], multiplyBy2)
```

### Callbacks & Higher Order Functions

+ How was this possible?
  + Functions in JavaScript = first class objects
  + They can co-exist with and can be treated like any other JavaScript object
    1. Assigned to variables and properties of other objects
    2. Passed as arguments into functions
    3. Returned as values from functions

+ Which is our Higher Order Function?
  + The outer function that takes in a function is our higher-order function
+ Which is our Callback Function?
  + The function we insert is our callback function

+ Higher-order functions
  + Takes in a function or passes out a function
  + Just a term to describe these functions - any function that does it we call that - but there's nothing different about them inherently

+ Callbacks and Higher Order Functions simplify our code and keep it DRY
  + **Declarative readable code:** Map, filter, reduce - the most readable way to write code to work with data
  + **Codesmith & pro interview prep:** One of the most popular topics to test in interview both for Codesmith and mid/senior level job interviews
  + **Asynchronous JavaScript:** Callbacks are a core aspect of async JavaScript, and are under-the-hood of promises, async/await

### Arrow Functions

+ Introducing arrow functions - a shorthand way to save functions

```js
function multiplyBy2(input) { return input * 2 }

const multiplyBy2 = (input) => { return input * 2 }

const multiplyBy2 = (input) => input * 2

const multiplyBy2 = input => input * 2

const output = multiplyBy2(3) // 6
```

+ Updating our callback function as an arrow function

```js
function copyArrayAndManipulate(array, instructions) {
  const output = []
  for(let i = 0; i < array.length; i++) {
    output.push(instructions(array[i]))
  }
  return output
}

const multiplyBy2 = input => input * 2
const result = copyArrayAndManipulate([1, 2, 3], multiplyBy2)
```

+ We can even pass in multiplyBy2 directly without a name
  + But it's still just the code of a function being passed into copyAndManipulate

```js
function copyArrayAndManipulate(array, instructions) {
  const output = []
  for (let i = 0; i < array.length; i++) {
    output.push(instructions(array[i]))
  }
  return output
}

const result = copyArrayAndManipulate([1, 2, 3], input => input * 2)
```

+ Anonymous and arrow functions
  + Improve immediate legibility of the code
  + But at least for our purposes here they are 'syntactic sugar' - we'll see their full effects later
  + Understanding how they're working under-the-hood is vital to avoid confusion

### Pair Programming

+ Pair Programming
  + The most effective way to grow as a software engineer

+ Researcher
  + Avoids blocks by reading everything they can find on their block/bug
+ Stackoverflower
  + Uses code snippets to fix bug without knowing how they work
+ Pair programming
  + Tackle blocks with a partner
  + Stay focused on the problem
  + Refine technical communication
  + Collaborate to solve problem

+ For each topic you know give yourself a point to get a total out of
  + I know what a variable is
  + I've created a function before
  + I've added a CSS style before
  + I have implemented a sort algorithm(bubble, merge, etc)
  + I can add a method to an object's prototype
  + I understand the event loop in JavaScript
  + I understand 'callback functions'
  + I can implement filter
  + I can handle collisions in a hash table

## Closure

### Closure Introduction

+ Closure
  + Closure is the most esoteric of JavaScript concepts
  + Enables powerful pro-level functions like 'once' and 'memoize'
  + Many JavaScript design patterns including the module pattern use closure
  + Built iterators, handle partial application and maintain state in an asynchronous world

+ Functions get a new memory every run/invocation

```js
function multiplyBy2 (inputNumber) {
  const result = inputNumber * 2
  return result
}

const output = multiplyBy2(7)
const newOutput = multiplyBy2(10)
```

+ Function with memories
  + When our functions get called, we created a live store of data(local memory/variable environment/state) for that function's execution context.
  + When the function finishes executing, its local memory is deleted(except the returned value)
  + But what if our function could hold on to live data between executions?
  + This would let our function definitions have an associated cache/persistent memory
  + But it all starts with us **returning a function from another function**

### Returning Functions

+ Functions can be returned from other functions in JavaScript

```js
function createFunction() {
  function multiplyBy2 (num) {
    return num * 2
  }
  return multiplyBy2
}

const generatedFunc = createFunction()
const result = generatedFunc(3) // 6
```

### Nested Function Scope

+ Calling a function in the same function call as it was defined

```js
function outer () {
  let counter = 0
  function incrementCounter() {
    counter++
  }
  incrementCounter()
}
outer()
```

> Where you define your functions determines what data it has access to when you call it

### Retaining Function Memory

+ Calling a function outside of the function call in which it was defined

```js
function outer () {
  let counter = 0
  function incrementCounter() { counter++ }
  return incrementCounter
}

const myNewFunction = outer()
myNewFunction()
myNewFunction()
```

### Function Closure

+ 函数声明时会获得一个私有属性，一般为`[[scope]]`，里面保存了函数执行上下文所涉及到的变量环境（也就是函数的背包或者闭包的实际实现的方式）
  + 当函数使用上上级执行上下文是的变量，也会保存上上级执行上下文的变量环境（作用域链）

### Closure Technical Definition & Review

+ [I never understood JavaScript closures](https://medium.com/dailyjs/i-never-understood-javascript-closures-9663703368e8)

+ [If Hemingway Wrote JavaScript](https://nostarch.com/hemingway?__cf_chl_captcha_tk__=b759174fad03f3f24dda56cb6c2ae2ee4239838b-1585970682-0-AXERc6qisOqUz7bjRmojbQHq_2MXylDLXb-hhNpEAPb_chqu_pJe2ST1k0khNVvd8_BJYrNehqv6J2Q39kIRdJIqDlt833grZTP8ur6ai2ULzcGLCKSR3WDNT2ZguBb76UktrHMs5KGdQVneazRWP6YGuIiUjPFenso4mcBs4XBaAfyqBMtUH7-sqAEnJrHxK-yLeHJ-EXTphTuPWJMEyOUPAtBLgUR4--QMXplbRxAoChLY64ajVgPNY6wX3BePYMypHaLQTWjWoiK9xyG-j9Zufm94rlM0SUkQLCchLAlqlPP0IK-pQBHVTa9kcLIrOA)
  + 在这本书中，作者将closure(闭包)称为Closed Over Variable Environment
  + 闭包里保存的变量称为Persistent Lexically(or Static) Scope Reference Data
  + 或者可以简单的称闭包为背包(Backpack)😂

+ 每种语言都可以根据自己的需求制定自己的作用域规则，在JavaScript中，使用词法作用域(lexical Scope)或称静态作用域(Static Scope)的规则，既当函数定义时，就确定的函数所可以访问的变量范围。
  + 同样，如果函数执行时才能确定访问的变量，称作动态作用域，具体区别可见[这篇博客](https://blingblingredstar.github.io/2019/04/29/Deep-JavaScript-Foundations/#Scope-%E4%BD%9C%E7%94%A8%E5%9F%9F)

+ What can we call this 'backpack'
  + Closed over 'Variable Environment'(C.O.V.E.)
  + Persistent Lexical Scope Referenced Data(P.L.S.R.D.)
  + 'backpack'
  + 'Closure'

+ The 'backpack'(or 'closure') of live data is attached incrementCounter (then to myNewFunction) through a hidden property know as `[[scope]]` which persists when the inner function is returned out

### Multiple Closure Instance

+ Let's run outer again

```js
function outer() {
  let counter = 0
  function incrementCounter() {
    counter++
  }
  return incrementCounter
}

const myNewFunction = outer()
myNewFunction() // 1
myNewFunction() // 2

const anotherFunction = outer()
anotherFunction() // 1
anotherFunction() // 2
```

### Practical Application

+ Closure gives out functions persistent memories and entirely new toolkit for writing professional code
  + **Helper functions:** Everyday professional helper functions like 'once' and 'memorize'
  + **Iterators and generators:** Which use lexical scoping and closure to achieve the most contemporary patterns for handling data in JavaScript
  + **Module pattern:** Preserve state for the life of an application without polluting the global namespace
  + **Asynchronous JavaScript:** Callbacks and promises rely on closure to persist state in an asynchronous environment

### Closure Exercise

+ [exercise link](http://csbin.io/closures)

## Asynchronous JavaScript

### Single Threaded Execution Review

+ Promises, Async & Event Loop
  + Promises - the most significant ES6 feature
  + Asynchronicity - the feature that makes dynamic web applications possible
  + The event loop - JavaScript triage
  + Microtask queue, callback queue and Web Browser features(APIs)

+ [Tasks, microtasks, queues and schedules](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)

+ A reminder of how JavaScript executes code

```js
const num = 3
function multiplyBy2 (inputNumber) {
  const result = inputNumber * 2
  return result
}

const output = multiplyBy2(num)
const newOutput = multiplyBy2(10)
```

### Asynchronicity in JavaScript

+ Asynchronicity is the backbone of modern web development in JavaScript yet...
  + JavaScript is:
    + Single thread (one commend runs at a time)
    + Synchronously executed (each line is run in order the code appears)
  + So what if we have a task:
    + Accessing Twitter's server to get new tweets that takes a long time
    + Code we want to run using those tweets
  + **Challenge**: We want to wait for the tweets to be stored in tweets so that they're there to run displayTweets on - but no code can run in the meantime

+ Slow function blocks further code running

```js
const tweets = getTweets('http://twitter.com/will/1')

// ⛔️ 350ms wait while a request is sent to Twitter's server

displayTweets(tweets)

// more code to run
console.log('I want to running!')
```

+ What if we try to delay a function directly using setTimeout?
  + setTimeout is a built in function - its first arguments is to function to delay followed by ms to delay by

```js
function printHello() {
  console.log('Hello')
}

setTimeout(printHello, 1000)
console.log('Me first')
// In what order will our console.log appear?
```

+ So what about a delay of 0ms
  + Now, in what order will our console.log occur?

```js
function printHello() {
  console.log('hello')
}

setTimeout(printHello, 0)

console.log('Me first!')
```

+ JavaScript is not enough - We need new pieces(some of which aren't JavaScript at all)
  + Our core JavaScript engine has 3 main parts:
    1. Thread of execution
    2. Memory/variable environment
    3. Call stack
  + We need to add some new components:
    + Web Browser APIs/Node background APIs
    + Promises
    + Event loop, Callback/Task queue and micro task queue

### Asynchronous Browser Features

+ ES5 solution: Introducing 'callback functions', and Web Browser APIs

```js
function printHello() { console.log('Hello') }

setTimeout(printHello, 1000)

console.log('Me first!')
```

### Web API Rules

+ We're interacting with a world outside of JavaScript now - so we need rule

```js
function printHello() { console.log('hello') }
function blockFor1Sec() {
  // blocks in the JavaScript thread for 1 sec
}
setTimeout(printHello, 0)

blockFor1Sec()
console.log('Me first!')
```

### Callback Queue & Event Loop

+ 使用setTimeout或者setTimeout时，传入的回调函数在其他线程中开始计时，计时结束时推入callback queue, 此时代码不会阻塞线程，而是会继续执行，只有当前代码全部执行完毕，event loop才会开始轮询callback queue内是否有需要执行的回调函数

### Callback Hell & Async Exercise

+ ES5 Web Browser APIs with callback functions
  + Problems
    + Our response data is only available in the callback function - Callback Hell
    + Maybe it feels a little odd to think of passing a function into another function only for it to run much later
  + Benefits
    + Super explicit once you understand how it works under-the-hood

+ [Exercise Link](http://csbin.io/async)

## Promises

### Promises Introduction

+ ES6+ Solution(Promises)
  + Using two-pronged 'facade' functions that both:
    + Initiate background web browser work and
    + Return a placeholder object(promise) immediately in JavaScript

### Promises Example: fetch

+ ES6+ Promises

```js
function display(data) {
  console.log(data)
}

const futureData = fetch('http://twitter.com/will/tweets/1')
futureData.then(display)

console.log('Me first')
```

### Promises Example: then

+ 当我们使用Promise时，会会返回一个promise对象，其中对象会有一个隐藏的value属性代表新建promise时传入的回调函数的返回值，还有一个onFulfilled属性数组。当我们使用then方法是，就会将传入then方法的回调函数推入这个数组，在promise的状态变为resolved时，会将value作为参数传入onFulfilled数组中的函数依次执行。

### Web APIs & Promises Example: fetch

+ But we need to know how our promise-deferred functionality gets back into JavaScript to be run

```js
function display(data) { console.log(data) }
function printHello() { console.log('hello') }
function blockFor300ms() {/* blocks js thread for 300ms */}

setTimeout(printHello, 0)

const futureData = fetch('http://twitter.com/will/tweets/1')
futureData.then(display)

blockFor300ms()
console.loo('me first')
// me first
// display
// hello
```

+ `then` method and functionality to call on completion
  + Any code we want to run on the returned data must also be saved on the promise object
  + Added using `.then` method to the hidden property `onFulfillment`
  + Promise objects will automatically trigger the attached function to run(with its input being returned data)

### Web APIs & Promises Example: Microtask Queue

+ 上面的代码中，在控制台打印’me first'之后会执行display而不是printHello，这是由于promise使用了microtask queue，而setTimeout使用callback queue(规范中称为task queue)
+ microtask queue的优先级是高于callback queue的，event loop会在当前执行线程完毕后首先检查microtask queue中是否有回调函数并执行，也就是将回调推入call stack，在microtask queue全部执行完后才会检查callback queue，所以在microtask queue中返回promise有可能造成死循环。而
+ callback queue执行完一个回调之后并不会阻塞线程，如果有未执行完毕的会推迟到下一个执行周期去执行、

+ [Web APIs](https://developer.mozilla.org/en-US/docs/Web/API)

### Promises Review

+ Problems
  + 99% of developers have no idea how they're working under the hood
  + Debugging becomes super-hard as a result
  + Developers fail technical interviews
+ Benefits
  + Cleaner readable style with pseudo-synchronous style code
  + Nice error handling process

+ 这里作者吐槽了then方法的命名有可能造成误解，应该命名为storeFunctionToRunLater😂,Promise应该命名为FutureData

+ 如上所述，Promise的优点还有对错误处理的良好风格。
  + Promise除了value、onFulfilled数组，还有一个onRejection数组，用来放置错误处理的回调函数。我们可以使用`.catch`方法或者在`.then`方法中传入第二个参数来将回调添加到onRejection数组中。

+ We have rules for the execution of our asynchronously delayed code
  + Hold promise-deferred functions in a microtask queue and callback function in a task queue(Callback queue) when Web Browser Feature(API) finishes
  + Add the function to the Call stack(i.e. run the function) when:
    + Call stack is empty & all global code run (have the event loop check this condition)
  + Prioritize functions in the microtask queue over the Callback queue

+ Promises, Web APIs, the Callback & Microtask Queues and Event loop enable:
  + **Non-blocking applications:** This means we don't have to wait in the single thread and don't block further code from running
  + **However long it takes:** We cannot predict when our Browser feature's work will finish so we let JS handle automatically running the function on its completion
  + **Web applications:** Asynchronous JavaScript is the backbone of the modern web - letting us build fast 'non-blocking' applications

## Classes & Prototypes

### Class & OOP Introduction

+ Classes, Prototypes - Object Oriented JavaScript
  + An environment popular paradigm for structuring our complex code
  + Prototype chain - the feature behind-the-scenes that enables emulation of OOP but is a compelling tool in itself
  + Understanding the difference between `__proto__` and prototype
  + The new and class keywords as tools to automate our object & method creation

+ Core of development(and running code)
  1. Save data(e.g. in a quiz game the scores of user1 and user2)
  2. Run code(functions) using that data(e.g. increase user 2's score)
  + Easy! So why is development hard?
  + In a quiz game I need to save lots of users, but also admins, quiz questions, quiz outcomes, league tables - all have data and associated functionality
  + In 1000,000 lines of code
    + Where is the functionality when I need it?
    + How do I make sure the functionality is only used on the right data!

+ That is, I want my code to be:
  1. Easy to reason about, but also
  2. Easy to add feature to(new functionality)
  3. Nevertheless efficient and performance
+ The Object-oriented paradigm aims to let us achieve these three goals

### Object Dot Notation

+ So if I'm storing each user in my app with their respective data(let's simplify)

| --    | User 1 | User 2    |
| ----- | ------ | --------- |
| name  | Tim    | Stephanie |
| score | 3      | 5         |

+ And the functionality I need to have for each user(again simplifying!)
  + Increment functionality(there'd be a ton of functions in practice)
+ How could I store my data and functionality together in one piece?

+ Objects - store functions with their associated data!
  + This is the principle of encapsulation - and it's going to transform how we can 'reason about' our code

```js
const user1 = {
  name: 'Will',
  score: 3,
  increment() {
    user1.score++
  }
}

user1.increment() // user1.score -> 4
```

+ Let's keep creating our objects. What alternative techniques do we have for creating objects?

+ Creating user2 use dot natation
  + Declare an empty object and add properties with dot natation

```js
const user2 = {} // create an empty object

// assign properties to that object
user2.name = 'Tim'
user2.score = 6
user2.increment = function () {
  user2.score++
}
```

+ Creating user3 using `Object.create`
  + Object.create is going to give us fine-grained control over our object later on

```js
const user3 = Object.create(null)

user3.name = 'Eva'
user3.score = 9
user3.increment = function () {
  user3.score++
}
```

+ Our code is getting repetitive, we're breaking our DRY principle. And suppose we have millions of users! What could we do?

+ Solution 1
  + Generate objects using a function

```js
function userCreator(name, score) {
  const newUser = {}
  newUser.name = name
  newUser.score = score
  newUser.increment = function() {
    newUser.score++ // 注意这里使用了闭包
  }
  return newUser
}

const user1 = userCreator('Will', 3)
const user2 = userCreator('Tim', 5)
user1.increment()
```

+ Solution 1. Generate Objects using a function
  + **Problems:** Each time we create a new user we make space in our computer's memory for all our data and functions. But our functions are just copies
  + Is there a better way?
  + **Benefits:** It's simple and easy to reason about!

### Prototype Chain

+ Solution 2: Using the prototype chain
  + Store the increment function in just one object and have the interpreter, if it doesn't find the function on user1, look up to that object to check if it's there
  + Link user1 and functionScore so the interpreter, on not finding `.increment`. makes sure to check up in functionStore where it would find it
  + Make the link with `Object.create()` technique

+ Solution 2: Using the prototype chain

```js
function userCreator(name, score) {
  const newUser = Object.create(userFunctionStore) // 此时newUser.__proto__指向userFunctionStore
  newUser.name = name
  newUser.score = score
  return newUser
}

const userFunctionStore = {
  increment() { this.score++ },
  login() { console.log('Logged in') }
}

const user1 = userCreator('Will', 3)
const user2 = userCreator('Tim', 5)
user1.increment()
// 当js引擎在user1上没有找到increment方法时，就会去user1的__proto__属性所指向的对象上去寻找（原型）
// 如果这个对象上也没有，就去这个对象的__proto__属性所指向的对象上寻找（原型链)
```

### Prototype Chain Example: Implicit Parameters

+ [MDN `Object.getPrototypeOf()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf)

+ 在上面的代码中，我们在声明increment方法时并不清楚会是user1还是user2调用此方法，那么我们如何确定我们需要增加分数的对象呢？
+ 可以看到，这也正是`this`关键字的作用
+ 可以认为`this`指向方法调用时`.`关键字前面的对象，如果没使用`.`语法调用方法，可以认为`this`指向全局对象

### hasOwnProperty Method

+ What if we want to confirm our user! has property score

```js
function userCreator(name, score) {
  const newUser = Object.create(userFunctionStore)
  newUser.name = name
  newUser.score = score
  return newUser
}

const userFunctionStore = {
  increment() { this.score++ },
  login() { console.log('Login in') }
}

const user1 = userCreator('Will', 3)
const user2 = userCreator('Tim', 5)

user1.hasOwnProperty('score')
// user1.__proto__.__proto__.hasOwnProperty
```

+ We can use the `hasOwnProperty` method - but where is it?
  + Object.prototype.hasOwnProperty

+ What if we want to confirm our user1 has the property score?
  + We can use the hasOwnProperty method - but where is it? Is it on user1?
  + All objects have a `__proto__` property by default which defaults to linking to a big object - Object.prototype full of (somewhat) useful functions
  + We get access to it via userFunctionStore's `__proto__` property - the chain

### this Keyword

+ Declaring & calling a new function *inside* our 'method' increment

```js
function userCreator(name, score) {
  const newUser = Object.create(userFunctionStore) // 此时newUser.__proto__指向userFunctionStore
  newUser.name = name
  newUser.score = score
  return newUser
}

const userFunctionStore = {
  increment() { this.score++ },
  login() { console.log('Logged in') }
}

const user1 = userCreator('Will', 3)
const user2 = userCreator('Tim', 5)
user1.increment()
```

+ Let's start by simplifying(just increment method - written over 3 lines now)

+ Create and invoke a new function (add1) inside increment

```js
function userCreator(name, score) {
  const newUser = Object.create(userFunctionStore)
  newUser.name = name
  newUser.score = score
  return newUser
}

const userFunctionStore = {
  increment() {
    function add1() {
      this.score++
    }
    add1()
  }
}

const user1 = userCreator('Will', 1)
const user2 = userCreator('Tim', 4)
user1.increment()
```

+ What does `this` get auto-assigned to?
  + window/global

### Arrow Function Scope & this

+ Arrow functions override the normal `this` rules

```js
function userCreator(name, score) {
  const newUser = Object.create(userFunctionStore)
  newUser.name = name
  newUser.score = score
  return newUser
}

const userFunctionStore = {
  increment() {
    const add1 = () => { this.score++ }
    add1()
  }
}

const user1 = userCreator('Will', 3)
const user2 = userCreator('Tim', 5)
user1.increment()
```

+ Now our inner function gets its `this` set by where it was saved - it's a `lexical scoped this`

### Prototype Chain Review

+ Solution 2: Using the prototype chain
  + Problems: No problems! It's beautiful. Maybe a little long-winded
  + Write this every single time - but it's 6 words!

    ```js
    const newUser = Object.create(userFunctionStore)
    /** ... **/
    return newUser
    ```

  + Benefit: Super sophisticated but not standard

### new Keyword

+ Solution 3 - Introducing the keyword that automates the hard work: new
  + When we call the function that returns an object with `new` in front we automate 2 things
    1. Create a new user object
    2. Return the new user object
  + But now we need to adjust how we write the body of userCreator - how can we:
    + Refer to the auto-created object?
      + `this`
    + Know where to put our single copies of functions?
      + prototype

+ The `new` keyword automates a lot of our manual work

```js
function userCreator(name, score) {
  this.name = name
  this.score = score
}

userCreator.prototype.increment = function () {
  this.score++
}

const user1 = new userCreator('Will', 4)
```

+ Interlude - functions are both objects and functions

```js
function multiplyBy2(num) {
  return num * 2
}

multiplyBy2.stored = 5
multiplyBy2(3)

multiplyBy2.stored // 5
multiplyBy2.prototype // {}
```

+ We could use the fact that all functions have a default property 'prototype' on their object version.(itself an object) - to replace our 'functionStore' object

### new Keyword Example

+ The new keyword automates a lot of our manual work

```js
function userCreator(name, score) {
  this.name = name
  this.score = score
}

userCreator.prototype.increment = function () { this.score++ }
userCreator.prototype.login = function () { console.log('Logged in') }

const user1 = new userCreator('Eva', 9)

user1.increment()
```

### class keyword

+ Solution 3 - Introducing the keyword that automates the hard work: new
  + **Benefits:**
    + Faster to write. Often used in practice in professional code
  + **Problem:**
    + 95% of developers have no idea how it works and therefore fail interviews
    + We have to upper case first letter of the function so we know it requires `new` to work!

+ Solution 4: The class 'syntactic sugar'
  + We're writing our shared methods separately from our object 'constructor' itself(off in the `userCreator.prototype` object)
  + Other languages let us do this all in one place. ES2015 lets us do so too

+ Solution 4: The class 'syntactic sugar'

```js
class UserCreator {
  constructor(name, score) {
    this.name = name
    this.score = score
  }
  
  increment() { this.score++ }
  login() { console.log('login') }
}

const user1 = new UserCreator('Eva', 4)
user1.increment()
```

+ Solution 4: The class 'syntactic sugar'
  + **Benefit:**
    + Emerging as a new standard
    + Feels more like style of other languages(e.g. Python)
  + **Problems:**
    + 99% of developers have no idea how it works and therefore fail interviews
    + But you will not be one of them
