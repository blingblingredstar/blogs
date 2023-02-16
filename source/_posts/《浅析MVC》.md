---
title: 《浅析MVC》
date: 2019-11-27 16:05:20
categories: [学习笔记]
tags: [饥人谷, MVC, EventBus, 表驱动编程, 模块化]
---
## 开篇

近期在学习设计模式，了解到了几个术语，分别为

1. MVC
2. EventBus
3. 表驱动编程（数据驱动编程）
4. 模块化

在这里，我将总结一下目前我对这几个术语的理解，相信随着对这几个术语更加深入的接触与了解，在今后也会有不同的理解。
<!-- more -->
## MVC

JavaScript在设计之初只是为了做一些表单验证等简单工作的脚本语言，但随着互联网的蓬勃发展，使用JavaScript所完成的需求也越来越复杂。在几年前，使用jQuery一把嗦的设计思路还能满足绝大部分需求，但随着需求的日益复杂与规模的日益庞大，像以往那样使用jQuery来手动管理DOM节点来实现数据的增删改查也产生了几个问题，其中就包括了数据与视图的紧密耦合，也就促使开发者们将MVC思想引入了前端开发之中。

那么，什么是MVC，MVC又为什么会流行在前端开发之中呢？

MVC是一种历史悠久的设计模式或者说架构模式了。M意为Model，代表了数据，V意为View，代表视图，C意为Controller，代表控制器。其中数据和视图很好理解，那么控制器是什么呢？我理解为，控制器负责响应用户的操作，用户不能直接操作数据与视图，而是通过控制器来操作。

下面我们来用代码来说明，在这里我们实现一个简单的功能，有以下HTML元素，

```html
<div class="buttons">
  <button id="add1">+1</button>
  <button id="min1">-1</button>
</div>
<div id="number">0</div>
```

我们实现的功能就是点击+1按钮数字+1，-1按钮数字减一，为了简化DOM操作，引入了jQuery，使用过程式操作，我们可能写出如下代码？

```js
import $ from "jquery";

const $number = $("#number");

$("#add1").on("click", () => {
  const oldNumber = +$number.text();
  const newNumber = oldNumber + 1;
  $number.text(newNumber + "");
});

$("#min1").on("click", () => {
  const oldNumber = +$number.text();
  const newNumber = oldNumber - 1;
  $number.text(newNumber + "");
});
```

使用MVC思想，可能写出如下代码：

```js
const Model = {
  _data: {
    number: 0
  },
  get() {
    return this._data;
  },
  update(newData) {
    this._data = newData;
  }
};

const View = {
  element: document.querySelector("#number"),
  render(content) {
    this.element.textContent = content;
  }
};

const Controller = {
  events: {
    "#add1 click": "add1",
    "#min1 click": "min1"
  },
  autoBindEvents() {
    Object.keys(this.events).forEach(key => {
      const value = this.events[key];
      const handler = this[value];
      const [selector, event] = key.split(" ");
      document.querySelector(selector).addEventListener(event, handler);
    });
  },
  add1() {
    const oldValue = Model.get().number;
    const newValue = oldValue + 1;
    Model.update({ number: newValue });
    View.render(newValue);
  },
  min1() {
    const oldValue = Model.get().number;
    const newValue = oldValue - 1;
    Model.update({ number: newValue });
    View.render(newValue);
  },
  init() {
    View.render(Model.get().number);
    this.autoBindEvents();
  }
};

Controller.init();
```

可以看出，在使用MVC反倒是提升了代码量。但是我们实现的功能比较简单，如果我们有多个按钮呢？如果我们的功能需求发生变更呢？使用过程式思想，我们需要逐行分析代码，手动追踪实现流程。而使用MVC思想，我们可以根据发生变化的部分来相对准确的定位所要修改的部分。

我们在编写程序时，一直追求高内聚低耦合的原则。使用过程式思想，我们将数据视图与用户操作耦合在了一起，在更改时牵一发而动全身。而是用MVC模式，我们仅仅需要追踪需要变化的部分即可。虽然在功能需求简单时增加了代码量与思维负担。但在业务规模庞大，业务逻辑复杂是会降低我们的思维负担与修改成本。

## EventBus

在上面的代码中，我们可以发现使用MVC之后，在每次用户点击之后，我们都要重新渲染视图与更新数据，这说明在上面要实现的功能中数据是与用于视图绑定在一起的，及数据更新时用户视图也要同步更新。

