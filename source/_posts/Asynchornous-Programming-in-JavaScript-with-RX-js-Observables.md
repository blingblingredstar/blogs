---
title: Asynchornous Programming in JavaScript(with RX.js Observables)
date: 2019-07-06 16:58:35
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, rxjs, Asynchronous Programming, Functional Programming]
---

## Building Blocks

### Introduction

The goal: To learn how to solve big async problems just by thinking differently about events
目标:通过对事件的不同思考来学习如何解决大型异步问题

#### Async seems hard

+ Race Conditions
  竞态条件
+ Memory Leaks
  内存泄漏
+ Complex State Machines
  复杂状态
+ Uncaught Async Errors
  无法捕获异步错误
<!-- more -->
#### Playing a Movie Asynchronously

```js
function play(movieId, cancelButton, callback) {
  var movieTicket,
      playError,
      tryFinish = function() {
        if (playError) {
          callback(null, playError)
        }
        else if (movieTicket && palyer.initialized) {
          callback(null, ticket)
        }
      }
  cancelButton.addEventListener('click', () => { playError = 'callcelled' }) // 这里只绑定了取消事件，执行后并没有取消绑定事件，下次开始播放时会绑定另一个取消事件，引发内存泄漏
  if (!player.initialized) {
    player.init((error) => {
      playError = error
      tryFinish()
    })
  }
  authorizeMovie((error, ticket) => {
    playError = error
    movieTicket = ticket
    tryFinish()
  })
}
```

Today is going to show how to write complex async programs using just a fex **flexible** functions.
今天将展示如何使用**灵活的**函数编写复杂的异步程序。

### JavaScript Tutorial

#### 箭头函数

```js
function add1(x) {
  return x + 1
}

const add1 = x => x + 1

function addTwo(x, y) {
  return x + y
}

const addTwo = (x, y) => x + y
```

#### ForEach

```js
[1, 2, 3].forEach(item => console.log(item))
```

#### Map

```js
[1, 2, 3].map(item => item + 1)
```

#### Filter

```js
[1, 2, 3].filter(item => item > 1)
```

#### concatAll

not in JavaScript, it takes a multidimensional array and flattens it by on dimension
不是在JavaScript中，它接受一个多维数组并按维将其压扁

```js
[[1], [2, 3], [], [4]].concatAll() // [1, 2, 3, 4]
```

新增的`Array.prototype.flat()`，`Array.prototype.flatMap()`功能类似。

### Creating Collections

#### Top-rated Movies Collection

```js
const getTopRatedFilms = user =>
  user.videoLists
    .map(videoList =>
        videoList.videos
          .filter(video => video.rating === 5.0)
      )
    .concatAll()

getTopRatedFilms(user)
  .forEach(film => { console.log(film) })
```

#### Mouse Drags Collection

```js
const getElementDrags = ele =>
      ele.mouseDowns
        .map(mouseDown =>
          document.mouseMoves
            .takeUntil(document.mouseUps)
          )
        .concatAll()

getElementDrags(image)
          .forEach(pos => image.position = pos)
```

### Iterators & Observers

Events and Arrays are both **collections**.
事件和数组都是**集合**。

So why don’t we program them the same way?

Iterator and Observer是两种存在已久的设计模式

#### Iterator

```js
const iterator = [1, 2, 3][Symbol.iterator]()
console.log(iterator.next())
// {value: 1, done: false}
console.log(iterator.next())
// {value: 2, done: false}
console.log(iterator.next())
// {value: 3, done: false}
console.log(iterator.next())
// {value: undefined, done: true}
```

**Map**, **Filter**, and **ConcatAll** can be implemented using an Iterator.

在迭代器模式中，iterator是consumer，决定了什么时候获取数据

#### Observer Pattern

```js
document.addEventListener(
  'mousemove',
  function next(e) {
    console.log(e)
  }
)
```

在观察者模式中，producer决定何时将数据推入集合

#### The Iterator and Observer Pattern are Symmetrical

In Observer pattern producer iterate you
在Observer模式生成器中迭代

As a result, they gave Iterator and Observer different semantics

So many push APIS

+ DOM Events
+ Websockets
+ Server-sent Events
+ Node Streams
+ Service Workers
+ JQuery Events
+ XMLHttpRequest
+ setInterval

## Observables

### Observables Introduction

#### Introducing Observable

Observable === Collection + Time

Observables can model

+ Events
+ Async Server Requests
+ Animations

#### Reactive Extensions

+ Events as Streams
+ Open Source(Apache2)
+ Ported to many languages
  + C
  + .NET
  + JavaScript
  + Java(Netflix)
  + Objective-C

