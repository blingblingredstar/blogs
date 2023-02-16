---
title: web-performance-with-webpack
date: 2019-12-15 16:21:33
categories: 学习笔记
tags: [FrontEndMasters, performance, webpack, code splitting]
---

## Web Performance

### Top Performance Issues

+ Top three causes of web performance issues
  1. Amount of JavaScript for initial download
  2. Amount of CSS for initial download
  3. Amount of network request on initial download

### Performance Goals

+ Goals
  + <=200kb(uncompressed) initial JavaScript [total].
  + <=100kb(uncompressed) initial CSS [total].
    + HTTP: <=6 initial network calls
    + HTTP/2: <=20 initial network calls
  + 90% code coverage(only 10% code unused)
<!-- more -->
### Code Coverage
<!-- markdownlint-disable md033 -->
在Chrome开发者工具中按下<kbd>shift</kbd> + <kbd>CMD</kbd> + <kbd>p</kbd>，输入coverage，即可查看代码加载时的使用率。

## Code Splitting

+ Code splitting
  + Process of splitting pieces of your code into async chunks **at build time**.

### Types of Code Splitting

+ Why should care about code splitting
  + The future of web is mobile
  + The average mobile website takes 14 seconds to get interactive
  + Load less code => interactive faster

+ Two types of code splitting
  1. Static
  2. "Dynamic"

+ Static code splitting
  + When to use:
    + "Heavy" JavaScript
      + 例如之后才会用到的一些第三方包(three.js之类)
    + Anything temporal
      + 像弹框等只有满足条件才可见的
    + Routes
      + 只加载用户可见的路由页面

### Code Splitting Demonstration

```js
import Listener from "./listener.js";

const getModal = () => import("./src/modal.js");

Listener.on("didSomethingToWarrantModalBeingLoaded", () => {
  getModal().then(module => {
    const modalTarget = document.getElementById("Modal");
    module.initModal = modalTarget;
  });
});
```

例如我们要实现一个点击加载footer模块的功能，简单实现就是先引入，在点击之后插入DOM。或者我们也可以使用Dynamic import，代码类似如下:

```js
button.addEventListener('click', e => {
  import('./footer').then(footerModule => {
    document.body.appendChild(footerModule.footer)
  })
})
```

### Webpack Code Splitting Under the Hood

当我们使用动态引入之后，分析打包后生成的文件可见不止一个js文件，而是额外生成了动态引入的js文件。

分析打包后的代码可见，webpack会将依赖以数组的形式进行缓存，在使用动态引入时，会在数组加载完毕新的包后进行异步操作。

### Load a Heavy Module Asynchronously

加入我们需要引入GSAP，引入后分析打包文件体积可见增加了几百kb。

我们可以使用如下语法来异步引入

```js
const getGSAP = () => import('gsap')
```

此时再进行打包可见整个GSAP库以被单独放入一个打包文件内。

### Code Splitting in Vue, React & Frameworks

使用Vue时，我们只需在引入组件时将组件引入形式更改为异步引入，而无需更改其他代码即可。

使用React，我们可以使用React Loadable库。

### Code Splitting Named Exports

截止到此workshop时，webpack只能异步引入使用export default导出的模块，所以无法tree shaking。

加入我们要引入lodash其中一个方法，我们只能使用如下语法

```js
const getUnique = () => import('lodash-es/unique')
```

### Vendor Bundlers are an Anti Pattern

有时我们会使用缓存来缓存所有文件并希望能提高加载速度。但是用网络缓存只能减少网络请求时间，js的解析执行仍需要一段时间。作者认为提升性能的主要措施还是减少首次加载时的文件体积。

### Dynamic Code Splitting

此处的”动态“代码分离并不是真的”动态“，而是一个技巧。

```js
const getTheme = themeName => import(`./src/themes/${themeName}`);

if (window.feeling.stylish) {
  getTheme("stylish").then(module => {
    module.applyTheme();
  });
} else if (window.feeling.trendy) {
  getTheme("trendy").then(module => {
    module.applyTheme();
  });
}
```

