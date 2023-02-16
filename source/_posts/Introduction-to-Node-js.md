---
title: Introduction to Node.js
date: 2019-09-02 14:29:29
categories: [学习笔记]
tags: [FrontEndMasters, JavaScript, Node.js]
---
## Introduction

### What is Node.js

> tldr; Environment to run JavaScript outside a browser

+ Open source runtime
+ Built on Chrome's V8 JavaScript engine
+ Created by Ryan Dahl in 2009
+ Evolved since creation to allow developers to build almost anything

[nodejs](https://nodejs.org/en/)

[v8](https://v8.dev/)
<!-- more -->
### What can I create with Node.js

tldr; Pretty much anything a scripting and server language like python or ruby can, but with JavaScript

+ Tooling (build, automation, etc)
+ API's (REST, Realtime, etc)
+ CDNs
+ Shareable libraries
+ Desktop applications 💯😎🎉👌🏾
+ IOT(Internet of Things)
+ Pretty much anything because node is on everything now

### Installing Node.js

tldr; Use NVM(Node Version Manager)

[GitHub NVM](https://github.com/nvm-sh/nvm#installation)

`curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash`

`nvm install node`

### Running Node.js Code

tldr; REPL for playing around, cli for everything else

+ Interactive REPL
  + Great for trying things out, think console on the browser
  + Just type the node command with no args and begin

+ CLI Executable
  + Use to execute your node apps
  + `node path/to/your/file.js`

## Node.js Modules

### Browser JavaScript vs Node.js

| Browser                            | Node.js                            |
| ---------------------------------- | ---------------------------------- |
| Bulid Interactive apps for the web | Bulid server side apps and scripts |
| DOM                                | Nope, no GUI(can virtualize)       |
| Window                             | No window, but does have a global  |
| Fragmentation                      | Versioned, so no fragmentation     |
| Optional modules(es6)              | Required modules(commonjs+)        |
| Cann't not access filesystem       | Can access filesystem              |
| Async                              | Async                              |
|                                    | No browser based APIs              |

### Globals in Node.js

tldr; Nodejs gives you helpful globals, but just like the browser, you should not create your own

+ process
  + has information about the environment the program is running in
+ require
  + function to find and use modules in current module
+ __dirname
  + the current directory path
+ module
  + information about current module, methods or making module consumable
+ global
  + like window, its the "global" object. Almost **NEVER** use this
+ .... many more

### Node.js Module

+ What are modules?
  + tldr; Encapsulated code

node会自动将文件转换为模块，类似下面代码

```js
var module1 = (function(exports, require, module, __filename, __dirname){
  // your node js code in a file
})

var module2 = (function(exports, require, module, __filename, __dirname){
  // your node js code in another file
})
```

可以看到默认只传入五个参数，那么我们是如何调用其他全局方法或变量的呢？

可以理解为类似浏览器有个兜底对象windows，node有个兜底对象global。没有传入的方法和属性都挂载在上面，而上面五个变量会因为每个module实例的不同而不同(例如文件路径，模块路径等)，所以需要单独传入。

+ Modules in Node.js
  + tldr; CommonJs
+ NodeJs uses commonJs for its module system.
+ There are other module systems out there like:
  + ESM (ecmascript modules) *new standard*
  + AMD (pretty much dead)
  + ... others, but don't matter at all

### Creating Node Modules

+ Creating modules
  + tldr; Regular code, just export it

+ All your Nodejs code are modules.
+ As the author, you decide how and what to expose from your modules, to other modules.
+ You do this with the module global object provided you by the Nodejs runtime

```js
module.exports = {}
```

### Importing Node Modules

+ Using modules
  + tldr; require
+ The Nodejs runtime injects another global, require.
+ This function takes a relative path to the module that you want to consume, and synchronously loads it by returning whatever the target module exported.

```js
const myModule = require('./myModule')
```

## Internal Modules & npm

### The fs Module

+ Shipped modules
  + tldr; Node.js ships with a bunch of helpful modules already

+ fs - fileSystem module to interacting with files on a machine
+ http - low level-ish module for creating network based programs, like APIs
+ path - useful for mainpulating path strings and handling differences across many OS's
+ ...many more

```js
const fs = require('fs')
```

node内核自带模块引入时不需要加路径参数。

### Other Internal Node Modules

### Remote Modules

tldr; download and use other modules from the internet

+ Node.js has grown a bunch, and a bunch of that growth is due to its community and the ability to share modules and consume them at will
+ You can slap together an app really fast by reusing public modules. Which are the same as the modules you make, but packaged for downloading
+ This sounds nice, but now you have to be aware of malicious code. Also, you need a system to help with the management ogf remote modules(downloading, publishing, updating, etc)

### Modules Recap

+ Three module types, on require
  + tldr; modules you created are always relative paths.".js" is implied.

Custom local modules

```js
const lib = require('../rel/path/to/lib') // 相对路径
```

Remote modules

```js
const lib = require('lib') // 与在npm进行安装时同样的名称
```

Shipped modules

```js
const fs = require('fs') // 内置模块，直接用模块名称
```

### npm

tldr; CLI(Command Line Interface) to manage remote modules

+ Ships with Node.js
+ Allows you to publish, download, and update modules
+ Use `package.json` file in your Node.js project to determine dependedcies
+ Store remote modules in the `node_modules` folder
+ ...whole bunch of other stuff

### yarn

[yarn](https://yarnpkg.com/en/)

## Asynchronous Node.js

### Asynchronous Code in Node.js

+ Async code
  + tldr; Node.js is single threaded and async like the browser, but you'll probably do more async things

+ Node.js is single threaded and event based. Just like the browser
+ Unlike the browser, your Node.js app will be shared by all clients
+ You now have consider CPU intensive tasks that block the single thread(while loops)

### Asynchronous Patterns

+ Async patterns
  + tldr; async/await is legit

+ callback parrern

```js
doAsyncThing((err, result) => { /* do somethins */ })
```

+ promises

```js
doAsyncThing()
  .then(result => {})
  .catch(err => {})
```

+ async/await

```js
const run  = async() => {
  const result = await doAsyncThing() // must return a promise
  console.log(result)
}
```

### Error handling

tldr; Errors kill your app, just like the browser

+ Any thrown or unhandled errors will cause the process to crash and exit
+ Your app may have errors that should not cause a crash, so you must handle accordingly

### Servers

tldr; one server, handling many requests from clients

+ A server's job is to handle a request from some sort of client(browser, mobile app, another server, etc)
+ Without considering scaling, one server instance will handle many client requesets.Compared to a client app where that code only cared about itself on the host machine.
+ Node.js has built in and community packages for build all types of servers(API's, static, realtime, etc)

## Debugging & Testing

### Debugging Node

+ Debugging
  + tldr; just like chrome

+ Level 1
  + Use `console.log` to log your way through fixing your app. In production, record your logs
+ Level 2
  + Use the node inspector when starting your app and debug just like you would an browser app in chrome
+ Level 3
  + Text editor integration offers the most seamless experience

### Testing Node Libraries

+ Testing
  + tldr; export you modules and use a testing framework

+ Before you can test your code, make sure it is testable. As long as you can export what you want to test, you should be able to test it. There are other concerns specific to what libraries and frameworks you use
+ You can test pretty much antthing in Node.js. Browser apps, API's, CLI's, scripts, tools, etc. Your test themselves will be executed in Node.js, so they have the abality to pretty much do anything.

### Anatomy of Tests

tldr; many tools for the same job

+ Your code to be tested
+ Test Suite - responsible for helping organize you tests, provide hooks, and overall envionment
+ Assertion Library - does the actual comparisons in your test
+ Mocks + Spies - tools to help you test your code without testing other code or actually running your code(mock out api calls, check to see if an internal function was called)

### Type of Tests

tldr; everything can be tested with Node

+ unit
+ integration
+ end-to-end
+ UI
+ snapshot
+ performance
+ ...so many more

## Publishing and Deploying

### Sharing Modules

tldr; push code to github, publish to NPM

+ Sharing your modules are easy. Just a few things you have to check:
  + add `node_modules` to gitignore
  + declare remote modules as devDependencies if you only need them to develop with(like jest)
  + think about how your app will be used and what dependencies the host app might have, you don't want to include another version(although NPM tries ti fix this for you)
  + is this private or public?

### Deploying Your Servers

tldr; follow cloud provider instructions, and never hard code secrets

+ Each cloud provider has it's own method of deploying your apps on their platforms. Most of them have some integration with github or a CLI to assist.

+ Few things to remember
  + remove secrets and use environment vars
  + setup a CI flow for your app
  + make sure you are developing with the same version of node you are deploying to

### Futher Tools & Resources

[express](https://expressjs.com/en/guide/routing.html)
[commander](https://github.com/tj/commander.js/)
[Gatsby](https://www.gatsbyjs.org/)
[Nuxt](https://nuxtjs.org/)
[Next.js](https://nextjs.org/)
