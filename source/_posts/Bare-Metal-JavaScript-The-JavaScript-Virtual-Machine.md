---
title: 'Bare Metal JavaScript: The JavaScript Virtual Machine'
date: 2023-10-29 13:17:22
categories:
tags: [FrontEndMasters, JavaScript]
---

# Bare Metal JavaScript: The JavaScript Virtual Machine

## Introduction

`==` can be 15 times slower than `===` in some cases.

`(a, b) => a + b` is 3.6x faster than `(a, b) => b + a`

`a = 0 - x` is about 3-10x faster than `a = -x`

- Agenda

  - Introduction to VMs
  - CPU - Understanding the the Physical Machine > 中央处理器 - 理解物理机器
  - VMs - Arrays, Objects, functions, prototype chains > 虚拟机 - 数组、对象、函数、原型链
  - DeoptExplorer: collecting data from the VM > DeoptExplorer：从虚拟机收集数据
  - Deopt: calling conventions & inlining > Deopt：调用约定和内联
  - Megamorphism & Inline Cache: Object properties > Megamorphism 和内联缓存：对象属性
  - Holey Arrays: Prototype chains resolution > Holey 数组：原型链解析
  - == vs ===: `valueOf()`

<!-- more -->

## How the CPU Runs Code

### Physical & Virtual Machines

- Physical Machine(CPU) > 物理机器（CPU）

  - Numbers > 数字
  - Arithmetic operations > 算术运算
  - Flat memory > 平面内存
  - Conditional jumps > 条件跳转
  - Subroutine > 子程序

- Virtual Machine > 虚拟机
  - Strings > 字符串
  - Local variables > 局部变量
  - Memory management(gc) > 内存管理（垃圾回收）
  - Conditionals: if/switch > 条件：if/switch
  - Loops: for/while > 循环：for/while
  - Primitive types > 原始类型
  - Objects/Arrays > 对象/数组
  - Classes > 类
  - Functions/Closures > 函数/闭包
  - Exceptions/Stack traces > 异常/堆栈跟踪

### CPU basics & Speed limits

- CPU 由于电路的限制，每个电路的运行速度都是有限的，所以 CPU 的运行速度也是有限的
- 解决方案：多核 CPU
- 并行计算的话，需要考虑数据的依赖关系，比如 `a = b + c`，`b` 和 `c` 之间没有依赖关系，可以并行计算，但是 `a` 和 `b` 之间有依赖关系，需要等 `b` 计算完才能计算 `a`，所以并行计算的效率并不是很高
- 解决方案：speculative execution(分支预测？)
  - 通过猜测下一步的指令，来提前计算，提高 CPU 的利用率
  - 但是如果猜错了，就需要回滚，这样就会浪费 CPU 的资源
  - 通过分支预测来解决这个问题
    - 通过分支预测，来猜测下一步的指令
    - 如果猜对了，就可以提前计算，提高 CPU 的利用率
    - 如果猜错了，就需要回滚，浪费 CPU 的资源
    - 通过分支预测，可以提高 CPU 的利用率，但是也会浪费 CPU 的资源
- Scepter(权杖)：分支预测的算法

  - 通过分支预测，来猜测下一步的指令
  - 如果猜对了，就可以提前计算，提高 CPU 的利用率
  - 如果猜错了，就需要回滚，浪费 CPU 的资源
  - 通过分支预测，可以提高 CPU 的利用率，但是也会浪费 CPU 的资源
  - 前几年，Intel 的 CPU 出现了一个漏洞，就是 Spectre 漏洞，通过 Spectre 漏洞，可以读取 CPU 的缓存，从而读取到敏感信息
    - Zero day specter

- JavaScript 是单线程，所以每次只能用到一个 cpu 核心，所以 JavaScript 的性能瓶颈在于 CPU 的性能
  - JavaScript 虽然有 service worker，但并不像 go 等语言那样，可以利用方便的并行计算，例如内存不共享，在传递数据的时候，需要进行序列化和反序列化，这样就会浪费 CPU 的资源（JS 也有 SharedArrayBuffer，但是使用起来并不方便）

### CPU Memory Management

- Modern CPU 会区分 Code Cache 和 Data Cache

  - Code 会放在 Code Cache 中
  - Data 会放在 Data Cache 中
  - 通过分开存储，可以提高 CPU 的利用率

- CPU 会存储一个 base address 和一个 offset 来表示一个地址，这样就可以通过 base address 和 offset 来计算出一个地址，这样就可以减少存储的空间，提高 CPU 的利用率
  - 跟数组的存储方式类似 const arr = [1, 2, 3]，arr 的地址就是 base address，arr[0] 的地址就是 base address + offset
  - When you talk about the CPU memory, at the end of the day, it's just array access.

### CPU Subroutines & Calling Conventions

- Subroutine 类似于函数，但是 Subroutine 是 CPU 的概念，函数是编程语言的概念
  - Function 会有参数，返回值，但是 Subroutine 没有参数，也没有返回值
  - Subroutine 会有一个指令，叫做 `ret`，这个指令会返回到调用 Subroutine 的指令（类似于 goto?）
  - Subroutine 会存储返回地址，这样就可以返回到调用 Subroutine 的指令
  - 返回地址会存储在一个 stack 中，在 CPU memory 的 data cache 中
  - 这个 Stack 从 data cache 的尾部开始，向前增长，如果 Stack 的大小超过了 data cache 的大小，就会发生 stack overflow
