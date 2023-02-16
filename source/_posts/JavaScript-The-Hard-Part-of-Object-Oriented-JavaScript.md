---
title: 'JavaScript: The Hard Part of Object Oriented JavaScript'
date: 2019-08-11 10:56:58
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, 面相对象, 原型, 原型链, class语法]
---

## Introduction

+ The 5 capacities we look for in candidates
  > 我们在候选人身上寻找的5种能力

  1. Analytical problem solving with code
     > 用代码分析解决问题的能力
  2. Technical communication (can I implement your approach just from your explanation)
     > 技术沟通能力（我能仅通过你的描述实现你的方法吗）
  3. Engineering best practices and approach (Debugging, code structure, patience and reference to documentation)
     > 编程的最佳实践和方法（Debugging，代码结构，耐心和文档）
  4. Non-technical communication (empathetic and thoughtful communication)
     > 非技术性沟通能力（同理心和思考后的交流）
  5. Language and computer science experience
     > 语言和计算机科学经验

### Object Oriented Paradigm

+ OOP - an enormously popular paradigm for structuring our complex code
  > 面向对象编程 - 一种非常流行的用于构建复杂代码编程范式
  + Easy to add features and functionality
    > 可以简便的添加功能
  + Easy for us and other developers to reason about   + (a clear structure)
    > 容易理解
  + Performant (efficient in terms of memory)
    > 性能表现好（在内存方面的效率）
<!-- more -->
## Object Creation

### Creating an Object

+ Let's suppose we're building a quiz game with users
  + Some of our users
    + Name: Phil
    + Score: 4
    + Name: Julia
    + Score: 5
    + Functionality
      + Ability to increase score
  + What would be the best way to store this data and functionality?

```js
const user1 = {
  name: "Phil",
  score: 4,
  increment: function() {
    user1.score++;
  }
};
user1.increment(); //user1.score => 4
```

This is the principle of encapsulation. Let's keep creating our objects
> 这就是封装的原理。让我们继续创建对象。

+ Note we would in reality have a lot of different relevant functionality for our user objects
  + Ability to increase score
  + Ability to decrease score
  + Delete user
  + Log in user
  + Log out user
  + Add avatar
  + get user score
  + ... (100s more applicable functions)

### Object dot Notations

+ What alternative techniques do we have for creating objects?
  + Creating user2 user 'dot notation'

```js
const user2 = {}; //create an empty object
user2.name = "Julia"; //assign properties to that object
user2.score = 5;
user2.increment = function() {
  user2.score++;
};
```

### Object.create

+ Creating user3 using Object.create

```js
const user3 = Object.create(null);
user3.name = "Eva";
user3.score = 9;
user3.increment = function() {
  user3.score++;
};
```

+ Our code is getting repetitive, we're breaking our DRY principle And suppose we have millions of users!
+ What could we do?

### Creating Objects with Functions

#### Solution 1. Generate objects using a function

```js
function userCreator(name, score) {
  const newUser = {};
  newUser.name = name;
  newUser.score = score;
  newUser.increment = function() {
    newUser.score++;
  };
  return newUser;
};
const user1 = userCreator("Phil", 4);
const user2 = userCreator("Julia", 5);
user1.increment()
```

这个方法有一个问题就是在每一个对象实例上都创造各自的increment方法，很明显这回浪费内存空间，并且修改起来也比较麻烦，

## Prototype & New

### Avoid Duplication with Prototype

+ Problems:
  + Each time we create a new user we make space in our computer's memory for all our data and functions. But our functions are just copies
  + Is there a better way?
+ Benefits:
  + It's simple and easy to reason about!

+ Solution 2:
  + Store the increment function in just one object and have the interpreter, if it doesn't find the function on user1, look up to that object to check if it's there
  + How to make this link?

#### Using the prototype chain

```js
const functionStore = {
  increment: function(){this.score++;},
  login: function(){console.log("You're loggedin")}
};
const user1 = {
  name: "Phil",
score: 4 }
user1.name // name is a property of user1 object
user1.increment // Error! increment is not!
```

Link user1 and functionStore so the interpreter, on not finding .increment, makes sure to check up in functionStore where it would find it

#### Solution 2 in full

```js
const userFunctionStore = {
  increment() {
    this.score++
  },
  login() {
    console.log(`${this.name} are logged in`)
  }
}

function userCreator(name, score) {
  const newUser = Object.create(userFunctionStore)
  newUser.name = name
  newUser.score = score
  return newUser
}

const user1 = userCreator("Phil", 4);
const user2 = userCreator("Julia", 5);
user1.increment();
```

### Prototype Walkthrough

