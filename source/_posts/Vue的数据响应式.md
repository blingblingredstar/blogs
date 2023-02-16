---
title: Vue的数据响应式
date: 2019-12-19 15:45:09
categories: [学习笔记]
tags: [饥人谷, Vue]
---

> 本文内容基本来自于[Evan You在FrontendMaster上的《Advanced Vue.js Features form the Ground Up》课程中第二节Reactivity](https://frontendmasters.com/courses/advanced-vue/introducing-reactivity/)中的内容。课程为英文授课，根据我自己的理解总结了这篇文章，课程其余内容可见我的[另一篇博客](https://blingblingredstar.github.io/2019/10/18/Advanced-Vue-js-Features-from-the-Ground-Up/)。
> 最后一部分内容为我个人的一些总结，及官方文档的一些内容。

## 什么是数据响应式

说道数据响应式，首先要说一下响应式编程。目前响应式编程中比较流行的一个框架就是[Rx.js](https://cn.rx.js.org/)，但响应式编程不仅仅指Rx.js这一个库。

响应式编程指当改变一个状态之后，整个系统应该随状态的更新而一同更新，在Vue中，也可以特指当数据状态改变时数据所绑定的DOM应该一同改变。
<!-- more -->
这里举一个简单的例子

```js
let a = 3
```

假设我们需要有一个新的变量，其值是a的十倍，那么一个简单的写法如下

```js
let b = a * 10
```

但是这有一个明显的问题就是当a改变时b并不会随之改变

```js
a = 4
console.log(b) // 30
```

也许我们可以手动重新给b赋值，完成需求

```js
a = 4
b = a * 10
console.log(b)
```

但是这明显很过程式，并且容易疏忽。分析我们的需求，其实就类似于办公软件Excel。当我们改变一个表格内容时，里一个表格会根据预设好的公式自动更新。

假设我们有一个函数，可以实现类似Excel的功能，那么使用时的代码应该类似如下

```js
onAChanged(() => {
  b = a * 10
})
```

在实现这个onAChanged之前，我们来看一个更加贴合前端需求的代码

```html
<span class="cell b1"></span>
```

我们需要这个span的内容根据state.a的值乘以10进行显示，使用命令式写法，可以写为如下

```js
document
  .querySelector('.cell.b1')
  .textContent = state.a * 10
```

使用上面还未实现的onStateChange，代码应该类似如下

```js
onStateChanged(() => {
  document
    .querySelector('.cell.b1')
    .textContent = state.a * 10
})
```

当我们把与DOM交互的部分抽离出去，那么代码应该如下

```html
<span class="cell b1">
  {{ state.a * 10 }}
</span>
```

```js
onStateChanged(() => {
  view = render(state)
})
```

其中view = render(state)这个公式，就是一个高度的抽象，涉及到了与DOM交互的种种细节，我们这里先不管。那么onStateChanged这个函数，就是关于数据响应式的核心，我们也许可是实现如下

```js
let update // 使用全局变量保存更新方法

const onStateChanged = (_update) => {
  update = _update // 调用时将参数注册为update方法
}
// 当改变状态是必须通过调用此方法来改变数据状态
const setState = (newState) => {
  state = newState // 首先更新数据状态
  update() // 然后根据全局注册的update方法更新
}
```

当我们使用上述实现时，就比较像React了

```js
onStateChanged(() => {
  view = render(state)
})

setState({ a: 5 })
```

不过Vue中的数据响应式明显并非如此实现，而是类似如下

```js
onStateChanged(() => {
  view = render(state)
})

state.a = 5 // 更改state就自动触发视图更新
```

Vue将数据对象转换为一个响应式的对象。

## Vue中的数据响应式

Vue使用了ES5规范中的Object.defineProperty方法，改写了数据对象中所有属性的getter和setter方法，将上述的onStateChanged方法变更为类似如下

```js
autorun(() => {
  console.log(state.count)
})
```

### Object.defineProperty

我们可以首先实现一个convert方法，接受一个对象为参数，在convert方法内部使用Object.defineProperty来将传入的参数对象添加功能，除正常操作外，每次获取、更改对象属性值就在控制台进行打印，使用时类似如下

```js
const obj = { foo: 123 }
convert(obj)

obj.foo // 控制台打印`getting key "foo": 123`
obj.foo = 234 // 控制台打印`setting key "foo" to 234`
obj.foo // 控制台打印`getting key "foo": 234`
```

我们可以实现如下

```js
function convert(obj) {
  Object.keys(obj).forEach(key => {
    let internalValue = obj[key];
    Object.defineProperty(obj, key, {
      get() {
        console.log(`getting key "${key}": ${internalValue}`);
        return internalValue;
      },
      set(newValue) {
        console.log(`setting key "${key}" to: ${newValue}`);
        internalValue = newValue;
      }
    });
  });
}
```

可见，我们使用了Object.defineProperty来更改了对象的默认行为，在获取或设置对象的属性值时，我们添加了“副作用”，这里的副作用就是控制台打印。显然，我们不仅仅可以控制台打印一些字符串，还一个在获取或设置对象属性值时添加其他行为。

### 依赖追踪

接下来我们可以实现一个依赖追踪系统和一个autorun函数，我们使用class语法实现依赖追踪。这个class应该有两个方法，depend和notify。autorun函数应该接受一个update函数，并在内部调用depend方法。这样调用notify时就会调用传入autorun内的函数，使用类似如下：

```js
const dep = new Dep();

autorun(() => {
  dep.depend();
  console.log("updated");
});
// 控制台打印“updated”
dep.notify();
// 控制台打印“updated”
```

我们可以实现如下:

```js
class Dep {
  constructor() {
    this.subscribers = new Set();
  }
  depend() {
    if (activeUpdate) {
      this.subscribers.add(activeUpdate);
    }
  }
  notify() {
    this.subscribers.forEach(subscriber => {
      subscriber();
    });
  }
}

let activeUpdate;

function autorun(update) {
  function wrappedUpdated() {
    activeUpdate = wrappedUpdated;
    update();
    activeUpdate = null;
  }
  wrappedUpdated();
}
```

### Observe

接下来我们可以将上面两个功能结合起来，实现一个observe方法。

observe方法使用类似如下

```js
const state = {
  count: 0
}

observe(state)

autorun(() => {
  console.log(state.count)
})
// 控制台立即打印"count is: 0"

state.count++
// 控制台打印“count is: 1”
```
<!-- markdownlint-disable md033 -->
<details>

<summary>代码实现</summary>

```js
class Dep {
  constructor() {
    this.subscribers = new Set();
  }
  depend() {
    if (activeUpdate) {
      this.subscribers.add(activeUpdate);
    }
  }
  notify() {
    this.subscribers.forEach(subscriber => {
      subscriber();
    });
  }
}

function observe(obj) {
  Object.keys(obj).forEach(key => {
    let internalValue = obj[key];

    const dep = new Dep();

    Object.defineProperty(obj, key, {
      get() {
        dep.depend();
        return internalValue;
      },
      set(newValue) {
        const hasChanged = newValue !== internalValue;
        internalValue = newValue;
        if (hasChanged) {
          dep.notify();
        }
      }
    });
  });
  return obj;
}

let activeUpdate = null;

function autorun(update) {
  function wrappedUpdated() {
    activeUpdate = wrappedUpdated;
    update();
    activeUpdate = null;
  }
  wrappedUpdated();
}

const state = {
  count: 0
};

observe(state);

autorun(() => {
  console.log("count is: " + state.count);
});
// 控制台立即打印"count is: 0"

state.count++;
// 控制台打印“count is: 1”
```

</details>

我们可以把autorun内的update函数替换view = render(state)，至此，我们可以说是实现了一个与Vue原理类似的数据响应式系统。

## Vue数据响应式系统的一些限制

### 须在声明时初始化data对象的全部属性

了解了Vue数据响应系统的一些原理，就可以立即[Vue官方文档中深入响应式原理](https://cn.vuejs.org/v2/guide/reactivity.html)的一些内容。

首先，我们只能将对象中已经存在的属性的getter和setter进行转化，所以最好在创建Vue实例是就将需要的属性在data对象上创建好，举例来说

```js
const vm = new Vue({
  data: {
    a: 1 // 响应式的，可感知变化
  }
})

vm.b = 2 // 后期动态添加的属性，无法感知变化
```

所以我们最好将所需要的全部属性在data对象上声明好，而不是后期动态的添加。

我们也可以使用绑定在Vue构造函数原型上的set方法或者实例对象上的$set方法来手动的将属性变为响应式的。

### data对象关于数组的一些转换

在data对象中含有数组时，由于无法感知数组的长度，为了能够将数组变为响应式的，Vue在原生的数组中增加了一层，提供了一些方法来替换原生数组变异(mutate)原数组的方法，分别为

1. push
2. pop
3. shift
4. unshift
5. splice
6. reverse
7. sort

### Vue组件中使用函数返回对象而不是直接使用对象

在单独的Vue实例中，data属性使用对象和函数返回一个对象并没有什么不同。但是如果我们多次复用同一组件，在直接使用对象时，多个组件的data属性所指向的对象的引用为同一对象，由于Vue的响应式系统，同一对象无疑会共享所有的数据及响应方式，会造成一些意向不到的情况。所以在将Vue实例作为组件时，推荐使用函数返回对象的方式进行使用，这样即可使组件间data对象的形状(shape)相同，又不是同一引用，保持了各个data对象的独立性。
