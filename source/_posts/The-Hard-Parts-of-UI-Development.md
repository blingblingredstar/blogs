---
title: The Hard Parts of UI Development
date: 2023-07-15 13:15:48
categories: 学习笔记
tags: [FrontEndMasters]
---

# The Hard Parts of UI Development

## Introduction

In UI Hard Parts we develop an under-the-hood understanding of building user interfaces in the web browser. > 在 UI Hard Parts 中，我们将在 Web 浏览器中构建用户界面的底层工作原理。

### Principles of Engineering

- Analytical problem solving > 分析性解决问题
  - Can you break down complex challenges and develop a strategy for solving the problems. > 你能分解复杂的挑战并制定解决问题的策略吗？
- Technical communication > 技术沟通
  - Can I implement your approach from your explanation? > 我能根据你的解释实现你的方法吗？
- Engineering approach > 工程方法
  - How you handle "not knowing", debugging, code structure and working w/docs. > 如何处理“不知道”，调试，代码结构和使用文档。
- Non-technical communication > 非技术沟通
  - Empathetic and thoughtful communication, supportive approach to the community. > 有同理心和深思熟虑的沟通，对社区的支持性方法。
- JavaScript and programming experience > JavaScript 和编程经验
  - Concepts like closure & data structures, state & view. > 诸如闭包和数据结构，状态和视图之类的概念。

<!-- more -->

## State and View

### User Interface Dev Overview

- State, View, JavaScript, Dom & Events
  - Two goals to UI engineering: Display content - state/data - so the user can 'view' it, then let them interact with it. > UI 工程的两个目标：显示内容 - 状态/数据 - 以便用户可以“查看”它，然后让他们与之交互。
- The web browser's ad hoc history has led to the features that let us do this being spread across languages, APIs and environments. > Web 浏览器的临时历史导致了让我们能够在语言、API 和环境之间进行分布的功能。
- Requires JavaScript and the DOM to combine with help of WebCore, Web IDL, the DOM API, Event API. > 需要 JavaScript 和 DOM 与 WebCore、Web IDL、DOM API、Event API 的帮助结合起来。
- But lets us build dynamic interactive experience at the center of all modern applications. > 但是让我们在所有现代应用程序的中心构建动态交互体验。

- Almost every piece of technology needs a visual "user interface"(UI). > 几乎每一种技术都需要一个视觉“用户界面”（UI）。
  - Content displayed on a screen(output from the computer) that a user can change by doing something. > 屏幕上显示的内容（计算机的输出），用户可以通过做某事来更改。
    - Video stream from zoom. > 来自 zoom 的视频流。
    - Likes on tweet. > 推文上的喜欢。
    - Comments in a chat. > 聊天中的评论。
    - Mario's position on the screen. > Mario 在屏幕上的位置。
  - What are other UIs and what's the interactivity. > 其他 UI 是什么，交互性是什么。
    - A video game. > 电子游戏。
    - A website. > 网站。
    - A mobile app. > 移动应用程序。
    - ...
