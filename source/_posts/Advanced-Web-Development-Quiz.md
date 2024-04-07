---
title: Advanced Web Development Quiz
date: 2024-04-06 17:08:57
categories: 学习笔记
tags: [FrontEndMasters]
---

# Advanced Web Development Quiz

## Question 1 - async & defer execution order

- Put the scripts in the right order of execution

```html
<script async src="async1.js" />
// Loads in 300ms
<script defer src="defer1.js" />
// Loads in 200ms
<script defer src="defer2.js" />
// Loads in 300ms
<script async src="async2.js" />
// Loads in 50ms
<script async defer src="async-defer.js" />
// Loads in 60ms
```

the order of execution is:

1. async2.js
2. async-defer.js
3. async1.js
4. defer1.js
5. defer2.js

- `async` scripts are executed as soon as they are loaded, while `defer` scripts are executed only after the HTML document has been fully parsed.
- `async` and `defer` both exists in the same script tag, `async` takes precedence over `defer`.

<!-- more -->

- 默认情况下，当浏览器遇到一个`<script>`标签时，它会立即下载脚本并阻塞页面的渲染，直到脚本被完全下载并执行。这可能会导致页面的加载速度变慢，尤其是当脚本很大或者网络连接慢的时候。

- async 属性可以让浏览器异步地下载脚本，这意味着脚本的下载和页面的渲染可以同时进行。然后，一旦脚本被下载完毕，浏览器会暂停页面的渲染，执行脚本，然后再继续渲染页面。如果有多个 async 脚本，它们可能会按照下载完成的顺序，而不是它们在 HTML 中出现的顺序被执行。

- defer 属性也可以让浏览器异步地下载脚本，但是它会保证脚本在页面被完全解析之后，DOMContentLoaded 事件触发之前被执行。如果有多个 defer 脚本，它们会按照它们在 HTML 中出现的顺序被执行。

- 总的来说，async 和 defer 都可以提高页面的加载速度，但是它们在脚本的执行顺序和时机上有所不同。你应该根据你的脚本是否依赖于其他脚本或者 DOM，以及你的脚本是否需要在页面被用户看到之前就被执行，来选择使用哪一个属性。

## Question 2 - Rendering Pipeline & Compositing

- Which statements are true

1. The render tree contains all elements from the DOM and CSSOM combined. > False, the hidden elements are not included in the render tree.
2. Composition is the process of separating layers based on `z-index`, which are then combined to form the finial image displayed on the screen. > False, the composition is the process of combining the layers to form the final image. The `z-index` is used in the layout process.
3. The layout process assigns colors and images to the visual elements in the render tree. > False, the layout process calculates the position and size of the elements, the color and images are assigned in the composite process.
4. THe compositing process happens on the compositor thread. > True, the compositing process is done on the GPU.
5. Elements that aren't visible on the page, for example `display: hidden`, aren't part of the DOM tree. > False, they are part of the DOM tree but not part of the render tree.

- Layout -> Paint -> Composite

## Question 3 - Resolving Domain Requests

- Browser sends requests to Recursive DNS resolver,
  - The recursive DNS resolver sends requests to the root name server.
  - The root name server sends requests to the Top-Level Domain (TLD) name server.
  - The TLD name server sends requests to the authoritative name server.
  - The authoritative name server sends the IP address back to the recursive DNS resolver.

## Question 4 - Call Stack & Event Loop

- What gets logged?

```js
setTimeout(() => console.log('1'), 0);

Promise.resolve().then(() => console.log('2'));

Promise.resolve().then(() => setTimeout(() => console.log('3'), 0));

new Promise(() => console.log('4'));

setTimeout(() => console.log('5'), 0);
```

The order of execution is:

1. 4
2. 2
3. 1
4. 5
5. 3

## Question 5 - Resource Hints

- Match the resources hints with their definitions
  - `dns-prefetch` - perform domain name resolution in the background
  - `preconnect` - proactively performs DNS resolution and `TCP/TLS` handshake.
  - `prefetch` downloads resources that are likely to be needed in the future.
  - `preload` - prioritizes fetching of critical resources needed for the current navigation.

## Question 6 - Object Reference & Destructuring

- What gets logged?

```js
const member = {
  name: 'Jane',
  address: { street: '123 Main St' },
};

const member2 = { ...member };

member.address.street = '456 Main St';
member.name = 'John';
console.log(member2);
// { name: "Jane", address: { street: "456 Main St" } }
```

