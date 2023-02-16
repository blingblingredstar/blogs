---
title: Sass Fundamentals
date: 2019-10-04 15:44:35
categories: [学习笔记]
tags: [FrontEndMasters, CSS, Sass, BEM]
---
## Sass Fundamentals

### Introduction

[GitHub Repo](https://github.com/mike-works/sass-fundamentals)

### CSS Pitfalls

+ No encapsulation
+ No variables
+ Not composable
+ Bad modularity primitives
+ Globals
+ Beating-into-submission-driven-development
<!-- more -->
### Preprocesser Benefits

+ Rise of the preprocessors
  + Compile to CSS
  + Parameterized(variables)
  + Composable
  + Modular
  + Plug in to your existing tools

+ Preprocessors: Why use one?
  + Add stuff that should have been there
  + Style is faster to build & easier to maintain
  + D.R.Y
  + It's easier to keep your styles organized
  + Easy to set up
  + Toolkits on top of preprocessors

### Course Agenda

+ Sass Origins & Basics
+ Variables & Mixins
+ Control flow
+ CSS Architecture
+ Defining your own Sass functions

## Sass Basics

### Syntax, Nesting & Selectors

+ Syntax

+ style.css

```scss
.foo {
  color: green;
  font-size: 1.3rem;
}
```

+ style.sass

```sass
.foo
  color: green
  font-size: 1.3rem
```

+ Terminology
  + selector
    + `.foo`
  + declaration block
    + `{}`
  + declaration
    + `color: green;`
  + property
    + `color`
  + value
    + `green`

+ Nesting & Scoping

```html
<div class="container">
  <div class="sidebar">Sidebar</div>
  <div class="main">Main Content</div>
</div>
```

```css
.container {
  max-width: 600px;
  width: 100%;
  margin: auto;
  background: #eee;
}

.container .sidebar,
.container .main {
  padding: 10px;
}

.container .main {
  margin-left: 220px;
  min-height: 100vh;
  border-left: 2px solid #333;
}

.container .sidebar {
  width: 200px;
  float: left;
}
```

+ Nesting & Scoping
  + Styles can be placed in the declaraton block of a parent element
  + Everything is expanded as you would expect during the Sass compilation process
  + This is already D.R.Y.'ed up quite a bit!

+ style.scss

```scss
.container {
  max-width: 600px;
  width: 100%;
  margin: auto;
  background: #eee;

  .sidebar,
  .main {
    padding: 10px;
  }

  .main {
    margin-left: 220px;
    min-height: 100vh;
    border-left: 2px solid #333;
  }

  .sidebar {
    width: 200px;
    float: left;
  }
}
```

+ Descentant

+ html

```html
<div class="container">
  <div class="left-area">...</div>
  <div class="other-thing">
    <div class="left-area"></div>
  </div>
</div>
```

+ Sass

```scss
.container {
  .left-area {
    /*...*/
  }
}
```

+ CSS

```css
.container .left-area {
  /* ... */
}
```

+ Direct Descentant(>)

```scss
.container {
  > .left-area {

  }
}
```

```css
.container > .left-area {

}
```

+ Parent Selector(&)
  + The parent selector is ofen useful in situations where adding a secondary class changes styles

```html
<div class="container right-nav"></div>
```

```scss
.container {
  &.right-nav {
    color: #333;
  }
}
```

```css
.container.right-nav {
  color: #333;
}
```

### Challenge: Nesting

`git clone git@github.com:mike-works/sass-fundamentals.git`

`cd sass-fundamentals`

`npm i`

`./run -e nesting`

+ All buttons must have 10px padding on left and right, 2px on top and bottom
+ All buttons should have a 1px #c46 solid border, with a 2px radius
+ Adding the .btn-primary class to a button should turn its text white and background #c46
+ Adding the .btn-secondary to a button should turn its background #edbcc8 and text black
+ Disabling a button should cause its opacity to be set to 0.5

### Challenge: Parent

+ Expand upon our sidebar example, so that adding the .right-nav class to the .container div can be used to toggle between left and right sidenav alignment
+ Ensure that your code passes the tests that are in place

`./run -e parent`

## Sass Variables

### Sass @import and Variables

+ Importing
  + Using an `@import` in CSS results in a new round-trip HTTP Request, this is a pref concern
  + The files our users download vs. files we use to manage source code are different concerns
  + JS Modules have taught us that modularity is a wonderful tool
  + `@import` in Sass does something else(something more useful)

+ Variables

```scss
$error_color: #f00 !default;

.alert-error {
  $text_color: #ddd;
  background-color: $error_color;
  color: $text_color;
  text-shadow: 0 0 2px darken($text_color, 40%);
}
```

+ Global variable declaration
  + `$error_color`
+ Value
  + `#f00`
+ Unless set else where
  + `!default`
+ Local variable declaration
  + `$text_color`

+ Variables
  + Simple arithmetic for values is ok
  + Sass can convert units, as long as dimension is the same
  + Any CSS value should be OK

+ Comments

```scss
/**
* will remain
*/
.foo {
  color: red; // will be removed
}
```

```css
/**
* will remain
*/
```

+ String Interpolation

```scss
/**
* Hue is #{hue(green)}
*/
```

```css
/**
* Hue is 120deg
*/
```

### Challenge: Imports and Variables

+ This looks like our first exercise
+ Except, there’s a _variables.scss
+ Primary button should have a $hopbush background and $venus border
+ Secondary button should have a $nebula background and $patina border

`./run -e variables`

## Sass Mixins

### Sass Mixins & Arguments

+ Mixins
  + Allow for re-use of style
  + Mental model: declaration block is merged, by way of @include
  + Best practice: separate from styles, like variables

```scss
@mixin alert-text {
  background-color: #f00;
  color: #fff;
  font-variant: small-caps;
}

.error-text {
  @include alert-text;
}

.has-error::after {
  @include alert-text;
  display: inline-block;
  content: attr(data-error)
}
```

+ Mixins: Arguments
  + Variables allow for parameterization

```scss
@mixin alert-text($color) {
  background-color: $color;
  color: #fff;
  font-variant: small-caps;
}

.error-text {
  @include alert-text(blue);
}

.has-error::after {
  @include alert-text(red);
  display: inline-block;
  content: attr(data-error);
}
```

### Challenge: Mixins

+ In your app.scss file, we’re making use of a new (currently empty) mixin
+ The goal is to define a means of defining the style of a button, given a color
+ Implement the mixin, such that all failing tests pass

`./run -e mixins`

### Default Argrment Values

+ Mixin - Default Argument Values
  + We can define default values for arguments
  + Arguments can be provided in order, or by name
  + When "keyword args" used, order is ignored

```scss
@mixin alert-text($color: #333) {
  background-color: $color;
  color: #fff;
  font-variant: small-caps;
}

h1 {
  @include alert-text(blue);
}

h2 {
  @include alert-text($color: green);
}
```

+ Maxins - Null Values

```scss
@mixin foo($opacity: null) {
  color: #333;
  opacity: $opacity;
}

.btn {
  @include foo();
}

.other-btn {
  @include foo(0.5);
}
```

+ compile to css

```css
.btn {
  color: #333;
}

.other-btn {
  color: #333;
  opacity: 0.5;
}
```

+ Mixins - Passing a Declaration Block

```scss
@mixin foo($color) {
  color: $color;
  .inner {
    @content
  }
}

.btn {
  @include foo(#c69) {
    color: #f00;
  }
}
```

+ compile to css

```css
.btn {
  color: #c69;
}

.btn .inner {
  color: #f00;
}
```

### Challenge: range

+ The range input can be styled, but it involves lots of ugly vendor-prefixing
+ This excellent tutorial from CSS tricks describes what needs to be done in CSS. Do it the Sass way!

`./run -e range`

## Sass Functions

### Introducing Sass Functions

[Built-In Functions](https://sass-lang.com/documentation/functions)

+ Color Functions
  + [document](https://sass-lang.com/documentation/functions/color)
  + `adjust-hue($color, $degrees);`
  + Rotates the hue of a color by a certain number of degrees

```scss
.foo {
  color: adjust-hue(#63f, 60deg);
}
```

+ Color Functions
  + `darken($color, $percent);`
  + `lighten($color, $percent);`
  + Change the brightness of a color by a certain amount

```scss
.foo {
  color: darken(#63f, 20%); // #30c
}
```

+ Color Functions
  + `desaturate($color, $percent);`
  + `saturate($color, $percent);`
  + Change the saturation of a color by a specified amount

```scss
.foo {
  color: desaturate(#f00, 75%); // #9f6060
}
```

### Challenge: Functions

+ We wish to apply some basic “theming” to a set of buttons

```html
<div class="theme-1">"""< Will be themed ""-!<div>
```

+ Theme mixin should take a $color as first argument, $huerot as second, and a $darkenpct as third
  + all buttons
    + border 20% darker color than background
    + hover background: 20% more saturated then 10% lightened
  + non-primary buttons
    + darkened by $darkenpct

`./run -e functions`

## Control Flow

### @if Sass Directive

```scss
@mixin foo($size) {
  font-size: $size;
  @if $size > 20 {
    line-height: $size;
  }
}

.small {
  @include foo(14px);
}

.large {
  @include foo(24px);
}
```

+ compile to css

```css
.small {
  font-size: 14px
}
.large {
  font-size: 24px;
  line-height: 24px;
}
```

### Challenge: Control Flow

+ Only use white button text if the brightness value of a button background color is less than 70%

`./run -e if`

## Data Structures

+ Control Flow - @for

```scss
@for $i from 1 through 5 {
  h#{$i} {
    font-size: 5rem - $i * 0.75rem;
  }
}
```

+ compile to css

```css
h1 { font-size: 4.25rem; }
h2 { font-size: 3.5rem; }
h3 { font-size: 2.75rem; }
h4 { font-size: 2rem; }
h5 { font-size: 1.25rem; }
```

+ Data Structures - Lists

```scss
$mylist: 0 0 2px #000;

.foo {
  /*
  shadow blur radius:
  #{nth($mylist, 3)}
  */
  box-shadow: $mylist;
}
```

```css
.foo {
  /*
  shadow blur radius:
  2px
  */
  box-shadow: 0 0 2px #000;
}
```

+ Data Structures - Lists and @each

```scss
$mylist: 0 0 2px #000;

.foo {
  @each $i in $mylist {
    /* #{$i} */
  }
}
```

```css
.foo {
  /* 0 */
  /* 0 */
  /* 2px */
  /* #000 */
}
```

+ Data Structures - nth

```scss
$gradients:
  (to left top, blue, red),
  (to left top, blue, yellow);

.foo {
  background: linear-gradient(nth($gradients, 2));
}
```

+ Data Structures - Maps

```scss
$mymap: (
  dark: #009,
  light: #66f
);

@mixin theme-button($key) {
  /* Theme: #{$key} */
  color: map-get($mymap, $key);
}

.btn-dark {
  @include theme-button('dark');
}

.btn-light {
  @include theme-button('light');
}
```

```css
.btn-dark {
  /* Theme: dark */
  color: #009;
}

.btn-light {
  /* Theme: light */
  color: #66f;
}
```

### Challenge: Nudging Classes

+ We're going to build a bunch of tiny css rules, each with exactly one style declaration
+ Using @for, @each and Sass data structures, build a set of styles in 5px increments, for both margin and padding, in each of the four directions(top, bottom, left and right)
+ BONUS: Try to use 15 lines of Sass or less!

`./run -e tiny`

## CSS Architecture

### BEM Introduction

+ Block -  standalone entity, meaningful on its own
  + header, container, menu, checkbox, input, button

+ Elements - A part of a block that has no standalone meaning, and is semantically tied to its block
  + menu-item, list-item, checkbox-caption, header-title

+ Modifier - A flag on a block or element, used to change appearance and/or behavior
  + disabled, highlighted, checked, size-big, color-yellow

```html
<div class="textfield">
  <label for="first-name" class="textfield__label">
    First Name
  </label>
  <input name="first-name" type="email" class="textfield__input" />
  <span class="textfield__validation-error">
    Must be two characters or longer
  </span>
</div>
```

```scss
.textfield {
  &__input {}
  &__label {}
  &__validation-error {}
}
```

```html
<div class="textfield textfield-state-validated">
</div>
```

```scss
.textfield {
  &.textfield-state-validated {}
  &.textfield-state-error {}
}
```

### Exercise: BEM Buttons

+ Build a "one-click checkout" button using BEM
+ Basic color requirements for buttons, same as exercise 1
+ Price should have bg of #080, but #aaa when button is disabled

`./run -e bem`

### Reusing Style with Mixins

+ Style reuse via Mixins

```scss
@mixin danger {
  background: red;
  color: white;
}

.btn-danger {
  @include danger();
  padding: 2px;
}

.alert-danger {
  @include danger();
  width: 100%;
}
```

```css
.btn-danger {
  background: red;
  color: white;
  padding: 2px;
}

.alert-danger {
  background: red;
  color: white;
  width: 100%;
}
```

+ @extend

```scss
.danger {
  background: red;
  color: white;
}

.btn-danger {
  @extend .danger;
  padding: 2px;
}

.alert-danger {
  @extend .danger;
  width: 100%;
}
```

```css
.danger,
.btn-danger,
.alert-danger {
  background: red;
  color: white;
}

.btn-danger {
  padding: 2px;
}

.alert-danger {
  width: 100%;
}
```

+ @extend - Placeholders

```scss
%danger {
  background: red;
  color: white;
}

.btn-danger {
  @extend %danger;
  padding: 2px;
}

.alert-danger {
  @extend %danger;
  width: 100%;
}
```

```css
.danger,
.alert-danger {
  background: red;
  color: white;
}

.btn-danger {
  padding: 2px;
}

.alert-danger {
  width: 100%;
}
```

### Challenge: @extend

+ Achieve the same outcome as exercise 1, except...
+ Do not change the appearance of the buttons at the bottom
+ Use @extend and a %placeholder

`./run -e extend`

## Working with Sass Functions

### Writing Sass Functions

+ Defining your own functions

```scss
$w: 2px;
@function double($x) {
  @return 2 * $x;
}

.thin-border {
  border-width: $w;
}

.thick-border {
  border-width: double($w);
}
```

```css
.thin-border {
  border-width: 2px;
}
.thick-border {
  border-width: 4px;
}
```

```scss
@function biggest_channel_only($color) {
  $r: red($color);
  $g: green($color);
  $b: blue($color);
  @if $r > $g and $r > $b {
    @return rgb($r, 0, 0);
  } @else if $g > $r and $g > $r {
    @return rgb(0, $g, 0);
  } @else {
    @return rgb(0, 0, $b);
  }
}
```

### Challenge: Relative Luminance in Sass

+ Exercise 6 involved using @if to ensure that the text/background color combination on a button was readable
+ This quite the right concept. What we really want is to base the label text color on a difference in “relative luminance” between the label and background colors
+ First, we’ll need to convert our RGB values from their current form (a gamma-compressed color space) to a linear color space.
+ A pow($base, $exp) function has been provided
+ Next, we’ll want to weigh these linear RGB values as follows
  + Y = 0.2126R + 0.7152G + 0.0722B
+ Finally, we’ll want to change the @if involved with determining button text color, so that instead of the existing comparison, it sets a white text color when the background color and the color white differ in luminance by more than 0.7

`./run -e luminance`