- Calling convention
  - 既然 Subroutine 没有参数，也没有返回值，那么怎么传递参数，怎么返回值呢？
  - 通过寄存器来传递参数，通过寄存器来返回值
  - Calling convention 不是一个标准，而是一个约定，不同的编程语言，不同的 CPU，都有不同的 Calling convention

## Virtual Machine & Deopt

- Physical Machine(CPU)
  - Numbers
  - Arithmetic
  - Flat memory
  - Index access
  - Subroutine
  - Register: PC(Program Counter)
  - Register: SP(Stack Pointer)

```js
let v = 1;
function a() {
  return v === 0 ? 0 : 2;
}

function b() {
  return v === 0 ? 0 : 3;
}

function c() {
  return v === 0 ? 0 : 4;
}
```

运行 10000 次左右以上，就会进行优化，并且此时第一个函数的执行速度会比其他两个函数快很多

这是因为 compiler 会根据现有的信息进行优化，当运行第一个函数时，发现会执行多次，所以会进行优化（inline），但是当运行第二个函数时，发现还有其他的函数，所以就不会进行优化，并且会把之前的优化去掉（deopt）

### Deopt Explorer & Fixing a Deopt

- [VSCode Extension: DeoptExplorer](https://marketplace.visualstudio.com/items?itemName=rbuckton.deoptexplorer-vscode)

- 可以将大的函数拆分成多个小的函数，这样就可以避免 deopt

### Object Shapes & Hidden Classes

```js
const person = { name: 'John', age: 30 };
// will internally create a hidden class
const person_HiddenClass = [Object.prototype, 'name', 'age'];
const person = [person_HiddenClass, 'John', 30];

const name = person.name;
// equivalent to
const name = person[person[0].indexOf('name')];
```

- 根据上面的示例，在有多个对象的时候，如果对象的属性顺序不一样，就会创建多个 hidden class，这样就会影响性能
- 如果对象的属性顺序一样，就会使用同一个 hidden class（这个 hidden class 会被缓存起来），这样就不会影响性能

- 如果从性能方面考量，在创建对象的时候，就应该保证对象的属性顺序一样，这样就可以避免创建多个 hidden class

所以在使用 ts 的时候，使用 `{[key: string]: unknown | undefined}` 会比 `{[key: string]?: unknown}` 更好，因为前者会保证属性的顺序一样，而后者不会

## Micro Benchmarking

### Object Shapes & Inline Caching

```js
// 对于经常访问的属性，会进行 inline caching
const persons = [
  { name: 'John', age: 30 },
  { name: 'Jane', age: 25 },
];
let avgAge = 0;
for (let i = 0; i < persons.length; i++) {
  avgAge += persons[i].age;
  // without inline caching
  const person = persons[i];
  const personHiddenClass = person[0];
  avgAge += person[personHiddenClass.indexOf('age')];
  // with inline caching
  avgAge +=
    person[
      person_HiddenClass == person_HC_0
        ? 2
        : person_HiddenClass == person_HC_1
        ? 2
        : person_HiddenClass === person_HC_2
        ? 1
        : person_HiddenClass.indexOf('age')
    ];
}
```

- vm 会创建一个 1024 个元素的数组，用来缓存对象的 hidden class，这样就可以避免每次都要遍历对象的 hidden class（将 key 映射为 index，例如 user.name 会映射为 user[0]）
- 但是如果对象的 hidden class 超过了 1024 个，就会发生 deopt，这样就会影响性能
- 在实际开发中，不会有这么多的空间来缓存对象的 hidden class，所以不要对于此过渡乐观

### Double & Triple Equal

- `==` 会进行类型转换，`===` 不会进行类型转换
- `==` 会比 `===` 慢很多，大概慢 15 倍左右
- 当使用`==`比较 Object 时，给比较的值添加`.valueOf()`，可以提高性能

### Object & Array Copying

- Origin JSX

```jsx
<div class="title" id="main">
  Hello
  <b class="name">World</b>!
</div>
```

- Current

```js
h('div', { class: 'title', id: 'main' }, [
  'Hello',
  h('b', { class: 'name' }, 'World'),
  '!',
]);
```

- Fast

```js
[
  'div',
  'class',
  'title',
  'id',
  'main',
  [null, 'Hello'],
  ['b', 'class', 'name', [null, 'World']],
  [null, '!'],
];
```

- Copy Array 的开销不大，但是 Copy Object 的开销很大

### Negative Numbers

- JS 中有`0`和`-0`，`0`在 vm 中用`int`来表示，`-0`在 vm 中用`float`来表示(为了存储负号)
- `-x`会将`x`转换为`float`，然后再取负号，所以`a = -x`会比`a = 0 - x`慢很多

### Holey Arrays

```js
const arr = [1, 2, , 3];
```

- 上面的数组中间有一个空位，这个数组就是 holey array
- 当访问 arr[2] 的时候，会沿着原型链查找，最终找不到任何值的时候，才会返回 undefined

```js
// 使用这种方式创建数组会被VM识别为holey array
const arr = new Array(3);
```

- VM 会对使用场景进行检测，当检测到一些场景不适合进行优化的时候，就会进行 deopt

### Amdahl's Law

- 通过优化一部分代码，可以提高性能，但是优化的代码越多，提高的性能就越少

  - 例如把一段代码的性能提高了 10 倍，但是这段代码只占总代码的 10%，那么总体的性能只提高了 1%

- 计算机执行一段烂代码时不会头疼，但是读烂代码的人会头疼
