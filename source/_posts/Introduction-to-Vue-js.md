---
title: Introduction to Vue.js
date: 2019-10-14 14:44:18
categories: [学习笔记]
tags: [FrontEndMasters, JavaScript, Vue, Vuex, Nuxt]
---
## Introduction

[GitHub Repo](https://github.com/sdras/intro-to-vue)

+ Why Vue.js
  + Declarative
  + Legible
  + Easy to Maintain
  + Powerful
  + A collection of the best of the best
  + Elegant
  + Gives me what I want when I need it, and gets out of my way
  + A way to be really productive
  + Goodness gracious it's freaking fun

+ Comparison with other frameworks
  + A virtual DOM
  + Reactive components that offer the View layer only
  + Props and a Redux-like store similar to React
  + Conditional rendering, and services, similar to Angular
  + Inspired by Polymer for simplicity and performance, Vue offers a similar development style as HTML, styles, and JavaScript are composed in tandem
  + Slightly better performance than React, no use of polyfills like Polymer, and an isolated, less opinionated view than Angular, which is an MVC
<!-- more -->
### Course Agenda

+ What this course is:
  + Introduction
  + Stuff to get you going
  + Exercises to help to learn
  + Covering basic premises, and a couple of more advanced features

+ Won't cover
  + ES6(we'll use it but won't explain it)
  + Details - so that we can cover more ground
  + Deployment
  + Edge-cases

## Directives & Data Rendering

### Vue Instance

+ Intro & Vue Instance

+ Obligatory example
  + Hello World!

```html
<div id="app">{{ text }} Nice to meet vue.</div>
```

```js
new Vue({
  el: '#app',
  data: {
    text: 'Hello world'
  }
})
```

### Comparing Vanilla JavaScript

+ Vanilla JS

```js
const items = [
  'thingie',
  'another thingie',
  'lots of stuff',
  'yadda yadda'
]

function listOfStuff() {
  let full_list = ''
  items.forEach(item => {
    full_list += `<li> ${item} </li>`
  })
  const contain = document.querySelector('#container')
  contain.innerHtml = `<ul> ${full_list} </ul>`
}
```

+ Vue
  + clean
  + semantic
  + declarative
  + legible
  + easy to maintain
  + reactive

```js
new Vue({
  el: '#app',
  data: {
    items: [
      'thingie',
      'another thingie',
      'lots of stuff',
      'yadda yadda'
    ]
  }
})
```

```html
<div id="app">
  <li v-for="item in items">
    {{ item }}
  </li>
</div>
```

### Directives

+ Directives
  + v-text
  + v-html
  + v-show
  + v-if
  + v-else
  + v-else-if
  + v-for
  + v-on
  + v-bind
  + v-model
  + v-pre
  + v-cloak
  + v-once

+ v-for
  + Loops through a set of values(previous example - item in items)
  + can also do a static number

+ v-model
  + Creates a relationship between the data in the instance/component and a form input, so you can dynamically update values
  + accepting user input and managing it in a responsible manner

  ```js
  new Vue({
    el: '#app',
    data() {
      return {
        message: 'This is a good place to type things.'
      }
    }
  })
  ```

  ```html
  <div id="app">
    <h3>Type here:</h3>
    <textarea v-model="message" class="message" rows="5" maxlength="72"/>
    <br>
    <p class="booktext">{{ message }}</p>
  </div>
  ```

+ Modifiers
  + `v-model.trim` will strip any leading or trailing whitespace from the bounding string
  + `v-model.number` changes strings to number inputs
  + `v-model.lazy` won't populate the content automatically, it will wait to bind until an event happens.(It listens to change events instead of input)

+ v-if/v-show
  + Is a conditional that will display information depending on meeting a requirement. This can be anything - buttons, forms, divs, components

+ v-if/v-else
  + Pretty straightforward - you can conditionally render one thing or another. There's also v-else-if

+ v-bind or :
  + One of the most useful directives so there's a shortcut! We can use it for so many things - class and style binding, creating dynamic props, etc...

+ v-once & v-pre
  + Not quite as useful, v-once will not update once it's been rendered
  + v-pre will print out the inner text exactly how it is, including code(good for documentation)

+ v-on or @
  + Extremely useful so there's a shortcut! v-on is great for binding to events like click and mouseenter. You're able to pass in a parameter for the event like (e)
  + We can also use ternaries directly

+ Modifiers
  + `@mousemove.stop` is comparable to `e.stopPropogation()`
  + `@mousemove.prevent` is like `e.preventDefault()`
  + `@submit.prevent` will no longer reload the page on submission
  + `@click.once` not to be confused with v-once, this click event will be triggered once
  + `@click.native` so that you can listen to native events in the DOM

+ v-html
  + Great for strings that have html elements that need to be rendered
+ Warnings:
  + Not the same as templates: inserted as plain HTML
  + Don't use on user-rendered content, avoid XSS attacks

+ v-text
  + Similar to using mustache templates
+ Warning:
  + If you want to dynamically update, it's recommended to use mustache templates instead

+ [Intro to Vue.js: Rendering, Directives, and Events](https://css-tricks.com/intro-to-vue-1-rendering-directives-events/)

### Challenge: Calculator

[exercise link](https://codepen.io/sdras/pen/vZjozM/)

[my solution](https://codepen.io/blingblingredstar/pen/zYYGoqd)

## Methods, Computed & Watchers

### Methods

[slides](http://slides.com/sdrasner/intro-to-vue-2?token=502n2b7V#/)

+ Methods
  + Are bound to the Vue instance, they are incredibly useful for functions you would like to access in directives

### Working with Methods

```js
new Vue({
  el: '#app',
  data: {
    newComment: '',
    comments: [
      'Looks great Julianne!',
      'I love the sea.',
      'Where are you at?'
    ]
  },
  methods: {
    addComment() {
      this.comments.push(this.newComment)
      this.newComment = ''
    }
  }
})
```

```html
<ul>
  <li v-for="comment in comments">
    {{ comment }}
  </li>
</ul>
<input
  v-model="newComment"
  @keyup.enter="addComment"
  palceholder="Add a comment"
/>
```

### Computed Properties

+ Computed
  + Computed properties are calculations that will be cached and will only update when needed
  + Highly performant but use with understanding

| Computed                                            | Methods                                     |
| --------------------------------------------------- | ------------------------------------------- |
| Runs only when a dependency has changed             | Runs whenever an update occurs              |
| Cached                                              | Not cached                                  |
| should be used as a property, in place of data      | Typically invoked from v-on/@, but flexible |
| By default getter only, but you can define a setter | Getter/setter                               |

### Challenge: Updating a Blog

[exercise](https://codepen.io/sdras/pen/WOyjoj/)

[solution](https://codepen.io/blingblingredstar/pen/OJJVWJX?editors=1010)

### Reactive Programming

+ Watchers & Vue's Reactivity System

+ What is reactive?
  + Reactive programming is programming with asynchronous data streams
  + A stream is a sequence of ongoing events ordered in time that offer some hooks with which to observe it
  + When we use reactive premises for building applications, this means it's very easy to update state in reaction to events
  + [The introduction to Reactive Programming you've been missing](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)

+ What is reactive?
  + Angular 1.x has dirty checking
  + Cycle.js and Angular 2 use reactive streams like XStream and Rx.js
  + Vue.js, MobX or Reactive.js all use a variation of getters/setters
  + Despite the name, React is not Reactive - it uses a "pull" approach(rather than "push")
  + More Resources
    + [React design documentation](https://zh-hans.reactjs.org/docs/design-principles.html)
    + [深入响应式原理](https://cn.vuejs.org/v2/guide/reactivity.html)
    + [How to build a reactive engine in JavaScript. Part 1: Observable objects](https://www.monterail.com/blog/2016/how-to-build-a-reactive-engine-in-javascript-part-1-observable-objects)

+ In Vue
  + Vue takes the object, walks through its properties and converts them to getter/setters

    ```js
    new Vue({
      data: {
        text: 'msg'
      }
    })
    ```

  + Vue cannot detect property addition or deletion so we create this object to keep track

### Watchers

+ Each components has a watcher instance
+ The properties touched by the watcher during the render are registered as dependencies
+ When the setter is triggered, it lets the watcher know, and causes the component to re-render

+ The Vue instance is the middleman between the DOM and the business logic
+ Watch updates the DOM only if it's required - performing calculations in JS is really performant but accessing the DOM is not. So we have a virtual DOM which is like a copy, but parsed in JavaScript
+ It will only update the DOM for things that need to be changed, which is faster

+ Watchers
  + Good for asynchornous updates, and updates/transitions with data changes

+ We're going to 'watch' any data property declared on the Vue instance

+ When watching a property, you trigger a method on change:

    ```js
    new Vue({
      el: '#app',
      data() {
        return {
          bottom: false,
          beers: []
        }
      },
      watch: {
        bottom(bottom) {
          if(bottom) {
            this.addBeer()
          }
        }
      }
    })
    ```

+ We also have access to the new value and the old value

    ```js
    watch: {
      watchedProperty(value, oldValue) {
        // your dope code here
      }
    },
    ```

+ We can also gain access to nested values with 'deep':

    ```js
    watch: {
      watchedProperty {
        deep: true,
        nestedWatchedProperty(value, oldValue) {
          // your dope code here
        }
      }
    }
    ```

+ [Transitioning state with watchers](https://codepen.io/sdras/pen/OWZRZL)

## Components

### Templates

+ Templates
  + Vue.js uses HTML-based template syntax to bind the Vue instance to the DOM, very useful for components
  + Templates are optional, you can also [write render function](https://cn.vuejs.org/v2/guide/render-function.html) with optional JSX support
  + We will start with strings(only useful for small cases) and then to scirpt, and then to single-file templates in the next section

+ Start with a simple template(not terribly useful)

    ```js
    const app = new Vue({
      el: '#app',
      template: '<div>Hello world!</div>'
    })
    ```

    ```html
    <div id="app"></div>
    ```

+ 上面的代码当页面复杂起来时可读性明显不强，也许我们需要下面的语法来增加可读性

    ```html
    <header></header>
    <aside>
      <sidebar-item v-for="item in items"></sidebar-item>
    </aside>
    <main>
      <blogpost v-for="post in posts"></blogpost>
    </main>
    <footer></footer>
    ```

### Introducing Components

+ Components
  + A collection of elements that are encapsulated into a group that can be accessed through one single element

+ Simplest Component

    ```js
    Vue.component('child', {
      template: '<div>Hello world!</div>'
    })

    new Vue({
      el: '#app'
    })
    ```

    ```html
    <div id="app">
      <child></child>
    </div>
    ```

+ Slightly better, with props

    ```js
    Vue.component('child', {
      props: ['text'],
      template: `<div>{{ text }}</div>`
    })

    new Vue({
      el: '#app',
      data() {
        return {
          message: 'Hello world!'
        }
      }
    })
    ```

    ```html
    <div id="app">
      <child :text="message"></child>
    </div>
    ```

### Props

+ Props
  + Passing data down from the parent to the child
    + `props: ['text']`
  + Props are intended for one way communication
  + You can think of it a little like the component holds a variable that is waiting to be filled out by whatever the parent sends down to it
  + Types & validation
    + `props: { text: [String, Number] }`

      ```js
      Vue.component('child', {
        props: {
          text: {
            type: String,
            required: true,
            default: 'Hello world!'
          }
        },
        template: `<div>{{ text }}</div>`
      })
      ```

  + Note: Objects and arrays need their defaults to be returned from a function:

    ```js
    text: {
      type: Object,
      default() {
        return { message: 'Hello world!' }
      }
    }
    ```

+ You don't need to necessarily pass the data in props to the child, either, you have the option of using Vue instance data or a static value as you see fit
  + We use `v-bind` or `:` to dynamically bind props to data on the parent

+ Each component instance has its own isolated scope
  + data must be a function

+ camelCasing will be converted
  + `props: ['booleanValue']`
+ In HTML it will be kebab-case
  + `<checkbox :boolean-value="booleanValue"></checkbox>`

### Refactoring into a Component
<!-- markdownlint-disable md033 -->
<details>
<summary>原文件</summary>

```js
new Vue({
  el: '#app',
  data() {
    return {
      newComment: '',
      comments: [
        'Looks great Julianne!',
        'I love the sea',
        'Where are you at?'
      ]
    }
  },
  methods: {
    addComment() {
      this.comments.push(this.newComment)
      this.newComment = ''
    }
  }
})
```

```html
<ul>
  <li v-for="comment in comments">
    {{ comment }}
  </li>
</ul>
<input
  v-model="newComment"
  v-on:keyup.enter="addComment"
  placeholder="Add a comment"
/>
```

</details>

<details>
<summary>组件化为</summary>

```js
Vue.component('individual-comment', {
  template: `<li> {{ commentpost }} </li>`,
  props: ['commentpost']
})

new Vue({
  el: '#app',
  data: {
    newComment: '',
    comments: [
      'Looks great Julianne!',
      'I love the sea',
      'Where are you at?'
    ]
  },
  methods: {
    addComment() {
      this.comments.push(this.newComment)
      this.newComment = ''
    }
  }
})
```

```html
<ul>
  <li
    is="individual-comment"
    v-for="comment in comments"
    v-bind:commentpost="comment"
  ></li>
</ul>
<input
  v-model="newComment"
  v-on:keyup.enter="addComment"
  placeholder="Add a comment"
/>
```

</details>

+ In JS

```js
Vue.component('individual-comment', {
  template: '#comment-template',
  props: ['commentpost']
})
```

+ In HTML

```html
<script type="text/x-template" id="comment-template">
  <li>
    <img class="post-img" v-bind:src="commentpost.authorImg" />
    <small>{{ commentpost.author }}</small>
    <p class="post-comment">"{{ commentpost.text }}"</p>
  </li>
</script>
```

+ Another option: Local component
  + we can do it since it's all one file

```js
const IndividualComment = {
  template: '#comment-template',
  props: ['commentpost']
}

new Vue({
  // ...
  components: {
    'individual-commnet': IndividualComment
  }
})
```

### Communicating Events

+ [Change prop on event, incorrectly](https://codepen.io/sdras/pen/6a0a49ff73cd3c49f14b83d63a9ffd00)

```html
<my-component @myEvent="parentHandler"></my-component>
```

```js
methods: {
  fireEvent() {
    this.$emit('myEvent', eventValueOne, eventValueTwo)
  }
}
```

+ `$emit`
  + The child is reporting it's activity to the parent

+ JS

```js
Vue.component('child', {
  props: ['text'],
  template: '#child',
  methods: {
    talkToMe() {
      // this.text = 'forget introductions, I want a taco' // 更改为以下
      this.$emit('changetext', 'forget introductions, I want a taco')
    }
  }
})

new Vue({
  el: '#app',
  data() {
    return {
      message: 'hello mr. magoo'
    }
  }
})
```

+ HTML

```html
<div id="app">
  <!-- <child :text="message"></child> 更改为以下 -->
  <child :text="message" @changetext="message = $event"></child>
</div>

<script type="text/x-template" id="child">
  <div>
   <p>{{ text }}</p>
   <button @click="talkToMe">Let's talk</button>
  </div>
</script>
```

+ [Change prop on event, emitting](https://codepen.io/sdras/pen/GEzwOz)

+ [Emitting samples](https://codepen.io/collection/db363299f0318f24902ad5f8494248ed/)

+ With a method:
  + [Vue emitting events from child to update a ball bounce](https://codepen.io/sdras/pen/771eaa6936cc619048604ce8a2331b37)
  + [Vue emitting events from child to update a ball bounce 2](https://codepen.io/sdras/pen/PjyNqV)

+ Components:
  + Data must be a function
  + [Vue.js Docs](https://cn.vuejs.org/v2/guide/components.html#data-%E5%BF%85%E9%A1%BB%E6%98%AF%E4%B8%80%E4%B8%AA%E5%87%BD%E6%95%B0)
  + [Backpack Shop Counter Components 1](https://codepen.io/sdras/pen/5f7deb2df0057f44c9583f37f0935fec)
  + [Backpack Shop Counter Components 2](https://codepen.io/sdras/pen/2751def77693975f59bc72a237f1ba89)

### Slots

```html
<script type="text/x-template" id="childarea">
  <div class="child">
    <slot><slot>
    <p>It's a veritable slot machine!</P>
  </div>
</script>
```

```html
<div id="app">
  <h2>We can use slots to populate content</h2>
  <app-child>
    <h3>This is slot number one</h3>
  </app-child>
  <app-child>
    <h3>This is slot number two</h3>
    <small>I can put more info in, too!</small>
  </app-child>
</div>
```

+ [Slots Example](https://codepen.io/sdras/pen/e06f9393e73054e7185ff48dfa36e161)

+ You can have defaults
  + `<slot>I am some default text</slot>`

+ You can have more than one
  + Or just keep things tidy by naming them

    ```html
    <slot name="headerinfo"></slot>
    <h1 slot="headerinfo">I will populate the headerinfo slot</h1>
    ```

    ```html
    <div id="post">
      <main>
        <slot name="header"></slot>
        <slot></slot>
      </main>
    </div>
    ```

    ```html
    <app-post>
      <h1 slot="header">This is the main title</h1>
      <p>I will go in the unnamed slot!</p>
    </app-post>
    ```

    ```html
    <main>
      <h1>This is the main title</h1>
      <p>I will go in the unnamed slot!</p>
    </main>
    ```

+ [slot example](https://codepen.io/sdras/pen/BpjQzE)

### Keep-Alive

```html
<keep-alive>
  <component :is="selected">
    <!-- ... -->
  </component>
</keep-alive>
```

+ [ue Wine Label Maker- with keep-alive](https://codepen.io/sdras/pen/db71c231f760ee3a53e9d4e65f8745b8)

### Challenge: Refactoring into a Component

[exercise](https://codepen.io/sdras/pen/OgBmYV/)

[solution](https://codepen.io/blingblingredstar/pen/OJJVKvw?editors=1000)

## Vue CLI & Nuxt.js

### Introducing Vue ClI

+ Why
  + Building precesses that allow us to use great features like ES6, or Sass, easy to use other libraires
  + We're going to build and concatenate single file templates, which are AWSOME(not biased)
  + Not load all our files at startup(lazy load, async)
  + Server-side rendering, code-splitting, performance metrics...
  + Build/prod versions

```sh
npm install -g vue-cli

vue init webpack-simple my-project
cd my-project
yarn

npm run dev
```

+ (eventually we'll ues webpack not webpack-simple)

+ Single File Templates

```html
<template>
  <div>
    <!-- write your HTML with Vue in here -->
  </div>
</template>

<script>
  export default {
    // write your Vue component logic here
  }
</script>

<style scoped>
  /* write your styles for the component in here */
</style>
```

+ Vue files mean to context-shifting

```js
import New from './components/New.vue'

export default {
  components: {
    appNew: New
  }
}
```

```html
<app-new></app-new>
```

### Vue CLI Walkthrough

+ You'll start off with an App file in your `/src/` directory with a `Hello.vue` file in the `/components/` directory. This is really nice beaucase you can see already how you'd set up these files, and how imports and exports might work

### Vue Snippets

+ Snippets save lives

```html
<style scoped></style>
```

+ This allow us to very easily scope the styles for this component to only this component
  + @import styles: vue-style-loader

+ slots in Vue components with the scoped style tags, they apply to the component that has the slots

```html
<template>
  <div>
    <slot></slot>
  </div>
</template>
```

```html
<style scoped>
  .label {
    fill: black;
  }
  .bottle, .wine-text {
    fill: white;
  }
  .flor {
    fill: #ccc;
  }
  .bkimg {
    filter:url(#inverse)
  }
</style>
```

### Lifecycle Hooks

+ The lifecycle hooks provide you a method so that you might trigger something precisely at different junctures of a component's lifecycle. Components are mounten when we instantiate them, and in trun unmounted, for instance when we toggle them in a v-if/v-else statement
  + beforeCreate
  + created
  + beforeMount
  + mounted
  + beforeUpdate
  + updated
  + activated
  + deactivated
  + beforeDestory
  + destoryed
  + errorCaptured(2.5.0+)

+ [Demystifying Vue Lifecycle Methods](https://scotch.io/tutorials/demystifying-vue-lifecycle-methods)

+ newVue()
+ beforeCreate()
  + observe data && init events
+ created()
  + template options and render
+ beforeMount()
  + create virtual DOM el and repalce 'el' with it
+ mounted()
  + beforeUpdate()
    + virtual DOM and patch
  + updated()
  + activated()
    + keep-alive component reactivated
  + deactivated()
+ beforeDestory()
  + teardown watchers, child components, event listeners
+ destoryed()

+ Lifecycle hooks also auto-bind the instance so that you can use the component's state, and methods. Again, you don't have to console.log to find out what this refers to!
+ For this reason though, you shouldn't use an arrow function on a lifecycle method, as it will return the parent instead of giving you nice binding out of the box.

### Introducing Nuxt.js

+ Why?
  + Automatic Code Splitting
  + Server-Side Rendering
  + Powerful Routing System with Asynchornous Data
  + Static File Serving
  + ES6/ES7 Transpilation
  + Hot reloading in Development
  + Pre-processor: SASS, LESS, Stylus, etc
  + Write Vue files

### Nuxt.js Application Walkthrough

```sh
npm install -g vue-cli

vue init nuxt/starter my-project
cd my-project
yarn

npm run dev
```

+ [https://nuxtjs.org/guide/installation](https://nuxtjs.org/guide/installation)

## Animations

### Introducing Animations

+ Transitions vs. Animations

+ Transition
  + Modal Example

```html
<script type="text/x-template" id="childarea">
  <div>
    <h2>Here I am!</h2>
    <slot></slot>
  </div>
</script>
```

```html
<div id="app">
  <h3>Let's trigger this here modal</h3>
  <button @click="toggleShow">
    <span v-if="isShowing">Hide child</span>
    <span v-else>Show child</span>
  </button>
  <app-child v-if="isShowing" class="modal">
    <button @click="toggleShow">Close</button>
  </app-child>
</div>
```

```js
const Child = {
  template: 'childarea'
}

new Vue({
  el: '#app',
  data() {
    return {
      isShowing: false
    }
  },
  methods: {
    toggleShow() {
      this.isShowing = !this.isShowing
    }
  },
  components: {
    appChild: Child
  }
})
```

+ [codepen demo](https://codepen.io/sdras/pen/df955fc6a1f482dcf104f2648746f8b3)

```html
<transition name="fade">
  <app-child v-if="isShowing" class="modal">
    <button @click="toggleShow">
      Close
    </button>
  </app-child>
</transition>
```

+ Default 'v-' prefix, otherwise name="name"
+ Example

    ```css
    .v-enter-active {
      transition: color 1s;
    }
    ```

+ Reusable for other components

    ```css
    .fade-enter-active, .fade-leave-active {
      transition: opacity 0.25s ease-out;
    }
    .fade-enter, .fade-leave-to {
      opacity: 0;
    }
    ```

  + This is unnecessary, as it's default

    ```css
    .fade-enter-to, .fade-leave {
      opacity: 1;
    }
    ```

+ [CodePen demo](https://codepen.io/sdras/pen/6ef951b970faf929d8580199fe8ea6ba)

+ Great
  + But what would happen if we wanted to make that background content fade out of view, so that the modal took center stage and the background lost focus?
  + Can't use the transition component

    ```html
    <div :class="[isShowing ? blurClass : '', bkClass]">
      <h3>Let's trigger here modal!</h3>
      <button @click="toggleShow">
        <span v-if="isShowing">Hide child</span>
        <span v-else>Show child</span>
      </button>
    </div>
    ```

    ```css
    .bk {
      transition: all 0.1s ease-out;
    }

    .blur {
      filter: blur(2px);
      opacity: 0.4;
    }
    ```

    ```js
    new Vue({
      el: '#app',
      data() {
        return {
          isShowing: false,
          bkClass: 'bk',
          blurClass: 'blur'
        }
      },
      ...
    })
    ```

### CSS Animation

+ Still `<transition />` component, but
  + enter-active-class="toasty"
  + leave-active-class="bounceOut"

    ```css
    .toasty {
      toasty 1s ease both;
    }
    ```

+ Can also hook into CSS animation libraries this way

+ Bounce a ball

    ```html
    <div id="app">
      <h3>Bounce the Ball!</h3>
      <button @click="toggleShow">
        <span v-if="isShowing">Get it gone!</span>
        <span v-else>Here we go!</span>
      </button>
      <transition
        name="ballmove"
        enter-active-class="bouncein"
        leave-active-class="rollout">
      <div v-if="isShowing">
        <app-child class="child"></app-child>
      </div>
      </transition>
    </div>
    ```

    ```scss
    @mixin ballb($yaxis: 0) {
      transform: translate3d(0, $yaxis, 0);
    }

    @keyframes bouncein {
      1% { @include ballb(-400px); }
      20%, 40%, 60%, 80%, 95%, 99%, 100% { @include ballb() }
      30% { @include ballb(-80px); }
      50% { @include ballb(-40px); }
      70% { @include ballb(-30px); }
      90% { @include ballb(-15px); }
      97% { @include ballb(-10px); }
    }

    .bouncein {
      animation: bouncein 0.8s cubic-bezier(0.25, 0.46, 0.45, 0.94) both;
    }

    .ballmove-enter {
      @include ballb(-400px);
    }
    ```

+ [CodePen demo](https://codepen.io/sdras/pen/pRWxGg)

### Challenge: Adding Animation

[exercise](https://codepen.io/sdras/pen/pRWxGg)

### Transition Mode

+ in-out
  + The current element waits until the new element is done transitioning in to fire
+ out-in
  + The current element transitions out and then the new element transitions in

```html
<transition name="flip" mode="out-in">
  <slot v-if="!isShowing"></slot>
  <img v-else src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/28963/cartoonvideo14.jpeg" />
</transition>
```

+ [Vue transition modes](https://codepen.io/sdras/pen/mRpoOG)
+ [Vue in-out modes - no modes contrast](https://codepen.io/sdras/pen/6f42a0203557cfc041c340a1ce44f059)

  ```css
  .flip-enter-active {
    transition: all .2s cubic-bezier(0.55, 0.085, 0.68, 0.53); //ease-in-quad
  }

  .flip-leave-active {
    transition: all .25s cubic-bezier(0.25, 0.46, 0.45, 0.94); //ease-out-quad
  }

  .flip-enter, .flip-leave-to {
    transform: scaleY(0) translateZ(0);
    opacity: 0;
  }
  ```

### JavaScript Hooks

+ Custom Naming

  ```html
  <transition
    @before-enter="beforeEnter"
    @enter="enter"
    @after-enter="afterEnter"
    @enter-cancelled="enterCancelled"

    @before-leave="beforeLeave"
    @leave="leave"
    @after-leave="afterLeave"
    @leave-calcelled="leaveCancelled"
    :css="false"
  >
  </transition>
  ```

+ Most basic example

  ```html
  <transition
    @enter="enterEl"
    @leave="leaveEl"
    :css="false"
  >
    <!-- put element here -->
  </transition>
  ```

  ```js
  methods: {
    enterEl(el, done) {
      // entrance animation
      done()
    },
    leaveEl(el, done) {
      // exit animation
      done()
    }
  }
  ```

+ [Vue Book Content Typer](https://codepen.io/sdras/pen/MJedjd)

<details>
<summary>code</summary>

```js
new Vue({
  el: '#app',
  data() {
    return {
      message: 'This is a good place to type things.',
      load: false
    }
  },
  methods: {
    beforeEnter(el) {
      TweenMax.set(el, {
        transformPerspective: 600,
        perspective: 300,
        transformStyle: "preserve-3d",
        autoAlpha: 1
      });
    },
    enter(el, done) {
      ...
      tl.add("drop");
      for (var i = 0; i < wordCount; i++) {
        tl.from(split.words[i], 1.5, {
          z: Math.floor(Math.random() * (1 + 150 - -150) + -150),
          ease: Bounce.easeOut
        }, "drop+=0." + (i/ 0.5));
       ...
    }
  }
})
```

</details>

### Connect to Interaction

+ [Vue-controlled Wall-E](https://codepen.io/sdras/pen/YZBGNp)

### Simple Transition

+ Progress bar
  + nuxt.config.js

  ```js
  /*
  ** Customize the progress-bar color
  */
  loading: { color: '#3B8070' },
  ```

  + to

  ```js
  loading: false,
  css: ['assets/normalize.css', 'assets/main.css'],
  ```

  + and create `assets/normalize.css`, and blank `assets/main.css`

  ```css
  .page-enter-active, .page-leave-active {
    transition: all .25s ease-out;
  }
  .page-enter, .page-leave-active {
    opacity: 0;
    transform: scale(0.95);
    transform-origin: 50% 50%;
  }
  ```

### Page-Specific Transitions

```html
<template>
  <div class="container">
      <h1>This is the content page</h1>
      <p><next-link to="/">Home page</next-link></p>
  </div>
</template>

<script>
export default {
  transition: 'fadeOpacity'
}
</script>
```

+ Either assets/main.css or `<scope style>` within the template

```css
.fadeOpacity-enter-active, .fadeOpacity-leave-active {
  transition: opacity 0.35s ease-out;
}

.fadeOpacity-enter, .fadeOpacity-leave-active {
  opacity: 0;
}
```

+ Page-specific JavaScript hooks

<details>
<summary>Review</summary>

```html
<transition
  @before-enter="beforeEnter"
  @enter="enter"
  @after-enter="afterEnter"
  @enter-cancelled="enterCancelled"

  @before-leave="beforeLeave"
  @leave="leave"
  @after-leave="leave"
  @leave-cancelled="leaveCancellled"
  :css="false"
>
</transition>
```

```js
methods: {
  enter(el, done) {
    // entrance animation
    done()
  },
  leave(el, done) {
    // exit animation
    done()
  }
}
```

</details>

+ Available with nuxt.js

<script>
export default {
  transition: {
    mode: 'out-in',
    css: false,
    beforeLeave(el) {
      console.log('About before leave')
      console.log(el)
    },
    leave(el, done) {
      console.log('About leave')
      done()
    },
    beforeEnter(el) {
      console.log('About before enter')
    },
    enter(el, done) {
      console.log('About enter')
      done()
    }
  }
}
</script>

+ What about the single element?

```js
enter(el, done) {
  TweenMax.to('h1', 1, {
    color: 'red'
  })
  TweenMax.to(el, 1, {
    rotationY: 360,
    transformOrigin: '50% 50%',
    ease: Back.easeOut
  })
  done()
},
```

+ or Lifecycle Hooks

```js
mounted() {
  TweenMax.to('h1', 1, {
    color: 'red'
  })
}
```

### Planning & Fancy Demo

+ Things that you need to stay constant can live outside of the pages

<details>
<summary>layouts/default.vue</summary>

```html
<template>
  <div>
    <nuxt />
  </div>
</template>

<script>
  import Navigation from '-components/Navigation.vue'

  export default {
    components: {
      Navigation
    }
  }
</script>
```

</details>

### Filters, Mixins & Custom Directives

+ [Current Slide](http://slides.com/sdrasner/intro-to-vue-6?token=fcL8qgTg#/)

+ The first thing to understand about filters is that they aren't replacements for methods, computed values, **beaucase filters don't transform the data, just the output that the user sees**.

+ Two ways to register a new filter:

    ```js
    // global
    Vue.filter('filterName', function(value) {
      return // thing to transform
    })

    // locally, like methods or computed

    filters: {
      filterName(value) {
        return // thing to transform
      }
    }
    ```

+ Use it like this:

    ```js
    {{ data | filter }}
    ```

+ In JS:

    ```js
    new Vue({
      el: '#app',
      data() {
        return {
          customer1total: 35.43
        }
      },
      filters: {
        tip15(value) {
          return (value * 0.15).toFixed(2)
        }
      }
    })
    ```

+ In HTMl:

    ```html
    <div id="app">
    <h2>Tip Calculator</h2>
    <p><strong>Total: {{ customer1total }}</strong></p>
    <p>15%: {{ customer1total | tip15 }}</p>
    </div>
    ```

+ [CodePen demo](https://codepen.io/sdras/pen/39cd370ccc0e414aafb2a1d6e01aecc0)

+ You can chain them:

    ```js
    {{ data | filterA | filterB }}
    ```

+ The first will be applied first, second after
+ [Chained filters](https://codepen.io/sdras/pen/33d92d8939e0c10e8ec80de693795a26)

+ You can pass arguments:

    ```js
    {{ data | filterName(arg1, arg2) }}
    ```

    ```js
    // arguments are passed in order after the value
    filters: {
      filterName(value, arg1, arg2) {
        return // thing to transform
      }
    }
    ```

+ Filters sounds like it would be good to filter a lot of data, but filters are rerun on every single update, so better to use computed, for values like these that should be cached

### Mixins

+ It's a common situation: you have two components that are pretty similar, they share the same basic functionality, but there's enough that's different about each of them that you come to a crossroads: do I split this component into two different components? Or do I keep one component, but create enough variance with props that I can alter each one?

+ > OO makes code understandable by encapsulating moving parts. FP makes code understandable by minimizing moving parts. - Micheal Feathers on Twitter

+ A mixin allows you to encapsulate one piece of functionality so that you can use it in different components throughout the application

+ If written correctly, they are pure - they don't modify or change things outside of the function's scope, so you will reliably always receive the same value with the same inputs on multiply executions

+ This can be really powerful

+ A simple usecase

```js
// modal
const modal = {
  template: '#modal',
  data() {
    return {
      isShowing: false
    }
  },
  methods: {
    toggleShow() {
      this.isShowing = !this.isShowing
    }
  },
  components: {
    appChild: Child
  }
}
```

```js
// tooltip
const Tooltip = {
  template: '#tooltip',
  data() {
    return {
      isShowing: false
    }
  },
  methods: {
    toggleShow() {
      this.isShowing = !this.isShowing
    }
  },
  components: {
    appChild: Child
  }
}
```

+ Extract the functionality

```js
const toggle = {
  data() {
    return {
      isShowing: false
    }
  },
  methods: {
    toggleShow() {
      this.isShowing = !this.isShowing
    }
  }
}
```

```js
const Modal = {
  template: '#modal',
  mixins: [toggle],
  components: {
    appChild: Child
  }
}

const Tooltip = {
  template: '#tooltip',
  mixins: [toggle],
  components: {
    appChild: Child
  }
}
```

+ [CodePen demo - Mixin](https://codepen.io/sdras/pen/101a5d737b31591e5801c60b666013db)

+ Some examples
  + getting dimensions of the viewport and component
  + gathering specific mousemove events
  + base elements of charts

+ [Example repo by Paul Pflugradt](https://github.com/paulpflug/vue-mixins)

+ Merging with mixins
  + By default, mixins will be applied first, and the component will be applied second so that we can override it as necessary
  + The component has the last say

```js
// mixin
const hi = {
  mounted() {
    console.log('Hello from mixin')
  }
}
// Vue instance or component
new Vue({
  el: '#app',
  mixins: [hi],
  mounted() {
    console.log('Hello from Vue instance')
  }
})
// Out put in console
// Hello from mixin
// Hello from Vue instance
```

+ Global mixins!
  + Global mixins are literally applied to every single component. One use I can think of that makes sense is something like a plugin, where you may need to gain access to everything
  + But still, the use case for them is extremely limited and they should be **considered with great caution**

```js
Vue.mixin({
  mounted() {
    console.log('Hello from mixin')
  }
})

new Vue({
  ...
})
```

+ This console.log would now appear in every component

### Custom Directives

```html
<p v-tack>I will now be tracked onto the page</p>
```

```js
Vue.directive('tack', {
  bind(el, binding, vnode) {
    el.style.position = 'fixed'
  }
})
```

+ Directive deep dive
  + `v-example` - this will instantiate a directive, but doesn't accept any argument. Without passing a value, this would not be very flexible, but you could still hang some piece of functionality off of the DOM element
  + `v-example="value"` - this will pass a value into the directive, and the directive figures out what to do based off of that value
    + `<div v-if="stateExample">I will show up if stateExample is true</div>`
  + `v-example="string"` - this will let you use a string as an expression
    + `<p v-html="'<strong>this is an example of a string in some text</strong>'"></p>`
  + `v-example:arg="value"` - this allows us to pass in an argument to the directive. In the example below, we're binding to a class, and we'd style it with an object, stored separately
    + `<div v-bind:class="someClassObject"></div>`
  + `v-example:arg.modifier="value"` - this allows us to use a modifier. The example below allows us to call `preventDefault()` on the click event
    + `<button v-on:submit.prevent="onSubmit"></button>`

    ```html
    <div id="app">
      <p>Scroll down the page</p>
      <p v-track="70">Stick me 70px from the top of the page</p>
    </div>
    ```

    ```js
    Vue.directive('tack', {
      bind(el, binding, vnode) {
        el.style.position= 'fixed'
        el.style.top = binding.value + 'px'
      }
    })
    ```

+ Passing an argument

    ```html
    <p v-tack:left="70">I'll now be offset from the left instead of the top</p>
    ```

    ```js
    Vue.directive('tack', {
      bind(el, binding, vnode) {
        el.style.position = 'fixed'
        const s = (binding.arg === 'left' ? 'left' : 'top')
        e.style[s] = binding.vlaue + 'px'
      }
    })
    ```

+ [Simple directive with arg](https://codepen.io/sdras/pen/4dfeb0b4f8ac1158236d3b9fea71cc9a)

+ More than one value

    ```html
    <p v-tack="{ top: '40', left: '100' }">Stick me 40px from the top of the page  and 100px from the left of the page</p>
    ```

    ```js
    Vue.directive('tack', {
      bind(el, binding, vnode) {
        el.style.position = 'fixed'
        el.style.top = binding.value.top + 'px'
        el.style.left = binding.value.left + 'px'
      }
    })
    ```

+ [Simple directive with two values](https://codepen.io/sdras/pen/b307a9dd0449ad8563fb489d9ae4ab95)

+ LET'S MAKE SOMETHING

    ```js
    Vue.directive('scroll', {
      inserted(el, binding) {
        let f = (evt) => {
          if(binding.value(evt, el)) {
            window.removeEventListener('scroll', f)
          }
        }
        window.addEventListener('scroll', f)
      }
    })

    // main app
    new Vue({
      el: '#app',
      methods: {
        handleScroll(evt, el) {
          if(window.scrollY > 50) {
            TweenMax.to(el, 1.5, {
              y: -10,
              opacity: 1,
              ease: Sine.easeOut
            })
          }
          return window.scrollY > 100
        }
      }
    })
    ```

    ```html
    <div class="box" v-scroll="handleScroll">
      <p>Lorem ipsum dolor ist amet, consectetur adipisicing elit. a</p>
    </div>
    ```

+ [Custom Scroll Directive](https://codepen.io/sdras/pen/5ca1e0c724d7d900603d8898b5551189)

## Vuex

### Introducing Vuex

+ [current slide](http://slides.com/sdrasner/intro-to-vue-7?token=u9qUgRsW#/)

+ What is Vuex
  + Centralized store for shared data and logic, even shared methods or async
  + Unidirectional data flow
  + Influenced by Flux application architecture
  + Similar to Redux
  + You can still use Redux if you like but this is Vue's version

+ Why use Vuex?
  + In a complex single page application, passing state between many components, and especially deeply nested or sibling components, can get complicated quickly. Having one centralized place to access your data can help you stay organized

+ When to use Vues?
  + "You just konw"
  + Or multiple instances of children/siblings communicating
  + Or I'd like to "see" what all of the state looks like and keep it organized in one place
  + **Warning:** not a replacement for single component methods

### Examining a Vuex Setup

+ How to use Vuex

```sh
npn i --save vuex
#or
yarn add vuex
```

+ I set it up this way: within my `/src/` directory, I create another directory named store(this is a preference, you could also just create a `store.js` file in that same directory), and a file named `store.js`

+ The initial set up in `store.js` would look something like this

    ```js
    import Vue from 'vue'
    import Vuex from 'vuex'

    Vue.use(Vuex)

    export const store = new Vuex.store({
      state: {
        key: value
      }
    })
    ```

+ In our `main.js` file, we'd perform the following updates

    ```js
    import Vue from 'vue'
    import App from './App.vue'

    import { store } from './store/store' // add this

    new Vue({
      el: '#app',
      store: store, // add this
      template: '<App />',
      components: { App }
    })
    ```

+ **Getters** will make values able to show statically in our templates. In other words, getters can read the value, but not mutate the state
+ **Mutations** will allow us to update the state, but they will always be synchornous. Mutations are the only way to change data in the state in the store
+ **Actions** will allow us to update the state, asynchronously, but will use an existing mutation. This can be very helpful if you need to perform a few different mutations at once in a particular order, or reach out to a server

+ We separate actions and mutations because we don't want to get into an ordering problem

<details>
<summary>Basic Abstract Example</summary>

```js
export const store = new Vuex.Store({
  state: {
    counter: 0
  },
  // showing things, not mutating state
  getters: {
    tripleCounter: (state) => {
      return state.counter * 3
    }
  },
  // mutating the state
  // mutations are always synchronous
  mutations: {
    increment: (state, num) => {
      state.counter += num
    }
  },
  // commits the mutation, it's asynchronous
  actions: {
    // showing passed with payload, represented as asyncNum(an object)
    asyncIncrement: ({commit}, asyncNum) => {
      setTimeout(() => {
        // the asyncNum objects could also just be static amounts
        commit('increment', asyncNum.by)
      }, asyncNum.duration)
    }
  }
})
```

</details>

+ Actions
  + We have to pass in the context: context.state, context.getter

    ```js
    actions: {
      // increment(context) {
      //   constext.commit('increment')
      // }
      // destructure it
      increment({context}) {
        commit('increment')
      },
      asyncIncrement({commit}, duration) {
        setTimeout(() => {
          commit('increment')
        }, duration)
      }
    }
    ```

    ```js
    // in component
    methods: {
      asyncIncrement() {
        this.$store.dispatch('asyncIncrement', { duration: 1000 })
      }
    }
    ```

+ On the component itself, we would use computed for getters(this makes sense because the value is already computed for us), and methods with commit to access the mutations, and methods with dispatch for the actions
+ In the Vue instance or a component:

    ```js
    computed: {
      value() {
        return this.$store.getters.tripleCounter
      }
    },
    methods: {
      increment() {
        this.$store.commit('increment', 2)
      },
      asyncIncrement() {
        this.$store.dispatch('asyncIncrement', 2)
      }
    }
    ```

+ Or, you can use a spread operator, useful when you have to work with a lot of getters/mutations/actions

    ```js
    import { mpaActions } from 'vuex'

    export default {
      // ...
      methods: {
        ...mapActions([
          // map this.increment() to this.$store.commit('increment')
          'increment',
          'decrement',
          'asyncIncrement'
        ])
      }
    }
    ```

+ This allows us to still make our own computed properties if we wish

### Vuex Example Walkthrough

+ [Vue Weather Notifier](https://codepen.io/sdras/full/YNpaoJ)

### Wrapping Up

+ [current slide](http://slides.com/sdrasner/intro-to-vue-7?token=u9qUgRsW#/25)

+ Futher Resources:
  + [vue docs](https://vuejs.org/)
  + [vue repo](https://github.com/vuejs)
  + [next docs](https://nuxtjs.org/)
  + [vuex docs](https://github.com/vuejs/vuex)
  + [css-tricks guide](https://css-tricks.com/guides/vue/)
  + [awesome vue](https://github.com/vuejs/awesome-vue)
  + [vue newsletter](http://vue-newsletter.com/)
  + [monterail blog](https://www.monterail.com/blog)
  + [vue tips](http://vuetips.com/)
  + [the majesty of vue](https://leanpub.com/vuejs2)
