---
title: Blazingly Fast JavaScript
date: 2024-03-26 00:20:58
categories: 学习笔记
tags: [FrontEndMasters]
---

# Blazingly Fast JavaScript

## Introduction

- [Course Website](https://theprimeagen.github.io/fem-jsperf/)

What this course is about > 这个课程是关于什么的
This course is as much a "performance" course as its a javascript fundamentals. If you stick through the whole course you will learn a about how javascript works > 这门课程既是关于性能的课程，也是关于 JavaScript 基础的课程。如果你坚持学完整个课程，你会学到很多关于 JavaScript 的工作原理
These are transferrable skills. Any other GC based language will have similar concepts > 这些是可转移的技能。任何其他基于 GC 的语言都会有类似的概念
Its also transferrable between bun and node and the browser > 它也可以在 bun 和 node 以及浏览器之间转移
We will not talk about the dom today > 今天我们不会讨论 dom
It will make you write better code. Better code in this case delivers what the customer wants but smoother, crispier, and smothered in coconut oil > 它会让你写出更好的代码。在这种情况下，更好的代码会让客户更顺畅、更清晰，并且涂上椰子油
we will not deep dive into perf or chrome-tracing or v8-tracing
we will not talk about "micro" optizimations (property ordering, monomorphism) > 我们不会深入研究性能或 chrome-tracing 或 v8-tracing, 我们不会讨论“微”优化（属性排序，单态性）

<!-- more -->

### Requirements

- [Rust](https://www.rust-lang.org/tools/install)
- [Golang](https://golang.org/doc/install)
- [Node 20+](https://nodejs.org/en/)
- [Repo](https://github.com/ThePrimeagen/js-perf-example)

Two quick things

1. We will do optimizations step by step and independently and combine them to see the real value of each change. > 我们将逐步独立地进行优化，并将它们组合起来，以查看每个更改的真实价值
2. We are using local host to test. this comes with a series of optimizations and can be a bit misleading. At the end of the day, real tests should be done in production environments, but that doesn't mean you cannot make a series of great improvements otherwise. (programs running) > 我们使用本地主机进行测试。这带来了一系列的优化，可能会有点误导。归根结底，真正的测试应该在生产环境中进行，但这并不意味着你不能做一系列很好的改进（程序运行）

## Benchmarking & Performance Monitoring

- 使用 performance 面板进行性能监控，根据耗时从长到短进行优化

### Sets vs Arrays

- 数据规模越大，Set 的性能优势越明显，因为 Set 的查找时间复杂度是 O(1)，而 Array 是 O(n)
- 数据规模小的时候，Array 的性能优势更明显

## Garbage Collection & Async Code

- [V8 Garbage Collection](https://v8.dev/blog/trash-talk)

  - Major GC: stop the world, mark and sweep
    - The major GC collects garbage from the entire heap.
    - Start at a set of known objects pointers, called root set(includes execution stack, global object)
    - Sweep all the objects that are not reachable

- Compaction

  > 主要 GC 还会根据碎片化启发式选择疏散/压缩一些页面。你可以把压缩看作是在旧 PC 上的硬盘碎片整理。我们将幸存的对象复制到其他当前未压缩的页面中（使用该页面的空闲列表）。这样，我们就可以利用死对象留下的内存中的小而分散的间隙。
  > The major GC also chooses to evacuate/compact some pages, based on a fragmentation heuristic. You can think of compaction sort of like hard-disk defragmentation on an old PC. We copy surviving objects into other pages that are not currently being compacted (using the free-list for that page). This way, we can make use of the small and scattered gaps within the memory left behind by dead objects. - [V8 Garbage Collection](https://v8.dev/blog/trash-talk)

- Minor GC: Scavenge
  - This is the second form of GC in v8 and this follows the "Generational Hypothesis" > 这是 v8 中的第二种 GC 形式，遵循“代假设”
  - This basically says that most objects die young > 这基本上是说大多数对象寿命很短
  - In other words, most objects are allocated and then almost immediately become unreachable, from the perspective of the GC > 换句话说，从 GC 的角度来看，大多数对象被分配后几乎立即变得不可访问
- How minor GC works

  - Its more complicated than the major GC, but effectively it uses a few techniques and a different set of "roots` to calculate life. 1/2 of the minor GC space is left empty and objects are copied "From Space" to "To Space" every GC. Only the "alive objects" are copied over to "To Space" > 它比主要 GC 更复杂，但实际上它使用了一些技术和一组不同的“根”来计算生命。副垃圾回收器空间的一半留空，每次 GC 都会将对象从“From Space”复制到“To Space”。只有“活着的对象”才会被复制到“To Space”

- To Recap

  - Major GC (Full Mark-Compact): A stop the world, full heap crawl of all the objects created > 主 GC（完全标记-压缩）：全暂停（stop the world），对堆中创建的所有对象进行全面遍历
    - typically more rare > 通常更少见
    - typically takes significantly longer than Minor GC > 通常比副 GC 花费的时间长得多
    - Sometimes has to compact a memory region due to fragmentation > 有时必须压缩内存区域以解决碎片化问题
  - Minor GC (Scavenger): GC collecting only from the "nursery" or young generation. > 副 GC（Scavenger）：GC 仅从“幼儿园”或年轻代收集
    - typically more frequent > 通常更频繁
    - relatively "fast" > 相对“快”

- 实际上跟对象的大小也有关系，副垃圾回收器内存区域的大小是固定的，如果对象太大，会直接进入主垃圾回收器

- This is a brief introduction to GC

  - Hopefully the point that got across. {} are a bit more expensive than you may have realized. Perhaps you will think more about Array#map and Array#filter from here on out > 这是对 GC 的简要介绍，希望你能理解。{} 比你想象的要贵一些。也许从现在开始，你会更多地考虑 Array#map 和 Array#filter
  - really short lived objects can be cleaned efficiently > 真正短命的对象可以被高效地清理
  - longer lived, across a couple promises, those can be worse overall > 更长寿的对象，跨越几个 promise，这些可能会更糟糕

- Some Notes
  - Major and Minor GCs do have some parallelism to it and even runs while JavaScript is running. The article linked goes into great detail about it and its quite clever. But remember, if you have a program getting tons of messages from the internet, and you are getting bogged down by GC, and its not a powerful machine, like mine, GC can have a more disproportional effect > 主 GC 和副 GC 确实有一些并行性，甚至在 JavaScript 运行时运行。链接的文章详细介绍了这一点，而且非常聪明。但请记住，如果你的程序从互联网上收到大量消息，并且你被 GC 拖慢了速度，而且你的电脑配置不是很高，就像我的一样，GC 可能会产生更大的不成比例的影响

### Memory Profiling

- [Memory Profiling](https://developers.google.com/web/tools/chrome-devtools/memory-problems)
- 使用 memory 面板进行内存监控，根据内存占用从大到小进行优化
- 这里的 profiling type 选择了 Allocation Sampling，这个选项会在每次 GC 时记录内存分配的堆栈信息，这样可以更好地定位内存泄漏

### Event Loop & Promises

- [Event Loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)
- Promise 并不 cheap，每个注册在 then/catch 的回调都会创建一个新的对象，并且会在微任务队列中排队，这会导致内存占用增加
- 谨慎使用 async/await，因为它会创建一个新的 Promise 对象，从而增加 GC 的负担

- 即使优化之后显示 GC 的时间减少，或者某个任务的执行时间减少/内存占用减少，但是在实际应用中，可能并不会有明显的性能提升

### Speeding Up Web Sockets

- 使用其他语言编写的 WebSocket 包，可以减少 GC 的负担，提高性能
  - WS to [UWS](https://github.com/uNetworking/uWebSockets.js)

## Update & Memory Optimization

### Refactoring Collision

- 这里的优化主要是针对特定场景
  - 之前的是一个通用的判断：每一帧都判断是否有子弹与敌人相撞
  - 但其实在有多个子弹的情况下，只有第一个子弹与敌人相撞时，才会有碰撞效果
  - 有点像 react 在 diff 算法中的 key，只有 key 相同时才会进行更新，否则会重新渲染，通过放弃重新排序来减少 diff 的复杂度

### Memory Pool

- 通过创建一个对象池，减少对象的创建和销毁，从而减少 GC 的负担

```ts
class Pool<T> {
  private pool: T[] = [];
  private create: () => T;

  constructor(create: () => T) {
    this.create = create;
  }

  get(): T {
    if (this.pool.length > 0) {
      return this.pool.pop()!;
    }
    return this.create();
  }

  set(obj: T) {
    this.pool.push(obj);
  }
}
```

### Optimizing Logging

- 通过减少 log 可以降低内存占用（log 中的变量会被保存在内存中）

### Other Optimizations Considerations

- 可以尝试使用 JSON 以外的序列化方式
- 使用 LinkedList 代替 Array，因为 Array 的删除操作会导致内存的移动，而 LinkedList 只需要修改指针

### Wrapping Up

- The goals of this course

  - The goal of this course is to make you better at JavaScript and to understand why such a simple piece of syntax such as `{}` has so much weight. > 本课程的目标是让你更擅长 JavaScript，并理解为什么一个简单的语法如 `{}` 会有如此重要的作用。
  - But not all fixing of software is going to be this easy. What we did is observe and fix, but some software there is no obvious issues. This is the problem with code that is written sloppy over course of many functions. > 但并不是所有的软件修复都会这么容易。我们所做的是观察和修复，但有些软件没有明显的问题。这就是代码写得很松散的问题，经过多个函数的过程。

- Sets of tools
  - performance and memory tabs in chrome > chrome 中的性能和内存选项卡
  - node's async stuff is slow(for now) > node 的异步操作很慢（目前）
  - memory is a good indication to where problem could be > 内存是问题所在的好指示
  - GC time a good indicator of issues > GC 时间是问题的一个很好的指标
  - Sets are not always better than arrays > Set 并不总是比 Array 更好
