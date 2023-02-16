---
title: SVG Essentials & Animation v2
date: 2019-09-11 15:54:02
categories: [学习笔记]
tags: [FrontEndMasters, CSS, SVG, GSAP, Animation]
---
## Introduction

[Sarah Drasner CodePen](https://codepen.io/sdras/)

[workshop github repo](https://github.com/sdras/svg-workshop)

+ Suggestions for getting the most out of this course
  + Do exercise!

[chrome codepen plugin](https://chrome.google.com/webstore/detail/codopen/agnkphdgffianchpipdbkeaclfbobaak/)

[GreenSock](https://codepen.io/GreenSock/pen/OPqpRJ)
<!-- more -->
## SVG Anatomy Overview

### What is SVG(Scalable Vector Graphics)

1. Crisp on any display
2. Less HTTP requests to handle
3. Easily scalable for responsive
4. Small filesize if you design for performence
5. Easy to animate
6. Easy to make accessible
7. Fun!

### SVG Support & Performance

[codepen demo](https://codepen.io/sdras/pen/NqYGZv)

[smashing magazine](https://www.smashingmagazine.com/)

### The Antidote to Positioning in CSS

[codepen demo](https://codepen.io/chriscoyier/pen/YyxKea)

### Platonic Shapes

+ Overview of the SVG DOM
  + platonic shapes
  + viewbox
  + preserveAspectRatio
  + grouping
  + grawing paths
  + SVG on export

```html
<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" width="450px" height="100px" viewBox="0 0 450 100">
 <rect x="10" y="5" fill="white" stroke="black" width="90" height="90"/>
 <circle fill="white" stroke="black" cx="170" cy="50" r="45"/>
 <polygon fill="white" stroke="black" points="279,5 294,35 328,40 303,62 309,94 279,79 248,94 254,62 230,39 263,35 "/>
 <line fill="none" stroke="black" x1="410" y1="95" x2="440" y2="6"/>
 <line fill="none" stroke="black" x1="360" y1="6" x2="360" y2="95"/>
</svg>
```

在上面的代码中，使用x,y,width,heigth来定义长方形，使用cx,xy,height来定义原型，使用多个点来定义多边形，使用起点和终点定义直线。

### ViewBox & Responsive

viewbox类似画板，可以通过css来设定宽高，并且只显示画板内的内容。

[codepen demo](https://codepen.io/sdras/pen/XJKJZL)

### preserveAspectRatio

基本不需要使用。

+ Default: `preserveAspectRatio=”xMidYMid meet”`
  + uniformly scales, like `background-size: cover;` in CSS
+ MEET (DEFAULT)
  + entire viewBox is visible within viewport
  + the viewbox is scaled up as much as possible, meeting other criteria
  + viewBox < viewport
+ SLICE
  + entire viewport is covered by the viewBox
  + the viewBox is scaled down as much as possible, meeting other criteria
  + viewBox > viewport

+ Use it for layout
  + `perserveAspectRatio = "none"`

[CodePen demo](https://s.codepen.io/sdras/debug/XdgBOa)

### Paths, Groups & Polylines

```html
<svg viewBox="0 0 218.8 87.1">
  <g fill="none" stroke="#000">
    <path d="M7.3 75L25.9 6.8s58.4-6.4 33.5 13-41.1 32.8-11.2 30.8h15.9v5.5s42.6 18.8 0 20.6" />
    <path d="M133.1 58.2s12.7-69.2 24.4-47.5c0 0 4.1 8.6 9.5.9 0 0 5-10 10.4.9 0 0 12.2
32.6 13.6 43 0 0 39.8 5.4 15.8 15.4-13.2 5.5-53.8 13.1-77.4 5.9.1 0-51.9-15.4 3.7-18.6z" />
  </g>
</svg>
```

`<g>`标签代表组(group)。

如果想要闭合一个`<path>`，可以直接在最后添加一个`z`来连接最后一个坐标与第一个坐标。`d`代表drawing，引号后的大写`M`代表move to。如果路径突然间混乱，很可能就是不小心删除了`M`。

使用`<g>`标签，类似illustrator中的编组，可以批量添加classes，同时作用于多个路径。

```html
<polyline points="14,17 136,37 77,117 230,87 132,158 172,158 "/>
```

polyline就是折线。可以用来做简单的数据展示（折线图）。

```html
<path d="M7.3 75L25.9 6.8s58.4-6.4 33.5 13-41.1 32.8-11.2
 30.8h15.9v5.5s42.6 18.8 0 20.6" />
<path d="M133.1 58.2s12.7-69.2 24.4-47.5c0 0 4.1 8.6 9.5.9
 0 0 5-10 10.4.9 0 0 12.2 32.6 13.6 43 0 0 39.8 5.4 15.8
 15.4-13.2 5.5-53.8 13.1-77.4 5.9.1 0-51.9-15.4 3.7-18.6z" />
```

[Demo exploring paths](http://codepen.io/netsi1964/full/pJzWoz)

类似钢笔工具

### Animated Bezier Curves & Template Literals

[Animated Bézier Curves](https://www.jasondavies.com/animated-bezier/)

```js
function plotter(points, startX) {
  var pathArr = [],
      pathpoints = [];
  for (i = 0; i <= inc; i++) {
    pathpoints.push(points + ((i * (points*2)) + points));
    pathArr.push(` ${startX} ${pathpoints[i]}`);
  }
  return pathArr;
}

rope1.setAttribute("d", `M ${plotter(50, start1)}`.join(" L") );
```

使用模板字符串来更改SVG路径

### Accessibility

```html
<svg aria-labelledby="title"
  id="svg"
  role="presentation"
  xmlns="http://www.w3.org/2000/svg"
  viewBox="0 0 765 587">
<title id="title"
  lang="en">
  Icons that illustrate Global Warming Solutions
</title>
```

Title for elements in the SVG DOM

Role to let the screen reader know whether to traverse

[This resource](https://developer.paciellogroup.com/blog/2013/12/using-aria-enhance-svg-accessibility/), with support charts.

[This article](https://css-tricks.com/accessible-svgs/) by Heather Migliorisi.

## CSS Animation

### Optimizing & Building

可以在illustrator中减少锚点数量来减少文件体积。

+ [SVGOMG](https://jakearchibald.github.io/svgomg/)
+ [Peter Collingridge's SVG Editor](http://petercollingridge.appspot.com/svg-editor)
+ [SVGO](https://github.com/svg/svgo) / [SVGO-GUI](https://github.com/svg/svgo-gui)

### Consturcting an SVG

使用illustrator的导出，来导出SVG（sketch导出的文件优化较差）。

### Starting an SVG

+ Main principle:
  + Design everything first, slowly unveil things
  + Ugly storyboards save you time!

### Prototyping

+ Thumbnails
  + Notes for yourself
+ Storyboards
  + Notes for collaboration
+ Low Fidelity Prototypes
  + Show the motion
+ Low Fidelity Prototypes
  + Screenshot to build on top of

### Planning an Animation Exercise

### Planning an Animation Review

### Animation Support Breakdown

+ For img src, object, embed, background url, and iframe, you can only designate animation if inside the SVG
+ But for **inline**, both SVG animation and interaction are supported

+ Animating with CSS, refresher

+ keyframes

```css
@keyframes animation-name-you-pick {
  0% {
    background: blue;
    transform: translateX(0);
  }
  50% {
    background: purple;
    transform: translateX(50px);
  }
  100% {
    background: red;
    transform: translateX(100px);
  }
}
```

+ Animation Properties-Shorthand

```css
.ball {
  animation: animation-name-you-pick 2s 2s 3 alternate ease-in-out forwards;
}
```

### Differences in Functionality

+ So, what happens when we apply that on the SVG?

可以看到SVG元素被添加了背景色。

+ Why?
  + moving the whole SVG instead of the circle(not a problem here)
  + `background` dosen't work for SVG, we use `fill`
  + Apply the fill to the cicle, not whole SVG

但是当我们把动画添加到`circle`上时，会发现`circle`移出了viewBox，所以我们需要增加viewBox的宽度来显示`circle`

最终如下

```html
<svg width="200px" height="70px" viewBox="0 0 200 70">
  <circle class="ball3" cx="45" cy="45" r="25" />
</svg>
```

```css
@keyframes second-animation {
  0% {
    fill: blue;
    transform: translateX(0);
  }
  50% {
    fill: purple;
    transform: translateX(50px);
  }
  100% {
    fill: red;
    transform: translateX(100px);
  }
}
```

### Why SVG

我们可以通过一些方法来使div看起来像一个三角形或者圆形，但这是一种hack，而svg被设计用来就是做矢量图形的，更加直观，更加可控。

### Transform-Origin

变形的中心点，推荐使用百分数作为值

### Animate an SVG Exercise

### SVG Tools and Ideas

[inkscape](https://inkscape.org/)

### Sprites

可以通过移动viewBox来制作SVG雪碧图。

+ Sprite Technique: Rolling Sprite Background

[codepen demo](https://codepen.io/sdras/full/xbyopy)

上述是通过媒体查询来更改背景颜色，减少图片细节

### Atmospheric and Elemental Motion

+ Elemental Motion
  + Futher away is less contrast, blurry
  + Does the air or environment effect movement
  + Reducing precision allows for understanding
  + Combine techniques

## GreenSock

### Tools Overview

| DOM/Virtual DOM                                             | canvas                                            |
| ----------------------------------------------------------- | ------------------------------------------------- |
| Pros                                                        | Pros                                              |
| Great for UI/UX animation                                   | Dance, pixels, dance!                             |
| Great for SVG that is resolution indenpendent               | Great for really impressive 3d or immersive stuff |
| Easier to debug                                             | Movement of a tons of objects                     |
| Cons                                                        | Cons                                              |
| Tanks with a lot of objects                                 | Harder to make accessible                         |
| Beaucase of this you have to care about the way you animate | Not resolution independent out of the box         |
|                                                             | Breaks to nothing                                 |

+ Correct Tools for the job
  + My recommendations

| css/scss                                | GASP(GreenSock)                           | React Spring/React-Motion                                    |
| --------------------------------------- | ----------------------------------------- | ------------------------------------------------------------ |
| Small sequences and simple interactions | Great for sequencing and complex movement | Great for single movements that you'd like to look realistic |
| Once you get more than 3...switch to:   | Corss-browser consistency                 |                                                              |

+ Snap.svg is more like jQuery for SVG
+ AnimeJS is more like GSAP! But less plugins
+ Web Animations API looks great, still waiting on support
+ Velocity is similar to GSAP with less bells and whistles
+ Mo.js won't come out of beta
+ D3.js was built for data vis but you can do a lot more with it

### Performance & GSAP

+ Not all are created equal
  + Opacity
  + Transforms
  + Hardware Acceleration

```css
@mixin accelerate() {
 transform: translateZ(0);
 backface-visibility: hidden;
 perspective: 1000px;
}

.foo {
  @include accelerate();
}
```

+ Resources
  + [Debugging Keyframe Animations](https://css-tricks.com/debugging-css-keyframe-animations/)
  + [High Performance Animations](https://www.html5rocks.com/en/tutorials/speed/high-performance-animations/)
  + [Performant CSS Animations: Netflix Case Study](http://eng.wealthfront.com/2015/06/30/implementing-netflix-redesign/)

+ GreenSock Killer Feature
  + Simple syntax
  + Timelines
  + Nested Timelines
  + Draggable
  + DrawSVG
  + Stagger
  + MorphSVG

[CodePen demo](https://codepen.io/sdras/full/JobJMO)

+ SOLVES CROSS-BROWSER INCONSISTENCIES
  + Historically bad transform origin bug on rotation in CSS,
  + now about to be resolved in Firefox.

+ SOLVES WEIRD SPEC ISSUES

### TweenMax Syntax

+ Syntax

```js
TweenMax.to/.from/.fromTo(element, duration{
  property: amount
})
```

```js
TweenMax.to('div', 2, {
  scaleY: .75, scaleX: 1.25, y: 100, opacity: .75, ease: Elastic.easeOut
})
```

[gsap-cheatsheet](https://github.com/sdras/svg-workshop/blob/master/gsap-cheatsheet.js)

### Stagger

```css
@keyframes staggerFoo {
  to {
    background: orange;
    transform: rotate(90deg);
 }
}

.css .bar:nth-child(1) { animation: staggerFoo 1s 0.1s ease-out both; }
.css .bar:nth-child(2) { animation: staggerFoo 1s 0.2s ease-out both; }
.css .bar:nth-child(3) { animation: staggerFoo 1s 0.3s ease-out both; }
.css .bar:nth-child(4) { animation: staggerFoo 1s 0.4s ease-out both; }
.css .bar:nth-child(5) { animation: staggerFoo 1s 0.5s ease-out both; }
.css .bar:nth-child(6) { animation: staggerFoo 1s 0.5s ease-out both; }
```

```scss
@keyframes staggerFoo {
  to {
    background: orange;
    transform: rotate(90deg);
 }
}

@for $i from 1 through 6 {
  .sass .bar:nth-child(#{$i} ) {
      animation: staggerFoo 1s ($i * 0.1s) ease-out both;
   }
 }
```

```js
TweenMax.staggerTo('.gsap .bar', 1, {
  backgroundColor: 'orange',
  rotation: 90,
  ease: Sine.easeOut
}, 0.1);
```

上面三个代码块分别为用css、sass和gsap实现同一个动画。可见使用gsap更简洁明了。

+ STAGGER
  + Things like stagger are really complicated to do in CSS animation, and in GSAP are one line of code.

```js
TweenMax.staggerTo(".squares", 2, {
  y:100, backgroundColor:"#4f9d88", ease:Elastic.easeOut
}, 0.05);
TweenMax.staggerTo(".squares", 2, {
  rotation:200, delay:1, scale:0.5, backgroundColor:"#72b165", ease:Elastic.easeOut
}, 0.025);
```

### GSAP Monster Demo

[gsap demo](https://codepen.io/sdras/full/Wramvo)

### Cycle Stagger

[GSAP + SVG Article](https://davidwalsh.name/gsap-features)

### Setting CSS Properties

```js
TweenMax.set('.squares', {
  transformPerspective:200, perspective:200, transformStyle:"preserve-3d"
});

TweenMax.to('.squares', 2.5, {
  rotationX:230, z:-150, y:180, opacity:0.2, ease:Power2.easeOut
}, "+=0.2");
```

### Comments on D3

使用D3来创建DOM，使用gsap来创建动画。

### Timeline

+ Timeline
  + stack tweens
  + set them a little before and after one another
  + change their placement in time
  + group them into scenes
  + add relative labels
  + animate the scenes
  + make the whole thing faster, move the placement of the whole scene, nesting

+ Position Parameter

```js
var tl = new TimelineLite()
tl
  .to('.orange', 1, {x: 750})
  // this just follows the first
  .to('.green', 1, {x: 750})
  // there is a one second gap between these two tweens
  .to('.blue', 1, {x: 750}, '+=1')
  // this goes to two seconds in
  .to('.red', 1, {x: 750}, '2')
  // add a relative label at this part of the timeline
  .add('newLabel')
  // tween at 3 seconds past the relative label
  .to('purple', 1, {x: 750}, 'newLabel+=3')
```

+ Simple Timeline

```js
var tl = new TimelineMax()

tl.to(el, 3, {
  fill: 'white',
  opacity: .3,
  ease: Elastic.easeOut
})

tl.to(el2, 3, {
  fill: 'orange',
  ease: Sine.easeOut
})
```

+ NESTING TIMELINES

```js
//set properties needed for animation outside
TweenMax.set(el, {
  perspective: 400
});

// the first scene
function sceneOne() {
  var tl = new TimelineMax();

  tl.to(el, 3, {
    fill: 'white',
    opacity: 0.3,
    ease: Elastic.easeOut
  });

  return tl;
}

// Create a master timeline
var master = new TimelineMax({options});
// Add the scene function to the master
master.add(sceneOne(), "labelOnMaster");

//use this while you're working to get to a place in time
//master.seek("labelOnMaster+=2");
```

[codepen demo](https://codepen.io/sdras/pen/ByEWON)

+ No Momentary Display

```css
/* set to hide in css */
.foo {
  visibility: hidden;
}
```

```js
// set back in js
TweenMax.set('.foo', {
  visibility: 'visible'
})
```

+ Percentage based transforms on SVG

```js
tl.staggerTo('.box', 0.5, {x: '100%'}, 0.4)
```

### GSAP Demo

```html
<div class="newbox"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
<div class="box"></div>
```

```css
body {
  background: teal;
  height: 100vh;
  display: flex;
  justify-content: center;
  overflow: hidden;
}

.box {
  background: indigo;
  width: 50px;
  height: 50px;
  margin: 100px 30px;
}

.newbox {
  background: red;
  width: 50px;
  height: 50px;
  margin: 100px 30px;
}
```

```js
var tl = new TimelineMax(),
    box = document.querySelectorAll(".box");

tl.staggerFrom(box, 5, {
  y: -300,
  ease: Bounce.easeOut
}, 0.05)

tl.add('rotate')
tl.staggerFrom(box, 2, {
  rotation: 90,
  ease: Bounce.easeIn
}, 0.05, 'rotate')
tl.staggerFrom(box, 1, {
  rotation: 180,
  ease: Bounce.easeOut
}, 0.05, 'rotate+=1.75')

tl.from('.newbox', 2, {
  rotation: 90,
  ease: Bounce.easeIn
}, 'rotate')
tl.from('.newbox', 1, {
  rotation: 180,
  ease: Bounce.easeOut
}, 'rotate+=1.75')
```

## UI/UX Animation

+ UI/UX ANIMATION VS. STANDALONE + INTERACTION

ANIMATED ICONS THIS [REPO](https://github.com/sdras/vue-sample-svg-icons)

[codepen demo](https://codepen.io/sdras/pen/Kwjyzo)

+ UI/UX ANIMATION
  + Pieces of the UI move to aid in an informative UX choreography. This can typically be done with well placed CSS, some JS to trigger interactions. Responsive can be achieved with **good CSS media queries**

+ UI/UX ANIMATION: CONTEXT-SHIFTING

[demo](https://cssanimation.rocks/list-items/)

[navigation example](https://www.concrete-matter.com/)

+ STANDALONE
  + Used to illustrate concepts in the body of the page, either alongside or on it's own. Most of the basis of this talk is complex standalone animation. JavaScript is recommended for much longer implementations (explained later).

ui/ux动效更多的是为了视觉化用户操作，给用户一个视觉化的反馈，不应喧宾夺主，过渡吸引用户注意。单独的动画目的就是吸引用户注意力。

[联系界面demo](https://codepen.io/sdras/full/LEorev)

### Social Engineering with Animation

+ Anticipatory Cues
  + Custom Loaders
  + [Viget did an experiment](https://www.viget.com/articles/experiments-in-loading-how-long-will-you-wait/) and found that despite some individual variation, novel loaders as a whole had a higher wait time and lower abandon rate than generic ones

可以看到一个良好的页面等待动效会增加用户的留存时间。

+ STANDALONE SVG AS AN ILLUSTRATION OF TEXT
  + THE [POOR-MAN VERSION](http://codepen.io/sdras/full/7f31b37ae59639b3f7f66b76bd6f010d)
  + Very easy to implement. SVG stays a consistent size

### Context Switching

+ UI/UX ANIMATION WITH INTERACTION HELPS WITH [CONTEXT-SHIFTING](https://css-tricks.com/the-importance-of-context-shifting-in-ux-patterns/)
  + [CodePen demo](https://codepen.io/sdras/full/qOdwdB)

+ UI/UX ANIMATION: [CONTEXT-SHIFTING](http://codepen.io/sdras/full/qOdWEP)

### Improving an Existing UI Demo

+ Apply this to realy dev

[demo](https://codepen.io/sdras/full/amJLxN)

### Interaction, JS Detection & Scaling

可以使用js来监听动画

+ HOOK INTO
  + animationstart
  + animationiteration
  + animationend

### Interaction & GSAP Timeline Functions

+ USEFUL FUNCTIONS FOR INTERACTION + GSAP TIMELINE

```js
tl.pause() // Pause timeline
tl.resume() // Continue playback
tl.restart() // Restart the timeline
tl.play(X) // Play from Xs
tl.play(-X) // Play Xs from end
tl.seek(X) // Go to Xs or 'label'
tl.reverse() // Reverse playback anytime
tl.timeScale(x) // Speed up/slow down timeline
tl.progress(0.5) // Skip to halfway
```

[cheatsheet](https://github.com/sdras/svg-workshop/blob/master/gsap-basic-timeline.js)

### Interaction Demo

+ [Interaction plus response](https://codepen.io/sdras/full/waXKPw)

### Graggable

```js
Draggable.create(".box", {type:"x,y", edgeResistance:0.65, bounds:"#container", throwProps:true});
```

+ Device-enabled for touchscreen
+ Impose bounds- containing units or pixel parameters bounds:{top:100, left:0, width:1000, height:800}
+ Momentum: If you have ThrowPropsPlugin you can set throwProps:true
+ Draggable.hitTest() to sense if elements touch eachother (more on this in a moment)
+ Honors transform-origin
+ Still works on transformed elements
+ Lock movement to an axis lockAxis:true
+ GPU-accelerated and requestAnimationFrame-driven
+ [more](https://greensock.com/draggable)

+ RICH CALLBACK SYSTEM AND EVENT DISPATCHING
  + onPress
  + onDragStart
  + onDrag
  + onDragEnd
  + onRelease
  + onLockAxis
  + onClick

+ "this" refers to the Draggable instance itself, so you can easily access its "target" or bounds.
+ If you prefer event listeners, Draggable dispatches events:
  + `yourDraggable.addEventListener("dragend", yourFunc);`

## GSAP Extras

### DrwaSVG

[document](https://greensock.com/drawSVG)

### Motion Along a Path

+ This is one of the coolest things about SMIL, but the promise of support has a longer tail with GreenSock
  + Backwards compatibility and cross browser even IE
  + SMIL motion along a path will probably continue to be unsupported in IE, but support for this future will move into CSS. However, this is down the line. In the meantime, use GSAP for the widest support

### Curviness & Rotation

+ MOTION ALONG A PATH: CURVINESS
  + Can just use paths as general coordinates and smooth out the motion between
  + Either set the type parameter to soft
  + Or for more control set the type to thru (this is the default), and define a curviness value. 0 defines no curviness, 1 is normal, 2 is twice as curvy, etc

### Animating Text

+ SPLIT TEXT
  + Plugin- no dependencies, even on TweenMax
  + Support Back to IE8
  + Breaks into characters, words or lines
  + Honors natural line breaks
  + Option to create auto-incrementing classes, i.e. .char1, .char2, .char3

### Relative Color Values

+ HOW WOULD WE TURN A MANY ELEMENT SCENE FROM DAY TO NIGHT?
  + HSL RELATIVE COLOR
    + Hue
    + Saturation
    + Lightness

### MorphSVG

[GreenSocks documents](https://greensock.com/morphSVG)

### Bonus Demos

[Blake Bowen](https://codepen.io/osublake/)

[demo](https://codepen.io/sdras/full/EVRJqg)

[demo](https://s.codepen.io/sdras/debug/VpYeNj)

[Sound, Interaction, Animation](https://codepen.io/sdras/full/zvXbGJ/)

## Advanced SVGs

### Clipping & Masking

[Masking vs. Clipping: When to Use Each](https://css-tricks.com/masking-vs-clipping-use/)

### viewBox

### svgOrigin
