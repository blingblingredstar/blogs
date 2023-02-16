---
title: The Hard Parts of Servers & Node.js
date: 2019-08-15 09:53:48
categories: 学习笔记
tags: [FrontEndMasters, Node.js, Event Loop]
---

## Introduction

+ The power of Node
  + Most powerful technology in web development to emerge in 10 years
  + Enables applications that can handle millions of users without blocking
  + From simple webpages to largest scaled applications, to Windows/Mac desktop apps (with Electron), and hardware (embedded systems)
  + Allows us to build entire applications end-to- end in one language - JavaScript

+ From client side development to full stack development
  + Our users open twitter.com
  + they need code and data to load twitter.com on their computers
  + What code/data do they need to load?
  + Where’s the code/data coming from?
<!-- more -->
### Node Overview

+ From client side development to full stack development
  + Our users open twitter.com - they need code and data to load twitter.com on their computers
    + What code/data do they need to load?
    + Where’s the code/data coming from?

+ Servers are the behind-the-scenes of all web applications - where our client-side code/data comes from
  + Computer connected to the internet - a permanent store of code/data, always on, ready to receive messages over the internet from users requesting code/data and send it back
    + How’s this computer know what to send back?(We write **code**)
    + What languages can we use to write these instructions?(c/c++、java、php、ruby... )
  + But how can we access these inbound messages as developers and send code/data back in response?

+ Sending the right data back requires using multiple features of the computer
  + Network socket - Receive and send back messages over the internet
  + Filesystem - that’s where the html/css/javascript code is stored in files
  + CPU - for cryptography and optimizing hashing passwords
  + Kernel - I/O management
+ Our dream - be able to use JavaScript to control this computer because
  1. we know JavaScript
  2. it has some really nice design decisions

+ Each programming language (PHP, Ruby, C++, JavaScript) have different levels of ability to interact with these features directly
  + C++ has many features that let it directly interact with the OS directly
  + JavaScript does not! So it has to work with C+ + to control these computer features. What is this combination known as? ... Node.js
  + JS -> Node -> Computer feature (e.g. network, file system)

### JavaScript, Node.js & the Computer

+ Rewind. We had better understand JavaScript to understand Node.js then
  + It’s a language that does 3 things (and 1 involves a lot of help from C++)
    1. Saves data and functionality (code)
    2. Uses that data by running functionality (code) on it
    3. Has a ton of built-in labels that trigger Node features that are built in C++ to use our computer’s internals

### Executing JavaScript Code Review

+ Let’s see the 2 things that JS does by itself - saving and using data

```js
let num = 3;
// 1. Save a function (code to run, parameters awaiting inputs)
function multiplyBy2 (inputNumber){
  const result = inputNumber*2;
  return result;
}
// 2a. Call/run/invoke/execute a function (with parens)
// and 2b. insert an input (an argument)
const output = multiplyBy2(num); // 我们使用函数名后加括号调用函数，这与给函数传参是分开的
const newOutput = multiplyBy2(10);
```

js只做了两件事，存储数据和方法，调用方法运行代码。

### Executing Node Code

+ So let’s see JavaScript other talent - built-in labels that trigger Node features
  + We can set up, with a JavaScript label, a Node.js feature (and so computer internals) to wait for requests for html/css/js/tweets from our users
  + How? The most powerful built-in Node feature of all: http(Hyper Text Transfer Protocol) (and its associated built- in label in JS - also http conveniently)

+ Using http feature of Node to set up an open socket

## Using Node APIs

### Calling Node with JavaScript

```js
const server = http.createServer() // 调用node引擎中的c++代码，创建一个socket接收http请求，并返回一个对象
server.listen(80) // 调用返回对象的listen方法，监听80端口 计算机总共有六万多个端口，这里设置为监听80端口
```

+ Inbound web request -> run code to send back message
+ if inbound message -> send back data
+ But at what moment?

+ Node auto-runs the code (function) for us when a request arrives from a user

### Calling Methods in Node

```js
function doOnIncoming(incomingData, functionsToSetOutgoingData){
    functionsToSetOutgoingData.end("Welcome to Twitter!")
}
const server = http.createServer(doOnIncoming)
server.listen(80)
```

1. We don’t know when the inbound request would come - we have to rely on Node to trigger JS code to run
2. JavaScript is single-threaded & synchronous. All slow work (e.g. speaking to a database) is done by Node in the background (more on this later)