这里强调了一下看代码是跳过函数定义，直到调用时再看函数体内的代码。

### Prototype Chain

+ Problem
  + No problems! It's beautiful Maybe a little long-winded
  + const newUser = Object.create(functionStore);
  + ...
  + return newUser
  + Write this every single time - but it's 6 words! Super sophisticated but not   + standard

### new & this Keyword

#### Solution 3 - Introducing the keyword that automates the hard work: new

+ `const user1 = new userCreator("Phil", 4)`
+ When we call the constructor function with new in front we automate 2 things
  1. Create a new user object
  2. return the new user object
+ But now we need to adjust how we write the body of userCreator - how can we:
  + Refer to the auto-created object?
  + Know where to put our single copies of functions?

+ The new keyword automates a lot of our manual work

```js
function userCreator(name, score) {
// const newUser = Object.create(functionStore);
/*newUser*/ this.name = name;
/*newUser*/ this.score = score;
// return newUser;
};
/*functionStore*/userCreator.prototype // {};
/*functionStore*/userCreator.prototype.increment = function(){
  this.score++;
}
let user1 = new userCreator("Phil", 4);
```

### Functions are Objects & Functions

#### Interlude - functions are both objects and functions :/

```js
function multiplyBy2(num){
    return num * 2
}
multiplyBy2.stored = 5
multiplyBy2(3) // 6
multiplyBy2.stored // 5
multiplyBy2.prototype // {}
```

We could use the fact that all functions have a default property on their object version, ’prototype’, which is itself an object - to replace our functionStore object

### new Keyword & Share Function with prototype

### Complete Solution 3

```js
function UserCreator(name, score){
  this.name = name;
  this.score = score;
}
UserCreator.prototype.increment = function(){
  this.score++;
};
UserCreator.prototype.login = function(){
  console.log("login");
};
const user1 = new UserCreator(“Eva”, 9)
user1.increment()
```

根据《JavaScript高级程序设计》,`new`关键字会自动执行以下四个步骤

1. 创建一个新对象
2. 将函数的作用域指向这个新对象（因此函数内的`this`也就指向了新对象）
3. 执行函数体中的代码
4. 返回新对象

为了共享同一属性或者方法，避免在每个实例上都创建新的副本，js设计了原型链机制。

函数作为一等公民，同时也是一个对象，那么就可以给函数添加属性。每个函数在创建时都会自动添加一个`prototype`属性，在我们使用`new`关键字调用函数时，同时也会给使用`new`关键字创建的对象的`__proto__`(浏览器实现，规范为`[[proto]]`)属性指向函数的`prototype`属性。

函数的`prototype`属性指向了一个对象，那么使用`new`关键字调用函数后，函数和函数所返回的新对象都指向了同一个对象。

每次使用`new`关键字都会创建一个新的对象，对象内包含独立的属性和方法，但所有函数使用`new`创建的对象都有一个属性指向了函数的`prototype`属性。

js又有着原型链机制，既在当前对象的属性和方法中找不到所调用的属性或方法时不会立即报错，而是会在对象的`__proto__`(浏览器实现，规范为`[[proto]]`)属性寻找。这样，我们就可以使用函数的`prototype`属性所指向的对象中的属性和方法，既多个对象共享了同一对象的属性和方法。这些属性和方法可以理解为公共属性和方法。

### Review of new

Benefits
— Faster to write
— Still typical practice in professional code

— 99% of developers have no idea how it works and therefore fail interviews
— We have to upper case first letter of the function so we know it requires ‘new’ to work!

我们在函数的`prototype`创建公共的属性和方法，在方法中我们需要操作当前实例的属性和方法，这就需要`this`关键字。

`this`关键字默认指向函数调用时的作用域，使用`new`关键字后，我们将实例的原型与函数的原型指向了同一对象，这样我们就可以通过新创建的对象调用公共方法，此时`this`指向了调用的对象。

但是如果我们不适用`new`关键字调用函数，此时函数就是普通函数，函数体内的`this`也就指向了全局对象（浏览器内是`window`，node里应该是`global`）。我们称创建对象的函数为构造函数，在es6之前约定函数名首字母要大写，这样来区分构造函数与普通函数，但只是人为约定，即使不使用`new`关键字也能调用构造函数（毕竟只是普通函数）。

## Scope & this

### Calling Prototype Methods

对象方法内的`this`指向调用时所在的对象既点方法调用时点左侧的对象，函数内的`this`一般指向全局对象。

### this Keyword Scoping Issues

#### What if we want to organize our code inside one of our shared functions - perhaps by defining a new inner function

