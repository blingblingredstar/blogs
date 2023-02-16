---
title: CSS In Depth v2
date: 2019-05-23 10:07:25
categories: 学习笔记
tags: [FrontEndMasters, CSS]
---

## Introduction

[CSS Mastery Workshop](https://estelle.github.io/)

### CSS Introduction

CSS(Cascading Style Sheets层叠样式表)

#### Separation of Concerns

HTML代表了网页结构，CSS代表了网页的表现形式，JS代表了网页的行为，这是对HTML，css以及JS在最初对其范围的划分。

> CSS tells the browser how HTML content is to be presented to the user

现在许多人在js中编写动画，作者认为这偏离了最初对于js适用范围的界定，因为动画中并没有数据交互，只是动态的网页展示部分，应该归属于CSS的范畴。

在HTML的头部中添加最小化的CSS样式可以提高网页首次加载的速度（网页只有在css加载完后才会显示出来），所以将css放在html中可以减少http请求数量，可以提升首次加载速度，但如果每次加载网页都要重新加载头部信息中的css，那么就会降低性能表现。

所以可以最小化一些样式放在首页，然后加载一些外联样式保存在缓存中来提升多次加载的性能表现。

<!--more-->

#### 为什么使用外联样式表 Why Use External Stylesheets

+ 可以重用 Allows reusability
+ 简化了可维护性 Eases maintainability
+ 全页面的变化 Changes are sitewide
+ 即时的变化 Changes are instantaneous
+ 可互换的表示层 Interchangeable presentation layer

#### Tools

[FontSquirrel](https://www.fontsquirrel.com/)
[CodePen](https://codepen.io/)

## Selectors

### Basic Selectors & CSS Levels

[Slides](https://estelle.github.io/cssmastery/selectors/index.html#slide1)

#### Basic Selectors

1. ID选择器(#idName)
2. class选择器(.className)
3. 标签选择器(tagName)

在选择元素时，尽量使用最小的特殊性(specificity)来选择元素。首先使用最小特殊性的选择器选择（如使用标签选择器指定所有li的字号）再使用class或者id选择器指定特定元素样式（权利最小化原则？）。

### Specificity Introduction

Specificity: How it works

+ 1-0-0:id选择器
+ 0-1-0:class选择器(也包括属性选择器和伪类选择器)
+ 0-0-1:标签选择器
+ 0-0-0: * {} 通配符和{}没有值

The * selector, or global selector, has no value.

Combinators, like ~, >, and + have no value

### Relational Selectors & Combinators

+ 后代选择器(descendant selector) `ul li`
+ 子类选择器(child selector) `ul > li`
+ 相邻兄弟选择(adjacent sibling) `li.class-name + li`

### Attribute Selectors

> `element[attribute]` Select elements containing the named attribute

```css
img[alt] {}
```

```html
     <img src="image.jpg" alt="accessible">
     <!-- 下面的img标签没有选择，因为没有alt属性 -->
     <img src="image.jpg" title="inaccessible">  
```

#### CSS 2 attribute selectors

`E[attr]` Element E that has the attribute attr with any value.
> 选择元素E，其属性attr具有任意值。

`E[attr="val"]` Element E that has the attribute attr with the exact, case-sensitive if attribute is case sensitive, value val.
> 选择元素E具有attr属性，如果属性是区分大小写的，则该元素的值为val。

`E[attr|=val]` Element E whose attribute attr has a value val or begins with val- ("val" plus "-").
> 选择元素E，其属性attr具有val值或以val-开头(“val”加上“-”)。

```css
p[lang|="en"] {
  /* <p lang="en-us">  <p lang="en-uk"> */
}
```

#### Attribute Selectors in CSS Selectors Level 3

`E[attr^=val]` Element E whose attribute attr starts with the value val.
> 选择元素E，其属性attr以val值开始。

```css
a[href^=mailto] {
  background-image: url(emailicon.gif);
}

a[href^=http]:after {
  content: " (" attr(href) ")";
}
```

`E[attr$=val]` Element E whose attribute attr ends in val .
> 选择元素E，其属性attr以val结尾。

```css
a[href$=pdf] {
  background-image: url(pdficon.gif);
}

a[href$=pdf]:after {
  content: " (PDF)";
}
```

`E[attr*=val]` Element E whose attribute attr matches val anywhere within the attribute. Similar to E[attr~=val] above, except the val can be part of a word.
> 选择元素E，其属性attr与属性内任何位置的val匹配。与上面的E[attr~=val]类似，只是val可以是单词的一部分。

Note: Multiple attributes work! `a[title][href]`

#### Attribute Selectors 4: Case Insensitivity

```css
E[foo="bar" i]
input[type=checkbox i]
```

Only relevant if attribute value is case senstive (as it is for all attributes in XHTML)
> 只有当属性值区分大小写时才相关(XHTML中的所有属性都是如此)

### User Interface Selectors

Based on current state of UI

```css
:enabled

:disabled

:checked

:indeterminate (Level 4)
```

Form related UI pseudo-classes

```css
:default
:valid
:invalid

:required
:optional

:in-range
:out-of-range

:read-only
:read-write

:placeholder-shown

:user-error
:user-invalid
```

### Structural Selectors

```css
/* html根元素 */
:root
/* 空元素 */
:empty
:blank
/* 剩余选择器根据其与其他元素在dom(document object modal)文档模型的关系进行选择 */
:nth-child()

:nth-last-child()

:first-child*

:last-child

:only-child

:nth-of-type()

:nth-last-of-type()

:first-of-type

:last-of-type

:only-of-type
```

First, last, & only

```css
:first-child

:last-child

:first-of-type

:last-of-type

:only-child

:only-of-type
```

### nth-of-type Structural Selectors

nth pseudo-classes

```css
:nth-child(3n)

:nth-last-child(odd)

:nth-of-type(5)

:nth-last-of-type(3n+1)
```

Target element or elements based on argument passed to the selector

```css
:nth-of-type(even)
:nth-of-type(odd)
:nth-of-type(an+b)
```

### root, empty & blank

```css
:root
```

Selects the document root, which is `<html>`

+ Declare font-size on :root if using rem units
+ Style :root only if showing `<head>` (as in our exercise files)
+ In CSS4, define Defining Variables on root. (see [Variables module](https://drafts.csswg.org/css-variables/))

:empty & :blank pseudo-classes

```css
E:empty
```

```xml
<E/>
<E></E>
<E><!-- this is a comment --></E>
<E title="this is an empty element"/>
```

### Negation, Matching & Parent

:not - Negation pseudo-class

```css
E:not(s1)

div:not(.excludeMe)

/* Supported everywhere since IE9 */
```

:not(s1, s2)

```css
E:not(s1, s2)

div:not(.excludeMe, .excuseYou)

/* Safari Only */
```

Parent Selector

```css
:has

/* Contains a header */
header:has(h1, h2, h3, h4, h5, h6)

/* Contains no headers */
header:not(:has(h1, h2, h3, h4, h5, h6))

/* Contains something that is not a header */
header:has(:not(h1, h2, h3, h4, h5, h6))
```

### Linguistic Pseudo Classes

```css
/* CSS 2.1 */
html[lang|="en"]
p:lang(en)

/* CSS Selectors Level 4 */
:lang(*-ch)
:dir(ltr|rtl)
```

### Link, Location & User Actions

#### Link Pseudo Classes

a with an href attribute

```css
:link
:visited

/* CSS Selectors Level 4 */
:any-link
/* :any-link is the same as :matches(:link, :visited) */
```

#### User Action Pseudo Classes

```css
:hover
:active
:focus
/* Note: always style :focus when you style :hover */

:focus-ring
:focus-within

:drop
:drop()
```

#### :hover, :active, :focus

```css
a:visited:hover
button:active:focus

/* Never, ever, ever do.... */
*:focus { outline: none; }
```

#### Drag and Drop Pseudo Classes

+ :drop
  > drop targets while the user is “dragging”.
  > Unfortunately, dropzone attribute is not yet supported
+ :drop(active)
  > current drop target for the drag operation.
+ :drop(valid
  > drop target is valid for the object currently being dragged, like correct filetype.
+ :drop(invalid)
  > drop target is invalid for the object currently being dragged, i.e. doesn't except the filetype of object being dragged.
+ :drop(valid active)
  > matches active drop target if it’s valid

#### Other Pseudo Classes

```css
:target

div:target::first-line {
  font-weight: bold;
}

:scope
/* Matches elements that are a reference point for selectors to match against. */

/* Selects a scoped element */
:scope {
  background-color: blue;
}
/* In CSS, :scope is the :root, since we don't have scoped CSS yet. */
/* In JS, :scope matches the element returned by querySelector(), querySelectorAll(), matches(), or el.closest() */

/* Column combinator */
col.selected || td {
  /* matches all cells within the column's scope*/
}
:nth-column(An+B)
:nth-last-column(An+B)

/* Time dimensional */
:current
:future
:past

/* Video & Audio */
:playing
:paused
```

### Specificity

Specificity: How it works

+ 0-0-0: Global selector
+ 1-0-0: ID selector
+ 0-1-0: Class selector (Also attribute selector & pseudoclass)
+ 0-0-1: Element Selector

## Pseudo-Elements

### Introduction to Pseudo-Elements

Pseudo elements

```css
::first-line
::first-letter
::before
::after

/* (not in specification) */
::selection
```

Pseudo-classes select elements that already exist.
Pseudo-elements create "faux" elements you can style.

### Generated Content with ::before and ::after

```css
p:before {
  content: "before content - ";
  font-weight: bold;
}
p:after{
  content: " - after content";
  font-weight: bold;
}
```

```xml
<p>
    <before>before content - </before>
        the content
    <after> - after content</after>
</p>
```

## Generated Content

### Before and After

[CSS: Generated Content](https://estelle.github.io/cssmastery/generated/index.html#slide1)

::before and ::after

```css
p:before {
  content: "before content - ";
  font-weight: bold;
}
p:after{
  content: " - after content";
  font-weight: bold;
}
```

```xml
<p>
    <before>before content - </before>
        the content
    <after> - after content</after>
</p>
```

You get 2 free stylable faux elements for every non-empty element

```css
element::before {
  /* the only 'required' attribute */
  content: "";
 }

element::after {
  /* without "content:", you have no content */
  content: "";
 }
```

### Counters

```css
body {
  counter-reset: sections;
}

header h1.sectiontitle:before{
  content: "part" counter(sections) ": ";
  counter-increment: sections;
}
```

```html
<p data-count="5">Hi</p>

width: attr(data-count em, auto);
```

### Quotes & Attributes

```css
/* Specify pairs of quotes for two levels in two languages */
:lang(en) > q { quotes: '"' '"' "'" "'" }
:lang(fr) > q { quotes: "«" "»" "’" "’" }

/* Insert quotes before and after Q element content */
q::before { content: open-quote }
q::after  { content: close-quote }
```

### Images

```css
.showMe {
  position: relative;
}
.showMe:hover::after {
  position: absolute;
  content: url(attr(data-url)); /* doesn't work */
  /* Content is not parsed, so url(attr()) does not work. */
  content: url(estelle.svg); /* does work */
  width: 200px;
  height:200px; color: blue;
  bottom: -39px;
  left: 20px;
}
```

### Strings & Specail Characters

```css
 element:before {
    content: '';                  /* empty */
    content: " (.pdf) ";          /* any text */
    content: "\2603";             /* unicode */
    content: " (" attr(href) ")"; /* attributes */
    content: counter(name);
    counter-increment: name;  /* counters */
 }
```

### Icon Accessibility

```css
[class|='material-icons']:after {
  content: "\e84e";
  content: "accessibility";
  color: red;
}

.material-icons {
  font-size: 3rem;
}
```

### Design Element

```css
.thought,.thought:before,
.thought:after  {
  border-radius: 50%;
  border: 5px solid blue;
  background-color: white;
  position:relative;
}
.thought:before,
.thought:after {
  content: '';
  position:absolute;
  left: 20%; bottom: -30px;
  height: 40px; width: 40px;
}
.thought:after {
  left: 12%; bottom: -50px;
  height: 20px; width: 20px;
}
```

## Media Query

### Media Type, Screen Size, Resolution

```css
/* 跨度小于600px时应用一下样式 */
@media screen and (max-width: 600px) {
  #presentation {
    background: red;
  }
}
/* 高度大于宽度时应用如下样式 */
@media screen and (orientation: portrait) {
  #presentation {
    background: yellow;
  }
}
/* 宽度大于高度时应用如下样式 */
@media screen and (orientation: landscape) {
  a[href^="mailto:"]:before {
    content: url(icons/email.gif);
  }
}
```

```html
<link rel='stylesheet'
media='screen and (min-width: 320px) and (max-width: 480px)'
href='css/smartphone.css' />
```

```css
@media screen and (max-width: 480px){
  selector { /* small screen */
    property: value;
  }
}

@media screen and (orientation: landscape) {
  selector { /* landscape properties */
    property: value;
  }
}

@media screen and (min-device-pixel-ratio: 1.5) {
  selector { /* properties for higher resolution screens */
    property: value;
  }
}
```

### Syntax & Punctuation

```css
/* "only" leaves out older browsers */
media="only print and (color)"

/* "and" - both parts must be true */
media="only screen and (orientation: portrait)"

/* "not" - if untrue */
media="not screen and (color)"

/* Comma separates selectors - any part can be true */
media="print, screen and (min-width: 480px)"
```

### Browser Capability, @supports

```css
@supports (display: flex){
  /* rules for browsers supporting unprefixed flex box */
}

/* Don't use */
@media screen and (transform-3d) {
  .transforms {}
}
@media screen and (min-width: 320px) and (max-width: 500px){
  @-ms-viewport { width: device-width;}
}
```

### Viewport

```html
<meta
name="viewport"
content="width=device-width,initial-scale=1, maximum-scale=1"/>
```

### Use Cases: Hyphenations

```css
@media screen and (min-width: 38em){
  #content { padding: 0 21%; }
}
 p {
  hyphens: auto;
}
```

### Use Cases: Columns

[Columns](https://estelle.github.io/cssmastery/media/#slide25)

```css
p {
  column-count: 9;
  column-width: 10em;
  column-rule: 1px solid #ccc;
  column-gap: 2em;
}
```

### Use Cases: SVG

[Media Queries in SVG](https://estelle.github.io/cssmastery/media/#slide27)

```css
:root {
  background-image: url(circle.svg);
  background-repeat: no-repeat;
  background-position: bottom center;
  background-size: 70%;
}
```

```xml
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="400" height="400">
 <g>
  <title>Simple SVG + mediaqueries
   <defs>
    <style>
      @media screen and (max-width: 100px) {
         #circle { fill: #bada55;}
     }
    </style>
   </defs>
  <circle cx="200" cy="200" r="150" id="circle" />
 </g>
</svg>
```

## Colors & Appearance

### Colors: RGB, HSL & HEX

```css
color: white;
color: #fff;
color: #FFFFFF;
color: #FFFFFFFF;
color: rgb(255,255,255);
color: rgb(100%,100%,100%);
color: rgba(255,255,255,1);
color: rgba(100%,100%,100%, 1);
color: hsl(0, 100%, 100%);
color: hsla(0, 100%, 100%, 1);
color: transparent;
color: currentColor;
```

Color Tips

+ transparent == rgba(0, 0, 0, 0);
+ CurrentColor == current Text color
+ Accessibility: Color is NOT the only visual means of conveying information,indicating an action, prompting a response, or distinguishing a visual element.
+ appearance: Changes the appearance of buttons and other controls to resemble native controls.

### Opacity vs. Alpha Transparency

```css
/* Opacity  */
opacity: 1;

/* Alpha Transparency */
color: rgba(0, 0, 0, 1);
background-color: rgba(58, 103, 171, 1);
```

AlphaTransparency Tips

```css
/* Used in RGBA & HSLA colors */
rgba(58, 103, 171, 0.5);
hsla(216, 49%, 45%, 0.5);

/* Transparent Shadows */
.trans {
  box-shadow: -10px 10px rgba(0,0,0,0.3);
  text-shadow: 0 21px 1px rgba(0,0,0,0.3);
}

/* Solid Shadows */
.solid {
  box-shadow: -10px 10px #999;
  text-shadow: 0 21px 1px #999;
}
```

### Appearance

[Appearance](https://estelle.github.io/cssmastery/colors/#slide18)

## Flexbox

### Goal of Flexbox and Demos

[Flex Box](https://estelle.github.io/cssmastery/flexbox/#slide4)

### Browser Support and Overview

[can i use](https://caniuse.com/#search=flexbox)

[Flex Box Specification CSS Flexible Box Layout Module Level 1](https://drafts.csswg.org/css-flexbox/)

[Holy Grail Layout](https://estelle.github.io/cssmastery/flexbox/#slide14)

```css
body {
  display: flex;
  flex-flow: column;
}
main {
  display: flex;
  flex: 1;
}
article {
  flex: 1;
}
nav {
  order: -1;
}
```

### Setup Flex Container & Items

[Components of Flexbox](https://estelle.github.io/cssmastery/flexbox/#slide15)

1. Creation: display
2. Direction: flex-flow (flex-direction, flex-wrap)
3. Alignment: justify-content, align-items, align-self, align-content
4. Ordering: order
5. Flexibility: flex (flex-grow, flex-shrink, flex-basis)

[Flexible Box Properties](https://estelle.github.io/cssmastery/flexbox/#slide16)

### Understanding Flexbox

#### flex-direction property

+ row
+ row-reverse
+ column
+ column-reverse

#### flex-wrap property

+ nowrap
+ wrap
+ wrap-reverse

### Flex Container Properties

Controlling Flex Items From the flex container

#### justify-content property

+ flex-start
+ flex-end
+ center
+ space-between
+ space-around
+ space-evenly

aligns flex items along the main axis

#### align-items property

+ align-items property
+ flex-start
+ flex-end
+ center
+ baseline
+ stretch

aligns flex items along the cross axis

Impact of visibility: collapse

```css
.container{
  display: flex;
  align-items: center;
}
.item {
  flex: 1;
}
.b {
  visibility: collapse;
  /* like visibility: hidden */
}
```

#### align-content property

+ flex-start
+ flex-end
+ center
+ space-between
+ space-around
+ stretch
+ space-evenly

Only applies to multi-line flex containers.

### Flex Item Property

Controlling Flex Items From the flex items themselves

#### align-self property

+ auto
+ flex-start
+ flex-end
+ center
+ baseline
+ stretch

Override the align-items on a per flex item basis

#### order property

```css
div:nth-of-type(3n) {
   order: -1;
}
```

The default value is 0. Anything lower will come before those without set values. Anything above will come after.

### Flexibility & Shorthand

#### flex shorthand

+ flex-grow: How to divide the extra space. Non-negative number. default: 1.
+ flex-shrink: How to shrink if there's not enough room. Non-negative number. default: 1.
+ flex-basis: the starting size before free space is distributed. length value, content or auto . If set to auto, sets to flex item’s main size property.

## Tables

### Overview & Semantics

[HTML Tables & CSS](https://estelle.github.io/cssmastery/tables/#slide1)

#### Table Purpose

Presentation of Data

+ Presenting
+ Comparing
+ Sorting
+ Calculating
+ Cross Referencing

NOT for presentation

#### Structure of a Table

```html
<table>
  <caption></caption>

  <colgroup>
    <col/>
  </colgroup>

  <thead>
    <tr>
      <th></th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td></td>
    </tr>
  </tbody>

  <tfoot>
    <tr>
      <th></th>
    </tr>
  </tfoot>
</table>
```

```html
<table>
  <caption>Table Caption</caption>
  <colgroup>
    <col/>
  </colgroup>
  <thead></thead>
  <tbody></tbody>
  <tfoot></tfoot>
</table>
```

### Caption

+ Specifies the title of table
+ Always the first child of a `<table>`
+ Can be positioned on top or bottom with `caption-side`
  In ff, can be top, bottom, left or right of table
+ Can be styled
+ CSS: Inherits from `table`

#### caption-side property

Put on the `<table>`, not the `<caption>`.

```css
table {
    caption-side: bottom;
}
```

supported

```css
caption-side: top;
caption-side: bottom;
```

Experimental

```css
caption-side: left;
caption-side: right;
caption-side: top-outside;
caption-side: bottom-outside;
```

In the specification

```css
caption-side: inline-start;
caption-side: inline-end;
```

deprecated:

```html
<table align="left | top | right | bottom">
```

### Border Padding & Spacing

#### border-collapse

```css
border-collapse: separate | collapse | inherit
```

When the borders are collapsed `border-spacing` is relevant.

#### border-spacing

```css
border-spacing: <length> <length>?;
```

The look you want is probably

```css
table, td, th {
  border: none;
}

table {
  border-spacing: 5px 10px;
}
```

+ one length: vertical and horizontal padding are the same.
+ two lengths: first is horizontal, second is vertical
+ Note: not TRouBLe
+ Irrelevant if border-collapse: collapse
+ Empty space is part of the table, not the column, tbody, row or cell.

### Other Table Properties

#### Empty Cells

```css
empty-cell: show | hide
```

Similar to

```css
td:empty, th:empty {
  visibility: none;
}
```

+ ignored if border-collapse: separate
+ applies to elements with diplay of table-cell
+ property of table or the cells themselves

#### table-layout

```css
table-layout: auto | fixed
```

fixed renders faster

#### vertical-align

```css
vertical-align: baseline | sub | super | text-top | text-bottom | middle | top | bottom | <percentage> | <length>
```

+ baseline
  Aligns the baseline of the cell with the baseline of all other cells in the row that are baseline-aligned.
+ top
  Aligns the top padding edge of the cell with the top of the row.
+ middle
  Centers the padding box of the cell within the row.
+ bottom
  Aligns the bottom padding edge of the cell with the bottom of the row.

applied to thead, tfoot, tbody, tr, td, th, but not table.
negative values are ok
additional values (sub, super, text-top, text-bottom, `<length>`, and `<percentage>`) equal

```css
tbody {
  vertical-align: top;
}
```

#### display property

```css
display: table | table-cell

display: table;
display: table-row-group;
display: table-header-group;
display: table-footer-group;
display: table-row;
display: table-cell;
display: table-column-group;
display: table-column;
display: table-caption;
```

```css
.parent {
  display: table;
}
.child {
  display: table-cell;
  vertical-align: baseline;
}
```

### Styling a Table

`<col>` Syntax

```html
<table>
  <caption>
    2017–18 Primera División: Player of the week
  </caption>
  <colgroup>
    <col class="week"/>
    <col class="player"/>
    <col class="club"/>
    <col class="stat"/>
  </colgroup>
<thead>
```

通过下面的样式代码，可以给每一列指定样式

```css
col.week {}
col.player {}
col.club {}
col.stat {}
```

## Grid

### Flexbox vs. Grids

[Grids](https://estelle.github.io/cssmastery/grid/#slide1)

[Flexbox](https://estelle.github.io/cssmastery/grid/#slide2)

```css
ol {
  display: flex;
  flex-flow: row wrap;
}

li {
  flex: 1 1 250px;
  margin: 10px;
}

li:last-of-type {
  display: none;
}
```

[Grid](https://estelle.github.io/cssmastery/grid/#slide3)

```css
ol {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 10px;
}

li:last-of-type {
  display: none;
}
```

上述代码中，使用flexbox最后一行会被两列平分，grid最后一行还是会有三列，最后一列空白

[Grid](https://estelle.github.io/cssmastery/grid/#slide4)

```css
ol {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 20px;
}

li:nth-of-type(4) {
  grid-column: auto / span 2;
  background-color: pink;
}
```

上述代码中，第四个单元格会占据两列。

[Grid](https://estelle.github.io/cssmastery/grid/#slide5)

```css
ol {
  grid-template-columns: repeat(12, 1fr);
}
.test {
  grid-column: 2 / 8;
  grid-row: 3 / 5;  /*change values */
}
```

上述代码中，可以方便的修改单个单元格所占的行数与列数。

### Grid Basics

+ Grid lines
  The vertical and horizontal lines that divide the grid and separate the columns and rows. Start counting at 1, not 0.
+ Grid cell
  A single child or unit of a CSS grid.
+ Grid area
  Any rectangular space surrounded by four grid lines. Can contain any number of grid cells.
+ Grid track
  The space between two grid lines. This space can be horizontal or vertical: a grid row or grid column
+ Grid row
  A horizontal grid track.
+ Grid column
  A vertical grid track.
+ Gutter
  Space between two rows and two columns.

[can I use: grid](https://caniuse.com/#search=grid)

#### Setting up the Grid

Properties declared on the parent

```css
display
grid-template-columns
grid-template-rows
grid-template-areas
grid-template (shorthand)
grid-column-gap
grid-row-gap
grid-gap
```

```css
justify-items
align-items
justify-content
align-content
grid-auto-columns
grid-auto-rows
grid-auto-flow
grid
```

### Display Property

display: inline | block | list-item | inline-list-item | inline-block | flex | inline-flex| **grid** | **inline-grid** | table | inline-table | table-row-group | table-header-group | table-footer-group | table-row | table-cell | table-column-group | table-column | table-caption | ruby | ruby-base | ruby-text | ruby-base-container | ruby-text-container | contents | none | flow | flow-root

### Columns & Rows

Defines the columns and rows of the grid with a space-separated list of values representing the track size

```css
grid-template-columns: none | <track-list> | <auto-track-list>
grid-template-rows: none | <track-list> | <auto-track-list>
```

```css
grid-template-columns: 200px 1fr max-content minmax(min-content, 1fr)
```

### fr Unit and repeat() Notation

fr

Fraction Unit
describes a fraction of the available space

repeat()

repeat notation:
repeat(# of repeats, length)

### Adding Gutters & Exercise

Gutter size: global spacing between grid items with:

+ grid-column-gap: vertical space between columns
+ grid-row-gap: horizontal space between rows
+ grid-gap: Shorthand for grid-row-gap and grid-column-gap, in that order. Can take one or two lengths or percents.

### Positioning Grid Items

item positioning properties

```css
.myItem {
  grid-row-start: 2;
  grid-row-end: 4;
  grid-column-start: 2;
  grid-column-end: 5;
}

.myItem {
  grid-row: 2 / 4;
  grid-column: 2 / 5;
}

.myItem {
  grid-area: 2 / 2 / 4 / 5;
  /* grid-area: row-start / column-start / row-end / column-end */
}
```

### Grid Column & Row Naming

if grid-row-start is named, all four longhands are set to that value. Otherwise, set to auto

### Item Properties & Holy Grail Layout

#### Grid Item Properties

```css
grid-column-start
grid-column-end
grid-column

grid-row-start
grid-row-end
grid-row

grid-area

justify-self
align-self
```

#### Holy Grail Layout

```css
body {
  display: grid;
  /* 共三列，左边1/6，中间4/6，右边1/6 */
  grid-template-columns: 1fr 4fr 1fr;
  grid-template-rows: 3em 1fr 1.5em;
  grid-gap: 1em;
}

header {
  grid-row: 1/2;
  grid-column: 1/4;
}

footer {
  grid-row: 3/4;
  grid-column: 1/4;
}
```

### Named Template Areas

Our magic layout:

```css
body {
  display: grid;
  grid-template-columns: 1fr 4fr 1fr;
  grid-template-areas:
      "header header header"
      "nav article aside"
      "footer footer footer";
}

header {
  grid-area: header;
}
nav {
  grid-area: nav;
}
article {
  grid-area: article;
}
aside {
  grid-area: aside;
}
footer {
  grid-area: footer;
}
```

### Align & Justify Items

#### Container Properties

```css
justify-items
align-items
justify-content
align-content
grid-auto-columns
grid-auto-rows
grid-auto-flow
grid
```

#### Item properties

```css
justify-self
align-self
```

### Align and Justify Content

Defines how the items are aligned with respect to the grid if the size of all the items combined is not the same size as the container.
定义如果组合的所有项的大小与容器的大小不相同，则项相对于网格的对齐方式。

```css
justify-content: baseline | center | end | flex-end | flex-start |
   left | normal | right | space-around | space-between | space-evenly | start |
   stretch | safe | unsafe

align-content: baseline | center | end | flex-end | flex-start |
  left | normal | right | space-between | space-evenly | start |
  stretch | safe | unsafe
```

Tip: auto track sizes (and only auto track sizes) can be stretched by the align-content and justify-content properties
提示:自动轨道大小(只有自动轨道大小)可以通过对齐内容和对齐内容属性进行拉伸

### Track Sizing & Auto Flow

When items are placed outside of the tracks defined by grid-template-rows, grid-template-columns, and grid-template-areas, implicit grid tracks by added. These properties size those tracks
当项目被放置在由网格模板行、网格模板列和网格模板区域定义的轨道之外时，添加隐式网格轨道。这些属性决定了轨迹的大小

```css
grid-auto-columns:
grid-auto-rows:
grid-auto-flow:
```

### More Grid Resources

[gridbyexample](https://gridbyexample.com/)

[](https://labs.jensimmons.com/)

## Background & Borders

### Background Properties & Color

#### Background properties

+ background-color
+ background-image
+ background-repeat
+ background-attachment
+ background-position
+ background-clip
+ background-origin
+ background-size
+ background shorthand

#### Border properties

+ border-color
+ border-style
+ border-width
+ border shorthand
+ border-radius

#### Border Images

+ border-image-source
+ border-image-slice
+ border-image-width
+ border-image-outset
+ border-image-repeat
+ border-image shorthand

#### background-color property

+ Use any of the color types
+ Always declare when declaring background images
+ Declare only once in a shorthand declaration.
+ Nothing really new here

#### Background Image

```css
background-image: url(path/aSingleImage.jpg);
```

Often part of background shorthand with:

+ background-repeat
+ background-attachment
+ background-position
+ background-color

Don't use background shorthand for risk of setting any of 8 properties to default values

### Background Image Futures

Allows the use of any element, including `<canvas>` where images can be used.

```css
background-image: -moz-element('#someID');
background-image: element('#someID')
```

Define which portion of the image to show:

```css
background-image:
    -moz-image-rect(url(ico_sprite.jpg), 32, 64, 16, 16);
background-image: image('ico_sprite.jpg#xywh=32,64,16,16')
```

Fallback in case your images doesn't load:

```css
background-image:
     image("try1.svg", "try2.png", "try3.gif", blue)
```

Flip the image if the direction is rtl.

```css
background-image: image("arrow.png" rtl)
```

### Repeat, Attachment & Position

#### background-repeat

```css
background-repeat:
  repeat | repeat-x | repeat-y | no-repeat | space | round;
```

#### background-attachment

```css
background-attachment: fixed | local | scroll
```

#### background-position(old)

```css
div {background-position: 75% 75%;}
div:after {top: 75%; left:75%;}
```

#### background-position

Positioning relative to any corner

```css
background-position: right 50px bottom 50px;
```

#### background-clip & background-origin property

```css
background-clip: border-box | padding-box | content-box

background-origin: border-box | padding-box | content-box
```

#### background-size

```css
background-size: auto | contain | cover | <length>
```

+ Use for creating gradients backgrounds
+ Needed for hiDPI images
+ ‘contain’ grows/shrinks to fully fit
+ ‘cover’ grows to min size that covers 100% of height & width

### Shorthand

```css
background:
 img position / size repeat attachment origin clip,
 img position / size repeat attachment box{1,2} bgcolor;

background:
 url(topImg.jpg) 0 0 / 30px 30px repeat scroll border-box content-box,
 url(botImg.jpg) 15px 15px / 30px 30px fixed border-box #609;
```

**DO NOT USE BACKGROUND SHORTHAND**!!!

### Border Color, Style & Width

#### border-color

Use any of the color types
Nothing really new here

```css
  border-color: white;
  border-color: #fff;
  border-color: #FFFFFF;
  border-color: #FFFFFFFF;
  border-color: rgb(255,255,255);
  border-color: rgb(100%,100%,100%);
  border-color: rgba(255,255,255,1);
  border-color: rgba(100%,100%,100%, 1);
  border-color: hsl(0, 100%, 100%);
  border-color: hsla(0, 100%, 100%, 1);
  border-color: transparent;
  border-color: currentColor; /* default */
```

#### border-style

```css
border-style: none;
border-style: hidden;
border-style: dotted;
border-style: dashed;
border-style: solid;
border-style: double;
border-style: groove;
border-style: ridge;
border-style: inset;
border-style: outset;
```

#### border-width

```css
border-width: (length) | thin | medium | thick | inherit {1,4};
```

#### border shorthand

```css
border: width style color;

border-left: width style color;

border-top: 5px dashed rgba(217,68,11, 0.8);
```

+ style is REQUIRED.
+ width defaults to medium
+ color defaults to currentColor

### Border Radius & Border Image

#### border-radius

```css
border-top-left-radius
border-top-right-radius
border-bottom-right-radius
border-bottom-left-radius
```

DO NOT PREFIX

```css
.circle {border-radius: 50%;}

.oval { border-radius: 50%;}

.different { border-radius: 10px 30px;}

.elliptical { border-radius: 10px / 30px;}

.uglier {
  border-radius: 10px 35px 20px 15px /
                 30px 35px 5px 5px;}
```

#### border-image

```css
border-image: source || slice / width / outset  || repeat;
```

```css
border-image-source: none | url() | <image>

border-image-slice: <number> | XX% {1,4} && fill

border-image-width: <length> | XX% | <number> | 1 | auto {1,4}

border-image-outset: <length> | <number> {1,4}

border-image-repeat: stretch | repeat | round {1,2}

border-image: url(gradient.png) 34 / 34px stretch;
```

## Gradients

### Gradients Demos & Overview

[CSS Gradients](https://estelle.github.io/cssmastery/gradients/#slide1)

+ Anywhere an image can be used (theoretically)
  background-image, list-style-type, border-image, content, cursor
+ 4 Gradient Types

1. linear-gradient();
2. radial-gradient();
3. repeating-linear-gradient();
4. repeating-radial-gradient();

#### conical gradient

[conical gradient](https://estelle.github.io/cssmastery/gradients/#slide11)

### Linear & Radial Gradient Syntax

#### Linear Gradient Syntax

```css
linear-gradient([<angle>| to <side-or-corner>,]?
   [<color-stop>[, <color-hint>]?, ]# <color-stop>)
```

Things to remember:

1. Use 'to' with keyterms 在关键词中使用to
2. 0deg is to top  0度是最高的
3. Angles go clockwise 角度顺时针

```css
.slide {
  background-image:
      linear-gradient(rebeccapurple, palegoldenrod);
}
```

#### Radial Gradient Syntax

```css
radial-gradient([<shape>|| <size> at <position>]?
   [<color-stop>[, <color-hint>]?, ]# <color-stop>)
```

Things to remember:

1. Use 'at' with position
  与position连用at
2. position is center of gradient
  position是梯度的中心
3. If shape is specified as circle or omitted, the size can be a length/percent
  如果形状指定为圆形或省略，则大小可以是长度/百分比

### Color Stops

```css
.slide {
  background-image:
    linear-gradient(
      red 0vh,
      orange 20vh,
      yellow 40vh,
      green 60vh,
      blue 80vh,
      purple 100vh
    )
}
```

### Color Hints

+ Midpoint of transition between 2 stops
+ Length? Any length unit
+ Percent? relative to gradient line which is relative to image size
+ Hard Stop? Use 0%
+ Point is relative to the 0 (zero) mark, not from the previous color stop

```css
linear-gradient(rebeccapurple, 50%, palegoldenrod);
```

### Gradient Directions

#### Keyterm Directions

```css
linear-gradient([<angle>| to <side-or-corner>,]?
   [<color-stop>[, <color-hint>]?, ]# <color-stop>)
```

Gradient progresses toward the declared side or corner.

`to [left | right] || [top | bottom]`

+ to top
+ to bottom
+ to left
+ to right
+ to top left
+ to top right
+ to bottom left
+ to bottom right

#### Angles

+ Gradient progresses the declared angle.
+ Degrees go clockwise, starting at 12:00
  + top: 0%;
  + right: 90%;
  + bottom: 180%;
  + left: 270%;
+ 0deg is the same as to top
+ 45% is NOT the same as to top right
+ deg is required

### Stripes with Repeat Linear Gradient

```css
background-color: rebeccapurple;
background-image:
   linear-gradient(135deg,
    rgba(255, 255, 255, 0.2) 25%,
    rgba(255, 255, 255, 0) 25%,
    rgba(255, 255, 255, 0) 50%,
    rgba(255, 255, 255, 0.2) 50%,
    rgba(255, 255, 255, 0.2) 75%,
    rgba(255, 255, 255, 0) 75%,
    rgba(255, 255, 255, 0) 100%
);
background-size: 100px 100px;
background-repeat: repeat;
```

### Radial Gradient & Position

```css
radial-gradient([<shape>|| <size> at <position>]?
   [<color-stop>[, <color-hint>]?, ]# <color-stop>)
```

Things to remember:

1. Use 'at' with position
2. position is center of gradient
3. If shape is specified as circle or omitted, the size can be a length/percent

```css
.slide {
  background-image:
    radial-gradient(ellipse at center,
      palegoldenrod 0%,
      rebeccapurple 100%);
}
```

#### position

+ same values as background-position
+ include 'at'
+ location of gradient center point
+ Gradient ray, no angled gradient line

```css
.slide {
  background-image:
    radial-gradient(ellipse at 75% 95%,
      black 1%, 1%,
      palegoldenrod 2%,
      rebeccapurple 100%);
}
```

### Shape, Size & Sizing KeyTerms

#### Shapes

Two options:

+ circle
+ ellipse

declared explicitly, or implied via size declaration
显式声明，或通过大小声明隐含

+ circle: single length value: radius
  圆:单长 值:半径
+ ellipse: two length values: width height
  椭圆:两个长度 值:宽高
+ percentage size only for ellipses
  百分比大小只适用于椭圆

```css
.slide {
  background-image:
    radial-gradient(ellipse,
      black 1%, 1%,
      palegoldenrod 2%, 50%,
      rebeccapurple 50%);
}
```

#### Sizing With Keywords

closest-side
closest-corner
farthest-side
farthest-corner

#### Size with KeyTerms

```css
.slide {
  background-image:
    radial-gradient(circle farthest-corner
      at bottom 40px right 50px,
      black 1%, 1%,
      palegoldenrod 2%, 98%,
      rebeccapurple 98%);
}
```

+ closest-side
  circle: gradient ray end touches closest side.
  ellipse: vertical ray touches closest of top or bottom edge. horizontal ray touchest closest of right or left side.
+ farthest-side
  circle: gradient ray end touches furthest side.
  ellipse: vertical ray touches furthest of top or bottom edge. horizontal ray touchest furthest of right or left side.
+ closest-corner
  circle: radius is length from center of circle (position) to closest corner.
  ellipse: gradient ray touches corner closest to center while maintaining aspect ratio.
+ farthest-corner
  Default!
  circle: radius is length from center of circle (position) to furthest corner.
  ellipse: gradient ray touches corner furthest from center while maintaining aspect ratio.

### Color Stops, Hints & Repeating

```css
.slide {
  background-image:
    radial-gradient(circle closest-side at 100px,
      red 0%,
      orange 20%,
      yellow 40%,
      green 60%,
      blue 80%,
      purple 100%
    );
}
```

#### repeating-radial-gradient()

```css
repeating-radial-gradient([<shape>|| <size> at <position>]?
   [<color-stop>[, <color-hint>]?, ]# <color-stop>)
```

Things to remember:

1. repeats the radial gradient after the 100% mark.
   在100%标记后重复径向梯度。
2. Has no impact when 'furthest-corner' is used or defaults for gradient size
   当使用“furthest-corner”或默认渐变大小时没有影响
3. Use 'at' with position
   与position连用at
4. position is center of gradient
   位置是梯度的中心
5. If shape is specified as circle or omitted, the size can be a length/percent
   如果形状指定为圆形或省略，则大小可以是长度/百分比

```css
.slide {
  background-image:
    repeating-radial-gradient(circle
      closest-side at 100px,
      red 0%,
      orange 20%,
      yellow 40%,
      green 60%,
      blue 80%,
      purple 100%
    );
}
```

## Transforms

### Transform Overview

[Transforms](https://estelle.github.io/cssmastery/transforms/#slide1)

```css
.letters {
transform:
 translate(-90px, 300px) rotate(50deg) scale(.7, .9) skew(15deg);
}

selector {
  transform: none | <transFunc()> [<transFunc()>];
}
```

[can i use](https://caniuse.com/#search=2D%20transforms)

### 2D Transform Funtions

`translate(<length>[, <length>])`
specifies a 2D translation by the vector [x, y], where x is the  translation-value parameter for the x axis and y  is the optional translation value along the y axis. parameter. If **y** is not provided, y==0.
通过向量[x, y]指定二维平移，其中x是x轴的平移值参数，y是沿y轴的可选平移值。参数。如果没有提供**y**，则y==0。

`translateX(<length>)`
specifies a translation by the given amount in the X direction.
指定在X方向上给定数量的平移量。

`translateY(<length>)`
specifies a translation by the given amount in the Y direction.
指定在Y方向上给定数量的平移量。

`scale(<number>[, <number>])`
specifies 2D scaling operation by the [sx,sy]. If sy  is not provided, sy will equal sx (growsing or shrinking with the same scale). Scale(1, 1)  or scale(1) leaves an element in it's default state. Scale(2, 2) or scale(2)  causes the element to appear twice as wide and twice as tall as its default size, taking up 4-times the original area.
指定[sx,sy]的2D缩放操作。如果不提供sy, sy将等于sx(以相同的比例增长或收缩)。Scale(1,1)或Scale(1)保留元素的默认状态。Scale(2,2)或Scale(2)使元素的宽度和高分别为默认大小的两倍和两倍，占原始区域的4倍。

`scaleX(<number>)`
specifies a scale operation using the [sx, 1] scaling vector, where sx is given as the parameter.
使用[sx, 1]缩放向量指定缩放操作，其中sx作为参数。

`scaleY(<number>)`
specifies a scale operation using the [1, sy] scaling vector, where sy is given as the parameter.
指定使用[1,sy]缩放向量的缩放操作，其中sy作为参数给出。

`rotate(<angle>)`
specifies a 2D rotation by the angle specified in the parameter about the origin of the element, as defined by the **transform-origin** property. For example, rotate(90deg) would cause elements to appear rotated one-quarter of a turn in the clockwise direction.
根据元素原点参数中指定的角度指定2D旋转，由**transform-origin**属性定义。例如，旋转(90deg)会使元素以顺时针方向旋转四分之一圈。

`skewX(<angle>)`
specifies a skew transformation along the X axis by the given angle.
指定给定角度沿X轴的倾斜变换。

`skewY(<angle>)`
specifies a skew transformation along the Y axis by the given angle.
指定给定角度沿Y轴的倾斜变换。

`matrix(<num>, <num>, <num>, <num>, <num>, <num>)`
Generally machine generated, specifies a 2D transformation in the form of a transformation matrix of six values. matrix(a,b,c,d,e,f) is equivalent to applying the transformation matrix **[a b c d e f]**.
一般由机器生成，以六个值的转换矩阵的形式指定二维转换。矩阵(a,b,c,d,e,f)等价于应用变换矩阵**[a b c d e f]*。

### Function & Tranform Order

#### multiple transforms

```css
transform: translate(-80px, 200px)
  rotate(-15deg) scale(2, 1.5) skewX(8deg);
```

#### transforms

+ Take advantage of transform-origin
+ Multiple values are SPACE separated (no commas)
+ The order of the values matters
+ [3D transforms](http://westciv.com/tools/3Dtransforms/index.html) are hardware accelerated
+ Play with [Westciv's Transform Tool](http://westciv.com/tools/transforms/index.html)
+ [Matrix](http://meyerweb.com/eric/tools/matrix/) is another syntax
+ More about [transforms](http://www.standardista.com/css3/css3-transform-property-and-the-various-transform-functions)

#### Transform Order

[Transform Order](https://estelle.github.io/cssmastery/transforms/#slide13)

Order of transform functions matters: if you rotate first, your translate direction will be on the rotated axis!
转换函数的顺序很重要:如果你先旋转，你的平移方向将在旋转轴上!

### 3D Transform Functions & Properties

#### 3D Transform Functions

+ `translate(<x-length>[, <y-length>])`
  specifies a 2D translation by the vector [tx, ty], where tx is the first translation-value parameter and ty is the optional second translation-value parameter. If *`<ty>`* is not provided, ty has zero as a value.
  通过向量[tx, ty]指定2D转换，其中tx是第一个平动值参数，ty是可选的第二个平动值参数。如果没有提供**`<ty>`*，则ty的值为0。
+ **`translate3d(<x>, <y>, <z>)`**
  specifies a 3D translation by the vector [tx,ty,tz], with tx, ty and tz being the first, second and third translation-value parameters respectively.
  指定向量[tx,ty,tz]的三维平移，其中tx、ty和tz分别是第一个、第二个和第三个平移值参数。
+ `translateX(<x-length>), translateY(<y-length>)`
  specifies a translation by the given amount in the X or Y direction.
  指定在X或Y方向上给定数量的平移量。
+ **`translateZ(<z-value>)`**
  specifies a translation by the given amount in the Z direction. Note that percentage values are not allowed in the translateZ translation-value, and if present are evaluated as 0.
  指定在Z方向上给定数量的平移量。注意，百分比值在translateZ平动值中是不允许的，如果当前值被计算为0。
+ `scale(<number>[, <number>])`
  specifies a 2D scale operation by the [sx,sy] scaling vector described by the 2 parameters. If the second parameter is not provided, it is takes a value equal to the first.
  指定由两个参数描述的[sx,sy]缩放向量进行的2D缩放操作。如果没有提供第二个参数，它将接受一个与第一个相同的值。
+ **`scale3d(<number>, <number>, <number>)`**
  specifies a 3D scale operation by the [sx,sy,sz] scaling vector described by the 3 parameters.
  指定由3个参数描述的[sx,sy,sz]缩放向量进行的三维缩放操作。
+ `scaleX(<number>), scaleY(<number>)`
  specifies a scale operation using the [sx,1,1] or [1,sy,1] scaling vector, where sx or sy is given as the parameter respectively.
  指定使用[sx,1,1]或[1,sy,1]缩放向量的缩放操作，其中sx或sy分别作为参数给出。
+ **`scaleZ(<number>)`**
  specifies a scale operation using the [1,1,sz] scaling vector, where sz is given as the parameter.
  使用[1,1,sz]缩放向量指定缩放操作，其中sz作为参数。
+ `rotate(<angle>)`
  specifies a 2D rotation by the angle specified in the parameter about the origin of the element, as defined by the **transform-origin* property.
  根据元素原点参数中指定的角度指定2D旋转，由**transform-origin*属性定义。
+ **`rotate3d(<number>, <number>, <number>, <angle>)`**
  specifies a clockwise 3D rotation by the angle specified in last parameter about the [x,y,z] direction vector described by the first 3 parameters. If the direction vector is not of unit length, it will be normalized. A direction vector that cannot be normalized, such as [0, 0, 0], will cause the rotation to not be applied. This function is equivalent to matrix3d(1 + (1-cos(angle))*(x*x-1), -z*sin(angle)+(1-cos(angle))*x*y, y*sin(angle)+(1-cos(angle))*x*z, 0, z*sin(angle)+(1-cos(angle))*x*y, 1 + (1-cos(angle))*(y*y-1), -x*sin(angle)+(1-cos(angle))*y*z, 0, -y*sin(angle)+(1-cos(angle))*x*z, x*sin(angle)+(1-cos(angle))*y*z, 1 + (1-cos(angle))*(z*z-1), 0, 0, 0, 0, 1).
  指定按最后一个参数中指定的角度顺时针旋转3D，前三个参数描述的[x,y,z]方向向量。如果方向向量不是单位长度的，它将被归一化。不能归一化的方向向量，如[0,0,0]，将导致不应用旋转。这个函数相当于matrix3d (1 + (1-cos(角))*(x * x - 1) - z *罪(角)+ (1-cos(角))* x * y, y *罪(角)+ (1-cos(角))* x * z, 0, z *罪(角)+ (1-cos(角))* x * y, 1 + (1-cos(角))* (y * y-1), - x *罪(角)+ (1-cos(角))* y * z, 0, - y *罪(角)+ (1-cos(角))*x*z,x * sin(角)+ (1-cos(角))* y*z, 1 + (1-cos(角))*(z * z 1), 0, 0, 0, 0, 1)。
+ `rotateX(<angle>)`
  specifies a clockwise rotation by the given angle about the X axis.
  指定绕X轴的给定角度的顺时针旋转。
+ `rotateY(<angle>)`
  specifies a clockwise rotation by the given angle about the Y axis.
  指定以给定的角度绕Y轴顺时针旋转。
+ **`rotateZ(<angle>)`**
  specifies a clockwise rotation by the given angle about the Z axis.
  指定绕Z轴的给定角度的顺时针旋转。
+ `skewX(<angle>),skewY(<angle>)`
  specifies a skew transformation along the X axis or Y axis by the given angle.
  指定给定角度沿X轴或Y轴的倾斜变换。
+ `skew(<angle> [, <angle>])`
  specifies a skew transformation along the X and Y axes. The first angle parameter specifies the skew on the X axis. The second angle parameter specifies the skew on the Y axis. If the second parameter is not given then a value of 0 is used for the Y angle (ie. no skew on the Y axis).
  指定沿X轴和Y轴的倾斜变换。第一个角参数指定X轴上的倾斜。第二个角参数指定了Y轴上的倾斜。如果没有给出第二个参数，则Y角的值为0(即。Y轴没有倾斜)。
+ `matrix(<number>{6})`
  specifies a 2D transformation in the form of a transformation matrix of six  comma separated values. matrix(a,b,c,d,e,f) is equivalent to matrix3d(a, b, 0, 0, c, d, 0, 0, 0, 0, 1, 0, e, f, 0, 1).
  以由六个逗号分隔值组成的转换矩阵的形式指定二维转换。矩阵(a,b,c,d,e,f)等价于矩阵x3d(a, b, 0,0,c,d, 0,0,0,1, 0, e,f, 0,1)
+ **`matrix3d(<number>{15})`**
  specifies a 3D transformation as a 4x4 homogeneous matrix of 16 comma separated values in column-major order.
  指定一个3D转换为4x4齐次矩阵，其中16个逗号分隔值按列-主顺序排列。
+ **`perspective(<number>)`**
  specifies a perspective projection matrix. This matrix maps a *viewing cube* onto a pyramid whose base is infinitely far away from the viewer and whose peak represents the viewer's position. The viewable area is the region bounded by the four edges of the viewport (the portion of the browser window used for rendering the webpage between the viewer's position and a point at a distance of infinity from the viewer). The *depth*, given as the parameter to the function, represents the distance of the z=0 letters from the viewer. Lower values give a more flattened pyramid and therefore a more pronounced perspective effect. The value is given in pixels, so a value of 1000 gives a moderate amount of foreshortening and a value of 200 gives an extreme amount. The matrix is computed by starting with an identity matrix and replacing the value at row 3, column 4 with the value -1/depth. The value for depth must be greater than zero, otherwise the function is invalid.
  指定透视投影矩阵。这个矩阵将一个*观察立方体*映射到一个金字塔上，该金字塔的底部无限远离观察者，其峰值代表观察者的位置。可视区域是由viewport的四条边(浏览器窗口的一部分，用于在查看器的位置和与查看器无限远的点之间呈现页面)所限定的区域。作为函数的参数，*depth*表示z=0个字母到查看器的距离。数值越低，金字塔越平，透视效果越明显。该值以像素为单位给出，因此值1000表示适度的透视，值200表示极端的透视。矩阵的计算方法是从一个单位矩阵开始，将第3行第4列的值替换为-1/depth。深度值必须大于零，否则函数无效。

#### 3D Transform related Properties

+ perspective: none | length
  Same as `transforms: perspective(value)`  except it applies to the positioned or transformed children of the element, not the element itself.
  与`transforms: perspective(value)`相同，但它适用于元素的定位或转换后的子元素，而不是元素本身。
+ transform-origin: length | keyterm {1,3}
  The centerpoint of the transform
  变换的中心点
+ transform-style: flat | preserve-3d
  How to handle nested elements are rendered in 3D space.
  如何处理嵌套元素呈现在三维空间。
+ perspective-origin: pos relative to parent
  Defines the origin for the perspective property. It effectively sets the X and Y position at which the viewer appears to be looking at the children of the element.
  定义透视图属性的原点。它有效地设置了X和Y的位置，在这个位置上，查看器似乎正在查看元素的子元素。
+ backface-visibility: visible | hidden
  When an element is flipped, is the content that is flipped away from user visible or not.
  当元素被翻转时，是从用户可见或不可见的角度翻转的内容。

### Perspective & Perspective Origin

#### Perspective

Screens are flat. 3D requires perspective.

1. transform: perspective(100) ...
2. perspective: 100

Difference: with the parent property, all the transformed elements share the same perspective. with the function, each transformed element has it's own perspective.
区别:使用父属性，所有转换后的元素共享同一个透视图。使用该函数，每个转换后的元素都有自己的透视图。

#### Transform Function: perspective()

```css
transform: perspective(40px) rotateX(10deg)
```

The perspective() function must come first
Smaller numbers have a bigger impact

#### perspective Property

```css
.child {
  transform: rotateX(10deg);
}
.parent {
   perspective: 40px;
}
```

#### perspective-origin

`perspective-origin: x y`

+ Property on parent element
  父元素上的属性
+ Positions the perspective relative to parent, defining the origin for the perspective property.
  将透视图相对于父视图定位，为透视图属性定义原点。
+ Sets the X and Y position at which the viewer appears to be looking at the children of the element.
  设置查看器似乎正在查看元素的子元素的X和Y位置。

#### perspective-origin Property

```css
.one, .two { perspective: 100px; }
.one:after, .two:after { transform: rotateX(45deg); }
.one { perspective-origin: top left; }
.two { perspective-origin: top right; }
```

#### transform-origin

```css
transform: perspective(40px) rotateX(10deg);
transform-origin: top left;
```

Specifies the x and y position of the origin, relative to the transform object.
指定原点相对于转换对象的x和y位置。

#### transform-origin property

```css
transform: rotate(90deg);
transform-origin:  top left;
```

+ Keyword positions: left, right, bottom, top, center
+ Length values
+ Percentage values
+ default is 50% 50% (or center center)
+ Supported in all browsers that support transform. Prefixed if transform is prefixed

### Backface Visibility & Box

#### transform-style

`transform-style: flat | preserve-3d`

How to handle nested elements are rendered in 3D space.

+ Default is flat
+ Set on parent element
+ Only works if the following are set to their default values:
  + overflow
  + clip
  + clip-path
  + filter
  + mask-border-source
  + mask-image
  + mix-blend-mode

#### transform-style Property

```css
.parent {
    transform: perspective(400px) rotateX(15deg) rotateY(-15deg);
    transform-style: preserve-3d;
}
.child {
    transform: perspective(400px) translateZ(90px) rotate(20deg);
}
```

```css
.one, .two {
    transform: rotateY(245deg);
    transform-style: flat; }
.one:after, .two:after {
    transform: translateZ(50px) rotateX(20deg); }
.two {
    transform-style: preserve-3d; }
```

#### backface-visibility Property

```css
.one:after,
.two:after { transform: rotateX(180deg); }
.one:after { backface-visibility: visible; }
.two:after { backface-visibility: hidden; }
```

transform-box property

Future: Defines the layout box, to which the transform and transform-origin properties relate to. (FF, Ch 64, O 51)
Future:定义与transform和transform-origin属性相关的布局框。(FF, Ch 64, O 51)

+ border-box
  border box as reference box.
+ fill-box
  Uses the object bounding box as reference box.
+ view-box
  Nearest SVG viewport as reference box. If viewBox attribute exists, the reference box is at the viewBox 0 0, and the width and height are those of the viewBox attribute.

## Transitions

### Overview & Transition Properties

[Transitions Overview](https://estelle.github.io/cssmastery/animations/#slide2)

Simplest Transition

```css
a {
  color: green;
}
a:hover {
  color: orange;
}
```

#### CSS3 Transition

```css
a {
  color: green;
  transition: 1s;
}
a:hover {
  color: orange;
}
```

#### Transitions Properties

Enables the transition of properties from one state to the next over a defined length of time

+ **transition-property:** properties (or 'all') that transition
+ **transition-duration:** s or ms it takes to transition
+ **transition-timing-function:** bezier curve of transition
+ **transition-delay:** s or ms before transition starts
+ **transition:** shorthand for 4 transition properties

#### transitions

```css
code {
   color: black;
   font-size: 85%;
   background-color: rgba(255,255,255,0.9);
   transition: all 2s ease-in 50ms;
}
code:hover {
   color: red;
   font-size: 120%;
   background-color: rgba(255,255,255,0.8);
}
```

```css
code {
   transition:
      color, font-size, background-color 2s ease-in 50ms;
}
```

```css
code {
   color: black;
   font-size: 85%;
   background-color: rgba(255,255,255,0.9);
   transition: all 2s ease-in 50ms;
}
code:hover {
   color: red;
   transform: scale(1.4);
   transform-origin: 0 0;
   background-color: rgba(255,255,255,0.8);
}
```

```css
code {
   transition:
      transform 2s ease-in 50ms;
}
```

### Animatable Properties

What can be transitioned?

Anything that has intermediate values

What can be transitioned?

2 values that have REAL intermediary values

#### Transitionable Properties

background-color
background-position
border-color
border-width
border-spacing
bottom
clip
color
crop
font-size
font-weight
height
left
letter-spacing
line-height
margin
max-height
max-width
min-height
min-width
opacity
outline-color
outline-offset
outline-width
padding
right
text-indent
text-shadow
top
vertical-align
visibility
width
word-spacing
z-index

#### Transition Features (or Limitations)

+ Single Iteration
  一次迭代
+ Reverse goes to initial state
  反转到初始状态
+ No granular control
  无颗粒控制
+ Limited methods of initiation
  有限的起始方法
+ Can't force them to finish
  不能强制完成

### Events & Transition Examples

#### transitionend event

+ Event thrown only when transition completes
+ transitionend for EVERY property
+ transitionend for each long-hand property within a shorthand

```css
table {
  border: 1px black solid;
  transition: border 1s linear 50ms;
}
table:hover {
  border: 5px red solid;
}
```

+ border-top-color
+ border-right-color
+ border-bottom-color
+ border-left-color
+ border-top-width
+ border-right-width
+ border-bottom-width
+ border-left-width

#### Examples

[Putting it together in the Real World](https://estelle.github.io/cssmastery/animations/#slide19)

```css
/* 导航栏 */
nav ul li {
  list-style-type: none;
}
nav > ul > li {
  display: inline-block;
  position:relative;
}
nav ul ul {
  transform: scale(1,0);
  transform-origin: top center;
  transition:0.2s linear 50ms;
  position:absolute;
  top: 100%;
}
nav li:hover ul {
  transform: scale(1,1);
}
```

[Pick a card](https://estelle.github.io/cssmastery/animations/#slide20)

```css
.card {
  position: absolute;
  perspective: 600px;
}
.card:hover {
  transition: 1s ease;
}
.card .front {
  transform: rotateX(0deg) rotateY(0deg);
  transform-style: preserve-3d;
  backface-visibility: hidden;
  transition: all .4s ease-in-out;
}
.card:hover .front {
  transform: rotateY(180deg);
}
.card .back {
  transform: rotateY(-180deg);
  transform-style: preserve-3d;
  backface-visibility: hidden;
  transition: all .4s ease-in-out;
}
.card:hover .back {
  transform: rotateY(0);
}
.card:first-of-type {
  transform: rotate(-5deg);
}
.card:nth-of-type(2):hover {
  transform: rotate(0) translatex(120px);
}
.card:last-of-type {
  transform: rotate(5deg);
}
.card:first-of-type:hover {
  transform: rotate(10deg) translatey(180px);
}
```

## Animations

### CSS Animations Features

[Animations](https://estelle.github.io/cssmastery/animations/#slide21)

#### Animation Features (or limitations)

+ Single, many or infinite iterations
  单个、多个或无限次迭代
+ Single or bi-directional
  单个或双向
+ Granular control
  细粒度的控制
+ Can be initiated on page load
  可以在页面加载时启动
+ Has more robust JS Hooks
  有更健壮的JS钩子
+ Can be paused
  可以暂停
+ Lowest priority in UI Thread
  UI线程中的最低优先级

#### Animation Essentials

+ @keyframes
+ animation-name
+ animation-duration
+ animation-timing-function
+ animation-iteration-count
+ animation-direction
+ animation-play-state
+ animation-delay
+ animation-fill-mode
+ animation (shorthand)

### @keyframes

#### Name your keyframe animation

`@keyframes clever_name_here ...`

+ Identifier
+ Unquoted
+ Characters [a-zA-Z0-9], -, _ and ISO 10646 characters U+00A0 and higher
+ ISO 10646 = Universal Character Set = Unicode standard
+ [-_a-zA-Z0-9\u00A0-\u10FFFF]
+ Can't start [0-9], -- , or hyphen+digit
+ Can contain escaped:
  Q&A! may be written as Q\&A\! or Q\26 A\21

**Don't use keyterms**!

What's in a name?

```css
div {
  animation: infinite 3s alternate;
}
@keyframes {
  from, 30% { background-color: #030; }
  to { background-color: #393; }
}
```

#### Keyframes

```css
@keyframes writing {

    from {
      left: 0;
    }

    to {
      left: 100%;
    }
}
```

```css
@keyframes writing {
    0% {
      left: 0;
    }

    100% {
      left: 100%;
    }
}
```

Don't forget the %

Don't quote the animation name

#### 0% and 100%

```css

div {
  animation: test infinite 2s alternate;
  background-color: red;  border: 1px black solid;
}

@keyframes test {
  45% { background-color: green; border-width: 10px;}
  55% { background-color: blue; border-width: 20px;}
  /*
  0%到100%默认值为元素原本的属性值
  0%, 100% {background-color: red;  border: 1px black solid;}  
   */
}
```

#### Granular animation control

```css
@keyframes writingWhileTired {
    0% {
      left: 0;
    }

    10% {
      left: 45%;
    }

    90% {
      left: 55%;
    }

    100% {
      left: 100%;
    }
}
```

#### !important it isn't

```css
div {
  animation: test infinite 2s alternate;
  background: red; border: 5px black solid;
}
@keyframes test {
  45% { background: green; }
  /* 下面两个属性变化没有生效 加了!important的属性会被忽略*/
  55% { background: blue !important;  
    border-width: 20px !important;
  }
}
```

#### Animating multiple properties

```css
@keyframes W {
    0% {
      left: 0;
      top: 0;
    }
    25%, 75% {
      top: 400px;
    }
    50% {
      top: 50px;
    }
    100% {
      left: 80%;
      top: 0;
    }
}
```

#### Duplicated Keyframes

```css
div {
  animation: duplicate infinite 20s alternate;
  background-color: red; opacity: 1;
}
@keyframes duplicate {
  45% { background-color: green; }
  45% { opacity: .3;}
  55% { background-color: blue; }
  55% { opacity: .7;}
  }
```

#### Animating CSS Transforms

尽量使用transform而不是top、left等，以减少页面重绘

```css
@keyframes gettingThePencil {
  0%{
    transform:
      translate3d(0, 0, 0)
      rotate(0deg)
      scale(0.5, 0.5);
  }

  100% {
    transform:
      translate3d(600px, 400px, 0)
      rotate(80deg)
      scale(1.2, 1.2);
  }
}
```

### Naming Animations & Specificity

#### Base CSS

如下设置了基本属性，但是并没有设置动画名称

```css
.pencil {
  width:100px;
  text-align: right;
  border-bottom: 10px solid;
}
.pencil::after {
  content: '✎';
  position: absolute;
  bottom: -20px;
  right: -10px;
}
```

#### animation name and duration

添加动画名称及时间后，动画进行

```css
.pencil {
  animation-name: drawALine;
  animation-duration: 7s;
}
```

#### Order of Precedence

最后一个动画将会执行，既blue

```css
div {
  animation: infinite 3s alternate;
  animation-name: red, green, blue;
}
```

#### Specificities

可以看到动画的background-color及border覆盖了元素原有的属性，即使其添加了!important来提升优先级。

```css
#blue {
  background-color: #9999ff;
}
div {
  border: 5px black solid !important;
  background-color: black !important;
  animation: grey infinite 3s alternate;
}

@keyframes grey {
  from {
    background-color: #999;
    border: 5px red dashed;
  }
  to {
    background-color: #ccc;
    border: 5px yellow dashed;
  }
}
```

#### !important it isn't too

```css
div {
  animation: test infinite 10s alternate;
  background: red; border: 5px black solid;
}
@keyframes test {
  45% { background: green; }
  55% { background: blue !important;  
    border-width: 20px !important;
  }
}
```

### Timing Functions

#### ‘animation-timing-function’

```css
.pencil {
  animation-name: drawALine;
  animation-duration: 5s;
  animation-timing-function: linear;
}
```

```css
ease           linear
ease-in        ease-out
ease-in-out    cubic-bezier(x1, y1, x2, y2)
step-start
step-end
steps( X, start|end)
```

#### Cubic Bezier

[cubic-bezier.com](https://cubic-bezier.com/#.42,0,1,1)

```css
div {
  animation: height 10s cubic-bezier(0,4,1,-4) 5s both;
}
@keyframes height  {
  0% {height: 100px; width: 500px;}
  100% {height: 100px; width: 100px;}
}
```

```css
div {
  animation: width 2s cubic-bezier(0,4,1,-4) both;
}
@keyframes width {
  0% {height: 5px; width: 500px;}
  100% {height: 5px; width: 100px;}
}
```

### Steps

[Steps()](https://estelle.github.io/cssmastery/animations/#slide46)

```css
@keyframes width  {
  0% {width: 0px;}
  100% {width: 500px;}
}
#grey {
  background: #aaa;
  animation: width 10s steps(10, start) 5s both;
}
#black {
  background: #000;
  animation: width 10s linear 5s both;
}
```

[Steps()](https://estelle.github.io/cssmastery/animations/#slide47)

```css
#psy {width: 225px; height: 400px;
  background-image: url(sprite.png);
  animation: gangham 4s steps(23,start) infinite,
    movearound 15s alternate infinite steps(23,start);
}
@keyframes gangham {
  0% {background-position: 0 0}
  100% {;background-position: -5175px 0}
}
@keyframes movearound {
  0% {transform: translatex(-300px);}
  100% {transform: translatex(300px);}
}
```

[steps(4, end)](https://estelle.github.io/cssmastery/animations/#slide48)

```css
.biker {
  height: 92px;
  width: 90px;
  background-image: url(../static/img/bike.png);
  background-repeat: no-repeat;
  animation: pedal 400ms steps(4, end) infinite;
}
@keyframes pedal {
    100% {background-position: -360px 0;}
}
```

### Iteration Count & Delays

#### ‘animation-iteration-count’

```css
.pencil {
  ...
  animation-name: drawALine;
  animation-duration: 3s;
  animation-timing-function: ease-in-out;
  animation-iteration-count: 3;
}
```

‘infinite’ or number. Default is ‘1’.

#### Partial Iteration

重复次数可以是小数，不能是负数

```css
div {
  animation: width 10s linear 5s;
  animation-iteration-count: 0.25;
}
@keyframes width  {
  0% {height: 500px; width: 500px;}
  100% {height: 100px; width: 100px;}
}
```

#### No Iteration

```css
div {
  animation-name: red, green, blue;
  animation-fill-mode: forwards;
  animation-iteration-count: 0;
  animation-delay: 10s;
}
```

#### ‘animation-delay’

默认值时0，可以为负值

```css
.pencil {
  animation-name: drawALine;
  animation-duration: 8s;
  animation-timing-function: ease-in-out;
  animation-iteration-count: 2;
  animation-delay: 2s;
}
```

### Direction, Shorthand & Fill Mode

#### ‘animation-direction’

```css
.pencil {
  ...
  animation-name: drawALine;
  animation-duration: 5s;
  animation-timing-function: ease-in-out;
  animation-iteration-count: 5;
  animation-direction: normal
}
```

values: normal | alternate | reverse | alternate-reverse;

`animation-direction: alternate;`

#### ‘animation’ (shorthand)

```css
.pencil {
  animation-name: drawALine;
  animation-duration: 5s;
  animation-delay: 100ms;
  animation-timing-function: ease-in-out;
  animation-iteration-count: 5;
  /* animation-direction: normal; */
}
```

. . . can also be written as . . .

```css
.pencil {
  animation: drawALine 5s ease-in-out 100ms 5;
}
```

[Pick a card](https://estelle.github.io/cssmastery/animations/#slide56)

#### ‘animation-fill-mode’

values: none | forwards | backwards | both

```css
.pencil {
  animation-name: gettingThePencil;
  animation-duration: 3s;
  animation-timing-function: ease-in-out;
  animation-iteration-count: 1;
  animation-delay: 2s;
 animation-fill-mode: none;
}
```

```css
.pencil {
  animation: gettingThePencil 3s ease-in-out 1s  forwards;
}
```

### Stoping Animations & Events

#### ‘animation-play-state’

`animation-play-state: paused | running`

```css
.pencil {
  transform-origin: center 300px;
  animation: writingInCircles 3s linear infinite;
 }
.pencil:hover {
  animation-play-state:paused;
 }

@keyframes writingInCircles {
  100% {
    transform:
      rotate(360deg);
  }
}
```

What happens at animation end?

+ animationstart
+ animationend
+ animationiteration

```js
el.addEventListener( 'animationend',
    function( event ) {
        console.log('Animation Ended');
    }, false );
```

Starting next animation at end of previous

```js
el.addEventListener( 'animationend',
    function( event ) {
        el.removeClassName('newClass');
        setTimeout("el.addClassName('newClass')", 100ms)
    },
    false );
```

[Events: start, lots 'o iterations, end](https://estelle.github.io/cssmastery/animations/files/events.html)

## Other Features

### CSS Columns

```css
column-count: 1;
column-width: 10em;
column-rule: 1px solid #bbb;
column-gap: 2em;
```

#### Multi Column Layout Properties

```css
column-count:
column-width:
/* shorthand */
columns: <min-column-width> <max-column-count>

column-fill: auto | balance;
column-gap: normal | length;
column-span: none | all;

column-rule-color:
column-rule-style: <border-style>
column-rule-width:
/* shorthand */
column-rule:

break-after
break-before
break-inside

orphans
widows
```

### Box Sizing

`box-sizing: content-box | border-box`

border-box replicates the old IE box model

### Shaders & Blend Modes

#### Shaders

[CSS Filter Effects](http://html5-demos.appspot.com/static/css/filters/index.html)

#### background-blend-mode

```css
div {
  height: 500px; width: 500px;
  background-image: linear-gradient(to right, black 0%,white 100%),
    url(images/animatedheart.svg);
  background-blend-mode: color-dodge;
}
```

+ normal
+ multiply
+ screen
+ overlay
+ darken
+ lighten
+ color-dodge
+ color-burn
+ hard-light
+ soft-light
+ difference
+ exclusion
+ hue
+ saturation
+ color
+ luminosity

### CSS Shapes

[example1](https://estelle.github.io/cssmastery/other/#slide9)

```css
div {
  //clip-path: circle(30% at 48% 36%);
  //shape-outside: circle(30% at 48% 36%);  
  //clip-path: ellipse(40% 25% at 48% 27%);
  //shape-outside: ellipse(40% 25% at 48% 27%);
  clip-path: polygon(0 0, 50% 50%, 0% 100%);  
  shape-outside: polygon(0 0,50% 50%, 0% 100%);
}
```

[example2](https://estelle.github.io/cssmastery/other/#slide10)

```css
div {
   float: left;
  //clip-path: polygon(0 20%, 80% 20%, 80% 80%, 0 80%);
  //shape-outside: inset(20% 20% 20% 20%);
  clip-path: polygon(0 0, 60% 0, 100% 90%, 0 90%);
  shape-outside: polygon(0 0, 60% 0, 100% 90%, 0 90%);  
}
```

[CSS Shapes Editor](https://chrome.google.com/webstore/detail/css-shapes-editor/nenndldnbcncjmeacmnondmkkfedmgmp/related)

### Fonts, Icons & Subsetting

#### Material Design Icons

[icons](https://estelle.github.io/cssmastery/other/files/icons_list.html)

```css
[class|='material-icons'] {
  font-family: "Material Icons";
  font-size: 48px;
  word-wrap: normal;
  -webkit-font-feature-settings: 'liga';
  -webkit-font-smoothing: antialiased;
}
```

```html
<p class="material-icons">face<p>

<link href="https://fonts.googleapis.com/icon?family=Material+Icons"
      rel="stylesheet">
```

[font squirrel](https://www.fontsquirrel.com/)

#### Unicode Subset

```css
@font-face {
    font-family: 'myFont';
    src: url('myfont-webfont.woff2') format('woff2'),
         url('myfont-webfont.woff') format('woff');
    font-weight: normal;
    font-style: normal;
    unicode-range: U+26;
}
```

#### Subsets of Google Fonts

```css
p {
    font-family: 'Rye', sans-serif;
  }
```

```html
<link  rel="stylesheet"
href="//fonts.googleapis.com/
css?family=Rye:regular&text=AEIOUaeiou">
```

### CSS as the Solution

[example](http://estelle.github.io/input-masking/indexcss.html)

```css
/* additional styles */
li {line-height: 2; clear: both;}
label {display: inline-block; width: 200px;}
.shell span {color: pink;}
li {font-family: helvetica; font-size: 0.93rem;}
```

### Cursors

[example](https://estelle.github.io/cssmastery/other/#slide26)

### Text Overflow, Word Wrap & Calc

#### text-overflow: ellipsis

```css
p {
  text-overflow: ellipsis;

  white-space: nowrap;
  overflow: hidden;
}
```

#### word-wrap: breakword

`word-wrap:  normal | break-word`

#### calc()

```css
.colA {
  width:50%;
  margin-right: 1em;
}
.colB {
  width: calc(50% - 1em);
}
```

+, -, *, /, mod, min, and max operators.

### rem & Viewport Width

#### rem

```css
html {
    font-size: 62.5%;
}
p {
    font-size: 13px;
    font-size: 1.3rem;
}
small {
    font-size: 11px;
    font-size: 1.1rem;
}
```

#### viewport width

+ vw | vh | vmin | vmax
  5vw = 5% of the width of the viewport
  7vh = 7% of the height of the viewport
  4vmin = 4% of the viewport height or width, whichever is smaller
  8vmax = 8% of the viewport height or width, whichever is larger

+ under consideration
  vi
  1% of containing block, in the direction of the root element’s inline axis.

  vb
  1% of the containing block, in the direction of the root element’s block axis.

### Pointer Events & Content Editable

#### pointer-events: none

Can the element be clicked?!?!

```css
.animatedElement {
  pointer-events: none;
}
```

Did you note that you could change the transforms examples even when the example was covering the code?

#### -webkit-user-modify

Related to contentEditable: Determines whether content of an element is editable.
与contenttEditable相关:确定元素的内容是否可编辑。

```css
-webkit-user-modify: read-only | read-write | read-write-plaintext-only;
-moz-user-modify: read-only | read-write | write-only;
```

+ read-only
  The content is read-only.
+ read-write
  The content can be read and written.
+ write-only (moz only)
  The user is able to edit the content, but not to read it.
+ read-write-plaintext-only (webkit)
  The content can be read and written, but any rich formatting of pasted text is lost.

#### Links

[MDN: CSS Filters](https://developer.mozilla.org/en-US/docs/Web/CSS/filter)

[CSS-Tricks: background-blend-mode](https://css-tricks.com/almanac/properties/b/background-blend-mode/)

MDN: [shape-outside](https://developer.mozilla.org/en-US/docs/Web/CSS/shape-outside) and [clip-path](https://developer.mozilla.org/en-US/docs/Web/CSS/clip-path)