## Question 7 - PerformanceNavigationTiming

- Put the `PerformanceNavigationTiming` in the right order

  - `loadEventStart` - the time when the load event was sent for the current document.
  - `domComplete` - the time when the `DOM` was parsed and the resources were loaded.
  - `domContentLoadedEventStart` - the time when the `DOMContentLoaded` event was sent.
  - `fetchStart` - the time when the browser starts to fetch the resource.
  - `connectEnd` - the time when the connection to the server was closed.
  - `domInteractive` - the time when the `DOM` was fully parsed and the resources were loaded.
  - the correct order is `fetchStart` -> `connectEnd` -> `domInteractive` -> `domContentLoadedEventStart` -> `domComplete` -> `loadEventStart`.

- DNS lookup -> `connectStart` -> TCP/TLS handshake -> `secureConnectionStart` -> `connectionEnd` -> HTTP Request -> HTTP Response -> `domLoading` -> Processing -> `domInteractive` -> `domContentLoadedEventStart` -> `domContentLoadedEventEnd` -> `domComplete` -> `loadEventStart` -> `loadEventEnd`.

## Question 8 - Cache Directives

- Match the cache directives with their definitions
  - `no-cache` - validates a stale response with the origin server before using it, even if it's fresh.
  - `must-revalidate` - validates a stale response with the origin server before using it.
  - `stale-while-revalidate` - servers stable content while revalidating the cached response with the origin server.
  - `no-store` - doesn't cache any part of the request or response.
  - `private` - prevents caching on shared caches.

## Question 9 - Garbage Collection

- What is true about this code?

```js
function addMember(name) {
  return { name, createAt: Date.now() };
}

let obj1 = addMember('John');
let obj2 = addMember('Sarah');

obj1.friend = obj2;
obj2.friend = obj1;

obj1 = null;
obj2 = null;
```

1. The `obj1` and `obj2` can't be garbage collected, leading to a memory leak. > False, the `obj1` and `obj2` are still referenced by each other, but they are not reachable from the root, so they can be garbage collected.
2. `obj1` and `obj2` will be garbage collected immediately after setting them to `null`. > False, the garbage collection process is non-deterministic.
3. `obj1` and `obj2` will only be garbage collected after closing the browser tab. > False, the garbage collection will happen by the garbage collector, but will not wait for the browser tab to close.
4. `obj1` and `obj2` can be garbage collected during the next garbage collection cycle. > True, the `obj1` and `obj2` are not reachable from the root, so they can be garbage collected.

## Question 10 - Animation Cost

- When animating the following properties, which have the correctly listed rendering costs?
  - width - layout paint composite > true
  - opacity - paint composite > false, only composite
  - background-image - composite > false, needs paint
  - left - layout paint composite > true
  - transform - paint composite > false, only composite

## Question 11 - Event Propagation

- What gets logged when the button is clicked?

```html
<div class="outer">
  <div class="inner">
    <button>Click me</button>
  </div>
</div>
```

```js
outer.addEventListener('click', () => console.log('a'), true);
outer.addEventListener('click', () => console.log('b'));
inner.addEventListener('click', () => console.log('c'), true);
inner.addEventListener('click', (e) => {
  console.log('d');
  e.stopPropagation(); // stop the event propagation, so the event won't bubble up to the outer div, `b` won't be logged.
  console.log('e');
});
button.addEventListener('click', () => console.log('f'));
button.addEventListener('click', () => console.log('g'), true);
```

The order of execution is:

a -> c -> g -> f -> d -> e

## Question 12 - CSS Specificity

- Order the following selectors by specificity

```css
div h1.large-text::before // 0 0 1 3
div h1:first-child // 0 0 1 2
h1:not(.small-text) // 0 0 1 1
.large-text:nth-child(1) // 0 0 2 0
h1.large-text[id="title"] // 0 0 2 1
h1.large-text#title // 0 1 1 1
```

## Question 13 - WeakMap

- What statements are true?

```js
const userTokenMap = new WeakMap();
let user = {
  name: 'John',
  age: 30,
};

userTokenMap.set(user, 'token');
```

1. The `userTokenMap` implements the iterator protocol. > False, the keys of the `WeakMap` are weakly referenced, so they can't be iterated.
2. When setting the `user` to `null`, `userTokenMap.size` returns 0. > False, the `user` is weakly referenced, so it can be garbage collected, but the `userTokenMap.size` will still return 1 until the next garbage collection cycle.
3. If the `user` object is set to `null`, the `userTokenMap` entry can be garbage collected. > True, the `user` object is weakly referenced, so it can be garbage collected, and the `userTokenMap` entry will be removed.
4. `[...userTokenMap]` returns an array of `userTokenMap` entries. > False, the `WeakMap` can't be iterated.

