---
title: A Practical Guide to Algorithms with JavaScript
date: 2019-09-12 13:10:30
categories: [学习笔记]
tags: [FrontEndMasters, JavaScript, Data Structures, Algorithms]
---
## Introduction

[slide](https://slides.com/bgando/intro-to-algorithms/#/)

+ What is an algorithem?
  + Algorithm is just the steps that you take to solve a problem
+ Why should you care?  
  + Engineer's job is to solve problems

+ What will we cover today?
  1. Estimate generally how fast an algorithms is
  2. Use some techniques to optimize certain types of algorithms
  3. Get comfortable with recursion
  4. Implement a couple sorting and searching algorithms
  5. Understand the difference between Divide & Conquer and Dynamic Programming
  6. Learn about the pros and cons of the Greedy technique
  7. Cover a recursive brute force algorithms
<!-- more -->
## Space & Time Complexity

### Introducing Space & Time Complexity

+ What make an algorithm fast
  + **Space** complexity
    + How much **memory** is used
  + **Time** complexity
    + How many primitive **operations** are executed

... with respect to input size
... and assuming worst case scenarios

+ Problem:
  + Given a list of hotels, return the price range of hotels in a given search result
  + let's write an algorithm to do the job
  + We'd expect that the more data we have, the longer it will take to figure out the min and max required for the range
  + However, as our dataset grows, the cost can grow really fast or slow

+ Approach #1: compare all numbers to one another

```js
var hotels = [
  {price: 200, brand: 'Estin'},
  {price: 50, brand: 'Best Eastern'},
  ...
  ...
  {price: 175, brand: 'Radishin'}
]
```

As our data grows, how much does our work increase?

| # of hotels(n) | 3   | 5   | 10  | 100  |
| -------------- | --- | --- | --- | ---- |
| # Ops          | 9   | 25  | 100 | 1000 |

We call this n^2, where n is the number of hotels. As n grows, the amount of work increase at that rate

+ Appraoch #2: Track min & max

使用两个循环，第一个寻找最大值，第二个寻找最小值

How many comparisons were made?

| Price | 200 | 50  | ... | 175 |
| ----- | --- | --- | --- | --- |
| Max?  | 200 | 200 | ... | 200 |
| Min?  | 200 | 50  | ... | 50  |

We consider this 2n beaucase as the data grows, the amount of work increases by 2

+ Approach #3: Sorted list

如果已经是一个排序过的数组，那么明显只要查找两次即可。

| # of Operations | Algorithm                        |
| --------------- | -------------------------------- |
| n^2             | compare all numbers              |
| 2n              | Find min and max numbers         |
| 2               | Sorted list, find first and last |

| Big-O, name       | # of Operations | Algorithm                        |
| ----------------- | --------------- | -------------------------------- |
| O(n^2), quadratic | n^2             | compare all numbers              |
| O(n),linear       | 2n              | Find min and max numbers         |
| O(1), constant    | 2               | Sorted list, find first and last |

|              | Super fast |             |        |           | Super Slooooow |
| ------------ | ---------- | ----------- | ------ | --------- | -------------- |
| **Name**     | constant   | logarithmic | linear | quadratic | exponential    |
| **Notation** | O(1)       | O(logn)     | O(n)   | O(n^2)    | O(k^n)         |

### Native Methods & JS Expressions

```js
// What are some simple, native JS methods/expressions/operations?

const arr = [1,2,3]
arr.pop() // shift和unshift并不是常量级操作，以为要移动整个数组来在数组头部添加或删除项
arr[1]

const obj = { a: 1, b: 2, c: 3 }
obj.a

1 + 1

// 以上时间复杂度都是常数级
```

### Big O Notation

+ Calculating Time
  + What do we do if we have multiple expressions/loops/etc?

如果是常数操作，复杂度相加，例如三个加法，可视作O(3)，简化为O(1)

如果套嵌循环，那么复杂度相乘，例如三重套嵌循环，复杂度O(n^3)

也就是循环内的操作的时间复杂度要与循环相乘

+ Complexity of Common Operations

| **Complexity** | Operation                                      |
| -------------- | ---------------------------------------------- |
| O(1)           | Running a statement                            |
| O(1)           | Value look-up on an array, object, variable    |
| O(logn)        | Loop that cuts problem in half every iteration |
| O(n)           | Looping through the values of an array         |
| O(n^2)         | Double nested loops                            |
| O(n^3)         | Tirple nested loops                            |

### Space Complexity & Review

与时间复杂度类似，主要考虑每个操作是否创建了新的数据结构及数据结构的大小

Time complexity of an algorithms signifies the total time required by the program to run to completion. The time complexity of algorithms is most commonly expressed using the **Big O notation**

Big O notation gives us an industry-standard language to discuss the performance of algorithms.Not knowing how to speak this language can make you stand out as an inexperienced programmer

Did you know there are other notations that are typically used in academic settings? Learn more [here](https://www.geeksforgeeks.org/analysis-of-algorithms-set-3asymptotic-notations/)

### Big O: Loop

[Big-O Cheat Sheet](https://www.bigocheatsheet.com/)

+ What is the TC(time complexity)

```js
var countChars = function(str) {
  var count = 0 // O(1)
  for(let i = 0; i < str.length; i++) {
    count++ // O(n)
  }
  return count // O(1)
}

countChars('dance')
countChars('walk')
```

以上操作为线性(O(n))时间复杂度

### Big O: Property Lookup

+ What is the TC?

```js
var countChars = function (str) {
  return str.length // js会自动追踪字符串或者数组长度，并作为属性添加在字符串和数组上，所以此操作为O(1)
}

countChars('dance')
countChars('walk')

// How much more work would it take to get the length of 1 million char string?
```

上述函数为属性查找操作，时间复杂度为常数级。

### Bio O: Push, Shift & Unshift

+ What is the TC?

```js
var myList = ['hello', 'hola']

myList.push('bonjour')
mylist.unshift()
myList.shift()
// Calculate the time complexity for the native methods above(separately)
```

就像之前所说，删除或添加数组第一项，我们要将其他的项后移或者前移，时间复杂度O(n)

## Optimization with Caching

### Faster Algorithms

+ Time Complexity?

```js
const isUnique = (arr) => {
  let result = true;
  
  for (let i = 0; i < arr.length; i++) {
    console.log(`~~~~ OUTER LOOP ~~~~ i === ${i}`);

    for (let j = 0; j < arr.length; j++) {
      console.log(`~~~~ INNER LOOP ~~~~ j === ${j}`);
      if (i !== j && arr[i] === arr[j]) {
        result = false;
      }
    }
  }
  
  return result;
};

console.log(isUnique([1,2,3]) === true);
console.log(isUnique([1,1,3]) === false);
```

上面我们循环中套嵌了循环，所以时间复杂度为平方级(O(n^2))

+ We can do better

```js
isUnique = (arr) => {
  const breadcrumbs = {};
  let result = true;
  
  for (let i = 0; i < arr.length; i++) {
    console.log(`~~~~ LOOP ~~~~ i === ${i}`);
    if (breadcrumbs[arr[i]]) {
      result = false;
    } else {
      breadcrumbs[arr[i]] = true;
    }
  }
  
  return result;
};

console.log(isUnique([1,2,3]) === true);
console.log(isUnique([1,1,3]) === false);
```

在上面的方法中，我们使用一个新对象来保存已经循环过得数组的项，并不断查找数组项是否已经存在于对象中。用空间换取时间，时间复杂度为线性(O(n))，空间复杂度为(O(n))。

### Exercise: Unique Sort

```js
//Task: Transform this simple sorting algorithm into a unique sort.
// It should not return any duplicate values in the sorted array.

//input: [1,5,2,1] => output: [1,2,5]
//input: [4,2,2,3,2,2,2] => output: [2,3,4]

const uniqSort = function(arr) {
    const breadcrumbs = {};
    const result = []
    for(let i = 0; i < arr.length; i++) {
      const current = arr[i]
      if(!breadcrumbs[current]) {
        result.push(current)
        breadcrumbs[current] = true
      }
    }
    return result.sort((a, b) => a - b);
};

uniqSort([4,2,2,3,2,2,2]); // => [2,3,4]
```

### Caching with Memoization

Memoization: caching the value that a function returns

```js
const factorial = (n) => {
    // Calculations: n * (n-1) * (n-2) * ... (2) * (1)
    return factorial;
}

factorial(35);
factorial(36); // factorial(36) = factorial(35) * 36
```

### Exercise: Basic Memoization

```js
// Task 1: Write a function, times10, that takes an argument, n, and multiples n times 10
// a simple multiplication fn
const times10 = (n) => { return n * 10 };

console.log('~~~~~~~~~~~~~~TASK 1~~~~~~~~~~~~~~');
console.log('times10 returns:', times10(9));

// Task 2: Use an object to cache the results of your times10 function.
// protip 1: Create a function that checks if the value for n has been calculated before.
// protip 2: If the value for n has not been calculated, calculate and then save the result in the cache object.

const cache = {};

const memoTimes10 = (n) => {
  if(!cache[n]) {
    cache[n] = times10(n)
  }
  return chche[n]
}

console.log('~~~~~~~~~~~~~~TASK 2~~~~~~~~~~~~~~');
console.log('Task 2 calculated value:', memoTimes10(9));  // calculated
console.log('Task 2 cached value:', memoTimes10(9));  // cached
```

### Exercise: Memoization with Closure

```js
// Task 3: Clean up your global scope by moving your cache inside your function.
// protip: Use a closure to return a function that you can call later.
const times10 = (n) => (n * 10);

const memoizedClosureTimes10 = () => {
  const cache = {}
  return function memoTimes10(n) {
    if(!cache[n]) {
      cache[n] = times10(n)
      console.log('calculated')
      return cache[n]
    }
    console.log('cached')
    return cache[n]
  }
};

const memoClosureTimes10 = memoizedClosureTimes10();
console.log('~~~~~~~~~~~~~~TASK 3~~~~~~~~~~~~~~');
try {
  console.log('Task 3 calculated value:', memoClosureTimes10(9));  // calculated
  console.log('Task 3 cached value:', memoClosureTimes10(9));  // cached
} catch(e) {
  console.error('Task 3:', e);
}
```

### Exercise: Generic Memoize Funciton

```js
const times10 = (n) => (n * 10);
// Task 4: Make your memo function generic and accept the times10 function as a callback rather than defining the n * 10 logic inside the if/else or pulling it in from the global scope.

// protip: Take advantage of the fact that parameters are saved in the closure as well, just like the cache from the previous example.
const memoize = (cb) => {
  const cache = {}
  return function memoizeCb(n) {
    if(!cache[n]) {
      cache[n] = cb(n)
    }
    return cache[n]
  }
}

// returned function from memoizedAdd
const memoizedTimes10 = memoize(times10);
console.log('~~~~~~~~~~~~~~TASK 4~~~~~~~~~~~~~~');
try {
  console.log('Task 4 calculated value:', memoizedTimes10(9));  // calculated
  console.log('Task 4 cached value:', memoizedTimes10(9));  // cached
} catch(e) {
  console.error('Task 4:', e)
}
```

## Recursion

### Introducing Recursion

+ Recursion is simply when a function calls itself; however it doesn't stop there.

+ Why recursion?
  + Elegant solution to keep your code D.R.Y.
  + Expected CS knowledge

### Call Stack Walkthrough

+ Call Stack Game
  1. Push **called** Fn on stack
  2. Execute Fn body
  + until...
  + ...another fn is called:
    + Pause the current execution and start at step 1
  + ...a return is hit:
    + Pop the current Fn off the stack
    + Resume executing the previous Fn

递归可以看做是一种循环，为了不进入无限循环，首先要确定停止条件。

```js
var tracker = 0

var callMe = function (arg){
  tracker++
  if(tracker === 3){
    tracker = 0
    return `loops! ${arg}`
  }
  return callMe('anytime')
}

callMe()
```

### Looping with Recursion

+ Recursion in 4 steps
  1. Identify base case(s)
  2. Identify recursive case(s)
  3. Return where appropriate
  4. Write procedures for each case that bring you closer to the base case(s)

```js
var loopTimes = n => {
  console.log('n===', n)
  if(n <= 1) {
    return 'complete'
  }
  return loopTimes(n - 1)
}

loopTimes(3)
```

### Factorial with a Loop

```js
function computeFactorial(num) {
  var result = 1
  for(let i = 2; i <= num; i++) {
    result *= i
  }
  return result
}
```

```js
function computeFactorial(num, total = 1) {
  if(num === 0) return total
  return computeFactorial(num - 1, num * total)
}

computeFactorial(5)
```

+ Recursion can always be implemented as a loop, but in some situations, believe it or not, it is simpler to use recursion

+ Tail-Call Optimization
  + ES6 offers TCO, which allows some functions to be called without growing the call stack
  + Read more [here](http://www.2ality.com/2015/06/tail-call-optimization.html) and [here](http://www.integralist.co.uk/posts/js-recursion.html)

### Wrapper Function

+ Common Patterns for Recursion
  + Wrapper Funcitons
  + Accumulators

```js
function wrapperFnLoop(start, end) { // 使用闭包记录变量
  function recurse(i) {
    console.log(`looping from ${start} until ${end}`);

    if(i < end) {
      recurse(i + 1);
    }
  }

  recurse(start)
}

function MemoFnLoop(i, end) { // 使用参数记录变量
  console.log(`looping from ${i} until ${end}`);
  if(i < end) {
    MemoFnLoop(i + 1, end);
  }
}

console.log('~~~ wrapperFnLoop ~~~')
wrapperFnLoop(1,5);
console.log('~~~ MemoFnLoop ~~~')
MemoFnLoop(1, 6);
```

### Accumulators

```js
function joinElements(array, joinString) {
  function recurse(index, resultSoFar) {
    resultSoFar += array[index]

    if(index === array.length - 1) {
      return resultSoFar
    }
    return recurse(index + 1, resultSoFar + joinString)
  }

  return recurse(0, '')
}

joinElements(['s','cr','t cod', ' :) :)'], 'e');
```

### Exercise: Iterative Loop Exercise

```js
// Task: 将上面的代码使用循环重写

function joinElements(array, joinString) {
  let result = ''
  for(let i = 0; i < array.length - 1; i++) {
    result += array[i] + joinString
  }
  return result + array[array.length - 1]
}

joinElements(['s','cr','t cod', ' :) :)'], 'e');
```

### Exercise: Recursive Factorial & Memoize

```js
// Task 1: Without peeking, write your own recursive factorial method
function factorial(num, total = 1) {
  if(num === 0) return total
  return factorial(num - 1, num * total)
}

factorial(5)
// Task 2: Use your memo function from the previous exercise to memoize your factorial method
function memo(fn) {
  const cache = {}
  return function memoize(...args) {
    const num = args[0]
    if(!cache[num]) {
      console.log('cached')
      cache[num] = fn(num)
    }
    return cache[num]
  }
}

var memoFactorial = memo(factorial)
memoFactorial(100)
memoFactorial(100)
```

### Divide & Conquer

分治法通过递归将复杂问题拆解为小问题进行求解。比较经典的就是二分查找。

+ Binary Search
  + Search for a value in a *sorted* array by cutting the side of the search area in half

+ Linear Search
  + Search for a value in an array by checking each value in order

### Exercise: Linear Search

```js
// TASK: Implement linear search.

function linearSearch(list, item) {
  let index = -1
  list.forEach((listItem, listIndex) => {
    if(listItem === item) {
      index = listIndex
    }
  })
  return index
}

linearSearch([2,6,7,90,103], 90);
```

### Binary Search

```js
function binarySearch(list, item) {
  let min = 0
  let max = list.length - 1
  let guess

  while(min <= max) {
    guess = Math.floor((min + max) / 2)

    if(list[guess] === item) {
      return guess
    }else {
      if(list[guess] < item) {
        min = guess + 1
      } else {
        max = guess - 1
      }
    }
  }

  return -1
}

binarySearch([2, 6, 7, 90, 103], 90)
```

### Divide & Conquer Review

+ Recursive calls to a subset of the problem
  0. Recongnize base case
  1. Divide: Break problem down during each call
  2. Conquer: Do work on each subset
  3. Combine: Solutions

### Sorting Types

+ Naive Sorts
  + Keep looping and comparing values until the list is sorted
    + Bubble Sort
    + Insertion Sort
    + Selection Sort

+ Divide & Conquer Sorts
  + Recursively divide lists and sort smaller parts of list until entire list is sorted
    + Mergesort
    + Quiksort

+ *Bubble Sort*
  + Loop through an array, comparing adjacent indices and swapping the greater value to the end

[动画演示](http://rebootjeff.github.io/comparisonsort/)

+ *Merge sort*
  + Recursively merge sorted sub-list

### Merge Sort

+ Concept: Merging Lists
  + The merge step takes two sorted lists and merges them into 1 sorted list

+ Pseudocode: Merge Routine

```js
merge(L, R)
// [3, 27] // [9, 10]
// initialize empty array
// compare the first index of the left array
// to the first index of the right array
// push the lower value to the empty array
// shift the array with the lower value
// repeat until both array are empty
```

+ Concept: Merge Sort
  + Step 1:
    + Divide input array into 'n' single element subarrays
  + Step 2:
    + Repeatedly merge subarrays and sort on each merge

[youtube 并归排序舞蹈](https://www.youtube.com/watch?v=XaqR3G_NVoo)

+ Pseudocode: Merge Sort

```pseudocode
mergeSort(list)
  base case: if list.length < 2, return
  break the list into halves L & R
  Lsroted = mergeSort(L)
  Rsroted = mergeSort(R)
  return merge(Lsorted, Rsorted)
```

mergeSort的时间复杂度为O(nlogn)，因为递归的将数组拆分为多个子数组，并用线性的方式对子数组进行排序。

### Exercise: Bubble Sort

```js
function bubbleSort(list) {
  let countOuter = 0
  let countInner = 0
  let countSwap = 0
  for(let i = 0; i < list.length; i++) {
    countOuter++
    for(let j = 0; j < i; j++) {
      countInner++
      if(list[j] > list[i]) {
        countSwap++
        ;[list[j], list[i]] = [list[i], list[j]]
      }
    }
  }
  console.log('outer:', countOuter, 'inner:', countInner, 'swap:', countSwap);
  return list
}

bubbleSort([9, 2, 5, 6, 4, 3, 7, 10, 1, 8])
bubbleSort([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
bubbleSort([10, 9, 8, 7, 6, 5, 4, 3, 2, 1])
```

```js
function swap(array, i, j) {
  ;[array[i], array[j]] = [array[j], array[i]]
}
// optimized
function bubbleSort(array) {
  let countOuter = 0
  let countInner = 0
  let countSwap = 0

  let swapped
  do {
    countOuter++
    swapped = false
    for(let i = 0; i < array.length; i++) {
      countInner++
      if(array[i] && array[i + 1] && array[i] > array[i + 1]) {
        countSwap++
        swap(array, i, i + 1)
        swapped = true
      }
    }
  } while (swapped)

  console.log('outer:', countOuter, 'inner:', countInner, 'swap:', countSwap);
  return array;
}

bubbleSort([9, 2, 5, 6, 4, 3, 7, 10, 1, 8])
bubbleSort([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
bubbleSort([10, 9, 8, 7, 6, 5, 4, 3, 2, 1])
```

### Exercise: Merge Sort

```js
function merge(left, right) {
  const result = []
  while(left.length && right.length) {
    if(left[0] < right[0]) {
      result.push(left.shift())
    } else {
      result.push(right.shift())
    }
  }
  return result.concat(left, right)
}

function mergeSort(list) {
  if(list.length < 2) return list
  const length = list.length
  const middle = Math.floor(length / 2)
  const left = list.slice(0, middle)
  const right = list.slice(middle)
  return merge(mergeSort(left), mergeSort(right))
}

mergeSort([9, 2, 5, 6, 4, 3, 7, 10, 1, 8])
mergeSort([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
mergeSort([10, 9, 8, 7, 6, 5, 4, 3, 2, 1])
```

## Greedy Algorithms

### Introducing Greedy

+ Greedy algorithms always make the locally optimal choice!

例如寻找一个最短路径，使用贪算法，每次都会选择两点之间最短的路径，但实际上这距离可能不是最短的，所以使用贪心算法得出的往往不是最优解。在实际应用时，如果数据过于复杂庞大，那么使用贪心算法得出结果总比没有结果要好。

### Greedy Algorithms Wlakthrough

You are the banker in Monopoly with your family who has lost many of the game pieces so you only have bills in these denominations:

$5 $10 $25

You need only pay out your family in the least number of bills possible so you don't run out before the game is over. Write a function that calculates the least number of bills required for any given dollar amount that is divisible by 5.

```js
// Write a function, makeChange, that returns an integer that represents the least number of coins that add up to an amount where the amount is always divisible by 5.


// coin values: 5, 10, 25

function makeChange(coins, amount) {
  coins.sort((a, b) => b - a)
  let coinTotal = 0
  let i = 0
  while(amount > 0) {
    if (coins[i] <= amount) {
      amount -= coins[i]
      coinTotal++
    } else {
      i++
    }
  }
  return coinTotal
}

makeChange([5, 10, 25], 50);
// input amount: 40 , output # of coins: 3 (25, 10, 5)

// input amount: 35, output # of coins: 2 (25, 10)
```

### Brute Force

+ Would these values work with your greedy solution?
  + coin values: 1, 6, 10
  + input: 12

+ Greedy Approach:
  + Always subtract the largest coin possible from the current amount.

+ Algorithmic Correctness
  + Does your algorithm correctly solve the problem?

+ Brute Force Approach:
  + Calculate every single combination possible and keep track of the minimum.

```js
let recursionCounter = 0
const coins = [10, 6, 1]

function makeChange(value, i) {
  recursionCounter++
  console.log(`${recursionCounter}: calling ${value} at ${i}`)
  if(value === 0) return 0
  let minCoins
  coins.forEach((coin, i) => {
    if(value - coin >= 0) {
      let currMinCoins = makeChange(value - coin, i)
      if(minCoins === undefined || currMinCoins < minCoins) {
        minCoins = currMinCoins
      }
    }
  })
  return minCoins + 1
}

makeChange(10)
```

## Dynamic Algorithms

### Intriducing Dynamic Programming

+ Dynamic Approach:
  + Cache values to avoid repeated calculations

+ DP Qualities:
  + Optimal Substructure (tends to be recursive)
  + Overlapping Subproblems

+ DP vs Divide & Conquer

+ DP Approaches:
  + Top Down (recursive) vs Bottom Up (iterative)

### Memoization with Recursion

```js
const cache = {}
const coins = [10, 6, 1]

function makeChange(c) {
  if(cache[c]) return cache[c]

  let minCoins = -1

  coins.forEach(coin => {
    if(c - coin >= 0) {
      let currMinCoins = makeChange(c - coin)
      if(minCoins === -1 || currMinCoins < minCoins) {
        minCoins = currMinCoins
      }
    }
  })

  cache[c] = minCoins + 1
  return cache[c]
}

makeChange(12)
```

### The Landscape of Data Structure & Algorithms

[Geeks fro Geeks](https://www.geeksforgeeks.org/)

[mooc](http://mooc.org/)

[coursera - princeton](https://www.coursera.org/princeton)

[cs50](https://www.youtube.com/channel/UCcabW7890RKJzL968QWEykA)
