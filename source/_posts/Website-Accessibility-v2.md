---
title: Website Accessibility v2
date: 2023-07-09 15:04:47
categories: 学习笔记
tags: [FrontEndMasters, a11y]
---

## Introduction

- [Course website](https://learn-a11y.netlify.app/)
- [Course repo](https://github.com/jkup/learn-a11y)

### What is Accessibility?

> Web accessibility means that people with disabilities can use the web. > 网页无障碍意味着残疾人可以使用网页。

More specially, web accessibility means that people with disabilities can **perceive**, **understand**, **navigate**, and **interact** with the web, and that they can contribute to the web. > 更具体地说，网页无障碍意味着残疾人可以感知、理解、导航和与网页交互，并且他们可以为网页做出贡献。

<!-- more -->

- Types of disabilities > 残疾类型

  - Mobility and physical > 肢体和身体
  - Cognitive and neurological > 认知和神经
  - visual > 视觉
  - hearing > 听觉

- Reasons developers should learn accessibility > 开发者应该学习无障碍的原因
  - It's fun! > 有趣
  - We're the ones making it inaccessible > 我们是使其无法访问的人
  - Human rights > 人权
  - Legal issues > 法律问题
  - Reach a larger audience > 覆盖更广泛的受众
  - Impactful > 有影响力的
  - Makes you a specialist > 使你成为专家

### Ways People Use the Web

- Keyboard Only > 只用键盘
- Head Wand > 头部杖
- Mouth Stick > 嘴棍
- Single Switch > 单开关
- Screen Reader > 屏幕阅读器

### Curb Cut Effect

> The Curb-Cut Effect, in its essence, asserts that an investment in one group can cascade out and up and be a substantial investment in the broader well-being of a nation -- one whose policies and practices create an equitable economy, a healthy community of opportunity, and just society. > 路边切割效应本质上断言，对一个群体的投资可以向外扩散并向上扩散，并且是对一个国家整体福祉的重大投资——其政策和做法创造了一个公平的经济、一个充满机会的健康社区和一个公正的社会。

### Accessibility Standards

- Web Content Accessibility Guidelines (WCAG) > 网页内容无障碍指南
  - [WCAG](https://www.w3.org/WAI/standards-guidelines/wcag/) specifies three different [conformance levels](https://www.w3.org/WAI/WCAG21/Understanding/conformance#levels) they are:
    - A (lowest)
    - AA (mid range)
    - AAA (highest)
- WebAIM
  - [WebAIM](https://webaim.org/) provides a handy [checklist](https://webaim.org/standards/wcag/checklist) with their recommendations. > WebAIM 提供了一个方便的清单，其中包含他们的建议。

## Screen Readers

- How they work

  > Screen readers convert digital text into synthesized speech. They empower users to hear content and navigate with the keyboard. The technology helps people who are blind or who have low vision to use information technology with the same level of independence and privacy as anyone else. > 屏幕阅读器将数字文本转换为合成语音。它们使用户能够听到内容并使用键盘进行导航。该技术帮助盲人或视力低下的人使用信息技术，与其他任何人一样独立和保护隐私。

- Popular screen readers
  - JAWS
  - NVDA
  - VoiceOver
  - ZoomText
  - System Access or SA To Go
  - Window-Eyes
  - ChromeVox
  - Narrator
  - Other

### Alternative Text and Captions

#### Alternative Text

By default, when a screen reader encounters an image, if it can't find `alt` text it will read the image's file name. > 默认情况下，当屏幕阅读器遇到图像时，如果找不到 `alt` 文本，它将读取图像的文件名。

```html
<img src="logo.png" alt="logo" />
```

#### Skipping over images

Sometimes your website will have images that are strictly for decorative purposes. In these cases, you can add `alt=""` to the image to tell the screen reader to skip over it. > 有时，您的网站将具有严格用于装饰目的的图像。在这些情况下，您可以向图像添加 `alt=""`，以告诉屏幕阅读器跳过它。

```html
<img src="logo.png" alt="" />
```

#### Captions for audio

Remember not all content on the web is visual! If your application has video content, be sure to use a captioning service to add closed captioning. > 记住，网页上的所有内容不全是可视的！如果您的应用程序具有视频内容，请务必使用字幕服务添加闭路字幕。

### Exercise

- [Screen reader exercise](https://learn-a11y.netlify.app/exercises/1.html)

## Accessible HTML

### Semantic HTML

- Some elements have semantic meaning but no special functionality. > 一些元素具有语义意义，但没有特殊功能。
  - `<main>`
  - `<article>`
  - `<footer>`
  - `<header>`
  - ...
- Other's provide a lot of builtin functionality. > 其他的提供了很多内置功能。
  - `<button>`
  - `<input>`
  - `<select>`
  - `<textarea>`
  - ...

### Accessible Form Field Labels

Form fields can be confusing for screen reader users. There are many ways to label form fields so the label is read out loud whenever the field has focus. > 对于屏幕阅读器用户来说，表单字段可能会令人困惑。有许多方法可以标记表单字段，以便在字段具有焦点时将标签读出来。

- Visual only labels > 仅视觉标签
  - A common pattern is using div's or paragraph tags to label form fields. > 一种常见的模式是使用 div 或段落标签来标记表单字段。

```html
<form>
  <p>First Name</p>
  <input type="text" />
  <p>Last Name</p>
  <input type="text" />
  <input type="submit" value="Submit" />
</form>
```

- A better option is to use the HTML `label` tag which will allow screen readers to recite the label when the field is focused. > 更好的选择是使用 HTML `label` 标签，这将允许屏幕阅读器在字段聚焦时朗读标签。

```html
<form>
  <label for="first-name">First Name</label>
  <input id="first-name" type="text" />
  <label for="last-name">Last Name</label>
  <input id="last-name" type="text" />
  <input type="submit" value="Submit" />
</form>
```

- implicit label

```html
<form>
  <label>
    First Name
    <input type="text" />
  </label>
  <label>
    Last Name
    <input type="text" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

- The label tag can only works with 'labelable' elements. > label 标签只能与 'labelable' 元素一起使用。

  - `<button>`
  - `<input>`
  - `<keygen>`
  - `<meter>`
  - `<output>`
  - `<progress>`
  - `<select>`
  - `<textarea>`

- If you ever need to label an element not on that list, use `aria-label` instead. > 如果您需要标记不在该列表中的元素，请改用 `aria-label`。

```html
<div aria-label="Interactive div">Hello</div>
```

#### Screen reader only content

```css
.sr-only {
  position: absolute;
  left: -10000px;
  width: 1px;
  height: 1px;
  top: auto;
  overflow: hidden;
}
```

### Buttons

- Original button

```html
<button onclick="alert('Hello')">Click Me</button>
```

- Rewrite button with a div

```html
<div onclick="alert('Hello')">Click Me</div>
```

- Add `role="button"` to the div

```html
<div role="button" onclick="alert('Hello')">Click Me</div>
```

- Add `tabindex="0"` to the div to make it focusable with the keyboard

```html
<div role="button" tabindex="0" onclick="alert('Hello')">Click Me</div>
```

- Add keyboard support

```html
<div
  role="button"
  tabindex="0"
  onclick="alert('Hello')"
  onkeyup="if (event.keyCode === 13) alert('Hello')"
>
  Click Me
</div>
```

- Add `aria-label` for screen reader users

```html
<div
  role="button"
  tabindex="0"
  aria-label="Alert Hello"
  onclick="alert('Hello')"
  onkeyup="if (event.keyCode === 13) alert('Hello')"
>
  Click Me
</div>
```

### Exercise

[Accessible HTML exercise](https://learn-a11y.netlify.app/exercises/2.html)

## Aria

[MDN ARIA](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA)
[Official ARIA](https://www.w3.org/TR/wai-aria-1.1/)
[Aria table](https://www.w3.org/TR/html-aria/#aria-table)

### Labels

- `aria-label` > 用于提供元素的可读标签。
- `aria-labelledby` > 用于提供元素的可读标签的 ID。
- `aria-describedby` > 用于提供元素的描述性文本的 ID。

What is the difference between `aria-labelledby` and `aria-describedby`? > `aria-labelledby` 和 `aria-describedby` 之间有什么区别？

- `aria-labelledby` is used to label an element. > `aria-labelledby` 用于标记元素。
- `aria-describedby` is used to describe an element. > `aria-describedby` 用于描述元素。

```html
<div id="label">Submit</div>
<div id="description">Click to submit the form</div>
<button aria-labelledby="label" aria-describedby="description">Click Me</button>
```

### Roles, States, and Properties

Aria also provides roles which can be applied to any element, example:

- button
- checkbox
- tree
- banner
- aria-autocomplete
- aria-haspopup
- [see here] for full list(https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques)

- Using `aria` attributes with css

```css
.dropdown[aria-expanded='true'] {
  display: block;
}
```

### Live Regions

- `aria-live` > 用于标记元素的内容是否会动态更改。
  - `aria-live="off"` > 默认值，不会通知屏幕阅读器。
  - `aria-live="polite"` > 会通知屏幕阅读器，但不会打断用户的操作。
  - `aria-live="assertive"` > 会通知屏幕阅读器，并打断用户的操作。

### Exercise

[Accessible Aria exercise](https://learn-a11y.netlify.app/exercises/3.html)

## Focus Management

As users navigate around using only the keyboard, focus rings provide a necessary clue as to the currently active item. > 当用户仅使用键盘导航时，焦点环提供了有关当前活动项的必要线索。

```css
:focus {
  outline: 2px solid blue;
}
```

### Keyboard shortcuts

Keyboard shortcuts are another curb cut example. Sites like Twitter and Facebook offer keyboard shortcuts for almost any action which are great for both keyboard only users and and power users! > 键盘快捷键是另一个 curb cut 示例。Twitter 和 Facebook 等网站为几乎所有操作提供了键盘快捷键，这对于仅使用键盘的用户和高级用户都非常有用！

### Skip links

Skip links are a way to allow keyboard users to skip over large sections of content. They are hidden by default and only show when focused.

```html
<a href="#main" class="sr-only">Skip to main content</a>
```

```css
.sr-only {
  position: absolute;
  left: -10000px;
  width: 1px;
  height: 1px;
  top: auto;
  overflow: hidden;
}

.sr-only:focus {
  position: absolute;
  left: 0;
  top: 0;
  width: auto;
  height: auto;
}
```

- How to make a skip link
  - Create a link with a `href` attribute that points to the main content. > 创建一个链接，其中 `href` 属性指向主内容。
  - Prepend it to the body of the page. > 将其预置到页面的 body 中。
  - Make it invisible by default. > 默认情况下使其不可见。
  - Make it visible when focused. > 聚焦时可见。

### Tab Navigation

You can use <key>Tab</key> to navigate to the next tabbable element and <key>Shift</key> + <key>Tab</key> to navigate to the previous tabbable element. > 您可以使用 <key>Tab</key> 导航到下一个可制表元素，使用 <key>Shift</key> + <key>Tab</key> 导航到上一个可制表元素。

- Tabbable elements

  - `<a>`
  - `<button>`
  - `<input>`
  - `<select>`
  - `<textarea>`
  - `[iframe]`

- Making an element tabbable

  - Add `tabindex="0"` to the element. > 将 `tabindex="0"` 添加到元素中。

- Tabindex values

  - A negative means that the element should be focusable, but should not be reachable via sequential keyboard navigation. > 负数表示元素应该是可聚焦的，但不应该通过顺序键盘导航可达。
  - `0` means that the element should be focusable and reachable via sequential keyboard navigation, but its relative order is defined by the platform convention. > `0` 表示元素应该是可聚焦的，并且可以通过顺序键盘导航可达，但其相对顺序由平台约定定义。
  - A positive value means that the element should be focusable and reachable via sequential keyboard navigation, with its order defined by the value of the number. > 正值表示元素应该是可聚焦的，并且可以通过顺序键盘导航可达，其顺序由数字的值定义。

- Active Element

```js
// Assuming there is modal is about to be opened
// Store the current news item
const currentNewsItem = document.activeElement;
// Open the modal
// On modal close, refocus the news item
currentNewsItem.focus();
```

- Tap trapping > 点击陷阱

Another useful concept is tab trapping. Consider opening a modal on a page which contains a form. A keyboard only user will want to tab around the form but unless we help, tabbing while focused on the last form element will send us all the way back to the main document. > 另一个有用的概念是 tab 陷阱。想像假如在页面上打开一个包含表单的模态框。仅使用键盘的用户将想要尽在表单之中使用 tab 键导航，但是除非我们帮助，否则在最后一个表单元素上聚焦时，按 tab 键将会将我们带回到主文档。（注：使用 js 进行判断，当用 tab 导航到第一个/最后一个元素时，再次按下 tab 键时，将焦点聚焦到模态框内的第一个/最后一个元素上）。

```js
const focusableElements = modal.querySelectorAll(
  'a[href], button, textarea, input[type="text"], input[type="radio"], input[type="checkbox"], select, [tabindex]:not([tabindex="-1"])'
);

const firstFocusableElement = focusableElements[0];

const lastFocusableElement = focusableElements[focusableElements.length - 1];

document.addEventListener('keydown', function (e) {
  const isTabPressed = e.key === 'Tab' || e.keyCode === 9;

  if (!isTabPressed) {
    return;
  }

  if (e.shiftKey) {
    /* shift + tab */
    if (document.activeElement === firstFocusableElement) {
      lastFocusableElement.focus();
      e.preventDefault();
    }
  } else {
    /* tab */
    if (document.activeElement === lastFocusableElement) {
      firstFocusableElement.focus();
      e.preventDefault();
    }
  }
});
```

### Exercise

[Accessible Focus Management exercise](https://learn-a11y.netlify.app/exercises/4.html)

## Visual Considerations

### Color Contrast

- WebAIM
  - [Color Contrast Checker](https://webaim.org/resources/contrastchecker/)
- Chrome DevTools

  - [Color](https://developer.chrome.com/docs/devtools/css/color/)

- Colors and forms
  - An important consideration for colorblind users is making sure that color isn't the only way users can tell if there is an error with the form. For example, a red ring is not enough. Consider adding an icon or an error label. > 对色盲用户来说，一个重要的考虑因素是确保颜色不是用户能够判断表单是否有错误的唯一方式。例如，红色的圆圈是不够的。考虑添加一个图标或错误标签。

### Visual Impairments > 视觉障碍

To simulate visual impairments, you can use the Firefox add-on [NoCoffee](https://addons.mozilla.org/en-US/firefox/addon/nocoffee/). > 要模拟视觉障碍，您可以使用 Firefox 插件 [NoCoffee](https://addons.mozilla.org/en-US/firefox/addon/nocoffee/)。

### Setting the language

Remember to set the lang attribute on the top level html tag as well as any sections where the language deviates from it. > 请记住，在顶级 html 标签以及任何语言与之不同的部分上设置 lang 属性。

```html
<html lang="en">
  <!-- ... -->
  <blockquote lang="fr">
    <!-- ... -->
  </blockquote>
</html>
```

### Fixing Markup Errors

- [W3C Markup Validation Service](https://validator.w3.org/)

### Neurocognitive > 神经认知

Prefers Reduced Motion

Users can set a "prefers reduced motion" setting in their operating system (Windows, Mac, Linux) and we can read that setting in CSS and swap out animations with more subtle effects. > 用户可以在其操作系统（Windows、Mac、Linux）中设置“偏好减少动作”，我们可以在 CSS 中读取该设置，并用更细微的效果替换动画。

This is important both as a preference and also to avoid causing issues for users who may suffer from seizures. > 这对于偏好和避免给癫痫患者造成问题都很重要。

- [prefers-reduced-motion](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion)

```css
.animation {
  animation: pulse 1s linear infinite both;
}

@media (prefers-reduced-motion: reduce) {
  .animation {
    animation: none;
  }
}
```

Prefers Color Scheme

Users can set a "prefers color scheme" setting in their operating system (Windows, Mac, Linux) and we can read that setting in CSS and swap out colors to match. > 用户可以在其操作系统（Windows、Mac、Linux）中设置“偏好颜色方案”，我们可以在 CSS 中读取该设置，并交换颜色以匹配。

- [prefers-color-scheme](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-color-scheme)

```css
body {
  background-color: white;
  color: black;
}

@media (prefers-color-scheme: dark) {
  body {
    background-color: black;
    color: white;
  }
}
```

### Exercise

[Exercise 5](https://learn-a11y.netlify.app/exercises/5.html)

## Tooling

### Accessibility Linters & Dev Tools

- Linters
  - [eslint-plugin-jsx-a11y](https://www.npmjs.com/package/eslint-plugin-jsx-a11y)
  - [eslint-plugin-vuejs-accessibility](https://www.npmjs.com/package/eslint-plugin-vuejs-accessibility)
- Accessible Design System
  - [Adobe React Spectrum](https://react-spectrum.adobe.com/react-spectrum/index.html)
  - [Google Material Design](https://material.io/)
- Accessibility Dev Tools
  - [axe DevTools](https://www.deque.com/axe/devtools/)
  - [Google Lighthouse](https://developers.google.com/web/tools/lighthouse)

### Additional Resources

- [Web Content Accessibility Guidelines (WCAG) Overview](https://www.w3.org/WAI/standards-guidelines/wcag/)
- [WebAIM](https://webaim.org/)
- [Microsoft Inclusive Design](https://inclusive.microsoft.design/)
- [Global Accessibility Awareness Day](https://accessibility.day/)
- [Accessibility in JavaScript Applications](https://frontendmasters.com/courses/javascript-accessibility/)
- [Start Building Accessible Web Applications Today](https://egghead.io/courses/start-building-accessible-web-applications-today)
- [Accessibility Tips and Tricks](https://egghead.io/courses/accessibility-tips-tricks-49286904)
- [Google Accessibility Audits](https://developer.chrome.com/docs/lighthouse/accessibility/)