## Question 14 - Web Vitals

- Match the web vitals with their definitions
  - `TTFB` - The time it takes for the server to respond to a request and start sending data back to the client.(Time to First Byte)
  - `FID` - The time it takes for a webpage to respond to a user's first interaction.(First Input Delay)
  - `TTI` - The time it takes for a webpage to be fully loaded and responsive to user input.(Time to Interactive)
  - `TBT` - The time that the main thread is blocked for from responding to user input.(Total Blocking Time)
  - `CLS` - The stability of a webpage's layout, or the unexpected layout shifts that occur on a webpage as it loads.(Cumulative Layout Shift)
  - `INP` - THe average time it takes for a webpage to update its visual after a user interacts with it.(Interaction to Next Paint)

## Question 15 - Content Security Policy CSP Header

> Content Security Policy (CSP) 是一种安全标准，它可以帮助防止跨站脚本攻击 (XSS) 和其他跨站注入攻击。CSP 通过定义哪些内容是安全的，哪些内容是不安全的，来限制网页中可以加载和执行的资源。

> CSP 通过 HTTP 头部 Content-Security-Policy 来设置。这个头部的值是一系列的指令，每个指令都定义了一种资源的来源。例如，script-src 'self' 指令表示只允许从同源加载脚本。

- Which resources will be allowed with the following CSP header?

`default-src 'none'; script-src 'self'; img-src "self" example.com; style-src fonts.googleapis.com; font-src fonts.gstatic.com;`

1. `<script src="/js/app.js"></script>` - Allowed, the `script-src` directive is set to `'self'`.
2. `fetch('https://api.example.com/data')` - Not allowed, the `connect-src` directive is not specified, so it's falling back to the `default-src` directive which is set to `'none'`.
3. `@font-face { url('fonts/myfont.woff2') }` - Not allowed, the font is only allowed from `fonts.gstatic.com`.
4. `<img src="data:image/png;base64,..." />` - Not allowed, the image with `data:` URL is not allowed.
5. `<style>body { font-family: 'Roboto'; }</style>` - Not allowed, the `default-src` directive is set to `none`, and `style-src` is only allowed from `fonts.googleapis.com`.
6. `<iframe scr="https://example.com"></iframe>` - Not allowed, no `frame-src` directive is specified.
7. `<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto" />` - Allowed, the stylesheet is loaded from `fonts.googleapis.com`.
8. `<video src="https://example.com/video.mp4"></video>` - Not allowed, no `media-src` directive is specified.

## Question 16 - Refer Policy

- Which statements are true?
  - `rel="noopener"` is used to prevent the original page from accessing the window object of the new page. > False, `rel="noopener"` is used to prevent the new page from accessing the window object of the original page.
  - `rel="noreferrer"` can be used to prevent the newly opened page from accessing the window object of the original page. > True, `rel="noreferrer"` is used to prevent the newly opened page from accessing the `document.referrer` property, and it implicitly sets `rel="noopener"`.
  - `rel=noopener` and `rel=noreferrer` can only be used with HTTPS. > False, they can be used with HTTP as well.
  - `rel="noopener"` can be used to prevent tabnabbing. > True, `rel="noopener"` can be used to prevent tabnabbing.
  - The default Referer Policy is `no-referrer-when-downgrade`. > False, the default Referer Policy is `no-referrer`.

## Question 17 - Generators

- When does `In log: My input!` get logged?

```js
function* generatorFunc() {
  const result = yield 'My input!';
  console.log('In log:', result);
  return 'All done!';
}

const it = generatorFunc();

it.next(); // { value: "My input!", done: false }
it.next('My input!'); // In log: My output! { value: "All done!", done: true }
```

## Promise Methods