### Calling a Function Under the Hood

+ Two parts to calling a function - executing its code and inserting input (arguments)
  + In multiplyBy2(3) the argument is 3 and we, the developer, inserted it
  + Node not only will auto-run our function at the right moment, it will also automatically insert whatever the relevant data is as the additional argument (input)
  + Sometimes it will even insert a set of functions in an object (as an argument) which give us direct access to the message (in Node) being sent back to the user and allows us to add data to that message

+ And that’s exactly what Node does with its http feature
  + Node inserts the arguments (inputs) automatically in the function it auto-runs:
    1. ‘Inbound object’ - all data from the inbound (request) message
    2. ‘Outbound object’ - functions to be used to set outbound (response) message data
  + These objects (the arguments to the auto-run function) aren’t given labels by Node. So how do we access them? We do so with parameters (placeholders).
  + We must make sure to format the function Node auto-runs the way Node expects (use docs)

### Creating a Server Summary

+ Code again

```js
function doOnIncoming(incomingData, functionsToSetOutgoingData){
    functionsToSetOutgoingData.end("Welcome to Twitter!")
}
const server = http.createServer(doOnIncoming)
server.listen(80)
```

+ People often end up using req and res for the parameters...

### Request & Response with Node

+ Let’s get more personalized with what we send back to our user from our server
  + By writing code to investigate the inbound message to see exactly what she’s asked for
  + Our user, needs a specific tweet (tweet 3) back. How does their browser tell us that?

+ Messages are sent in HTTP format - The ‘protocol’ for browser-server interaction

## Node with HTTP

### Preparing for HTTPRequestObject

+ HTTP message: Request line (url, method), Headers, Body (optional)

```js
const tweets = ["Hi", " ", "Hello", " ", " "]
function doOnIncoming(incomingData, functionsToSetOutgoingData){
    const tweetNeeded = incomingData.url.slice(8)-1 // 2
    functionsToSetOutgoingData.end(tweets[tweetNeeded])
}
const server = http.createServer(doOnIncoming)
/**
 * request line: GET /tweetw/3
 * Headers: meta data
 * Body: ...
 */
server.listen(80)
```

### Parsing HTTPRequestObject