- Just two "simple" goals
  1. Display content(data inside computer/from internet) on screen. > 在屏幕上显示内容（计算机/互联网中的数据）。
  2. Enable the user to interact(tap, click, etc) with the content they see and change it(presumably change the underlying data - otherwise it'd be inconsistent). > 让用户与他们看到的内容进行交互（点击、点击等），并改变它（假设改变底层数据 - 否则它将不一致）。

### Display Content

Code to display content

1. What's on your mind?
2. Input field
3. Submit button

But we still have to think about the location(coordinates), browser & device variations... > 但是我们仍然必须考虑位置（坐标）、浏览器和设备的变化...

### Rendering HTML Under the Hood

Solution: A list of elements in C++ to add to the page - the DOM. > 解决方案：在 C++ 中添加到页面的元素列表 - DOM。

Web Core: converts the DOM(stored in C++) into a visual representation on the screen. > Web Core：将 DOM（存储在 C++ 中）转换为屏幕上的视觉表示。

```html
<form>
  <label for="input">What's on your mind?</label>
  <input id="input" type="text" />
  <button type="submit">Submit</button>
</form>
```

```c++
// DOM
[
  {
    type: 'form',
    props: {},
    children: [
      {
        type: 'label',
        props: { for: 'input' },
        children: ['What's on your mind?'],
      },
      {
        type: 'input',
        props: { id: 'input', type: 'text' },
        children: [],
      },
      {
        type: 'button',
        props: { type: 'submit' },
        children: ['Submit'],
      },
    ],
  },
];
```

DOM - ordered list(object) of page(document) elements we can add to using HTML > DOM - 我们可以使用 HTML 添加到的页面（文档）元素的有序列表（对象）

- Order/structures set by where we write each HTML line. > 通过我们编写每个 HTML 行的位置设置顺序/结构。

Layout engine - works out page layout for specific browser/screen. > 布局引擎 - 为特定的浏览器/屏幕计算页面布局。

Render engine - produces the composite 'image' for graphics card. > 渲染引擎 - 为图形卡生成复合“图像”。

### CSSOM for Styling

Adding CSSOM(CSS Object Model) for styling and formatting. > 添加 CSSOM 用于样式和格式。

```css
form {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}
```

```c++
// CSSOM
[
  {
    selector: 'form',
    declarations: [
      { property: 'display', value: 'flex' },
      { property: 'flex-direction', value: 'column' },
      { property: 'align-items', value: 'flex-start' },
    ],
  },
];
```

CSSOM - ordered list(object) of page(document) styles we can add to using CSS > CSSOM - 我们可以使用 CSS 添加到的页面（文档）样式的有序列表（对象）

So far, we finish goal 1 - display content by using HTML and CSS. > 到目前为止，我们通过使用 HTML 和 CSS 完成了目标 1 - 显示内容。

### Enabling Change of Content

Problem: Pixels != Data. > 问题：像素！= 数据。

- We're trained to think so by the real world where metaphysical and epistemic are tied. > 我们通过现实世界的形而上学和认识论联系在一起来思考。
- "Data" as "View". > “数据”作为“视图”。

But HTML 1x display/paint of content, user can change what they see e.g. input field. > 但是 HTML 1x 显示/绘制内容，用户可以更改他们看到的内容，例如输入字段。

Step 2 impossible w/ our approach to Step 1. > 用我们的方法完成步骤 1 后，步骤 2 是不可能的。

- HTML 1x display/paint of content. > HTML 1x 显示/绘制内容。
- DOM - Display "data" but can't run code. > DOM - 显示“数据”但无法运行代码。

We have to use JavaScript to: > 我们必须使用 JavaScript 来：

- Create & save content/data. > 创建和保存内容/数据。
- Run code to change it. > 运行代码来改变它。

## JavaScript, DOM & Events

### Store Data in JavaScript

Data(and code to change it) is only available in JavaScript. > 数据（以及更改它的代码）仅在 JavaScript 中可用。

- JS file of code added using HTML code. > 使用 HTML 代码添加的 JS 代码文件。
- JavaScript is an entire runtime. > JavaScript 是一个完整的运行时。
  - Thread of execution to run code. > 执行线程以运行代码。
  - Memory for data. > 数据内存。
  - Call stack & event loop to allow code(functions) to run asynchronously. > 调用堆栈和事件循环允许代码（函数）异步运行。
- We have data now - but how do we display it? > 我们现在有数据了 - 但是我们如何显示它？
  - With WebIDL & WebCore. > 使用 WebIDL 和 WebCore。

```html // App.html
<script src="app.js"></script>
```

```js
let post = 'Hello World';
// ...
post = 'Something else';
console.log(post);
```

```c++
// DOM
[
  {
    type: 'script',
    props: { src: 'app.js' },
    children: [],
  },
];

// CSSOM
[];
```

### WebIDL & WebCore

WebCore - converts the DOM(stored in C++) into a visual representation on the screen. > WebCore - 将 DOM（存储在 C++ 中）转换为屏幕上的视觉表示。

Web IDL - an interface description language (IDL) format for describing application programming interfaces (APIs) that are intended to be implemented in web browsers. > Web IDL - 用于描述应用程序编程接口（API）的接口描述语言（IDL）格式，这些接口旨在在 Web 浏览器中实现。

<details>

<summary>WebCore gives JavaScript access to the DOM & pixels</summary>

```html
<input />
<div></div>
<script src="app.js"></script>
```

```js
// app.js
let post = 'Hello World';
const jsDiv = document.querySelector('div');
jsDiv.textContent = post;
console.log(jsDiv);
```

### Updating DOM Elements with JavaScript

WebCore gives JavaScript access to the DOM & pixels. > WebCore 使 JavaScript 可以访问 DOM 和像素。

- Goal 1: Display content/data to user
  1.  Data created & stored in JS: `post = 'Hello World'`
  2.  Get DOM element(a link in JS):`document.querySelector('div')`
  3.  Store link to element, in JS: `const jsDiv`
  4.  Use built-in "property-methods" to add content/data from JS to DOM element: `jsDiv.textContent = post`
  5.  DOM automatically displays content on page(the "view")

### Handling User Interaction

```html
<input />
<div></div>
<script src="app.js"></script>
```

```js
// app.js
let post = '';
const jsInput = document.querySelector('input');
const jsDiv = document.querySelector('div');

function handleInput() {
  post = jsInput.value;
  // we have to sync the DOM with the data manually
  // we can't just change the data and expect the DOM to update
  jsDiv.textContent = post;
}

jsInput.oninput = handleInput;
```

Solving for Goal 2: Users can change what they see(& underlying data) by interacting > 解决目标 2：用户可以通过交互来更改他们看到的内容（和底层数据）

User interaction(typing, clicking etc) is "registered" by DOM elements(they know when they're tapped) > 用户交互（键入，单击等）由 DOM 元素“注册”（它们知道何时被点击）

But how can this cause a change in data which is elsewhere(in JavaScript)? > 但是这如何导致数据（在 JavaScript 中的其他位置）发生变化？

DOM events and handler functions - let user action/input("events") change data in JavaScript > DOM 事件和处理程序函数 - 让用户操作/输入（“事件”）更改 JavaScript 中的数据

```js
jsInput.oninput = handleInput;
```

By saving a function definition to the DOM element - when the user action("event") happens, the function will be "called back" into JS and executed automatically. > 通过将函数定义保存到 DOM 元素 - 当用户操作（“事件”）发生时，该函数将“回调”到 JS 并自动执行。

We've solved for UI goals 1 & 2! > 我们已经解决了 UI 目标 1 和 2！

User actions can trigger running JS code to:

1.  Save/change underlying data(in JS) > 保存/更改底层数据（在 JS 中）
2.  Change what data is displayed(in DOM) > 更改显示的数据（在 DOM 中）

- Note that we have to manually re-update the DOM with our data - there is no "propagation". > 请注意，我们必须手动使用我们的数据重新更新 DOM - 没有“传播”。

We have a full user interface(UI). > 我们有一个完整的用户界面（UI）。

Goal 1: Display content(data inside computer/from internet) as the "view" for user to see. > 目标 1：将内容（计算机/互联网中的数据）显示为用户查看的“视图”。
Goal 2: Enable the user to interact(tap, click etc) with the "view" they see and change it (by changing the underlying data & updating the view). > 目标 2：使用户能够与他们看到的“视图”进行交互（点击，单击等）并更改它（通过更改底层数据并更新视图）。

## Data-Binding In the UI

### One-Way Data Binding

Let's expand our UI to be more sophisticated. > 让我们扩展我们的 UI 以使其更加复杂。

Adding another handler - this one for clicks > 添加另一个处理程序 - 这是点击的处理程序

```js
let post = '';
const jsInput = document.querySelector('input');
const jsDiv = document.querySelector('div');

jsInput.value = 'Hello World'; // we may update the DOM accidentally

function handleInput() {
  post = jsInput.value;
  jsDiv.textContent = post;
}

function handleClick() {
  // post = ''; // we may forget to update the data
  jsDiv.textContent = post;
}

jsInput.oninput = handleInput;
jsDiv.onclick = handleClick;
```

We're changing our "view" based on multiple possible user interactions. In practice there will be 1000s that can all affect data/view. > 我们正在根据多种可能的用户交互来更改我们的“视图”。 在实践中，将有数千个可以影响数据/视图的数据。

We need a way to make these changes as predictable as we possibly can. > 我们需要一种尽可能可预测地进行这些更改的方法。

- One-way Data Binding
  - Popular paradigm for tackling the essential UI engineering challenge - data/view consistency. > 解决基本 UI 工程挑战 - 数据/视图一致性的流行范例。
  - Invaluable at scale(state/view with 1000s of points of interaction) & implemented in React, Angular & Vue. > 在规模上是无价的（具有 1000 多个交互点的状态/视图）并在 React、Angular 和 Vue 中实现。
  - Enables us to build scalable apps with or without frameworks, debug complex UIs & answer the toughest interview Qs. > 使我们能够构建可扩展的应用程序，无论是否使用框架，都可以调试复杂的 UI 并回答最难的面试问题。

### Separate Data & View Updates

```js
let post = undefined;
const jsInput = document.querySelector('input');
const jsDiv = document.querySelector('div');

function dataToView() {
  jsInput.value = post === undefined ? '' : post;
  jsDiv.textContent = post === undefined ? '' : post;
}

function handleClick() {
  post = '';
  dataToView();
}

function handleInput() {
  post = jsInput.value;
  dataToView();
}

jsInput.oninput = handleInput;
jsDiv.onclick = handleClick;
dataToView();
```

Restrict every change to view to be via (1) An update of "data" and (2) A run of a single dataToView convertor function. > 限制对视图的每次更改为（1）“数据”的更新和（2）单个 dataToView 转换器函数的运行。

- This is just one approach - but an immensely popular one(React, Next, Vue, Angular, Svelte). > 这只是一种方法 - 但是非常受欢迎（React、Next、Vue、Angular、Svelte）。
- Tees us up for semi-visual coding with a virtual DOM. > 为我们提供了一个半可视化的虚拟 DOM。

```js
// Or we can sync the data for every 15ms
function handleInput() {
  post = jsInput.value;
}

function handleClick() {
  post = '';
}

setInterval(dataToView, 15);
```

We've added predictability to our Data and View flow

- 1000s of ways for users to interact in a modern app > 现代应用程序中用户交互有数千种方式
  - Each piece of "view"(element on page) can be clicked/typed/moused over - all of which need to change data and view - exponential complexity > 每个“视图”（页面上的元素）都可以点击/键入/鼠标悬停 - 所有这些都需要更改数据和视图 - 指数复杂性
- We need a predictable structure > 我们需要一个可预测的结构
  - Better to be restricted but predictable than overly flexible and it be impossible to identify which line of code caused our view/data to change. > 最好是受限但可预测，而不是过于灵活，以至于无法确定哪行代码导致我们的视图/数据发生更改。
- Every interaction must change JS data and then update all dependent views. > 每个交互都必须更改 JS 数据，然后更新所有相关视图。
  - Changes to views via (1) And update of "data" and (2) A run of a single dataToView convertor. > 通过（1）更新“数据”和（2）单个 dataToView 转换器的运行来更改视图。
- One-way data binding > 单向数据绑定
  - Restricting all our user's changes to flow to associated underlying data and all changes of view to flow through a single dataToView convertor is a restrictive but powerful step to simplify our reasoning about UI by order of magnitude. > 将所有用户更改限制为流到相关的底层数据，并将所有视图更改限制为通过单个 dataToView 转换器流动是简化 UI 推理的一种限制但强大的步骤。

## Virtual DOM

- Virtual DOM
  - Most misunderstood concept in UI development > UI 开发中最被误解的概念
  - Enables us to have a more visual(or declarative) experience of coding UIs with JavaScript > 使我们能够使用 JavaScript 更直观（或声明性）地编码 UI
  - Requires significant optimizations(diffing, reconciliation) to be performative > 需要进行高效的优化（差异，协调）才能执行

### Auto-Updating Views UI

Returning to our full UI with auto updating views(from data) > 返回到具有自动更新视图（来自数据）的完整 UI

- We're describing a key part of the UI in dataToView > 我们正在描述 UI 的关键部分
  - THe contents(data) and how to display it > 内容（数据）及其显示方式
- But even our "containers" are "data" of sorts > 但是即使是我们的“容器”也是某种“数据”
- What if we also described the element as well than our dataToView convertor is a complete description of the data + view? > 如果我们也描述了元素，那么我们的 dataToView 转换器就是数据 + 视图的完整描述？

```html
<input />
<div></div>
<script src="app.js"></script>
```

```js
let post = ''; // data
const jsInput = document.querySelector('input'); // view
const jsDiv = document.querySelector('div'); // view

// affect view
function dataToView() {
  jsInput.value = post;
  jsDiv.textContent = post;
}

function handleInput() {
  post = jsInput.value;
  if (post === 'will') {
    jsDiv.remove(); // view!
  }
}

jsInput.oninput = handleInput;

setInterval(dataToView, 15);
```

### Understanding UI Component

```js
let post = '',
  jsInput,
  jsDiv;

function dataToView() {
  jsInput = document.createElement('input');
  jsDiv = post === 'Will' ? '' : document.createElement('div');
  jsInput.value = post;
  jsDiv.textContent = post;
  jsInput.oninput = handleInput;

  document.body.replaceChildren(jsInput, jsDiv);
}

function handleInput() {
  post = jsInput.value;
}

setInterval(dataToView, 15);
```

- [Detached Element Tool](https://learn.microsoft.com/en-us/microsoft-edge/devtools-guide-chromium/memory-problems/)

- Function that fully creates element & relates data/view is known as a UI component > 完全创建元素并关联数据/视图的函数称为 UI 组件
  - Combines everything into 1 function - Creates, sets their data/contents, attaches handlers & displays via DOM > 将所有内容组合到 1 个函数中 - 通过 DOM 创建、设置其数据/内容、附加处理程序并显示
  - Ties data to view - handles how user can change data(here, via input) then re-runs to update view with new data. > 将数据绑定到视图 - 处理用户如何更改数据（此处通过输入），然后重新运行以使用新数据更新视图。
  - React, Angular, Vue, Svelte all have these. > React、Angular、Vue、Svelte 都有这些。

### Emulate HTML with String Interpolation

- String interpolation gives us 'visual' code > 字符串插值给我们提供了“可视化”代码

```js
let name = 'Jo';

let textToDisplay = 'Hello, ';
textToDisplay = textToDisplay.concat(name);
textToDisplay = textToDisplay.concat('!');
// textToDisplay = "Hello, Jo!"

// Alternatively
textToDisplay = `Hello, ${name}!`;
```

- Could we emulate HTML with semi-visual coding

```js
let name = 'Jo';

const divInfo = ['div', `Hello, ${name}!`];

function convert(node) {
  const [element, ...children] = node;
  const el = document.createElement(element);
  for (const child of children) {
    if (typeof child === 'string') {
      el.appendChild(document.createTextNode(child));
    } else {
      el.appendChild(convert(child));
    }
  }
}

const jsDiv = convert(divInfo);
```

### Creating a JavaScript Virtual DOM

```js
let name = '';
let jsInput = null,
  jsDiv = null,
  vDOM = null;

function createVDOM() {
  return [
    ['input', name, handle],
    ['div', `Hello, ${name}!`],
  ];
}

function handle() {
  name = jsInput.value;
}

function updateDOM() {
  vDOM = createVDOM();
  jsInput = convert(vDOM[0]);
  jsDiv = convert(vDOM[1]);
  document.body.replaceChildren(jsInput, jsDiv);
}

function convert(node) {
  const [element, content, handler] = node;
  const el = document.createElement(element);
  if (element === 'input') {
    el.value = content;
    el.oninput = handler;
  }
  if (element === 'div') {
    el.textContent = content;
  }
  return el;
}

setInterval(updateDOM, 15);
```

- Create a JavaScript ('virtual') DOM > 创建 JavaScript（'虚拟'）DOM

1. Blocks of code representing each piece of 'view' > 表示每个“视图”部分的代码块
   - JavaScript array with all the details > 具有所有细节的 JavaScript 数组
   - Function that takes in that array and produces a DOM element > 接受该数组并生成 DOM 元素的函数
2. Then where we place them in the code mirrors the order they show up on the page > 然后我们在代码中放置它们的位置与它们在页面上显示的顺序相同

### Declarative UI as a Paradigm

Declarative UI as a programming paradigm > 声明性 UI 作为编程范例

- Goal: See a JS nested list representation 1 of your actual DOM elements(and contents) so you can get semi-visual coding > 目标：查看 JS 嵌套列表表示形式 1 您的实际 DOM 元素（及其内容），以便您可以获得半可视化编码
  - Via nested objects or arrays of info(as we default to in UIHP(UI Hard Part)) - or functional components in React > 通过嵌套的对象或信息数组（如 UIHP 中默认的） - 或 React 中的功能组件
- For it to be guaranteed accurate(& not out of sync) - you need your data in JS to be the only source of truth > 为了保证准确（而不是不同步），您需要将 JS 中的数据作为唯一的真相来源
  - You can only do a JS "DOM" if you've set on 'one-way data binding' - otherwise it's not guaranteed to be reflective of the actual DOM(which has been altered by the user) > 如果您已设置为“单向数据绑定”，则只能执行 JS“DOM” - 否则，它不能保证反映实际 DOM（已由用户更改）
- So we literally take any change to the DOM(e.g. user action like typing letters in an input field) and immediately replace it by > 因此，我们实际上采取了对 DOM 的任何更改（例如，用户操作，例如在输入字段中键入字母）并立即替换它
  - sending the info to 'data' store in JS > 将信息发送到 JS 中的“数据”存储
  - have that update contents of the JS DOM > 更新 JS DOM 的内容
  - convert that to the DOM(view) > 将其转换为 DOM（视图）
- We have semi-visual coding - our JS DOM contents and positioning on the JS page is reflected in our actual view > 我们有半可视化编码 - 我们的 JS DOM 内容和 JS 页面上的定位反映在我们的实际视图中
  - We 'declare' (a) what page structure we want and (b) how we want its contents to depend on our data > 我们“声明”（a）我们想要的页面结构以及（b）我们希望其内容依赖于我们的数据
  - And then it appears on the page(even an empty form field is the propagation of data - our 'empty string') > 然后它出现在页面上（即使是空的表单字段也是数据的传播 - 我们的“空字符串”）

## Composition & Functional Components

### Using Lists for UI Components

- Our JavaScript code mirrors our output with our JS DOM - we can make it flexible by mapping over it. > 我们的 JavaScript 代码与我们的 JS DOM 输出相匹配 - 我们可以通过对其进行映射来使其灵活。
- We can use reuseable functions to create JS(V)DOM elements each with specific data. > 我们可以使用可重用的函数来创建具有特定数据的 JS（V）DOM 元素。
- We get UI composition with units of UI(data, view and handlers) that we can reuse and combine flexibly to build out our applications. > 我们使用 UI 组合来获取 UI 单元（数据、视图和处理程序），我们可以重用和灵活组合以构建应用程序。

#### Lists are central to UI development > 列表是 UI 开发的核心

We have a list of 2 arrays with their element details - but in reality it will likely be many(49 video elements in a zoom meeting, 100 tweets in a timeline) > 我们有一个包含其元素详细信息的 2 个数组的列表 - 但实际上它可能是许多（49 个视频元素在 zoom 会议中，100 个推文在时间轴中）

We need tools for dealing with lists > 我们需要处理列表的工具

- To apply code(functionality) to each element - here to take the info and connect to a DOM element `vDOM.map()` > 将代码（功能）应用于每个元素 - 在此处获取信息并连接到 DOM 元素 `vDOM.map()`
- To convert an array elements to individual inputs(arguments) to a function(for functions that don't take in arrays) spread syntax `...` > 将数组元素转换为函数的单个输入（参数）（对于不接受数组的函数）扩展语法 `...`

### Composable Code with Map and Spread

```js
let name = '',
  vDOM,
  elements;

function createVDOM() {
  vDOM = [
    ['input', name, handle],
    ['div', `Hello ${name}`][('div', `Goodbye ${name}`)],
  ];
}

function handle() {
  name = jsInput.value;
}

function convert(node) {
  const [element, content, handler] = node;
  const el = document.createElement(element);
  if (element === 'input') {
    el.value = content;
    el.oninput = handler;
  }
  if (element === 'div') {
    el.textContent = content;
  }
  return el;
}

function updateDOM() {
  vDOM = createVDOM();
  elements = vDOM.map(convert);
  document.body.replaceChildren(...elements);
}

setInterval(updateDOM, 15);
```

#### 'Mapping' over our vDOM and 'spreading' resulting array

- Now adding new 'elements' doesn't require any new code
- The map call will handle as many elements in the vDOM as there are. > map 调用将处理 vDOM 中的任意数量的元素。
- To add our array of DOM elements we need to convert them to individual arguments. > 要添加 DOM 元素数组，我们需要将它们转换为单个参数。
- Spread syntax allows an iterable such as an array expression or string to be expanded. > Spread 语法允许扩展可迭代对象，例如数组表达式或字符串。
- To work with the append method(which can't handle arrays). > 与无法处理数组的 append 方法一起使用。

- Without our new element-flexible code we can 'compose' our code
  - Adding to our list of arrays each new element we want to see in order on our page. > 将我们要按顺序在页面上看到的每个新元素添加到我们的数组列表中。
  - They'll show up on the page exactly as they're placed in code. > 它们将按照它们在代码中的位置显示在页面上。

```js
let posts = ['Ginger', 'Gez', 'Ursy', 'Fen'];
function Post(msg) {
  return ['div', msg];
}

function Input() {
  return ['input', name, handle];
}

function createDOM() {
  return [Input, ...posts.map(Post)];
}
/**
 * Const Input = () => <input value={name} onChange={handle} />
 * Const Post = ({msg}) => <div>{msg}</div>
 * Const App = () => <><Input />{posts.map(Post)}</>
 */
```

- Even more composition - creating multiple(similar) VDOM elements each with different data. > 更多组合 - 创建具有不同数据的多个（类似的）VDOM 元素。
  - We can produce our VDOM elements with a function so that we can easily add new elements. > 我们可以使用函数生成 VDOM 元素，以便我们可以轻松添加新元素。
  - Post defines data -> view relationship where the data(each element in the array posts) changes for each of the posts. > Post 定义数据->视图关系，其中数据（数组 posts 中的每个元素）对每个帖子都会更改。
  - Can also add more logic to the Post/Input functions to make them more powerful. > 还可以向 Post/Input 函数添加更多逻辑，使其更加强大。

## Performance Improvements(Hooks, diffing & reconciliation)

### Update the DOM on Data Changes

- We love the vDOM for semi-visual coding - however performance is a nightmare - we need some improvements. > 我们喜欢 vDOM 用于半可视化编码 - 但性能是一场噩梦 - 我们需要一些改进。
- We're updating DOM every 15ms(handlers, CSS animations & smooth scrolling is at risk). We can 'simulate' auto-updating view from data change with a state hook. > 我们每 15ms 更新一次 DOM（处理程序、CSS 动画和平滑滚动处于危险之中）。我们可以使用状态钩子“模拟”从数据更改自动更新视图。
- Only some of our DOM elements need recreating from scratch - we can compare(w/'diffing algorithm') archived VDOMs and workout what changes to actually make using an algorithm. > 只有一些 DOM 元素需要从头开始重新创建 - 我们可以比较（'diffing 算法'）存档的 VDOMs，并使用算法计算出实际要进行的更改。

- Automatic update on data change without looping > 不适用循环的数据更改自动更新
  - instead of directly updating name, we run a function `updateName` to do so. > 而不是直接更新名称，我们运行一个函数 `updateName` 来这样做。
  - And of course it's low key running updateDOM for us. > 当然，它正在为我们运行 updateDOM。
  - As long as we restrict our team from every changing data directly and only let them do so using the update function then we're fine. > 只要我们限制团队不直接更改数据，并且只允许他们使用更新函数这样做，那么我们就没问题了。
  - We'd likely lock down name so that it can't be accessed directly. > 我们可能会锁定名称，以便无法直接访问它。

```js
let name = '';
function updateName(value) {
  name = value;
  updateDOM();
}
function handler(e) {
  updateName(e.target.value);
}
```

### Automatic Updates with Hooks

- Turning it into something we can use with any data. > 将其转换为我们可以使用的任何数据。
  - We can make our `updateName` function an `updateData` function and have it work for any piece of data in our app - we just hook into it. > 我们可以使我们的 `updateName` 函数成为 `updateData` 函数，并使其适用于应用程序中的任何数据 - 我们只需将其连接起来。
  - So they call it a hook... > 所以他们称之为钩子...
  - We could alternatively switch to running `requestAnimationFrame` rather than updateDOM directly on data change - so it never prioritizes over animations(CSS etc) - truly optimized wrt other priorities now. > 或者，我们可以在数据更改时切换到运行 `requestAnimationFrame` 而不是直接更新 DOM - 因此它永远不会优先于动画（CSS 等） - 现在真正优化了其他优先级。
    - `requestAnimationFrame(updateDOM)`

```js
const data = { name: '' };
function updateData(label, value) {
  data[label] = value;
  updateDOM();
}
function createVDOM() {
  return [
    ['input', data.name, (e) => updateData('name', e.target.value)],
    ['div', `Hello ${data.name}`],
  ];
}
```

### Performance Gains Using Diffing

All the benefit of composition - but it's still dangerously inefficient. > 所有组合的好处 - 但仍然非常低效。

- Only some of our elements need recreating from scratch
- Couldn't we recreate our vDOM from scratch to give us 'composition' but then: > 我们不能从头开始重新创建 vDOM 来给我们“组合”，但是：
  - Write an 'algorithm'(smart instructions) to check what elements actually differ - and only change the DOM elements that need updating. > 编写一个“算法”（智能指令）来检查实际上有什么元素不同 - 并且仅更改需要更新的 DOM 元素。

```js
let name = '';
function createVDOM() {
  return [
    [
      'input',
      name,
      (e) => {
        name = e.target.value;
        updateDOM();
      },
    ],
    ['div', `Hello ${name}`],
    ['div', 'Good job!'],
  ];
}

function findDiff(prev, current) {
  current.forEach((el, i) => {
    if (JSON.stringify(el) !== JSON.stringify(prev[i])) {
      // change the actual DOM element
    }
  });
}

const vDOM1 = createVDOM();
name = 'Bob';
const vDOM2 = createVDOM();
findDiff(vDOM1, vDOM2);
```

#### Finally - descriptive & composable UI that's not a disaster!

```js
const data = { name: '' };
let vDOM = createVDOM();
let prevVDOM;
let elements;

function updateData(label, value) {
  data[label] = value;
  requestAnimationFrame(updateDOM);
}

function createVDOM() {
  return [
    ['input', data.name, (e) => updateData('name', e.target.value)],
    ['div', `Hello ${data.name}`],
    ['div', 'Good job!'],
  ];
}

function updateDOM() {
  if (elements === undefined) {
    elements = vDOM.map(convert);
    document.body.append(...elements);
  } else {
    prevVDOM = [...vDOM];
    vDOM = createVDOM();
    findDiff(prevVDOM, vDOM);
  }
}

function convert(element) {
  const [tag, content, handler] = element;
  const el = document.createElement(tag);
  if (tag === 'input') {
    el.value = content;
    el.addEventListener('input', handler);
  }
  if (tag === 'div') {
    el.textContent = content;
  }
  return el;
}

function findDiff(prev, current) {
  current.forEach((el, i) => {
    if (JSON.stringify(el) !== JSON.stringify(prev[i])) {
      const [tag, content, ...rest] = el;
      const currentEl = elements[i];
      if (tag === 'input') {
        currentEl.value = content;
      }
      if (tag === 'div') {
        currentEl.textContent = content;
      }
    }
  });
}

updateDOM();
```

#### We have a groundbreaking approach to UI

- Single source of truth for our data. > 数据的单一真相来源。
  - Everything the user sees stems from the data. It's restrictive but desperately predictable - and enables data propagation to the view. > 用户看到的所有内容都源于数据。它是有限的，但是非常可预测 - 并且可以将数据传播到视图。
- Enables UI composition with JavaScript. > 使用 JavaScript 组合 UI。
  - A JavaScript DOM that enables a semi visual form of coding our UIs - where units of code that describe units of view can be positioned in the code to indicate their order on the webpage. > JavaScript DOM 可以实现半可视化的 UI 编码形式 - 在代码中可以放置描述视图单元的代码单元，以指示它们在网页上的顺序。
- Makes reasoning about state/view much easier. > 使状态/视图的推理变得更加容易。
  - The downside of semi-visual code is it takes the data and info on the view at this moment and creates it in full(that's why it's so easy to reason about - we don't have to figure out the change from the previous data/view - just current data generating current view). > 半可视化代码的缺点是它获取此刻的数据和视图信息，并将其完整地创建出来（这就是为什么它很容易推理的原因 - 我们不必弄清楚从先前的数据/视图中的更改 - 只需当前数据生成当前视图）。
- But requires hooks & VDOM diffing for efficiency. > 但是需要钩子和 VDOM diffing 来提高效率。
  - If we rebuilt the DOM every time data changed that would be entirely unnecessary. Instead we spot actual difference & only change those(DOM reconciliation). > 如果我们每次更改数据时都重建 DOM，那将是完全不必要的。相反，我们会发现实际的差异，并仅更改这些（DOM 协调）。
