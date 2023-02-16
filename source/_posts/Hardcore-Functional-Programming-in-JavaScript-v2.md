---
title: Hardcore Functional Programming in JavaScript v2
date: 2020-04-28 21:52:50
tags: [FrontEndMasters, JavaScript, Functional Programming, Monads, Functor, Point-Free]
---

## Introduction

+ [Slides](https://docs.google.com/presentation/d/1nj5xmsHeJh-6RdjLs1190Hwl8smclvFLePqPCTVsrYw/edit#slide=id.g338d117be_040)

+ [repo](https://github.com/FrontendMasters/hardcore-functional-js-v2)

### Mathematical, Pure Functions

+ Functional Programming
  + Programming with functions

+ Set theoretically
  + Every function is a single-valued collection of pairs
  + One input, one output
<!-- more -->
```js
const toLowerCase = {
  A: "a",
  B: "b",
  C: "c",
  D: "d",
  E: "e",
  F: "f",
};

toLowerCase["C"]; // c

const isPrime = { 1: false, 2: true, 3: true, 4: false, 5: true, 6: false };

isPrime[3]; // true
```

+ Functions
  1. Total
  2. Deterministic
  3. No Observable Side-Effects

+ Total
  + For every input there is a corresponding output

```js
// 很显然，我们不能枚举每种情况，所以会有返回undefined的情况
const inc = i => {
  if(i === 0) return 1
  if(i === 1) return 2
  if(i === 2) return 3
}

// 一般而言，每个输入都会返回一个结果
const inc = i => {
  return i + 1
}
```

+ Deterministic
  + Always receive the same output for a given input(幂等)

```js
// not deterministic
const timeSince = comment => {
  const now = new Date()
  const then = new Date(comment.createdAt)
  return getDifference(now, then)
}

// deterministic
const getDifference = (now, then) => {
  const days = Math.abs(now.getDate() - then.getDate())
  const hours = Math.abs(now.getHours() - then.getHours())
  return {day, hours}
}
```

+ No Side Effects
  + No observable effects besides computing a value

```js
const add = (x, y) => {
  // side effect
  console.log(`Adding ${x} ${y}`)
  return x + y
}

const add = (x, y) => {
  return { result: x + y, log: `Adding ${x} ${y}` }
}
```

### Pure Functions Checklist

```js
var xs = [1, 2, 3, 4. 5]

// not a function
// 同样的输入，不同的返回值
xs.splice(0, 3) // => [1, 2, 3]
xs.splice(0, 3) // => [4, 5]
xs.splice(0, 3) // => []

// function
xs.slice(0, 3) // => [1, 2, 3]
xs.slice(0, 3) // => [1, 2, 3]
xs.slice(0, 3) // => [1, 2, 3]
```

```js
// not a function
const toSlug = (title) => {
  const urlFriendly = title.replace(/W+/ig, '-')
  if(urlFriendly.length < 1) {
    throw new Error('is bad')
  }
  return urlFriendly
}

// function
const toSlug = (title) => {
  return new Promise((res, rej) => {
    const urlFriendly = title.replace(/\W+/ig, '-')

    if(urlFriendly.length < 1) {
      rej(new Error('is bad'))
    }

    return res(urlFriendly)
  })
}
```

```js
// not a function
const signUp = (attrs) => {
  const user = saveUser(attrs)
  welcomeUser(user)
}

// function
const signUp = (attrs) => {
  return () => {
    const user = saveUser(attrs)
    welcomeUser(user)
  }
}
```

```js
const birthday = user => {
  user.age += 1
  return user
}
// not a function

const shout = word =>
  word.toUpperCase().concat('!')
// function

const headerText = header_selector =>
  querySelector(header_selector).text()
// not a function

const parseQuery = () =>
  location.search.substring(1).split('&').map(x => x.split('='))
// not a function, location is a global variable
```

### Pure Functions Advantages

+ Why?
  + Reliable
  + Portable
  + Reuseable
  + Testable
  + Composable
  + Properties/Contract

## Currying

### Properties, Arguments & Currying

```js
// associative
add(add(x, y), z) == add(x, add(y, z))

// commutative
add(x, y) == add(y, x)

// identity
add(x, 0) == x

// distributive
add(multiply(x, y), multiply(x, z)) == multiply(x, add(y, z))
```

```js
const url = (t) => `http://gdata.youtube.com/feeds/api/videos?q=${t}&alt=json`;

const src = _.compose(
  _.prop("url"),
  _.head,
  _.prop("media$thumbnail"),
  _.prop("media$group")
);

const srcs = _.compose(_.map(src), _.prop("entry"), _.prop("feed"));

const images = _.compose(_.map(imageTag), srcs);

const widget = _.compose(_.map(images), getJSON, url)

widget('cats').fork(log, setHtml(document.querySelector('#youtube')))
```

```js
const add = (x, y) => x + y;

const toPair = (f) => ([x, y]) => f(x, y);

const fromPair = (f) => (x, y) => f([x, y]);

const result = fromPair(toPair(add))(1, 2); // 3

const flip = (y, x) => f(x, y);

const curry = (f) => (x) => (y) => f(x, y);

const curriedAdd = curry(add);

const increment = curriedAdd(1);

const three = increment(2)

const uncurry = (f) => (x, y) => f(x)(y);

const modulo = curry((x, y) => y % x);

const isOdd = modulo(2);
```

### Currying Example & Argument Order

```js
const filter = (f, xs) => xs.filter(f)
// 可以看到这里第一个参数是函数，第二个才是数据，这样我们才可以写出如下代码
const getOdds = filter(isOdd)
const result = getOdds([1, 2, 3, 4])
// 如果我们首先传入数据，就无法简单抽象出对数据的操作
// 我们要写出如下代码
const filter = (xs, f) => xs.filter(f)
const getOdds = (xs) => xs.filter(f)
const result = getOdds([1, 2, 3, 4])
```

### Ramda Generalized Currying

```js
const replace = (regex, replacement, str) => str.replace(regex, replacement);

const replaceVowels = replace(/[AEIOU]/gi, "!");
```

### Partial Application vs Currying

partial application一次传入部分参数（可以是多个）, curry一次传入一个参数。

### Currying Exercise

+ [Exercise link](https://codepen.io/drboolean/pen/OJJOQMx?editors=1010)

### Composition

```js
const toUpper = (str) => str.toUpperCase();

const exclaim = (str) => str + "!";

const first = (xs) => xs[0];

const compose = (f, g) => (x) => f(g(x));

const shout = compose(exclaim, toUpper);

console.log(shout("tear")); // TEAR!

console.log(compose(first, compose(exclaim, toUpper))("tear")); // T
console.log(compose(compose(first, exclaim), toUpper)("tear")); // T

const loudFirst = compose(toUpper, first);
const shoutFirst = compose(exclaim, loudFirst);
console.log(shoutFirst("tears")); // T!
```

### Creating Programs with Curry & Compose

使用curry和compose可以使多元或二元函数转换为一元函数(一次接收一个参数)

```js
const _ = require("ramda");

const doStuff = _.compose(
  _.join(""),
  _.filter((x) => x.length > 3),
  _.reverse,
  _.map(_.trim),
  _.split(" "),
  _.toLower
);
// 等同于
const doStuff = (str = "") => {
  const lower = str.toLowerCase();
  const words = lower.split(" ");

  words.reverse();

  for (let i in words) {
    words[i] = words[i].trim();
  }

  const keepers = [];

  for (let i in words) {
    if (words[i].length > 3) {
      keepers.push(words[i]);
    }
  }

  return keepers.join("");
};
```

### Composition is Dot Chaining

```js
// 以上代码使用链式调用风格，可以书写为如下
const doStuff = (str = "") =>
  str
    .toLowerCase()
    .split(" ")
    .map((c) => c.trim())
    .reverse()
    .filter((x) => x.length > 3)
    .join("");
```

### Logging in Composition

```js
const log = curry((tag, x) => (console.log(tag, x), x));
const loudFirst = compose(toUpper, first);
const shoutFirst = compose(exclaim, loudFirst);
const logShoutFirst = compose(shoutFirst, log("here")); // here tears
console.log(logShoutFirst("tears")); // T!
```

### Compose Practice

+ [Exercise Link](https://codepen.io/drboolean/pen/zYYPmZO)

## Functors

### Creating the Identity Functor

```js
const nextCharForNumberString = (str = "") => {
  const trimmed = str.trim();
  const number = parseInt(trimmed);
  const nextNumber = number + 1;
  return String.fromCharCode(nextNumber);
};

console.log(nextCharForNumberString("   64  ")); // A
```

```js
const Box = (x) => ({
  map: (f) => Box(f(x)),
  toString: `Box(${x})`,
  fold: (f) => f(x),
});

const nextCharForNumberString = (str = "") => {
  return Box(str)
    .map((x) => x.trim())
    .map((x) => parseInt(x))
    .map((x) => x + 1)
    .fold(String.fromCharCode);
};

console.log(nextCharForNumberString("   64  ")); // A;
```

### Refactoring to Dot Chaining

```js
const first = (xs) => xs[0];

const halfTheFirstLargeNumber = (xs) => {
  const found = xs.filter((x) => x >= 20);
  const answer = first(found) / 2;
  return `The answer is ${answer}`;
};

const res = halfTheFirstLargeNumber([1, 4, 50]);
console.log(res); // The answer is 25
```

```js
const halfTheFirstLargeNumber = (xs) =>
  Box(xs)
    .map((xs) => xs.filter((x) => x >= 20))
    .map((found) => first(found) / 2)
    .fold((answer) => `The answer is ${answer}`);

const res = halfTheFirstLargeNumber([1, 4, 50]);
console.log(res); // The answer is 25
```

```js
const compose = (f, g) => (x) => Box(x).map(g).fold(f);
```

### Functor Practice

+ [Exercise Link](https://codepen.io/drboolean/pen/poodxOm?editors=0010)

```js
const Box = (x) => ({
  map: (f) => Box(f(x)),
  toString: `Box(${x})`,
  fold: (f) => f(x),
  /** flatMap */
  chain: (f) => f(x),
});
```

## Either Monad

### Either

```js
const findColor = (name) =>
  ({
    red: "#ff4444",
    blue: "#3b5998",
    yellow: "3fff68f",
  }[name]);

console.log(findColor("red").toUpperCase()); // #FF4444
console.log(findColor("redddddd").toUpperCase()); // through new Error
```

```js
/**
 * Left用于处理错误情况，传入map的函数并不执行而是直接将值传递下去
 * 在fold中，用第二个参数处理错误
 */
const Left = (x) => ({
  chain: (f) => Left(x),
  map: (f) => Left(x),
  fold: (f, g) => f(x),
  toString: `Left(${x})`,
});

/**
 * Right用于处理一般情况，在map中调用回调，
 * 在fold的二个参数中处理一般情况，一般来说传递 x => x 获取到值
 */
const Right = (x) => ({
  chain: (f) => f(x),
  map: (f) => Right(f(x)),
  fold: (f, g) => g(x),
  toString: `Right(${x})`,
});

const findColor = (name) => {
  const found = {
    red: "#ff4444",
    blue: "#3b5998",
    yellow: "3fff68f",
  }[name];

  return found ? Right(found) : Left("not found");
};

console.log(
  findColor("red")
    .map((x) => x.toUpperCase())
    .fold(
      () => "no color",
      (x) => x
    )
);
// #FF4444

console.log(
  findColor("redd")
    .map((x) => x.toUpperCase())
    .fold(
      () => "no color",
      (x) => x
    )
);
// no color
```

### fromNullable

```js
const fromNullable = (x) => (x !== null && x !== undefined ? Right(x) : Left());

const findColor = (name) =>
  fromNullable(
    {
      red: "#ff4444",
      blue: "#3b5998",
      yellow: "3fff68f",
    }[name]
  );

console.log(
  findColor("red")
    .map((x) => x.toUpperCase())
    .fold(
      () => "no color",
      (x) => x
    )
);
// #FF4444

console.log(
  findColor("redd")
    .map((x) => x.toUpperCase())
    .fold(
      () => `no color`,
      (x) => x
    )
);
// no color
```

### Refactoring Using the Either Monad

```js
const fs = require("fs");

const getPort = () => {
  try {
    const str = fs.readFileSync("config.json");
    const config = JSON.parse(str);
    return config.port;
  } catch (e) {
    return 3000;
  }
};

console.log(getPort()); // 3000
```

```js
const tryCatch = (f) => {
  try {
    return Right(f());
  } catch (e) {
    return Left(e);
  }
};

const readFileSync = (path) => tryCatch(() => fs.readFileSync(path));

const getPort = () =>
  readFileSync("./config.json")
    .map((content) => JSON.parse(content))
    .map((config) => config.port)
    .fold(
      () => 8080,
      (x) => x
    );

console.log(getPort()); // 8080
```

### Flattering Either Monads with Chain

```js
/**
 * 通过chain可以将套嵌的Left或者Right展开，类似flatMap
 */
const Left = (x) => ({
  chain: (f) => Left(x),
  map: (f) => Left(x),
  fold: (f, g) => f(x),
  toString: `Left(${x})`,
});

const Right = (x) => ({
  chain: (f) => f(x),
  map: (f) => Right(f(x)),
  fold: (f, g) => g(x),
  toString: `Right(${x})`,
});

const tryCatch = (f) => {
  try {
    return Right(f());
  } catch (e) {
    return Left(e);
  }
};

const readFileSync = (path) =>
  tryCatch(() => fs.readFileSync(path.join(__dirname + path)));

const parseJSON = (content) => tryCatch(() => JSON.parse(content));

const getPort = () =>
  readFileSync("config.json")
    .chain((content) => parseJSON(content))
    .map((config) => config.port)
    .fold(
      () => 8080,
      (x) => x
    );

console.log(getPort()); // 8080
```

### Either Practice

+ [Exercise Link](https://codepen.io/drboolean/pen/xgoeWR?editors=0010)

### Debugging with Logging

```js
const logIt = x => {
  console.log(x)
  return x
}
```

## Task

### Task Monad

```js
Task.of(2).map(two => two + 1) // Task(2)

const t1 = Task((rej, res) => res(2))
  .map(two => two + 1)
  .map(three => three + 1)
/**
 * Task 不同于 promise 的两点
 * 首先是error first的回调函数处理风格
 * 更重要的是Task是lazy的，在调用fork之前并不执行
 */
t1.fork(console.error, console.log) // 6
```

### Refactoring Node IO with Task

```js
const app = () =>
  fs.readFile('config.json', 'utf-8', (err, contents) => {
    console.log(err, contents)
    if(err) throw err

    const newContents = contents.replace(/3/g, '6')

    fs.writeFile('config1.json', newContents, (err, _) => {
      if(err) throw err
      console.log('success!')
    })
  })

app()
```

```js
const readFile = (path, enc) =>
  Task((rej, res) =>
    fs.readFile(path, enc, (err, contents) =>
      err ? rej(err) : res(contents)
    )
)

const writeFile = (path, contents) =>
  Task((rej, res) =>
    fs.writeFile(path, contents, (err, contents) =>
      err ? rej(err) : res(contents)
    )
)

const app = () =>
  readFile('config.json', 'utf-8') // Task(contents)
    .map(contents => contents.replace(/3/g, '6'))
    .chain(newContents => writeFile('config1.json', newContents))

app()
  .fork(console.error, () => console.log('success'))
```

### Task Practice

+ [Exercise Link](https://codepen.io/drboolean/pen/Mparbp?editors=0010)

### Transform & Monad Patterns

```js
const httpGet = (path, params) =>
  Task.of(`${path}: result`)

const getUser = x => httpGet('/user', { id: x})
const getTimeline = x => httpGet(`/timeline/${x}`, {})
const getAds = () => httpGet('/ads', {})

List([getUser, getTimeline, getAds])
  .traverse(Task.of, f => f())
  .fork(console.log,  x => console.log(x.toJS()))
```

```js
const greaterThan5 = x =>
  x.length > 5 ? Right(x) : Left('not greater than 5')

const looksLikeEmail = x =>
  x.match(/@/ig) ? Right(x) : Left('not an email')

const email = 'blahh@yadda.com'

const res = [greaterThan5, looksLikeEmail]
  .map(x => x(email))
console.log(res)

const res2 = List([greaterThan5, looksLikeEmail])
  .traverse(Either.of, x => x(email))
res2.fold(console.log, x => console.log(x.toJS()))
```