[ReactiveX - An API for asynchronous programming with observable streams](http://reactivex.io/)

Events to Observables

```js
var mouseMoves =
  Observable
    .fromEvent(element, 'mousemove')
```

| Adapt Push APIs to Observable |
| ----------------------------- | ---------- |
| DOM Events                    |            |
| Websockets                    |            |
| Server-sent Events            |            |
| Node Stream                   | Observable |
| Service Workers               |            |
| JQuery Events                 |            |
| XMLHttpRequest                |            |
| setInterval                   |            |

#### Event Subscription

```js
// "subscribe"
const handler = e => console.log(e)
document.addEventListener('mousemoves', handler)

// "unsubscibe"
document.removeEventListener('mousemoves', handler)
```

#### Observable.forEach

```js
// "subscribe"
const subscription =
  mouseMoves.forEach(console.log)

// "unsubcribe"
subcription.dispose()
```

#### Expanded Observable.forEach

```js
// "subscribe"
const subscription =
  mouseMoves.forEach(
    // next data
    event => console.log(event)
    // error
    error => console.log(error)
    // complete
    () => console.log('Done')
  )

// "unsubscribe"
subscription.dispose()
```

```js
const subscription =
  mouseMoves.forEach({ // Observer
    onNext: event => console.log(event),
    // error
    onError: error => console.log(error),
    // completed
    onCompleted: () => console.log('done')
  })

// "unsubscribe"
subscription.dispose()
```

### Observable Metaphor

iterator 和 observer 的区别就是是producer(数据提供者)还是consumer(数据接收使用者)占据主动。 所以有时两者可以互相转换。

就像分蛋糕，分蛋糕的人是producer，吃蛋糕的是consumer，可以由consumer根据自己的需求要蛋糕，也可以由producer根据自己的意愿分蛋糕。

#### Converting Events to Observables

```js
Observable.fromEvent = function(dom, eventName) {
  // returning Observable object
  return {
    forEach: function(observer) {
      const handler = e => observer.onNext(e)
      dom.addEventListener(eventName, handler)

      // returning Subscription object
      return {
        dispose: function() {
          dom.removeEventListener(eventName, handler)
        }
      }
    }
  }
}
```

### Observables in Action

Observer的forEach,map,filter等方法与数组的不同，是一个异步方法，当数据流更新时，马上对新数据进行处理，并不对旧数据进行保存。

使用concatAll方法，可以将数据流依次收集起来，合并为一个有序的一维数组，在需要的时候在对数据进行处理。

### Race Conditions & Nested Observables

通过concatAll，可以将一个有限的数据流缓存起来，转换为一个有序的一维数组，这样对一维数组进行处理，因为数据流按时间顺序尽心了排序，就可以对race condition进行处理。所以也不建议使用cancatAll对一个无限的数据流进行缓存。

### TakeUntil

takeUntil需要两个参数，一个是Source Collection，一般是一个无限的数据流，另一个是Stop Collection，是停止条件，当停止事件触发时，停止监听Source Collection。

之前停止监听的方法是使用`.dipose()`停止订阅，现在可以使用另一个事件作为停止条件自动停止订阅。

### Implementing Mouse Move

JS中的异步编程使用了event loop模型，独立于主线程之外，所以递归的尾调用对于异步编程来说并没有什么作用（尾调用把递归变为类似循环，发生在主线程之内，会堵塞主线程）。
<!-- markdownlint-disable md024 -->
#### Mouse Drags Collection

```js
const getElementDrags = ele => {
  ['mouseMoves', 'mouseDowns', 'mouseUps']
  .forEach(key =>
    ele[key] = Observable.fromEvent(ele, key)
  )

  return ele.mouseDowns
    .map(mouseDown =>
      document.mouseMoves
        .takeUntil(document.mouseUps)
    )
    .concatAll()
}

getElementDrags(image)
  .forEach(pos => image.position = pos)
```

### MergeAll & SwitchLatest

mergeAll按照时间顺序对数据流进行合并(concatAll会按照多个数据流，每个数据流开始的顺序进行合并，在当前数据流完成（onComplete发生）前不会跳转到下一个数据流，mergeAll会按照多个数据流的每一个数据的时间尽心合并，下一个数据流的数据下发生时，会合并到最终数据流中。

switchLatest会监听最后一个数据流，既多个数据流传入时，只监听最后传入的数据流。当新的数据流传入时，会对当前数据流停止监听，并开始监听新传入的数据流。

### Netflix Search Box

Don't unsubscribe from Events.*Complete them when another event fires*.

```js
const searchResultSets =
  keyPresses
    .throttle(250)
    .map(key =>
      getJSON(`/searchResults?q=${input.value}`)
        .retry(3)
        .takeUntil(keyPresses)
    )
    .concatAll()

searchResultSets.forEach(
  resultSet => updateSearchResults(resultSet),
  error => showMessage('the server appears to be down.')
)
```

As we begin to solves these problems, we are gonna start describing them in four steps:

1. What collections do I have?
2. What collection do I want?
3. How do I get from the collections I have to the collection that I want？
4. Once I've got the collection that I want, what am I gonna to do with the data that comes out of it?

### Optimizing the Search

```js
const searchResultSets =
  keyPresses
    .throttle(250)
    .map(key =>
      getJSON(`/searchResults?q=${input.value}`)
        .retry(3)
        // .takeUntil(keyPresses)
    )
    // .concatAll()
    .switchLatest() // 注释掉的两段代码的作用可以使用switchLatest完成

searchResultSets.forEach(
  resultSet => updateSearchResults(resultSet),
  error => showMessage('the server appears to be down.')
)
```

这里提到了promise，并提出了observable由于promise的三点：

1. observable可以取消
2. observable可以重试
3. 有些网络请求是一个数据流（websockets），更适合使用observable

### Three-Dimensional Collections

#### Player Callback Hell

```js
function play(movieId, cancelButton, callback) {
  var movieTicket,
      playError,
      tryFinish = function() {
        if (playError) {
          callback(null, playError)
        }
        else if (movieTicket && palyer.initialized) {
          callback(null, ticket)
        }
      }
  cancelButton.addEventListener('click', () => { playError = 'callcelled' }) // 这里只绑定了取消事件，执行后并没有取消绑定事件，下次开始播放时会绑定另一个取消事件，引发内存泄漏
  if (!player.initialized) {
    player.init((error) => {
      playError = error
      tryFinish()
    })
  }
  authorizeMovie((error, ticket) => {
    playError = error
    movieTicket = ticket
    tryFinish()
  })
}
```

#### Player with Observable

```js
var authorizations =
  player
    .init()
    .map(() =>
      playAttempts
        .map(movieId =>
          player.authorize(movieId)
            .catch(e => Observable.empty)
            .takeUntil(cancles)
        )
        .cancatAll()
    ).concatAll()

authorizations.forEach(
  license => player.play(license),
  error => showDialog("Sorry, can't play right now.")
)
```

## Creating Array Functions

### Working with Arrays

[Interactive Learning Exercises](http://jhusain.github.io/learnrx/)

The Array is Javascript's only collection type. Arrays are everywhere. We're going to add the five functions to the Array type, and in the process make it much more powerful and useful. As a matter of fact, Array already has the map, filter, and reduce functions! However we're going to reimplement these functions as a learning exercise.
数组是Javascript唯一的集合类型。数组是无处不在。我们将把这五个函数添加到数组类型中，在这个过程中使它变得更加强大和有用。事实上，Array已经具有map、filter和reduce函数!不过，我们将把这些函数重新实现为一个学习练习

This section will follow a pattern. First we'll solve problems the way you probably learned in school, or on your own by reading other people's code. In other words, we'll transform collections into new collections using loops and statements. Then we'll implement one of the five functions, and then use it to solve the same problem again without the loop. Once we've learned the five functions, you'll learn how to combine them to solve complex problems with very little code. The first two exercises have been completed in advance, but please look them over carefully!

本节将遵循一种模式。首先，我们将用你可能在学校学到的方法来解决问题，或者你自己通过阅读别人的代码来解决问题。换句话说，我们将使用循环和语句将集合转换为新的集合。然后我们将实现这五个函数中的一个，然后使用它来解决同样的问题，而不需要循环。一旦我们学习了这五个函数，您将学习如何将它们组合在一起，用很少的代码解决复杂的问题。前两个练习已经提前完成了，请仔细检查!

#### Traversing an Array

#### Exercise 1: Print all the names in an array

```js
function(console) {
  var names = ["Ben", "Jafar", "Matt", "Priya", "Brian"],
    counter;

  for(counter = 0; counter < names.length; counter++) {
    console.log(names[counter]);
  }
}
```

#### Exercise 2: Use forEach to print all the names in an array

```js
function(console) {
  var names = ["Ben", "Jafar", "Matt", "Priya", "Brian"];

  names.forEach(function(name) {
    console.log(name);
  });
}
```

### Projecting Arrays

Applying a function to a value and creating a new value is called a projection. To project one array into another, we apply a projection function to each item in the array and collect the results in a new array.

#### Exercise 3: Project an array of videos into an array of {id,title} pairs using forEach()
<!-- markdownlint-disable md033 -->
<details>

<summary>
For each video, add a projected {id, title} pair to the videoAndTitlePairs array.
</summary>

```js
function() {
  var newReleases = [
    {
      "id": 70111470,
      "title": "Die Hard",
      "boxart": "http://cdn-0.nflximg.com/images/2891/DieHard.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": [4.0],
      "bookmark": []
    },
    {
      "id": 654356453,
      "title": "Bad Boys",
      "boxart": "http://cdn-0.nflximg.com/images/2891/BadBoys.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": [5.0],
      "bookmark": [{ id: 432534, time: 65876586 }]
    },
    {
      "id": 65432445,
      "title": "The Chamber",
      "boxart": "http://cdn-0.nflximg.com/images/2891/TheChamber.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": [4.0],
      "bookmark": []
    },
    {
      "id": 675465,
      "title": "Fracture",
      "boxart": "http://cdn-0.nflximg.com/images/2891/Fracture.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": [5.0],
      "bookmark": [{ id: 432534, time: 65876586 }]
    }
  ],
  videoAndTitlePairs = [];

  // ------------ INSERT CODE HERE! -----------------------------------
  // Use forEach function to accumulate {id, title} pairs from each video.
  // Put the results into the videoAndTitlePairs array using the Array's
  // push() method. Example: videoAndTitlePairs.push(newItem);
  // ------------ INSERT CODE HERE! -----------------------------------
  newReleases.forEach(({id, title}) => videoAndTitlePairs.push({id, title}))

  return videoAndTitlePairs;
}
```

</details>

All array projections share two operations in common:

1. Traverse the source array
2. Add each item's projected value to a new array
Why not abstract away how these operations are carried out?

#### Exercise 4: Implement map()

To make projections easier, let's add a map() function to the Array type. Map accepts the projection function to be applied to each item in the source array, and returns the projected array.

```js
Array.prototype.map = function(projectionFunction) {
  var results = [];
  this.forEach(function(itemInArray) {

    // ------------ INSERT CODE HERE! ----------------------------
    // Apply the projectionFunction to each item in the array and add
    // each result to the results array.
    // Note: you can add items to an array with the push() method.
    // ------------ INSERT CODE HERE! ----------------------------
    results.push(projectionFunction(itemInArray))
  });

  return results;
};

// JSON.stringify([1,2,3].map(function(x) { return x + 1; })) === '[2,3,4]'
```

#### Exercise 5: Use map() to project an array of videos into an array of {id,title} pairs

<details>
<summary>
Let's repeat the exercise of collecting {id, title} pairs for each video in the newReleases array, but **this time we'll use our map function**.
</summary>

```js
function() {
  var newReleases = [
    {
      "id": 70111470,
      "title": "Die Hard",
      "boxart": "http://cdn-0.nflximg.com/images/2891/DieHard.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": [4.0],
      "bookmark": []
    },
    {
      "id": 654356453,
      "title": "Bad Boys",
      "boxart": "http://cdn-0.nflximg.com/images/2891/BadBoys.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": [5.0],
      "bookmark": [{ id: 432534, time: 65876586 }]
    },
    {
      "id": 65432445,
      "title": "The Chamber",
      "boxart": "http://cdn-0.nflximg.com/images/2891/TheChamber.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": [4.0],
      "bookmark": []
    },
    {
      "id": 675465,
      "title": "Fracture",
      "boxart": "http://cdn-0.nflximg.com/images/2891/Fracture.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": [5.0],
      "bookmark": [{ id: 432534, time: 65876586 }]
    }
  ];

  // ------------ INSERT CODE HERE! -----------------------------------
  // Use map function to accumulate {id, title} pairs from each video.
  return newReleases.map(({id, title}) => { return {id, title} }) // finish this expression!
  // ------------ INSERT CODE HERE! -----------------------------------

}
```

</details>

### Filtering Arrays

Like projection, filtering an array is also a very common operation. To filter an array we apply a test to each item in the array and collect the items that pass into a new array.

#### Exercise 6: Use forEach() to collect only those videos with a rating of 5.0

<details>
<summary>
Use forEach() to loop through the videos in the newReleases array and, if a video has a rating of 5.0, add it to the videos array.
</summary>

```js
function() {
  var newReleases = [
    {
      "id": 70111470,
      "title": "Die Hard",
      "boxart": "http://cdn-0.nflximg.com/images/2891/DieHard.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": 4.0,
      "bookmark": []
    },
    {
      "id": 654356453,
      "title": "Bad Boys",
      "boxart": "http://cdn-0.nflximg.com/images/2891/BadBoys.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": 5.0,
      "bookmark": [{ id: 432534, time: 65876586 }]
    },
    {
      "id": 65432445,
      "title": "The Chamber",
      "boxart": "http://cdn-0.nflximg.com/images/2891/TheChamber.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": 4.0,
      "bookmark": []
    },
    {
      "id": 675465,
      "title": "Fracture",
      "boxart": "http://cdn-0.nflximg.com/images/2891/Fracture.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": 5.0,
      "bookmark": [{ id: 432534, time: 65876586 }]
    }
  ],
  videos = [];

  // ------------ INSERT CODE HERE! -----------------------------------
  // Use forEach function to accumulate every video with a rating of 5.0
  // ------------ INSERT CODE HERE! -----------------------------------
  newReleases.forEach((video) => {
    if(video.rating === 5.0)
    videos.push(video)
  })
  return videos;
}
```

</details>

Notice that, like map(), every filter() operation shares some operations in common:

1. Traverse the array
2. Add objects that pass the test to a new array
Why not abstract away how these operations are carried out?

#### Exercise 7: Implement filter()

To make filtering easier, let's add a filter() function to the Array type. The filter() function accepts a predicate. A predicate is a function that accepts an item in the array, and returns a boolean indicating whether the item should be retained in the new array.

```js
Array.prototype.filter = function(predicateFunction) {
  var results = [];
  this.forEach(function(itemInArray) {
    // ------------ INSERT CODE HERE! ----------------------------
    // Apply the predicateFunction to each item in the array.
    // If the result is truthy, add the item to the results array.
    // Note: remember you can add items to the array using the array's
    // push() method.
    // ------------ INSERT CODE HERE! ----------------------------
    if(predicateFunction(itemInArray)){
      results.push(itemInArray)
    }
  });

  return results;
};

// JSON.stringify([1,2,3].filter(function(x) { return x > 2})) === "[3]"
```

Like map(), filter() lets us express what data we want without requiring us to specify how we want to collect the data.

### Query Data by Chaining Method Calls

#### Exercise 8: Chain filter and map to collect the ids of videos that have a rating of 5.0

<details>
<summary>
Answer
</summary>

```js
function() {
  var newReleases = [
    {
      "id": 70111470,
      "title": "Die Hard",
      "boxart": "http://cdn-0.nflximg.com/images/2891/DieHard.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": 4.0,
      "bookmark": []
    },
    {
      "id": 654356453,
      "title": "Bad Boys",
      "boxart": "http://cdn-0.nflximg.com/images/2891/BadBoys.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": 5.0,
      "bookmark": [{ id: 432534, time: 65876586 }]
    },
    {
      "id": 65432445,
      "title": "The Chamber",
      "boxart": "http://cdn-0.nflximg.com/images/2891/TheChamber.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": 4.0,
      "bookmark": []
    },
    {
      "id": 675465,
      "title": "Fracture",
      "boxart": "http://cdn-0.nflximg.com/images/2891/Fracture.jpg",
      "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
      "rating": 5.0,
      "bookmark": [{ id: 432534, time: 65876586 }]
    }
  ];

  // ------------ INSERT CODE HERE! -----------------------------------
  // Chain the filter and map functions to select the id of all videos
  // with a rating of 5.0.

  return newReleases
    .filter(({rating}) => rating === 5.0)
    .map(({id}) => id) // Complete this expression
  // ------------ INSERT CODE HERE! -----------------------------------
}
```

</details>

Chaining together map() and filter() gives us a lot of expressive power. These high level functions let us express what data we want, but leave the underlying libraries a great deal of flexibility in terms of how our queries are executed.

### Querying Trees

Sometimes, in addition to flat arrays, we need to query trees. Trees pose a challenge because we need to flatten them into arrays in order to apply filter() and map() operations on them. In this section we'll define a concatAll() function that we can combine with map() and filter() to query trees.

#### Exercise 9: Flatten the movieLists array into an array of video ids

<details>

<summary>
Let's start by using two nested forEach loops to collect the id of every video in the two-dimensional movieLists array.
</summary>

```js
function() {
  var movieLists = [
    {
      name: "New Releases",
      videos: [
        {
          "id": 70111470,
          "title": "Die Hard",
          "boxart": "http://cdn-0.nflximg.com/images/2891/DieHard.jpg",
          "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
          "rating": 4.0,
          "bookmark": []
        },
        {
          "id": 654356453,
          "title": "Bad Boys",
          "boxart": "http://cdn-0.nflximg.com/images/2891/BadBoys.jpg",
          "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
          "rating": 5.0,
          "bookmark": [{ id: 432534, time: 65876586 }]
        }
      ]
    },
    {
      name: "Dramas",
      videos: [
        {
          "id": 65432445,
          "title": "The Chamber",
          "boxart": "http://cdn-0.nflximg.com/images/2891/TheChamber.jpg",
          "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
          "rating": 4.0,
          "bookmark": []
        },
        {
          "id": 675465,
          "title": "Fracture",
          "boxart": "http://cdn-0.nflximg.com/images/2891/Fracture.jpg",
          "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
          "rating": 5.0,
          "bookmark": [{ id: 432534, time: 65876586 }]
        }
      ]
    }
  ],
  allVideoIdsInMovieLists = [];

  // ------------   INSERT CODE HERE!  -----------------------------------
  // Use two nested forEach loops to flatten the movieLists into a list of
  // video ids.
  // ------------   INSERT CODE HERE!  -----------------------------------

  movieLists.forEach(movieList => {
    movieList.videos.forEach(video => {allVideoIdsInMovieLists.push(video.id)})
  })

  return allVideoIdsInMovieLists;

}
```

</details>

Flattening trees with nested forEach expressions is easy because we can explicitly add items to the array. Unfortunately it's exactly this type of low-level operation that we've been trying to abstract away with functions like map() and filter(). Can we define a function that's abstract enough to express our intent to flatten a tree, without specifying too much information about how to carry out the operation?

#### Exercise 10: Implement concatAll()

Let's add a concatAll() function to the Array type. The concatAll() function iterates over each sub-array in the array and collects the results in a new, flat array. Notice that the concatAll() function expects each item in the array to be another array.

```js
Array.prototype.concatAll = function() {
  var results = [];
  this.forEach(function(subArray) {
    // ------------ INSERT CODE HERE! ----------------------------
    // Add all the items in each subArray to the results array.
    // ------------ INSERT CODE HERE! ----------------------------
    subArray.forEach(subItems => results.push(subItems))
  });

  return results;
};

// JSON.stringify([ [1,2,3], [4,5,6], [7,8,9] ].concatAll()) === "[1,2,3,4,5,6,7,8,9]"
// [1,2,3].concatAll(); // throws an error because this is a one-dimensional array
```

concatAll is a very simple function, so much so that it may not be obvious yet how it can be combined with map() to query a tree. Let's try an example...

#### Exercise 11: Use map() and concatAll() to project and flatten the movieLists into an array of video ids

<details>

<summary>
Answer
Hint: use two nested calls to map() and one call to concatAll().
</summary>

```js
function() {
  var movieLists = [
      {
        name: "New Releases",
        videos: [
          {
            "id": 70111470,
            "title": "Die Hard",
            "boxart": "http://cdn-0.nflximg.com/images/2891/DieHard.jpg",
            "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 4.0,
            "bookmark": []
          },
          {
            "id": 654356453,
            "title": "Bad Boys",
            "boxart": "http://cdn-0.nflximg.com/images/2891/BadBoys.jpg",
            "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 5.0,
            "bookmark": [{ id: 432534, time: 65876586 }]
          }
        ]
      },
      {
        name: "Dramas",
        videos: [
          {
            "id": 65432445,
            "title": "The Chamber",
            "boxart": "http://cdn-0.nflximg.com/images/2891/TheChamber.jpg",
            "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 4.0,
            "bookmark": []
          },
          {
            "id": 675465,
            "title": "Fracture",
            "boxart": "http://cdn-0.nflximg.com/images/2891/Fracture.jpg",
            "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 5.0,
            "bookmark": [{ id: 432534, time: 65876586 }]
          }
        ]
      }
    ];

  // ------------   INSERT CODE HERE!  -----------------------------------
  // Use map and concatAll to flatten the movieLists in a list of video ids.
  // ------------   INSERT CODE HERE!  -----------------------------------

  return movieLists
    .map(movieList =>
      movieList.videos.map(({id}) => id)
    )
    .concatAll() // Complete this expression!

}
```

</details>

Wow! Great work. Mastering the combination of map() and concatAll() is key to effective functional programming. You're half way there! Let's try a more complicated example...

#### Exercise 12: Retrieve id, title, and a 150x200 box art url for every video

<details>
<summary>
You've managed to flatten a tree that's two levels deep, let's try for three! Let's say that instead of a single boxart url on each video, we had a collection of boxart objects, each with a different size and url. Create a query that selects {id, title, boxart} for every video in the movieLists. This time though, the boxart property in the result will be the url of the boxart object with dimensions of 150x200px. Let's see if you can solve this problem with map(), concatAll(), and filter().

**There's just more one thing: you can't use indexers**. In other words, this is *illegal*:

```js
var itemInArray = movieLists[0];
```

Furthermore, **you're not allowed to use indexers in any of the remaining exercises** unless you're implementing one of the five functions. There is a very good reason for this restriction, and that reason will eventually be explained. For now, you'll simply have to accept it on faith that this restriction serves a purpose. :-)
</summary>

```js
function() {
  var movieLists = [
      {
        name: "Instant Queue",
        videos : [
          {
            "id": 70111470,
            "title": "Die Hard",
            "boxarts": [
              { width: 150, height: 200, url: "http://cdn-0.nflximg.com/images/2891/DieHard150.jpg" },
              { width: 200, height: 200, url: "http://cdn-0.nflximg.com/images/2891/DieHard200.jpg" }
            ],
            "url": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 4.0,
            "bookmark": []
          },
          {
            "id": 654356453,
            "title": "Bad Boys",
            "boxarts": [
              { width: 200, height: 200, url: "http://cdn-0.nflximg.com/images/2891/BadBoys200.jpg" },
              { width: 150, height: 200, url: "http://cdn-0.nflximg.com/images/2891/BadBoys150.jpg" }

            ],
            "url": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 5.0,
            "bookmark": [{ id: 432534, time: 65876586 }]
          }
        ]
      },
      {
        name: "New Releases",
        videos: [
          {
            "id": 65432445,
            "title": "The Chamber",
            "boxarts": [
              { width: 150, height: 200, url: "http://cdn-0.nflximg.com/images/2891/TheChamber150.jpg" },
              { width: 200, height: 200, url: "http://cdn-0.nflximg.com/images/2891/TheChamber200.jpg" }
            ],
            "url": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 4.0,
            "bookmark": []
          },
          {
            "id": 675465,
            "title": "Fracture",
            "boxarts": [
              { width: 200, height: 200, url: "http://cdn-0.nflximg.com/images/2891/Fracture200.jpg" },
              { width: 150, height: 200, url: "http://cdn-0.nflximg.com/images/2891/Fracture150.jpg" },
              { width: 300, height: 200, url: "http://cdn-0.nflximg.com/images/2891/Fracture300.jpg" }
            ],
            "url": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 5.0,
            "bookmark": [{ id: 432534, time: 65876586 }]
          }
        ]
      }
    ];


  // Use one or more map, concatAll, and filter calls to create an array with the following items
  // [
  //   {"id": 675465,"title": "Fracture","boxart":"http://cdn-0.nflximg.com/images/2891/Fracture150.jpg" },
  //   {"id": 65432445,"title": "The Chamber","boxart":"http://cdn-0.nflximg.com/images/2891/TheChamber150.jpg" },
  //   {"id": 654356453,"title": "Bad Boys","boxart":"http://cdn-0.nflximg.com/images/2891/BadBoys150.jpg" },
  //   {"id": 70111470,"title": "Die Hard","boxart":"http://cdn-0.nflximg.com/images/2891/DieHard150.jpg" }
  // ];

  // return movieLists
  //   .map(movieList =>
  //     movieList.videos
  //       .map(({id, title, boxarts}) => (
  //         {
  //           id,
  //           title,
  //           boxart: boxarts
  //                     .filter(boxart => boxart.width === 150 && boxart.height === 200)
  //                     .map(item => item.url) + ''
  //         }
  //       ))
  //   )
  //   .concatAll()// Complete this expression!

  return movieLists
    .map(movieList =>
      movieList.videos.map(video =>
        video.boxarts
          .filter(boxart => boxart.width ===150 && boxart.height === 200)
          .map(boxart =>
            ({
              id: video.id,
              title: video.title,
              boxart: boxart.url
            })
          )
      ).concatAll()
    )
    .concatAll()
    // 首先获取最深层的数据，然后将在作用域内获取上层数据并保存在新数组中，最后压平数组
}
```

</details>

Fantastic job! Now you've learned to use concatAll() alongside map() and filter() to query trees. Notice that map() and concatAll() are very commonly chained together. Let's create a small helper function to help us with this common pattern.

#### Exercise 13: Implement concatMap()

Nearly every time we flatten a tree we chain map() and concatAll(). Sometimes, if we're dealing with a tree several levels deep, we'll repeat this combination many times in our code. To save on typing, let's create a concatMap function that's just a map operation, followed by a concatAll.

```js
Array.prototype.concatMap = function(projectionFunctionThatReturnsArray) {
  return this.
    map(function(item) {
      // ------------   INSERT CODE HERE!  ----------------------------
      // Apply the projection function to each item. The projection
      // function will return a new child array. This will create a
      // two-dimensional array.
      // ------------   INSERT CODE HERE!  ----------------------------
      return projectionFunctionThatReturnsArray(item)
    }).
    // apply the concatAll function to flatten the two-dimensional array
    concatAll();
};

/*
  var spanishFrenchEnglishWords = [ ["cero","rien","zero"], ["uno","un","one"], ["dos","deux","two"] ];
  // collect all the words for each number, in every language, in a single, flat list
  var allWords = [0,1,2].
    concatMap(function(index) {
      return spanishFrenchEnglishWords[index];
    });

  return JSON.stringify(allWords) === '["cero","rien","zero","uno","un","one","dos","deux","two"]';
*/
```

Now, instead of using map().concatAll() to flatten a tree, we can just use concatMap helper function.

#### Exercise 14: Use concatMap() to retrieve id, title, and 150x200 box art url for every video

<details>
<summary>
Let's repeat the exercise we just performed. However this time we'll simplify the code by replacing the map().concatAll() calls with concatMap().
</summary>

```js
function() {
  var movieLists = [
      {
        name: "Instant Queue",
        videos : [
          {
            "id": 70111470,
            "title": "Die Hard",
            "boxarts": [
              { width: 150, height: 200, url: "http://cdn-0.nflximg.com/images/2891/DieHard150.jpg" },
              { width: 200, height: 200, url: "http://cdn-0.nflximg.com/images/2891/DieHard200.jpg" }
            ],
            "url": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 4.0,
            "bookmark": []
          },
          {
            "id": 654356453,
            "title": "Bad Boys",
            "boxarts": [
              { width: 200, height: 200, url: "http://cdn-0.nflximg.com/images/2891/BadBoys200.jpg" },
              { width: 150, height: 200, url: "http://cdn-0.nflximg.com/images/2891/BadBoys150.jpg" }

            ],
            "url": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 5.0,
            "bookmark": [{ id: 432534, time: 65876586 }]
          }
        ]
      },
      {
        name: "New Releases",
        videos: [
          {
            "id": 65432445,
            "title": "The Chamber",
            "boxarts": [
              { width: 150, height: 200, url: "http://cdn-0.nflximg.com/images/2891/TheChamber150.jpg" },
              { width: 200, height: 200, url: "http://cdn-0.nflximg.com/images/2891/TheChamber200.jpg" }
            ],
            "url": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 4.0,
            "bookmark": []
          },
          {
            "id": 675465,
            "title": "Fracture",
            "boxarts": [
              { width: 200, height: 200, url: "http://cdn-0.nflximg.com/images/2891/Fracture200.jpg" },
              { width: 150, height: 200, url: "http://cdn-0.nflximg.com/images/2891/Fracture150.jpg" },
              { width: 300, height: 200, url: "http://cdn-0.nflximg.com/images/2891/Fracture300.jpg" }
            ],
            "url": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 5.0,
            "bookmark": [{ id: 432534, time: 65876586 }]
          }
        ]
      }
    ];


  // Use one or more concatMap, map, and filter calls to create an array with the following items
  // [
  //   {"id": 675465, "title": "Fracture", "boxart": "http://cdn-0.nflximg.com/images/2891/Fracture150.jpg" },
  //   {"id": 65432445, "title": "The Chamber", "boxart": "http://cdn-0.nflximg.com/images/2891/TheChamber150.jpg" },
  //   {"id": 654356453, "title": "Bad Boys", "boxart": "http://cdn-0.nflximg.com/images/2891/BadBoys150.jpg" },
  //   {"id": 70111470, "title": "Die Hard", "boxart": "http://cdn-0.nflximg.com/images/2891/DieHard150.jpg" }
  // ];

  return movieLists
    .concatMap(movieList =>
      movieList.videos.concatMap(video =>
        video.boxarts
          .filter(boxart => boxart.width === 150 && boxart.height === 200)
          .map(boxart => (
            {
              id: video.id,
              title: video.title,
              boxart: boxart.url
            }
          ))
      )
    ) // Complete this expression!

}
```

</details>

It's a very common pattern to see several nested concatMap operations, with the last operation being a map. You can think of this pattern as the functional version of a nested forEach.

### Reducing Arrays

Sometimes we need to perform an operation on more than one item in the array at the same time. For example, let's say we need to find the largest integer in an array. We can't use a filter() operation, because it only examines one item at a time. To find the largest integer we need to compare items in the array to each other.

One approach could be to select an item in the array as the assumed largest number (perhaps the first item), and then compare that value to every other item in the array. Each time we come across a number that was larger than our assumed largest number, we'd replace it with the larger value, and continue the process until the entire array was traversed.

If we replaced the specific size comparison with a closure, we could write a function that handled the array traversal process for us. At each step our function would apply the closure to the last value and the current value and use the result as the last value the next time. Finally we'd be left with only one value. This process is known as reducing because we reduce many values to a single value.

#### Exercise 15: Use forEach to find the largest box art

<details>
<summary>
In this example we use forEach to find the largest box art. Each time we examine a new boxart we update a variable with the currently known maximumSize. If the boxart is smaller than the maximum size, we discard it. If it's larger, we keep track of it. Finally we're left with a single boxart which must necessarily be the largest.
</summary>

```js
function() {
  var boxarts = [
      { width: 200, height: 200, url: "http://cdn-0.nflximg.com/images/2891/Fracture200.jpg" },
      { width: 150, height: 200, url: "http://cdn-0.nflximg.com/images/2891/Fracture150.jpg" },
      { width: 300, height: 200, url: "http://cdn-0.nflximg.com/images/2891/Fracture300.jpg" },
      { width: 425, height: 150, url: "http://cdn-0.nflximg.com/images/2891/Fracture425.jpg" }
    ],
    currentSize,
    maxSize = -1,
    largestBoxart;

  boxarts.forEach(function(boxart) {
    currentSize = boxart.width * boxart.height;
    if (currentSize > maxSize) {
      largestBoxart = boxart;
      maxSize = currentSize;
    }
  });

  return largestBoxart;
}
```

</details>

This process is a reduction because we're using the information we derived from the last computation to calculate the current value. However in the example above, we still have to specify the method of traversal. Wouldn't it be nice if we could just specify what operation we wanted to perform on the last and current value? Let's create a helper function to perform reductions on arrays.

#### Exercise 16: Implement reduce()

<details>
<summary>
Let's add a reduce() function to the Array type. Like map. Take note this is different from the reduce in ES5, which returns a value instead of an Array!
</summary>

```js
// [1,2,3].reduce(function(accumulatedValue, currentValue) { return accumulatedValue + currentValue; }); === [6];
// [1,2,3].reduce(function(accumulatedValue, currentValue) { return accumulatedValue + currentValue; }, 10); === [16];

Array.prototype.reduce = function(combiner, initialValue) {
  var counter,
    accumulatedValue;

  // If the array is empty, do nothing
  if (this.length === 0) {
    return this;
  }
  else {
    // If the user didn't pass an initial value, use the first item.
    if (arguments.length === 1) {
      counter = 1;
      accumulatedValue = this[0];
    }
    else if (arguments.length >= 2) {
      counter = 0;
      accumulatedValue = initialValue;
    }
    else {
      throw "Invalid arguments.";
    }

    // Loop through the array, feeding the current value and the result of
    // the previous computation back into the combiner function until
    // we've exhausted the entire array and are left with only one value.
    while(counter < this.length) {
      accumulatedValue = combiner(accumulatedValue, this[counter])
      counter++;
    }

    return [accumulatedValue];
  }
};
```

</details>

#### Exercise 17: Retrieve the largest rating

<details>
<summary>
Let's use our new reduce function to isolate the largest value in an array of ratings.
</summary>

```js
function() {
  var ratings = [2,3,1,4,5];

  // You should return an array containing only the largest rating. Remember that reduce always
  // returns an array with one item.
  return ratings.
    reduce((acc, curr) => acc > curr ? acc : curr, ratings[0])   // Complete this expression
}
```

</details>

Nice work. Now let's try combining reduce() with our other functions to build more complex queries.

#### Exercise 18: Retrieve url of the largest boxart

<details>
<summary>
Let's try combining reduce() with map() to reduce multiple boxart objects to a single value: the url of the largest box art.
</summary>

```js
function() {
  var boxarts = [
      { width: 200, height: 200, url: "http://cdn-0.nflximg.com/images/2891/Fracture200.jpg" },
      { width: 150, height: 200, url: "http://cdn-0.nflximg.com/images/2891/Fracture150.jpg" },
      { width: 300, height: 200, url: "http://cdn-0.nflximg.com/images/2891/Fracture300.jpg" },
      { width: 425, height: 150, url: "http://cdn-0.nflximg.com/images/2891/Fracture425.jpg" }
    ];

  // You should return an array containing only the URL of the largest box art. Remember that reduce always
  // returns an array with one item.
  return boxarts
    .reduce((acc, curr) => acc.width * acc.height > curr.width * curr.height ? acc : curr)
    .map(({url}) => url)   // Complete this expression
}
```

</details>

#### Exercise 19: Reducing with an initial value

<details>
<summary>
Sometimes when we reduce an array, we want the reduced value to be a different type than the items stored in the array. Let's say we have an array of videos and we want to reduce them to a single map where the key is the video id and the value is the video's title.
</summary>

```js
function() {
  var videos = [
    {
      "id": 65432445,
      "title": "The Chamber"
    },
    {
      "id": 675465,
      "title": "Fracture"
    },
    {
      "id": 70111470,
      "title": "Die Hard"
    },
    {
      "id": 654356453,
      "title": "Bad Boys"
    }
  ];

  // Expecting this output...
  // [
  //   {
  //     "65432445": "The Chamber",
  //     "675465": "Fracture",
  //     "70111470": "Die Hard",
  //     "654356453": "Bad Boys"
  //   }
  // ]
  return videos.
    reduce(function(accumulatedMap, video) {
    var obj = {};

    // ----- INSERT CODE TO ADD THE VIDEO TITLE TO THE ----
    // ----- NEW MAP USING THE VIDEO ID AS THE KEY   ----

    obj[video.id] = video.title

    // Object.assign() takes all of the enumerable properties from
    // the object listed in its second argument (obj) and assigns them
    // to the object listed in its first argument (accumulatedMap).
    return Object.assign(accumulatedMap, obj);
    },
    // Use an empty map as the initial value instead of the first item in
    // the list.
    {});
}
```

</details>

Nice work. Now let's try combining reduce() with our other functions to build more complex queries.

#### Exercise 20: Retrieve the id, title, and smallest box art url for every video

<details>
<summary>
This is a variation of the problem we solved earlier, where we retrieved the url of the boxart with a width of 150px. This time we'll use reduce() instead of filter() to retrieve the smallest box art in the boxarts array.
</summary>

```js
function() {
  var movieLists = [
    {
      name: "New Releases",
      videos: [
        {
          "id": 70111470,
          "title": "Die Hard",
          "boxarts": [
            { width: 150, height:200, url:"http://cdn-0.nflximg.com/images/2891/DieHard150.jpg" },
            { width: 200, height:200, url:"http://cdn-0.nflximg.com/images/2891/DieHard200.jpg" }
          ],
          "url": "http://api.netflix.com/catalog/titles/movies/70111470",
          "rating": 4.0,
          "bookmark": []
        },
        {
          "id": 654356453,
          "title": "Bad Boys",
          "boxarts": [
            { width: 200, height:200, url:"http://cdn-0.nflximg.com/images/2891/BadBoys200.jpg" },
            { width: 140, height:200, url:"http://cdn-0.nflximg.com/images/2891/BadBoys140.jpg" }

          ],
          "url": "http://api.netflix.com/catalog/titles/movies/70111470",
          "rating": 5.0,
          "bookmark": [{ id:432534, time:65876586 }]
        }
      ]
    },
    {
      name: "Thrillers",
      videos: [
        {
          "id": 65432445,
          "title": "The Chamber",
          "boxarts": [
            { width: 130, height:200, url:"http://cdn-0.nflximg.com/images/2891/TheChamber130.jpg" },
            { width: 200, height:200, url:"http://cdn-0.nflximg.com/images/2891/TheChamber200.jpg" }
          ],
          "url": "http://api.netflix.com/catalog/titles/movies/70111470",
          "rating": 4.0,
          "bookmark": []
        },
        {
          "id": 675465,
          "title": "Fracture",
          "boxarts": [
            { width: 200, height:200, url:"http://cdn-0.nflximg.com/images/2891/Fracture200.jpg" },
            { width: 120, height:200, url:"http://cdn-0.nflximg.com/images/2891/Fracture120.jpg" },
            { width: 300, height:200, url:"http://cdn-0.nflximg.com/images/2891/Fracture300.jpg" }
          ],
          "url": "http://api.netflix.com/catalog/titles/movies/70111470",
          "rating": 5.0,
          "bookmark": [{ id:432534, time:65876586 }]
        }
      ]
    }
  ];


  // Use one or more concatMap, map, and reduce calls to create an array with the following items (order matters)
  // [
  //   {"id": 675465,"title": "Fracture","boxart":"http://cdn-0.nflximg.com/images/2891/Fracture120.jpg" },
  //   {"id": 65432445,"title": "The Chamber","boxart":"http://cdn-0.nflximg.com/images/2891/TheChamber130.jpg" },
  //   {"id": 654356453,"title": "Bad Boys","boxart":"http://cdn-0.nflximg.com/images/2891/BadBoys140.jpg" },
  //   {"id": 70111470,"title": "Die Hard","boxart":"http://cdn-0.nflximg.com/images/2891/DieHard150.jpg" }
  // ];

  return movieLists.
    concatMap(movieList =>
      movieList.videos.concatMap(video =>
        video.boxarts
          .reduce((acc, curr) =>
            acc.width * acc.height < curr.width * curr.height ? acc : curr
          )
          .map(boxart => (
            {
              id: video.id,
              title: video.title,
              boxart: boxart.url
            }
        ))
      )
    )
    .reduce((acc, curr) =>{
      if()
    }, [])

}

```

</details>

### Zipping Arrays

Sometimes we need to combine two arrays by progressively taking an item from each and combining the pair. If you visualize a zipper, where each side is an array, and each tooth is an item, you'll have a good idea of how the zip operation works.

#### Exercise 21: Combine videos and bookmarks by index

<details>
<summary>
Use a for loop to traverse the videos and bookmarks array at the same time. For each video and bookmark pair, create a {videoId, bookmarkId} pair and add it to the videoIdAndBookmarkIdPairs array.
</summary>

```js
function() {
  var videos = [
      {
        "id": 70111470,
        "title": "Die Hard",
        "boxart": "http://cdn-0.nflximg.com/images/2891/DieHard.jpg",
        "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
        "rating": 4.0,
      },
      {
        "id": 654356453,
        "title": "Bad Boys",
        "boxart": "http://cdn-0.nflximg.com/images/2891/BadBoys.jpg",
        "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
        "rating": 5.0,
      },
      {
        "id": 65432445,
        "title": "The Chamber",
        "boxart": "http://cdn-0.nflximg.com/images/2891/TheChamber.jpg",
        "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
        "rating": 4.0,
      },
      {
        "id": 675465,
        "title": "Fracture",
        "boxart": "http://cdn-0.nflximg.com/images/2891/Fracture.jpg",
        "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
        "rating": 5.0,
      }
    ],
    bookmarks = [
      {id: 470, time: 23432},
      {id: 453, time: 234324},
      {id: 445, time: 987834}
    ],
  counter,
  videoIdAndBookmarkIdPairs = [];

  for(counter = 0; counter < Math.min(videos.length, bookmarks.length); counter++) {
    // Insert code here to create a {videoId, bookmarkId} pair and add it to the videoIdAndBookmarkIdPairs array.
    videoIdAndBookmarkIdPairs.push(
        {
          videoId: videos[counter].id,
          bookmarkId: bookmarks[counter].id
        }
      )
  }

  return videoIdAndBookmarkIdPairs;
}
```

</details>

#### Exercise 22: Implement zip

<details>
<summary>
Let's add a static zip() function to the Array type. The zip function accepts a combiner function, traverses each array at the same time, and calls the combiner function on the current item on the left-hand-side and right-hand-side. The zip function requires an item from each array in order to call the combiner function, therefore the array returned by zip will only be as large as the smallest input array.
</summary>

```js
// JSON.stringify(Array.zip([1,2,3],[4,5,6], function(left, right) { return left + right })) === '[5,7,9]'

Array.zip = function(left, right, combinerFunction) {
  var counter,
    results = [];

  for(counter = 0; counter < Math.min(left.length, right.length); counter++) {
    // Add code here to apply the combinerFunction to the left and right-hand items in the respective arrays
    results.push(
      combinerFunction(left[counter], right[counter])
    )
  }

  return results;
};
```

</details>

#### Exercise 23: Combine videos and bookmarks by index

<details>
<summary>
Let's repeat exercise 21, but this time lets use your new zip() function. For each video and bookmark pair, create a {videoId, bookmarkId} pair.
</summary>

```js
function() {
  var videos = [
      {
        "id": 70111470,
        "title": "Die Hard",
        "boxart": "http://cdn-0.nflximg.com/images/2891/DieHard.jpg",
        "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
        "rating": 4.0,
      },
      {
        "id": 654356453,
        "title": "Bad Boys",
        "boxart": "http://cdn-0.nflximg.com/images/2891/BadBoys.jpg",
        "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
        "rating": 5.0,
      },
      {
        "id": 65432445,
        "title": "The Chamber",
        "boxart": "http://cdn-0.nflximg.com/images/2891/TheChamber.jpg",
        "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
        "rating": 4.0,
      },
      {
        "id": 675465,
        "title": "Fracture",
        "boxart": "http://cdn-0.nflximg.com/images/2891/Fracture.jpg",
        "uri": "http://api.netflix.com/catalog/titles/movies/70111470",
        "rating": 5.0,
      }
    ],
    bookmarks = [
      {id: 470, time: 23432},
      {id: 453, time: 234324},
      {id: 445, time: 987834}
    ];

  return Array.
    zip(videos, bookmarks, (video, bookmark) => ({
        videoId: video.id,
        bookmarkId: bookmark.id
      })
    ) //... finish this expression
}

```

</details>

#### Exercise 24: Retrieve each video's id, title, middle interesting moment time, and smallest box art url

<details>
<summary>
This is a variation of the problem we solved earlier. This time each video has an interesting moments collection, each representing a time during which a screenshot is interesting or representative of the title as a whole. Notice that both the boxarts and interestingMoments arrays are located at the same depth in the tree. Retrieve the time of the middle interesting moment and the smallest box art url simultaneously with zip(). Return an {id, title, time, url} object for each video.
</summary>

```js
function() {
  var movieLists = [
      {
        name: "New Releases",
        videos: [
          {
            "id": 70111470,
            "title": "Die Hard",
            "boxarts": [
              { width: 150, height:200, url:"http://cdn-0.nflximg.com/images/2891/DieHard150.jpg" },
              { width: 200, height:200, url:"http://cdn-0.nflximg.com/images/2891/DieHard200.jpg" }
            ],
            "url": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 4.0,
            "interestingMoments": [
              { type: "End", time:213432 },
              { type: "Start", time: 64534 },
              { type: "Middle", time: 323133}
            ]
          },
          {
            "id": 654356453,
            "title": "Bad Boys",
            "boxarts": [
              { width: 200, height:200, url:"http://cdn-0.nflximg.com/images/2891/BadBoys200.jpg" },
              { width: 140, height:200, url:"http://cdn-0.nflximg.com/images/2891/BadBoys140.jpg" }

            ],
            "url": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 5.0,
            "interestingMoments": [
              { type: "End", time:54654754 },
              { type: "Start", time: 43524243 },
              { type: "Middle", time: 6575665}
            ]
          }
        ]
      },
      {
        name: "Instant Queue",
        videos: [
          {
            "id": 65432445,
            "title": "The Chamber",
            "boxarts": [
              { width: 130, height:200, url:"http://cdn-0.nflximg.com/images/2891/TheChamber130.jpg" },
              { width: 200, height:200, url:"http://cdn-0.nflximg.com/images/2891/TheChamber200.jpg" }
            ],
            "url": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 4.0,
            "interestingMoments": [
              { type: "End", time:132423 },
              { type: "Start", time: 54637425 },
              { type: "Middle", time: 3452343}
            ]
          },
          {
            "id": 675465,
            "title": "Fracture",
            "boxarts": [
              { width: 200, height:200, url:"http://cdn-0.nflximg.com/images/2891/Fracture200.jpg" },
              { width: 120, height:200, url:"http://cdn-0.nflximg.com/images/2891/Fracture120.jpg" },
              { width: 300, height:200, url:"http://cdn-0.nflximg.com/images/2891/Fracture300.jpg" }
            ],
            "url": "http://api.netflix.com/catalog/titles/movies/70111470",
            "rating": 5.0,
            "interestingMoments": [
              { type: "End", time:45632456 },
              { type: "Start", time: 234534 },
              { type: "Middle", time: 3453434}
            ]
          }
        ]
      }
    ];

  //------------ COMPLETE THIS EXPRESSION --------------
  return movieLists.
    concatMap(movieList =>
      movieList.videos.concatMap(video =>
        Array.zip(
          video.boxarts
            .reduce((acc, curr) => acc.width * acc.height < curr.width * curr.height ? acc : curr),
          video.interestingMoments
            .filter(item => item.type === 'Middle'),
          (boxart, interestingMoment) => (
            {
              id: video.id,
              title: video.title,
              time: interestingMoment.time,
              url: boxart.url
            }
          )
        )
      )
    );

}

```

</details>

## Creating Trees

### Debugging Async

使用开发者工具的调试器设置断点并执行来观察结果。

### SQL Comparison

使用上面的几种方法，可以实现与SQL语句一样的查询效果

Powerful Queries

Now that we've learned the five operators let's flex our muscles and write some powerful queries.

#### Exercise 25: Converting from Arrays to Trees

<details>
<summary>
When information is organized in a tree like a JSON expression, relationships point from parent to child. In relational systems like databases, relationships point from children to their parents. Both ways of organizing information are equivalent, and depending on the circumstances, we might get data organized in one way or another. It may surprise you to learn that you can use the 5 query functions you already know to easily convert between these representations. In other words, not only can you query arrays from trees, you can query trees from arrays.

We have 2 arrays each containing lists, and videos respectively. Each video has a listId field indicating its parent list. We want to build an array of list objects, each with a name and a videos array. The videos array will contain the video's id and title. In other words we want to build the following structure:
</summary>

```json
[
  {
    "name": "New Releases",
    "videos": [
      {
        "id": 65432445,
        "title": "The Chamber"
      },
      {
        "id": 675465,
        "title": "Fracture"
      }
    ]
  },
  {
    "name": "Thrillers",
    "videos": [
      {
        "id": 70111470,
        "title": "Die Hard"
      },
      {
        "id": 654356453,
        "title": "Bad Boys"
      }
    ]
  }
]
```

</details>
<details>
<summary>
Note: please make sure when creating objects (both lists and videos) that you add properties in the same order as above. This doesn't impact the correctness of your code, but the verifier expects properties to be created in this order.
</summary>

```js
function() {
  var lists = [
      {
        "id": 5434364,
        "name": "New Releases"
      },
      {
        "id": 65456475,
        "name": "Thrillers"
      }
    ],
    videos = [
      {
        "listId": 5434364,
        "id": 65432445,
        "title": "The Chamber"
      },
      {
        "listId": 5434364,
        "id": 675465,
        "title": "Fracture"
      },
      {
        "listId": 65456475,
        "id": 70111470,
        "title": "Die Hard"
      },
      {
        "listId": 65456475,
        "id": 654356453,
        "title": "Bad Boys"
      }
    ];

  return lists.map(list =>({
    name: list.name,
    videos: videos
              .filter(video => video.listId === list.id)
              .map(video => ({id: video.id, title: video.title}))
  })) // complete this expression

}

```

</details>

Looks like you figured out that you can use map and filter to join two different arrays by a key. Now let's try a more complex example...

#### Exercise 26: Converting from Arrays to Deeper Trees

<details>
<summary>
Let's try creating a deeper tree structure. This time we have 4 separate arrays each containing lists, videos, boxarts, and bookmarks respectively. Each object has a parent id, indicating its parent. We want to build an array of list objects, each with a name and a videos array. The videos array will contain the video's id, title, bookmark time, and smallest boxart url. In other words we want to build the following structure:
</summary>

```json
[
  {
    "name": "New Releases",
    "videos": [
      {
        "id": 65432445,
        "title": "The Chamber",
        "time": 32432,
        "boxart": "http://cdn-0.nflximg.com/images/2891/TheChamber130.jpg"
      },
      {
        "id": 675465,
        "title": "Fracture",
        "time": 3534543,
        "boxart": "http://cdn-0.nflximg.com/images/2891/Fracture120.jpg"
      }
    ]
  },
  {
    "name": "Thrillers",
    "videos": [
      {
        "id": 70111470,
        "title": "Die Hard",
        "time": 645243,
        "boxart": "http://cdn-0.nflximg.com/images/2891/DieHard150.jpg"
      },
      {
        "id": 654356453,
        "title": "Bad Boys",
        "time": 984934,
        "boxart": "http://cdn-0.nflximg.com/images/2891/BadBoys140.jpg"
      }
    ]
  }
]
```

</details>

<details>
<summary>
Note: please make sure when creating objects (both lists and videos) that you add properties in the same order as above. This doesn't impact the correctness of your code, but the verifier expects properties to be created in this order.
</summary>

```js
function() {
  var lists = [
      {
        "id": 5434364,
        "name": "New Releases"
      },
      {
        "id": 65456475,
        name: "Thrillers"
      }
    ],
    videos = [
      {
        "listId": 5434364,
        "id": 65432445,
        "title": "The Chamber"
      },
      {
        "listId": 5434364,
        "id": 675465,
        "title": "Fracture"
      },
      {
        "listId": 65456475,
        "id": 70111470,
        "title": "Die Hard"
      },
      {
        "listId": 65456475,
        "id": 654356453,
        "title": "Bad Boys"
      }
    ],
    boxarts = [
      { videoId: 65432445, width: 130, height:200, url:"http://cdn-0.nflximg.com/images/2891/TheChamber130.jpg" },
      { videoId: 65432445, width: 200, height:200, url:"http://cdn-0.nflximg.com/images/2891/TheChamber200.jpg" },
      { videoId: 675465, width: 200, height:200, url:"http://cdn-0.nflximg.com/images/2891/Fracture200.jpg" },
      { videoId: 675465, width: 120, height:200, url:"http://cdn-0.nflximg.com/images/2891/Fracture120.jpg" },
      { videoId: 675465, width: 300, height:200, url:"http://cdn-0.nflximg.com/images/2891/Fracture300.jpg" },
      { videoId: 70111470, width: 150, height:200, url:"http://cdn-0.nflximg.com/images/2891/DieHard150.jpg" },
      { videoId: 70111470, width: 200, height:200, url:"http://cdn-0.nflximg.com/images/2891/DieHard200.jpg" },
      { videoId: 654356453, width: 200, height:200, url:"http://cdn-0.nflximg.com/images/2891/BadBoys200.jpg" },
      { videoId: 654356453, width: 140, height:200, url:"http://cdn-0.nflximg.com/images/2891/BadBoys140.jpg" }
    ],
    bookmarks = [
      { videoId: 65432445, time: 32432 },
      { videoId: 675465, time: 3534543 },
      { videoId: 70111470, time: 645243 },
      { videoId: 654356453, time: 984934 }
    ];

  return lists.map(list =>({
    name: list.name,
    videos: videos
              .filter(video => video.listId === list.id)
              .concatMap(video =>
                Array.zip(
                  bookmarks.filter(bookmark => bookmark.videoId === video.id),
                  boxarts
                    .filter(boxart => boxart.videoId === video.id)
                    .reduce((acc, curr) => acc.width * acc.height < curr.width * curr.height ? acc : curr),
                    (bookmark, boxart) => ({
                      id: video.id,
                      title: video.title,
                      time: bookmark.time,
                      boxart: boxart.url
                    })
                )
              )
  })) // complete this expression

}
```

</details>

Wow! That's a large query, but the code is still small relative to the amount of work it's doing. If we rewrote this query with a series of loops our code would be much less self-describing. Loops don't give the reader any information about the kind of operation being performed. Every time you see a loop, you need to carefully read through the code to find out what's being done. Is it a projection? A filter? A reduction? Why use loops for querying data when we've demonstrated that the 5 functions can be used to create virtually any output we want?

#### Exercise 27: Stock Ticker

<details>
<summary>
Let's try an easier question. Let's say we have a collection of all of the prices for NASDAQ stocks over time. Every time the price of a stock changes on the NASDAQ ticker an entry is added to this collection. Let's say that ten days ago you bought shares in Microsoft, and now you want to print all of the MSFT share prices since then. Filter the collection for MSFT trades starting from ten days ago and print each price record (including the time stamp) using the print() function. Note: this is not a trick question. It's as easy as it seems.

```js
// The pricesNASDAQ collection looks something like this...
var pricesNASDAQ = [
  // ... from the NASDAQ's opening day
  {name: "ANGI", price: 31.22, timeStamp: new Date(2011,11,15) },
  {name: "MSFT", price: 32.32, timeStamp: new Date(2011,11,15) },
  {name: "GOOG", price: 150.43, timeStamp: new Date(2011,11,15)},
  {name: "ANGI", price: 28.44, timeStamp: new Date(2011,11,16)},
  {name: "GOOG", price: 199.33, timeStamp: new Date(2011,11,16)},
  // ...and up to the present.
];
```

</summary>

```js
function(pricesNASDAQ, printRecord) {
  var microsoftPrices,
    now = new Date(),
    tenDaysAgo = new Date( now.getFullYear(), now.getMonth(), now.getDate() - 10);

  // use filter() to filter the trades for MSFT prices recorded any time after 10 days ago
  microsoftPrices =
    pricesNASDAQ.
      filter(function(priceRecord) {
        return priceRecord.timeStamp > tenDaysAgo && priceRecord.name === "MSFT"
      }// finish this expression

  // Print the trades to the output console
  microsoftPrices.
    forEach(function(priceRecord) {
      printRecord(priceRecord);
    });
}
```

</details>

Notice that the console is changing over time. Now look at the time stamps on the stock prices. We're displaying stock prices sampled after we ran our program! How could our array have contained stock price records from the future? Did we accidentally rip a hole in the space-time continuum?

The solution to the riddle is that pricesNASDAQ is not an array. Unlike an array, which can only store a snapshot of stock prices, this new type can react to changes and update over time.

In the next section I'll reveal the inner workings of this magical type. You'll learn how you can use it to model everything from mouse events to asynchronous JSON requests. Finally I'll show you how you can query this type using the 5 query functions you already know. It's about time we gave this magical type a name...

## Handling Events with Ovservables

### Working with Observables

Microsoft's open-source Reactive Extensions library introduces a new collection type to Javascript: Observable. An Observable is a lot like an Event. Like an Event, an Observable is a sequence of values that a data producer pushes to the consumer. However unlike an Event, an Observable can signal to a listener that it has completed, and will send no more data.

微软的开源反应扩展库为Javascript引入了一种新的集合类型:Observable。可观察对象很像事件。与事件类似，可观察对象是数据生产者推送给消费者的一系列值。然而，与事件不同的是，一个可观察对象可以向侦听器发送它已经完成的信号，并且不会发送更多的数据。

Observables can send data to consumers asynchronously. Unlike Array, there's no Javascript literal syntax for creating Observable sequences. However we can build a helper method that visually describes the contents of sequences as well as the times between each item's arrival. The seq function creates an Observable from an array of items, and adds a delay for every empty item encountered. Every ,,, adds up to a second.

可观察对象可以异步地向消费者发送数据。与数组不同的是，没有用于创建可观察序列的Javascript文字语法。但是，我们可以构建一个帮助器方法，它可以直观地描述序列的内容以及每个条目到达之间的时间。seq函数从一个项目数组中创建一个可观察对象，并为遇到的每个空项目添加一个延迟。每…加起来是一秒。

```js
// An array of numbers 1,2,3
var numbers123Array =      [1,2,3];

// A sequence that returns 1, and then after 4 seconds returns 2,
// and then after another second returns 3, and then completes.
var numbers123Observable = seq([1,,,,,,,,,,,,2,,,3]);

// Like Arrays, Observables can contain any object - even Arrays.
var observableOfArrays = seq([ [1,2,3],,,,,,[4,5,6],,,,,,,,,,,[1,2] ]);
```

Observables are a sequence of values, delivered one after the other. Therefore it's possible that an Observable can go on sending data to a listener forever just like a mouse move event. To create a sequence that doesn't complete, you can add a trailing ,,, to the end of the items passed to seq().

可观察值是一系列值，一个接一个地交付。因此，一个可观察对象可能会像鼠标移动事件一样，一直向侦听器发送数据。要创建不完整的序列，可以在传递给seq()的项的末尾添加尾随…。

```js
// The trailing ,,, ensures that the sequence will not complete.
var mouseMovesObservable =
  seq([ {x: 23, y: 55},,,,,,,{x: 44, y: 99},,,{x:55,y:99},,,{x: 54, y:543},,, ]);

// No trailing ,,, indicates that sequence will complete.
var numbers123Observable = seq([1,,,2,,,3]);
```

Querying Arrays only gives us a snapshot. By contrast, querying Observables allows us to create data sets that react and update as the system changes over time. This enables a very powerful type of programming known as reactive programming.

查询数组只给我们一个快照。相比之下，查询Observables允许我们创建数据集，这些数据集会随着系统的变化做出反应并进行更新。这支持一种非常强大的编程类型，称为反应性编程。

Let's start off by contrasting Observable with Events...

让我们从可观察到的与事件的对比开始…

#### Exercise 28: Subscribing to an event

You're probably used to thinking about events as a list of handlers stored in an object. In this example, we subscribe to a button click event and then unsubscribe the first time the button is clicked.

您可能习惯于将事件看作存储在对象中的处理程序列表。在本例中，我们订阅按钮单击事件，然后在第一次单击按钮时取消订阅。

```js
function(button) {
  // the button click handler
  var handler = function(ev) {
    // Unsubscribe from the button event.
    button.removeEventListener("click", handler);

    alert("Button was clicked. Unsubscribing from event.");
  };

  // add the button click handler
  button.addEventListener("click", handler);
}
```

Ask yourself this question: How is subscribing to an event different than traversing an array? Both operations involve sending a listener a sequence of items by repeatedly invoking a function. So why can't we traverse Arrays and Events the same way?

问问你自己这个问题:订阅一个事件和遍历一个数组有什么不同?这两种操作都涉及通过反复调用函数向侦听器发送项目序列。为什么我们不能以相同的方式遍历数组和事件呢?

#### Exercise 29: Traversing an Event

Subscribing to an Event and traversing an Array are fundamentally the same operation. The only difference is that Array traversal is synchronous and completes, and Event traversal is asynchronous and never completes. If we convert a button click Event to an Observable object, we can use do() to traverse the Event.

订阅事件和遍历数组基本上是相同的操作。唯一的区别是数组遍历是同步的并完成的，而事件遍历是异步的，并且永远不会完成。如果将按钮单击事件转换为可观察对象，则可以使用do()遍历该事件。

```js
function(button) {
  var buttonClicks = Observable.fromEvent(button, "click");

  var subscription =
    buttonClicks.
      do(function(clickEvent) {
        alert("Button was clicked. Stopping Traversal.");

        // Stop traversing the button clicks
        subscription.unsubscribe();
      }).
      subscribe();
}
```

Notice that Observable's forEach() function returns a Subscription object. Disposing of a Subscription object unsubscribes from the event and prevents memory leaks. Disposing of a subscription is the asynchronous equivalent of stopping half-way through a counting for loop.

注意，Observable的forEach()函数返回一个订阅对象。处理订阅对象将从事件中取消订阅，并防止内存泄漏。处理订阅相当于在循环计数的中途停止异步操作。

Disposing of a Subscription object is basically the same as calling removeEventListener(). On the surface, these two approaches to Event handling don't seem to be very different. Under the circumstances, why should we bother converting Events to Observables? The reason is that if we convert Events to Observable Objects, we can use powerful functions to transform them. In the next exercise we'll learn how we can use one such function to avoid dealing with Subscriptions in many cases...

处理订阅对象与调用removeEventListener()基本相同。从表面上看，这两种事件处理方法似乎没有太大的不同。在这种情况下，我们为什么要把事件转换成可观测的呢?原因是，如果我们将事件转换为Observable，我们可以使用强大的函数来转换它们。在下一个练习中，我们将学习如何使用一个这样的函数来避免在许多情况下处理订阅…

#### Exercise 30: Completing Sequences with take()

Have you ever wished that you could listen for the next occurrence of an event and then immediately unsubscribe? For example, developers will often attach an event handler to window.onload, expecting that their event handler will only be called once.

你是否曾经希望你可以收听下一个事件的发生，然后立即取消订阅?例如，开发人员经常将事件处理程序附加到`window.onload`，期望它们的事件处理程序只被调用一次。

```js
window.addEventListener(
  "load",
  function()
    // do some work here, but expect this function will only be called once.
  })
```

In instances such as this, it's good practice to unsubscribe from the event after it's fired. Failing to unsubscribe is a memory leak. Depending on the circumstances, memory leaks can seriously destablize your application and can be very difficult to track down. Unfortunately unsubscribing from an event after one occurrence can be cumbersome:

在这种情况下，最好在事件触发后取消订阅。未能取消订阅会造成内存泄漏。根据不同的情况，内存泄漏会严重破坏应用程序的性能，并且很难跟踪。不幸的是，在事件发生后取消订阅可能很麻烦:

```js
var handler = function() {
  // do some work here, then unsubscribe from the event
  window.removeEventListener("load", handler)
};
window.addEventListener("load", handler);
```

Wouldn't it be nice if there was an easier way to code this? That's why Observable has a take() function. The take() function works like this...

如果有一种更简单的编码方法不是很好吗?这就是为什么Observable有一个take()函数。take()函数的工作原理是这样的……

```js
seq([1,,,2,,,3,,,4,,,5,,,6,,,]).take(3) === seq([1,,,2,,,3]);
```

An Observable based on an Event will never complete on its own. The take() function creates a new sequence that completes after a discrete number of items arrive. This is important, because unlike an Event, when an Observable sequence completes it unsubscribes all of its listeners. That means that if we use take() to complete our Event sequence, we don't need to unsubscribe!

基于事件的可观察性永远不会自己完成。`take()`函数的作用是:创建一个新序列，该序列在到达离散数量的项之后完成。这一点很重要，因为与事件不同，当一个可观察序列完成时，它会取消所有侦听器的订阅。这意味着如果我们使用take()来完成事件序列，就不需要取消订阅!

<details>
<summary>
Let's repeat the previous exercise, in which we listened for a single button click and then unsubscribed. This time let's use the take() function to complete the sequence after the button is clicked.

让我们重复前面的练习，在该练习中，我们侦听单个按钮单击，然后取消订阅。这次我们使用take()函数在单击按钮后完成序列。
</summary>

```js
function(button) {
  var buttonClicks = Observable.fromEvent(button, "click");

  // Use take() to listen for only one button click
  // and unsubscribe.
  buttonClicks.
    // Insert take() call here
    take(1).
    forEach(function(clickEvent) {
      alert("Button was clicked once. Stopping Traversal.");
    });
}
```

</details>

The take() function is great way of listening for a discrete number of events and then unsubscribing, but Observable has an even more flexible function that we can use to complete sequences...

take()函数是侦听离散事件数量然后取消订阅的好方法，但是Observable有一个更灵活的函数，我们可以使用它来完成序列……

#### Exercise 31: Completing sequences with takeUntil()

Have you ever wanted to unsubscribe from one Event when another Event fires? Observable's takeUntil() function is a convenient way of completing a sequence when another Event occurs. Here's how takeUntil() works:

您是否曾经想在另一个事件触发时取消订阅某个事件?Observable的takeUntil()函数是在另一个事件发生时完成序列的一种方便方法。下面是takeUntil()的工作原理:

```js
var numbersUntilStopButtonPressed =
  seq(              [ 1,,,2,,,3,,,4,,,5,,,6,,,7,,,8,,,9,,, ]).
    takeUntil(seq([  ,,, {eventType: "click"},,, ]) )                    === seq([ 1,,,2 ])
```

Earlier we (unknowningly) built a dynamic Microsoft price stock ticker using Observable. The problem with that stock ticker was that it kept going on forever. If left unchecked, all the entries in the log could use up all of the memory on the page. In the exercise below, filter the Observable sequence of NASDAQ prices for MSFT stock prices, use the fromEvent() function to create an Observable .

早些时候，我们(不知不觉地)使用Observable构建了一个动态的Microsoft price股票行情。股票行情的问题是它会一直持续下去。如果不选中，日志中的所有条目都可能耗尽页面上的所有内存。在下面的练习中，过滤MSFT股票价格的NASDAQ价格的可观察序列，使用fromEvent()函数创建一个可观察的序列。

```js
function(pricesNASDAQ, printRecord, stopButton) {
  var stopButtonClicks = Observable.fromEvent(stopButton, 'click')// ----- To finish this expression, use Observable.fromEvent to convert the "click" event on the stop button to an Observable
    microsoftPrices =
      pricesNASDAQ.
        filter(function(priceRecord) {
          return priceRecord.name === "MSFT";
        }).
        takeUntil(stopButtonClicks)// ----- To finish this expression, use takeUntil to complete the sequence when stopButtonClicks fires.

  microsoftPrices.
    forEach(function(priceRecord) {
      printRecord(priceRecord);
    });
}
```

We've learned that Observable sequences are much more powerful than raw events, because they can complete. The take() and takeUntil() functions are powerful enough to ensure that we never have to unsubscribe from another event again! This reduces the risk of memory leaks and makes our code more readable.

我们已经知道，可观察序列比原始事件强大得多，因为它们可以完成。take()和takeUntil()函数功能强大，足以确保我们再也不必取消订阅另一个事件!这降低了内存泄漏的风险，并使我们的代码更具可读性。

Here's what we learned in this section:
以下是我们在这一节中学到的:

+ We can traverse Observables using forEach().
  我们可以使用forEach()遍历可观察对象。
+ We can use fromEvent() to convert Events into Observables that never complete.
  我们可以使用fromEvent()将事件转换为永不完成的可观察对象。
+ We can apply take() and takeUntil() to an Observable to create a new sequence which does complete.
  我们可以将take()和takeUntil()应用于一个可观察对象，以创建一个完成的新序列。

In the next section we'll learn how to combine events to create more complex events. You'll be suprised how easily you can solve complex, asynchronous problems!

在下一节中，我们将学习如何组合事件来创建更复杂的事件。您会惊奇地发现，您可以轻松地解决复杂的异步问题!

### Querying Observables

What's the difference between these two tasks?
What's the difference between these two tasks?

1. Creating a flat list of movies with a rating of 5.0 from a bunch of movie lists.
   从一堆电影列表中创建一个评分为5.0的电影列表。
2. Creating a sequence of all the mouse drag events from the mouseDown, mouseMove, and mouseUp events.
   从mouseDown、mouseMove和mouseUp事件创建所有鼠标拖动事件的序列。

You might think of them as different, and might code them very differently, but **these tasks are fundamentally the same**. Both of these tasks are queries, and can be solved using the functions you've learned in these exercises.

您可能认为它们是不同的，并且代码也可能非常不同，但是**这些任务本质上是相同的**。这两个任务都是查询，可以使用您在这些练习中所学到的函数来解决。

**The difference between traversing an Array and traversing an Observable is *the direction in which the data moves.*** When traversing an Array, the client pulls data from the data source, blocking until it gets a result. When traversing Observables, the data source pushes data at the client whenever it arrives.

**遍历数组和遍历可观察对象的区别在于*数据移动的方向***。遍历数组时，客户机从数据源中提取数据，并进行阻塞，直到得到结果。当遍历可观察对象时，数据源将数据推送到客户机。

It turns out that the direction in which data moves is orthogonal to querying that data. In other words, **when we're querying data it doesn't matter whether we pull data, or data is pushed at us**. In either case the query methods make the same transformations. The only thing that changes is the input and output type respectively. If we filter an Array, we get a new Array. If we filter an Observable, we get a new Observable, and so on.

事实证明，数据移动的方向与查询该数据是正交的。换句话说，**当我们查询数据时，是拉数据还是数据被推入并不重要**。在这两种情况下，查询方法都执行相同的转换。惟一改变的是输入和输出类型。如果我们过滤一个数组，我们会得到一个新的数组。如果我们过滤一个Observable，我们会得到一个新的Observable，以此类推。

Take a look at how the query methods transform Observables and Arrays:
看看查询方法如何转换可观察对象和数组:

```js
// map()

[1,2,3].map(x => x + 1)                                         === [2,3,4]
seq([1,,,2,,,3]).map(function(x) { return x + 1})               === seq([2,,,3,,,4])
seq([1,,,2,,,3,,,]).map(function(x) { return x + 1 })           === seq([2,,,3,,,4,,,])

// filter()

[1,2,3].filter(function(x) { return x > 1; })                   === [2,3]
seq([1,,,2,,,3]).filter(function(x) { return x > 1; })          === seq([2,,,3])
seq([1,,,2,,,3,,,]).filter(function(x) { return x > 1; })       === seq([2,,,3,,,])

// concatAll()

[ [1, 2, 3], [4, 5, 6] ].concatAll()                             === [1,2,3,4,5,6]
seq([ seq([1,,,2,,,3]),,,seq([4,,,5,,,6]) ]).concatAll()         === seq([1,,,2,,,3,,,4,,,5,,,6])

// If a new sequence arrives before all the items
// from the previous sequence have arrived, no attempt
// to retrieve the new sequence's elements is made until
// the previous sequence has completed. As a result the
// order of elements in the sequence is preserved.
/** 如果一个新序列在前一个序列的所有项到达之前到达，
 * 则在前一个序列完成之前不会尝试检索新序列的元素。
 * 结果保留了序列中元素的顺序。
 **/
seq([
  seq([1,,,, ,2, ,3])
  ,,,seq([,,4, ,5, ,,6]) ]).
  concatAll()                                                  === seq([1,,,,,2,,3,,4,,5,,,6])

// Notice that as long as at least one sequence being
// concatenated is incomplete, the concatenated sequence is also
// incomplete.
seq([
  seq([1,, ,,, ,,,2,,,3])
  ,,,seq([4,,,5,,, ,,, ,,6,,,]) ]).
  concatAll()                                                  === seq([1,,,,,,,,2,,,3,4,,,5,,,,,,,,6,,,])

// reduce()

[ 1, 2, 3 ].reduce(sumFunction)                                 === [ 6 ]
seq([ 1,,,2,,,3 ]).reduce(sumFunction)                          === seq([,,,,,,6])

// Reduced sequences do not complete until the sequence does.
seq([ 1,,,2,,,3,,, ]).reduce(sumFunction)                       === seq([ ,,,,,,,,,])

// zip()

// In both Arrays and Observables, the zipped sequence
// completes as soon as either the left or right-hand
// side sequence completes.
Array.zip([1,2],[3,4,5], sumFunction)                           === [4,6]
Observable.zip(seq([1,,,2]),seq([3,,,4,,,5]), sumFunction)      === seq([4,,,6])

// take()
[1,2,3].take(2)                                                 === [1, 2]
seq([ 1,,,2,,,3 ]).take(2)                                      === seq([ 1,,,2 ])
seq([ 1,,,2,,,3,,, ]).take(2)                                   === seq([ 1,,,2 ])

// takeUntil()

// takeUntil works for Arrays, but it's not very useful
// because the result will always be an empty array.
[1,2,3].takeUntil([1])                                          === []

seq([1,,,2,,,3,,, ]).takeUntil(
seq([ ,,, ,,4 , ]))                                             === seq([ 1,,,2 ])
```

Remember when I prohibited the use of array indexers? The reason for that restriction should now become clearer to you. Whereas the 5 functions can be used on any collection, indexers can only be used on collections that support random-access (like Array). If you avoid indexers and stick to the functions you've learned in this tutorial, you'll have a unified programming model for transforming any collection. Having a unified programming model makes it trivial to convert synchronous code to asynchronous code, a process which would otherwise be very difficult. As we've demonstrated, you don't need indexers to perform complex collection transformations.

还记得我什么时候禁止使用数组索引器吗?这个限制的原因现在应该对您更清楚了。这5个函数可以用于任何集合，而索引器只能用于支持随机访问的集合(如数组)。如果您避免使用索引器并坚持使用本教程中学习的函数，您将拥有一个用于转换任何集合的统一编程模型。拥有统一的编程模型使得将同步代码转换为异步代码变得非常简单，否则这将是一个非常困难的过程。正如我们所演示的，您不需要索引器来执行复杂的集合转换。

Now that we've seen that we can query asychronous and synchronous data sources using the same programming model, let's use Observable and our query functions to create complex new events.

既然我们已经看到可以使用相同的编程模型查询同步数据源，那么让我们使用Observable和查询函数来创建复杂的新事件。

### Observable & Events

通过Observable，我们可以把一个事件转换为一个数据流，根据通过组织数据流的结构来按照我们想要的顺序返回一个新的数据流。这样就可以把复杂的异步交互转换为同步的数据流来进行处理。

### Anatomy of Observable

Observable只会产出数据流，在使用froEach方法之前并不会对数据进行操作。Observable只是将函数传入另一个函数，最终将其组合起来，创建一个新的组合函数，使用forEach进行使用。

Observable就是一个拥有forEach方法的对象，forEach分别接受三个参数：onNext, onError, onComplete三个参数来处理三种情况。

### Creating a setTimeout Observable

```js
function timeout(time) {
  return {
    forEach(observer) {
      const handle =
        setTimeout(() => {
          observer.onNext(undefined),
          observer.onCompleted()
        }, time)
      // subscription
      return {
        dispose() {
          clearTimeout(handle)
        }
      }
    }
  }
}

timeout(500).forEach({
  onNext: () => console.log('timeout'),
  onCompleted: () => console.log('onComplete')
})
```

```js
function fromEvent(dom, eventName) {
  return {
    forEach(observer) {
      const handler = e => observer.onNext(e)
      dom.addEventListener(eventName, handler)

      return {
        dispose() {
          removeEventListener(eventName, handler)
        }
      }
    }
  }
}
```

### Observable vs. Promise

Observable在创建时只是创建了数据流，Promise创建时会立即返回一个promise对象，并调用浏览器API，所以Observable是懒惰模式，promise是饥饿模式。promise一经调用就无法取消。使用Observable可以轻松地retry，但是promise就比较复杂。使用observable可以传入promise对象，但是promise对象中无法使用observable。

#### Exercise 32: Creating a mouse drag event

Remember the exercise we solved earlier? The one in which we retrieved all the movies with a rating of 5.0 from an array of movie lists? If we were to describe the solution in pseudocode it might read something like this...

还记得我们之前解过的题吗?我们从一组电影列表中检索评分为5.0的所有电影?如果我们用伪代码来描述解决方案，它可能是这样的……

"For every movie list, retrieve only those videos with a rating of 5.0"

“对于每个电影列表，只检索评分为5.0的视频”

```js
var moviesWithHighRatings =
  movieLists
    .concatMap(movieList =>
      movieList.videos
        .filter(video => video.rating === 5.0)
    )
```

Now we're going to create a mouseDrag event for a DOM object. If we were to describe this problem as pseudocode it might read something like this...

现在我们要为DOM对象创建一个mouseDrag事件。如果我们把这个问题描述为伪代码，它可能是这样的……

"For every movie list mouse down event on the sprite, retrieve only those videos with a rating of 5.0 mouse move events that occur before the next mouse up event."

“对于sprite上的每个电影列表鼠标下拉事件，只检索那些在下一次鼠标上拉事件之前发生的评分为5.0的鼠标移动事件的视频。”

```js
function(sprite, spriteContainer) {
  var spriteMouseDowns = Observable.fromEvent(sprite, 'mousedown'),
      spriteContainerMouseMoves = Observable.fromEvent(spriteContainer, 'mousemove'),
      spriteContainerMOuseUps = Observable.fromEvent(spriteContainer, 'mouseup'),
      spriteMouseDrags =
        spriteMouseDowns
          .concatMap(concatPoint =>
            srriteContainerMouseMoves // ...retrieve all the mouse move events on the sprite container...
              .takeUntils(spriteContainerMOuseUps) // ...until a mouse up event occurs.
          )

  // For each mouse drag event, move the sprite to the absolute page position.
  spriteMouseDrags.forEach(dragPoint => {
    sprite.style.left = dragPoint.pageX + 'px'
    sprite.style.top = dragPoint.pageY + 'px'
  })
}
```

Now we're really cooking. We just created a complex event with a few lines of code. We didn't have to deal with any subscriptions objects, or write any stateful code whatsoever. Let's try something a little harder.

现在我们真的在做饭。我们刚刚用几行代码创建了一个复杂的事件。我们不需要处理任何订阅对象，也不需要编写任何有状态代码。让我们再努力一点。

#### Exercise 33: Improving our mouse drag event

Our mouse drag event is a little too simple. Notice that when we drag around the sprite, it always positions itself at the top-left corner of the mouse. Ideally we'd like our drag event to offset its coordinates, based on where the mouse was when the mouse down event occurred. This will make our mouse drag more closely resemble moving a real object with our finger.

我们的鼠标拖动事件有点太简单了。注意，当我们拖动sprite时，它总是将自己定位在鼠标的左上角。理想情况下，我们希望我们的拖动事件偏移它的坐标，基于鼠标在鼠标下拉事件发生时的位置。这将使我们的鼠标拖动更接近于用手指移动一个真实的物体。

Let's see if you can adjust the coordinates in the mouse drag event, based on the mousedown location on the sprite. The mouse events are sequences, and they look something like this:

让我们看看是否可以根据sprite上的mousedown位置在鼠标拖动事件中调整坐标。鼠标事件是序列，它们看起来像这样:

```js
spriteContainerMouseMoves =
  seq([ {x: 200, y: 400, layerX: 10, layerY: 15},,,{x: 210, y: 410, layerX: 20, layerY: 26},,, ])
```

Each item in the mouse event sequences contains an x, y value that represents that absolute location of the mouse event on the page. The moveSprite() function uses these coordinates to position the sprite. Each item in the sequence also contains a pair of layerX and layerY properties that indicate the position of the mouse event relative to the event target.

鼠标事件序列中的每个项都包含一个x, y值，表示鼠标事件在页面上的绝对位置。 moveSprite()函数的作用是:使用这些坐标来定位sprite。序列中的每个项还包含一对layerX和layerY属性，它们指示鼠标事件相对于事件目标的位置。

```js
function(sprite, spriteContainer) {
  // All of the mouse event sequences look like this:
  // seq([ {pageX: 22, pageY: 3423, layerX: 14, layerY: 22} ,,, ])
  var spriteMouseDowns = Observable.fromEvent(sprite, "mousedown"),
    spriteContainerMouseMoves = Observable.fromEvent(spriteContainer, "mousemove"),
    spriteContainerMouseUps = Observable.fromEvent(spriteContainer, "mouseup"),
    // Create a sequence that looks like this:
    // seq([ {pageX: 22, pageY:4080 },,,{pageX: 24, pageY: 4082},,, ])
    spriteMouseDrags =
      // For every mouse down event on the sprite...
      spriteMouseDowns.
        concatMap(function(contactPoint) {
          // ...retrieve all the mouse move events on the sprite container...
          return spriteContainerMouseMoves.
            // ...until a mouse up event occurs.
            takeUntil(spriteContainerMouseUps).
            // ------------   INSERT CODE HERE  -----------------
            // Project each mouse move object into a new object
            // with adjusted pageX and pageY properties.
            // Translate each page coordinate based on the value
            // of the layerX and layerY properties in the
            // contactPoint.
            // -------------------------------------------------
            // Complete expression...
            map(movePoint => ({
              pageX: movePoint.pageX - contactPoint.layerX,
              pageY: movePoint.pageY - contactPoint.layerY
            }))
        });

  // For each mouse drag event, move the sprite to the absolute page position.
  spriteMouseDrags.forEach(function(dragPoint) {
    sprite.style.left = dragPoint.pageX + "px";
    sprite.style.top = dragPoint.pageY + "px";
  });
}
```

## Handing HTTP Requests with Observables

### Exercise 34: HTTP request

可以使用[jsbin](https://jsbin.com/?html,css,js,output)来引入库并书写执行代码。

Events aren't the only source of asynchronous data in an application. There's also HTTP requests. Most of the time HTTP requests are exposed via a callback-based API. To receive data asynchronously from a callback-based API, the client typically passes a success and error handler to the function. When the asynchronous operation completes, the appropriate handler is called with the data. In this exercise we'll use jQuery's getJSON api to asynchronously retrieve data.

事件不是应用程序中异步数据的唯一来源。还有HTTP请求。大多数时候HTTP请求是通过基于回调的API公开的。为了异步地从基于回调的API接收数据，客户机通常将一个成功和错误处理程序传递给函数。当异步操作完成时，将使用数据调用适当的处理程序。在本练习中，我们将使用jQuery的getJSON api异步检索数据。

```js
function($) {
  $.getJSON(
    "http://api-global.netflix.com/queue",
    {
      success: function(json) {
        alert("Data has arrived.");
      },
      error: function(ex) {
        alert("There was an error.")
      }
    });
}
```

## Observable In-Depth

### Creating an Observable Class

```js

// Observable构造函数
function Observable(forEach) {
  this._forEach = forEach
}

Observable.prototype = {
  forEach(onNext, onError, onCompleted) { // 传入的参数是函数
    if (typeof onNext === 'function') {
      return this._forEach({
        onNext: onNext,
        onError: onError || function() {},
        onCompleted: onCompleted || function() {}
      })
    } else { // 传入的是一个observer对象
      return this._forEach(onNext)
    }
  }
}

Observable.fromEvent = function fromEvent(dom, eventName) {
  return new Observable(function forEach(observer) {
    const handler = event => observer.onNext(event)
    dom.addEventListener(eventName, handler)

    return {
      dispose() {
        dom.removeEventListener(eventName, handler)
      }
    }
  })
}
```

### Observable Map Function

```js
Observable.prototype = {
  // 传入的参数是函数
  forEach(onNext, onError, onCompleted) {
   ...
  },

  map(projectionFunction) {
    const self = this
    return new Observable(function forEach(observer) {
      return self.forEach(
        function onNext(x) {
          observer.onNext(projectionFunction(x))
        },
        function onError(err) {
          observer.onError(err)
        },
        function onCompleted() {
          observer.onCompleted()
        }
      )
    })
  }
}
```

### Observable Filter Function

```js
Observable.prototype = {
  // 传入的参数是函数
  forEach(onNext, onError, onCompleted) {
   ...
  },

  map(projectionFunction) {
    ...
  },

  filter(testFunction) {
    const self = this
    return new Observable(function forEach(observer) {
      return self.forEach(
        function onNext(x) {
          if (testFunction(x)) observer.onNext(x)
        },
        function onError(err) {
          observer.onError(err)
        },
        function onCompleted() {
          observer.onCompleted()
        }
      )
    })
  }
}
```

### Observable Take Function

[Object.observe](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/observe) - 已废弃，现使用[Proxy](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)代替

```js
Observable.prototype = {
  // 传入的参数是函数
  forEach(onNext, onError, onCompleted) {
    if (typeof onNext === 'function') {
      return this._forEach({
        onNext: onNext,
        onError: onError || function() {},
        onCompleted: onCompleted || function() {}
      })
    } else {
      // 传入的是一个observer对象
      return this._forEach(onNext)
    }
  },

  map(projectionFunction) {
   ..
  },

  filter(testFunction) {
    ...
  },

  take(num) {
    const self = this
    return new Observable(function forEach(observer) {
      let counter = 0
      const subscription = self.forEach(
        function onNext(data) {
          observer.onNext(data)
          counter++
          if (counter === num) {
            observer.onCompleted()
            subscription.dispose()
          }
        },
        function onError(err) {
          observer.onError(err)
        },
        function onCompleted() {
          observer.onCompleted()
        }
      )

      return subscription
    })
  }
}
```

### Syncing Data with the Server

可以使用combineLatest来合并最新数据提交给服务器

### Observables as Animations

通过mergeAll，concatAll和switchAll的不同特性，可以设置不同的动画曲线。