```js
function UserCreator(name, score){
  this.name = name;
  this.score = score;
}
UserCreator.prototype.increment = function(){
  this.score++ // 2.方法执行，创建新的执行上下文，首先将这里的this指向了调用时所在的对象
  function add1(){
    this.score++; // 4.函数执行，创建新的执行上下文，由于没有通过点方法调用，此时函数位于全局作用域，this指向widows，此时widow对象会得到一个score属性，值为NAN
  }
  // const add1 = function(){this.score++;}
  add1() // 3.执行函数
};
UserCreator.prototype.login = function(){
  console.log("login");
};
const user1 = new UserCreator("Eva", 9)
user1.increment() // 1.调用方法
```

为了避免上述情况的发生，我们可以使用临时变量保存`this`的指向，例如在`increment`内`const self = this`，也可以使用`bind`，`call`，`apply`方法显式绑定`this`指向。

#### We need to introduce arrow functions - which bind this lexically

在es6中，提出了新的函数定义方法，既箭头函数。根据规范，箭头函数并不存在`this`关键字，所以箭头函数内的`this`就会通过作用域链指向箭头函数外的`this`，也就是`increment`中的`this`。

### Solving Scope with Arrow Functions

```js
function UserCreator(name, score){
  this.name = name;
  this.score = score;
}
UserCreator.prototype.increment = function(){
  const add1 = ()=>{this.score++}
  add1()
};
UserCreator.prototype.login = function(){
  console.log("login");
};
const user1 = new UserCreator(“Eva”, 9)
user1.increment()
```

### ES6 class Keyword

#### Solution 4

+ We’re writing our shared methods separately from our object ‘constructor’ itself (off in the User.prototype object)
+ Other languages let us do this all in one place. ES2015 lets us do so too

+ The class ‘syntactic sugar’

```js
class UserCreator {
  constructor (name, score){
    this.name = name;
    this.score = score;
  }
  increment (){
    this.score++;
  }
  login (){
    console.log("login");
  }
}
const user1 = new UserCreator("Eva", 9);
user1.increment();
```

+ Benefits:
  + Emerging as a new standard
  + Feels more like style of other languages (e.g. Python)
Problems
  + 99% of developers have no idea how it works and therefore fail interviews

## Default Prototype Chain

### Objects default __protp__

+ JavaScript uses this proto link to give objects, functions and arrays a bunch of bonus functionality. All objects by default have `__proto__`

```js
const obj = {
  num : 3
}
obj.num // 3
obj.hasOwnProperty("num") // ? Where's this method?
Object.prototype // {hasOwnProperty: FUNCTION}
```

+ With Object.create we override the default `__proto__` reference to
Object.prototype and replace with functionStore
+ But functionStore is an object so it has a `__proto__` reference to `Object.prototype` - we just intercede in the chain

### Function.prototype and Array.prototype

#### Arrays and functions are also objects so they get access to all the functions in Object.prototype but also more goodies

```js
function multiplyBy2(num){
  return num*2
}
multiplyBy2.toString() //Where is this method?
Function.prototype // {toString : FUNCTION, call : FUNCTION, bind : FUNCTION}
multiplyBy2.hasOwnProperty("score") // Where's this function?
Function.prototype.__proto__ // Object.prototype  {hasOwnProperty: FUNCTION}
```

### Subclassing with Factory Functions

#### Into to Subclassing and Inheritance

面向对象有三个比较显著的特性，封装，继承和多态。

使用ES6的`class`语法糖，我们可以将代码更加简洁的组织在一起，也就是封装。

那么在实现一个类之后，我们希望其他类继承现有类的属性，那么应该如何实现呢？

#### We can achieve this in JavaScript in Solution 2, 3 and 4

在之前的《JavaScript Deep Foundation》中，我们学到了一下知识：

> 我们都知道js是一门基于对象的语言。在一些面相对象的语言中，我们会创建一个类，在使用时创造一个类的实例来进行使用。类就类似图纸，实例就类似根据图纸造出来的建筑。我们没法直接使用图纸，但是可以使用建筑，同时，我们的建筑时根据图纸制造的，可以造的不同，但是一些图纸上的特性，建筑上也存在。
> js原型系统的关键在于**实例并没有复制构造函数的原型对象，而是将实例的原型与构造函数的原型对象链接到了一起**。
> 在传统的面向对象语言中，实例复制了公共的属性方法。在js中，实例连接到了存储着公共属性及方法的对象。所以js的设计模式与传统的面相对象语言的设计模式并不相同。
> 在js的设计模式并不是“继承”，更像是“委托”。js使用原型系统或者说委托系统，而不是类系统或者说继承系统。

### Create object with Factory Function