```js
const promises = [
  new Promise((res) => setTimeout(() => res(1), 200)),
  new Promise((res) => setTimeout(() => res(2), 100)),
  new Promise((_, rej) => setTimeout(() => rej(3), 100)),
  new Promise((res) => setTimeout(() => res(4), 300)),
];

Promise.all(promises)
  .then((res) => console.log('Result: ', res))
  .catch((err) => console.log('Error: ', err));
// Error: 3
Promise.allSettled(promises)
  .then((res) => console.log('Result: ', res))
  .catch((err) => console.log('Error: ', err));
// Result: [ { status: "fulfilled", value: 1 }, { status: "fulfilled", value: 2 }, { status: "rejected", reason: 3 }, { status: "fulfilled", value 4 } ]
Promise.any(promises)
  .then((res) => console.log('Result: ', res))
  .catch((err) => console.log('Error: ', err));
// Result: 2
Promise.race(promises)
  .then((res) => console.log('Result: ', res))
  .catch((err) => console.log('Error: ', err));
// Result: 2
```

## Question 19 - Back Forward Cache

> Back Forward Cache（BFCache）是一种浏览器优化技术，它可以使用户在点击浏览器的前进和后退按钮时，页面能够快速加载。当用户从一个页面导航到另一个页面时，浏览器会将原页面保存在内存中，而不是完全销毁它。然后，当用户点击后退按钮返回到原页面时，浏览器可以从内存中快速恢复该页面，而不需要重新加载和执行页面的所有资源和脚本。

- Which of the following values will always make your page ineligible for back-forward cache?
  - unload - True, the page is not eligible for back-forward cache if the `unload` event is triggered.
  - pagehide - False, the page is still eligible for back-forward cache.
  - onbeforeunload - False, the page is still eligible for back-forward cache.
  - pageshow - False, the page is still eligible for back-forward cache.

## Question 20 - Front End Security

- Connect the terms with their definitions

  - XSS - allows attackers to inject malicious scripts into web pages viewed by other users. (Cross-Site Scripting)
  - CSRF - tricks users into executing unwanted actions by exploiting their authenticated session. (Cross-Site Request Forgery)
  - UI Redressing - tricks users into interacting with disguised or hidden elements. (Clickjacking)
  - MITM - allows attackers to intercept and modify the communication between two parties without their knowledge. (Man-in-the-Middle)

- Preventing XSS:
  - Encode Data: Always encode user data before displaying it on your website. This ensures that any scripts that are included in the user data are displayed as plain text and not executed.
  - Validate Input: Validate user input on the server-side and client-side. Only allow necessary HTML tags if your application requires users to input HTML.
  - Use HTTPOnly Cookies: Set the HTTPOnly flag for cookies to prevent them from being accessed through client-side scripts.
  - Content Security Policy (CSP): Implement a CSP to limit the sources of scripts and other resources.
- Preventing CSRF:
  - Use Anti-CSRF Tokens: Include a unique, random value in each form and validate this token on the server-side when the form is submitted.
  - SameSite Cookies: Use the SameSite attribute for cookies to prevent the browser from sending the cookie along with cross-site requests.
  - Check Referer Headers: Check the HTTP referer header on the server-side to ensure that the request is coming from your website.

## Question 21 - Font Strategies

- Connect the font strategies to their definition
  - `font-display: block` - temporarily renders an invisible font until the custom font has been downloaded.
  - `font-display: swap` - use the custom font if it is available, use a fallback font if the custom font is not available.
  - `font-display: fallback` - uses the fallback font until the custom font has been downloaded, switch to the custom font when available.
  - `font-display: optional` - only use the custom font if it is available, otherwise use a fallback font.
  - `font-display: auto` - allows browser to determine the most appropriate behavior for font loading.

## Question 22 - Cookie Policy Header

- `Set-Cookie: my-cookie="value"; Domain="website.com"; Secure; HttpOnly;`
  - This cookie is accessible from `www.website.com` but not from `sub.website.com`. > False, the cookie is accessible from `website.com`, including all subdomains.
  - This cookie can only be set client-side on the `website.com` domain. > False, the `HttpOnly` flag prevents the cookie from being accessed by client-side scripts.
  - This cookie gets treated like a session cookie. > True, the cookie is not set with an expiration date, so it will be treated as a session cookie.
  - THis cookie will be sent when navigating from another website to `www.website.com`. > True, there is no `SameSite` attribute set, so the cookie will be sent with cross-site requests.

## Question 23 - CSS Pseudo Selector

- Select the first lite item `<li>one</li>`

```html
<div>
  <ul>
    <li>one</li>
    <ul>
      <li>two</li>
      <li>three</li>
    </ul>
  </ul>
  <ul>
    <li>four</li>
    <li>five</li>
    <li>six</li>
  </ul>
</div>
```

```css
ul:first-child > li {
}
ul:first-child > li:first-child {
}
```

