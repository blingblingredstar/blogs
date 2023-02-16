---
title: Vue中的sync修饰符
date: 2019-12-29 16:43:27
categories: [学习笔记]
tags: [饥人谷, Vue]
---

## sync修饰符的作用

根据Vue的中文文档中的[这部分内容](https://cn.vuejs.org/v2/guide/components-custom-events.html#sync-%E4%BF%AE%E9%A5%B0%E7%AC%A6)，sync修饰符主要用来对prop进行“双向绑定”。
<!-- more -->
一般而言，Vue中的数据只能通过父组件以prop的形式传递给子组件，形成一个自父组件流向子组件的单向数据流。

正是由于这种明显的单向数据流的设计模式，Vue中会在父组件数据更新时自动更新子组件的prop的值，与此同时，Vue中不推荐在子组件内更改prop，因为这明显违背了单向数据流的设计模式。

但是，在某些场景下，我们需要在子组件内更新父组件的值呢？

假设我们有一个父组件，展示一个total数字，并有两个按钮可以对数字进行加一减一操作，同样的，也有一个子组件，展示相同的数字，有两个按钮，对数字进行加一减一操作，我们可能需要写出如下代码
<!-- markdownlint-disable md033 -->
<details>
<summary>父组件</summary>

```html
<template>
  <div id="app">
    我是父组件 total:{{ total }}
    <button @click="total += 1">+1</button>
    <button @click="total -= 1">-1</button>
    <hr />
    <Child :childTotal="total" @update:childTotal="total = $event"></Child>
  </div>
</template>

<script>
import Child from "./components/Child.vue";
export default {
  name: "app",
  data() {
    return {
      total: 1000
    };
  },
  components: {
    Child
  }
};
</script>
```

</details>

<details>
<summary>子组件</summary>

```html
<template>
  <div>
    我是子组件 total:{{ childTotal }}
    <button @click="$emit('update:childTotal', childTotal + 1)">+1</button>
    <button @click="$emit('update:childTotal', childTotal - 1)">-1</button>
  </div>
</template>

<script>
export default {
  props: {
    childTotal: Number
  }
};
</script>
```

</details>

这里父组件实现对数据的加一减一操作很好理解，但在子组件这里，由于不推荐直接操作prop，而且直接修改prop时，父组件数据无法更新，所以子组件通过注册一个"update:childTotal"事件，并在父组件内对事件进行监听来修改父组件内total的值，在通过父组件将修改后的值重新传递给子组件的prop来实现。

可见，通过一个事件中心注册监听事件，我们可以实现此功能。

但是由于这个需求比较常见，而且这样书写起来比较麻烦，所以Vue提供了一个语法糖，也就是.sync修饰符。

## 如何使用.sync修饰符

同样的功能，我们在父组件内可以使用.sync修饰符，来减少代码，只需将上述的如下代码

```html
<Child :childTotal="total" @update:childTotal="total = $event"></Child>
```

修改为如下

```html
<Child :childTotal.sync="total"></Child>
```

可见，使用.sync修饰符之后，我们无需显示的监听“update:childTotal"事件，但与此同时，我们仍需在子组件内显示的注册一个“update:childTotal"事件，并为total提供一个新的值。

以上，便是我对.sync修饰符的一些总结。