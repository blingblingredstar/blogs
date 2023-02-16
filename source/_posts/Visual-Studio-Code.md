---
title: Visual-Studio-Code
date: 2019-06-06 11:27:19
categories: 学习笔记
tags: [FrontEndMasters, Editor, Markdown, VS Code]
---

## Visual Studio Code

### Introduction

[mike-works/vscode-fundamentals](https://github.com/mike-works/vscode-fundamentals)

Course outline and slides

[View course outline here](https://mike.works/course/vs-code-607be6c)
[View slides here](https://github.com/mike-works/vscode-fundamentals/tree/master/docs)

### Editor Feature

Over time we're seeing support for:

+ Task running
+ Workspace-specific settings
+ A rich plugin ecosystem
+ Things that analyze your code in the background
+ A wide range of programming languages
<!--more-->
### Developer Focus

#### It's built using web technology, for web developers, the Open Source way

👩‍💻 Delivering on features and an ecosystem that's easy to jump into

🔌 Talks to web technologies more easily (i.e., Node talking to Node)

📖 Binaries are proprietary, but source code is open source

🗣 Close collaboration between the VS Code, Edge, Visual Studio and TypeScript teams'

#### Learning from past lessons

+ 🚫 Don't make ANOTHER heavy IDE

+ 🔒 Don't impose lots of opinions onto users
  + ⌨️ Bring your keybindings with you
+ 🤝 Favor open standards over proprietary formats
  + i.e., .jsconfig, .editorconfig

### Up and Running

#### Not just a new twist

🙌 Best cross-platform TypeScript development experience available

+ Leverages TypeScript technology to give you insight into your regular JavaScript
  
👌 A great .NET development experience on OS X and Linux

+ finally an OS maker that doesn't lock us in
  
⏩ Doesn't require a week of tweaking/config to get "right"

🚢 Shipping new features at an incredible rate!

### Course Agenda

#### How this course is going to work

Agenda is as follows:

1. [Using It](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/1_using/README.md)
2. [Customizing It](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/2_customizing/README.md)

New code editing tricks (i.e. ⌘ + D along the way)

We'll be working with JavaScript and TypeScript.

+ It's OK (maybe even better) if you don't know TS yet

#### Using Visual Studio Code

+ 📄 [Awesome Documents](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/1_using/markdown.md)
+ ⏩ [Emmet](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/1_using/emmet.md)
+ 🎛 [Refactoring](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/1_using/refactoring.md)
+ ✅ [Type-Checking](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/1_using/type-checking.md)
+ 🐞 [Debugging](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/1_using/debugging.md)

### Setting Up

#### The best way to get to know this editor

Let's use it to do some stuff!

Here's the example we will be working with - a grocery app!

![a grocery app](https://github.com/mike-works/vscode-fundamentals/raw/master/public/grocer.png)

It's a client/server app, built with bleeding edge versions of the following:

+ [React 16 (beta)](https://github.com/facebook/react/issues/10294)
+ [Webpack 3](https://webpack.js.org/)
+ [TypeScript 2.5](https://blogs.msdn.microsoft.com/typescript/2017/08/31/announcing-typescript-2-5/)
+ [Koa](https://koajs.com/)

This is intended to show you the most modern web tools🔧 available. 95% of this can be done in older versions too!

Take a moment now to ensure you're all set up. First, make sure you're running a recent version of Node.js

```bash
control + ` # 打开terminal的快捷键
```

```bash
node --version # v8.0.0 or newer
```

And we need a library called sqlite3 for our database. You can check your version by running

```bash
# If you don't have it yet
brew install sqlite3
# If you already have it
sqlite3 --version # 3.16.0 2016-11-04...
```

And it's helpful to have a package manager called yarn

```bash
# If you don't have it yet
brew install yarn
# If you already have it
yarn --version # 0.27.5
```

```bash
git clone https://github.com/mike-north/vscode-fundamentals vscode
cd vscode
yarn
npm start
# Open http://localhost:3000
```

NEXT: 📄 [Awesome Documents](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/1_using/markdown.md)

## Documents

### Markdown

#### Images

Normal markdown images

```markdown
![VS Code](../../public/vscode.png)
```

![vscode](https://github.com/mike-works/vscode-fundamentals/raw/master/public/vscode.png)

The HTML `<img>` tag can be used as well. Many attributes will be respected in most markdown renderers (including GitHub)

```markdown
<img src="../../public/vscode.png" height=50 align=right vspace=20/>
<img src="../../public/vscode.png" height=100/>
```
<!-- markdownlint-disable MD033 -->
<img src="https://github.com/mike-works/vscode-fundamentals/raw/master/public/vscode.png" height=50 align=right vspace=20/>

<img src="https://github.com/mike-works/vscode-fundamentals/raw/master/public/vscode.png" height=100/>

👉 Keep in mind that, in this case, GitHub wraps each image in

```markdown
<p>
  <a href="./myimage.jpg">
    <!--image-->
  </a>
</p>
```

#### Alignment

The `align` attribute can be used on a variety of HTML tags

```markdown
<p align=right>right ➡</p>
<p align=center>⬅ center ➡</p>
<p align=left>⬅ left</p>
```

<p align=right>right ➡</p>
<p align=center>⬅ center ➡</p>
<p align=left>⬅ left</p>

If you wrap multiple inline elements in a `<p>` tag, it's possible to do some interesting things with vertical align attribute values

```markdown
<p>
  <img src="../../public/vscode.png" height=50 align=top />
  <img src="../../public/vscode.png" height=50 align=bottom />
  <img src="../../public/vscode.png" height=100/>
</p>
```

<p>
  <img src="https://github.com/mike-works/vscode-fundamentals/raw/master/public/vscode.png" height=50 align=top />
  <img src="https://github.com/mike-works/vscode-fundamentals/raw/master/public/vscode.png" height=50 align=bottom />
  <img src="https://github.com/mike-works/vscode-fundamentals/raw/master/public/vscode.png" height=100/>
</p>

#### Lists

##### Unordered Lists

These can be nested to create a multi-level outline

> Markdown Lint的规则是使用加号

```markdown
* one
* two
  * three
    * four
```

+ one
+ two
  + three
    + four

##### Ordered Lists

These cannot be nested in most markdown rendering engines

```markdown
1. first
1. second
1. third
```

1. first
1. second
1. third
  
but, if you use HTML, you can fix this, and customize the list "type"

```html
<ol type='A' >
  <li> first </li>
  <li> second </li>
  <li> third</li>
  <ol type='a' >
    <li> fourth </li>
    <ol type='i' >
      <li> fifth </li>
    </ol>
  </ol>
</ol>
```

<ol type='A' >
  <li> first </li>
  <li> second </li>
  <li> third</li>
  <ol type='a' >
    <li> fourth </li>
    <ol type='i' >
      <li> fifth </li>
    </ol>
  </ol>
</ol>

##### [Description Lists](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/dl) (`<dl>`)

```markdown
<dl>
  <dt>Images</dt>
  <dd>.jpg, .gif, .png</dd>
  <dt>Styles</dt>
  <dd>.css</dd>
  <dt>Scripts</dt>
  <dd>.js</dd>
  <dt>Documents</dt>
  <dd>.html</dd>
</dl>
```

<dl>
  <dt>Images</dt>
  <dd>.jpg, .gif, .png</dd>
  <dt>Styles</dt>
  <dd>.css</dd>
  <dt>Scripts</dt>
  <dd>.js</dd>
  <dt>Documents</dt>
  <dd>.html</dd>
</dl>

#### [Details](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/details)/Summary

````markdown
<details>
  <summary>Click me for something absolutely amazing</summary>

```ts
  let Hello : string = 'World';
```
</details>
````

<details>
  <summary>Click me for something absolutely amazing</summary>

```ts
  let Hello : string = 'World';
```

</details>

```markdown
| Item      | Price | Qty |
|-----------|-------|-----|
| 🍇 Grapes | $2.99 | 3   |
| 🍐 Pears  | $4.15 | 1   |
| 🍋 Lemons | $0.99 | 2   |
```

| Item      | Price | Qty |
|-----------|-------|-----|
| 🍇 Grapes | $2.99 | 3   |
| 🍐 Pears  | $4.15 | 1   |
| 🍋 Lemons | $0.99 | 2   |

Thanks to folks who posted tips I didn't know about!

NEXT: ⏩ [Emmet](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/1_using/emmet.md)

#### Most useful Detail, second Align

### Emmet & Html

#### Emmet Autocompletions

+ Speed up repetitive task with `TAB`
+ Plugins for most editors
+ Built into VS Code
+ Works in JSX

#### Emmet: HTML Elements

+ Think of as CSS selectors for DOM generation
+ Cursor usually ends up placed just where you want it to be next

`div`

```html
<div></div>
```

`.foo`

```html
<div class="foo"></div>
```

`span.foo#bar`

```html
<span class="foo" id="bar"></span>
```

`img`

```html
<img src="" alt="">
```

#### Nesting & Sibling

+ Just like in CSS, we can use...

| Selector | Description |
|----------|-------------|
| `>` |Direct decendant selector|
| `+` |Sibling selector|

`ul>li.special-item`

```html
<ul>
  <li class="special-item"></li>
</ul>
```

`.navbar+.content+.footer`

```html
<div class="navbar"></div>
<div class="content"></div>
<div class="footer"></div>
```

We can also use the "climb up" operator: `^`

`div+div>p>span+em^bq`

```html
<div></div>
<div>
  <p><span></span><em></em></p>
  <blockquote></blockquote>
</div>
```

Personally, I prefer using parentheses (grouping) rather than climb-up

`div+div>(p>(span+em))+bq`

```html
<div></div>
<div>
  <p><span></span><em></em></p>
  <blockquote></blockquote>
</div>
```

There's a feature called "multiplication" that's useful for lists

`ul.col>li.col-item*5`

```html
<ul class="col">
  <li class="col-item"></li>
  <li class="col-item"></li>
  <li class="col-item"></li>
  <li class="col-item"></li>
  <li class="col-item"></li>
</ul>
```

And you can even add text to the body of elements, with a placeholder for number in the list

`ul.col>li.col-item*5{Item $}`

```html
<ul class="col">
  <li class="col-item">Item 1</li>
  <li class="col-item">Item 2</li>
  <li class="col-item">Item 3</li>
  <li class="col-item">Item 4</li>
  <li class="col-item">Item 5</li>
</ul>
```

### Emmet: CSS

#### Emmet: Styles

+ Lots of different combinations(too many to remember)
+ Value aliases are pretty easy to get used to

| Alias | Value |
|-------|-------|
| `p` | Percent |
| `e` | em |
| `r` | rem |
| `px` | px |

+ Rule of thumb: first letter of each word for a property

| Emmet | Evaluates to |
|-------|-------|
| `w100p!` | `width: 100% !important` |
| `lh1.5r` | `line-height: 1.5rem` |
| `op50p` | `opacity: 50%` |

#### Tips for Productive Emmet use

+ No newlines or spaces
+ You can create custom shortcuts(more on this later)
+ Aim for several small expansions, rather than one huge expansion
+ Start basic, and add new shortcuts over time

### Challenge 1: Rapid Expansion

<img align=right width=150 src='https://github.com/mike-works/vscode-fundamentals/raw/master/public/emmet/click-for-more.png'>

> We want to add a new tile to the right of each category row, as shown here 👉
> You'll need to edit the `render` function in [client/routes/home/category-row/index.tsx](https://github.com/mike-works/vscode-fundamentals/blob/master/client/routes/home/category-row/index.tsx)
> There's a per-category image you can use, which take the form `/images/fallback-<lowercase-category-name>.png`. (i.e., [http://localhost:3000/images/fallback-frozen.png](http://localhost:3000/images/fallback-frozen.png))
> Use Emmet autocompletions to build the appropriate HTML structure
>
> ```html
> <li class="GroceryItem mui-panel">
>   <h4 class="item-name">
>     Click Here for More
>   </h4>
>   <span class="click-for-more">
>     <img class='item-image' src=? />
>   </span>
> </li>
> ```
>
> ⚠️ React likes `className=` not `class=`
> ⚠️ You interpolate dynamic and static values in JSX like this
>
> ````js
> <img alt={`my value is ${3 + 4}`} />
> ````

## Code Navigation & Refactoring

### Go To

[🎛 Refactoring](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/1_using/refactoring.md)

+ ...file
+ ...definition
+ ...type definition
+ ...symbol
  + ...in file
  + ...in workspace
+ ...line(by number)

### Selection

+ Select All <kbd>Cmd + A</kbd>
+ Select Next Occurrence <kbd>Cmd + D</kbd>
+ Select All Occurrence <kbd>Shift + Cmd + L</kbd>

### Multi-Cursor & Line Manipulation

#### Multi-Cursor

+ Add cursor @ position <kbd>Opt + Click</kbd>
+ Box select <kbd>Opt + Shift + Click</kbd>
+ Undo last cursor <kbd>Cmd + U</kbd>

#### Line Manipulation

+ Move line up <kbd>Opt + ⬆️</kbd>
+ Move line down <kbd>Opt + ⬇️</kbd>
+ Copy line below <kbd>Opt + Shift + ⬇️</kbd>

### Peek Editing

+ Opens up a split in editor
+ Peek at type definition
+ Find all References

### Renaming

+ Level 1: Find/Replace <kbd>Cmd + F</kbd>
+ Level 2: <kbd>Cmd + D</kbd> for selection
+ Level 3: "Extract into function" and <kbd>F2</kbd> for rename in ALL FILES

### Challenge 2: Refactoring

> + Make the following changes to [/client/data/listener-support.js](https://github.com/mike-works/vscode-fundamentals/blob/master/client/data/listener-support.js)
>   + `register` and `unregister` should be changed to `registerListener` and `unregisterListener`, respectively
>   + `fire` should be passed an object of the structure `{data: object[]}`
>     + Ensure that all places in the code that call `fire` are updated to use this new structure
>     + No code that registers listeners should need to be altered as a consequence of this change

## Type Checking

### Introducing Type Checking

[✅ Type-Checking](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/1_using/type-checking.md)

#### Typechecking

+ VS Code has TypeScript at its core
+ TypeChecking is becoming increasingly popular
  + React's [proptypes](https://reactjs.org/docs/typechecking-with-proptypes.html)(runtime)
  + Facebook's [flow](https://github.com/facebook/flow)(static)
  + Microsoft's [TypeScript](https://www.typescriptlang.org/)(static)

<br/><br/>

+ TypeScript is a *typed superset* of JavaScript
  + It heavily relies on [type inference](https://en.wikipedia.org/wiki/Type_inference) - the automatic deduction of the data type of an expression
+ As of [TypeScript 2.3](https://github.com/Microsoft/TypeScript/wiki/Type-Checking-JavaScript-Files) VS Code can apply type checking to your JavaScript
+ Type information can be added via [JSDoc](https://www.jianshu.com/p/46519b0499c3) comments!

### Benefits of Types

*Why would I want to use Types?*

+ Catch more bugs at compile time
+ Better developer experience
+ Your code becomes a better expression of your intent
  >
  > ```js
  > function add(x, y) { /* ? */}
  > ```
  >
+ Avoid common situations where your code is de-optimized

### Categories of Type Systems

#### Nominal Typing

Decisions about whether types are equivalent are made based on their name - Nominal type system

#### Structural Typing

Decisions about whether types are equivalent are made based on their structure - Structural type system

```ts
interface CartItem {
  item: {
    name: string
    description: string
    price: number
  }
  quantity: number
}
```

Because VS Code uses TypeScript to check our code, it uses **structural** type system.

### Type Checking in VS Code

*Activating Type-Checking in VS Code*.

+ If your project already is set up for typescript, just add `"checkJs": true` to your `tsconfig.json`.
+ Otherwise, add a comment at the top of your JS file.

```js
// @ts-check
```

### Annotating Types

*Annotating Types with JSDoc*.

+ Types can be added with comments
  
 ```js
/** @type {number} */
let value

value = 21 // ✅ Ok
value = '21' // 🚫 not Ok
```
  
+ Constructors are regarded as types too!
+ Remember, this is still structural!
  + TypeScript has a bulit-in type definition for the DOM API and other common JavaScript constructs
  
 ```ts
/** @type {Element} */
let x = document.querySelecto('.passwordField')
```

<br/><br/>

+ Sometimes we work with code that provides us with a less specific type than what we want

```ts
getFruit('orange'); // 🍊
getFruit('pear');   // 🍐
getFruit('grapes'); // 🍇
```

+ In the above class, perhaps we really wanted the JavaScript representation of an `<input>`, not just a generic element!

```ts
/** @type {HTMLInputElement} */
let x = document.querySelector('.passwordField')
// 🛑 Type 'Element' is not assignable to type 'HTMLInputElement'
// 🛑 Property 'accept' is missing in type 'Element'.
```

+ This would be no problem if `Element`'s structure was a superset of `HTMLInputElement`'s, but it's the other way around.
+ The TypeScript compiler has found that `Element` is lacking the [accept](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/Input) property.
+ We can perform an explicit type conversion(type casting)

```ts
/** @type {HTMLInputElement} */
let x = /** @type {HTMLInputElement} */(
  document.querySelector('passwordField')
)
```

+ At this point, the explicit declaration dosen't add any value

```ts
let x = /** @type {HTMLInputElement} */(
  document.querySelector('passwordField')
)
```

### Functions

+ Function is a first-class value type
+ We can also define a structure of a function(arguments)

```js
/** @type {function(MouseEvent)[]} */
let mouseListeners = []

addEventListeners('keypress', function(evt) {
  mouseListeners.forEach(ml => {
    ml(evt)
    // 🛑 Argument of type 'KeyboardEvent' is not assignable to parameter of type 'MouseEvent'.
    //    Property 'button' is missing in type 'KeyboardEvent'.
  })
})
```

+ Arrow functions => can also be used to define argument types

```js
/** @type {(request: Request) => Response} */
let requestHandler = function(x) {
  return new Response('<h1>Hello World</h1>')
}
```

+ Note that we have defined a return type as well here. We could also have used @return
+ **Keep in mind:** this is just static analysis. Function passed as arguments aren't checked for argument type alignment.

### Generics

+ Operate across many types
+ Still maintain compile-time type safety

```js
/**
 * Returns an array containing 5 of whatever you want
 * @template T
 * @param {T} item
 * @return {T[]}
 */
function gimmieFive(item) {
  return new Array(5).fill(item, 0, 5)
}

let students = gimmieFive('students')
students.push('teacher') // ✅

let objects = gimmieFive({key: 'Object'})
objects.push({})
// 🛑 Argument of type '{}' is not assignable to parameter of type '{ key: string; }'.
//   Property 'key' is missing in type '{}'.
```

+ Promises and other things that wrap other values (which have their own types) are defined using this concept of generics
+ Be careful when mixing types with [JSX](https://zh-hans.reactjs.org/docs/introducing-jsx.html)!

```js
/** @type {PromiseLike<string>} */
let x
x = Promise.resolve('hello world')
```

### Custom Types

*Defining our own named types*.

+ We can use @typedef to create a new named type of our own
+ Two ways to do this:

```js
/**
 * @typedef {Object} InventoryItem
 * @prop {string} name
 * @prop {number} price
 */

/**
 * @typedef {{qty: number, item: InventoryItem}} InvoiceItem
 */

/** @type {InvoiceItem[]} */
let invoice = [];
invoice.push({
  qty: 2,
  item: {
    name: 'Apple',
    price: 1.32
  }
}); // ✅

invoice.push({bad: 'thing'}); // 🛑
```

### Challenge 3: Data Layer Types

+ Updata the TypeScript compiler configuration to
  + `"CheckJs": true` - Type-check JavaScript files
  + `"noImplicitAny": true` - Complain about "implicit any" types (i.e, require us to provide an explicit type in spots where the compiler can't infer well enough)
+ Add a variety of JSDoc type annotations to
  + client/data/listener-support.js
    + Type of `this._listeners` be an array of function **with argument types specified**.
  + client/data/grocery-item-store.js
  + client/data/cart-store.js
    + You may find that you need to define a "grocery item" type in this file **and** `grocery-item-store.js`.
    + This is fine
  + client/data/order-store.js
+ Eliminate all complaints about implicit use of the `any` type (visible in the `problem` and `terminal`)
+ HINT: `ArrayLike<T>` covers things like `ReadonlyArray<T>` and `Array<T>`

### Debugging

#### Debugging Node

[🐞 Debugging](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/1_using/debugging.md)

Node has no GUI, so the built-in debugging experience is 🤢 (vomoticon). Let's take a look!

```bash
npm run debug
```

Thankfully, in [May 2016](https://www.youtube.com/watch?v=x8u0n4dT-WI&feature=youtu.be&t=2571) we got a new flag that basically

+ 🛑 Suspends global node.js execution on the first line of code
+ 🔗 Gives us a URL to open w/ chrome

```bash
npm run debug:attach
```

Under the hood, this runs `node --inspect --inspect-brk`.

### Debugging Node with Editors

This still involves a ton of context switching

+ 👎 Yes, this is a bad thing
+ 🔫 Multitasking stockholm syndrome(斯德哥尔摩综合症)
+ 🐟 Seems like we've been floundering around for a decade trying to get this right

![NetBeansIDE](https://github.com/mike-works/vscode-fundamentals/raw/master/public/debugging/netbeans.png)

![DeceloperTools](https://github.com/mike-works/vscode-fundamentals/raw/master/public/debugging/chrome-as-sublime.png)

![SublimeText](https://github.com/mike-works/vscode-fundamentals/raw/master/public/debugging/sublime-as-chrome.png)

👆 This one was/is actually really cool, but...

+ insanely hard to set up
+ and brittle
+ and looks like it works
+ but it doesn't really

There are some [community sourced Atom plugins](https://atom.io/packages/node-debugger)

![AtomDebuuger](https://github.com/mike-works/vscode-fundamentals/raw/master/public/debugging/atom.jpg)

But these plugins are complex and need a HUGE amount of effort to get right

+ sourcemaps
+ advanced debugging features
+ asynchrony
+ multiple threads
+ multiple debugging sessions

### Debugging Node with VS Code

+ Start node with same `--inspect` and `--inspect-brk` flags

```bash
npm run debug:inspect
```

+ Open the **Debugger** side panel
+ Create a new "lanuch configuration" that looks like this

```json
{
  "type": "node",
  "request": "attach",
  "name": "Attach",
  "sourceMaps": true,
  "port": 9229,
  "protocol": "inspector"
}
```

+ Now press the <kbd>Lunch▶️</kbd> button to attach the Node runtime
  + Inspector vs Legacy Protocol
  + Launch vs. Attach
  + Conditional breakpoints
  + Restart Frame
  + Column Breakpoints
  + Skipping Code

### Debugging Chrome with VS Code

We just need another launch configuration

```json
{
  "type": "chrome",
  "request": "launch",
  "name": "Launch Chrome",
  "url": "http://localhost:3000",
  "webRoot": "${workspaceRoot}"
}
```

We can lunch this at the same time as our node debugger

### Challenge 4: Muliti-Process Debugging

+ Try adding something to the cart. There's a suble bug involving a misunderstanding between client & server about > data format
+ Set a breakpoint in /client/data/cart-store.js's `_saveCart()` function right before the `PUT` request is sent
+ Set a breakpoint in /server/routes/cart.js's `.put("/items")` handler, keeping an eye on the body of the HTTP request as it lands
+ Use the above guidelines to create "attach" launch configurations for both Chrome and Node
+ Find and fix the problem
+ You should be able to (almost) step from one breakpoint to the other

## Workspace Customization

### Benefits of Customization

[🎨 Workspace Customization](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/2_customizing/workspace.md)

#### Why Customize Your Workspace

+ Developer spend LOTS of time in their editor
+ What can confirguration do for you?
  + Speed up your workflow
  + Make it easier to read code
  + Spot errors
  + Reduce fatigue

#### Levels of customization

+ 🖥Workspace `./.vscode/setting.json`
+ 👨‍💻‍User
  + Windows `%APPDATA%\Code\User\settings.json`
  + Mac `$HOME/Library/Application Support/Code/User/settings.json`
  + Linux `$HOME/.config/Code/User/settings.json`
+ [Defualt](https://code.visualstudio.com/docs/getstarted/settings#_copy-of-default-settings) that ship with the editor
+ Edit via <kbd>Cmd + ,</kbd>

### VS Code Themes

+ 🎨 Color Theme
+ 🗂 File Icon Theme
+ ⚙️ Customize colors in settings
+ 📚 [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), a variety of code editing features will be helpful here

```json
"workbench.colorCustomizations": {
  "contrastActiveBorder": "#0ff"
}
```

+ 🖍 Syntax Highlighting Customizations

```json
"editor.tokenColorCustomizations": {
  "comments": "#aaa",
  "types": "#f66",
  "textMateRules": [{
    "scope": "variable.parameter",
    "settings": {
      "fontStyle": "bold",
      "foreground": "#FF0000"
    }
  }],
  "functions": {
    "fontStyle": "italic"
  }
```

### Custom Fonts

#### Setting up a custom font

+ Lots of FREE fonts specifically for developers
  + [Fira Code](https://github.com/tonsky/FiraCode)
  + [Source Code Pro](https://github.com/adobe-fonts/source-code-pro)
  + [Terminus](http://terminus-font.sourceforge.net/)
  + [Hack](http://sourcefoundry.org/hack/)
  + [Input](http://input.fontbureau.com/)

```json
"editor.fontFamily": "Fira Code",
"editor.fontLigatures": true, // optional
"terminal.integrated.fontLigatures": true, // optional
```

### Key Bindings

+ Start with a Keymap from your preferred editor
  + Search for extensions: `@recommended:keymaps`
+ Basic customizations in Keyboard Shortcuts <kbd>Cmd + K</kbd> <kbd>Cmd + S</kbd>
+ Advanced customizations in `keybindings.json`

```json
{
  "key": "cmd+z",
  "command": "undo",
  "when": "editorTextFocus && !editorReadonly"
}
```

[Key Bindings for Visual Studio Code](https://code.visualstudio.com/docs/getstarted/keybindings)

### Code Snippets

+ Prefix is what you'll type (and then hit <kbd>Tab<kbd>)
+ `${1:300}` means "first placeholder, which initially has a value of 300"
+ When filling out this templete, repeated use of a given placeholder is updated

```json
"For Loop": {
  "prefix": "for",
  "body": [
    "for (var ${1:index} = 0; ${1:index} < ${2:array}.length; ${1:index}++) {",
    "\tvar ${3:element} = ${2:array}[${1:index}];",
    "\t$0",
    "}"
  ],
  "description": "For Loop"
},
```

### Challenge 5: VS Code Tune-Up

+ Make some UI color customizations, that apply across all projects
  + Panel: background `#033`
  + Badge: foreground `#000`, background: ``#ff0``
  + Status bar while debugging: foreground #000, background: `#ff0`
  + Currently active editor tab: foreground: `#0ff`
  + Peek editor: background: `#311`
+ Design your own syntax highlighting scheme. If you need ideas: [https://coolors.co/browser/best/1](https://coolors.co/browser/best/1)
+ Disable the minimap
+ Disable the indent guides
+ Create a code snippet for a placeholder image in HTML files
  + Shortcut: ph
  + Result: `<img src='http://placecorgi.com/{x}/{y}' />` where x and y default to 300

## Tasks

### Introducting Custom VS Code Tasks

[🗒 Tasks](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/2_customizing/tasks.md)

#### About Tasks

+ Run build steps, linting, testing, deploying scripts from within VS Code
+ Anything in a `gruntfile`, `Gulpfile`, [package.json](https://github.com/mike-works/vscode-fundamentals/blob/master/package.json) ("scripts") should be auto-detected
+ Where's the best place for these files to live?
  + Main stuff: `package.json` or [Scripty](https://github.com/testdouble/scripty)
  + VS Code specifics layered on top
+ Ability to parse "problems" from output, linking to file and line number

#### Custom tasks in tasks.json

+ Define one or more things in the `tasks` array
+ Basic Example of a `"type": "shell"` task

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "taskName": "echo",
      "type": "shell",
      "command": "echo Hello"
    }
  ]
}
```

#### Tasks: Shell Command Arguments

+ It's best to use the `"args"` property to ensure arguments are properly quoted

```json
{
  "taskName": "echo",
  "type": "shell",
  "command": "echo",
  "args": [
    "I said, 'Hello world'"
  ]
}
```

+ We can add a working directory option as well, to ensure this runs at our project root folder

```json
{
  "taskName": "echo",
  "type": "shell",
  "command": "echo",
  "args": [
    "I said, 'Hello world'"
  ],
  "options": {
    "cwd": "${workspaceRoot}"
  }
}
```

#### Tasks: Capturing output into "problems"

+ If the output of this command printed indications of problems in our code (at a particular file/line), we can use <kbd>Command</kbd> + Click 🖲 on a file path to open that file

```json
  "taskName": "echo",
  "type": "shell",
  "command": "echo",
  "args": [
    "client/index.tsx,LINE:8,this line stinks"
  ],
  "options": {
    "cwd": "${workspaceRoot}"
  },
```

+ But what about pointing out problems by line/column?

This task would output something like this:

```bash
client/index.tsx,LINE:8,this line stinks
```

What we need is a regular expression with [capture groups](https://www.regular-expressions.info/refcapture.html)

+ [https://regex101.com/](https://regex101.com/)
+ [http://regexr.com/](http://regexr.com/)

##### Cheat sheet

```bash
[0-9]        any number
[w]+         1 or more "w" characters
[w]*         0 or more "w" characters
[\s\w]+      1 or more "word" or "space"
LINE:([0-9]+)  grab digits after "LINE:"
```

**EXAMPLE:** If our string is `LINE:31`, and we use a regex:

```bash
LINE:([0-9]+)
```

+ Group 0: "LINE:31"
+ Group 1: "31"

#### Let's figure out the appropriate regex

Once we have this, we can use it within our task to detect the files, line #s, and specific details for any problems that are found:

```json
{
  "taskName": "echo",
  "type": "shell",
  "command": "echo",
  "problemMatcher": {
    "owner": "ts",
    "pattern": {
      "regexp": `<the regex>`,
      "file": 1,
      "line": 2,
      "message": 3
    }
  }
}
```

And we should see the following in our "problems" tab

![problems](https://github.com/mike-works/vscode-fundamentals/raw/master/public/tasks/problems.png)

and clicking on the problem should take us right to the appropriate line in the appropriate file

![redline](https://github.com/mike-works/vscode-fundamentals/raw/master/public/tasks/redline.png)

### Challenge 6: TSLint Problems

+ Think of it as "ESLint for TypeScript"
+ You can run this via `./node_modules/.bin/tslint --project <root of your workspace>`
+ <details>
  <summary>click for sample output</summary>
  <pre>
  ERROR: /Users/northm/Development/workshops/vscode/client/components/app-header/index.tsx[22, 7]: Identifier 'cartIcon' is never reassigned; use 'const' instead of 'let'.
  </pre>
  </details>

1. Create a custom task to run TSLint
2. Define a regex-based Problem Matcher to capture output, so that any issues found are shown in the "problems" panel
3. Fix all the bugs it points out (Cycle through problems with <kbd>F8</kbd>)

## Launch Confirguration

### Launch Setting

[🚀 Launch Configuration](https://github.com/mike-works/vscode-fundamentals/blob/master/docs/2_customizing/launch-configuration.md)

+ `launch.json` is where launch confirgurations live
+ We've dealt with launch & config in Debugging exercise 4
+ Let's break it down:

```json
{
  "type": "node", // Use the "node" debugger
  "request": "attach", // "attach" instead of "launch"
  "name": "Start my app",
  "sourceMaps": true, // Use sourcemaps for breakpoints
  "port": 9229, // Connect on :9229
  "protocol": "inspector" // Use the V8 inspector protocol, not the old V8 debugger protocl
}
```

#### `"type:" ?`

+ You will only need to worry about two types for now:
  + `"node"` for Node
  + `"chrome"` for Chrome
+ Others can be added via extensions

#### `runtimeExecutable:" ?`

+ This is the runtime that you pass your code into, to run
+ Default value: determined by `"type` of debugger
+ Practical Example: `./node_modules/.bin/ts-node`
+ Usually accompanied by something like `"program": "./index.js"`

#### `"restart:" ?` (Node)

+ Automatically re-executes the command when Node terminates
+ In an `"attach"` mode, VS Code will start listening for connections again and preserve your breakpoints

#### Console Options

+ Where does the output go?

```json
"console": "integratedTerminal" // "Terminal"
  | "externalTerminal" // your own terminal (must config)
  | "integratedConsole" // "Debug Console"
```

+ How the console at the bottom of the editor is dealt with:

```json
"internalConsoleOptions": "neverOpen" // Don't open
  | "openOnFirstSessionStart" // Open at first
  | "openOnSessionStart" // Open on every "restart"
```

#### Arguments & Environment

+ `"args": []` property takes care of quoting properly
+ `"env": {}` object is for environment variables
+ `"cwd": "./"` for working directory
  + Default to `"${workspaceRoot}"`

#### Variables

+ `${workspaceRoot}` - the path of the folder opened in VS Code
+ `${workspaceRootFolderName}` - the name of the folder opened in VS Code without any slashes (/)
+ `${file}` - the current opened file
+ `${relativeFile}` - the current opened file relative to workspaceRoot
+ `${fileBasename}` - the current opened file's basename
+ `${fileBasenameNoExtension}` - the current opened file's basename with no file extension
+ `${fileDirname}` - the current opened file's dirname
+ `${fileExtname}` - the current opened file's extension
+ `${cwd}` - the task runner's current working directory on startup
+ `${lineNumber}` - the current selected line number in the active file

### Challenge 7: Analyze & Monitor

Build two new launch configurations

+ Show a bundle analysis visualization
  + Ultimately this command needs to be run `ANALYIZE=true node ./server/index.js` (ANALYZE is an environment variable)
  + We don't want to have to "attach" to this. Just let it run
+ Launch our server, but use `nodemon` to have it restart as we save files
  + You'll need to install the `nodemon` npm package globally (`npm install -g nodemon`)
  + Ultimately the command you'll need to run is something like `nodemon ./server/index.js`
  + Running this should result in the debug console opening, but only when we "launch", not on every restart
  + We shouldn't have to "attach" to this. It should be a one-click launch

## Wrapping Up VS Code

### VS Code Extension Recommendations

[REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)

[css-triggers](https://marketplace.visualstudio.com/items?itemName=kisstkondoros.csstriggers)

[Code Runner](https://marketplace.visualstudio.com/items?itemName=formulahendry.code-runner)