## Question 24 - Transport Security

- `Strict-Transport-Security: max-age=31536000; includeSubDomains;`
  - The header enforces HTTPS for one year on the domain and all subdomains. > True, the `includeSubDomains` directive enforces HTTPS on the domain and all subdomains.
  - When `max-age` expires, browsers will automatically switch to HTTP. > False, the browser will continue to enforce HTTPS after the `max-age` expires.
  - The `max-age` is refreshed every time the browser reads the header. > True, the `max-age` will be refreshed every time the browser reads the header.
  - Insecure requests to subdomains are allowed. > False, the `includeSubDomains` directive enforces HTTPS on the domain and all subdomains.

## Question 25 - Render Layer

- Which of the following properties causes the element to be promoted to its own Render Layer?
  - `z-index: 1` - False, the `z-index` property affects the stacking order of elements, but it doesn't promote the element to its own render layer.
  - `translate3d(0, 0, 0)` - True, the `translate3d` property triggers hardware acceleration and promotes the element to its own render layer.
  - `will-change: transform` - True, the `will-change` property hints to the browser that the element will change, and it should be promoted to its own render layer.
  - `transform: rotateY(45deg)` - True, the 3D transforms promote the element to its own render layer.
  - `position: fixed` - True, the `position: fixed` property promotes the element to its own render layer.
  - `position: absolute` - False, the `position: absolute` property doesn't promote the element to its own render layer.

## Question 26 - Image Formats

- Match the image formats to the descriptions
  - JPEG - Lossy compression, supports progressive rendering.
  - PNG - Lossless compression, high quality, supports transparency, large file size.
  - WebP - Both lossy and lossless compression, supports transparency, supports progressive rendering.
  - AVIF - Both lossy and lossless compression, supports HDR and WCG, supports transparency.

## Question 27 - CORS Header

- What is true about the following CORS config?

```
Access-Control-Allow-Origin: https://www.website.com
Access-Control-Allow-Headers: Content-Type
Access-Control-Allow-Methods: *
Access-Control-Expose-Headers: X-Custom-Header
Access-Control-Max-Age: 600
```

1. A preflight request is required. > False, if the request method is `GET`, `HEAD`, or `POST`, and the request headers are simple headers, then the preflight request is not required. Also, when the max-age is set, the preflight request is not required until the max-age expires.
2. Only requests from `https://www.website.com` are allowed. > True, the `Access-Control-Allow-Origin` header is set to `https://www.website.com`.
3. Requests with cookies are allowed. > False, the `Access-Control-Allow-Credentials` header is not set.
4. The actual response is cached for 600ms. > False, the `Access-Control-Max-Age` header is set to 600 seconds.
5. `X-Custom-Header` will be the only included response header. > False, besides `X-Custom-Header`, other headers can be included in the response like `Content-Type`.
6. `GET`, `POST`, `PUT`, `PATCH` are allowed, but not `DELETE`. > False, the `Access-Control-Allow-Methods` header is set to `*`, so all methods are allowed.

## Question 28 - Event Loop

- What gets logged?

```js
setTimeout(() => console.log('1'), 0);

(async () => {
  console.log('2');
  await Promise.resolve();
  console.log('3');
})();

Promise.resolve().then(() => {
  Promise.resolve().then(() => console.log('4'));
});
```

The order of execution is:

1. 2
2. 3
3. 4
4. 1

## Question 29 - HTTP 1/2/3

- What statements are correct?
  - HTTP 2 allows multiple requests and responses concurrently over a single connection. > True, HTTP 2 uses multiplexing to allow multiple requests and responses concurrently over a single connection.
  - HTTP 3 can only be used with HTTPS. > True, HTTP 3 is built on top of QUIC, which requires a secure connection.
  - HTTP 2 is backward compatible with HTTP 1.1. > True, HTTP 2 is backward compatible with HTTP 1.1.
  - HTTP 1.1 requires multiple TCP connections to process multiple requests simultaneously. > True, HTTP 1.1 requires multiple TCP connections to process multiple requests simultaneously.

## Question 30 - Invoking Object Methods Scope

- What gets logged?

```js
const objA = {
  type: 'A',
  foo() {
    console.log(this.type);
  },
};

const objB = {
  type: 'B',
  foo: objA.foo,
  bar: () => objA.foo(),
  baz() {
    objA.foo();
  },
};

objB.foo(); // B
objB.bar(); // A
objB.baz(); // A
```
