---
title: Vanilla JS - You Might Not Need a Framework
date: 2023-08-07 21:53:46
categories: 学习笔记
tags: [FrontEndMasters, Vanilla JS]
---

# Vanilla JS - You Might Not Need a Framework

## Introduction

- What we'll cover > 我们将会学习什么

  - Vanilla JS > 原生 JS
  - DOM API
  - Fetch
  - Design Patterns > 设计模式
  - Single page applications > 单页应用
  - Web Components
  - Reactive programming > 响应式编程
  - Routing > 路由

- Pre-requisites > 先决条件

  - [firtman.github.io/vanilla](https://firtman.github.io/vanilla/)

- [Course slides](https://firtman.github.io/vanilla/slides.pdf)
<!-- more -->

## Vanilla JS

### What is Vanilla JavaScript

- Help you avoid overusing frameworks like React, Vue, Angular, etc. > 帮助你避免过度使用 React、Vue、Angular 等框架

  - When you have a hammer, everything looks like a nail. > 当你有了一把锤子，所有东西看起来都像钉子

- Vanilla JavaScript > 原生 JavaScript

  - The usage of the core language and browser APIs to create web apps without any additional libraries or frameworks added on the top. > 使用核心语言和浏览器 API 来创建 web 应用，而不需要在顶部添加任何其他库或框架

- Vanilla comes from Ice Cream, meaning plain or without any extras. > Vanilla 来自冰淇淋，意思是普通的或没有任何额外的东西

### Why Vanilla JavaScript

- Why do we need to care about VanillaJS? > 为什么我们需要关心 VanillaJS？

  - Add on more tool to your toolbox. > 给你的工具箱添加更多的工具
  - Understand what your library is doing. > 了解你的库在做什么
  - Extend your library with plugins. > 使用插件扩展你的库
  - To be a better web developer. > 成为更好的 web 开发者
  - To mix with libraries. > 与库混合使用
  - To use it! > 使用它！
    - You can create simple and fast web apps with no CLI, no build process. > 您可以创建简单快速的 web 应用程序，无需 CLI，无需构建过程

- Main advantages of Vanilla JS > 原生 JS 的主要优势

  - Lightweight > 轻量
  - Control and Power > 控制和能力
  - Simplicity > 简单
  - Flexibility > 灵活性
  - Performance > 性能
  - No node_modules > 没有 node_modules

- Main fears of VanillaJS > 原生 JS 的主要担忧

  - Routing for single page applications > 单页应用的路由
  - Too verbose and time consuming > 太啰嗦和耗时
  - State management > 状态管理
  - Template > 模板
  - Complexity > 复杂性
  - Reusable Components > 可复用组件
  - Maintenance > 可维护性
  - Learning curve > 学习曲线
  - Browser compatibility > 浏览器兼容性
  - Reinventing the wheel every time > 重复造轮子
  - Scalability > 可扩展性

- Learn the tool and use it when it's the best options. > 学习和使用工具，当它是最好的选择时
- We won't advocate for using Vanilla JS on every project. > 我们不会主张在每个项目中都使用原生 JS

### The DOM API

- In this workshop, we'll be using different techniques and design patterns in parallel. > 在这个工作坊中，我们将同时使用不同的技术和设计模式

#### DOM Essentials

The Document Object Modal connects web pages to JavaScript by representing the structure of a document in memory. > 文档对象模型通过在内存中表示文档的结构来将 web 页面连接到 JavaScript

- DOM API

  - A browser API exposed to developers to manipulate the DOM from an scripting language. > 暴露给开发人员的浏览器 API，用于使用脚本语言操作 DOM

- The DOM API is available on many objects > DOM API 可用于许多对象上

  - `window` global object
  - `document` object
  - One object per HTML element and other nodes in your document

- Each element is represented by an object of HTMLElement interface or other interface that inherit it. > 每个元素由 HTMLElement 接口的对象或继承它的其他接口表示
- They have instance properties and methods. > 它们有实例属性和方法
- Changes in properties or children will trigger updates in the user interface **when you release the thread**. > 属性或子元素的更改将在释放线程时触发用户界面的更新
- We can listen to events happening in that element and react in consequence. > 我们可以监听发生在该元素中的事件，并做出相应的反应

- To work with DOM elements, we can > 要使用 DOM 元素，我们可以
  - Pick them from the current DOM > 从当前 DOM 中选择它们
  - Create them, and then inject them into the DOM > 创建它们，然后将它们注入到 DOM 中
- When we have a reference to an Element, we can > 当我们持有一个元素的引用时，我们可以
  - Read its content > 读取它的内容
  - Change its content > 改变它的内容
  - Remove it > 删除它
  - Add new elements into it > 向其中添加新元素

### Finding Elements in the DOM

- By id - `document.getElementById('id')`
- By class name - `document.getElementsByClassName('class')`
- By name - `document.getElementsByName('name')`
- By CSS selector - `document.querySelector('selector')`
- Navigating DOM structure - `element.parentElement`...

- When selecting elements, some functions return...

  - One HTML Element(HTMLElement) > 一个 HTML 元素
  - A live HTML Collection(HTMLCollection) > 一个活动的 HTML 集合
  - Static Element collection(NodeList) > 静态元素集合

- When you use a function that returns one element, it can also return null if no node was found. > 当你使用返回一个元素的函数时，如果没有找到节点，它也可以返回 null
- When you use a function that returns multiple elements, it can also return an empty collection if no elements were found. > 当你使用返回多个元素的函数时，如果没有找到元素，它也可以返回一个空集合
- `HTMLCollection`(live) don't have all the modern Array interface, such as `filter`, `map`, `reduce`, etc. > `HTMLCollection`(live) 没有所有现代数组接口，如 `filter`、`map`、`reduce` 等
- `NodeList`(static) have all the modern Array interface, such as `filter`, `map`, `reduce`, etc. > `NodeList`(static) 有所有现代数组接口，如 `filter`、`map`、`reduce` 等

- With an HTML element in JavaScript, you can > 用 JavaScript 中的 HTML 元素，你可以
  - Read and change attributes' values > 读取和更改属性的值
  - Read and change styles > 读取和更改样式
  - Hook event listeners > 挂载事件监听器
  - Add, remove or move children > 添加、删除或移动子元素
  - Read and change content > 读取和更改内容
  - More...

### Modifying DOM

- How to read or change attributes of a DOM element > 如何读取或更改 DOM 元素的属性

  - You can use dot syntax to access properties mapped from HTML attributes. > 你可以使用点语法来访问从 HTML 属性映射的属性
    - Most of the time, the property name is the same as the attribute name. > 大多数情况下，属性名与属性名相同
    - Some properties are different, such as `class` and `className`. > 有些属性是不同的，如 `class` 和 `className`
  - You can use dot syntax to access a style object. > 你可以使用点语法来访问样式对象
    - That object will have a map to every CSS property you can inline in HTML. > 该对象将有一个映射到你可以在 HTML 中内联的每个 CSS 属性
  - You can use dot syntax and bind a function to an event name using `addEventListener`. There is a list of standard events. > 你可以使用点语法和 `addEventListener` 将函数绑定到事件名。有一个标准事件列表

- Accessing and Editing Contents of Elements > 访问和编辑元素内容
  - Accessing `textContent` > 访问 `textContent`
  - Accessing `innerHTML` > 访问 `innerHTML`
  - Using DOM APIs to create new nodes > 使用 DOM API 创建新节点

## The DOM

### Course Project Overview

- Our Project

  - APP: Coffee Master
  - Web app for a coffee shop
  - Menu of products
  - Details for each product
  - Order with Cart
  - HTML and CSS provided
  - Data in JSON
  - No JavaScript written yet

- HTML and DOM are not same thing exactly. > HTML 和 DOM 不是完全相同的东西

  - HTML is a markup language, plain text. > HTML 是一种标记语言，纯文本
  - DOM is a representation of the HTML document in memory. > DOM 是 HTML 文档在内存中的表示

- HTML parser will auto insert the first visible element into the `document.body` > HTML 解析器将自动将第一个可见元素插入到 `document.body` 中

```HTML
<script></script>
<title>Title</title>
<h1>Heading</h1>
<!-- Will parse to -->
<head>
  <script></script>
  <title>Title</title>
</head>
<body>
  <h1>Heading</h1>
</body>
```

### Scoping querySelector

```js
// Search in the whole document
const header = document.querySelector('h1');
// Search in the head
header.querySelector('h1');
```

### Adding Scripts Async & Defer

- Old school - put script tag in the bottom of body > 将 script 标签放在 body 底部

```HTML
<body>
  <h1>Heading</h1>
  <script src="app.js"></script>
</body>
```

- Async - load script asynchronously > 异步加载脚本

```HTML
<head>
  <script async src="app.js"></script>
</head>
<body>
  <h1>Heading</h1>
</body>
```

- Defer - load script after HTML parsing > 在 HTML 解析后加载脚本

```HTML
<body>
  <h1>Heading</h1>
  <script defer src="app.js"></script>
</body>
```

- If you are not sure, use defer. > 如果你不确定，使用 defer
  - Async will execute the script as soon as it is downloaded. It will block the HTML parsing. > 异步将在下载脚本后立即执行脚本。它将阻塞 HTML 解析
  - Defer will execute the script after the HTML parsing is done. > Defer 将在 HTML 解析完成后执行脚本

### Main Script Setup

```js
window.addEventListener('DOMContentLoaded', () => {
  console.log('DOM fully loaded and parsed, can manipulate DOM here');
});
```

### Event Binding & Handlers

- Basic: load, click, dbclick
- Value: change
- Keyboard: keyup, keydown, keypress
- Mouse: mouseup, mousedown, mousemove, mouseenter, mouseleave
- Pointer and Touch: pointerup, pointerdown, pointermove, pointerenter, pointerleave, touchstart, touchend, touchmove
- Focus: focus, blur, focusin, focusout
- Scroll: scroll
- Special: DOMContentLoaded, popstate in window, submit in form, resize in window, hashchange in window
- More: https://developer.mozilla.org/en-US/docs/Web/Events

- The spec's naming pattern is to use lowercase with no word separator. > 规范的命名模式是使用小写字母而没有单词分隔符

  - `webkitcurrentplaybacktargetiswirelesschanged`

- Two way to bind event handlers > 两种绑定事件处理程序的方法
  - Inline like `element.onclick = () => {}`
  - Using `addEventListener` > 使用 `addEventListener`
- `addEventListener` can bind multiple handlers to the same event. > `addEventListener` 可以将多个处理程序绑定到同一事件
- Inline event handlers can only bind one handler to the same event. > 内联事件处理程序只能将一个处理程序绑定到同一事件

### Advanced Event Handling

- Event Options > 事件选项
  - `capture` - the event will be captured on the way down the DOM tree. > 事件将在 DOM 树下行时捕获
  - `once` - the event will only be fired once. > 事件只会触发一次
  - `passive` - the event will not call `preventDefault`, so it will not block the browser from doing its default behavior. Better not change the DOM in this handler. > 事件不会调用 `preventDefault`，因此它不会阻止浏览器执行其默认行为。最好不要在此处理程序中更改 DOM
- Dispatch Custom Events > 分派自定义事件
  - `new Event('name')`
  - `element.dispatchEvent(event)`

### Helpful Utils

```js
const $ = (...args) => document.querySelector.call(this, ...args);
const $$ = (...args) => document.querySelectorAll.call(this, ...args);
HTMLElement.prototype.$ = (...args) => this.querySelector.call(this, ...args);
HTMLElement.prototype.$$ = (...args) =>
  this.querySelectorAll.call(this, ...args);
HTMLElement.prototype.on = (...args) =>
  this.addEventListener.call(this, ...args);
HTMLElement.prototype.off = (...args) =>
  this.removeEventListener.call(this, ...args);
```

## Routing

### Browser Routing & History API

- SPA: How to change content > 如何更改内容

  - Remove Previous page and inject new page into the DOM > 删除上一页并将新页注入到 DOM 中
  - Hide Previous page and show new page > 隐藏上一页并显示新页

- Single page application and router > 单页应用和路由
  - We want to change the content of the page based on what user select: > 我们想根据用户的选择更改页面的内容
    - Homepage: menu
    - Detail page: product detail
    - Order: cart and order form
  - We won't have multiple HTML file, we will use the DOM APIs and web components. > 我们不会有多个 HTML 文件，我们将使用 DOM API 和 web 组件
  - We will use the history API to push the new entries to the navigation history. > 我们将使用 history API 将新条目推送到导航历史记录中

```js
history.pushState(state, title, url);

window.addEventListener('popstate', () => {
  // Handle back button
});
```

- Note: popstate won't be fired if the user clicks on an external link or change the URL manually. > 注意：如果用户单击外部链接或手动更改 URL，则不会触发 popstate

- If you are creating a SPA, configure your server properly and check the URL when the whole page loads for the first time. > 如果您正在创建 SPA，请正确配置服务器并在第一次加载整个页面时检查 URL

- Make the router reuseable by receiving a collection of routes(path as a regex and component to render). > 通过接收路由集合（路径作为正则表达式和要渲染的组件）使路由器可重用

## Web Components

### Overview & Custom Elements

#### Web Component

- A modular, reuseable building block for web development that encapsulates a set of related functionality and user interface elements. > 一种模块化、可重用的 web 开发构建块，它封装了一组相关的功能和用户界面元素
- Compatible with every browser. > 与每个浏览器兼容
- It's actually a set of standards. > 它实际上是一组标准
  - Custom Elements > 自定义元素
  - HTML Templates > HTML 模板
  - Shadow DOM > Shadow DOM
  - Declarative Shadow DOM > 声明式 Shadow DOM
- It's similar to the idea of components on most of the library for JavaScript. > 它类似于大多数 JavaScript 库的组件思想
- We have freedom of choice on how to define them and use them. > 我们可以自由选择如何定义它们和使用它们

#### Custom Elements

A way to define new, reuseable HTML elements with custom behavior and functionality using JavaScript. > 使用 JavaScript 定义具有自定义行为和功能的新的可重用 HTML 元素的方法

```js
class MyElement extends HTMLElement {
  constructor() {
    super();
    // Define your element's behavior here
  }
}

customElements.define('my-element', MyElement);
```

```html
<my-element></my-element>

<script>
  document.createElement('my-element');
</script>
```

```js
class MyElement extends HTMLElement {
  constructor() {
    super();
    // Define your element's behavior here
  }
  connectedCallback() {
    // Called when the element is connected to the DOM
  }
  disconnectedCallback() {
    // Called when the element is disconnected from the DOM
  }
  attributeChangedCallback(name, oldValue, newValue) {
    // Called when an attribute is added, removed, updated, or replaced
  }
  adoptedCallback() {
    // Called when the element is moved to a new document
  }
  static get observedAttributes() {
    // Return an array of attribute names to observe
  }
}
```

### HTML Template

Fragments of markup that can be cloned and inserted into the document at runtime, with reuseable HTML content that can be rendered and modified dynamically. > 可以在运行时克隆并插入到文档中的标记片段，具有可重用的 HTML 内容，可以动态地渲染和修改

```html
<template id="my-template">
  <h1>Heading</h1>
</template>
```

```js
const template = document.getElementById('my-template');
const clone = template.content.cloneNode(true);
document.body.appendChild(clone);
```

By default, the nodes of our custom element are part of the same page's DOM, so CSS style declaration applies to all the document. > 默认情况下，我们自定义元素的节点是同一页面 DOM 的一部分，因此 CSS 样式声明适用于整个文档

### Shadow DOM

A private, isolated DOM tree within a web component that is separate from the main document's DOM tree. > 一个私有的、与主文档 DOM 树分离的 web 组件内部的隔离 DOM 树

- Allows more control over styling and encapsulation of functionality of a Custom Element. > 允许更多地控制自定义元素的样式和功能的封装

- By default, CSS declared in the main DOM won't be applied to the Shadow DOM. > 默认情况下，主 DOM 中声明的 CSS 不会应用于 Shadow DOM

- There are new pseudo-classed and pseudo-element to allow communication between DOMs in stylesheets. > 有新的伪类和伪元素来允许样式表中的 DOM 之间的通信

- It can be opened or closed defining visibility from the outer DOM. > 它可以通过从外部 DOM 定义可见性来打开或关闭

```js
class MyElement extends HTMLElement {
  constructor() {
    super();
    this.root = this.attachShadow({ mode: 'open' });
    // Define your element's behavior here
  }

  connectedCallback() {
    // Called when the element is connected to the DOM
    this.root.appendChild(document.createElement('h1'));
  }
}
```

Where to define HTML for a custom element > 在哪里定义自定义元素的 HTML

- There are several alternatives > 有几种选择
  - Use DOM APIs > 使用 DOM API
  - Use a `<template>` tag in the main HTML > 在主 HTML 中使用 `<template>` 标签
  - Use an external HTML file loaded with fetch(it can be prefetched) > 使用 fetch 加载的外部 HTML 文件（它可以被预取）
    - Using `innerHTML` > 使用 `innerHTML`
    - Using DOM parser > 使用 DOM 解析器

### Declarative Shadow DOM

A way to define shadow DOM directly in HTML markup using a new set of attributes and tags. > 一种使用一组新的属性和标签直接在 HTML 标记中定义 shadow DOM 的方法

- Where to define CSS for a custom element > 在哪里定义自定义元素的 CSS
- There are several alternatives > 有几种选择
  - Use CSSOM APIs > 使用 CSSOM API
  - Add a `<style>` tag to a `<template>`. > 将 `<style>` 标签添加到 `<template>` 中
  - Add a `<link>` in the `<template>`. > 在 `<template>` 中添加 `<link>`
  - Use an external CSS file loaded with fetch(it can be prefetched) and injected in the shadow DOM as a `<style>`. > 使用 fetch 加载的外部 CSS 文件（它可以被预取）并将其作为 `<style>` 注入到 shadow DOM 中

## Reactive Programming with Proxies

### Creating a Proxy

- Proxy
  - A wrapper object that lets you intercept and modify operations performed on the wrapped object, allowing you to add custom behavior or validation to the object's properties and methods. > 一个包装对象，它允许你拦截和修改对包装对象执行的操作，从而允许你向对象的属性和方法添加自定义行为或验证

```js
const original = {
  name: 'John',
  age: 32,
};

const s = new Proxy(original, {
  get(target, prop) {
    if (prop === 'age') {
      return `${target[prop]} years old`;
    }
    return target[prop];
  },
});

console.log(s.age); // 32 years old
```

Proxies work with objects only. If you want to do something similar with primitives, you can use classes with getter and setters. > 代理只适用于对象。如果你想用原始值做类似的事情，你可以使用带有 getter 和 setter 的类

- Proxy handler
  - Object that contains traps for intercepting and customizing operations performed on a JavaScript proxy object. > 包含用于拦截和自定义对 JavaScript 代理对象执行的操作的陷阱的对象
- Proxy trap
  - Method on a proxy handler object that intercepts and customizes a specific operation performed on the target object. > 代理处理程序对象上的方法，用于拦截和自定义对目标对象执行的特定操作
