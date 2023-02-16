---
title: Interviewing For Front-End Engineers
date: 2020-03-29 15:32:52
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, Interview]
---
## Introduction

+ 为什么面试很难
  + 因为技术栈不适合当前公司？
  + 即使编程技能很好但仍会当前公司使用的是不熟悉的技术？

+ 也许是每个人对编程的理解不同
  + 每个工程师对于构建产品或者解决问题的思路可能不同
  + 所以并没有一个类似高考分数的同一标准

+ 3 type of programmers
  1. Programming is math
     + 编程就是算法，操作数字的技术
  2. Programming is for controlling hardware
     + 编程的关键是硬件性能，程序是否足够接近底层以获得更高的性能表现
  3. Programming is for building things
     + 巨大多数人的类型，前端工程师的主要工作
     + 制作产品，用产品与用户沟通，理解规范与浏览器差异来实现功能

+ [3 tribes of programming](https://josephg.com/blog/3-tribes/)
<!-- more -->
### Challenges Interviewing for Front-End Engineering

+ Why is interviewing for Frontend so difficult?

+ 问题特定到前端面试，为什么如此之难？
  + 技术更新过快，一夜间可能就多出许多概念
  + 前端涉及范围广泛
    + 车载应用、电视应用、浏览器应用、桌面应用、移动应用等等
  + 与其他技术相比，gui仍十分年轻，只在最近二三十年开始蓬勃发展

+ 这里说了个笑话
  + In JavaScript, how would \_you\_ define scope?
+ 作用域在JS中是一个核心概念，可是即便是如此核心的概念，不同人对此仍有不同理解

## Application

### Interview & Candidate Perspectives

+ Interviewer
  + 这里提了一个问题，作为一个面试官，你总共花了多久学习面试？
  + 问题的答案可能就是从面试者的角度来说，面试很难的原因之一，因为我们花了许多时间学习如何通过面试，但很少学习如何面试他人

### Goal Setting & The Interview Process

+ 0.300
  + 这个应该是美职棒的平均打击数量（10次挥棒七次不中）
  + 既每十次打中三次就可以在美职棒中处于平均水准之上，成为棒球明星

+ 0.501
  + 这是作者提出的面试成功率，及面试两次就能成功一次

+ Steps of an interview
  + Application
  + Initial call
  + Code test
  + Phone screen
  + On-site
  + Result

### Before You Apply

+ Application
  + What do you like about your current job?
    + 是工作氛围很好，能帮助成长？
    + 解决了许多复杂难题并且获得了奖励？
    + 公司有所谓的工程师文化？
  + What sort of things are you looking for in your next role?
    + 建议在入职前仔细考虑下自己的需求
  + Which companies have appealing engineering cultures? What makes it appealing?

### About Your Resume

+ One page
+ Skills
+ Experience
+ Technology familiarity
+ Education
+ Accomplishments

### What to Do with No Experience

+ What do I do if I have little/no experience?
  + 可以先做测试，进入互联网公司，再转型。
  + 以一个低价格帮助企业建站
  + 有人会说在GItHub上放一大堆样例代码，但作者认为可能并没有太大帮助，将算法等发到Twitter等社交媒体，将一些酷炫的ui和动画发到CodePen上的效果可能比GItHub上更好，能被更多人注意到。
  + 最好的办法还是多在网络上跟人交流，争取内推的机会。
  + 最终的建议总结
    + LinkedIn Profile
    + Personal Webpage
    + Network
  + 非计算机相关的经历怎么处理
    + 可以放在简历上

### Evaluating Application

+ Interviewer
  + Which roles does this person fit?
  + What sort of experience is required?
  + Is there any bias in your selection process?

## Initial Call

### Initial Call Overview

+ What do you do  currently?
+ What are some projects you've worked on recently?
+ What are you looking for in your next role?
+ Why do you want to work for ____?
+ What is your availability for the next steps?

### Preparing Interview Questions

1. How many steps are in the interview process? How long does it generally take?
2. How big is your engineering team?
3. Which team would I be interviewing for?
4. What is the culture like?
5. Who are you competitors?
6. What sort of projects would I work on?

### Prescreen JavaScript Questions

+ What is the difference between const let, and var?
  + var与let和const作用域不同；var会提升到作用域顶部，在初始化之前使用，let和const不能再初始化之前使用；const一旦初始化不可重新赋值，当然，还是可以更改引用类型的属性。
+ Explain prototypical inheritance
  + JS使用原型提供面向对象的继承。通过指定新对象的原型属性来继承其他对象的属性或者方法。
+ What is "this" mean in JavaScript?
  + 当前执行上下文所指向的对象，如果没有的话就是全局对象
+ What is the data structure of the DOM?
  + Tree
+ What is a Stack and Queue? How would you create data structures in JavaScript?
  + Stack: FILO(First In Last Out)
  + Queue: FIFO(First In First Out)
+ How can you tell if an image element is loaded on a page?
  + Image元素有onload事件
+ What is call() and apply()?
  + 显式的更改this指向
+ What is event delegation and what are the performance tradeoffs?
  + 当有多个元素注册同一监听事件时，可以将事件注册在其共有的父级元素上，这就是事件委托。因为注册事件会消耗性能，所以应该尽可能少的减少注册事件
+ What is a Worker? When would you use one?
  + 浏览器内置功能，在后台可以做一些计算或者缓存，如果有一些复杂计算，并且不想阻塞当前进程，可以使用worker后台计算。

+ 作者并不追求完全正确的答案，而是期待对这些知识有一定了解

+ ~~Trivia~~
  + {} === +[]

+ 有时候你认为你出的面试题简单是因为你已经知道答案，如果你在独立解决一些怪异问题时，还认为这些问题简单吗？

+ 面试本来就不简单，我们被赋予高额的工资来面试别人，有些困难和挑战才是工资如此之高的正常现象。

## Code Test

### Code Test Overview

+ Interviewee Code test tips
  + Make your code as readable as possible
    + 例如别用\~\~代替Math.round😂
    + Comment your code
    + 即使追求self documenting也最好加注释，这样即使不赞同实现方式也可以顺着实现逻辑缕清代码思路
    + Don't over complicate the architecture
    + 还是尽量简单，简单易读的代码就是好代码
  + Don't import too many libraries
  + If you have time, add unit tests
  + Ask questions!

### Giving & Evaluating a Code Test

+ Interviewer Code Test Tips
  + Make the problem as straightforward as possible
  + Be honest with the time constraints
  + Have a code review checklist

| Average                     | Good                                | Exceptional                                     |
| --------------------------- | ----------------------------------- | ----------------------------------------------- |
| Application starts properly | Code is well documented             | Modular architecture designed for extensibility |
| 3/5 requirements completes  | All requirements are complete       | Created unit and integration tests              |
|                             | No errors and thrown in the console |                                                 |

### Big-O

+ Knowledge: Big-O
  + 大O,没啥好说的，描述时间和空间复杂度的，简单说就是忽略一切常数和其他，只保留最高位的系数。
  + Big Omega Ω "best case"
  + Big Theta ⍬ "average case"
  + Big O O "worst case"

```js
function search(arr, num) {
  for(let i = 0; i < arr.length; i++) {
    if(arr[i] === num) {
      return true
    }
  }
  return false
}
// Big Omega Constant
// Big Theta O(n)
// Big O O(n)
```

## Phone Screen

### Phone Screen Overview

+ Candidate
  + Ask questions
  + Talk out your solution
  + Get comfortable with the environment

+ Interviewer
  + Are you in a quiet area?
  + Is the problem well worded?
  + Does the candidate know what the requirements and restrictions are?
  + Did you leave time for questions at the end?

### Phone Screen Exercise

+ [Exercise Link](https://codepen.io/jemyoung/pen/0817cc37159377752b6cd9bf70d40883?editors=1011)

+ My solution

```js
/** YOUR CODE BELOW **/
class Foods {
  constructor(rootElement, foodData) {
    this.rootElement = rootElement
    this.foodData = foodData
    this.staticClickHandler = this.clickHandler.bind(this)
  }
  
  render() {
    const lis = this.foodData.map((food, index) => (
      `<li data-index=${index}>${food.image} ${food.name}</li>`
    ))

    this.rootElement.innerHTML = `
      <ul>${lis.join('')}</ul>
    `

    this.rootElement.addEventListener('click', this.staticClickHandler)
  }
  
  clickHandler(e) {
     const clickIndex = e.target.dataset.index
     this.foodData.splice(clickIndex, 1)
     this.rootElement.removeEventListener('click', this.staticClickHandler)
     this.render()
  }
}

const foods = new Foods(rootElement, foodData)

foods.render()
```

+ Author's Solution

```js
class Foods {
  constructor(el, foodData) {
    this._root = el
    this._data = foodData
  }

  renderList() {
    this._root.addEventListener('click', e => {
      const { target } = e
      target.remove()
    })

    // 创建一个fragment,将food同一插入至此元素后在将其同一放入rootElement内
    // 以防止多次操作页面dom，提高性能表现
    const fragment = document.createDocumentFragment()

    this._data.forEach(i => {
      fragment.appendChild(this.createFoodItem(i))
    })

    this._root.appendChild(fragment)
  }

  createFoodItem(item) {
    const itemEl = document.createElement('div')
    itemEl.innerText = item.image
    itemEl.classList.add(item.name)

    return itemEl
  }
}
```

### On-Site Preparation

+ 不是所有面试者都能走到On-Site这一步，如果进去了On-Site环节，即使此环节搞砸了，也要强于多数面试者。

+ Candidate
  + Practice writing code without a computer
  + Go over general sample problems
  + Ask your friends to test you
  + Try to ask what the style of technical questions will be

+ Interviewer
  + Make sure questions are relevant to the role
  + Have backup interviewers
  + Book the room for the day
  + Allow time for breaks(bathroom, lunch, etc)

## On-Site Interview & Concept Review

### On-Site Interview Overview

+ 一般来说，On-Site作为最后一个环节决定了是否发offer。那么除了大量的js基础问题，还涉及到作为面试者，在我问这些问题时我在寻找被面试者的那些特质？
+ 作为被面试者，你试图尽可能的表现出自己最好的一面，当已经进行到On-Site代表公司认同你的天分，所以此时其实寻找的是不发offer的原因，不要给公司这个机会。

### What is a String

+ Knowledge: strings
  + Primitive type
  + Useful methods
    + .split()
    + .toLowerCase()
    + .substring()
    + .startsWith()
  + Immutable

### Reversing a String

```js
const reverseString = (str) => [...str].reverse().join('')
```

### Built-in Methods

+ Knowledge: arrays
  + Convert methods
    + Object.entries()
    + Array.from()
    + \[...item\]
  + Useful methods
    + .isArray()
    + .filter()
    + .reduce()
    + .map()
    + .concat()
    + .join()
    + .pop()
    + .push()

## On-Site Interview Example Questions

### Removing Duplicate Strings Exercise

```js
// Create a function that takes a string and returns a
// new string with duplicates removed

const removeDuplicates = (str) => {
  const map = {}
  return str
    .split(' ')
    .reduce((arr, str) => {
      if(map[str]) return arr
      map[str] = 1
      return [...arr, str]
    }, [])
    .join(' ')
}

// author solution
function removeDup(str) {
  const arr = str.split(' ')
  const set = new Set(arr)
  return [...set].join(' ')
}

const str = 'This is is a test test string';
removeDuplicates(str); // 'This is a test string'
```

+ Tip
  + 如果涉及到unique value，考虑使用Set

### Flattening an Array Exercise

```js
// Without using .flat(), create a function to flatten an array
const flatten = (arr) => {
  return arr.reduce((res, item) => {
    if(Array.isArray(item)) {
      return [...res, ...flatten(item)]
    }
    return [...res, item]
  }, [])
}

// author solution
function flattenArr(arr) {
  arr.reduce((acc, curr) => {
    if(Array.isArray(curr)) {
      acc = acc.concat(flattenArr(curr))
    } else {
      acc.push(item)
    }
      return acc
  }, [])

  return someNewArray
}

const exampleArray = [1,2,[3,4, [5,6,7], 8], 9, 10];
flatten(exampleArray); // [1,2,3,4,5,6,7,8,9,10]
```

### JavaScript Scope: bind Exercise

+ Knowledge: scope
  + .call()
  + .apply()
  + .bind()

```js

// Implement Function.prototype.bind()
Function.prototype.bind = function bind(context, ...args) {
  const fn = this
  return function() {
    fn.call(context, ...args)
  }
}

const foo = function() {
    console.log(this.bar);
}
let baz = foo.bind({bar: 'hello'})
baz(); // Hello
```

### Timing: Debounce Exercise

+ Knowledge: timing
  + setInterval()
  + setTimeout()

```js
// Implement debounce
const debounce = function (fn, time) {
  let id = null

  return function () {
    if(id) {
      clearTimeout(id)
    }

    id = setTimeout(() => {
      fn.apply(this, arguments)
      id = null
    }, time)
  }
}

const throttle = function (fn, time) {
  let id = null
  
  return () => {
    if(id) {
      return
    }

    id = setTimeout(function () {
      fn.apply(this.arguments)
      id = null
    }, time)
  }
}
```

### Trees Exercise

+ Knowledge: trees

```js
// We have two identical DOM trees, A and B. For DOM tree A, we have
// the location of an element. Create a function to find that same element
// in tree B.
const reversePath = (element, root) => {
  const steps = []
  let pointer = element

  while(pointer.parent) {
    steps.push(
      pointer.parent.indexOf(pointer)
    )
    pointer = pointer.parent
  }

  pointer = root

  while(steps.length) {
    pointer = pointer.children[steps.pop()]
  }

  return pointer
}
```

### Rendering Exercise

+ Knowledge: rendering
  + requestAnimationFrame()

```js
// Create a function to move an element. The function arguments are,
// distance, duration, and the element to move.


function moveElement(duration, distance, element) {
  const start = performance.now()
  
  function move(currentTime) {
    const elapsed = currentTime - start
    const progress = elapsed / duration
    const amountToMove = distance * progress

    element.style.transform = `translateX(${amountToMove}px)`

    if(amountToMove < distance) {
      requestAnimationFrame(move)
    }
  }

  requestAnimationFrame(move)
}
```

### Promises Exercise

+ Knowledge: Promises

```js
function returnApple(callback) {
  setTimeout(() => {
    callback("🍎")
  }, 500)
}

function returnApplePromise() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve("🍎")
    }, 500)
  })
}

async function getApples() {
  const apple = await returnApplePromise()
  console.log(apple)

  returnApple(apple2 => {
    console.log(apple2)
  })
}
```

```js
// Create a sleep function that takes one parameter (time) and
// will wait "time" ms
const sleep = (time) => {
  return new Promise((res, rej) => {
    setTimeout(() => {
      res()
    }, time)
  })
}

async function run() {
    await sleep(500);
    console.log('hello');
    await sleep(500);
    console.log('world');
}
```

```js
// Create a function to turn any function into a "promisified" function.
// Any function to be promisified will always have a callback as the last argument.
// The callback will always have this signature:
//   function(result){}
const promisify = function (fn) {
  return function (...args) {
    return new Promise(resolve => {
      const cb = result => {
        resolve(result)
      }

      fn.apply(this, args.concat(cb))
    })
  }
}

const exampleFn = function (x, y, callback) {};
const promisedFn = promisify(exampleFn);
promisedFn().then(...).then(...)
```

## Wrapping Up

+ Results
  + 此时已通过所有面试流程，所以结果只有通过或者不通过
  + 有时你运气不错，基本回答上所有问题，有的时候也许只是运气不好，涉及到了知识盲区
  + 尽量获取到公司对面试的反馈，以便下一次的提升

+ Interviewer
  + Give feedback
  + Invite them to re-apply
  + Stay in touch
