---
title: Advanced Vue.js Features from the Ground Up
date: 2019-10-18 10:20:31
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, Vue]
---
## Introduction

+ [Slides](https://docs.google.com/presentation/d/1TgDx4DN8YqfdndYWMovBcQVPWyKLTNcbo1YS8XlLo9o/edit#slide=id.p)

+ Agenda
  + Intro
  + Fundamentals: Reactivity
  + Fundamentals: Writing Plugins
  + Fundamentals: Render Functions
  + State Management
  + Routing
  + Form Validation
  + Component Patterns
<!-- more -->
## Reactivity

### Introducing Reactivity

许多人对于响应式编程这个术语有些迷惑。有的人可能认为仅仅代表了Rx.js这个库。在Vue中，响应式意味着当状态改变时，会影响整个系统随之改变，也可以特指当数据改变时，DOM元素随之改变。

```js
// 首先定义一个简单的变量
let a = 3
let b = a * 10
// 此时产品经理提需求，要求无论a的值是什么，b的值都要是a的十倍
// 此代码中并不可以
console.log(b) // 30
a = 4
console.log(b) // 30
// 要这样才可以
b = a * 10
console.log(b) // 40
```

此时我们需要的是一个类似Excel表格的东西，当单元格的一项改变后，另一个单元格依靠公式自动计算出新的值，用代码表达的话，类似如下:

```js
onAChanged(() => {
  b = a * 10
})
```

接下来，我们来看一个更贴合前端的需求

```html
<span class="cell b1"></span>
```

+ 以下是一个简单的命令式代码

```js
document
  .querySelector('.cell.b1')
  .textContent = state.a * 10
```

+ 我们也许可以更加声明式

```js
onStateChanged(() => {
  document
    .querySelector('.cell.b1')
    .textContent = state.a * 10
})
```

+ 当我们把与DOM交互部分抽离出去的时候，我们就实现了一个简单的响应式框架

```html
<span class="cell b1">
  {{ state.a * 10 }}
</span>
```

```js
onStateChanged(() => { // 更新状态
  view = render(state) // 涉及到DOM绑定更新及Virtual DOM等内容
})
```

```js
let update
const onStateChanged = _update => {
  update = _update
}

const setState = newState => {
  state = newState
  update()
}
```

+ In React

```js
onStateChanged(() => {
  view = render(state)
})

setState({ a: 5 })
```

在react中，强制要求更改状态时要手动调用setState方法，但是在Vue中，我们可以直接操作state

+ In Vue

```js
autorun(() => {
  console.log(state.count)
})
```

+ This is the basic from of the dependency tracking systems as seen in Knockout.js, Meteor Tracker, Vue.js and MobX

### Challenge: Getters and Setters

+ Exercise: Mini Data Observer

+ Goal: implement observer() and autorun() which triggers re-computation when object is mutated
<!-- markdownlint-disable md033 -->
<details>
<summary>getter and setter</summary>

```js
function convert(obj) {
  // Implement this!
  Object.keys(obj).forEach((key) => {
    let internalValue = obj[key]
    Object.defineProperty(obj, key, {
      get() {
        console.log(`getting key "${key}": ${internalValue}`)
        return internalValue
      },
      set(newValue) {
        console.log(`setting key "${key}" to: ${newValue}`)
        internalValue = newValue
      },
    })
  })
}
```

</details>

### Challenge: Dependency Tracker

<details>
<summary>solution</summary>

```js
// a class representing a dependency
// exposing it on window is necessary for testing
window.Dep = class Dep {
  // Implement this!
  constructor() {
    this.subscribers = new Set()
  }

  depend() {
    if (activeUpdate) {
      this.subscribers.add(activeUpdate)
      // register the current active update as a subscriber
    }
  }

  notify() {
    // run all subscriber functions
    this.subscribers.forEach((sub) => {
      sub()
    })
  }
}

let activeUpdate

function autorun(update) {
  // Implement this!
  function wrappedUpdate() {
    activeUpdate = wrappedUpdate
    update()
    activeUpdate = null
  }
  wrappedUpdate()
}
```

</details>

### Challenge: Mini Observer

<details>

<summary>mini observer</summary>

```js
// This is a greatly simplified version of the dependency tracking
// system used in Vue, Knockout, MobX and Meteor Tracker (each with different
// implementation details, of course). It doesn't cover all the possible edge
// cases in detection, nor does it handle Arrays; The goal is simply to better
// understand how the tracking takes place and how data becomes "reactive".

class Dep {
  constructor () {
    this.subscribers = new Set()
  }

  depend () {
    if (activeUpdate) {
      this.subscribers.add(activeUpdate)
    }
  }

  notify () {
    this.subscribers.forEach(sub => sub())
  }
}

function observe (obj) {
  // iterate through all properties on the object
  // and convert them into getter/setters with
  // Object.defineProperty()
  Object.keys(obj).forEach(key => {
    let internalValue = obj[key]

    // each property gets a dependency instance
    const dep = new Dep()

    Object.defineProperty(obj, key, {
      // The getter is responsible for registering subscribers
      get () {
        dep.depend()
        return internalValue
      },

      // The setter is responsible for notifying change
      set (newVal) {
        const changed = internalValue !== newVal
        internalValue = newVal
        // triggering re-computation
        if (changed) {
          dep.notify()
        }
      }
    })
  })
  return obj
}

let activeUpdate = null

function autorun (update) {
  // wrap the raw update function into a "job" function that registers and
  // unregisters itself as the current active job when invoked
  const wrappedUpdate = () => {
    activeUpdate = wrappedUpdate
    update()
    activeUpdate = null
  }
  wrappedUpdate()
}
```

</details>

## Writing Plugins

### Introducing Writing Plugins

```js
function (Vue, options) {
  // ...plugin code
}
```

```js
Vue.use(plugin)
```

```js
Vue.mixin(options)
```

### Challenge: Writing a Simple Plugin

<details>
<summary>solution</summary>

```js
const RulesPlugin = {
  install (Vue) {
    Vue.mixin({
      created () {
        const rules = this.$options.rules
        if (rules) {
          Object.keys(rules).forEach(key => {
            const { validate, message } = rules[key]
            this.$watch(key, newValue => {
              const valid = validate(newValue)
              if (!valid) {
                console.log(message)
              }
            })
          })
        }
      }
    })
  }
}

Vue.use(RulesPlugin)
```

</details>

## Render Functions

### Introducing Render Functions

+ Initial Render

+ Template
  + -> (compiled into) Render Function
  + -> (returns) Virtual DOM
  + -> (generates) Actual DOM

+ Subsequent Updates

+ Render Function
  + -> (returns) New Virtual DOM
  + -> (diffed againest Old Virtual DOM) DOM Updates
  + -> (applied to) Actual DOM

### Virtual DOM

+ Actual DOM

  ```js
  document.createElement('div')
  ```

+ Virtual DOM

  ```js
  vm.$createElement('div')
  ```

+ Actual DOM
  + `"[Object HTMLDivElement]"`
  + Browser Native Object(expensive)

+ Virtual DOM
  + `{ tag: 'div', data: { attrs: {}, ... }, children: [] }`
  + Plain JavaScript Object(cheap)

+ Virtual DOM
  + (Essentially) A lightweight JavaScript data format to represent what the actual DOM should look like at a given point in time
  + Decouples rendering logic from the actual DOM - enables rendering capabilities in non-browser environments, e.g. server-side and native mobile rendering

+ Render Function:
  + A function that returns Virtual DOM

+ Template -> [ Compiler ] -> Render Function

### Putting Everything Together

+ [current slide](https://docs.google.com/presentation/d/1TgDx4DN8YqfdndYWMovBcQVPWyKLTNcbo1YS8XlLo9o/edit#slide=id.g1e6824c3c2_0_147)

### JSX vs. Templates

+ JSX与template都是声明数据与DOM之间关系的方式。Template语法更加静态，JSX更加动态。由于Template更加静态，可以在编译阶段做出许多假设来进行优化，但是实现功能时的灵活性也相对较差。JSX更加动态，所以编译时很难做出预判，但是对应的书写起来更加灵活。

### Render Function API

+ Render Function API

    ```js
    export default {
      render (h) { // h -> hyper script, 类似浏览器中document.createElement
        return h('div', {}, [...])
      }
    }
    ```

+ The "h" function

    ```js
    h('div', 'some text')

    h('div', { class: 'foo' }, 'some text')

    h('div', { ... }, [
      'some text',
      h('span', 'bar')
    ])
    ```

+ [深入数据对象](https://cn.vuejs.org/v2/guide/render-function.html#%E6%B7%B1%E5%85%A5%E6%95%B0%E6%8D%AE%E5%AF%B9%E8%B1%A1)

+ h can directly render a component

    ```js
    import MyComponent from '...'

    h(MyComponent, {
      props: {...}
    })
    ```

### Challenge: Dyamically Render Tags

<details>
<summray>solution</summray>

```html
<script src="../node_modules/vue/dist/vue.js"></script>

<div id="app">
  <example :tags="['h1', 'h2', 'h3']"></example>
</div>

<script>
Vue.component('example', {
  props: ['tags'],
  render (h) {
    // Note that the second argument (data object) can be omitted, and the
    // children argument can accept strings or numbers instead of Array of vnodes.
    return h('div', this.tags.map((tag, i) => h(tag, i)))
  }
})

new Vue({ el: '#app' })
</script>
```

</details>

### Challenge: Dynamic Render Components

<details>
<summary>solution</summary>

```html
<script src="../node_modules/vue/dist/vue.js"></script>

<div id="app">
  <example :ok="ok"></example>
  <button @click="ok = !ok">toggle</button>
</div>

<script>
const Foo = {
  render (h) {
    return h('div', 'foo')
  }
}

const Bar = {
  render (h) {
    return h('div', 'bar')
  }
}

Vue.component('example', {
  props: ['ok'],
  render (h) {
    return h(this.ok ? Foo : Bar)
  }
})

new Vue({
  el: '#app',
  data: { ok: true }
})
</script>
```

</details>

### Challenge: Higher-Order Components

<detials>
<summary>solution</summary>

```html
<script src="../node_modules/vue/dist/vue.js"></script>

<div id="app">
  <smart-avatar username="vuejs"></smart-avatar>
</div>

<script>
// mock API
function fetchURL (username, cb) {
  setTimeout(() => {
    // hard coded, bonus: exercise: make it fetch from gravatar!
    cb('https://avatars3.githubusercontent.com/u/6128107?v=4&s=200')
  }, 500)
}

const Avatar = {
  props: ['src'],
  template: `<img :src="src">`
}

function withAvatarURL (InnerComponent) {
  return {
    props: ['username'],
    inheritAttrs: false, // 2.4 only
    data () {
      return { url: null }
    },
    created () {
      fetchURL(this.username, url => {
        this.url = url
      })
    },
    render (h) {
      return h(InnerComponent, {
        attrs: this.$attrs, // 2.4 only
        props: {
          src: this.url || 'http://via.placeholder.com/200x200'
        }
      })
    }
  }
}

const SmartAvatar = withAvatarURL(Avatar)

new Vue({
  el: '#app',
  components: { SmartAvatar }
})
</script>
```

</detials>

## State Management

### Introducing State Mangement

+ Agenda
  + Shared objects as stores
  + Shared Vue instances as store
  + Abstracting the mutations API
  + Bonus: using a more functional interface

### Challenge: Passing Props

<details>
<summary>solution</summary>

```html
<script src="../node_modules/vue/dist/vue.js"></script>

<div id="app">
  <counter :count="count"></counter>
  <counter :count="count"></counter>
  <counter :count="count"></counter>
  <button @click="count++">increment</button>
</div>

<script>
// requirement: a counter component rendered 3 times
// the component takes the current count via props
// and a button that increments all 3 counters at once
new Vue({
  el: '#app',
  data: {
    count: 0
  },
  components: {
    counter: {
      props: ['count'],
      template: `<div>{{ count }}</div>`
    }
  }
})
</script>
```

</details>

### Challenge: Shared Object

<details>
<summary>solution</summary>

```html
<script src="../node_modules/vue/dist/vue.js"></script>

<div id="app">
  <counter></counter>
  <counter></counter>
  <counter></counter>
  <button @click="inc">increment</button>
</div>

<script>
// create a counter component (that doesn't take any props)
// all instances of it should share the same count state
// and a button that increments all counters at the same time

const state = {
  count: 0
}

const Counter = {
  data () {
    return state // same object, same reference
  },
  template: `<div>{{ count }}</div>`
}

new Vue({
  el: '#app',
  components: { Counter },
  methods: {
    inc () {
      state.count++
    }
  }
})
</script>
```

</details>

### Challenge: Shared Instance

<details>
<summary>solution</summary>

```html
<script src="../node_modules/vue/dist/vue.js"></script>

<div id="app">
  <counter></counter>
  <counter></counter>
  <counter></counter>
  <button @click="inc">increment</button>
</div>

<script>
// copy and modify the first exercise to use a Vue instance as
// a shared store instead.
const state = new Vue({
  data: {
    count: 0
  },
  methods: {
    inc () {
      this.count++
    }
  }
})

const Counter = {
  render (h) {
    return h('div', state.count)
  }
}

new Vue({
  el: '#app',
  components: { Counter },
  methods: {
    inc () {
      state.inc()
    }
  }
})
</script>
```

</details>

### Challenge: Mutation

<details>
<summary>solution</summary>

```html
<script src="../node_modules/vue/dist/vue.js"></script>

<div id="app">
  <counter></counter>
  <counter></counter>
  <counter></counter>
  <button @click="inc">increment</button>
</div>

<script>
// Implement a createStore function that allows the following usage.
function createStore ({ state, mutations }) {
  return new Vue({
    data: {
      state
    },
    methods: {
      commit (mutationName) {
        mutations[mutationName](state)
      }
    }
  })
}

const store = createStore({
  state: { count: 0 },
  mutations: {
    inc (state) {
      state.count++
    }
  }
})

const Counter = {
  render (h) {
    return h('div', store.state.count)
  }
}

new Vue({
  el: '#app',
  components: { Counter },
  methods: {
    inc () {
      store.commit('inc')
    }
  }
})
</script>
```

</details>

### Challenge: Functional

<details>
<summary>solution</summary>

```html
<script src="../node_modules/vue/dist/vue.js"></script>

<div id="app"></div>

<script>
function createApp ({ el, model, view, actions }) {
  Object.keys(actions).forEach(key => {
    const rawAction = actions[key]
    actions[key] = (...payload) => {
      app.model = rawAction(app.model, actions, ...payload)
    }
  })
  const app = new Vue({
    data: { model },
    render (h) {
      return view(h, this.model, actions)
    }
  }).$mount(el)
}

// voila
createApp({
  el: '#app',
  model: {
    count: 0
  },
  actions: {
    inc: ({ count }) => ({ count: count + 1 }),
    dec: ({ count }) => ({ count: count - 1 })
  },
  view: (h, model, actions) => h('div', { attrs: { id: 'app' }}, [
    model.count, ' ',
    h('button', { on: { click: actions.inc }}, '+'),
    h('button', { on: { click: actions.dec }}, '-')
  ])
})
</script>
```

</details>

## Routing

+ Agenda
  + The simplest router(hashchange + `<component :is>`)
  + Extracting a route table
  + URL matching with `path-to-regexp`

### Challenge: Basic Hash Router

<details>
<summary>solution</summary>

```html
<script src="../node_modules/vue/dist/vue.js"></script>
<script>
// goal:
// - display foo when url is at #foo
// - display bar when url is at #bar
// - bonus: implement links that navigate between #foo and #bar

// to access the current hash:
window.location.hash
// to listen for hash changes:
window.addEventListener('hashchange', () => {
  // read hash and update app
})
</script>

<div id="app">
  <component :is="url"></component>
  <a href="#foo">foo</a>
  <a href="#bar">bar</a>
</div>

<script>
window.addEventListener('hashchange', () => {
  // console.log(window.location.hash)
  app.url = window.location.hash.slice(1)
})

const app = new Vue({
  el: '#app',
  data: {
    url: window.location.hash.slice(1)
  },
  components: {
    foo: { template: `<div>foo</div>` },
    bar: { template: `<div>bar</div>` }
  }
})
</script>
```

</details>

### Challenge: Route Table

<details>
<summary>solution</summary>

```html
<script src="../node_modules/vue/dist/vue.js"></script>

<div id="app">
  <component :is="matchedComponent"></component>
  <a href="#foo">foo</a>
  <a href="#bar">bar</a>
</div>

<script>
const Foo = { template: `<div>foo</div>` }
const Bar = { template: `<div>bar</div>` }
const NotFound = { template: `<div>not found!</div>` }

const routeTable = {
  '#foo': Foo,
  '#bar': Bar
}

// '#/foo' -> Foo
// '#/bar' -> Bar
// '#/404' -> NotFound

const app = new Vue({
  el: '#app',
  data: {
    // retrieving the initial URL
    url: window.location.hash
  },
  mounted () {
    // reacting to URL changes
    window.addEventListener('hashchange', () => {
      this.url = window.location.hash
    })
  },
  // alternatively, this can be directly computed inside
  // a render function.
  computed: {
    matchedComponent () {
      return routeTable[this.url] || NotFound
    }
  },
  // render (h) {
  //   return h('div', [
  //     h(routeTable['/' + this.url] || NotFound),
  //     h('a', { attrs: { href: '#foo' }}, 'Go to Foo'),
  //     h('a', { attrs: { href: '#bar' }}, 'Go to Bar')
  //   ])
  // }
})
</script>
```

</details>

### Path to Regular Expressions

+ [GitHub repo path-to-regexp](https://github.com/pillarjs/path-to-regexp)

### Challenge: Dynamic Routes

<details>
<summary>solution</summary>

```html
<script src="../node_modules/vue/dist/vue.js"></script>
<script src="./path-to-regexp.js"></script>

<div id="app"></div>

<script>
const Foo = {
  props: ['id'],
  template: `<div>foo with id: {{ id }}</div>`
}
const Bar = { template: `<div>bar</div>` }
const NotFound = { template: `<div>not found!</div>` }

const routeTable = {
  '/foo/:id': Foo,
  '/bar': Bar
}

// pre-compile patterns into regex
const compiledRouteTable = {}
Object.keys(routeTable).forEach(pattern => {
  const dynamicSegments = []
  compiledRouteTable[pattern] = {
    component: routeTable[pattern],
    regex: pathToRegexp(pattern, dynamicSegments),
    dynamicSegments
  }
})

// '#/foo/123' -> foo with id: 123
// '#/bar' -> Bar
// '#/404' -> NotFound

window.addEventListener('hashchange', () => {
  app.url = window.location.hash.slice(1)
})

// path-to-regexp usage:
// const regex = pathToRegexp(pattern)
// const match = regex.exec(path)
// const params = regex.keys.reduce((params, key, index) => {
//   params[key] = match[index + 1]
// }, {})

const app = new Vue({
  el: '#app',
  data: {
    url: window.location.hash.slice(1)
  },
  render (h) {
    const path = '/' + this.url

    let componentToRender
    let props = {}

    // iterate through our compiled route table
    // and check if a route matches the current path
    // if it matches, extract dynamic segments (params) and use it as props
    // for the matched component
    Object.keys(compiledRouteTable).some(pattern => {
      const { component, regex, dynamicSegments } = compiledRouteTable[pattern]
      const match = regex.exec(path)

      if (match) {
        // we have a match!
        componentToRender = component
        dynamicSegments.forEach(({ name }, index) => {
          props[name] = match[index + 1]
        })
        return true
      }
    })

    return h('div', { attrs: { id: 'app'} }, [
      h(componentToRender || NotFound, {
        props
      }),
      h('a', { attrs: { href: '#foo/123' }}, 'foo/123'),
      ' | ',
      h('a', { attrs: { href: '#bar' }}, 'bar')
    ])
  }
})
</script>
```

</details>

## Form Validation

### Markup-based vs. Model-based

+ Different Validation Plugin Styles
  + Markup-based(vee-validate)
  + Model-based(vuelidate)

### Validation Library

<details>
<summary>solution</summary>

```html
<script src="../node_modules/vue/dist/vue.js"></script>

<div id="app">
  <form @submit="validate">
    <input v-model="text">
    <br>
    <input v-model="email">

    <ul v-if="!$v.valid" style="color:red">
      <li v-for="error in $v.errors">
        {{ error }}
      </li>
    </ul>

    <input type="submit" :disabled="!$v.valid">
  </form>
</div>

<script>
const validationPlugin = {
  install (Vue) {
    Vue.mixin({
      computed: {
        $v () {
          let valid = true
          const errors = []

          const schema = this.$options.validations
          if (schema) {
            Object.keys(schema).forEach(key => {
              const value = this[key]
              const validateFn = schema[key].validate
              const result = validateFn(value)

              if (!result) {
                valid = false
                errors.push(schema[key].message(key, value))
              }
            })
          }

          return {
            valid,
            errors
          }
        }
      }
    })
  }
}

Vue.use(validationPlugin)

const emailRE = /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/

new Vue({
  el: '#app',
  data: {
    text: 'foo',
    email: ''
  },
  validations: {
    text: {
      validate: value => value.length >= 5,
      message: (key, value) => `${key} should have a min length of 5, but got ${value.length}`
    },
    email: {
      validate: value => emailRE.test(value),
      message: key => `${key} must be a valid email`
    }
  },
  methods: {
    validate (e) {
      if (!this.$v.valid) {
        e.preventDefault()
        alert('not valid!')
      }
    }
  }
})
</script>
```

</details>

## Internationalization

### Internationalization Apporaches

<details>
<summary>simple-i18n.html</summary>

```html
<script src="../node_modules/vue/dist/vue.js"></script>

<div id="app">
  <h1>{{ $t('welcome-message') }}</h1>
  <button @click="changeLang('en')">English</button>
  <button @click="changeLang('zh')">中文</button>
  <button @click="changeLang('nl')">Dutch</button>
</div>

<script>
const i18n = {
  install (Vue, locales) {
    // install a $t method that looks up the message from correct locale
    Vue.prototype.$t = function (key) {
      const lang = this.$root.lang
      return locales[lang][key]
    }
  }
}

Vue.use(i18n, /* option */ {
  en: { 'welcome-message': 'hello' },
  zh: { 'welcome-message': '你好' },
  nl: { 'welcome-message': 'Hallo' }
})

new Vue({
  el: '#app',
  data: {
    lang: 'en'
  },
  methods: {
    changeLang (lang) {
      this.lang = lang
    }
  }
})
</script>
```

</details>
