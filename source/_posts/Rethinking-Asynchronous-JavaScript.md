---
title: Rethinking Asynchronous JavaScript
date: 2019-07-09 15:43:04
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, Asynchronous, Callbacks, Thunks, Promises, Generators, Observables, CSP]
---

## Parallel vs. Async

### Course Introduction

[you don't know JS](http://YouDontKnowJS.com)

#### Agenda

+ Async Patterns
  + Parallel vs. Async
  + Callbacks
  + Thunks
  + Promises
  + Generators/Coroutines
  + Event Reactive(observables)
  + CSP(channel-oriented concurrency)
<!-- more -->
### Single Threaded JavaScript

parallel(并行)既多线程同时协同工作，关键在于优化(optimization)

async(异步)是单线程的(single thread)

### Concurrency

虽然js是单线程，但是可以通过event loop来更改事件的执行顺序来模拟出异步状态，在用户看来就是并发的，尽管js仍是单线程的既同一时刻只执行一个事件。

Async is manage our concurrency

## Callback

### Callback Hell

最简单的回调函数：

```js
setTimeout(function() {
  console.log('callback')
}, 1000)
```

Callbacks == Continuations

callback Hell:

```js
setTimeout(function() {
  console.log('one')
  setTimeout(function() {
    console.log('two')
    setTimeout(function() {
      console.log('three')
    }, 1000)
  }, 1000)
}, 1000)
```

以上就是通常所说的回调地狱，也叫回调金字塔。回调地狱不仅仅是因为套嵌和缩进所造成的阅读问题，例如以下代码

```js
function one(cb){
  console.log('one')
  setTimeout(cb, 1000)
}

function two(cb){
  console.log('two')
  setTimeout(cb, 1000)
}

function three(cb){
  console.log('three')
}

one(function() {
  two(three)
})
```

尽管缩进问题和套嵌问题已经改善，但是可读性仍然没有质的提高。

### Exercise 1

1. This exercise calls for you to write some async flow-control code. To start off with, you'll use callbacks only.
   这个练习要求您编写一些异步流控制代码。首先，您将只使用回调。
2. Expected behavior:
   期望的行为:
   + Request all 3 files at the same time (in "parallel").
     同时请求所有3个文件(“并行”)。
   + Render them ASAP (don't just blindly wait for all to finish loading)
     尽快渲染(不要盲目等待加载完成)
   + BUT, render them in proper (obvious) order: "file1", "file2", "file3".
     但是，以适当的(明显的)顺序呈现它们:“file1”、“file2”、“file3”。
   + After all 3 are done, output "Complete!".
     完成所有3项后，输出“Complete!”

```js
function fakeAjax(url, cb) {
  var fake_responses = {
    file1: 'The first text',
    file2: 'The middle text',
    file3: 'The last text'
  }
  var randomDelay = (Math.round(Math.random() * 1e4) % 8000) + 1000

  console.log('Requesting: ' + url)

  setTimeout(function() {
    cb(fake_responses[url])
  }, randomDelay)
}

function output(text) {
  console.log(text)
}

// **************************************
// The old-n-busted callback way

function getFile(file) {
  fakeAjax(file, function(text) {
    // what do we do here?
    handleResponse(file, text)
  })
}

const response = {}

function handleResponse(fileName, contents) {
  if (!(fileName in response)) {
    response[fileName] = contents
  }

  const fileNames = ['file1', 'file2', 'file3']
  for (let i = 0; i < fileNames.length; i++) {
    const filename = fileNames[i]
    if (filename in response) {
      if (typeof filename == 'string') {
        output(response[filename])
        response[filename] = false
      }
    } else {
      return
    }
  }
}
// request all files at once in "parallel"
getFile('file1')
getFile('file2')
getFile('file3')
```

### Callback Problems: Inversion of Control

在这里的inversion of control指的是在代码中同步的部分是由我们预期的时间执行，由我们自己控制的，但是回调部分中的代码是交由event loop执行，无法预估执行时间，由event loop控制执行的。

```js
tracCheckout(
  purchaseInfo,
  function finish() {
    chargeCreditCard(purchaseInfo)
    showThankYouPage
  }
)
```

以上是一个用来追踪付款的函数，这里想象了一个场景，有一天顾客提出了一个问题：信用卡重复支付了5次。经过排查可以怀疑是回调函数重复执行了5次，但是回调无法追踪，也没有具体文档。这就引出了一个问题，既我们默认回调是可以信任的，没有任何问题的。但实际上，不同回调实现的算法不同，所以会有各种的Corner cases。回调会有以下一些信任问题：

1. not too early
2. not too late
3. not too many times
4. not too few times
5. no lost context
6. no swallowed errors
   ......

### Callback Problems: Not Reason-able

一般人的大脑基本是单线程的，在同一时刻只能思考一件事，js引擎也是一样。

当我们大脑的思考模式与js引擎工作模式分离的时候，也就是bug开始产生的时候。

以下是大脑工作模式的伪代码

```js
start task1:
  do some stuff
  pause

start task2:
  do some other stuff
  pause

resume task1:
  do more stuff
  pause

resume task2:
  finish stuff

resume task1:
  finish stuff
```

使用callback时，工作模式更像下面的伪代码:

```js
start task1:
  do some stuff
  pause

  resume task1:
    do more stuff
    pause

    resume task1:
      finish stuff

start task2:
  do some stuff
  pause

  resume task2:
    do more stuff
    pause

    resume task2:
      finish stuff
```

但我们需要一些函数执行完在执行里一个函数的情况可以称为Temporal Dependency，使用回调函数处理temporal dependency的话就要套嵌回调函数。但是很明显，我们的大脑并不适合这种工作模式，理解起来很困难。

```js
console.log('First half of my program')

setTimeout(() => {
  console.log('Second half of my program')
}, 1000)
```

### Non Fixes

#### Async Patterns: separate callbacks

```js
function trySomething(ok, err) {
  setTimeout(function() {
    const num = Math.random()
    if(num > .5) ok(num)
    else err(num)
  }, 1000)
}

trySomething(
  num => console.log(`Success ${num}`),
  num => console.log(`Sorry ${num}`)
)
```

#### Async Pattern: "error-first style"

```js
function trySomething(callback) {
  setTimeout(function() {
    const num = Math.random()
    if(num > .5) callback(null, num)
    else callback('Too low!')
  })
}

trySomething(
  (err, num) => {
    if(err) console.log(err)
    else console.log(`Number ${num}`)
  }
)
```

#### Async Pattern: nested-callback tasks

```js
function getData(data, callback) {
  setTimeout(() => { callback(data) }, 1000)
}

getData(10, function(num1) {
  const x = 1 + num1
  getData(30, function(num2) {
    const y = 1 + num2
    getData(
      `Meaning of life: ${x + y}`,
      answer => console.log(answer)
    )
  })
})
```

## Thunks

### Synchronous and Asynchronous Thunks

Thunk is a function that has everything already that it needs to do to give you some value back.

This is a synchronous thunk:

```js
function add(x, y) {
  return x + y
}

const thunk = function() {
  return add(10, 15)
}

thunk() // 25
```

This is asynchronous thunk, only need to pass the callback argument:

```js
function addAsync(x, y, cb) {
  setTimeout(() => { cb(x + y)}, 1000)
}

const thunk = cb => addAsync(10, 15, cb)

thunk(sum =>
  sum // 25
  )
```

Time is most complex factor of state in your program, managing time is most complex factor of state in your program

```js
function makeThunk(fn) {
  const args = [].slice.call(arguments, 1)
  return function callback(cb) {
    args.push(cb)
    fn.apply(null, args)
  }
}
```

```js
function addAsync(x, y, cb) {
  setTimeout(() => { cb(x + y)}, 1000)
}

const thunk = makeThunk(addAsycn, 10, 15)

thunk(sum => {
  console.log(sum) // 25
})
```

#### Async Patterns: nested-thunk tasks

```js
const get10 = makeThunk(getData, 10)
const get30 = makeThunk(getData, 30)

get10(num1 => {
  const x = 1 + num1
  get30(num2 => {
    const y = 1 + num2

    const getAnswer = makeThunk(getData,
      `Meaning of life: ${x + y}`
    )

    getAnswer(answer =>
      console.log(answer) // Meaning of life: 42
    )
  })
})
```

### Exercise 2

1. You'll do the same thing as the previous exercise(s), but now you should use thunks.
   您将做与前面的练习相同的事情，但是现在您应该使用thunks。

2. Expected behavior:
   期望的行为:
   + Request all 3 files at the same time (in "parallel").
     同时请求所有3个文件(“并行”)。
   + Render them ASAP (don't just blindly wait for all to finish loading)
     尽快渲染(不要盲目等待加载完成)
   + BUT, render them in proper (obvious) order: "file1", "file2", "file3".
     但是，以适当的(明显的)顺序呈现它们:“file1”、“file2”、“file3”。
   + After all 3 are done, output "Complete!".
     完成所有3项后，输出“Complete!”

```js
import { callbackify } from 'util'

function fakeAjax(url, cb) {
  var fake_responses = {
    file1: 'The first text',
    file2: 'The middle text',
    file3: 'The last text'
  }
  var randomDelay = (Math.round(Math.random() * 1e4) % 8000) + 1000

  console.log('Requesting: ' + url)

  setTimeout(function() {
    cb(fake_responses[url])
  }, randomDelay)
}

function output(text) {
  console.log(text)
}

// **************************************

function getFile(file) {
  // what do we do here?
  let text, fn // 在函数内设置变量来保存异步函数返回的数据和回调函数

  fakeAjax(file, response => {
    if (fn) fn(response) // 如果收到了闭包中的回调函数，使用回调函数处理数据
    else text = response // 如果还没有收到回调函数，直接返回数据给闭包
  })

  return callback => {
    if (text) callback(text) // 如果异步请求执行完毕，已返回数据，直接使用回调函数处理数据
    else fn = callback // 如果异步请求没有返回数据，将回调函数通过闭包传给异步请求，处理数据。
  }
}

// request all files at once in "parallel"
const thunk1 = getFile('file1'),
  thunk2 = getFile('file2'),
  thunk3 = getFile('file3')

thunk1(text1 => {
  output(text1)
  thunk2(text2 => {
    output(text2)
    thunk3(text3 => {
      output(text3)
      output('Complete!')
    })
  })
})
```

### Thunks and Closures

Why should thunk be something that's part of my arsenal of weapons that I used to take down asynchronous programming issure?

In the thunk pattern by using the closure to maintain the state of something. We eliminate time as a complecting factor of state.

在上面的代码中，我们在闭包中设置变量保存异步调用返回的数据和尚未传入的回调函数，通过判断数据的状态来决定是在回调中直接处理数据还是将回调函数传入异步函数中返回处理过后的数据。通过这种方法，我们解决了数据返回的时间问题，既可以确认通过thunk，传入一个回调函数，会返回一个处理后的结果，这也是promise的基本原理之一，也是promise如此革命性的愿意之一。

## Promises

### Native Promises

Promise就像去肯德基买汉堡，把钱交给收银员后并不会立即得到一个汉堡，而是一个订单号。拿着订单号去柜台可以在汉堡准备好之后领到汉堡。在拿到订单号之后，虽然并没有立即获得汉堡，但是可以将订单号视为汉堡进行处理，可以将汉堡自己吃掉也可以交给另一个人。

promise是一个包裹future value的容器。promise提供了一种可以忽略value的时间状态来composing的方法，所以在函数式编程的理论中，promise是一个monad。

在callback模式中，我们提供一个callback给异步方法，由异步方法决定何时调用回调函数。在promise中，我们调用promise，在promise执行结束后通知我们，由我们决定对执行结果进行怎样处理。promise也可以视为一个eventListener，在执行结束后会有一个“Completion Events”。

### Promis API

```js
function finish() {
  chargeCreditCard(purchaseInfo)
  showThankYouPage()
}

function error(err) {
  logStatsError(err)
  finish()
}

const listener = trackCheckout(purchaseInfo)

listener.on('completion', finish)
listener.on('error', error)
```

```js
function trackCheckout(info) {
  return new Promise(
    function(resolve, reject) {
      // attempt to track the checkout

      // if successful, call resolve()
      // otherwise, call reject(error)
    }
  )
}
```

```js
function finish() {
  chargeCreditCard(purchaseInfo)
  showThankYouPage()
}

function error(err) {
  logStatsError(err)
  finish()
}

const promise = trackCheckout(purchaseInfo)

promise.then(
  finish,
  error
)
```

通过promise，我们确认将要返回一个future value，可以自行决定对其如何进行处理，我们把控制权从异步方法转移回了我们自己，既通过消灭时间状态来消灭inversion of control。

#### Promise Trust

1. Only resloved once
2. Either success or error
3. Messages passed/kept
4. Exceptions becomes errors
5. Immutable once resloved

### Promise Flow Control

```pseudo
doFirstThing
  then doSecondThing
  then doThirdThing
  then complete
or error
```

通过Chainning Promises,我们可以使用promise来处理数据流。

```js
doFirstThing()
  .then(() => doSecondThing)
  .then(() => doThirdThing)
  .then(
    complete,
    error
  )
```

```js
function delay(num) {
  return new Promise(function (reslove, reject) {
    setTimeout(resolve,num)
  })
}

delay(100)
  .then(() => delay(50))
  .then(() => delay(200))
  .then(() => console.log('All done'))
```

```js
function getData(data) {
  return new Promise((res, rej) => {
    setTimeout(() => {
      res(data)
    }, 1000)
  })
}

let x

getData(10)
  .then(num1 => {
    x = 1 + num1
    return getData(30)
  })
  .then(num2 => {
    const y = 1 + num2
    return getData(`Meaning of life: ${x + y}`)
  })
  .then(answer => {
    console.log(answer) // Meaning of life: 42
  })
```

### Exercise 3

1. You'll do the same thing as the previous exercise(s), but now you should use thunks.
   您将做与前面的练习相同的事情，但是现在您应该使用thunks。

2. Expected behavior:
   期望的行为:
   + Request all 3 files at the same time (in "parallel").
     同时请求所有3个文件(“并行”)。
   + Render them ASAP (don't just blindly wait for all to finish loading)
     尽快渲染(不要盲目等待加载完成)
   + BUT, render them in proper (obvious) order: "file1", "file2", "file3".
     但是，以适当的(明显的)顺序呈现它们:“file1”、“file2”、“file3”。
   + After all 3 are done, output "Complete!".
     完成所有3项后，输出“Complete!”

```js

function fakeAjax(url, cb) {
  var fake_responses = {
    file1: 'The first text',
    file2: 'The middle text',
    file3: 'The last text'
  }
  var randomDelay = (Math.round(Math.random() * 1e4) % 8000) + 1000

  console.log('Requesting: ' + url)

  setTimeout(function() {
    cb(fake_responses[url])
  }, randomDelay)
}

function output(text) {
  console.log(text)
}

// **************************************

function getFile(file) {
  // what do we do here?
  return new Promise((resolve, reject) => {
    fakeAjax(file, resolve)
  })
}

// request all files at once in "parallel"
const promise1 = getFile('file1'),
  promise2 = getFile('file2'),
  promise3 = getFile('file3')

// promise1
//   .then(file1 => {
//     output(file1)
//     return promise2
//   })
//   .then(file2 => {
//     output(file2)
//     return promise2
//   })
//   .then(file3 => {
//     output(file3)
//     output('complete')
//   })

promise1
  .then(output)
  .then(() => promise2)
  .then(output)
  .then(() => promise3)
  .then(output)
  .then(() => output('complete'))
```

### Exercise 4

```js
function fakeAjax(url, cb) {
  var fake_responses = {
    file1: 'The first text',
    file2: 'The middle text',
    file3: 'The last text'
  }
  var randomDelay = (Math.round(Math.random() * 1e4) % 8000) + 1000

  console.log('Requesting: ' + url)

  setTimeout(function() {
    cb(fake_responses[url])
  }, randomDelay)
}

function output(text) {
  console.log(text)
}

// **************************************
// The old-n-busted callback way

function getFile(file) {
  return new Promise(function(resolve) {
    fakeAjax(file, resolve)
  })
}

// Request all files at once in
// "parallel" via `getFile(..)`.
//
// Render as each one finishes,
// but only once previous rendering
// is done.

const files = ['file1', 'file2', 'file3']

const parallel = files
  .map(file => getFile(file))
  .reduce((chain, nextPromise) => {
    return chain
      .then(function nextFile() {
        return nextPromise
      })
      .then(output)
  }, Promise.resolve())
  .then(function complete() {
    output('complete')
  })
```

### Abstractions

#### Promise.all()

```js
Promise.all([
  doTaks1a(),
  doTaks1b(),
  doTaks1c()
])
  .then(function (results){
    return doTask2(
      Math.max(
        results[0],
        results[1],
        results[2]
      )
    )
  })
```

Promise.all()将多个promise组合成的数组作为参数，参数中的promise状态更新后更新本身的状态。

#### Promise.race()

```js
const p = trySomeAsyncThing()

Promise
  .race([
    p,
    new Promise(function(_, reject) {
      setTimeout(function() {
        reject('Timeout')
      }, 3000)
    })
  ])
  .then(
    success,
    error
  )
```

Promise.race()将多个promise组合成的数组作为参数，并返回最先执行完毕的那个promise对象。

[github.com/getify/native-promise-only](https://github.com/getify/native-promise-only)

### Sequences & Gates

sequence = automatically chained promises

[Github Library Asynquence](https://github.com/getify/asynquence)

```js
const delay1Second = done => { setTimeout(done, 1000) }

ASQ()
  .then(delay1Second)
  .gate(
    delay1Second,
    delay1Second
  )
  .then(done => {
    console.log('2 seconds passed!')
  })
```

### Exercise 5

```js
function fakeAjax(url, cb) {
  var fake_responses = {
    file1: 'The first text',
    file2: 'The middle text',
    file3: 'The last text'
  }
  var randomDelay = (Math.round(Math.random() * 1e4) % 8000) + 1000

  console.log('Requesting: ' + url)

  setTimeout(function() {
    cb(fake_responses[url])
  }, randomDelay)
}

function output(text) {
  console.log(text)
}

// **************************************

function getFile(file) {
  return ASQ(function(done) {
    fakeAjax(file, done)
  })
}

// Request all files at once in
// "parallel" via `getFile(..)`.
//
// Render as each one finishes,
// but only once previous rendering
// is done.
ASQ()
  .seq(getFile('file1'))
  .val(output)
  .seq(getFile('file2'))
  .val(output)
  .seq(getFile('file3'))
  .val(output)
  .val(function() {
    output('Complete!')
  })
```

### Exercise 6

```js
function fakeAjax(url, cb) {
  var fake_responses = {
    file1: 'The first text',
    file2: 'The middle text',
    file3: 'The last text'
  }
  var randomDelay = (Math.round(Math.random() * 1e4) % 8000) + 1000

  console.log('Requesting: ' + url)

  setTimeout(function() {
    cb(fake_responses[url])
  }, randomDelay)
}

function output(text) {
  console.log(text)
}

// **************************************

function getFile(file) {
  return ASQ(function(done) {
    fakeAjax(file, done)
  })
}

// Request all files at once in
// "parallel" via `getFile(..)`.
//
// Render as each one finishes,
// but only once previous rendering
// is done.
// ASQ()
//   .seq.apply(
//     null,
//     ['file1', 'file2', 'file3']
//       // Request all files at once in "parallel"
//       .map(getFile)
//       // Render output as each file finishes, but
//       // only once previous rendering is done
//       .map(function(sq) {
//         return function() {
//           return sq.val(output)
//         }
//       })
//   )
//   .val(function() {
//     output('Complete!')
//   })

ASQ()
  .seq(...['file1', 'file2', 'file3'].map(getFile).map(seq => () => seq.val(output)))
  .val(function() {
    output('Complete!')
  })
```

## Generator

### Generator Example

If promises were about solving the inversion of control issue in callback, generators are about solving the non-local, non-sequential reasonability problem.

A generator is a syntactic form of a state machine, is a pauseable function.

```js
function *gen() {
  console.log('hello')
  yield // return and pause
  console.log('world')
}

const it = gen()  // return a iterator
it.next() // hello
it.next() // world
```

### Messaging

```js
function *main() {
  yield 1
  yield 2
  yield 3
}

const iterator = main()

iterator.next() // {value: 1, done: false}
iterator.next() // {value: 2, done: false}
iterator.next() // {value: 3, done: false}
iterator.next() // {value: undefined, done: true}
```

```js
function coroutine(generator) {
  const iterator = generator()
  return function (){
    return iterator.next.apply(iterator, arguments)
  }
}
```

```js
const run = coroutine(function *(){
  const x = 1 + (yield),
    y = 1 + (yield)
  yield (x + y)
})

run() // {value: undefined, done: false}
run(10) // {value: undefined, done: false}
console.log(`Meaning of life: ${run(30).value}`) // Meaning of life: 42
```

### Messaging Quertions

generator can never finish. Sometimes you have to design a generator never finish.

可以在generator内部使用while(true)这个无限循环来实现生成unique ID之类的功能。

### Async Generators

```js
function coroutine(generator) {
  const iterator = generator()
  return function (){
    return iterator.next.apply(iterator, arguments)
  }
}

function getData(data) {
  setTimeout(function() { run(data) }, 1000)
}

const run = coroutine(function *() {
  const x = 1 + (yield getData(10))
  const y = 1 + (yield getData(30))
  const answer = (yield getData(
    `Meaning of life: ${x + y}`
  ))
  console.log(answer) // Meaning of life: 42
  console.log('This will be first?')
})

run()
```

通过使用generator，我们拥有了暂停函数执行的能力，这使得我们可以以同步的模式来编写异步代码，同时Error handler也可以以同步的方式来进行处理。

### Promises + Generator

```js
function getData(data) {
  return ASQ(done => { setTimeout(function() {done(data)}, 1000)} ) // 返回一个新的promise对象，并设置好then方法
}

ASQ()
  .runner(function *() {
    const x = 1 + (yield getData(10))
    const y = 1 + (yield getData(30))
    const answer = (yield getData(
      `Meaning of life: ${x + y}`
    ))
    yield answer
  })
  .val(answer => {console.log(answer)}) // Meaning of life: 42
```

原生的promise和generator代码如下:

```js
function doWhenDataReecived(data) {
  returnNextElement.next(data)
}

function *createFlow() {
  const data = yield fetch('http://twitter.com/will/tweets/1')
  console.log(data)
}

const returnNextElement = createFlow()
const futureData = returnNextElement.next()

futureData.then(doWhenDataReceived)
```

使用es6的async await语法，代码类似如下:

```js
async function createFlow() {
  console.log('Me first!')
  const data = await fetch('https://twitter.com/will/tweets/1')
  console.log(data)
}

createFlow()

console.log('Me second!')
```

因为目前promise是无法取消的，所以最新的async await语法也是无法取消的。但是使用generator和promise结合的语法是，可以直接使用generator.next来跳过promise执行。

### Exercise 7

```js
function fakeAjax(url, cb) {
  var fake_responses = {
    file1: 'The first text',
    file2: 'The middle text',
    file3: 'The last text'
  }
  var randomDelay = (Math.round(Math.random() * 1e4) % 8000) + 1000

  console.log('Requesting: ' + url)

  setTimeout(function() {
    cb(fake_responses[url])
  }, randomDelay)
}

function output(text) {
  console.log(text)
}

// **************************************

function getFile(file) {
  return ASQ(function(done) {
    fakeAjax(file, done)
  })
}

// function getFile(file) {
//   return new Promise(function(resolve){
//     fakeAjax(file,resolve);
//   });
// }

ASQ()
  .runner(function* loadFiles() {
    // Request all files at once in
    // "parallel" via `getFile(..)`.
    var p1 = getFile('file1')
    var p2 = getFile('file2')
    var p3 = getFile('file3')

    // Render as each one finishes,
    // but only once previous rendering
    // is done.
    output(yield p1)
    output(yield p2)
    output(yield p3)
  })
  .val(function() {
    output('Complete!')
  })
```

### Quiz

1. What is "callback hell"? Why do callbacks suffer from "inversion of control" and "unreasonability"?
2. What is a Promise? How does it solve inversion of control issues?
3. How do you pause a generator? How do you resume it?
4. How do we combine generators and promises for flow control?

[davidwalsh.name/es6-generatros](https://davidwalsh.name/es6-generators)

## Observables

### Events + Promises

Concurrency: Events (+ Promises)?

```js
const p1 = new Promise(function executor(resolve, reject){
  $('#btn').click(event => {
    const className = event.target.className
    if(/foobar/.test(className)) {
      resolve(className)
    }
    else {
      reject()
    }
  })
})

p1.then(className => {
  console.log(className)
})
```

以上代码的功能为点击按钮，测试按钮的className是否符合正则，然后输出className,因为使用了promise，所以以上代码只能执行一次。所以我们可以改为如下，以多次运行：

```js
$('#btn').click(event => {
  const className = event.target.className,
    p1 = new Promise(function executor(resolve, reject){
      if(/foobar/.test(className)) {
        resolve(className)
      }
      else {
        reject()
      }
    })
  
  p1.then(className => {console.log(className)})
})
```

但是以上代码又变的没有什么意义，我们创建了一个promise后又在函数内直接使用了.then方法消费了promise。

```js
$('#btn').click(event => {
  [event]
    .map(event => event.target.className)
    .filter(className => /foobar/.test(className))
    .forEach(className => {console.log(className)})
})
```

以上代码我们还是将consumer和producer耦合在了一起，我们需要一种模式将producer和consumer解耦。

### Enter Observables

Observable在课程中主要指的是微软的rx.js。

Observable is kind of like a chain of calculated fields in a spreadsheet.

在Excel表格中，单元格可以依据其他单元格的数据进行计算的结果做为数据保存，这就像是一个数据流，当我们更改其中一个单元格数据时，其他单元格的数据也会重新进行计算并更改。

Observable是一个producer，产生数据流，经过一系列计算转换后由consumer进行使用。

An observable is an adapter hooked onto an event source, that produces a promise, every time a new event comes through.

```js
const observable = Rx.Observable.formEvent(btn, 'click')

observable
  .map(clickEvent => clickEvent.target.className)
  .filter(className => /foobar/.test(className))
  .distinctUntilChanged()
  .subscribe(data => {
    console.log(data[1])
  })
```

可以通过[rxmarbles.com](http://rxmarbles.com/)来观察rx.js的各种api的输出结果，理解操作符作用。

### Reactive Sequences

```js
function fromEvent(element, eventType) {
  return ASQ.react(proceed => {
    $(element).bind(eventType, proceed)
  })
}

const rsq = fromEvent(btn, 'click') // aka: Observable

rsq
  .val(event => event.target.className)
  .then((done, className) {
    if(/foobar/.test(className)) {
      done(className)
    }
  })
  .val(calssName => {console.log(className)})
```

```js
function fromEvent(el, eventType) {
  const rsq = ASQ.react.of()
  $(el).bind(eventType, rsq.push)
  return rsq
}
```

```js
const rsq1 = ASQ.react.of(),
 rsq2 = ASQ.react.of(1, 2, 3)
let x = 10

setInterval(() => {
  rsq1.push(x++)
  rsq2.push(x++)
}, 500)

rsq1.val(v => {console.log(`1:${v}`)}) // 1: 10...1: 12... 1:14
rsq2.val(v => {console.log(`2:${v}`)}) // 2: 1...2: 2... 2: 3... 2: 11... 2: 13...
```

### Exercise 8

1. In this exercise, we're going to practice with "reactive sequences" programming (aka "observables") using asynquence.

2. Set up a reactive sequence for the stream of click events from the button. Hint: `ASQ.react.of()` and `rsq.push(..)`

3. Set up another reactive sequence that samples the sequence stream from (2) once per second. In other words, if you click the button multiple times in a second, you only get one event message. Hint: `setInterval(..)`

4. The sampled sequence should add a "clicked!" message to the list.

```js
$(document).ready(function() {
  var $btn = $('#btn'),
    $list = $('#list'),
    clicks = ASQ.react.of(),
    msgs = ASQ.react.of(),
    latest

  $btn.click(function(evt) {
    // push click event messages into stream
    clicks.push(evt)
  })

  // sample clicks stream
  setInterval(function() {
    if (latest) {
      msgs.push('clicked!')
      latest = null
    }
  }, 1000)

  // subscribe to click stream
  clicks.val(function(evt) {
    latest = evt
  })

  // subscribe to sampled message stream
  msgs.val(function(msg) {
    $list.append($('<div>' + msg + '</div>'))
  })
})
```

## CSP

### Concurrency + Channels

Channel is kind of like a stream, kind of like a pipe.

Channel提供了consumer向producer发出信息的机制。

CSP: Communicating Sequential Processes

Conmunicating Sequential Processes is all about modeling your application with lots of tiny independent pieces that we call processes, and they get to a point where they say we need to coordinate.

```js
const channel = chan()

function *process1() {
  yield put(channel, 'Hello')
  const msg = yield take(channel)
  console.log(msg)
}

function *process2() {
  const greeting = yield take(channel)
  yield put(channel, greeting + 'World')
  console.log('Done')
}

// Hello World
// Done
```

### Blocking Channels

```js
csp.go(function *() {
  while(true) {
    yield csp.put(ch, Math.random())
  }
})

csp.go(function *() {
  while(true) {
    yield csp.take( csp.timeout(500) )
    let num = yield csp.take(ch)
    console.log(num)
  }
})
```

```js
csp.go(function*(){
  while(true) {
    const msg = yield csp.alts(ch1, ch2, ch3)
    console.log(msg)
  }
})
```

### Event Channels

```js
function fromEvent(el, eventType) {
  const ch = csp.chan()
  $(el).bind(eventType, event => {
    csp.putAsync(ch, event)
  })

  return ch
}

csp.go(function*() {
  const ch = fromEvent(el, 'mousemove')
  while(true) {
    const evt = yield csp.take(ch)
    console.log(
      evt.clientX + '.' + evt.clientY
    )
  }
})
```

```js
ASQ().runner(
  ASQ.csp.go(function *process1(ch) {
    yield ASQ.csp.put(ch, 'Hello')
    const msg = yield ASQ.csp.take(ch)
    console.log(msg)
  }),
  ASQ.csp.go(function *process2(ch) {
    const greeting = yield ASQ.csp.take(ch)
    yield ASQ.csp.put(ch, greeting + " World")
    console.log('done!')
  })
)
// Hello World
// done!
```

### Exercise 9

```js
$(document).ready(function() {
  var $btn = $('#btn'),
    $list = $('#list'),
    clicks = ASQ.csp.chan(),
    msgs = ASQ.csp.chan(),
    queuedClick

  $btn.click(listenToClicks)

  // run go-routines
  ASQ().runner(ASQ.csp.go(sampleClicks), ASQ.csp.go(logClick))

  // push click event messages into channel
  function listenToClicks(evt) {
    if (!queuedClick) {
      queuedClick = ASQ.csp.putAsync(clicks, evt)
      queuedClick.then(function() {
        queuedClick = null
      })
    }
  }

  // sample clicks channel
  function* sampleClicks() {
    while (true) {
      yield ASQ.csp.take(ASQ.csp.timeout(1000))
      yield ASQ.csp.take(clicks)
      yield ASQ.csp.put(msgs, 'clicked!')
    }
  }

  // subscribe to sampled message channel
  function* logClick() {
    while (true) {
      var msg = yield ASQ.csp.take(msgs)
      $list.append($('<div>' + msg + '</div>'))
    }
  }
})
```

### Recap

1. Callbacks / Thunks
2. Promises
3. Generators
4. Observables
5. CSP go-routines