那么，我们就有必要设计一种方法，不必每次手动调用，而是在数据更新时自动更新视图。这里，我们可以使用EventBus，先给出代码如下：

```js
import $ from "jquery";

const EventBus = $({});

const Model = {
  _data: {
    number: 0
  },
  get() {
    return this._data;
  },
  update(newData) {
    this._data = newData;
    EventBus.trigger("data_updated");
  }
};

const View = {
  element: document.querySelector("#number"),
  render(content) {
    this.element.textContent = content;
  }
};

const Controller = {
  events: {
    "#add1 click": "add1",
    "#min1 click": "min1"
  },
  autoBindEvents() {
    Object.keys(this.events).forEach(key => {
      const value = this.events[key];
      const handler = this[value];
      const [selector, event] = key.split(" ");
      $(".buttons").on(event, selector, handler);
    });
  },
  add1() {
    Model.update({ number: Model.get().number + 1 });
  },
  min1() {
    Model.update({ number: Model.get().number - 1 });
  },
  init() {
    this.autoBindEvents();
    EventBus.on("data_updated", () => {
      View.render(Model.get().number);
    });
  }
};

Controller.init();
```

这里主要的我借用了jQuery中的EventBus来实现EventBus，修改主要于在Model的update方法里添加了EventBus.trigger方法来在每次更新数据时触发事件，在Controller.init方法中使用了EventBus.on方法来监听数据更新时触发的事件。

可以看出EventBus类似document.addEventListener，不同的是addEventListener由用户指定操作触发，EventBus由程序设计者使用trigger方法来指定触发时机及触发事件名称。与addEventListener一样拥有监听事件及卸载监听事件的方法，在jQuery中为on与off方法。

通过EventBus，我们可以简化Controller的操作，在数据更新时自动更新视图，从而可以使我们的注意力可以集中在数据操作上，极大地减轻了我们的思维负担。

## 表驱动编程

在Controller中，在绑定按钮点击事件我使用了如下的实现方式

```js
const Controller = {
   events: {
    "#add1 click": "add1",
    "#min1 click": "min1"
  },
  autoBindEvents() {
    Object.keys(this.events).forEach(key => {
      const value = this.events[key];
      const handler = this[value];
      const [selector, event] = key.split(" ");
      $(".buttons").on(event, selector, handler);
    });
  },
}
```

在之前我可能会逐一查找对应元素，分别注册绑定事件。类似如下：

```js
document.querySelector('#add1').addEventListener('click', add1)
document.querySelector('#min1').addEventListener('click', min1)
```

可以看到，上述代码只有选择器及回调函数不同。为了减少重复，更重要的是将事件与注册事件解耦，我们可以使用表编程或数据编程方法，将数据与注册事件分开书写，创建一根据数据内容自动注册事件的方法。这样在不仅可以使我们注意点更加集中，也简化了需要扩展功能是所需的操作。

## 模块化

MVC设计思想，EventBus，以及表驱动编程都在一定程度上帮助我们实现了程序设计的高内聚低耦合。那么提到高内聚低耦合，就不得不提模块化。

随着前端工程规模日益庞大，功能也愈加复杂，也就自然而然的提出了模块化的需求。

在过去需求简单的时候，我们可能仅仅需要引入jQuery一个库，就可以完成日常的开发工作。这样减少了网络请求，提高了加载速度，但这也限制了我们所编写代码的组织方法。随着Node.js的流行，webpack等打包工具的出现，模块化开发也成为了前端开发的主流。

通过模块化开发，我们可以保持命名空间的隔离。在前端开发中，我们不能像后端一样控制程序的使用环境，浏览器插件及广告等很有可能污染运行环境，造成命名冲突等种种问题，通过模块化开发，我们可以降低这种可能。

我们也可以通过模块化来实现开发时的关注点分离，同时也使得单元测试更加方便。在理想情况下，我们可以只关注模块内的代码，并为其编写测试用例而不用担心影响模块外的内容。通过把大型应用拆解成模块，我们可以降低应用的复杂度，使得功能的添加与修改更加方便。

通过模块化开发与打包工具的结合，我们还可以减少代码体积。假如我们需要使用lodash中的某个方法，我们可以不必想以前一样引入整个lodash库，而是可以按需引入，仅仅导出我们所需要的功能，这可以在相当程度上提高我们的开发效率，减少代码体积。

以上就是我对于最近所学内容的一些思考，希望有不对的地方还请大家批评指正。
