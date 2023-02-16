---
title: Motion Design with CSS
date: 2019-09-20 13:28:30
categories: [学习笔记]
tags: [FrontEndMasters, CSS, Transitions, Animations]
---
## CSS Transitions

### Introduction

[web animation weekly](webanimationweekly.com)

+ Overview
  + CSS Transitions & Animations
  + How to use animation in product design
  + How to create stateful animations with JavaScript
  + How to use developer tools to manipulate animations
  + How to design performent animations
<!-- markdownlint-disable md024 -->
<!-- more -->
### CSS Transitions

+ A transition describes how a property should display change when given a different value
  + `transition: color 2s;`
  + 以上两个为必要参数，还有其他若干可选参数
  + [demo](http://cdpn.io/oXgqWy)
  + 也可以分开写
    + `transition-property: color;`
    + `transition-duration: 2s;`

+ Anatomy of a transition
  + `transition-property` the property you want to transition.(*Only some property* are transitionable, see [goo.gl/Ttk1S2](https://www.w3.org/TR/css-transitions-1/#animatable-properties-))
  + `transition-duration` in seconds or milliseconds: 4s or 4000ms(更推荐毫秒值)
  + `transition-timing-function` "cushioning" for the transition, **optional**: defaults to ease
  + `transition-delay` the number of milli/seconds to delay the transition before firing it, **optional**

### Exercise: Rolling a Ball

[exercise link](https://codepen.io/blingblingredstar/pen/WNeKgKO)

### Transitioning Multiple Properties

+ 不推荐使用`transition-property: all`
+ 使用`transition-property: color, transform;`
+ 并且可以使用逗号来分别给`transition-duration`和`transition-delay`来给每个动画元素指定持续时间和延迟时间
+ 推荐使用`transition: color 2s, transform 300ms 1s;`来合并书写

### Demonstration: Changing the Ball's Color

```css
.ball {
  transition:
    transform 1s .25s ease-in-out,
    background-color 1s .25s;
}
```

### Duration

+ Three timeframes for user attention
  1. 100ms, instantaneous
  2. 1 second, still connected
  3. 10 seconds. disconnected

> "However long your pre-production animation, halve its duration... then halve it again." - Studio animation rule of thumb

+ 250 ~ 300ms: sweet spot for many animations

+ faster !== better

### Browser Developer Tools

+ You can slow down transitions with Dev Tools to get a good look at them
  + chrome在开发者工具中elements按下`esc`来调出console面板，在tab栏中添加animation面板即可

### Timing Functions

+ Easing
  + Easing, also known as "cushioning" in studio animation, describes an animation's rate of change over time

+ Timing Function Values
  + linear
  + ease-in
  + ease-out
  + ease-in-out
  + steps(more on this later)

> Repalce "ease" with "slow" to better remember the different timing effects.

+ What you really want: cubic-bezier
  + Make your own at [cubic-bezier.com](https://cubic-bezier.com/) or edit with the Chorme/Firefox Dev Tools!

### Exercise: Applying Physics to the Ball

### CSS Transitions Summary

+ CSS Transitions rock beaucase...
  + **Single fire** If you only want something to happen once
  + **Granularity** If you would only animate one or two properties in a given state
  + **Bulletproof** If transitions aren't supported, the change happens anyway

## CSS Animations

```css
.animated-thing {
  animation: black-to-white 1s linear 1;
}

@keyframes black-to-white {
  0% { background: #000; }
  100% { background: #fff; }
}
```

```css
.animated-thing {
  animation:
    $name
    $duration
    $timing-function(optional)
    $animation-delay(optional)
    $iteration-count(optional);
}
```

+ Long form animation properties
  + animation-name: The name of the keyframe block you want to ues
  + animation-duration: How long the animation takes to go from 0% to 100%
  + animation-iteration-count: The number of times you want to go from 0% to 100%; use **infinite** to never stop. Defaults to 1
  + animation-timing-function: Like transition-timing-function
  + animation-delay: Like transition-delay

+ The @keyframes block

```css
@keyframes black-to-white {
  0% {
    background: #000;
    color: #fff;
  }
  100% {
    background: #fff;
    color: #000;
  }
}
```

```css
@keyframes black-to-white {
  from {
    background: #000;
    color: #fff;
  }
  to {
    background: #fff;
    color: #000;
  }
}
```

```css
@keyframes black-to-white {
  0%, 100% {
    background: #000;
    color: #fff;
  }
  50% {
    background: #fff;
    color: #000;
  }
}
```

```css
.animated-thing {
  animation:
    black-to-white 1s linear 1,
    black-to-white 2s ease-out infinite 2s;
}
```

### Sprite Animation with CSS

+ Meet steps()
  + steps(x) is a timing function...
  + ...splits a block of keyframes into x equal steps, then hops between them
  + How it works, plus gotchas: [cdpn.io/zeFqy](https://codepen.io/rachelnabors/full/zeFqy)

### Exercise: Making a Walking-Cycle

[exercise link](https://codepen.io/blingblingredstar/pen/dybgGYK)

### Advanced Animation Properties

+ Long form advanced animation properties
  + animation-fill-mode: can be **backwards**(the element displays 0% values before the animation starts). Can be set to **forwards**(retains 100% values after animation finishes), **both**, or **none**(default)
  + animation-play-state: defaults to **running** but can be set to **paused**
  + animation-direction: defaults to normal but can be set to **alternate**, **reverse**, **alternate-reverse**

### Exercise: Wag the Cat

[exercise link](https://codepen.io/blingblingredstar/pen/RwberMm?editors=0100)

### CSS Animations Summary

+ CSS Animations rock beaucase...
  + **Looping** Can loop infinitely
  + **Self starting** Doesn't require trigger like transition
  + **Repeating** You can set how many time it repeats
  + **Alternating** Can alternate between the end state and start state
  + **Grouping** Each animation can change a number of properties

## Animation in Design

### Stateful Transitions & Supplemental Animations

+ Stateful Transitions are useful for indicating...
  + Change in task flow location
  + Where you've been

+ Supplemental Animations are useful for indicating...
  + Change in information
  + What is possible for users
  + Important details that shouldn't be overlooked

### Causal Effects and Decorative Animations

### Jump Cuts and in-Betweening

+ Jump Cut
  + From film, when the camera cuts directly to a different perspective

+ In-Betweening
  + From animation, drawing all the poses "in between" two important poses

> "...Aniamtin allows the user to continue thinking about the task domain, with no need to shift contexts to the interface domain. By eliminating sudden visual changes, animation lessens the chance that the user is suiprised." - Scott E.Hudson and John T.Stasko(1993)

+ Jank destroys the precious illusion of life

### Drawing Attention

> "These speeded responses appeared to be due to the perceived animacy of the objects... We conclude that animate motion does indeed capture visual attention." - It's Alive! Animation Motion Captures Attention(2010)

+ Animacy
  + How "alive" something appears to be

+ If you want someone to notice something, make it bright and jiggly

+ When everything vies for our attention, we don't know what to focus on

### Animation Design Summary

## Stateful Animations

### Static vs. Dynamic Animations

+ Static Animations
  + Start => Finish

+ Stateful Animations
  + Default State => Event => Predefined State

+ Dynamic Animations
  + Current State => Event + Factors => New State

+ Ways of triggering states
  + Browser events
    + Loading
    + Scrolling
  + Human events
    + Hovering
    + Clicking
  + Timed events
    + Timeouts
    + Choreography

### Exercise: Sensing Visual Play Readiness

[exercise link](https://codepen.io/blingblingredstar/pen/eYOPaWK)

### Parallex Libraries

+ **Skrollr** creates dynamic animations by interpolating property values based on scroll position
+ **Waypoints** triggers events based on where the person has scrolled to

### Exercise: Falling up the Hole

[exercise link](https://codepen.io/blingblingredstar/pen/GRKYbJE)

### Managing State

+ What's good for the Declarative CSS Goose is not so great for the Functional JS Gander

+ Libraries for managing state
  + JavaScript Finite State Machine
    + linear state transitions easily
    + many-to-many states gets complicated
  + jStorage
    + stashes values in localStorage
    + great for storing state
    + library agnostic

### Sequencing

```css
.sequenced-thing {
  animation:
    sequence1 1s 2,
    sequence2 3s 2s infinite;
}
```

### Exercise 7: Sitting Tuna Down

[exercise link](https://codepen.io/blingblingredstar/pen/bGbQNMN)

### Chaining Animations with Event Listeners

+ But stacking isn't the only way, nor the most reliable

+ CSS animations and transitions run on the system's clock

+ Downsides...
  + **No looping** segments
  + **Only one** timing function at a time for all animating properties
  + **Mathy** you have to do a bit of math to get the timing right

+ We're gonna need a more robust technique
  + Chaining Animations with Event Listeners

+ Four must-have JavaScript event listeners
  1. animationstart
  2. animationend
  3. animationiteration
  4. transitionend

### Exercise: Sitting Tuna Down with Event Listeners

[exercise link](https://codepen.io/blingblingredstar/pen/xxKQbvJ)

### Stateful Animations Summary

## Performance

### CSS Triggers

[CSS Triggers](https://csstriggers.com/)

+ The two CSS properties considered most performant:
  + opacity
  + transform

+ Handy alternatives
  + **transform**: **scale()** instead of **width** or **height**
  + **transform**: **translate()** instead of **position**
  + **opacity**: instead of **z-index** or **visiblility: hidden**

### Exercise: Walk the Cat... Again

+ Find a way to walk the cat without using expensive background positioning

[exercise link](https://codepen.io/blingblingredstar/pen/ZEzmvEG)

### will-change Property

+ Hardware acceleration
  + AKA "kicking it to the GPU"

```css
.resource-sink {
  transform: translateZ(0)
}
```

+ Please don't do this

+ Hacks are not future-friendly

```css
.in-view .animated-thing {
  will-change: transform;
}

.in-view .animated-thing:hover {
  transform: translateY(5%);
}
```

+ Don't slap `will-change` on everything
  + When we `will-change` everything, we optimize nothing

### Frames Per Second

+ Frames Per Second(FPS)
  + Frame Rate on the computer is not the same as it is in the movie theatre
  + Optimal frame rates to maintain the "illusion of life":
    + Movies: 24FPS
    + Computer screens: 60FPS

+ Consistency is more important than FPS

+ Chrome's FPS Meter
  + Open your Dev Tools
  + Press the Escape Key to bring up the console window
  + Choose the Rendering tab
  + Check the Show FPS Meter option

### Paint Flasing and the Timeline

+ Paint Flasing
  + To enable Chrome's paint rectangles:

1. Open your Dev Tools
2. Press the Escape Key to bring up a console window
3. Choose the Rendering tab
4. Check Enable Paint Flasing

+ The Timeline Tool
  + To use Chome's timeline tool:

1. Open your Dev Tools
2. Go to the Timeline tab
3. Press the "record circle"
4. Do stuff
5. Press the "record circle" again to stop
6. Inspect

+ Best perf tip ever: Don't animate it if it ins't visible
