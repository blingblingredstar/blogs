---
title: JavaScript Performance
date: 2019-07-25 16:05:40
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, 性能优化]
---

## Introduction

### Thinking About Performance

[Course Notes and Materials](https://gist.github.com/stevekinney/fe401ffb8b2b7279e56dd165b272f0c3)

[Slides](https://speakerdeck.com/stevekinney/web-performance)

+ Prologue
  + What is performance and why does it matter?

+ Let's answer those in the opposite order, actually
<!-- more -->
+ Why dose performance matter?

> 0.1 second(100ms) is about the limit for having the user feel that the system is reaction instantaneously, meaning that no special feedback is necessary except to display the result. --Jakob Nielsen
> 0.1 second is about the limit for the user's flow of thought to stay uninterrupted, even though the user will notice the delay. Normally, no special feedback is necessary during delays of more than 0.1 but less than 1.0 second, but the user does lose the feeling of operating directly on the data. --Jakob Nielsen
> 10 seconds is about the limit for keeping the user's attention focused on the dialogue. For longer delays, users will want to perform other tasks while waiting for the computer to finish, so they should be given feedback indicating when the computer expects to be done. Feedback during the delay is especially important if the response time is ilkely to be highly variable, since users will then

+ If your user interface takes 10 seconds or more to respond to an interaction, then we should talk.

+ **Aberdeen Group** found that a 1 second slow down resulted 11% fewer page views, 7% less conversion.
  + [Source.](http://www.gomez.com/wp-content/downloads/Aberdeen_WebApps.pdf)
+ **Akamai** found that two-second delay in web page load time increase bounce rates by 103 percent.
  + [Source](https://www.akamai.com/us/en/about/news/press/2017-press/akamai-releases-spring-2017-state-of-online-retail-performance-report.jsp)
+ A 400 millisecond improvement in performance resulted in a 9% increase in traffic at **Yahoo**.
  + [Source](https://www.slideshare.net/stoyan/dont-make-me-wait-or-building-highperformance-web-applications#btnNext)
+ **Google** found that a 2% slower page resulted in 2% fewer searches, which means 2% fewer ads shown.
  + [Source.](http://assets.en.oreilly.com/1/event/29/Keynote%20Presentation%202.pdf)
+ 100 millisecond improvement in performance results in 1% increase in overall revenue at **Amazon**.
  + [Source.](http://radar.oreilly.com/2008/08/radar-theme-web-ops.html)
+ 53% of users will leave a mobile site if it takes more than 3 secs to load.
  + [Source.](http://gs.statcounter.com/press/mobile-and-tablet-internet-usage-exceeds-desktop-for-first-time-worldwide)
+ According to [research](https://www.smashingmagazine.com/2015/09/why-performance-matters-the-perception-of-time/#the-need-for-performance-optimization-the-20-rule), if you want user to feel like your site is faster than your competitors, you need to be **20% faster**.

+ But at the same time
  + Our applications are getting bigger
  + LTE is actually getting slower

+ There are different kind of performance.

   1. Network load performance
   2. Parsing JavaScript and Compilation performance
   3. Rendering performance

+ What matters to you?
  + The New York Times might care about time to first headline.
  + Twitter might care about time to first tweet.
  + Chrome might care about time to inspect element.
  + What does your product or project care about?

### The Importance of Measurement

+ Measure First

> Measure. Don’t tune for speed until you’ve measured, and even then don’t unless one part of the code overwhelms the rest. —Rob Pike

+ Do not go just blindly applying performance optimizations.

+ There is a cost to every abstraction and everything has a trade off.

+ Some things to think about while measuring
  + Are we testing performance on fancy MacBook Pros or consumer-grade hardware?
  + Are we simulating less-than-perfect network conditions.
  + What is our performance budget?

+ Thinking deeply about the architecture and design of your application is a better use of your time than micro-benchmarks.

+ Three Tiers of Advice
  + Definitely do this.
  + Maybe do this, but measure before and after.
  + Only do this if you find a performance problem that needs solving.

+ Steve’s Golden Rule of Performance
  + Doing Less Stuff Takes Less Time.
  + If you can do it later. Do it later.

+ Rough Outline
  + JavaScript performance: Write code that runs faster, later, or not at all.
  + Rendering performance: It turns out most of our JavaScript happens in the browser, which has its own performance concerns.
  + Load performance: Until the user actually gets the page, there isn’t much to optimize.

## Chapter 1: JavaScript Performance

### The Cost of JavaScript

+ Problem: You literally can’t buy faster servers to improve performance of client-side applications.
  + I mean, you could buy all of your customers faster computers, I guess.

+ A lot of time and energy is spent compressing assets, removing requests, and reducing latency, but what about once the application is running?
+ Sometimes, parsing and compiling is the real culprit.

+ Okay, so how does JavaScript even work?
  + Fun fact: JavaScript is a compiled language.
  + Most browsers use something called just-in-time (JIT) compilation.

+ Things to know about JIT compilation
  + It means that there is compilation step.
  + It means that it happens moments before execution.
  + That means it happens on our client’s machine.
  + That means they’re paying the cost and/or doing the hard work for us.

+ Let’s look at your code’s journey through V8 at a high level

Cloud => JS File => Parser => AST(Abstract Syntax Tree) => Interpreter => Optimizing Complier(Turbo Fan) => Machine Code(x86 || arm) => Byte Code

### Parsing

+ The source code is the true intention of the application, but the engine needs to figure out what this means.
+ Parsing can be slow. As slow as 1MB/s on mobile.
  + One way to reduce parsing time is to have less code to parse.
  + Another way is to do as much parsing as you need and as little as you can get away with.

+ Parsing happens in two phases
  + Eager (full parse): This is what you think of when you think about parsing.
  + Lazy (pre-parse): Do the bear minimum now. We’ll parse it for realsies later.
    + Generally speaking, this is a good thing™.
    + Doing less work is faster than doing work, right?

+ The basic rules
  + Scan through the top-level scope. Parse all the code you see that’s actually doing something.
  + Skip things like function declarations and classes for now. We’ll parse them when we need them.

```js
// These will be eagerly-parsed.
const a = 1;
const b = 2;
// Take note that there a function here,
// but, we'll parse the body when we need it.
function add(a, b) {
 return x + y;
}
add(a, b); // Whoa. Go back and parse add()!
```

+ Corollary: Doing stuff twice is slower than doing it once.

```js
const a = 1;
const b = 2;
// Parse it now!
(function add(a, b) {
 return x + y;
});
add(a, b);
```

+ It’s definitely helpful to know how this works, but…

+ micro-optimization (noun): Thing you read about one time and you know pester your co-works about in code reviews, even though it has an almost unnoticeable impact at scale.

### Exercise: Reducing Parsing Times

+ An exploration of why measuring is important

[Lab](https://nolanlawson.github.io/test-optimize-js/)

+ Try to avoid nested functions

```js
function sumOfSquares(x, y) {
  // This will repeatedly be parsed.
  function square(n) {
    return n * n;
  }
  return square(x) + square(y);
}
```

+ Better…

```js
function square(n) {
  return n * n;
}
function sumOfSquares(x, y) {
  return square(x) + square(y);
}
```

### ASTs & Initial Execution

+ Okay, cool—so it’s parsed. Now what?
+ It’s turned into an abstract syntax tree.

> In computer science, an abstract syntax tree (AST) […] is a tree representation of the abstract syntactic structure of source code written in a programming language. — Wikipedia

+ Essential, we’ve gone from a big long string of text to an actual data structure representing our code.
+ With our AST, we now have everything we need to make byte code!
+ The baseline compiler takes the AST and starts to execute our code as we wrote it

### The Optimizing Compiler

+ Three things the engine does to help you out
   1. Speculative optimization
   2. Hidden classes for dynamic lookups
   3. Function inlining

+ It turns out that JavaScript is hard.
+ It also turns out that JavaScript is dynamic.
+ But, what if we made some assumptions based on what we’ve seen in the past?

在node环境可以使用`node --trace-opt <filename>`来查看优化情况。

### Deoptimization, Deleting Properties

在node环境，可以使用`node --tarce-deopt <filename>`来查看没有优化情况。

使用一个`add`函数，可以发现使用数字作为参数时函数被引擎进行了优化，但是使用字符串相加时没有进行优化。

+ We use a system called speculative optimization.

+ How does this work?
  + We use an interpreter because the optimizing compiler is slow to get started.
  + Also: it needs some information before it knows what work it can either optimize or skip out on all together.
  + So, the interpreter starts gathering feedback about what it sees as the function is used.

+ But what if a string slips in there?
  + The optimizing compiler optimizes for what it’s seen. If it sees something new, that’s problematic.

### Exercise: Deleting, Feeding Objects

新建一个构造函数

```js
class Point() {
  constructor(x, y, z) {
    this.x = x
    this.y = y
    this.z = z
  }
}
```

创建Point的实例，每次首先分别给每个属性赋值为undefined，测量JSON.stringify的时间，然后删除每个属性，测量时间。

可以看到删除最后一个属性速度会快一些。

### Monomorphism, Polymorphism, and Megamorphism

为什么删除对象最后一个属性速度会快一些，事实说明在v8引擎中有一个类型系统。

This is not just for objects

+ morphism.
  + Monomorphic: This is all I know and all that I’ve seen. I can get incredibly fast at this one thing.
  + Polymorphic: I’ve seen a few shapes before. Let me just check to see which one and then I’ll go do the fast thing.
  + Megamorphic: I’ve seen things. A lot of things. I’m not particularly specialized. Sorry.

### Optimizing Objects

可以看到，只有两个对象创建时属性名和值的类型一致，过程中一致（添加同一属性），并且创建方式一致，才会判定为同一行为。

### Hidden Classes

+ Dynamic lookup: This object could be anything, so let me look at the rule book and figure this out.
+ Sure, computers are good at looking stuff up repeatedly, but they’re also good at remembering things.
+ It turns out there is a secret type system behind your back.

### Scoping & Prototypes

+ Takeaways
  + Turbofan is able to optimize your code in substantial ways if you pass it consistent values.
    + Initialize your properties at creation.
    + Initialize them in the same order.
    + Try not to modify them after the fact.
    + Maybe just use TypeScript or Flow so you don’t have to worry about these things?

### Function Inlining

可以看到有时将一些简单操作抽象为函数并不会明显的降低性能。

使用`node --trace-turbo-inlining <filename>`会发现v8引擎将简单函数自动写入调用其的函数中。

### JavaScript Performance Takeaways

+ Larger Takeaways
  + The easiest way to reduce parse, compile, and execution times is to ship less code.
  + Use the User Timing API to figure out where the biggest amount of hurt is.
  + Consider using a type system so that you don’t have to think about all of the stuff I just talked about.

## Rendering Performance

### How Web Pages Are Built

浏览器发送请求到服务器，服务器返回html，浏览器解析html为文档树（DOM），然后解析css文件，生成CSSOM(Cascading style sheet object model)，然后将两者结合生成Render Tree。

+ The Render Tree
  + The Render Tree has a one-to-one mapping with the visible objects on the page.
    + So, not hidden object.
    + Yes, to pseudo elements (e.g. :after, :before).
  + There might be multiple rules that apply to a single element. We need to figure that all out here.

+ Style calculation: The browser figures out all of the styles that will be applied to a given element
+ This involves two things:
  + Figuring out which rules apply to which elements.
  + Figuring out how what the end result of an element with multiple rules is.

+ Styling Elements: Selector Matching
  + This is the process of figuring out what styles apply to an element.
  + The more complicated you get, the longer this takes.
  + Class names are super simple.
  + `.sidebar > .menu-item:nth-child(4n + 1)`This will need more time to figure out.

> **Free Advice**: Stick to simple class names whenever possible. Consider using BEM

+ Browsers read selectors from right to left.
+ The less selectors you use, this faster this is going to be.

+ Takeaways
  + Use simple selectors whenever possible.
    + Consider using BEM or some other system.
  + Reduce the effected elements.
    + This is really a way to get to the first one.
    + A little bit of code—either on the server or the client—can go a long way.

+ Styling Elements: Calculating Render Styles
  + Selector matching tries to figure out what selectors apply to an element.
  + When multiple selectors apply to an element. The browser needs to figure out who wins.
  + The easiest way to make this faster is to not do it.

> **Free Advice (again):** Stick to simple class names whenever possible. Consider using BEM.
> **A quick note on style invalidation**: It doesn’t matter as much in newer browsers.

+ Some Takeaways
  + Reduce the amount of unused CSS that you’re shipping.
    + The less styles you have, the less there is to check.
  + Reduce the number of styles that effect a given element.

+ Layout (a.k.a Reflow): Look at the elements and figure out where they go on the page.
+ Paint: We know what things should look like and where they should go. Draw some pixels to the screen.
+ Composite Layers: You might end up painting on multiple layers, but you’ll eventually need to combine them

### JavaScript & the Render Pipeline

+ **JavaScript** gives you the ability to change all of this after the initial load, which means you might have to do all of the above *again*.
+ Things JavaScript can do: An incomplete list™
  + Change the class on an object.
  + Change the inline styles on an object.
  + Add or remove elements from the page.

+ The Render Pipeline
  + JS => style => layout => paint => composite

+ Okay, so let’s say you change a class or inline style on an element.
+ The computed styles could have changed—so, we better recalculate those and rebuild the render tree.
+ That may or may not have changed the geometry of the objects. We should probably re-layout the page.
+ Things are different. I guess we need to paint some new images.
+ Send those images off to the GPU to be composited.

> To be clear: You don’t need to do all of these things every time.
> Reminder: Steve’s golden rule of performance

### Layout & Reflows

> Reflows are very expensive in terms of performance, and is one of the main causes of slow DOM scripts, especially on devices with low processing power, such as phones. In many cases, they are equivalent to laying out the entire page again. —Opera
> (Browser implementations have different ways of optimizing this, so there is no point sweating the details in this case.)

+ Tasting Notes
  + A reflow is a blocking operation. Everything else stops.
  + It consumes a decent amount of CPU.
  + It will definitely be noticeable by the user if it happens often (e.g. in a loop).

+ A reflow of an element causes a reflow of its parents and children.

+ Okay, so what causes a reflow?
  + Resizing the window
  + Changing the font
  + Content changes
  + Adding or removing a stylesheet
  + Adding or removing classes
  + Adding or removing elements
  + Changing orientation
  + Calculating size or position
  + Changing size or position
  + (Even more…)
+ Generally speaking, a reflow is followed by a repaint, which is also expensive.

+ How can you avoid reflows?
  + Change classes at the lowest levels of the DOM tree.
  + Avoid repeatedly modifying inline styles.
  + Trade smoothness for speed if you’re doing an animation in JavaScript.
  + Avoid table layouts.
  + Batch DOM manipulation.
  + Debounce window resize events

### Exercise: Layout & Reflows

[Lab](https://codepen.io/stevekinney/full/eVadLB/)

### Layout Trashing

+ Less cool names: Forced synchronous layout.
+ There are a set of things you can do that cause the browser to stop what it’s doing and calculate style and layout.

> Layout Thrashing occurs when JavaScript violently writes, then reads, from the DOM, multiple times causing document reflows. —Winston Page

+ `const height = element.offsetHeight;`
  + The browser wants to get you the most up to date answer, so it goes and does a style and layout check.

```js
firstElement.classList.toggle('bigger'); // Change!
const firstElementWidth = firstElement.width; // Calculate
secondElement.classList.toggle('bigger'); // Change!
const secondElementWidth = secondElement.width; // Calculate
```

+ The browser knew it was going to have to change stuff after that first line.
+ Then you went ahead and asked it for some information about the geometry of another object
+ So, it stopped your JavaScript and reflowed the page in order to get you an answer.
+ **Solution**: Separate reading from writing

```js
firstElement.classList.toggle('bigger'); // Change!
secondElement.classList.toggle('bigger'); // Change!
const firstElementWidth = firstElement.width; // Calculate
const secondElementWidth = secondElement.width; // do nothing
```

### Solving for Layout Trashing

可以看到当把获取元素位置写在循环外时，会显著提高性能表现。

但是代码分离有时会造成无法准确定位代码的问题。所以可以使用`requestAnimationFrame`来重绘页面。

### FastDOM

+ It sounds like we could use a better abstraction, right?

[fastdom-Eliminates layout thrashing by batching DOM measurement and mutation tasks](https://github.com/wilsonpage/fastdom)

```js
fastdom.measure(() => {
 console.log('measure');
});
fastdom.mutate(() => {
 console.log('mutate');
});
fastdom.measure(() => {
 console.log('measure');
});
fastdom.mutate(() => {
 console.log('mutate');
});
```

### Frameworks & Layout Thrashing

+ React to the rescue?

```js
class App extends Component {
  state = { widths: [50, 100, 150] }
  doubleSize = () => {
    const widths = this.state.widths.map(n => n * 2)
    this.setState({ widths })
  }
  render() {
    const [firstWidth, secondWidth, thirdWidth] = this.state.widths
    return (
      <div>
        <button onClick={this.doubleSize}>Double Sizes!</button>
        <div style={{ width: firstWidth }} />
        <div style={{ width: secondWidth }} />
        <div style={{ width: thirdWidth }} />
      </div>
    )
  }
}
```

[Lab](https://2z379l1pjr.codesandbox.io/)

+ **Friendly fact**: Production mode is important in React!

+ Some Takeaways
  + Don’t mix reading layout properties and writing them—you’ll do unnecessary work.
  + If you can change the visual appearance of an element by adding a CSS class. Do that, you’ll avoid accidental trashing.

### Painting

+ Painting, Layers, the Profiling Thereof
  + Anytime you change something other than opacity or a CSS transform… you’re going to trigger a paint.
  + When we do a paint, the browser tells every element on the page to draw a picture of itself.
  + It has all of this information form when we constructed the render tree and did the layout.
  + Triggering a layout will always trigger a paint.
  + But, if you’re just changing colors or something—then you don’t need to do a reflow. Just a repaint.
  + Use your tools to see if you’re painting.

> **Rule of Thumb**: Paint as much as you need and as little as you can get away with.

### The Compositor Thread

+ **An Aside**: The Compositor Thread

+ Nice threads
  + The UI thread: Chrome itself. The tab bar, etc.
  + The Renderer thread: We usually call this the main thread. This is where all JavaScript, parsing HTML and CSS, style calculation, layout, and painting happens. There are one of these per tab.
  + The Compositor Thread: Draws bitmaps to the screen via the GPU.

+ The Compositor Thread
  + When we paint, we create bitmaps for the elements, put them onto layers, and prepare shaders for animations if necessary.
  + After painting, the bitmaps are shared with a thread on the GPU to do the actual compositing.
  + The GPU process works with OpenGL to make magic happen on your screen.

+ The Main Thread is CPUintensive.
+ The Compositor Thread is GPU-intensive.

+ It can go off and work on some super hard JavaScript computation and the animations will still chug along.
+ This is cool, because it frees up the main thread to do all of the work it’s responsible for.

### Managing Layers

+ Again: Painting is super expensive and you should avoid it whenever possible.

> But, Steve—how do I avoid painting? Isn’t that just a fact of life when it comes to getting pixels on the screen? —Your inner monologue
> “Let the Compositor Thread handle this stuff!” — Me, in response

+ Things the compositor thread is really good at:
  + Drawing the same bitmaps over and over in different places.
  + Scaling and rotating bitmaps.
  + Making bitmaps transparent.
  + Applying filters.
  + Mining Bitcoin.

+ If you want to be fast, then offload whatever you can to the less-busy thread.
+ **Disclaimer**: Compositing is kind of a hack
+ Layers are an optimization that the browser does for you under the hood.

+ What kind of stuff gets its own layer?
  + The root object of the page.
  + Objects that have specific CSS positions.
  + Objects with CSS transforms.
  + Objects that have overflow.
  + (Other stuff…)

+ Objects that don’t fall under one of these reasons will be on the same element as the last one that did.
  + (Hint: The root object is always its own layer.)
+ You can give the browser hints using the will-change property

### Will-Change

```css
.sidebar {
  will-change: transform;
}
```

```css
.sidebar {
  /* a hack to make a layer */
  transform: translateZ(0);
}
```

+ Using layers is a trade off.
  + Managing layers takes a certain amount of work on the browser’s behalf.
  + Each layer needs to be kept in the shared memory between the main and composite threads.

+ This is a terrible idea.

```css
* {
  will-change: transform;
}
```

+ The browser is already trying to help you out under the hood.

> **Pro Tip**: will-change is for things that will change. (Not things that are changing.)

+ Promoting an object to its own layer takes a non-zero amount of time.

```css
.sidebar {
 will-change: transform;
  transition: transform 0.5s;
}
.sidebar:hover {
 will-change: transform;
}
.sidebar.open {
  transform: translate(400px);
}
```

### Applying will-change with JavaScript

+ will-change is tricky because while it’s a CSS property, you’ll typically access it using JavaScript.

```js
element.addEventListener('mouseenter', () => {
 element.style.willChange = 'transform';
});
```

+ If it’s something that the user is interacting with constantly, add it to the CSS. Otherwise, do it with JavaScript
+ **Clean up after yourself.** Remove willchange when it’s not going to change anymore.

```js
element.addEventListener('mouseenter', () => {
 element.style.willChange = 'transform';
});
element.addEventListener('animationEnd', () => {
 element.style.willChange = 'auto';
});
```

### Exercise: will-change

+ Let’s look at the “Paint Storming” example.
+ Don’t be surprised if you find a paint storm.
+ Can you swap out that jQuery animation for a CSS transition?
+ Can you put the will-change on before the transition?
+ Can you remove it after?

## Load Performance

### Latency and Bandwidth

+ **Latency and Bandwidth**: A Journey of Self-Discovery

> Networks, CPUs, and disks all hate you. On the client, you pay for what you send in ways you can't easily see. —Alex Russell

+ Bandwidth vs. Latency
  + Bandwidth is how much stuff you can fit through the tube per second.
  + Latency is how long it takes to get to the other end of the tube.

[YouTuBe - Why Latency Matters: Foundations of Web Performance](https://www.youtube.com/watch?v=ak4EZQB4Ylg)

+ TCP focuses on reliability
  + We keep checking in with the server to make sure that everything is going well.
  + Packets are delivered in the correct order.
  + Packets are delivered without errors.
  + Client acknowledges each packet.
  + Unreliable connections are handled well.
  + Will not overload the network.

+ TCP starts by sending a small amount of data and then starts sending more and more as we find out that things are being successful.
+ **Fun fact**: This is why things feel so much worse on a slow Internet connection.
+ **Pro tip**: The initial window size is 14kb. So, if you can get files under 14kb, then it means you can get everything through in the first window. Very cool.

[Lab](http://www.cloudping.info/)

+ Hmm… So, where is the optimal place to put our assets?
+ Answer: Everywhere.

### Caching

+ HTTP/1.1 added the CacheControl response header.
+ Caching only affects the "safe" HTTP methods.
  + GET
  + OPTIONS
  + HEAD
+ It doesn’t support … because how would it?
  + PUT
  + POST
  + DELETE
  + PATCH

+ Cache-Control header
  + no-store
  + no-cache
  + max-age
  + s-maxage
  + immutable

+ Three over-simplified possibilities
  + Cache Missing: There is no local copy in the cache.
  + Stale: Do a Conditional GET. The browser has a copy but it's old and no longer valid. Go get a new version.
  + Valid: We have a thing in cache and its good—so, don't even bother talking to the server.

```js
const express = require('express');
const serveStatic = require('serve-static');

const app = express();

app.use(serveStatic(__dirname, {
 setHeaders(response, path) {
 response.setHeader('Cache-Control', 'no-store');
 }
}));
const port = process.env.port || 3000;
app.listen(post, () => console.log(`⚓ Ahoy! The server is listening on port ${port}!`));
```

+ **no-store**: The browser gets a new version every time.
+ **no-cache**: This means you can store a copy, but you can't use it without checking with the server.
+ **max-age**: Tell the browser not to bother if whatever asset it has is less than a certain number of seconds old

+ Caching is great unless you mess it up
+ We can say "Yo, cache this for a long time!”
+ But, what if we ship some bunk assets? Oh no.
+ How the will the user know to do a hard refresh to get the new ones?

+ Another solution: **Content-Addressable Storage**
+ `main.567eea7aa72b3ee48649.js`，在文件名中加入哈希值，如果一致直接使用缓存，版本更新时更改文件哈希值。

+ Caching for CDNs
  + CDNs respect the max-age header just like browsers. But this opens up a new can of worms.
    + We want CSS and JavaScripts to be cached by the browser.
    + We would like the CDN to cache the HTML that it serves up. But we don't want the browser to (because that ends us up in our earlier problem).

+ s-maxage is for CDNs only. Tell the CDN to keep it forever. But don't tell the browser to do it.
+ To reiterate: We have no way to reach into all of our customers browsers and tell them to purge their caches of our assets, but we can tell the CDN to

### Service Worker

[progressive-web-apps](https://frontendmasters.com/courses/progressive-web-apps/)

### Lazy Loading

+ Lazy-loading and pre-loading with React and webpack
+ And now: A review of Steve’s golden rules for performance.
  + Not doing stuff is faster than doing stuff.
  + Doing stuff later is a way to not do stuff now. So, it’s faster.

### Lazy Loading Demonstrations

### Analyzing Bundle Sizes

`webpack-bundle-analyzer`

### Slimming Dependencies

可以看到，之前使用`import _ form 'lodash'`时引入了整个lodash包，更改语法为`import {transform} from 'lodash'`后只引入了transform方法。或者也可以使用`babel-plugin-lodash`来使用babel自动转译语法。

### Lazy Loading components with React-Loadable

[react-loadable](https://github.com/jamiebuilds/react-loadable)

使用该插件，可以将首屏用不到的组件懒加载，减少首屏所需文件的体积。

### Exercise: Component Lazy Loading

可以在componentDidMount周期预先加载可能用到的组件来减少加载时间。

+ Takeaways
  + Some libraries have code you don’t need. See if you can get that out of your build.
  + Get the code you need now now.
  + Get the code you need later later.
  + Your tools can help you do this

### HTTP/2

+ HTTP/2: What even are you?
  + An upgrade to the HTTP transport layer.
  + Fully multiplexed—send multiple requests in parallel.
  + Allows servers to proactively push responses into client caches.

+ HTTP/1.1: What’s wrong with you?
  + Websites are growing: more images, more JavaScript
  + Sure, bandwidth has gotten a lot better, but roundtrip time hasn’t
  + It takes just as long to ping a server now as it did 20 years ago.
  + That’s right: one file at a time per connection
  + No big deal. It’s not like we are building websites that request 100 files to something.

+ The weird thing is that once you have this in place some “best practices” become not-so-good.
+ Is concatenating all of your JS and CSS into large, single files still useful?
+ What about inlining images as data URLs in our CSS?
+ Measure, measure, measure.

## Tools

### Introduction to Using Build Tools

+ Build It Faster. Build It Smarter.
+ When in doubt, let your tools help you.
+ [purifycss](https://github.com/purifycss/purifycss)

### Paying the Babel Tax

可以看到babel转译许多新语法时会生成许多代码，造成文件体积增加。

### Useful Babel Plugins

可以使用`@babel-env-preset`来设置目标浏览器来减少不必要的转译。

### Prepack

[prepack - Prepack is a partial evaluator for JavaScript.](https://github.com/facebook/prepack)
