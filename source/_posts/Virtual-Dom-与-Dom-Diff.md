---
title: Virtual DOM 与 DOM Diff
date: 2021-05-03 19:34:56
categories: [学习笔记]
tags: [React, Vue, Virtual DOM, DOM Diff]
---

> 最近面试比较多，常见问题其实也就那么几个，Virtual DOM 与 DOM Diff 算是绕不开的一个，毕竟主流框架为了提高性能，常见策略就是引入 Virtual DOM 与 DOM Diff，二者往往共同存在，这里也写篇文章记录下我当前的理解。
> 本文大部分内容来自于极客时间，李兵老师的《浏览器工作原理与实践》中 [《虚拟 DOM：虚拟 DOM 和实际的 DOM 有何不同？》](https://time.geekbang.org/column/article/144569?utm_source=time_web&utm_medium=menu&utm_term=timewebmenu)

<!--more-->

## 什么是 Virtual DOM

> tldr: 一个 JS 对象

这样的解释显然不能让面试官满意，也对我们理解 Virtual DOM 产生不了太多帮助。不过，要回答什么是 Virtual DOM，我觉得首先还是要回答一个前置问题，既 DOM 的缺陷。

### DOM 的缺陷

DOM(Document Object Modal)文档对象模型是浏览器暴露出来可供开发者调用的 API，用于操作页面文档内容，这点对于一个前端工程师来说，每天都要打交道，也不必多说。

一般情况下，我们要对 DOM 进行操作，无外乎 appendChild、removeChild、innerHtml、getElementById 等增删改查。当我们操作一个 DOM 元素时，浏览器会在背后做一系列的事情，这部分工作对于前端日常开发来将，往往是透明的，但作为一名合格的前端工程师，必须对此有一定了解，否则一些问题的排查以及页面性能指标的优化也就无从谈起。

例如当我们使用 document.body.appendChild 在 body 中添加一个子元素时，浏览器的渲染引擎会在添加节点时触发样式计算、布局、绘制、栅格化及合成等一系列任务，既**重排**。除此之外，更改样式或者其他属性有可能触发**重绘**或者**合成**操作。当我们操作不当时，也有可能**触发强制同步布局**与**布局抖动**(具体可见[这里](https://developers.google.cn/web/fundamentals/performance/rendering/avoid-large-complex-layouts-and-layout-thrashing?hl=zh-cn))。

### 引入 Virtual DOM 后是如何解决以上问题的（feat：Virtual DOM 的优点）

通过引入 Virtual DOM，可以从以下三方面解决上述问题

1. 我们可以将改变的内容首先应用到 Virtual DOM 而不是真实 DOM 上
2. 当 Virtual DOM 变化时，并不立刻渲染页面，而是仅仅调整 Virtual DOM 的状态，由于 Virtual DOM 仅仅是普通 JS 对象，操作成本非常轻量。
3. 当收集到一定程度的改变时，将变化批量更新到真实 DOM 上。

在[《计算机科学速成课》](https://www.bilibili.com/video/BV1EW411u7th?from=search&seid=15491133433427730338)(非常基础但很有意思的一门课程)中，一般我们为了解决计算机发展中的一些问题，就会提出一层新的抽象(New level of abstraction)，通过一层抽象，来将问题隔绝在此层，Virtual DOM 也是这样一层抽象。

通过以上三个设计思路，我们就有了 Virtual DOM 这个 JS 对象作为一个中间层来进行一些 DOM 操作的收集及对比，最终将变化批量更新至真实 DOM 中，从而做到尽可能小的 DOM 变化，以及 DOM 的批量更新，普通方式操作 DOM 可能产生的问题。

以上为纯技术视角，通过引入 Virtual DOM 这一层抽象，我们还可以从以下两种视角来分析下 Virtual DOM

1. 双缓存

   由于引入了 Virtual DOM，我们就可以不必在 DOM 变化时立刻操作 DOM，而是将变化收集起来，放在 Virtual DOM 中收集起来批量更新。这方面更多的是 DOM Diff 相关的一些问题，下面会更详细的分析。同时这里的思想，个人感觉也与李兵老师在《图解 Google V8》中[《20 | 垃圾回收（一）：V8 的两个垃圾回收器是如何工作的？》](20 | 垃圾回收（一）：V8 的两个垃圾回收器是如何工作的？)一文中介绍的 v8 的垃圾回收机制中针对新生代对象的副垃圾回收器中采用的 Scavenge 算法很类似。

2. MVC 模式

   通过引入 Virtual DOM，我们可以不必关心数据变更后视图如何进行更新，这部分的工作完全交于框架所提供的 Virtual DOM 以及 DOM Diff 即可，我们在开发时只需关心数据与 DOM 间的映射关系。从以前 JQuery 时代需要在数据变更后手动同步 DOM 状态变为只需关心数据是如何变化及流动的，相当大程度的减少了思维负担。

同时，也由于引入了 Virtual DOM 这一层新的抽象，我们可以不必关系底层 DOM 具体如何更新，在 React 中可以交由 ReactDOM 解决，React 本身只提供 Virtual DOM 即可。这也给框架提供了解决兼容性及跨平台问题提供了种种可能，我们可以通过各种其他类库，将 Virtual DOM 映射为 IE 版本的 DOM 或者小程序等其他平台的视图。

### Virtual DOM 所带来的问题(feat: Virtual DOM 的缺点)

如同计算机的其他问题一样，Virtual DOM 也不是 silver bullet，本身也会带来一些问题。

首先是书写问题。在 React 官方文档中，第一个示例为以下代码

```js
const e = React.createElement;

class LikeButton extends React.Component {
  constructor(props) {
    super(props);
    this.state = { liked: false };
  }

  render() {
    if (this.state.liked) {
      return "You liked this.";
    }

    return e(
      "button",
      { onClick: () => this.setState({ liked: true }) },
      "Like"
    );
  }
}
```

很明显，使用 createElement 生成 Virtual DOM 的语法很不直观。当然，我们一般开发时并不会如此书写，一般会使用 babel 所提供的 jsx 语法，通过 babel 转换为上述语法。

另一个主要问题，是性能问题。

是的，虽然 Virtual DOM 的引入解决了一部分操作 DOM 所带来的性能问题，但是引入 Virtual DOM 本身也不是没有任何性能代价的。

而且 Virtual DOM 的 diff 算法也进行了取舍(trade off)，对常用场景进行了优化，其他场景则舍弃了性能。这方面会在下面更为详细的说明。

对于一写高性能应用，往往采用人工操作 DOM 的方式更为高效，毕竟，人比算法聪明，可以针对不同场景采用不同手段进行性能优化。具体可见[知乎-vscode 为什么选择直接使用 DOM API 而不借助现有的前端框架来进行开发？](https://www.zhihu.com/question/318928283/answer/644372273)。

## 什么是 DOM Diff

上面讲完了 Virtual DOM，那么伴随着 Virtual DOM 的，往往是 DOM Diff。

在 Virtual DOM 变化后，我们要获取到变化的部分，并将变化部分映射到真实 DOM 上，其中获取 Virtual DOM 变化部分这一过程就是 DOM Diff。

说道 Diff，除了 DOM Diff，在我们日常使用 git 开发时，git 就是通过 diff 这一操作来获取新旧文件间的变化。

传统的 diff 算法，为了更细粒度的获取到变化，时间复杂度往往达到了 O(n3)的级别(左右子树同时深度遍历，同时为操作节点增删移动，还要进行一次遍历)，这就意味着对 1000 个节点的 diff 要进行 10 亿此比较，而一个页面往往几千至几万个节点，这个性能对于日常开发可以说是不可接受的。

React 中的 DOM Diff 为优化此问题，对 Diff 操作进行了取舍，忽略跨层级移动复用同一 DOM 这个场景，将 Diff 操作大致分为三个操作

1. tree diff
2. component diff
3. element Diff

首先对两个 Virtual DOM tree 进行同层比较，只比较新老 DOM 树的用一层级节点是否变化，节点内容时 component 就进行 component diff，是 element 就进行 element diff。

在 component diff 中，首先比较组件类型，如果类型不同直接替换，类型相同则比较属性进行更新，并对组件 children 进行 tree diff

在 element diff 中，还是首先比较标签类型，不同则直接替换，相同则比较属性进行更新，最后也是对标签的 children 进行 tree diff

这样，react 可以在 O(n)的时间复杂度，既一次遍历即可完成整个 diff 操作。

### DOM Diff 所存在的一些问题(DOM Diff 的缺点)

首先不得不将的，也可能是面试官想要听到的，就是为什么在遍历数组时要增加 key 属性的问题。

对于静态内容来说，由于叶子节点的数量和位置是固定的，所以在 diff 操作时，可以放心大胆的进行比较。但当节点的 children 为数组动态渲染出来时，由于节点的数量和位置不固定，在进行删除和移动时由于时间复杂度过高，无法进行深度比较，所以此时需要开发者手动添加一个 key 属性，帮助 diff 算法识别节点是否变化，这样当 diff 比较数组中各个节点时，可以直接比较新旧 key 属性是否相同。

其次是 diff 操作本身带来的性能问题。

不论是 react 和 vue，每次渲染之前都需要调用 diff 算法遍历新老 Virtual DOM tree。这样相对于直接操作 DOM，每次 diff 操作所消耗的时间就无法避免，即使我们只操作简单的 DOM 变化，也要完整执行 diff 算法。

对此，Vue 和 react 也走向了两种不同的优化路径。

React 提出了异步渲染，也就是最新的 fiber 架构及 concurrent mode，既放弃以前的同步的、递归式的 diff 及渲染过程，改为异步、可中断的 fiber 架构，通过 diff 及渲染的可中断，保持对用户交互等高优先任务的响应。

Vue 则采用了更细粒度的依赖收集策略，通过 compile time 时对 template 中静态内容与动态内容的分析，来最小化的生成需要 Virtual DOM 与 DOM diff 的内容，从而达到最小化判断及更新的目标，以此优化性能。

以上，便是我对于 Virtual DOM 以及 DOM diff 的一点认知，不对之处还望批评指正。