上面的代码中基于运行时的状态引入了异步模块。但是webpack在打包时仍会将src下的themes中所有文件进行打包，只是在引入时”动态“引入。

+ When to use:
  + A/B testing
  + Theme
  + Condition

## Module Methods & Magic Comments

### Introducing Magic Comments

[docs](https://webpack.docschina.org/api/module-methods/#magic-comments)

当我们将文件打包之后debug并不方便，所以增加了magic comments的功能。

```js
）
require（AMD 版本）
标签模块(Labeled Modules)
export 标签
require 标签
webpack
require.context
require.include
require.resolveWeak
模块变量
PLUGINS
Plugin API
compiler 钩子
compilation 钩子
resolver
parser
腾讯云技术社区
模块方法
查看原文|编辑此页
本节涵盖了使用 webpack 编译代码的所有方法。在 webpack 打包应用程序时，你可以选择各种模块语法风格，包括 ES6, CommonJS 和 AMD。

虽然 webpack 支持多种模块语法，但我们建议尽量遵循一致的语法，避免一些奇怪的行为和 bug。这是一个混合使用了 ES6 和 CommonJS 的示例，但我们确定还有其他的 BUG 会产生。
ES6（推荐） 
webpack 2 支持原生的 ES6 模块语法，意味着你可以无须额外引入 babel 这样的工具，就可以使用 import 和 export。但是注意，如果使用其他的 ES6+ 特性，仍然需要引入 babel。webpack 支持以下的方法：

import 
通过 import 以静态的方式，导入另一个通过 export 导出的模块。

import MyModule from './my-module.js';
import { NamedExport } from './other-module.js';
这里的关键词是静态的。标准的 import 语句中，模块语句中不能以「具有逻辑或含有变量」的动态方式去引入其他模块。关于 import 的更多信息和 import() 动态用法，请查看这里的说明。
export 
默认导出整个模块，或具名导出模块

// 具名导出
export var Count = 5;
export function Multiply(a, b) {
  return a * b;
}

// 默认导出
export default {
  // Some data...
};
import() 
import('path/to/module') -> Promise

动态地加载模块。调用 import() 之处，被作为分离的模块起点，意思是，被请求的模块和它引用的所有子模块，会分离到一个单独的 chunk 中。

ES2015 loader 规范 定义了 import() 方法，可以在运行时动态地加载 ES2015 模块。
if ( module.hot ) {
  import('lodash').then(_ => {
    // Do something with lodash (a.k.a '_')...
  });
}
import() 特性依赖于内置的 Promise。如果想在低版本浏览器使用 import()，记得使用像 es6-promise 或者 promise-polyfill 这样 polyfill 库，来预先填充(shim) Promise 环境。
Magic Comments 
Inline comments to make features work. By adding comments to the import we can do things such as name our chunk or select different modes. For a full list of these magic comments see the code below followed by an explanation of what these comments do.

// 单个目标
import(
  /* webpackChunkName: "my-chunk-name" */
  /* webpackMode: "lazy" */
  'module'
);

// 多个可能目标
import(
  /* webpackInclude: /\.json$/ */
  /* webpackExclude: /\.noimport\.json$/ */
  /* webpackChunkName: "my-chunk-name" */
  /* webpackMode: "lazy" */
  /* webpackPrefetch: true */
  /* webpackPreload: true */
  `./locale/${language}`
);
```

在webpack.config.js的output中可以添加配置如下

```js
output: {
  chunkFilename: "[name].lazy-chunk.js"
}
```

### Webpack Modes

动态导入的模块的模式，目前共四种

+ lazy
+ lazy-once
+ eager
+ weak

### Webpack Prefetch & Preload

[medium article](https://medium.com/webpack/link-rel-prefetch-preload-in-webpack-51a52358f84c)
