---
title: Introduction to Data Structures for Interviews
date: 2019-09-16 11:56:36
categories: [学习笔记]
tags: [FrontEndMasters, Data Structures, Interview, Algorithms]
---
## Introduction

### Interview Process

[hacker rank](https://www.hackerrank.com/)

+ What to expect

| Step                   | Process                                                                                                                                |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Resume/ Application    | Resume skimmer, manual reading, or submitted by recommendation                                                                         |
| Coding Challenge       | Hacker Rank, create a demo app, add a feature to a demo app                                                                            |
| Reruiter Phone Screen  | Friendly, marketing company, questions about why you are leaving. what you are working on, what you want next, etc                     |
| Technical Phone Screen | Code in a collabedit or google doc. Pair program on adding a feature to a codebase. High-level questions about your domain and project |
| Onsite Interview       | Similar to above except in person and 3-6hrs long, sometimes a bit harder                                                              |
<!-- more -->
+ Interview Skills at Each Step

| Step                   | Looking For                            | Over-achieve                                                                                                 |
| ---------------------- | -------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| Resume/Application     | Gauges relevant experience             | Recommendation from someone                                                                                  |
| Coding Challenge       | Clean, correct code                    | Use a linter, ensure you clearly Understand the problem, add tests and descirptive comments                  |
| Recruiter Phone Screen | Interest in role, company, culture fit | Read the engineering blog and relevant press about the company Ask engaging questions, have highlights ready |
| Technical Phone Screen | Gauge tech ability & fit               | Have a conversational tone, communicate the entire way. Be able to discuss previous projects in-depth        |
| Onsite Interview       | Gauge tech ability & fit               | Know your DS, your language of choice, and be their friend                                                   |

### Common Interview Mistakes

+ Some Reasons you Bombed

| Step                   | Looking For                            | Glaring problem                                                                                     |
| ---------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------- |
| Resume/Application     | Gauges relevant experience             | Relevant experience is buried, grammar issues, no role fit                                          |
| Coding Challenge       | Clean, correct code                    | Messy, unorganized code, didn't solve the challenge, miss details                                   |
| Recruiter Phone Screen | Interest in role, company, culture fit | Communication-based: Offended recruiter, didn't seem interested in company                          |
| Technical Phone Screen | Gauge tech ability & fit               | Inability to describe past project high-level implementation details, not communicative, frustrated |
| Onsite Interview       | Gauge tech ability & fit               | Thought-process was not clear, lack of CS fundamentals, communication                               |

### Data Structure

+ What is data structure, why should you care

+ Common Interview DS

| DS               | Common Uses           | FEM Course       |
| ---------------- | --------------------- | ---------------- |
| Arrays & Strings | ordered data, words   | Part 2(this one) |
| Hash Tables      | optimization          | Part 2(this one) |
| Linked Lists     | data insert/delete    | Part 2(this one) |
| Stacks/Queues    | creative aux DS       | Part 2(this one) |
| Trees, Heaps     | hierarchical data     | Part 3           |
| Graphs           | complex relationships | Part 4           |

### Course Overview

+ What will we cover today?
  1. Background
  2. Implementation
  3. Analysis
  4. Gain experience and intuition

+ How to be effective
  + Rule #1 don't aim to memorize, this will not help
  + Rule #2 find themes, but don't jump to conclusions
  + Rule #3 parctice with a timer, speed matters
  + Rule #3.5 actually practice, reading doesn't count
  + Rule #4 communicate and be nice

## Data Structures Overview

### Types of Data Structures

+ Stacks
+ Queues
+ Linked Lists
+ Hash Tables
+ Arrays
+ Strings

### Stacks & Queues

+ Stack
  + Stores items in a last-in, first-out(LIFO) order
+ Queue
  + Stores items in a first-in, last-out(FILO) order
  + 现代浏览器对unshifit操作进行了优化，传统意义上应该是线性的时间复杂度

+ stack & Queue IRL
  + JavaScript engines have a call stack and message queue that executes your code at runtime
  + When you hit 'undo' in your text editor or 'back' in your browser, you are using a stack

```js
const stack = [1, 2, 3, 4]
stack.push(5) // [1, 2, 3, 4, 5]
stack.pop() // 5

const queue = [1, 2, 3, 4]
queue.enqueue(5) // [1, 2, 3, 4, 5]
queue.edqueue() // 1
```

### Linked List Introduction

+ Linked List
  + Organizes items sequentially, with each item storing a pointer to next one

| Pros                       | Cons           |
| -------------------------- | -------------- |
| Fast operations on the end | Costly lookups |
| Flexible size              |                |

### Linked List Use Cases

[demo](https://visualgo.net/zh/list)

+ Liked List IRL
  + Linked Lists are often the underlying data structure for a stack or queue
  + You can implement a Least Recently Used cache using a linked list
  + Hash tables often use linked lists to handle collisions(more on this later)

```js
const linkedList = {
  head: {
    value: 1,
    next: {
      value: 2,
      next: {
        value: 3,
        next: null
      }
    }
  }
}
```

### Hash Tables

+ Hash Table
  + Organizes data for quick look up on values for a given key

| Pros          | Cons                       |
| ------------- | -------------------------- |
| Fast lookups  | Slow worst-case lookups    |
| Flexible keys | Unordered                  |
|               | Single-directional lookups |

### Hash Tables Use Cases. Arrays & Strings

+ Hash Table IRL(In Real Life)
  + {}
  + Map
  + Set

+ Array
  + Organizes items sequentially in memory

| Pros         | Cons         |
| ------------ | ------------ |
| Fast lookups | Slow insert  |
| Fast appends | Slow deletes |

## Stack Data Stracture

### Overview

+ Common Data Structure Operations

| Data Structure     | Time Complexity |        |           |          |        |        |           |          | Sapce Complexity |
| ------------------ | --------------- | ------ | --------- | -------- | ------ | ------ | --------- | -------- | ---------------- |
|                    | Average         |        |           |          | Worst  |        |           |          | Worst            |
|                    | Access          | Search | Insertion | Deletion | Access | Search | Insertion | Deletion |                  |
| Array              | O(1)            | O(n)   | o(n)      | O(n)     | O(1)   | O(n)   | O(n)      | O(n)     | O(n)             |
| Stack              | O(n)            | O(n)   | O(1)      | O(1)     | O(n)   | O(n)   | O(1)      | O(1)     | O(n)             |
| Queue              | O(n)            | O(n)   | O(1)      | O(1)     | O(n)   | O(n)   | O(1)      | O(1)     | O(n)             |
| Singly-Linked List | O(n)            | O(n)   | O(1)      | O(1)     | O(n)   | O(n)   | O(1)      | O(1)     | O(n)             |
| Doubly-Linked List | O(n)            | O(n)   | O(1)      | O(1)     | O(n)   | O(n)   | O(1)      | O(1)     | O(n)             |
| Hash Table         | n/a             | O(1)   | O(1)      | O(1)     | n/a    | O(n)   | O(n)      | O(n)     | O(n)             |

### Exercise: Stack

```js
/** Class representing a Stack. */

class Stack {

  constructor() {
    this._storage = {}
    this._length = 0
  }
  /*
  * Adds a new value at the end of the
  * stack
  * @param {*} value - the value to push
  */
  push(value) {
    // TODO: Add typechecking and arguments
    this._storage[this._length] = value
    this._length += 1
  }

  /*
  * Removes the value at the end of the stack and returns it
  * @return {*} the last and newest value in the stack
  */
  pop() {
    // What if it is empty
    if(!this._length) { return undefined }
    const last = this._storage[this._length - 1]
    this._storage[this._length - 1] = undefined
    this._length -= 1
    return last
  }
  /*
  * Returns the value at the end of the stack without removing it
  * @return {*} the last and newest value in the stack
  */
  peek() {
    if(!this._length) { return undefined }
    return this._storage[this._length - 1]
  }
}
```

## Queue Data Structure

### Exercise: Queue

```js
/** Class representing a Queue.
 * @constructor
*/

class Queue {

  constructor() {
    this._storage = {};
    this._length = 0
    this._headIndex = 0
  }
  /*
  * Enqueues a new value at the end of the queue
  * @param {*} value the value to enqueue
  */
  enqueue(value) {
    const lastIndex = this._length + this._headIndex
    this._storage[lastIndex] = value
    this._length += 1
  }

  /*
  * Dequeues the value from the beginning of the queue and returns it
  * @return {*} the first and oldest value in the queue
  */
  dequeue() {
    const first = this._storage[this._headIndex]
    delete this._storage[this._headIndex]
    this._length -= 1
    this._headIndex += 1
    return first
  }
  /*
  * Returns the value at the beginning of the queue without removing it from the queue
  * @return {*} the first and oldest value in the queue
  */
  peek() {

  }
}
```

## Linked List Data Structures

### Exercise: Linked List

```js
/** Class representing a Linked List */

class LinkedList {

  constructor(value) {
    this.head = {
      value,
      next: null
    }

    this.tail = this.head
  }
  /*
  * Inserts a new value to the end of the linked list
  * @param {*} value - the value to insert
  */
  insert(value) {
    const node = {value, next: null}
    this.tail.next = node
    this.tail = node
  }
  /*
  * Deletes a node
  * @param {*} node - the node to remove
  * @return {*} value - the deleted node's value
  */
  remove(node) {
    let currentNode = this.head
    while(currentNode.next !== node) {
      currentNode = currentNode.next
    }
    currentNode.next = node.next
  }
  /*
  * Removes the value at the end of the linked list
  * @return {*} - the removed value
  */
  removeTail() {
    let currentNode = this.head
    while(currentNode.next !== this.tail) {
      currentNode = currentNode.next
    }
    this.tail = currentNode
    this.tail.next = null
  }
  /*
  * Searches the linked list and returns true if it contains the value passed
  * @param {*} value - the value to search for
  * @return {boolean} - true if value is found, otherwise false
  */
  contains(value) {
    let currentNode = this.head
    while(currentNode.value !== value) {
      currentNode = currentNode.next
    }
    return currentNode.value === value
  }
  /*
  * Checks if a node is the head of the linked list
  * @param {{prev:Object|null, next:Object|null}} node - the node to check
  * @return {boolean} - true if node is the head, otherwise false
  */
  isHead(node) {
    return node === this.head
  }
  /*
  * Checks if a node is the tail of the linked list
  * @param {{prev:Object|null, next:Object|null}} node - the node to check
  * @return {boolean} - true if node is the tail, otherwise false
  */
  isTail(node) {
    return node === this.tail
  }
}
```

## Hash Table Data Structure

### Exercise: Hash Table

```js
/** Class representing a Hash Table */

class HashTable {

  constructor(val) {
    this._storage = []
    this._size = val
  }
  /*
  * Inserts a new key-value pair
  * @param {string} key - the key associated
  * with the value
  * @param {*} value - the value to insert
  */
  insert(key, value) {
    const index = this._hash(key, this._size)
    if(!this._storage[index]) {
      this._storage[index] = []
    }
    this._storage[index].push([key,value])
  }
  /*
  * Deletes a key-value pair
  * @param {string} key - the key associated with the value
  * @return {*} value - the deleted value
  */
  remove() {

  }
  /*
  * Returns the value associated with a key
  * @param {string} key - the key to search for
  * @return {*} - the value associated with the key
  */
  retrieve(key) {
    const index = this._hash(key, this._size)
    if(this._storage[index]) {
      return this._storage[index].filter(item => item[0] === key)[0][1]
    }
  }
  /*
  * Hashes string value into an integer that can be mapped to an array index
  * @param {string} str - the string to be hashed
  * @param {number} n - the size of the storage array
  * @return {number} - an integer between 0 and n
  */
  _hash(str, n) {
    let sum = 0;
    for (let i = 0; i < str.length; i++)
        sum += str.charCodeAt(i) * 3

    return sum % n;
  }
}
```

## Common Interview Questions
<!-- markdownlint-disable md024-->
### Overview

+ Common Interview Questions

[mooc](http://mooc.org/)

[Cracking the Coding Interview](http://www.crackingthecodinginterview.com/)

[Interview Bit](https://www.interviewbit.com/)

[LeetCode](https://www.interviewbit.com/)

[Geeks for Geeks](https://www.geeksforgeeks.org/)

### Stack & Queue

+ [Use an array to implement 3 stacks](https://repl.it/@bgando/k-stacks-one-array-prompt)
  + [solution](https://repl.it/@bgando/k-stacks-one-array-solution)
+ [Sort a stack with the min values in order, on top. You can use another stack as a buffer.](https://repl.it/@bgando/sort-stack-prompt)
  + [solution](https://repl.it/@bgando/sort-stack-solution)
+ [mplement a getMin() or getMax() method on your stack](https://repl.it/@bgando/min-stack-prompt)
  + [solution](https://repl.it/@bgando/min-stack-solution)
+ [Write a function that returns true if a string of brackets is valid/balanced](https://repl.it/@bgando/bracket-validator-prompt)
  + [solution](https://repl.it/@bgando/bracket-validator-solution)
+ [Create a queue using 2 stacks](https://repl.it/@bgando/queue-two-stacks-prompt)
  + [solution](https://repl.it/@bgando/queue-two-stacks-solution)

### Linked List

+ Delete the follow
  + [a node in the middle of the linked list](https://repl.it/@bgando/ll-delete-mid-node-prompt)
    + [solution](https://www.geeksforgeeks.org/delete-middle-of-linked-list/)
  + [a node with only a variable pointing at that node](https://repl.it/@bgando/ll-delete-node-no-pointers-prompt)
  + [a duplicate](https://repl.it/@bgando/ll-delete-dupe-node-prompt)
    + [solution](https://www.geeksforgeeks.org/remove-duplicates-from-an-unsorted-linked-list/)
+ [Partition a linked list around a value.](https://repl.it/@bgando/ll-partition-prompt)
  + [solution](https://repl.it/@bgando/ll-partition-solution)
+ [Write a function for reversing a linked list.](https://repl.it/@bgando/ll-reverse-prompt)
  + [solution](https://repl.it/@bgando/ll-reverse-solution)
+ [Can you do it in-place?](https://repl.it/@bgando/ll-reverse-prompt)
+ [Check if a linked list contains a cycle.](https://repl.it/@bgando/ll-cycle-detect-prompt)
  + [solution](https://www.geeksforgeeks.org/detect-loop-in-a-linked-list/)
+ [Find the kth to the last node.](https://repl.it/@bgando/ll-kth-to-last-node-prompt)

### Hash Table, Array & String

+ [Count the number of occurrences of all characters or words in a body of text or string.](https://repl.it/@bgando/ht-unique-string-prompt)
  + [solution](https://www.geeksforgeeks.org/determine-string-unique-characters/)
+ [Delete duplicates in a list.](https://repl.it/@bgando/ht-remove-dups-prompt)
  + [solution](https://www.geeksforgeeks.org/remove-duplicates-from-a-given-string/)
+ [Find a unique value in a list.](https://repl.it/@bgando/ht-find-unique-list-prompt)
  + [solution](https://www.geeksforgeeks.org/non-repeating-element/)
+ [Find if two integers in a list add up to k](https://repl.it/@bgando/ht-two-items-sum)
  + [solution](https://www.geeksforgeeks.org/given-an-array-a-and-a-number-x-check-for-pair-in-a-with-sum-as-x/)

+ [Rotate a matrix, string, or an array](https://repl.it/@bgando/rotate-array-prompt)
  + [solution](https://www.geeksforgeeks.org/array-rotation/)
+ [Given an m x n boolean matrix, if an element is 0, set its entire row and column to 0.](https://repl.it/@bgando/boolean-matrix-prompt)
  + [solution](https://www.geeksforgeeks.org/a-boolean-matrix-question/)
+ [Search for a value.](https://repl.it/@bgando/search-for-value-prompt)
  + [solution](https://www.geeksforgeeks.org/linear-search/)
+ [Write a function encodes a string by turning all spaces into `%20`.](https://repl.it/@bgando/encode-string-prompt)
  + [solution](https://www.geeksforgeeks.org/urlify-given-string-replace-spaces/)
+ [Merge two sorted lists into one list.](https://repl.it/@bgando/merge-sorted-arrays-prompt)
  + [solution](https://www.geeksforgeeks.org/merge-two-sorted-arrays/)

## Course Review

### Common Operations

+ Common Data Structure Operations

| Data Structure     | Time Complexity |        |           |          |        |        |           |          | Sapce Complexity |
| ------------------ | --------------- | ------ | --------- | -------- | ------ | ------ | --------- | -------- | ---------------- |
|                    | Average         |        |           |          | Worst  |        |           |          | Worst            |
|                    | Access          | Search | Insertion | Deletion | Access | Search | Insertion | Deletion |                  |
| Array              | O(1)            | O(n)   | o(n)      | O(n)     | O(1)   | O(n)   | O(n)      | O(n)     | O(n)             |
| Stack              | O(n)            | O(n)   | O(1)      | O(1)     | O(n)   | O(n)   | O(1)      | O(1)     | O(n)             |
| Queue              | O(n)            | O(n)   | O(1)      | O(1)     | O(n)   | O(n)   | O(1)      | O(1)     | O(n)             |
| Singly-Linked List | O(n)            | O(n)   | O(1)      | O(1)     | O(n)   | O(n)   | O(1)      | O(1)     | O(n)             |
| Doubly-Linked List | O(n)            | O(n)   | O(1)      | O(1)     | O(n)   | O(n)   | O(1)      | O(1)     | O(n)             |
| Hash Table         | n/a             | O(1)   | O(1)      | O(1)     | n/a    | O(n)   | O(n)      | O(n)     | O(n)             |

+ Array Sorting Algorithms

| Algorithm     | Time Complexity |            |            | Space Complexity |
| ------------- | --------------- | ---------- | ---------- | ---------------- |
|               | Best            | Average    | Worst      | Worst            |
| QuickSort     | Ω(nlog(n))      | θ(nlog(n)) | O(n^2)     | O(log(n))        |
| MergeSort     | Ω(nlog(n))      | θ(nlog(n)) | O(nlog(n)) | O(n)             |
| BubbleSort    | Ω(n)            | θ(n^2)     | θ(n^2)     | O(1)             |
| InsertionSort | Ω(n)            | θ(n^2)     | θ(n^2)     | O(1)             |
| SelectionSort | Ω(n^2)          | θ(n^2)     | θ(n^2)     | O(1)             |

### Data Structures Overview

|                 | Stack/Queue    | Linked List | Array/String    |
| --------------- | -------------- | ----------- | --------------- |
| Reversing       | linear         | linear      | linear          |
| Sorting         | tower of Hanoi | not typical | merge, quik     |
| Traversing      | not typical    | linear      | linear          |
| Merging, sorted | not typical    | linear      | linear          |
| Merging, range  | not typical    | not typical | if sorted, O(n) |
| Interleaving    | not typical    | linear      | linear          |
| Partitioning    | not typical    | linear      | linear          |
| Rotating        | not typical    | linear      | linear          |
| Edit distance   | not typical    | not typical | varies          |
| Shuffling       | not typical    | varies      | varies          |

|                   | Stack/Queue    | Linked List    | Hash Table  | Array/String   |
| ----------------- | -------------- | -------------- | ----------- | -------------- |
| Searching         | linear         | linear         | linear      | linear         |
| Searching, sorted | log(n)         | log(n)         | NA          | log(n)         |
| Min/Max           | stack, O(1)    | stack, O(1)    | stack, O(1) | stack, O(1)    |
| Min/Max, sorted   | in-place, O(1) | in-place, O(1) | NA          | in-place, O(1) |
| Unique            | HT, O(n)       | HT, O(n)       | default     | HT, O(n)       |
| Unique, sorted    | in-place, O(n) | in-place, O(n) | NA          | in-place, O(n) |
| Permutations      |                |                |             |                |

### Other Considerations

+ Things to consider
  + in-place / side effects
  + preserving original order
  + Set vs Map vs Object
  + lengths of 0 and 1
  + off-by-ones
  + optimization vs readability
  + what other information could you ask for?

### Additional Resources

+ On Frontend Masters:
  + Upcoming: Interview Prep course
  + [Brian Holt's CS in 5hrs](https://frontendmasters.com/courses/computer-science/)
+ [Geeks for Geeks](https://www.geeksforgeeks.org)
+ [coding-interview-university](https://github.com/jwasham/coding-interview-university)
+ [Cracking the Coding Interview](http://www.crackingthecodinginterview.com/)
+ [Hacker Rank](https://www.hackerrank.com/) / Leetcode
+ [Interviewing.io](https://interviewing.io/) & [Pramp](https://www.pramp.com/#/)
