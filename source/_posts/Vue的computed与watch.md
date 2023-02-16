---
title: Vue的computed与watch
date: 2019-12-19 19:17:39
categories: [学习笔记]
tags: [饥人谷, Vue]
---
## Computed

computed在Vue中文文档中称为计算属性，这里我总结了一下[中文文档中关于这部分](https://cn.vuejs.org/v2/guide/computed.html)的内容。

### computed的作用

顾名思义，计算属性一般依赖于其他属性，根据其他属性的值计算出结果作为属性值。
<!-- more -->
这里举一个简单的例子，假设后端同学返回给我们一个用户订单列表数组，我们希望在页面上为用户展示总金额，那么我们就可以书写如下代码

```js
const vm = new Vue({
    el: "#app",
  template: `
    <div>
      total: {{ total }}
      <hr/>
      <button @click="addGood">add good</button>
      <ul>
        <li v-for="good in goods" key="good.id">
          name: {{ good.name }}<br/>
          price: {{ good.price }}
        </li>
      </ul>
    </div>
  `,
  data: {
    goods: [
      genGood("bike", 100),
      genGood("basketball", 50),
      genGood("drink", 10)
    ]
  },
  computed: {
    total() {
      return this.goods.reduce((total, good) => {
        return (total += good.price);
      }, 0);
    }
  },
  methods: {
    addGood() {
      this.goods.push(genGood("shirt", 30));
    }
  }
})
```

运行后可以看到，当我们点击按钮增加商品数量时，计算属性total的值也会随之增加。

这里注意，在这里我们根据goods属性计算出total属性的值，在上述代码中，我们在data对象注册了goods数组，使得数组变为响应式属性，所以computed会在goods变化时重新进行计算。但如果我们所依赖的属性是非响应式的，Vue无法感知变化，就不会重新计算。

### computed的getter与setter

在上述代码中，我们使用computed时直接使用了一个函数来作为属性值，这里的函数会作为属性的getter使用。我们也可以将一个对象作为计算属性的属性值，分别指定属性的getter和setter。

### computed与method

但是其实我们也可以使用method来动态获取商品总价，代码类似如下

```js
new Vue({
  el: "#app",
  template: `
    <div>
      total: {{ updateTotal() }}
      <hr/>
      <button @click="addGood">add good</button>
      <ul>
        <li v-for="good in goods" key="good.id">
          name: {{ good.name }}<br/>
          price: {{ good.price }}
        </li>
      </ul>
    </div>
  `,
  data: {
    goods: [
      genGood("bike", 100),
      genGood("basketball", 50),
      genGood("drink", 10)
    ]
  },
  methods: {
    addGood() {
      this.goods.push(genGood("shirt", 30));
    },
    updateTotal() {
      return this.goods.reduce((total, good) => (total += good.price), 0);
    }
  }
});
```

这里的区别官方文档也给了我们答案，使用computed时，属性值会根据响应式依赖进行缓存，也就是说如果商品列表里的值没有改变，那么就不会重新计算。但是如果我们使用方法，那么显然每调用一次就需要重新计算一次。

## watch

watch在中文文档中成为侦听器，也叫侦听属性。与computed不同的是，computed会在Vue实例对象上添加一个属性，而watch一般用来给已有的响应式属性已定义响应方法。

当我们在Vue注册data对象时，Vue会将data对象的属性转换为响应式的。在数据变化时会自动调用注册在属性上的依赖进行更新视图或其他操作。而watch就是当我们不满足于Vue的默认响应式系统，而需要在数据变化时进行一些自定义操作时使用的。

这里简单举个例子

```js
new Vue({
  el: "#app",
  template: `
    <div>
      {{ number }}
      <button @click="add1">+1</button>
    </div>
  `,
  data: {
    number: 0
  },
  methods: {
    add1() {
      this.number++;
    }
  },
  watch: {
    number(newVal, oldVal) {
      // 在点击按钮时，data上的number属性会改变
      // 此时Vue会侦听到变化，控制台会打印出number之前的值和现在的值
      console.log(`old value: ${oldVal}, new value: ${newVal}`);
    }
  }
});
```

### watch的使用

watch的使用有多种方法，具体可见[官方文档](https://cn.vuejs.org/v2/api/#watch)，简单来说，watch接收一个对象，键名是需要观察的表达式，值可以使处理函数或者包含选项的对象。

在处理函数中，接收两个参数，分别为所监听的属性状态变化之前的值和变化之后的值。

选项对象可以指定是否在Vue实例初始化之后立即调用处理函数还是状态变更后调用；以及与computed不同的是，watch可以监听数据对象，并可以设置选项来监听数据对象内部属性变化，无论套嵌多深。

通过API的设计也可以看出，计算属性一般用于添加依赖于现有响应式属性的值而计算出的属性值。而watch则可以更细粒度的对现有响应式属性进行侦听，以便在属性变化时进行一些自定义操作。

这里官方文档也提示我们，在一些数据依赖其他数据而变动时，不要滥用watch，在watch内进行一些命令式的回调，而是使用更加声明式的computed。

以上便是我对于computed与watch的一些理解。
