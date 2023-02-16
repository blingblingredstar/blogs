---
title: JavaScript The Hard Part
date: 2019-04-22 23:20:13
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, 闭包, 高阶函数, 面相对象, 原型机制]
---

此文为学习笔记，来源FrontEndMasters的同名课程，中间加入了个人理解，以备将来复习。
<!--more-->
## 成为世界级工程师的五个原则

1. Analytical problem solving with code
   > 分析问题并用代码解决的能力
2. Technical communication （can I implement your approach just from your explanation）
   > 技术表达交流的能力（我能只通过看你的文档就能实现相应的功能吗）
3. Engineering best practices and approach （Debugging，code structure,patience and reference to documentation)
   > 工程最佳实践和方法(调试、代码结构、耐心和参考文档)
4. Non-technical communication(empathetic and thoughtful communication)
   > 非技术交流(有同理心、有思想的交流)
5. Language and computer science experience
   > 语言和计算机科学经验
其重要性依次排列

## JavaScript的基本原则(Principles of JavaScript)

传统的编译型语言逐行执行代码，现在js使用所谓的[及时汇编(Just In Time Compilation)](https://en.wikipedia.org/wiki/Just-in-time_compilation)，至少是在Chrome编译执行时是这样（v8引擎的特性），但基本上也是逐行解析的。

如果我们自己解析存储数据，转换数据，并且逐行运行代码，那么就可以理解所有的js代码。

js不会初始化函数体内的代码，直到函数被调用。直到函数被调用之前，函数体内的代码没有意义。所以在函数执行前不要进入函数体，永远！线程或进程永远不会再函数调用之前执行函数体内的代码。只有在函数被调用时，线程或进程才会进入函数体内执行函数体内的代码。（所以其实函数是个锦囊？在满足条件前你也不知道锦囊里写了啥，因为锦囊有可能永远用不到，当满足条件，我们就打开锦囊，按照锦囊里的办法处理当前情况，所以是不是闭包也可以跟锦囊联系起来？我们打开一个锦囊，用完就扔了。但是如果锦囊里面有个锦囊，说我有可能用到（被外部引用），而且你得照着外面锦囊里说的啥啥啥来办，那么这两个锦囊都扔不了）。

### 全局执行上下文(global execution context)

我们开始运行代码时，开始创建全局执行上下文。执行上下文是一个比较唬人的名词，但是表示了两个简单的事。

+ 执行线程（一行行的转化并执行代码）
+ 创建内存空间存储变量及数据

这是全局执行上下文，在文件刚刚开始运行时创建，是一个全局环境。

### js中的线程(Thread)

+ Single thread(one thing at a time)
+ Synchronous ececution(for now)

众所周知，js是单线程的，既只有一个线程，也就是只能同时做一件事。那么执行顺序呢？同步进行，既一步一步，顺序执行，不会跳过当前的代码。

### 本地执行上下文(local execution context)

调用函数(running/calling/invoking a functiong)与定义一个函数不同

当我们定义函数时，加上function关键字即可，当我们调用函数时，要在函数体后加上括号。（为了避免每次调用就重新写一遍函数，我们给函数命名一遍重复调用。并且因为不知道何时调用，我们暂时把函数体的代码放在内存中，当调用时直接访问内存，调用内存中的代码即可，所以如果我们想立即执行函数，可以直接把函数包起来，直接加括号调用，就是立即执行函数，应为垃圾回收机制，我们只想执行一次函数，用完就销毁，就可以不给函数命名，这样函数在内存中就没有其他引用，再直接加括号，那么函数执行完之后就会因为没有其他引用被垃圾回收机制销毁，这应该就是匿名立即执行函数）

当我们调用一个函数时，就创建了由以下两点组成的一个新的执行上下文(creat a new execution context comprising)：

1. 执行线程(thread of execution)，在函数内逐行解析代码。
2. 一个本地存储空间(local memory){变量环境(Variable environment)}，其中存储函数中定义的任何内容。

函数在执行结束前，返回的值就是undefined，函数内只定义了操作数据的方法，在执行结束后，才知道经过函数操作的数据内容也就是函数的返回值。函数并不关心输入与输出，函数本身只定义了方法。分析一个函数式，可以通过画图，左侧是线程区，右侧是内存区，代码沿着线程区顺序执行，在内存区存储相应的变量。

函数体内没有函数时，一切都很简单。但是，如果函数内调用了另一个函数，如果递归的调用函数呢？我们怎么追踪他们呢？

通过调用堆栈。

### 调用堆栈(Call Stack)

调用堆栈是一种特殊的数据结构，是一种存储数据信息的存储方式。一种能够使我们追踪到我们现在进行到哪，js现在解析到哪，线程现在执行到哪的特殊数据结构。

调用堆栈遵循先进后出的原则（可以看做一个桶，最先放入的最后拿出），全局执行上下文首先进入调用堆栈 **（在调用堆栈的最上方就是js线程当前执行的环境）** 。通过这个调用堆栈先进后出的这个机制，js解析器不用关心具体执行代码的所在位置，只关心调用堆栈最上方的执行上下文即可。当函数执行完毕时，执行上下文销毁，此时调用堆栈就回到了全局执行上下文。那么，把一个执行上下文添加到调用堆栈怎么说呢，**pushing**。怎么知道函数执行完毕呢（没有return的情况下），当线程执行完函数体内代码({}号内的代码)，会默认return一个undefined（这也是Chrome console中会不显式返回的情况下输出undefined的原因，既隐式返回了undefined）。把一个执行上下文从堆栈中删除怎么说呢，**poping**。

总结一下，当执行一个函数时，你创建了一个新的执行上下文，这个执行上下文包含一个线程，他逐行执行函数体内的代码，此时也开辟了一个局部存储空间(local memory)，用来存储任何不在全局存储空间(global memory)的变量及数据。此时，将这个执行上下文推到调用堆栈的顶部，js就会执行这个函数，执行完毕后，会将这个执行上下文推出调用堆栈，js继续执行调用堆栈顶部的执行上下文（既当前执行上下文的前一个执行上下文）。

这个本地存储空间(local memory)有一个学术化的名称--**可变环境(environment of available variants)**。

当我们定义一个函数时，我们只是在内存中开辟了一段空间存储定义函数的代码，所以如果console.log(函数名)，就会输出定义函数的代码(试验了一下，发现箭头函数和普通函数的输出内容并不一样，说明两者并不同)。

### 三个基本元素

执行上下文(execution context)，线程(thread)，调用堆栈(call stack)

深入了解js执行机制，不是出于理论目的，不是为了求知欲，我们了解这些是因为这些可以帮助我们debug，写一些干净的代码，使我们的想法实现的可能性更高。我们只需要了解其特性，并不需要我们了解具体如何实现，应为这对我们编程并没有影响。

## 函数式编程(Funcitional Programming)

### 纯粹的函数(Pure Functions)

函数式编程是一种编程范式，关于我们构建和思考如何大规模编写代码的方式。一般我们认为代码质量的评判标准是效率和性能，实际上评判代码的好坏更多的是其他开发人员是否能够快速读懂并添加功能。
在过去，最流行的编程范式是面相对象编程(OOP)，函数式编程是另一种构建代码的方式。
函数式编程有两个核心原则：

+ 纯粹的函数（没有副作用）
  > Pure functions(no side effects)
+ 高阶函数
  > Higher order functions - highly valuable tool & often part of the Codesmith interview

纯粹函数追求的是除了函数的return值之外不改变其他任何值，函数中只存在用完即销毁的局部变量，函数内部没有改变(mutating)任何全局变量或不是函数内的变量。当我们改变了任何不属于函数内部的变量，会使得我们测试我们代码更加困难，增加了需要测试的范围。但是当函数除了返回值之外不做任何其他事，那么就可以在测试时只关心返回值得内容。

作为一个软件工程师，我们有函数，所以，Don't repeat yourself.函数出现的原因就是我们想要重复使用相同的功能，我们把他封装起来，通过一个标志来在需要的时候找到并引用它，这样，我们就可以只写一次，可以重复使用多次。这不仅仅是因为我们懒惰，更是为了写出更多可读的代码，可以重用的代码。我们不想要在需要修改同一个功能时修改多处，有了函数，我们只要修改一次。

函数有定义阶段和调用阶段，定义阶段，我们把函数的参数叫为形参(parameter)，就像二元一次方程中的x，是一个未知数。在调用阶段，我们会给函数一个实参(argument)，此时就是二元一次方程中明确告诉了x的值，只需要代入算式即可。所以其实形参只是一个占位符(placeholder)，代表了可能会传入函数的值，我们定义函数如何对占位符进行操作，在传入具体的参数时，我们只需要将具体值带入占位符即可。既然形参是一个站位符，那么我们是不是可以留一个占位符，代表了一些特定的功能，这个功能的返回值目前还不知道，只有调用这个功能时才能确认这个返回值呢？功能的英文就是function，翻译一下，就是我们的返回值是不是可以是一个函数？所以这个占位符代表的不是具体的数据，而是一个功能，这个函数目前没有返回值，因为只是定义没有调用，只有调用时，才能知道返回值。这应该就是高阶函数的本质。

## 回调函数和高阶函数(callbacks and high order functions)

下面我们要进入一个精彩的编程世界。

下面是一个列表

+ I know what a variable is
  > 我知道什么是变量
+ I've creat a function before
  > 我之前写过一个函数
+ I've add a CSS style before
  > 我之前写过一个CSS样式
+ I've implement a sort algorithm(bubble,merge,etc)
  > 我之前完成过一个排序算法（冒泡、并归排序等）
+ I can add a method to an object's prototype
  > 我能在一个对象的原型上添加方法
+ I understand the event loop in JavaScript
  > 我理解JavaScript的事件循环
+ I understand callback functions
  > 我理解回调函数
+ I've built a project in React or Angular
  > 我写过一个React或Angular项目
+ I can handle collisions in hash tables
  > 我可以处理哈希表中的冲突

通过判断列表中所掌握的项目数量，我们可以给自己打一个分数（共九项，一至九分）。

这里提出了一个概念，成为一个初级开发人员相对容易，但是想要成为一个能够自主解决中级或高级问题的开发人员很难，这中间有一个陡峭的学习难度趋势图。
> 我认为既是遵循了帕累托的二八原则，既想要获得百分之八十的成果需要百分之二十的努力，但想要获得剩余百分之二十的成果需要付出百分之八十的努力。

### 学习困难的事

在刚开始，我们只需要学习一些简单的事情即可成为一个”初级“开发者，但是要成为一个”高级开发者“，我们要学习一些难度很大的知识。就像完成一个edX课程并做完所有测试，或者Coursera的课程。当我们遇到了一个难题，我们的大脑会尝试让我们转移我们的注意力，去喝杯咖啡或者什么，但这就是我们成长的过程，必不可少。在我们学习困难的事(hard learning)时我们很容易想到放弃，但学习困难的事让我们成长。

即使我们没有放弃学习困难的事(刚开始我理解为努力学习，但我觉得并不准确，暂时就用学习困难的事理解)，我们仍面临两种选择。

在我们学习困难的事的过程中，例如LeetCode刷题或者看一个复杂项目的官方文档，你会发现你练单词都看不懂，例如LeetCode题意是啥都不明白，webpack的插件(plugin)和loading等概念。我们会做什么，我们可能会花一天时间去研究相关概念，在一天的结束时甚至没有敲一行代码，这就落入了一个**研究者陷阱**。

另一种，Stack Overflow型，我们遇见问题，去社区提问，有人之前遇到过，解决代码是这样，好，复制粘贴。什么？还是有问题？再找一个帖子，帖子里有另一段代码，复制粘贴，看看能不能解决问题。什么？还是不行。好吧，把问题和这两段代码一起发到社区上问问别人。这算是一种**Stack Overflow方法**。此时我们只是努力让程序正常跑起来，但是并不知道具体怎么跑起来的。

这里提出了结对编程的解决方案。在之前的两种情况中（一种我必须了解一切，一种我只是想让他发挥作用）折中的方案。结对编程的核心原则就是自己不写代码，而是由你像与你水平相当的小伙伴解释你的伪代码，让你的小伙伴来负责具体的代码实现。那么，如果想要解释清楚，就必须理解你的伪代码，但是也不需要理解的特别深入，因为你的小伙伴在等待你的解释。你也不能直接开始写代码，你的目的是通过代码逐行说话，把他变成实际的代码。所以你只能在这两种情况的中间（必须了解一切，但永远不会make hands dirty；什么都不懂，但是一直在敲自己不懂的代码来实现功能）。就像飞驰人生里的拉力赛，有一个领航员，一个车手，领航员负责指路，车手负责开车。实际也就是把分析写出伪代码和代码实现交给两个人，这样就可以使得每个人只专注与自己的部分，就像画画先画出轮廓，再填充细节，画轮廓时应该只关心轮廓，画细节时只关心当前部分的细节。而不是注意力来回跳转（话说有人似乎这么画画，但这不是我们选择的方式）。

### 回调和高阶函数练习

> [callbacks](http://csbin.io/callbacks)

```JavaScript
// Type JavaScript here and click "Run Code" or press Ctrl + s
console.log('Hello, world!');


// Challenge 1
// Create a function addTwo that accepts one input and adds 2 to it.
function addTwo(num) {
  return num += 2
}

// To check if you've completed it, uncomment these console.logs!
// console.log(addTwo(3));
// console.log(addTwo(10));


// Challenge 2
// Create a function addS that accepts one input and adds an "s" to it.
function addS(word) {
  return word += 's'
}

// uncomment these to check your work
console.log(addS('pizza'));
console.log(addS('bagel'));


// Challenge 3
/*
Create a function called map that takes two inputs:
1.an array of numbers (a list of numbers)
2.a 'callback' function - a function that is applied to each element of the array (inside of the function 'map')
Have map return a new array filled with numbers that are the result of using the 'callback' function on each element of the input array.
*/
function map(array, callback) {
  const arr = []// 创建一个新数组，最后返回这个数组
  // 遍历传入的数组，并将每一项经callback处理后添加到新数组中
  for(let i = 0; i < array.length; i++){
    arr.push(callback(array[i]))
  }
  return arr// 返回处理过的数组
}

console.log(map([1, 2, 3], addTwo));


// Challenge 4
/*
  The function forEach takes an array and a callback, and runs the callback on each element of the array. forEach does not return anything.
*/
function forEach(array, callback) {
  for(let i = 0;i < array.length; i++){
    array[i] = callback(array[i])
  }
}
// see for yourself if your forEach works!
const arr3 = [1,2,3]
forEach(arr3,addTwo)
console.log(`forEach ${arr3}`)

//--------------------------------------------------
// Extension
//--------------------------------------------------

//Extension 1
/*
  In the first part of the extension, you're going to rebuild map as mapWith. This time you're going to use forEach inside of mapWith instead of using a for loop.
*/
function mapWith(array, callback) {
  const arr = [] // 创建一个新数组
  forEach(array, function(item){// 使用forEach遍历数组
    arr.push(callback(item)) // 遍历时把数组的每一项都经callback处理后添加到新数组中
  })
  return arr // 返回新数组
}

console.log(`mapWith ${mapWith([1, 2, 3], addTwo)}`)
//Extension 2
/*
  The function reduce takes an array and reduces the elements to a single value. For example it can sum all the numbers, multiply them, or any operation that you can put into a function.
    var nums = [4, 1, 3];
    var add = function(a, b) { return a + b; }
    reduce(nums, add, 0);   //-> 8
    0 + 4 = 4 => 4 + 1 = 5 => 5 + 3 = 8
  Here's how it works. The function has an "accumulator value" which starts as the initialValue and accumulates the output of each loop. The array is iterated over, passing the accumulator and the next array element as arguments to the callback. The callback's return value becomes the new accumulator value. The next loop executes with this new accumulator value. In the example above, the accumulator begins at 0. add(0,4) is called. The accumulator's value is now 4. Then add(4, 1) to make it 5. Finally add(5, 3) brings it to 8, which is returned.
*/
function reduce(array, callback, initialValue) {
  let total = initialValue // 创建一个变量，记录最后的结果
  forEach(array,item => { // 遍历输入的数组
    total = callback(item, total) // 计算经callback处理后的当前项和前一项的值，保存在total中
  })
  return total // 返回最终结果
}
var nums = [4, 1, 3];
var add = function(a, b) { return a + b; }
console.log(reduce(nums, add, 0))
//Extension 3
/*
  Construct a function intersection that compares input arrays and returns a new array with elements found in all of the inputs. BONUS: Use reduce!
*/
function intersection(arrays) {
  const arrs = Array.from(arguments) // 先把实参转化为数组
  return reduce(arrs, (previous, next) => { // 遍历实参的每一项，对上次操作的结果和当前项进行操作，得出结果，供下一次循环与下一项一同传入函数进行操作
    const arr = [] // 定义一个变量，存储交集数组
    if (previous.length === 0) return // 因为初始化值是空的，直接跳到下一项，此时previous代表第一项，next是参数第二项
    forEach(next, item => { // 对第二项进行遍历
      if(previous.includes(item)){ // 如果第二项的值包含在第一项中
        arr.push(item) // 把值添加到交集数组
      }
    })
    return arr// 返回交集数组供下一次操作使用，下次执行时，previous为第二项，next为第三项
  },[]) // 返回最终的交集数组
}

console.log(intersection([5, 10, 15, 20], [15, 88, 1, 5, 7], [1, 10, 15, 5, 20]));
// should log: [5, 15]

//Extension 4
function union(arrays) {
  const arrs = Array.from(arguments) // 把实参转化为数组
  // 使用reduce函数，输入这个数组，传入一个callback处理这个数组，返回一个新数组
  return reduce(arrs, (previous, next) => {
    // 传入两个数组，进行去重
    forEach(next, item => {  // 遍历第二个数组，此时previous为空数组，next为实参的第一个数组
      if(!previous.includes(item)) { // 如果在第一个数组中不包含第二个数组的这一项
        previous.push(item) // 就添加到第一个数组中
      }
    })
    return previous// 最终返回一个数组，数组中没有重复的项，下次调用时previous为此数组，next为实参的第二个数组
  },[])
}

// console.log(union([5, 10, 15], [15, 88, 1, 5, 7], [100, 15, 10, 1, 5]));
// should log: [5, 10, 15, 88, 1, 7, 100]

//Extension 5
function objOfMatches(array1, array2, callback) {

}

// console.log(objOfMatches(['hi', 'howdy', 'bye', 'later', 'hello'], ['HI', 'Howdy', 'BYE', 'LATER', 'hello'], function(str) { return str.toUpperCase(); }));
// should log: { hi: 'HI', bye: 'BYE', later: 'LATER' }

//Extension 6
function multiMap(arrVals, arrCallbacks) {

}

// console.log(multiMap(['catfood', 'glue', 'beer'], [function(str) { return str.toUpperCase(); }, function(str) { return str[0].toUpperCase() + str.slice(1).toLowerCase(); }, function(str) { return str + str; }]));
// should log: { catfood: ['CATFOOD', 'Catfood', 'catfoodcatfood'], glue: ['GLUE', 'Glue', 'glueglue'], beer: ['BEER', 'Beer', 'beerbeer'] }

```

### 关于const和let

我们应该尽量使用const定义变量，除非我们知道我们想要改变这个变量的值（重新给这个变量分配新的数据）。

### 广义函数(generalizing functiongs)

这里举了个例子，我们写了一个function，给输入的数组每一项乘以2，返回一个处理后的数组；又写了一个function，给输入数组每一项加上2，返回一个处理后的数组；再写一个，给每一项除以2，返回一个处理后的数组。可以看出，function其他部分都一样，你要新建一个数组，给每一项加上/乘以/除以二，返回一个新数组。

那么，我们就违反了一个编程原则：Don't repeat your self!

### 解构广义函数(deconstructing generalize function)

我们可以把对每一项的处理变成一个函数，但是这个函数是不确定的，需要我们作为参数传入传入，只有在函数执行时，才知道传入的函数是什么，这就是callback。

在上面的例子中，我们把对每一项的处理的部分用一个占位符代替（callback），并在占位符后加括号代表这是一个function（函数），把需要处理的项作为实参传递到函数中。但是在执行这段代码前，这只是一个占位符。

在函数外部，我们定义一个函数，有一个形参，函数体内写入我们想要如何操作这个形参的代码，在这个阶段，函数体内的代码也没有执行，只是进行了定义。

最后，我们调用第一个函数，传入数据和第二个函数。在执行到处理数据的每一项时，每次处理js就执行第二个函数，把每一项作为实参传入第二个参数，返回一个新的值，添加到新数组中。

通过这种方法，我们降低了代码的耦合性，是的代码的复用度更高。

在问答环节，强调了函数的变量名只是一个占位符，并没有具体的值，只有在执行时才会发挥作用。

### 第一等对象(first-class object)

在js中，functions是第一等对象(frist-class objects)，这意味着函数像对象一样，我们可以传递一个对象作为函数的参数，那么，我们当然也可以传递一个函数作为参数。在js中，只有七种数据类型，其中六种是基本数据类型（Number、String、Null、Undefined、Boolean、Symbol）,一种复杂数据类型（Object），Array、Date、RegExp、Function都是Object的一种，是引用类型。传递基本类型是，我们直接传递基本类型的值，传递复杂数据类型或叫引用数据类型时，我们传递的是引用（指针）。

函数作为引用类型，可以直接把指针分配给变量，称作函数表达式，也可以作为其他对象的属性，此时我们一般把这个函数叫做这个对象的方法，函数还可以作为其他函数的参数，这些都是因为函数是一种对象，是一个引用类型，可以把指针进行传递，在需要的时候进行调用。

这就是函数在js中最为美妙的部分。

函数与相对于普通对象，有个一美妙的特性，你可以使用()调用。

### 回调与高阶函数(callbacks and high order functions)

我们传递给外部函数作为参数的函数叫做回调函数，接收这个函数作为参数的外部函数叫做高阶函数（他接受了一个函数，可以在函数内部实现其他函数的功能，所以级别比一个普通函数更高）。

接受一个函数作为参数或者返回一个函数作为输出结果的函数叫做高阶函数。

这只是描述这些函数的一个术语 - 我们把有这些行为（函数作为参数或返回一个函数）的函数叫做高阶函数 - 但是它们与普通函数本质上没有什么不同。

那么，既然高阶函数跟普通函数没有什么不同，为什么要提出这么一个概念呢？

高阶函数简化了我们的代码，使他们更加易读。(So callbacks and high order functions simplify our code and keep it DRY)

通过上面的例子我们可以清晰地看到这一点。

并且，高阶函数有更为强大的应用。(and they do something even powerful)

通过高阶函数，我们可以异步执行代码。(they allow us to run asynchronous code)

## 闭包(closure)

当我们使用括号调用一个函数时，我们创建了一个变量环境(variable environment)，一般称作本地存储空间(local memory)，用于存储函数内定义的变量（本地变量，arguments等）。当函数执行完毕时，函数创建的执行上下文被调用栈推出，变量环境被销毁，**除了函数的返回值**。除了函数的返回值，函数的所创建的其他数据被垃圾回收机制回收删除，释放所占用的内存。那么，有没有一种办法能够在函数调用之前就保存一些数据呢？在一些情况下，我们希望函数能够保存一些数据，而不是每次都创建新的数据并在结束时全部回收（复杂计算的结果，之前运行的次数等情况）。这就是闭包存在的价值，他改变了我们编写代码的方式。这一切都从在一个函数内返回另一个函数开始。

### 练习

```JavaScript
/*
  Create a function createFunction that creates and returns a function. When that created function is called, it should print "hello".
*/
function createFunction() {
  return function() {
    console.log('hello')
  }
}

// UNCOMMENT THESE TO TEST YOUR WORK!
var function1 = createFunction();
function1();


/*
  Create a function createFunctionPrinter that accepts one input and returns a function. When that created function is called, it should print out the input that was used when the function was created.
*/
function createFunctionPrinter(input) {
  return function() {
    console.log(input)
  }
}

// UNCOMMENT THESE TO TEST YOUR WORK!
var printSample = createFunctionPrinter('sample');
printSample();
var printHello = createFunctionPrinter('hello');
printHello();


/*
  Examine the code for the outer function. Notice that we are returning a function and that function is using variables that are outside of its scope.
  Uncomment those lines of code. Try to deduce the output before executing.
*/
function outer() {
  var counter = 0; // this variable is outside incrementCounter's scope
  function incrementCounter () {
    counter ++;
    console.log('counter', counter);
  }
  return incrementCounter;
}

var willCounter = outer();
var jasCounter = outer();

// Uncomment each of these lines one by one.
// Before your do, guess what will be logged from each function call.

willCounter();//1
willCounter();//2
willCounter();//3

jasCounter();//1
willCounter();//4


/*
  Now we are going to create a function addByX that returns a function that will add an input by x.
    var addByTwo = addByX(2);
    addByTwo(1); //should return 3
    addByTwo(2); //should return 4
    addByTwo(3); //should return 5

    var addByThree = addByX(3);
    addByThree(1); //should return 4
    addByThree(2); //should return 5

    var addByFour = addByX(4);
    addByFour(4); //should return 8
    addByFour(10); //should return 14
*/
function addByX(x) {
  return function(y) {
    return x + y
  }
}

var addByTwo = addByX(2);

// now call addByTwo with an input of 1
console.log('addByTwo with 1',addByTwo(1))

// now call addByTwo with an input of 2
console.log('addByTwo with 2',addByTwo(2))


//--------------------------------------------------
// Extension
//--------------------------------------------------
/*
  Write a function once that accepts a callback as input and returns a function. When the returned function is called the first time, it should call the callback and return that output. If it is called any additional times, instead of calling the callback again it will simply return the output value from the first time it was called.
*/
function once(func) {
  let flag = true,
      result = null
  return function(...arg){
    if(flag){
      result = func(...arg)
      flag = false
    }else{
    return result
    }
  }
}

var onceFunc = once(addByTwo);

// UNCOMMENT THESE TO TEST YOUR WORK!
console.log(onceFunc(4));  //should log 6
console.log(onceFunc(10));  //should log 6
console.log(onceFunc(9001));  //should log 6

/*
  Write a function after that takes the number of times the callback needs to be called before being executed as the first parameter and the callback as the second parameter.
*/
function after(count, func) {
  let innerCount = 1
  return function() {
    if(innerCount < count){
      innerCount += 1
    } else {
      func()
    }
  }
}

var called = function() { console.log('hello') };
var afterCalled = after(3, called);

afterCalled(); // -> nothing is printed
afterCalled(); // -> nothing is printed
afterCalled(); // -> 'hello' is printed

/*
  Write a function delay that accepts a callback as the first parameter and the wait in milliseconds before allowing the callback to be invoked as the second parameter. Any additional arguments after wait are provided to func when it is invoked. HINT: research setTimeout();
*/
function delay(func, wait) {
  let timeout
  clearTimeout(timeout)
  timeout = setTimeout(func, wait)
}

delay(function(){
  console.log('hello')
}, 2000)

```

当我们调用一个函数，函数创建一个新的执行上下文，并将其推入调用栈的最顶端，使js优先执行。在执行完毕后，会返回一个返回值，然后函数的执行上下文被推出调用栈，执行上下文被垃圾回收机制回收，这是一个函数的生命周期。

但是，当函数返回一个在函数内部定义的函数时，情况变得稍微复杂起来。

当我们定义一个函数时，为了确保该函数能够访问其外部作用域的变量，js保留内部函数对外部函数变量的引用，此时，我们返回的不仅仅是一个函数体的代码，还有对齐定义时所在执行上下文的引用。所以即使外部执行函数已经执行完毕，但返回了内部函数，及内部函数与外部函数局部变量之间的联系。为了保持这种联系，也因为垃圾回收机制的原理，外部函数的变量数据并不会消失，仍保持着与内部函数的联系。但应为外部函数已经执行完毕，所以除了与内部函数之间的联系，通过其他方式无法找到这些数据。所以闭包的特殊之处在于它不仅仅是一个普通函数，还携带了定义时所在执行上下文（外部函数执行上下文）的数据，而且这个数据是存储在全局执行上下文中，但是除了内部函数外没有其他访问方式的。

这是js中最为美妙，最为深刻的概念！

在闭包中，js会保留内部函数引用的数据，回收其他未被引用的数据。复述一遍，我们得到了不止一个函数，还得到了一些因为被引用而保留下来的数据。

我想，这与引用类型传递的是指针而不是值本身有关系。（垃圾回收机制查看内存中那些数据没有被外部引用并进行回收，当我们执行外部函数时，数据被创建在内存中，外部函数执行完毕后，执行上下文被销毁。但是部分数据因为被内部函数引用，内部函数又因为被外部所引用，而保存下来。但是因为数据在外部函数的引用已经被销毁，只保留了内部函数对其的引用，所以只能通过内部函数来进行访问。）

### 词法作用域(lexical scope)

定义函数时，它会获得一个[[scope]]属性，该属性引用已定义它的本地内存/变量环境
> When a function is defined, it gets a [[scope]] property that references the Local Memory/Variable
Environment in which it has been defined

```JavaScript
function outer() {
  let counter = 0;
  function incrementCounter (){
    counter ++;
  }
  return incrementCounter;
}
let myNewFunction = outer(); // myNewFunction = incrementCounter
myNewFunction();
myNewFunction();
```

当我们调用incrementCounter函数时，会首先查找他自己的本地存储空间（变量环境），然后在[[scope]]的next的属性中查找
> Wherever we call that incrementCounter function - it will always look first in its immediate local
memory (variable environment), and then in the [[scope]] property next before it looks any further up

所以是否是因为函数也是一个对象，对象就有属性和方法，每一个函数都是Function的实例，在实例创建时，会有一个[[scope]]属性，这个属性应该也是一个对象，然后在[[scope]]中有一个next属性，指向了其定义时函数的[[scope]]属性。这样就可以沿着这个原型链来访问其中的数据。

### JavaScript的静态/词法范围(JavaScript static/lexical scoping)

这就是当我们说JavaScript是词法或静态范围时，所表达的意思
我们的词法作用域（函数定义时可以使用的实时数据）决定了函数执行时可以访问的变量以及访问变量的优先顺序，而不是在函数调用时决定可以访问的数据及访问优先级。

> This is what it means when we say JavaScript is lexically or statically scoped
Our lexical scope (the available live data when our function was defined) is what determines our available variables and prioritization at function execution, not where our function is called

当我们定义一个函数时，函数所能访问的数据就会被函数的[[scope]]属性所引用，这样就不会被垃圾回收机制回收，就会被一直保存下来，直到函数被销毁。

所以闭包是一个名词，代表着函数携带的数据，或者叫lexical scope reference。

当我们想访问闭包中的数据时，我们可以通过在内部函数中return来获取数据的指针。

当我们重新执行外部函数时，创建了一个新的执行上下文，返回了一个新的内部函数，如果新的内部函数引用了了新的外部函数执行上下文中的数据，那么显然，是一个新的数据。

在我们定义或者说声明一个函数时，我们给函数创建了一个新的词法作用域，引用了函数可以访问的数据。函数内部如果定义一个函数，同样会创建一个新的词法作用域，当然这个内部函数的词法作用域可以指向外部函数。当我们返回内部函数时，外部函数已经执行完毕，其执行上下文销毁，但是因为内部函数的词法作用域存在着对外部函数局部数据的引用，所以返回内部函数时也会返回一个词法作用域引用，也就是闭包。

### 闭包的力量(The power of Closure)

闭包可以使我们的函数有”记忆“，既可以操作闭包中的数据（私有变量）的内部函数来实现一个计时器或者”自动销毁“（执行一定次数后就不在执行回调函数）的函数。

我们也可应在js中通过闭包实现模块模式。（我们不想污染全局作用域）

> Now: Our functions get 'memories' - once, memoize
> Advanced: We can implement the module pattern in JavaScript

## 异步的JavaScript(Asynchrous JavaScript)

异步是现代JavaScript网络开发的基石
> Asynchronicity is the backbone of modern web development in JavaScript

JavaScript是单线程的（一次执行一个命令），并具有同步执行模型(按照每行代码的顺序执行)
> JavaScript is single threaded (one command executing at a time) and has a synchronous execution model (each line is executed in order the code appears)

通过上面的学习，我们知道，JavaScript是单线程的(single thread)。这就意味着在js执行代码时一般是一行行解析执行。但这就引申出来一个问题，或者说一个需求：如果我们需要等待一段时间在执行一个功能(function)怎么办？

在实际的开发情况中，这是一个常见的需求。例如我们需要等待服务器返回一段数据之后再进行相应的操作，又或者设置一个定时器等。此时，我们就有了一个新的需求，既如何在不阻碍js继续解析执行的前提下，定义一个在一段时间后执行的函数（例如先获取用户数据，再根据用户的数据信息显示对应评论，同时不影响页面加载）。

```JavaScript
function printHello(){
 console.log(“Hello”);
}
setTimeout(printHello,1000);
console.log(“Me first!”);
```

如果了解setTimeout函数的作用，我们知道，上面的代码首先会在控制台打印出Me first!，然后才是Hello。但是这与我们前面所学习的知识并不一致。那么，setTimeout函数的原理是什么，它为什么可以做出不符合我们之前所学习知识的行为？

```JavaScript
function printHello(){
 console.log(“Hello”);
}
setTimeout(printHello,0);
console.log(“Me first!”);
```

分析上面代码，根据我们对setTimeout的函数作用的了解，我们可以知道控制台会首先打印出Me first!，然后是Hello。

以上两种结果的出现，并不是因为我们之前所学是错误的，是因为我们所学习的JavaScript执行模型是不完整的。
> Our previous model of JavaScript execution is insufficient

既然有了新的需求，那么我们就要在JavaScript这个平台上添加新的功能。

在之前的学习中，我们在函数执行时，我们有如下三个组件

+ Thread of execution（执行线程）
+ Memory/variable environment（变量环境）
+ Call stack（调用栈）
  
现在因为新的需求，我们添加了一下三个组件

+ Web Browser APIs/Node background threads（浏览器内置功能 /节点后台线程）
+ Callback/Message queue（回调函数/消息队列）
+ Event loop（事件循环）

### 浏览器内置功能(Web Browser API)

这里，我们首先学习浏览器的API(Application Program Interface)，通过上面代码的执行结果，我们知道，只有之前的三种模型（线程、执行上下文，调用栈）是无法解释这个执行结果的。

让我们逐步分析下面代码的执行过程

```JavaScript
function printHello(){
 console.log(“Hello”);
}
setTimeout(printHello,0);
console.log(“Me first!”);
```

在代码执行最开始，我们首先创建了一个全局执行上下文，并将全局执行上下文推动到调用栈，然后线程执行到函数第一行。

在函数第一行，我们声明了一个函数，将这个函数的指针赋值给printHello这个变量。在声明函数时，同时也给函数创建了一个引用了当前全局作用域的词法作用域引用。

然后代码执行到第二行，我们调用了setTimeout函数，并将printHello变量的值，既我们声明的printHello函数的指针和一个基本类型值0作为两个参数传入。

按照我们之前所学，此时应该创建setTimeout的执行上下文，执行函数内的代码，又因为setTimeout是一个延时执行的函数，所以我们在等待一段时间后返回结果，将执行上下文推出调用栈，线程回到全局执行上下文，执行console.log()。

如果我们按照这个步骤执行，那么显然，代码的执行进程会卡在setTimeout上，等待setTimeout执行完毕后才会进行下一步。

但明显，这段代码并不是如此运行的，如果因为延时函数阻塞了js的进程也与我们的常见需求不符。那么，是因为什么代码才会如此执行呢？

在我们执行js时，js的执行环境中并不只有js，我们还有其他的外部环境，最常见的就是网络浏览器。

所以在js执行时，我们不仅有js，还有Web Browser APIs。这些API不止在js执行时发挥作用，这些功能(feature)还有许多其他功能供浏览器使用。其中一个很重要的工具就是一个计时器(timer)。

因为js是单线程的，为了不阻塞后面代码的执行，js会调用浏览器的API，通过浏览器来创建一个新的线程。此时，浏览器会创建一个后台计时器(background timer)。此时，我们使用的并不是js本身，而是一个浏览器的API。

所以当我们执行setTimeout函数时，并不是像js函数一样的执行过程。此时，setTimeout并不会在js的执行环境中创建执行上下文，而是会调用浏览器API，在浏览器的执行环境内创建一个执行环境，并开始计时。此时js代码调用浏览器api后并不会等待返回结果，而是为了不阻塞代码的执行而直接执行下一行console.log()，在控制台输出”me first“。在浏览器的计时器计时结束后，浏览器会将传入的函数返回到全局执行上下文，并调用函数，创建执行上下文，推入调用栈，执行函数，返回结果后将执行上下文从调用栈中推出。此时执行结果是在控制台打印”hello“。

### 编程练习

```JavaScript
/////////////////
//             //
// CHALLENGE 1 //
//             //
/////////////////
/*
  Write code that will log to the console, 'I am at the beginning of the code'.

  Beneath that console log, set a timer (see setTimeout) that will log to the console 'I am in the setTimeout callback function' after 3 seconds (3000 ms)

  Next, add code to the end of the challenge to log 'I am at the end of the code'. Now re-run the code.

  Make sure the 'console' and 'output' panes are showing (click the tabs above if not) and then run your code with the 'Run with JS' button.

  Clear the console. Change the delay time in the time from 3000 ms to 0. Think hard about how the order should change and then run the code again.
*/
// /* <<<=== Remove the first two slashes (//) to comment out this challenge when finished
console.log('Start of Challenge 1');
// ...your code below
console.log('I am at the beginning of the code')

// setTimeout(() => {
//   console.log('I am in the setTimeout callback function')
// }, 3000) // 会在所有控制台打印完毕后打印。

setTimeout(() => {
  console.log('I am in the setTimeout callback function')
}, 0) // 还是会在所有控制台打印完毕后打印。

console.log('I am at the end of the code')





console.log('End of Challenge 1');
// */// (do not alter this line)




/////////////////
//             //
// CHALLENGE 2 //
//             //
/////////////////

/*
  Write code that will log to the console "Interval Hello!" every 2 seconds (see setInterval). Use the given clearAllIntervals function to clear all the messages when you have this functionality working.

  Next, modify your code so that the "Interval Hello!" messages will automatically stop after 10 seconds.

  Then, modify your code again so that the "Interval Hello!" messages will automatically stop after 10 seconds without use of the clearAllIntervals function, and using clearInterval only once. Perform research if you are unsure how to do this.
*/

// /* <<<=== Remove the first two slashes (//) to comment out this challenge when finished
console.log('Start of Challenge 2');
// ...your code below

const timeId = setInterval(() => {
  console.log('Interval Hello!')
}, 2000) // 会在控制台最后打印

setTimeout(() => {
  clearInterval(timeId)
}, 10000)



// ...your code above
function clearAllIntervals() {
  for (let i = 0; i < 1000; i++) {
    clearInterval(i);
  }
}
console.log('End of Challenge 2');
// */// (do not alter this line)



/////////////////
//             //
// CHALLENGE 3 //
//             //
/////////////////

/*
  Write a function everyXsecsForYsecs that will accept three arguments: a function, an interval time in seconds, and a total time in seconds. everyXsecsForYsecs should invoke the given function every X times 1000 milliseconds, but then stop invoking the function after Y times 1000 milliseconds.

  Write function sayHowdy that will log "Howdy" to the console. Then test your everyXsecsForYsecs function by using it to invoke sayHowdy every 1 second for 5 seconds.

  Now, modify your everyXsecsForYsecs function so it does not use setInterval, but still keeps the same functionality.
*/

// /* <<<=== Remove the first two slashes (//) to comment out this challenge when finished
console.log('Start of Challenge 3');
// ...your code below


function everyXsecsForYsecs(func, intervalTime, totalTime) {
  const timeId = setInterval(func, intervalTime * 500)
  setTimeout(() => {
    clearInterval(timeId)
  }, totalTime * 500)
}

function sayHowdy() {
  console.log('Howdy')
}

everyXsecsForYsecs(sayHowdy, 1, 5)

function everyXsecsForYsecs2(func, intervalTime, totalTime) {
  for(let i = 0; i < totalTime / intervalTime; i++){
    setTimeout(func, intervalTime * 500)
  }
}

everyXsecsForYsecs2(sayHowdy, 1, 5)


console.log('End of Challenge 3');
// */// (do not alter this line)




/////////////////
//             //
// CHALLENGE 4 //
//             //
/////////////////
/*
  Recreate the built in array method, forEach: Write a function that takes as parameters an array, arr, and a callback function, cb. The forEach function will iterate through arr passing each element and its index as arguments to cb.

  Create a variable named delays and assign to it an array with the numbers 2000, 5000, 0, and 3500 (in that order).

  Write a function, delayLog, that takes as input a delayTime and an index, i. When invoked, the function should wait delayTime milliseconds before logging to the console, "printing element i" (with "i" replaced with the actual index passed in).

  Putting it all together, run the delayLog function on each item of the delays array using the forEach function you created.
*/
// /* <<<=== Remove the first two slashes (//) to comment out this challenge when finished
console.log('Start of Challenge 4');
// ...your code below
// part 1
function forEach(arr, callback) {
  if(!Array.isArray(arr)) return
  for(let i = 0; i < arr.length; i++){
    callback(arr[i], i)
  }
}
// part 2
delay = [2000, 5000, 0, 3500]
// part 3
function delayLog(delayTime, index) {
  setTimeout(() => {
    console.log(`print element ${index}`)
  }, delayTime)
}
// part 4
forEach(delay, delayLog)
console.log('End of Challenge 4');
// */// (do not alter this line)



/////////////////
//             //
// CHALLENGE 5 //
//             //
/////////////////
/*
  Write a function changeColor that when invoked will first check if the current page background color is "rgb(221, 238, 255)". If it is, it changes the color to "rgb(255, 238, 221)". If it isn't, it sets the color to "rgb(221, 238, 255)".

  Add a click event listener to button #1 above (it has an id of "activate"). On click, the button should log to the console "clicked #1". It should also set up a click event listener on button #2 (id of "color"). That listener should log to console "clicked #2" and then call the changeColor function you just created.

  Clear the console and hit the 'Run with JS' button. Look at what code has run by analyzing the console. Then try to change the background color by clicking button #2. What needs to happen for the button to work?
*/
// /* <<<=== Remove the first two slashes (//) to comment out this challenge when finished
console.log('Start of Challenge 5');
// ...your code below
// part 1
let background = document.body.style.background
function changeColor() {
  if(background === "rgb(221, 238, 255)"){
    background = "rgb(255, 238, 221)"
  }else{
     background = "rgb(221, 238, 255)"
  }
  console.log(background)
}
// part 2
const activateBtn = document.getElementById("activate")
const colorBtn = document.getElementById("color")
function handleColorBtn() {
  console.log("clicked #2")
  changeColor()
}

function handleActivateBtn() {
  console.log("clicked #1")
  colorBtn.addEventListener('click', handleColorBtn)
}

activateBtn.addEventListener('click', handleActivateBtn)

// ...your code above
document.body.style.background = '#def';
console.log('End of Challenge 3');
// */// (do not alter this line)



/////////////////
//             //
// CHALLENGE 6 //
//             //
/////////////////
/*
  In this challenge we are going to simulate an AJAX call to get information from a server. This is not a real AJAX call, but the asynchonicity is similar.

  Modify the function ajaxSimulate to take an id and a callback function as input. After the database array, set a timer that will pass the element of database whose index matches id to the callback function after 0 ms.

  Create a second function storeData (outside of ajaxSimulate) that takes data as input and assigns it to the dataReceived variable already defined.

  Invoke the ajaxSimulate function with an id of 1 and the storeData function as the callback. Immediately after, log to the console the value of dataReceived. What do you expect it to be?

  Without changing anything else, copy-paste the console.log statement somewhere where it will log with the info we need.
*/
// /* <<<=== Remove the first two slashes (//) to comment out this challenge when finished
console.log('Start of Challenge 6');
var dataReceived;
// part 1
function ajaxSimulate(id, callback) {
  var database = ['Aaron', 'Barbara', 'Chris'];
  setTimeout(() => {
    callback(database[id])
  }, 0)
}
// ...your code below
// part 2
function storeData(data) {
  dataReceived = data
}
// part 3
ajaxSimulate(1, (data) => {
  storeData(data)
  console.log(`part 4 ${dataReceived}`)
})
console.log(`part 3 ${dataReceived}`) // undefined

console.log('End of Challenge 6');
// */// (do not alter this line)



/////////////////
//             //
// CHALLENGE 7 //
//             //
/////////////////
/*
  Perform a GET request to the Bandsintown API (hosted by swaggerhub) (follow this link and then make sure 'UI' is sellected at the upper left, to read how to use their API) to search for your favorite band. For the 'app_id', use the string 'jshp'.

  Then in the returned JSON, find the URL for the image associated with the band and display it in the DOM.

  Make sure to use 'https' for JSBIN to allow the request. There is a div with the ID 'ch2' for you to target. If you want, you can view this div by opening the HTML pane, by clicking the HTML button at the top.
*/
// /* <<<=== Remove the first two slashes (//) to comment out this challenge when finished
console.log('Start of Challenge 7');
// ...your code below
const Http = new XMLHttpRequest() //新建一个异步请求对象
const url = 'https://rest.bandsintown.com/artists/sleepingwithsirens?app_id=jshp' // 把请求地址及请求id保存在url中
Http.open('get', url) // 新建一个get请求
Http.send() // 发起一个请求

Http.onreadystatechange = function() {
  if (this.readyState == 4 && this.status == 200){
    let response = Http.responseText
    dataHandler(JSON.parse(response))
  }
}

function dataHandler(data) {
  $("#ch2").append(`<img src="${data.image_url}"/>`)
}


console.log('End of Challenge 7');
// */// (do not alter this line)



/////////////////
//             //
// CHALLENGE 8 //
//             //
/////////////////

// /* <<<=== Remove the first two slashes (//) to comment out this challenge when finished
console.log('Start of Challenge 8');
// ...your code below






console.log('End of Challenge 8');
// */// (do not alter this line)



/////////////////
//             //
// CHALLENGE 9 //
//             //
/////////////////

// /* <<<=== Remove the first two slashes (//) to comment out this challenge when finished
console.log('Start of Challenge 9');
// ...your code below







console.log('End of Challenge 9');
// */// (do not alter this line)



```

### 回调队列(callback queue)

既然我们调用的是浏览器的api，并且此api与js的执行模型并不一样，那么我们就需要了解此api的执行模型，以便我们预测api执行的行为结果。

当我们执行一个setTimeout或者setInterval时，我们把参数传递给函数后，不会等待方法执行，阻碍线程，而是直接进行下一步。那么，setTimeout或setInterval计时完毕后，什么时候通知js执行对应的函数呢？直接将函数推入调用栈？那这样是不是会影响到当前调用栈的执行？此时我们需要一个工具来解决js执行函数的顺序。

这个工具有两个部分：

1. 回调函数/消息/任务队列(callback/message/task queue)
2. 事件循环(event loop)

#### 任务队列

如果计时器计时结束，我们并不会把回调函数立即推入调用栈，以免扰乱调用栈内函数的执行顺序，所以我们新建一个队列（先进先出），计时结束后，我们就将回调函数推入队列，该任务队列会在js的主线程执行完毕后（调用栈内全部线程执行完成，所有执行上下文被推出，调用栈为空）将队列内的首个函数推入js的调用栈，此时队列内第二个函数成为首个函数，调用栈主线程执行完毕后，再将首个函数推入调用栈，依次循环。

举例：就像一个非常忙的领导(JavaScript)交给属下(Web Browser API)一件事，属下可能立马就办完了，但是领导非常忙，属下不能立马汇报给领导，只能等领导忙完了在汇报。那么，如果有多件事交给属下，属下一件件办好，然后把结果按照办完的顺序记录下来，等领导忙完了，按照记录的顺序一件件汇报给领导。

### 回调队列和事件循环(callback queue & event loop)

对于异步延迟代码的执行，我们有两条规则
> We have two rules for the execution of our asynchronously delayed code

1. 当API“完成”时，在队列(回调队列)中保存每个延迟函数
   > Hold each deferred function in a queue (the Callback Queue) when the API ‘completes’
2. 只有当调用堆栈完全为空时才将函数添加到调用堆栈(即执行函数)(让事件循环检查此条件)
   > Add the function to the Call stack (i.e. execute the function) ONLY when the call stack is totally empty (Have the Event Loop check this condition)

这个异步的线程不是发生在js中的，js是单线程的，js只有依靠网络浏览器提供的api来模拟出异步执行。

事件循环就是一个不断检查js调用栈是否为空，然后检查任务队列是否为空的过程。

所以即使把延时设置为0，传入的回调函数也只能在js主线程全部执行完之后，才会在任务队列中推出回调函数到调用栈中执行。除了setTimeout，我们还有其他方法是按照此模型来进行的。

### 问答环节

是否有办法绕过事件循环？并没有，要按照此模型严格执行，否则无法预测函数的执行顺序。

### 其他浏览器API(introduce more Browser APIs)

在很多情况下，等待会阻塞线程，我们使用浏览器api来代替
> There are many things where waiting would block our thread and we use Browser APIs for instead

+ A timer to finish running(一个计时器)
+ New information from a server (Ajax)（一个服务器请求Ajax）
+ Indication that a portion of the page has loaded（指示页面的一部分已加载）
+ User interaction (clicks, mouseovers, drags)（用户交互(单击、鼠标移动、拖动)）
+ Writing/Reading to File system (Node)（写入/读取文件系统(node.js)）
+ Writing/reading database (Node)（写入/读取数据库(node.js)）

在返回数据时。我们使用的浏览器的API的设计决定了我们如何接受这些数据。
> Some come back with data. The design of the Browser API we are using determines how we access the returned data

在强调一遍，浏览器API并不属于js，是一个native code，使用c++等语言编写的内置于浏览器的功能。Js本身无法进行网络请求，Js通过XMLHttpRequest这个浏览器内置功能来进行网络请求。

所以setTimeout是js与本地浏览器之间的交互功能，XMLHttpRequest是js与其他网络服务器见交换数据的功能（feature）

### XMLHttpRequest

就像setTimeout一样，我们传入的回调函数并不会在XMLHTTPRequest()中执行，而是在XMLHttpRequest方法中，获取到数据后，将回调函数推送到任务队列，在主线程执行完毕后，将回调函数推送到调用栈的最顶端，并将XMLHttpRequest内获取到的数据作为一个实参传递给回调函数。

因为XMLHttpRequest也是一个函数，只不过用其他语言编写，所以传递给回调函数的参数的顺序要查询文档，根据作者文档中的定义顺序来决定我们编写回调函数时形参的顺序。

与setTimeout一样，XMLHttpRequest执行完毕后会将回调函数及获取的数据推入任务队列中，此时事件循环会不断检查js的调用栈是否清空，如果清空将任务队列的第一个回调函数及其参数推送到调用栈最顶端执行。

异步回调函数、Web API、回调队列和事件循环允许我们延迟操作，直到“工作”(API请求、计时器等)完成并同时逐行运行代码

> Asynchronous callbacks, Web APIs, the Callback Queue and Event loop allow us to defer our actions until the ‘work’ (an API request, timer etc) is completed and continue running our code line by line in the meantime

#### 异步JavaScript是现代web的支柱——让我们可以构建快速的“没有阻塞的”应用程序

> Asynchronous JavaScript is the backbone of the modern web - letting us build fast ‘nonblocking’ applications

## 面向对象的JavaScript - 理解面向对象编程(Object-oriented JavaScript – Approaches to OOP)

### 介绍JS中的面向对象(introducing Object-Oriented JavaScript)

 面相对象编程(Object-Oriented Programming)——一种非常流行的用于构造复杂代码的范例
> OOP - an enormously popular paradigm for structuring ourcomplex code

+ easy to add features and functionality(轻松地添加功能并且功能化代码)
+ performant(efficient in terms of memory)(性能更好(在内存方面的效率))
+ easy for us and other developers to reason about(a clear structure)(代码的可读化，语义化更好（结构清晰）)
  
当我们的代码越来越复杂，规模越来越庞大时，我们需要一种结构化的方式来将代码组织起来。面相对象编程范式可以让我们轻松的添加功能。
可以互相引用方法，因为方法存储在内存中，传递的只是一个指针，所以互相引用，而不是在开辟一块内存空间，可以提高内存的使用效率。
而且是其他人更容易推测出代码代表的功能。

我们想要实现一个功能，就要有相应的输入和方法，这样我们把数据和方法提供给计算机，计算机才能根据我们提供的数据和方法计算出结果返回给我们。面相对象编程就是利用这个原理，把计算所需的数据及方法都封装到一起，使用一个对象来组织所需的数据与方法，在需要时调用，提供给计算机进行计算。

此时举了一个例子，我们有一场比赛，希望统计每个人的分数。显而易见，每个人都有名字和分数，然后我们有一些公用的方法，适用于比赛的所有选手（增加得分，减少得分，删除选手，增加选手，增加角色，获取某选手得分。。。）。

对象——可以存储功能和与其相关的数据！
> Objects - store functions with their associated data!

#### 这种将我们所需要的数据和操作这些数据的方法绑定在一起想法，就叫封装。

```JavaScript
let user1 = {
 name: "Will",
 score: 3,
 increment: function() {
 user1.score++;
 }
};
user1.increment(); //user1.score => 4
```

在上述代码开始执行时，我们首先声明了一个变量user，并创建了一个新的对象，将对象的引用赋值给user。然后就进行到了全局执行上下文的下一步，及代码最后一行。首先我们使用了点方法调用了user的increment方法。我们首先找到user，然后查找里面的方法，此时处于全局执行上下文，然后执行方法，首先新建一个执行上下文，然后逐行执行函数体内代码，函数体内，我们使用了点方法访问了user1对象的score属性并自增1。为了执行这段代码，我们会在次执行上下文内寻找user1对象，发现并不存在，然后在调用栈的下一层执行上下文及全局执行上下文中寻找，找到了user1，并在user1内找到了score属性，此时我们给属性值自增1。函数执行完毕，执行上下文推出调用栈，线程回到全局执行上下文，发现并没有进程执行完毕，任务队列也没有需要执行的函数。代码执行完毕。

通过分析，我们把我们需要的数组和方法都组织到了一起，这样我们就可以更加容易的移动代码，而不用在移动时把他们拼凑在一起。我们只需要了解对象的组成结构就可以实现我们想要的功能。

我们有什么其他的技术来创建对象?
> What alternative techniques do we have for creating objects?

使用点方法创建user2
> Creating user2 user 'dot notation'

```JavaScript
let user2 = {}; //create an empty object
user2.name = "Tim"; //assign properties to that object
user2.score = 6;
user2.increment = function() {
 user2.score++;
};
```

使用Object.create()方法创建user3
> Creating user3 using Object.create

```JavaScript
let user3 = Object.create(null);
user3.name = "Eva";
user3.score = 9;
user3.increment = function() {
 user3.score++;
};
```

在这里再重复我们的目标，我们想构建一个应用程序，我想要把我需要的数据和方法组织在其内部。我已经决定了把每个用户封装成一个对象来进行实现。

在上诉代码开始执行时，我们创建了一个空对象(实参null的传入并不影响空对象的创建)，然后我们通过点方法给对象增加属性和方法，并将一些基本类型值赋值给属性，将函数的引用赋值给方法。

在创建了三个对象后可见我们的代码越来越重复，我们打破了枯燥的原则
假设我们有数百万user需要创建!
我们要怎么做?
> Our code is getting repetitive, we're breaking our DRY principle
> And suppose we have millions of users!
> What could we do?

此时我们发现了重复的代码，及代表了一段重复的执行过程，那么，我们可以把这段执行过程封装成函数。

#### 解决方案1：使用函数创建对象

> Solution 1. Generate objects using a function

```javascript
function userCreator(name, score) {
  let newUser = {}
  newUser.name = name
  newUser.score = score
  newUser.increment = function() {
    newUser.score++
  }
  return newUser
}

//later

let user1 = userCreator('Will', 3)
let user2 = userCreator('Tim', 5)
user1.increment()
user2.increment()
```

在上述代码中，我们先是声明了一个函数，将函数保存到内存中并将指针赋值给函数名，然后进程跳转到倒数第四行，
我们声明了user1，并调用了userCreator函数，将”Will“和3作为两个参数传递给userCreator，将执行结果赋值给user1，在执行完毕前，我们会先将undefined赋值给user1。此时执行userCreator。首先创建一个新的执行上下文，并将其推入调用栈顶端，然后执行函数内代码。首先我们在执行上下文中声明了一个newUser变量，并在局部缓存区中创建了一个新的空对象，并将此空对象的指针赋值给newUser变量。然后我们通过点方法给空对象添加了对应的属性及方法，在赋值方法时，在userCreator的缓存区中新开辟了一块空间保存方法的代码。最后，函数执行完毕，返回变量，注意，此时返回的时新建对象的指针。此时将此执行上下文从调用栈中推出，回到全局执行上下文，将函数执行结果也就是返回的对象的引用赋值给user1，此时user1指向了函数执行时创建的对象，因为外部保持了对对象的引用，所以此对象并不会被垃圾回收机制回收。后面的过程与此及上面函数的执行过程类似，并不具体展开。

缺点：
> Problems:

每次创建一个新的user，我们都会在计算机的内存中为所有数据和函数开辟空间。但是我们的函数功能明显是一样的。
有没有更好的方法？
> Each time we create a new user we make space in our computer's memory for all our data and functions. But our functions are just copies
> Is there a better way?

优点：
> Benefits:

简单！
It's simple!

此时谈到了高级开发者面试时会提问什么问题，其中一个经典的问题就是如何设计new关键词，怎么实现类。

#### 代码练习

```javascript
/****************************************************************
                  WORKING WITH OBJECT LITERALS
****************************************************************/
/*** CHALLENGE 1 of 1 ***/
/*
  Create a function that accepts two inputs (name and age) and returns an object. Let's call this function makePerson. This function will:
  create an empty object
  add a name property to the newly created object with its value being the 'name' argument passed into the function
  add an age property to the newly created object with its value being the 'age' argument passed into the function
  return the object
*/
function makePerson(name, age) {
// add code here
  const newPerson = Object.create(null)
  newPerson.name = name
  newPerson.age = age
  return newPerson
}
var vicky = makePerson('Vicky', 24);
// /********* Uncomment these lines to test your work! *********/
console.log(vicky.name); // -> Logs 'Vicky'
console.log(vicky.age); // -> Logs 24
/****************************************************************
                       USING OBJECT.CREATE
****************************************************************/
/*
Challenge 3/3
Without editing the code you've already written, add an introduce method to the personStore object that logs "Hi, my name is [name]".
*/
/*** CHALLENGE 1 of 3 ***/
/*
  Inside personStore object, create a property greet where the value is a function that logs "hello".
*/
var personStore = {
// add code here
  greet: function() {
    console.log('hello')
  }

};
// /********* Uncomment this line to test your work! *********/
personStore.greet(); // -> Logs 'hello'
/*** CHALLENGE 2 of 3 ***/
/*
  Create a function personFromPersonStore that takes as input a name and an age. When called, the function will create person objects using the Object.create method on the personStore object.
*/
function personFromPersonStore(name, age) {
// add code here
  const newPerson = Object.create(personStore)
  newPerson.name = name
  newPerson.age = age
  return newPerson
}
var sandra = personFromPersonStore('Sandra', 26);
// /********* Uncomment these lines to test your work! *********/
console.log(sandra.name); // -> Logs 'Sandra'
console.log(sandra.age); //-> Logs 26
sandra.greet(); //-> Logs 'hello'
/*** CHALLENGE 3 of 3 ***/
/*
  Without editing the code you've already written, add an introduce method to the personStore object that logs "Hi, my name is [name]".
*/
// add code here
personStore.introduce = function() {
  console.log(`Hi, my name is ${this.name}`)
}
sandra.introduce(); // -> Logs 'Hi, my name is Sandra'
/****************************************************************
                    USING THE 'NEW' KEYWORD
****************************************************************/
/*** CHALLENGE 1 of 3 ***/
/*
  Create a function PersonConstructor that uses the this keyword to save a single property onto its scope called greet. greet should be a function that logs the string 'hello'.
*/
function PersonConstructor() {
  // add code here
  this.greet = function() {
    console.log('hello')
  }
}
// /********* Uncomment this line to test your work! *********/
var simon = new PersonConstructor;
simon.greet(); // -> Logs 'hello'
/*** CHALLENGE 2 of 3 ***/
/*
  Create a function personFromConstructor that takes as input a name and an age. When called, the function will create person objects using the new keyword instead of the Object.create method.
*/
function personFromConstructor(name, age) {
  // add code here
  const newPerson = new PersonConstructor
  newPerson.name = name
  newPerson.age = age
  return newPerson
}
var mike = personFromConstructor('Mike', 30);
// /********* Uncomment these lines to test your work! *********/
console.log(mike.name); // -> Logs 'Mike'
console.log(mike.age); //-> Logs 30
mike.greet(); //-> Logs 'hello'
/*** CHALLENGE 3 of 3 ***/
/*
  Without editing the code you've already written, add an introduce method to the PersonConstructor function that logs "Hi, my name is [name]".
*/
// add code here
PersonConstructor.prototype.introduce = function() {
  console.log(`Hi, my name is ${this.name}`)
}
// mike.introduce(); // -> Logs 'Hi, my name is Mike'
/****************************************************************
                        USING ES6 CLASSES
****************************************************************/
/*** CHALLENGE 1 of 3 ***/
/*
  Create a class PersonClass. PersonClass should have a constructor that is passed an input of name and saves it to a property by the same name. PersonClass should also have a method called greet that logs the string 'hello'.
*/
class PersonClass {
  constructor(name) {
    // add code here
    this.name = name
  }
  // add code here
  greet(){
    console.log('hello')
  }
}
// /********* Uncomment this line to test your work! *********/
var george = new PersonClass;
george.greet(); // -> Logs 'hello'

/*** CHALLENGE 2 of 3 ***/
/*
  Create a class DeveloperClass that creates objects by extending the PersonClass class. In addition to having a name property and greet method, DeveloperClass should have an introduce method. When called, introduce should log the string 'Hello World, my name is [name]'.
*/
// add code here
class Developer extends PersonClass{
  constructor(name){
    super(name)
  }
  introduce(){
    console.log(`Hello World, my name is ${this.name}`)
  }
}
// /********* Uncomment these lines to test your work! *********/
// var thai = new DeveloperClass('Thai', 32);
// console.log(thai.name); // -> Logs 'Thai'
// thai.introduce(); //-> Logs 'Hello World, my name is Thai'
/****************************************************************
                      EXTENSION: SUBCLASSING
****************************************************************/
var userFunctionStore = {
  sayType: function() {
    console.log("I am a " + this.type);
  }
}

function userFactory(name, score) {
  let user = Object.create(userFunctionStore);
  user.type = "User";
  user.name = name;
  user.score = score;
  return user;
}

var adminFunctionStore /* Put code here */ ;

function adminFactory(name, score) {
  // Put code here
}

/* Put code here for a method called sharePublicMessage*/

var adminFromFactory = adminFactory("Eva", 5);

// /********* Uncomment these lines to test your work! *********/
// adminFromFactory.sayType() // -> Logs "I am a Admin"
// adminFromFactory.sharePublicMessage() // -> Logs "Welcome users!"
```

#### 解决方案2：

> Solution 2:

我们可以把increment这个方法保存在一个对象中并给使user与其进行引用，如果我们没有在user上找到这个方法，就到这个对象中寻找这个方法。
那么怎么才能实现这个引用？
> Store the increment function in just one object and have the interpreter, if it doesn't find the function on user1, look up to that object to check if it's there
> How to make this link?

#### 充分利用JavaScript的原型特性-解决方案2

> Using the prototypal nature of JavaScript - Solution 2 in full

```javascript
function userCreator (name, score) {
 let newUser = Object.create(userFunctionStore);
 newUser.name = name;
 newUser.score = score;
 return newUser;
};
let userFunctionStore = {
 increment: function(){this.score++;},
 login: function(){console.log("You're loggedin");}
};
let user1 = userCreator("Will", 3);
let user2 = userCreator("Tim", 5);
user1.increment();
```

js中的原型模式并不是实例”继承“了父级构造函数的原型，而是与父级构造函数指向了同一个对象，这个对象就是原型对象。

原型机制的问题是什么？
> Problem
没有问题！漂亮的答案！
> No problems! It's beautiful
就是代码有点长
>Maybe a little long-winded
`let newUser = Object.create(functionStore);`
`...`
`return newUser`

每次要写六个单词！
> Write this every single time - but it's 6 words!
超级复杂也不标准
> Super sophisticated but not standard

#### 解决方案3：

> Solution 3

介绍一个神奇的关键字new
> Introduce magic keyword new

`let user1 = new userCreator("Will", 3)`

当我们调用userCreator(“Will”，3)而没有new关键字时会发生什么?
> What happens when we invoke userCreator("Will", 3) without the new keyword?

就是解决方案2。

当我们使用new关键字调用构造函数时，我们自动完成了两件事
> When we call the constructor function with new in front we automate 2 things

1. 创建了一个新的user对象（Create a new user object）
2. 返回了这个对象（return the new user object）

new关键字自动帮我们完成了许多程序化工作
> The new keyword automates a lot of our manual work

```javascript
function userCreator(name, score) {
/*let newUser = Object.create(functionStore);*/
/*newUser*/ this.name = name;
/*newUser*/ this.score = score;
/*return newUser;*/
};
/*functionStore*/ userCreator.prototype // {};
/*functionStore*/ userCreator.prototype.increment = function(){
 this.score++;
}
let user1 = new userCreator("Will", 3);
```

#### 解决方案3的完整答案：

> Complete Solution 3

```javascript
function User(name, score){
 this.name = name;
 this.score = score;
}
User.prototype.increment = function(){
 this.score++;
};
User.prototype.login = function(){
 console.log("login");
};
let user1 = new User(“Eva”, 9)
user1.increment();
```

优点
> Benefits

+ 写起来更快(Faster to write)
+ 仍然是专业代码中的典型实践(Still typical practice in professional code)

##### 99%的开发人员不知道它是如何工作的，因此无法通过面试

> 99% of developers have no idea how it works and therefore fail interviews

#### 对于构造函数、原型对象的理解

在js高级程序设计中说，当我们使用new关键字时，会自动实行以下4个步骤

1. 创建一个新对象
2. 将构造函数的作用域赋给新对象（this就指向了这个新对象）
3. 执行构造函数中的代码
4. 返回新对象

但是构造函数也是函数，在我们创建一个函数时，会自动给函数添加一个prototype属性，这个属性存储了一个指针，指向了一个对象。

在方案一中，我们发现如果要创建多个对象，但是对象的一部分属性和方法是一样的，那么我们就没有必要为每个对象都开辟一块内存空间保存这些公共的属性和方法。所以在解决方案二中，我们创建了一个对象，来存储我们公共的属性和防范，应为对象是引用类型，在赋值时传递的是指针而不是全部数据，所以我们把存储公共方法的对象的指针赋值给模板函数，这样，每个通过模板函数所创建的新对象，都添加了公共对象的指针，解决了这个问题。但是，这又引申出了一个新的问题，及流程的重复。为了减少重复性的工作，我们设计了一个新的关键字new，通过使用new操作符，我们很大幅度的简化了代码的数量。

这里我们要理解一下prototype原型对象。

出于跟上述解决方案同样的思路，我们没有必要把一些公共的方法在每个实例上都开辟一块内存空间进行存储，这样会极大地浪费我们的资源，那么解决的办法就是新建一个公共的对象，把公共对象的指针传递给实例，这样实例上想调用公共的方法就可以通过这个对象。

我们知道，函数也是一个对象，我们创建的每个函数都是Function对象的一个实例，所以基于以上的解决方案，我们每创建一个新的函数，就给函数添加一个属性，这个属性就指向一个对象，可以用来存储公共的属性和方法，这个属性名就是prototype，我们把这个与每个函数实例联系起来的对象叫做原型对象。

在解决方案二中，我们首先做的就是手动创建一个存储公共方法的新对象，然后把公共方法的对象手动的与函数中创建的对象进行绑定。那么，既然有了原型对象，就可以省略这一步骤。

就像之前所说，在我们使用new关键字后，会自动将函数的作用域赋给新对象。那么，函数的作用域应该包含了prototype属性，存储了指向原型对象的指针。所以在使用new关键字调用函数后，通过函数创建的新对象包含了函数的prototype属性存储的指针，但是在创建的新对象中这个属性名并不叫prototype。ECMA规定中管这个指针叫[[Prototype]]。

众所周知，ECMA只是规范的制定者，具体实现要看各家浏览器厂商。各大厂商在具体的实现中把这个指针命名为__proto__。通过双下划线的命名方式可以看出，这个属性是不期望被我们直接修改的，他只是一个指针，指向了构造函数的prototype属性，通过阅读mdn的文档也支出直接修改__proto__会产生许多问题，首先他只是浏览器厂商约定俗成的一个命名，并不规范所以肯能会被修改，其次还会产生性能为题，还有可能有命名冲突而导致新定义的属性或方法覆盖默认属性或方法的种种问题。

那么，现在我们知道，每通过使用new关键字创建一个新对象，新对象就会有一个属性，指向创建这个函数的prototype属性所指向的对象，也就是通过在函数与实例中各创建一个新属性，指向同一个对象的方法来实现方法及属性的公用。这个对象就是原型对象。函数的prototype属性指向这个对象，函数创建的对象，有一个属性（__proto__）也指向这个对象。

如上所述，不推荐直接在对象上更改__proto__来给这个公共对象添加属性及方法。所以我们一般通过函数的prototype来访问这个原型对象。同时，因为属性保存的只是一个指针，那么我们就不能修改这个指针，不然指向的就是其他对象了。

那么，我们有怎么知道这个原型对象是那个函数创建的呢？为了解决这个问题，回到函数创建时，我们在内存中开辟一块空间创建一个对象，同时给对象一个新属性，这个属性名规定为constructor，保存的时创建这个对象的函数的指针，然后再把这个对象的指针赋值给函数的prototype属性。

这样，因为函数创建的实例中保存着原型对象的指针，所以我们可以在函数创建的实例对象上访问constructor属性，在访问时，js会在实例对象的属性中查找，当查找不到时就会查找__proto__（这个属性名是被隐藏起来不希望我们修改的，但是我们可以访问，来实现方法属性的共享），这个属性保存的是一个指针，指向了与创建实例对象的函数的prototype属性所指向的同一个对象。在函数创建时，这个对象就自动创建了一个constructor，保存了只想这个函数的指针。所以访问实例对象的constructor属性时，经过这样一级级查找，就指向了创建对象的函数。

以上为了明确流程，减少思维负担，我把创建对象的函数就叫做函数，实际上，我们把这种用来创建对象的函数叫做构造函数。在js高级程序设计中强调，使用new关键字调用的函数才是构造函数，为了区分构造函数与普通函数，我们将构造函数的首字母名称大写。所以实际上，首字母大写是为了区分函数作用而约定俗成的，并不会改变函数的作用。在js高级程序设计中也强调，构造函数（用来创建实例对象）的函数直接使用括号调用，就与普通函数没有任何区别。通过解决方案2和解决方案3我们也可以看出：new关键字是为了简化我们创建对象流程而设计出的，如果不使用new关键字就直接调用简化后的函数，当然会导致许多问题。

顺便说一下，this指向当前作用域，我们使用new关键字调用构造函数时，会自动创建一个新对象，并将新对象的作用域绑定在this上，所以此时this可以理解为函数内创建的新对象，函数执行完之后，返回新对象的指针，将指针赋值为变量，此时变量就指向了通过构造函数创建的新对象，既新的实例对象。

理解原型对象，首先要理解函数也是对象，是Function的实例对象。所以函数可以也有属性和方法。每个函数创建时就会创建一个prototype属性，并且指向一个新创建的对象，这个新创建的对象有一个属性constructor，保存着这个函数的指针。所以我们可以手动的在函数内部把函数内新建的对象的一个属性赋值为函数prototype所保存的指针，也可以通过new关键字调用函数简化书写的流程，但是只是简化了书写的流程，过程还是一样的。

使用new关键字的代码大概等同如下

```javascript
function UserCreator(name) {
  this = Object.create(UserCreator.prototype) // 创建一个新对象，并将this指向这个新对象，同时将新对象的__proto__指向函数的原型对象
  this.name = name // 执行构造函数中的代码
  return this // 返回这个新对象
}
```

在js中，我们定义一个‘类‘，需要定义一个构造函数来创建实例对象，在构造函数内部我们实现创建实例对象的私有属性方法，在通过构造函数的原型对象来给所有的实例对象添加公共的属性和方法。

显而易见，为了定义一个’类‘，我们需要最少两段代码，但是其他语言中只需要一段。那么有没有什么办法解决这个问题呢？

#### 解决方案4：

> Solution 4

我们直接在构造函数本身编写公共的属性和方法（而不是在构造函数的原型对象上）
> We’re writing our shared methods separately from our object ‘constructor’ itself (off in the User.prototype object)

其他语言可以让我们把所有内容都写在一起，ES2015的新功能可以使我们也这样写
> Other languages let us do this all in one place. ES2015 lets us do so too

##### class语法糖

> The class 'syntactic sugar'

```javascript
class User {
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
let user1 = new User("Eva", 9);
user1.increment();
```

在前面也提到，构造函数也是普通函数，即使不适用new关键字也可以调用，但显然构造函数的作用与普通函数并不相同，如果不适用new关键字，很可能会产生一些意想不到的效果，也就是bug。为了区分构造函数与普通函数，我们约定了将所有构造函数的首字母大写，但这只是一个人为约定，即使不这样做也能正常使用构造函数。

为了解决代码分离及上述问题，es2015中提出了class解决方案。

通过上面的代码可以看出，我们使用class关键字定义一个类，在后面书写类名，然后使用{}把代码段包裹起来形成一个局部作用域。然后constructor(){}函数就是以前我们书写的构造函数，在这之后我们直接在这个类的作用域中添加公共的属性和方法，也就是之前prototype上添加的属性及方法。

可以看到，使用class与使用构造函数一样需要时会用new调用，使用new调用并传入参数时，会调用class内的constructor方法，并将参数传递给该方法，此时与之前调用构造函数的运行机制一样。

使用class类，我们并不需要深入理解js底层实现机制（对目前来说），就像课程刚开始所讲，我们没必要做一个彻底的researcher，这并不能帮我们写出条理清晰，容易调试，容错率高的代码，或者说帮助不大，但是我们一定到搞清楚它的作用。

js的类实现机制是建立在原型及原型链上的，与其他语言可能并不一致，这是js语言的特性。

优点：
> Benefits:

+ 成为一种新的标准
  > Emerging as a new standard
+ 与其他语言的风格感觉上更一致(例如Python)
  > Feels more like style of other languages (e.g. Python)

缺点：
> Problems

+ 99%的开发人员不知道它是如何工作的，因此无法通过面试
  >99% of developers have no idea how it works and therefore fail interviews

class关键字定义类已经成为新的标准。

## 后记

以上，就是JavaScript: The Hard Part的学习笔记。在强调一遍，以上笔记是我用来总结思考所学内容的，来源于FrontEndMaster的付费课程。内容写的又乱又啰嗦，来源我也不是很确定是否可以转载。笔记只发表在我的个人博客，一般也不会有人来看。如果有人看到学到了，那么我很荣幸。如果看到觉得写得不好，有疏漏，不严谨，逻辑错误或根本性概念错误，那么真的欢迎批评指正。如果想转载，那么请先与我进行沟通，我不想要什么稿费，写得这么乱也不值几个钱，就像知道我的文章到底被谁用了，用在哪了。而且也会与FrontEndMaster那边沟通，看看是否可以转载。