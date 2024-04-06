---
title: Test Your JavaScript Knowledge
date: 2024-04-05 23:24:23
categories: [学习笔记]
tags: [FrontEndMasters]
---

# Test Your JavaScript Knowledge

- [Slides](https://static.frontendmasters.com/assets/courses/2024-02-20-javascript-quiz/javascript-quiz-slides.pdf)

## Event Loop & Task Queue

- Macrotask: setTimeout, setInterval, setImmediate, I/O, UI rendering

```js
setTimeout(() => console.log('A'), 0);
```

- Microtask: process.nextTick, Promises, Object.observe, MutationObserver, queueMicrotask

```js
Promise.resolve().then(() => console.log('B'));
```

### Question 1

- Put the logs in the correct order.

```js
Promise.resolve().then(() => console.log('1'));
queueMicrotask(() => console.log('2'));
setTimeout(() => console.log('3'), 0);
console.log('4');
new Promise(() => console.log('5'));
(async () => console.log('6'))();
```

will output:

```
4
5
6
1
2
3
```

### Question 2

Which of the following are not considered microtasks in JavaScript event loop?

- A. Script loading > it's a macrotask
- B. `setTimeout` > it's a macrotask
- C. `mousemove` > it's a macrotask
- D. `requestAnimationFrame` > it's a macrotask
- E. UI rendering > it's a macrotask
- F. `then` callback > it's a microtask
- G. `new Promise()` executor function > it's a macrotask
- H. `fetch` calls > it's a macrotask

Answer: A, B, C, D, E, G, H

### Question 3

Put the logs in the correct order.

```js
async function asyncFunction() {
  console.log('1');
  new Promise(() => console.log('2'));
  await new Promise((res) => {
    setTimeout(() => {
      console.log('3');
    }, 0);
    res();
  });
}

new Promise((res) => {
  console.log('4');
  (async () => {
    console.log('5');
    await asyncFunction();
    console.log('6');
  })();
  res();
}).then(() => console.log('7'));

console.log('8');
```

will output:

```js
4;
5;
1;
2;
8;
7;
6;
3;
```

### Question 4

```js
(async () => {
  const asyncFunc = async () => 'asyncFunc';

  const promise = new Promise((res) => {
    console.log('promise');
    // no res(), so the then() will not be called
  }).then(() => console.log('then'));

  console.log('async body');

  queueMicrotask(() => console.log('queueMicrotask'));

  const results = await Promise.all([asyncFunc(), promise]);

  return results;
})();

console.log('script');
```

will output:

```js
'promise';
'async body';
'script';
'queueMicrotask';
```

## Scope & Closure

### Question 5

```js
const outerFunc = () => {
  let count = 0;
  return () => ++count;
};

const counter = outerFunc();
console.log(counter());
console.log(counter());
```

will output:

```js
1;
2;
```

### Question 6

```js
function createCounter() {
  let globalCount = 0;

  function incrementCount() {
    let incrementedValue = ++globalCount;
    return incrementedValue;
  }

  return {
    incrementCount,
  };
}

const counter = createCounter();
console.log(counter.incrementCount());
console.log(counter.incrementCount());
console.log(createCounter().incrementCount());
```

will output:

```js
1;
2;
1;
```

### Question 7

```js
function createUserManager() {
  let user = null;

  return function (name) {
    'use strict';
    user = { name, createAt: Date.now() };
    return user;
  };
}

const createUser = createUserManager();
createUser('Alice') === createUser('Bob');
```

will be `false`.

### Question 8

```js
function createCounter(initial) {
  let count = initial;

  return function () {
    'use strict';
    count += 1;
    return count;
  };
}

const counter = createCounter(10);
counter();
counter();
console.log(counter());
```

will output:

```js
13;
```

### Question 9

Which statements are correct?

1. Hoisting is the process of moving functions and variables to the top of the file.
2. Variables declared with `let` and `const` are hoisted.
3. Variables declared with the var keyword are uninitialized.
4. Hoisting occurs during the execution phase
5. Import declarations are hoisted.

Answer: 2, 5

### Question 10

```js
const input = prompt('What fruit do you like?');
const css = `color: #fff;`;

// {} of switch statement will not create a new scope
switch (input) {
  case 'orange':
    const css = `color: orange;`;
    console.log('%cOrange', css);
    break;
  case 'lemon':
    const css = `color: yellow;`;
    console.log('%cLemon', css);
    break;
  default:
    console.log('No color');
}
// input 'orange'
```

will output: SyntaxError: Identifier 'css' has already been declared

```js
// we can use block scope to fix this issue
const input = prompt('What fruit do you like?');
const css = `color: #fff;`;

switch (input) {
  case 'orange': {
    const css = `color: orange;`;
    console.log('%cOrange', css);
    break;
  }
  case 'lemon': {
    const css = `color: yellow;`;
    console.log('%cLemon', css);
    break;
  }
  default:
    console.log('No color');
}
```

## this Keyword

### Question 11

```js
function logThis() {
  console.log(this);
}

const obj = {
  logThis,
  logThis2() {
    return logThis();
  },
  logThis3() {
    return obj.logThis();
  },
};

obj.logThis();
obj.logThis2();
obj.logThis3();
```

will output:

```js
obj;
window;
obj;
```

### Question 12

```js
const objA = {
  foo() {
    console.log(this);
  },
  bar: () => {
    console.log(this);
  },
};

const objB = {
  foo: objA.foo,
  bar: () => objA.bar(),
  baz() {
    objA.foo();
  },
};

objB.foo();
objB.bar();
objB.baz();
```

will output:

```js
objB;
window;
objA;
```

### Question 13

```js
function logThis() {
  console.log(this);
}

const obj = {
  foo: logThis,
  bar: () => logThis(),
  baz() {
    logThis();
  },
  qux() {
    logThis.call(this);
  },
};

const obj2 = {
  ...obj,
  foo2: () => obj.foo(),
};

for (const key in obj2) {
  obj2[key]();
}
```

will output:

```js
obj2;
window;
window;
obj2;
obj;
```

### Question 14

```js
function logThis() {
  console.log(this);
}

const obj = {
  logThis,
  logThisArrow: () => console.log(this),
  logThisNested() {
    const nested = () => console.log(this);
    nested();
  },
};

obj.logThis();
obj.logThisArrow();
obj.logThisNested();
```

will output:

```js
obj;
window;
obj;
```

### Question 15

```js
const obj = {
  logThis() {
    console.log(this);
  },
  logThis2() {
    function nested() {
      console.log(this);
    }
    return nested.apply(this);
  },
};

const { logThis, logThis2 } = obj;

logThis();
logThis2();
obj.logThis();
obj.logThis2();
```

will output:

```js
window;
window;
obj;
obj;
```

## Class & Prototypes

### Question 16

- Creating a new `User` instance would create a new `login` function in memory each time.

```js
class User {
  constructor(username) {
    this.username = username;
  }

  login() {
    console.log(`Welcome, ${this.username}`);
  }
}

const user1 = new User('Alice');
const user2 = new User('Bob');
user1.login === user2.login; // true
```

### Question 17

- Which of the following statements are true?

```js
class Dog {
  constructor(name) {
    this.name = name;
    this.wagTail = () => {
      return 'Wagging tail!';
    };
  }

  bark() {
    return 'Woof!';
  }
}

const dog1 = new Dog('Max');
const dog2 = new Dog('Spot');
```

A. `dog1.wagTail() === dog2.wagTail()` true
B. `dog1.wagTail === dog2.wagTail` false
C. `dog1.bark === dog2.bark` true
D. `Object.getPrototypeOf(dog1) === Object.getPrototypeOf(dog2)` true
E. `dog1.constructor === dog2.constructor` true

### Question 18

- What gets logged?

```js
class Counter {
  constructor(initialCount = 0) {
    this.count = initialCount;
  }

  increment() {
    return this.count++;
  }
}

const counterOne = new Counter(1);
counterOne.increment();
const counterTwo = Object.create(counterOne);
counterTwo.increment();

console.log(counterOne.count, counterTwo.count);
// will log 2, 3
// because Object.create() creates a new object with the prototype of the object passed in
// so the prototype of counterTwo is counterOne
// and if we mutate the count property of counterTwo
// it will create a new property on counterTwo
// instead of mutating the count property of counterOne
```

### Question 19

- What gets logged?

```js
class ChameLeon {
  constructor(color = 'green') {
    this.color = color;
  }

  static changeColor(newColor) {
    this.color = newColor;
    return this.color;
  }
}

const freddie = new ChameLeon('green');
freddie.changeColor('orange');
// will log TypeError: freddie.changeColor is not a function
// because changeColor is a static method
// and can only be called on the class itself
```

### Question 20

- Which statements are true?

```js
class User {
  constructor(username) {
    this.username = username;
  }

  login() {
    console.log(`Welcome, ${this.username}`);
  }
}

const user = new User('Alice');
```

A. `Object.getPrototypeOf(user) === User.prototype;` true
B. `Object.getPrototypeOf(user) === Object.getPrototypeOf(User);` false, because `Object.getPrototypeOf(User)` is `Function.prototype`
C. `user.prototype === User.prototype;` false, because `user` is an instance of `User`, not a class, so it doesn't have a `prototype` property
D `Object.getPrototypeOf(User) === User.constructor` false, because `User.constructor` is `Function`

## Generators & Iterators

### Question 21

- What gets logged?

```js
function* count() {
  yield 1;
  yield 2;
  return 3;
}

for (const num of count()) {
  console.log(num);
}

// will log 1, 2, because the return value of the generator is ignored
```

### Question 22

- To create a custom iterable object in JavaScript, the object must implement the `Symbol.iterator` method that returns an iterator.

The iterator must implement what methods?

A. `next` // true
B. `iterator` // false
C. `iterate` // false
D. `forEach` // false
E. none // false

### Question 23

- What gets logged?

```js
async function* range(start, end) {
  for (let i = start; i <= end; i++) {
    yield Promise.resolve(i);
  }
}

(async () => {
  const gen = range(1, 3);
  for await (const num of gen) {
    console.log(num);
  }
})();

// will log 1, 2, 3
```

### Question 24

- What gets logged?

```js
function* gen1() {
  yield 2;
  yield 3;
}

function* gen2() {
  yield 1;
  yield* gen1();
  yield 4;
}

console.log([...gen2()]);
// will log [1, 2, 3, 4]
```

## Garbage Collection

### Question 25

- Which of the following statements are true about a `WeakMap`?
  - A. Keys are strongly referenced, values are weakly referenced. // false
  - B. Keys are weakly referenced, values are strongly referenced. // true
  - C. It is enumerable, allow iteration over its elements. // false
  - D. It can have primitive data types as keys. // false
  - E. We can use the `keys()` method on a `WeakMap`, but not `values()` or `entries()`. // false

### Question 26

- When will `inner` function will be eligible for garbage collection?

```js
function outer() {
  return function inner() {
    console.log('Hello');
  };
}

const fn = outer();
```

A. By invoking `fn` // false
B. By explicitly setting `fn` to `null` // true
C. It is automatically garbage collected when the outer function is called // false
D. It depends on the JavaScript engine // false

### Question 27

- Which of the following statements are true?

```js
let obj = { a: { b: 2 } };
let ref = obj.a;

obj = null;
```

A. The object `{b: 1}` will be garbage collected // false
B. `ref` still references the object `{b: 1}`, so it will not be garbage collected // true
C. The entire `obj` object is retained in the memory due to the reference `ref` // false
D. Setting `obj` to `null` frees all memory associated with it // false

### Question 28

- You can get a list of all keys in a `WeakMap` using its `keys` method, but you can't get its `values` or `entries`. // false

### Question 29

- When will each `user` be eligible for garbage collection?

```js
function myFunc() {
  for (let i = 0; i < 10; i++) {
    const user = { name: 'Alice' };
    return user;
  }
}
```

A. Immediately after each iteration. // true
B. After the loop completes. // false
C. Only if explicitly set to `null` within the loop. // false
D. When the function containing the loop finishes executing. // false

### Question 30

- What gets logged?

```js
const obj = {
  bar() {
    console.log(this);
  },
};

setTimeout(() => obj.bar(), 0);

queueMicrotask(() => {
  delete obj.bar;
});

// will log type error: obj.bar is not a function
```

## Modules

### Question 31

- Match each module loading mechanism with its correct characteristic.
  - CommonJS: Used mainly in server-side contexts, this approach loads modules sequentially.
  - AMD: Focuses on client-side asynchronous loading, allowing modules to be fetched and executed in a non-blocking manner.
  - UMD: supports various environments, accommodating both server and client-side usages.
  - ESM: uses static syntax for imports/exports.

### Question 32

- Put the logs in the correct order.

```js
import { createRequire } from 'module';
const require = createRequire(import.meta.url);

require('./file1.js');
import './file2.mjs'; // import will be hoisted to the top
import './file3.mjs';

function getModule() {
  import('./file4.mjs'); // this is a dynamic import, it will return a promise, and the import will be executed asynchronously
  require('./file5.js');
}

getModule();
```

```js
// file1.js
console.log('file1');
module.exports = {};

// file2.mjs
console.log('file2');
export default {};

// file3.mjs
// top level await will block the execution of the code
await new Promise((res) => {
  setTimeout(() => {
    res();
  }, 0);
});

console.log('file3');
export default {};

// file4.mjs
console.log('file4');
export default {};

// file5.js
console.log('file5');
module.exports = {};
```

will output:

```js
file2;
file3;
file1;
file5;
file4;
```

### Question 33

- Which of the following statements correctly describe the differences between `require` and `import`?

A. `require` can be called conditionally, while the `import` statement can not. // true
B. `import` statements are hoisted, but `require` statements are not. // true
C. `require` synchronous loads modules, while `import` can load modules asynchronously. // true
D. `import` allows for static analysis and tree shaking, but `require` does not. // true

## Numbers

### Question 34

- What gets logged?

```js
let number = 0;
console.log(number++); // 0
console.log(++number); // 2
console.log(number); //2
```

### Question 35

- What gets logged?

```js
const a = 3;
const b = new Number(3);
const c = 3;

console.log(a == b); // true
console.log(a === b); // false
console.log(b === c); // false
console.log(a.toString() === b.toString()); // true
```

### Question 36

- What gets logged?

```js
const a = isNaN('5.2' + 2); // false
const b = isNaN(parseInt(a)); // true
const c = isNaN(parseFloat(a)); // true
const d = isNaN('1 * 2' * 2); // true
console.log(a, b, c, d);
```

### Question 37

- Match the equivalent values

```js
// 'ff' === 255..toString(16);
// '11111111' === 255['toString'](2);
// SyntaxError 255.toString(10); This will throw a syntax error because the dot is interpreted as a decimal point
// '255' === 255['toString'](10);
```

## Miscellaneous

### Question 38

- Which methods will return the value "Hello world"?

```js
const myMap = new Map();
const myFunc = () => 'greeting';

myMap.set(myFunc, 'Hello world');
```

A. `myMap.get('greeting')` // false
B. `myMap.get(myFunc)` // true
C. `myMap.get(() => "greeting")` // false
D. `myMap.get(myFunc())` // false
E. None of the above // false

### Question 39

- What does the `person` object look like after executing the following code?

```js
const person = {
  name: 'Jane',
  address: {
    city: 'Amsterdam',
  },
};

Object.freeze(person);

const personProxy = new Proxy(person, {
  set(target, key, value) {
    return Reflect.set(target, key, value);
  },
});

personProxy.name = 'Sarah';
personProxy.address.city = 'Paris';

console.log(person);
```

will output:

```js
{
  name: 'Jane',
  address: {
    city: 'Paris',
  },
}
// because Object.freeze() only freezes the first level of the object
```

### Question 40

- What will be the output of the following code if it executed in a module?

```js
const obj = Object.freeze({ name: 'Alice' });

obj.name = 'Bob';

console.log(obj.name);
// Will throw a TypeError because the object is frozen
```

### Question 41

- Which method should we choose so it logs `{name: "Lydia", age: 25}`?

```js
const keys = ['name', 'age'];
const values = ['Lydia', 25];

const method = 'fromEntries';
Object[method](keys.map((_, i) => [keys[i], values[i]]));
```

### Question 42

- What gets logged?

```js
const array = [1, 2, 3, 4, 5];
array.splice(2); // [1, 2]
array.concat(6); // new array [1, 2, 6]
allay.slice(0, 1); // new array [1, 2]
delete array[0]; // [empty, 2]

console.log(array.length); // 2
```

### Question 43

- What gets logged?

```js
const config = {
  languages: [],
  set language(lang) {
    this.languages.push(lang);
  },
};

config.language = 'EN';
console.log(config.language); // undefined
```

### Question 44

- What gets logged?

```js
const team = {
  members: [
    {
      info: {
        street: undefined,
        city: 'New York',
      },
    },
    {
      info: {
        street: '',
        city: 'New York',
      },
    },
    {
      info: {
        street: null,
        city: 'New York',
      },
    },
    null,
  ],
};

const getInfo = (mem) => {
  const info = mem?.info;
  return (info?.street ?? info?.city) || 'Unknown';
};

const result = team.members.map(getInfo);
// [ 'New York', 'Unknown', "New York", 'Unknown' ]
```

### Question 45

- Match the code to correct error it would throw

```js
new Array(-1); // RangeError: Invalid array length
25.toString(); // SyntaxError: Invalid or unexpected token
[].reduce((acc, cur) => acc + cur); // TypeError: Reduce of empty array with no initial value
const { a: b = c} = {a: undefined, c: 12} // ReferenceError: c is not defined
const a = {b : 1}; console.log((a ?? 1) || b); // No error
```

### Question 46

- Match the date methods with the correct values when invoked on `new Date()`

```js
toDateString(); // "Wed Apr 05 2023"
toISOString(); // "2023-04-05T07:00:00.000Z"
toLocalDateString(); // "4/5/2023"
toUTCString(); // "Wed, 05 Apr 2023 07:00:00 GMT"
toString(); // "Wed Apr 05 2023 00:00:00 GMT+0700 (Indochina Time)"
```

### Question 47

- What gets logged?

```js
const {
  a = 'default',
  b = 'default',
  c = 'default',
  d = 'default',
} = {
  a: null,
  b: undefined,
  c: false,
  d: 0,
};

console.log(a, b, c, d);
// 'null', 'default', 'false', 0
```

### Question 48

- What gets logged?

```js
const symbolOne = Symbol.for('key');
const symbolTwo = Symbol('key');
const symbolThree = Symbol.for('key');

console.log(symbolOne === symbolTwo); // false
console.log(symbolTwo === symbolThree); // false
console.log(symbolOne === symbolThree); // true
console.log(symbolThree === Symbol('key')); // false
```

### Question 49

- What gets logged?

```js
function compareUsers(user1, user2 = user) {
  console.log(user1 === user2);
}

const user = { name: 'Lydia' };
compareUsers(user, { ...user }); // false
compareUsers(user); // true

compareUsers({ name: 'Lydia' }, { name: 'Lydia' }); // false
```

### Question 50

- What gets logged?

```js
function thankYouTag(strings, greeting) {
  console.log(strings, greeting);
}

const greeting = 'Thank you for coming to my workshop!';
thankYouTag`This is the last question! ${greeting}`;
// [ 'This is the last question! ', '' ] 'Thank you for coming to my workshop!'
```