[curl](https://curl.haxx.se/)

[postman](https://www.getpostman.com/)

在上面的代码中，我们

1. 声明了一个常量，并将一个数组赋值给常量。
2. 注册一个函数
3. 注册一个常量，使用http对象createServer()方法调用node打开一个http socket，并将函数传递为参数，等待返回结果，此时首先返回一个对象赋值给常量server
4. 使用上一步的常量对象的listen方法，将80作为参数传递进去，使得node监听80端口。
5. 如果有http请求，node监听到后会调用传递进create()方法中的函数，既2中的函数，并自动将两个参数传递给函数，既request和response两个对象。
6. 将这两个对象作为实参传递给函数后，执行2中函数体中代码。

### HTTP Response in Node

在上面函数体中，

1. 我们首先声明一个常量，数值为Request对象的url属性的第8个字符-1，也就是2
2. 调用response对象的end方法，将`tweets[tweetEnd]`的值也就是`'Hello’`作为实参传入，node会自动解析执行将数据返回到请求的客户端

### Response Headers

+ Our return message is also in HTTP format
  + We can use the body to send the data and headers to send important metadata
  + In the headers we can include info on the format of the data being sent back - e.g. it’s html so to load it as a webpage

### Intro to Require in Node

+ Getting access to Node’s built in features with require
  + We have to tell Node we want to have access to each of its C++ features independently - we get a built-in function to do this
+ require
  + `const http = require(‘http’); // common js语法`

### JavaScript Node Development

+ How do we start Javascript off to do all this?
  1. Write the code (VSCode et al)
  2. Load it into Node and run it (have to load in using the terminal interface)
  3. Need to reload our code with Node every time we make a change so nodemon

[nodemon](https://www.npmjs.com/package/nodemon)

### Cloud Node Development

+ Do we need an always-on computer in our house to run a server?
  1. Write code on your computer
  2. SSH into someone else’s computer (one of AWS’s)
  3. Set up DNS to match domain name to right IP

### Local Node Development

使用localhost(127.0.0.1)加端口号来进行本地调试

### Events & Error Handling

+ In server side development we get errors
  + Understandable - we’re interacting with others’ computers over the internet - there’s lots of issues that   could arise
  + How can we handle this? We need to understand our background Node http server feature better

+ What triggers the doOnIncoming function to run? Events
  + http.createServer(doOnIncoming) is actually a one-line version of setting up the server that will auto-release (‘emit’) events (‘messages in Node’) that trigger a function to auto-run if we’ve set one
  + These events are preset on http: ‘request’, ‘error’

+ Node will automatically send out the appropriate event depending on what it gets from the computer internals (http message or error)

```js
function doOnIncoming(incomingData, functionsToSetOutgoingData){
    functionsToSetOutgoingData.end("Welcome to Twitter")
}
function doOnError(infoOnError){
    console.error(infoOnError)
}
const server = http.createServer();
server.listen(80)
server.on('request', doOnIncoming)
server.on('clientError', doOnError)
```

### Event Handling in Node

在上述代码中，我们通过引入http这个模块，调用其中的createServer方法，这个方法返回一个对象，对象中有许多方法，用来更改node的行为。
我们调用其中的on方法来监听事件，调用listen方法来更改node所监听的端口。

### Modifying the Node Server

我们通过调用http.createServer()所返回对象的on方法来注册两个事件，分别为'request'和'clientError'，并将两个函数作为参数传入进去。

### Node Event Handling in Action

当有外部请求时，node会首先进行解析，当解析错误时，node会触发'clientError'事件，调用之前传入的函数，并将错误信息作为实参（argument）传入函数。此时js会解析函数并执行函数体中代码。

## File System

### Introducing the File System API

+ We have much of our twitter app set up now - handling, inspecting and responding to these messages (‘requests’) is the core of our app, of Node, and of servers
  + But, Node can do even more. We have an archive of tweets stored in a huge file (1.5GB)
  + Unfortunately they’re saved on our computer, not in our little JavaScript-specific data store (JavaScript memory)
  + Could we load them into JavaScript to run a function that removes bad tweets?
  + We can use fs to do so but there might be some issues with a file that large

### Importing with fs

+ Importing our tweets with fs

```js
function cleanTweets (tweetsToClean){
  // code that removes bad tweets
}
function useImportedtweets(errorData, data){
    const cleanedTweetsJson = cleanTweets(data);
    const tweetsObj = JSON.parse(cleanedTweetsJson)
    console.log(tweetsObj.tweet2)
}
fs.readFile('./tweets.json', useImportedtweets)
```

+ Every file has a ‘path’ (a link - like a domestic url)
+ JSON(JavaScript Object Notation) is a javascript-ready data format

### Reading from the File System with fs

与之前的http模块一样，通过引入fs模块，可以使用js语法来调用node来访问电脑中的文件。

我们最后调用fs的readFile方法，node会调用useImportedtweets方法来处理返回的数据，并自动将两个参数传递给useImportedtweets。其中第一个参数为错误信息（Error first），默认值为`null`，通过此模式，可以确保首先考虑错误发生时的处理。

### Call Stack Introduction

Call stack既调用栈，栈顶端既js当前运行的代码，当代码执行完毕时即从栈顶弹出。

## Streams

### Introducing Node Streams

+ What if Node used the ‘event’ (message- broadcasting) pattern to send out a message (‘event’) each time a sufficient batch of the json datahad been loaded in
  + And at each point, take that data and start cleaning it - in batches

```js
let cleanedTweets = "";
function cleanTweets (tweetsToClean){
  // algorithm to remove bad tweets from `tweetsToClean`
}
function doOnNewBatch(data){
    cleanedTweets += cleanTweets(data);
}
const accessTweetsArchive = fs.createReadStream('./tweetsArchive.json')
accessTweetsArchive.on('data', doOnNewBatch);
```

使用createReadStream方法，默认每读取64kb数据就进行处理。

### Setting Up the Stream

在上述代码中，我们声明了一个常量accessTweetsArchive，赋值为fs.createReadStream的运行结果，fs.createReadStream会返回一个对象，对象内有着许多通知node操作计算机的方法。

然后我们使用返回对象的on方法来监听’data‘事件，事件发生时使用doOnNewBatch函数处理。

### Processing Data in Batches

与浏览器环境一直，node中有着callback queue，上述代码中，每次触发’data‘时间就将一个doOnNewBacth推入callback queue中，在call stack清空后将队列首个回调推入调用栈，在调用栈请空前（既首个doOnNewBacth执行完之前）不会将其他函数推入调用栈。

### Checking the Callback Queue

js是单线程语言，通过调用node的一些方法可以使用node进行多线程操作，并结合node的event loop来协调线程间的配合。

## Asynchornicity in Node

### Introduction to Asynchronicity

+ The call stack, event loop and callback queue in Node
  + Call stack: JavaScript keeps track of what function is being run and where it was run from. Whenever a function is to be run, it’s added to the call stack
  + Callback queue - any functions delayed from running (and run automatically by Node) are added to the callback queue when the background Node task has completed (or there’s been some activity like a request)
  + Event loop - Determines what function/code to run next from the queue(s)

+ Bringing it all together
  + But Node is most powerful because of the automated JS function execution triggered by Node at just the right moment.
  + This means we don’t have to wait in JS for the right moment to run code and block any other code running
  + But it also means we better know intimately how Node decides what to automatically execute at what moment...

+ The event loop is very strict. What rules does it set for what code to run next and when it may run?

```js
function useImportedtweets(errorData, data){
    const tweets = JSON.parse(data)
    console.log(tweets.tweet1)
}
function immediately(){console.log("Run me last   ")}
function printHello(){console.log("Hello")}
function blockFor500ms(){
  // Block JS thread DIRECTLY for 500 ms
  // With e.g. a for loop with 5m elements
}
setTimeout(printHello,0)
fs.readFile('./tweets.json', useImportedtweets)
blockFor500ms()
console.log("Me first")
setImmediate(immediately)
```

### Timer Queue

在上面代码中，我们首先声明了几个函数。然后调用了setTimeout方法，将printHello方法传入进去，并将0作为第二个参数传入。此时代码继续执行，在node后台开始计时，并在计时结束后将printHello推入timer queue。

setTimeout执行完毕后代码执行到下一行，使用了node的fs的readFile方法，传入了文件地址和回调函数useImportedtweets作为参数，此时js代码会执行下一行，在node后台，会开始读取文件。

然后js调用栈推入下一行代码，执行blockFor500ms，此时主线程花费一定时间执行此函数。执行完毕后将其推出调用栈。

### IO Queue

假设在blockFor500ms执行期间，node后台读取文件完毕，将错误信息和文件数据作为实参传递给了useImportedtweets函数。此时useImportedtweets并不会立即推入调用栈，而是会推入IO Queue等待执行。

blockFor500ms执行完毕后，调用栈执行`console.log("Me first")`，此时控制台输出"Me first"。

### Check Queue

调用栈之后会执行setImmediate方法，此方法会将回调函数推入Check Queue，与其名字相反，setImmediate所推入check queue的回调并不会立即执行，而已排在所有queue的优先序列的最后，既其中的回调会在其他queue清空后才会执行。

此时调用栈清空，event loop会首先开始执行timer queue中的回调函数，既printHello，此时控制台输出"Hello"。

### Event Loop Completion

printHello执行完毕后调用栈清空，此时event loop检查timer queue是否清空，清空后还是执行IO Queue中的回调函数，此时useImportedtweets推入调用栈，并添加了错误信息及文件数据作为实参。执行中控制台输出了`tweets.tweet1`中的数据，假设为”Hello“。此时调用栈，IO Queue清空。

Event Loop最后将check queue中的回调推入调用栈，调用栈执行immediately函数，控制台输出"Run me last   "。

代码执行完毕。

### Microtask & Close Queues

在使用promise时，我们会传入resolve和reject方法，在promise的状态更新时会自动调用这两个回调中的一个来处理数据。promise的回调并不会传入上述的队列中，而是会传入Microtask Queue。微任务队列的优先级高于上述几个队列，可以认为优先级为0。

在Event Loop检查每个队列之前，都会先检查Microtask Queue，并优先执行微任务队列中的方法。

微任务队列中有两个子队列，其中process.nextTick中传入的回调函数会传入第一个队列，promise中的回调会传入第二个队列。

还有一个队列叫做Close Queue，事件完成时的回调函数会推入此队列。

### Priority of Queue Execution

+ Rules for the automatic execution of the JS code by Node
  1. Hold each deferred function in one of the task queues when the Node background API ‘completes’
  2. Add the function to the Call stack (i.e. execute the function) ONLY when the call stack is totally empty (Have the Event Loop check this condition)
  3. Prioritize functions in Timer ‘queue’ over I/ O queue, over setImmediate (‘check’) queue