#### Subclassing in Solution 2 - Factory function approach

```js
function userCreator(name, score) {
  const newUser = Object.create(userFunctions) // 创建一个新对象，并将新对象的原型指向userFunction
  newUser.name = name
  newUser.score = score
  return newUser
}

const userFunctions = {
  sayName() {
    console.log(`I'm ${this.name}`)
  },
  increment() {
    this.score++
  }
}

const user1 = userCreator('Phil', 5)
user1.sayName() // I'm Phil
```

### Creating an object with a Sub-Factory Function

```js
function paidUserCreator(paidName, paidScore, accountBalance) {
  const newPaidUser = userCreator(paidName, paidScore) // 使用之前的工厂函数创建子类实例对象
  Object.setPrototypeOf(newPaidUser, paidUserFunction) // 将子类实例对象的原型对象进行绑定
  newPaidUser.accountBalance = accountBalance // 给子类添加属性
  return newPaidUser // 返回子类实例对象
}

const paidUserFunction = { // 子类对象的原型对象
  increaseBalance() {
    this.accountBalance++
  }
}

Object.setPrototypeOf(paidUserFunction, userFunctions) // 将子类构造函数添加到父类构造函数的原型上

const paidUser1 = paidUserCreator('Bob', 8, 25)
paidUser1.increaseBalance() // paidUser1.__proto__.accountBalance 26
paidUser1.sayName() // paidUser1.__proto__.__proto__.sayName() I'm Bob
```

### Call and Apply

#### Interlude - We have another way of running a function that allow us to control the assignment of this

```js
const obj = {
  num: 3,
  increment: function(){this.num++;}
};
const otherObj = {
  num: 10
};
obj.increment(); // obj.num now 4
obj.increment.call(otherObj); // otherObj.num now 11
// obj.increment.apply(otherObj);
```

+ `this` always refers to the object to the left of the dot on which the function (method) is being called - unless we override that by running the function using `.call()` or `.apply()` which lets us set the value of `this` inside of the increment function

## Subclassing with new and call

### Create an Object with `new`

#### Subclassing in Solution 3 - Constructor(Pseudoclassical) approach

```js
function userCreator(name, score) {
  this.name = name
  this.score = score
}

userCreator.prototype.sayName = function() {
  console.log(`I'm ${this.name}`)
}
userCreator.prototype.increment = function() {
  this.score++
}

const user1 = new userCreator('Phil', 5)
const user2 = new userCreator('Tim', 4)

user1.sayName() // I'm Phil
```

### Creating a Subclass with a Constructor

```js
function paidUserCreator(paidName, paidScore, accountBalance) {
  userCreator.call(this, paidName, paidScore) // 使用call或apply显式绑定this为当前函数执行上下文
  // userCreator.apply(this, [paidName, paidScore])
  this.accountBalance = accountBalance // 给子类添加属性
}

paidUserCreator.prototype = Object.create(userCreator.prototype) // 将子类的原型绑定到父类的原型的原型上。paidUserCreator.prototype.__proto__=userCreator.prototype
paidUserCreator.prototype.increaseBalance = function () {
  this.accountBalance++
}

const paidUser1 = new paidUserCreator('Bob', 8, 25)
paidUser1.increaseBalance()
console.log(paidUser1.accountBalance) // 26
paidUser1.sayName() // I'm Bob
// paidUser1.__proto__.__proto__.sayName() I'm Bob
```

## Subclassing with class, extends & super

### Create an Object with a class

#### Subclassing in Solution 3 - ES2015 Class approach

```js
class userCreater {
  constructor(name, score) {
    this.name = name
    this.score = score
  }

  sayName() {
    console.log(`I'm ${this.name}`)
  }

  increment() {
    this.score++
  }
}

const user1 = new userCreater('Phil', 4)
const user2 = new userCreater('Tim', 4)

user1.sayName()
```

### Creating a Subclass with a extends

```js
class paidUserCreator extends userCreater {  // 使用extends将paidUserCreator的prototype的__proto__连接到userCreator的prototype上
  constructor(paidName, paidScore, accountBalance) {
    super(paidName, paidScore) // 作用相当于Reflect.construct(userCreator,[paidName, paidScore])，会调用userCreator创建新对象，相当于new userCreator(paidName, paidScore)，此时constructor的this指向了返回的新对象，并将新对象的__proto__指向paidUserCreator的Prototype
    this.accountBalance = accountBalance // 给经super返回的新对象添加属性
  }

  increaseBalance() {
    this.accountBalance++
  }
}

const paidUser1 = new paidUserCreator('Bob', 8, 25)
paidUser1.increaseBalance()
paidUser1.sayName()
```
