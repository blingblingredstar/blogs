---
title: Hardcore Functional Programming in JavaScript
date: 2019-07-12 17:19:20
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, Functional Programming, Monads, Functor, Point-Free]
---

## The Slience

### Introduction

The Soul of Functional Programming: Separation and Recognition

函数式编程的灵魂:分离和识别

The structure of the course

1. The Slience
2. The Voyage
3. The Demo

In the beginning...

+ Anything goes
+ Everything changes
+ Weird names
<!-- more -->
There was primordial soup

```pseudo
10 i = 0
20 i = i + 1
30 PRINT i; " squared = "; i * i
40 IF i >= 10 THEN GOTO 60
50 GOTO 20
60 PRINT "Program Completed."
70 END
```

Discipline Wins

> Exercising restraint while coding feels weird at first, but it's worth it.
  > "The goto statement as it stands is just too primitive; it is too much an invitation to make a mess of one's program."

#### The Symptoms

+ Custom names
+ Looping patterns
+ Glue code
+ Side effects

### Omit Needless Names

Omit needless names with separations and recongnitions.

#### separate inputs from environment

Secret input: time

```js
function daysThisMonth() {
  var date  = new Date()
    , y     = date.getFullYear()
    , m     = date.getMonth()
    , start = new Date(y, m, 1)
    , end   = new Date(y, m + 1, 1);
  return (end - start) / (1000 * 60 * 60 * 24);
}
```

Always works the same

```js
function daysInMonth(y, m) {
  var start = new Date(y, m - 1, 1)
    , end   = new Date(y, m, 1);
  return (end - start) / (1000 * 60 * 60 * 24);
}
```

### Separating Mutation from Calculation

Teaser updates DOM

```js
function teaser(size, elt) {
  setText(elt, slice(0, size, text(elt)));
}

map(teaser(50), all('p'));
```

Merely calculates

```js
var teaser = slice(0);
map(compose(setText, teaser(50), text), all('p'));
```

### Recognize Pure Function

Function that don't change anything are called "pure".

Their purity makes them:

+ testable
+ memoizable
+ portable
+ parallelizable

Let's play pure or impure

```js
function getQueryVariable(variable) { // impure 因为window.loaction
    var query = window.location.search.substring(1);
    var vars = query.split('&');
    for (var i = 0; i < vars.length; i++) {
        var pair = vars[i].split('=');
        if (decodeURIComponent(pair[0]) == variable) {
            return decodeURIComponent(pair[1]);
        }
    }
}
```

```js
function random(m_w, m_z) { // pure
  m_z = 36969 * (m_z & 65535) + (m_z >> 16);
  m_w = 18000 * (m_w & 65535) + (m_w >> 16);
  return (m_z << 16) + m_w;
}
```

```js
function chattyAdd(a, b, console) { // impure 因为console参数
  var c = a+b;
  console.log(a, '+', b, '=', c);
  return c;
}
```

### Separate Functions from Rules

Functions are nouns

Set theoretically

Every function is a single-valued collection of pairs

One input, one output

Separate arity from functions

```js
function get(property, object) {
  return object[property]
}

const people = [{name: ...}, ...]
```

Args up front

```js
function getPersonName(person) {
  return get('name', person)
}

const names = people.map(getPersonName)
```

More args later

```js
// Magic!
const names = people.map(get('name'))
```

#### Curry

```js
function curry(fn) {
  return function() {
    if(fn.length > arguments.length) {
      const slice = Array.prototype.slice,
       args = slice.apply(arguments)

       return function() {
         return fn.apply(null, args.concat(slice.apply(arguments)))
       }
    }
    return fn.apply(null, arguments)
  }
}
```

Our code becomes

```js
const get = curry(function get(property, object) {
  return object[property]
})

const names = people.map(get('name'))
```

### Curring Exercise

```js
// use ramda.js
var _ = R;

/*****************************************
      C U R R Y I N G  E X A M P L E
******************************************/

// We've got a nice multiply function.
// It takes two arguments.

console.log( _.multiply(3, 4) );

// But it has been secretly curried already
// so we can feed it fewer arguments and it
// will return a new function.
//
// How about making a function to double a
// value? Done.
var double = _.multiply(2);

console.log( double(13) );

/*****************************************
               Y O U R  T U R N
******************************************/

// _.split pulls a string apart around a
// given value
console.log( _.split('i', 'mississippi') );

// -- Challenge 1 ------------------------
// Make a function called "words" which
// returns a list of words in a string.
// Use only the split function and
// currying.

console.log("Testing challenge 1...");

var words = _.split(' '); // change this
assertEqualArrays(
  ['one', 'two', 'three'],
  words('one two three')
);
console.log("passed");

// -- Challenge 2 ------------------------
// Create a function to triple every
// number in a list using only
// _.multiply and _.map.

console.log("Testing challenge 2...");

var tripleList = _.map(_.multiply(3));
assertEqualArrays([3,6,9], tripleList([1,2,3]));

console.log("passed");

// -- Challenge 3 ------------------------
// Create a function to find the largest
// number in a list. You can use the
// greater(a,b) function which returns the
// greater of its two inputs. You can do
// this with currying and one of the list
// functions _.map, _.filter, or _.reduce.

console.log("Testing challenge 3...");

var greater = function(a,b) {
  return a > b ? a : b;
};

var max = _.reduce(greater, undefined);
assertEqual(9, max([1,-3483,9,7,2]));
assertEqual(-1, max([-21,-3483,-2,-1]));

console.log("passed");


console.log("All tests pass.");
/******************************************
        B A C K G R O U N D  C O D E
*******************************************/

function assertEqualArrays(x,y) {
  if(x.length !== y.length) throw("expected "+x+" to equal "+y);
  for(var i in x) {
    if(x[i] !== y[i]) {
      throw("expected "+x+" to equal "+y);
    }
  }
}
function assertEqual(x,y){
  if(x !== y) throw("expected "+x+" to equal "+y);
}
```

#### Implment map with reduce

```js
const newmap = _.curry(function(f, list) {
  const concatList = function (acc, element) {
    return acc.concat(f(element))
  }

  return _.reduce(concatList, [], list)
})
```

### Compose

functions can "meld" aka *compose*.

A => f(A) => B => g(B) => C => h(C) => D
A =>       g | f       => C => h(C) => D
A =>         h | (g | f)            => D

Aside: categories

Zooming way out from code

You start seeing similarities

Pure functions are the arrows of a “category”

#### separate composition from args

Javascript code composes functions all the time

But too often it uses unnecessary arguments

example “glue” names

```js
on_error(function(error) {
 log(error.message);
});
```

To compose the logging and message extraction we created a function and a “glue” name called error.
为了组合日志记录和消息提取，我们创建了一个函数，并起了一个“胶水”名称叫error。

#### Simplified compose funciton

```js
function compose(g, f) {
  return function(x) {
    return g(f(x))
  }
}
```

This function does not care what is inside f or g. It produces a new function that pushes a value through.

“olleH” ← “Hello” ← “hello”
compose(reverse, properNoun)

Cut out the middle man

```js
on_error( compose(log, get('message')) );
```

### Composition Exercise

```js
var _ = R;
var get = _.curry(function(x, obj) { return obj[x]; });

/******************************************
    C O M P O S I T I O N  E X A M P L E
******************************************/

// Curried functions are easy to compose.
// Using _.map, _.size, and _.split we can
// make a function that returns the lengths
// of the words in a string.

var lengths = _.compose(
  _.map(_.size), _.split(' ')
);
console.log(lengths('once upon a time'));

/*******************************************
               Y O U R  T U R N
********************************************/

var articles = [
  {
    title: 'Everything Sucks',
    url: 'http://do.wn/sucks.html',
    author: {
      name: 'Debbie Downer',
      email: 'debbie@do.wn'
    }
  },
  {
    title: 'If You Please',
    url: 'http://www.geocities.com/milq',
    author: {
      name: 'Caspar Milquetoast',
      email: 'hello@me.com'
    }
  }
];

// -- Challenge 1 -------------------------
// Return a list of the author names in
// articles using only get, _.compose, and
// _.map.

var names = _.map(
              _.compose(get('name'), get('author'))
            ); // change this
assertEqualArrays(
  ['Debbie Downer', 'Caspar Milquetoast'],
  names(articles)
);

// -- Challenge 2 -------------------------
// Make a boolean function that says whether
// a given person wrote any of the articles.
// Use the names function you wrote above
// with _.compose and _.contains.

// var isAuthor = (name, list) => _.contains(name, names(list)) // change this
var isAuthor = (name, articles) => _.compose(_.contains(name), names)(articles);

assertEqual(  
  false,
  isAuthor('New Guy', articles)
);
assertEqual(
  true,
  isAuthor('Debbie Downer', articles)
);

// -- Challenge 3 -------------------------
// There is more to point-free programming
// than compose! Let's build ourselves
// another function that combines functions
// to let us write code without glue variables.

var fork = _.curry(function(lastly, f, g, x) {
  return lastly(f(x), g(x));
});

// As you can see, the fork function is a
// pipeline like compose, except it duplicates
// its value, sends it to two functions, then
// sends the results to a combining function.
//
// Your challenge: implement a function to
// compute the average values in a list using
// only fork, _.divide, _.sum, and _.size.

var avg = fork(_.divide, _.sum, _.size); // change this
assertEqual(3, avg([1,2,3,4,5]));



console.log("All tests pass.");

/******************************************
        B A C K G R O U N D  C O D E
*******************************************/

function assertEqualArrays(x,y) {
  if(x.length !== y.length)
    throw("expected "+x+" to equal "+y);
  for(var i in x) {
    if(x[i] !== y[i]) {
      throw("expected "+x+" to equal "+y);
    }
  }
}
function assertEqual(x,y){
  if(x !== y)
    throw("expected "+x+" to equal "+y);
}
```

### Point Free

Point means argument. You can think of Point Free means argument free.

就像是如下代码：

```js
var fork = _.curry(function(lastly, f, g, x) {
  return lastly(f(x), g(x));
});

var avg = fork(_.divide, _.sum, _.size)
```

把几个基本函数组合起来返回一个新的函数，在调用新函数时才输入要处理的数据。

### The Slience: Review

Recognize most loops are one of reduce, filter, map

In review

1. Make all function inputs explicit as arguments
2. These arguments can be provided over time, not just all at once
3. Try not to modify outside things
4. Compose without "glue" variables

## The Voyage

### Category Theory

```js
add(1, 1) // => 2
```

```js
// associative
add(add(1, 2), 4) == add(1, add(2, 4))

// commutative
add(4, 1) == add(1, 4)

// identity
add(n, 0) == n

// distributive
multiply(2, add(3,4)) == add(multiply(2, 3), multiply(2, 4))
```

```js
add("ta", "cos")
//=> tacos

add(9.2, 0.5)
//=> 9.7

add([1,2,3], [4,5,6])
//=> [1,2,3,4,5,6]
```

#### Go into Category Theory

compose :: (b -> c) -> (a -> b) -> (a -> c)

id:: a -> a

You need a composition and an identity to from a category

#### Category Laws

```js
// left identity
compose(id, f)  == f

// right identity
compose(f, id)  == f

// associativity
compose(compose(f, g), h)  == compose(f, compose(g, h))
```

### Objects

1. Containers/Wrappers for values
2. No methods
3. Not nouns
4. Probably won’t be making your own often

*think of it this way for now.

```js
var _Container = function(val) {
  this.val = val;
}

var Container = function(x) { return new _Container(x); };


Container(3)
//=> _Container {val: 3}
```

```js
capitalize("flamethrower")
//=> "Flamethrower"


capitalize(Container("flamethrower"))
//=> [object Object]
```

### Object Map

```js
_.Container.prototype.map = function(f) {
  return Container(f(this.val))
}

// Container('flamethrower').map(function(s){return capitalize(s)})
Container('flamethrower').map(capitalize）
// => Container(“Flamethrower”)
```

```js
Container(3).map(add(1)) // => Container(4)

[3].map(add(1)) // => [4]
```

```js
Container([1,2,3]).map(reverse).map(first)
//=> Container(3)


Container("flamethrower").map(length).map(add(1))
//=> Container(13)
```

```js
var map = _.curry(function(f, obj) {
  return obj.map(f)
})

Container(3).map(add(1)) // Container(4)

map(add(1), Container(3)) // Container(4)

var map = _.curry(function(f, obj) {
  return obj.map(f)
})

Container(3).map(add(1)) // Container(4)

map(add(1), Container(3)) // Container(4)
```

```js
map(match(/cat/g), Container("catsup"))
//=> Container([“cat”])

map(compose(first, reverse), Container("dog"))
//=> Container(“g”)
```

### Maybe Functor

Functor: “An object or data structure you can map over”

functions: map

#### Them pesky nulls

```js
var getElement = document.querySelector
var getNameParts = compose(split(' '), get('value'), getElement)

getNameParts('#full_name')
//=> ['Jonathan', 'Gregory', 'Brandis']

getNameParts('#fullname')
//=> Boom!
```

#### Use Maybe Functor

Captures a null check

The value inside may not be there

Sometimes has two subclasses Just / Nothing

Sometimes called Option with subclasses Some/None

```js
const _Maybe.prototype.map = function(func) {
  return this.val ? Maybe(func(this.val)) : Maybe(null)
}

map(capitalize, Maybe('flamethrower')) // Maybe('Flamethrower')
map(capitalize, Maybe(null)) // => Maybe(null)
```

```js
const firstMatch = compose(first, match(/cat/g))

firstMatch('dogsup') // => Boom!
```

```js
const firstMatch = compose(map(first), Maybe, match(/cat/g))

firstMatch('dogsup') // => Maybe(null)
```

### Functor Exercise

[http://jsbin.com/yumog](http://jsbin.com/yumog)

```js
console.clear();
var _ = R;
var P = PointFree;
var map = P.fmap;
var compose = P.compose;
var Maybe = P.Maybe;
var Identity = P.Id;


// Exercise 1
// ==========
// Use _.add(x,y) and map(f,x) to make a function that increments a value inside a functor
console.log("--------Start exercise 1--------")

var ex1 = map(_.add(1))

assertDeepEqual(Identity(3), ex1(Identity(2)))
console.log("exercise 1...ok!")


// Exercise 2
// ==========
// Use _.head to get the first element of the list
var xs = Identity(['do', 'ray', 'me', 'fa', 'so', 'la', 'ti', 'do'])
console.log("--------Start exercise 2--------")

var ex2 = map(_.head)

assertDeepEqual(Identity('do'), ex2(xs))
console.log("exercise 2...ok!")


// Exercise 3
// ==========
// Use safeGet and _.head to find the first initial of the user
var safeGet = _.curry(function(x,o){ return Maybe(o[x]) })
var user = {id: 2, name: "Albert"}
console.log("--------Start exercise 3--------")

var ex3 = compose(map(_.head), safeGet('name'));

assertDeepEqual(Maybe('A'), ex3(user))
console.log("exercise 3...ok!")


// Exercise 4
// ==========
// Use Maybe to rewrite ex4 without an if statement
console.log("--------Start exercise 4--------")

var ex4 = compose(map(parseInt), Maybe)

assertDeepEqual(Maybe(4), ex4("4"))
console.log("exercise 4...ok!")


// TEST HELPERS
// =====================
function inspectIt(x){
  return (x.inspect && x.inspect()) || (x.toString && x.toString()) || x.valueOf(); //hacky for teachy.
}

function assertEqual(x,y){
  if(x !== y){ throw("expected "+x+" to equal "+y); }
}
function assertDeepEqual(x,y){
  if(x.val !== y.val) throw("expected "+inspectIt(x)+" to equal "+inspectIt(y));
}
```

### Either

+ Typically used for pure error handling
+ Like Maybe, but with an error message embedded
+ Has two subclasses: Left/Right
+ Map the function over a Right, ignores the Left

```js
map(x => x + 1, Right(2)) // => Right(3)

map(x => x + 1, Left('Some message')) // => Left('Some message')
```

```js
const determineAge = function (user) {
  return user.age ? Right(user.age) : Left(`Couldn't get age`)
}

const yearOlder = compose(map(add(1)), determineAge)

yearOlder({age: 22}) // => Right(23)
yearOlder({age: null}) // => Left("Couldn't get age")
```

### IO

+ A lazy computation "builder"
+ Typically used to contain side effects
+ You must runIO to perform the operation
+ Map appends the function to a list of things to run with the effectful value

```js
const email_io = IO( () => $('#email').val() )
const msg_io = map(concat('welcome'), email_io)

runIO(msg_io) // => "welcome steve@foodie.net"
```

```js
const getBgColor = compose(get('background-color'), JSON.parse)
const bgPref = compose(map(getBgColor), Store.get('preferences'))

const app = bgPref()

runIO(app)
```

```js
const email_io = IO(() => $('#email').val())

const getValue = (sel => $(sel).val()).toIO()
```

### Either/IO Exercise

```js
console.clear();
var _ = R;
var P = PointFree;
var map = P.fmap;
var compose = P.compose;
var Maybe = P.Maybe;
var Identity = P.Id;

var Either = folktale.data.Either;
var Left = Either.Left;
var Right = Either.Right;
var IO = P.IO.IO;
var runIO = P.IO.runIO;
P.IO.extendFn();


// Exercise 1
// ==========
// Write a function that uses checkActive() and showWelcome() to grant access or return the error
console.log("--------Start exercise 1--------")

var showWelcome = compose(_.add( "Welcome "), _.get('name'))

var checkActive = function(user) {
 return user.active ? Right(user) : Left('Your account is not active')
}

var ex1 = compose(map(showWelcome), checkActive)

assertDeepEqual(Left('Your account is not active'), ex1({active: false, name: 'Gary'}))
assertDeepEqual(Right('Welcome Theresa'), ex1({active: true, name: 'Theresa'}))
console.log("exercise 1...ok!")


// Exercise 2
// ==========
// Write a validation function that checks for a length > 3. It should return Right(x) if it is greater than 3 and Left("You need > 3") otherwise
console.log("--------Start exercise 2--------")

var ex2 = function greaterThan3(x) {
//   return "TODO: write me";
  return x.length > 3 ? Right(x) : Left("You need > 3")
}

assertDeepEqual(Right("fpguy99"), ex2("fpguy99"))
assertDeepEqual(Left("You need > 3"), ex2("..."))
console.log("exercise 2...ok!")


// Exercise 3
// ==========
// Use ex2 above and Either as a functor to save the user if they are valid

var save = function(x){ console.log("SAVED USER!"); return x; }

var ex3 = compose(map(save), ex2)

console.log("--------Start exercise 3--------")
assertDeepEqual(Right("fpguy99"), ex3("fpguy99"))
assertDeepEqual(Left("You need > 3"), ex3("duh"))
console.log("exercise 3...ok!")


// Exercise 4
// ==========
// Get the text from the input and strip the spaces
console.log("--------Start exercise 4--------")

var getValue = function(x){ return document.querySelector(x).value }.toIO()
var stripSpaces = function(s){ return s.replace(/\s+/g, ''); }

var ex4 = compose(map(stripSpaces), getValue)


assertEqual("honkeytonk", runIO(ex4('#text')))
console.log("exercise 4...ok!")


// Exercise 5
// ==========
// Use getHref() / getProtocal() and runIO() to get the protocal of the page.
var getHref = function(){ return location.href; }.toIO();
var getProtocal = compose(_.head, _.split('/'))
var ex5 = compose(map(getProtocal), getHref)

console.log("--------Start exercise 5--------")
assertEqual('https:', runIO(ex5(null)))
console.log("exercise 5...ok!")


// Exercise 6*
// ==========
// Write a function that returns the Maybe(email) of the User from getCache(). Don't forget to JSON.parse once it's pulled from the cache so you can _.get() the email

// setup...
localStorage.user = JSON.stringify({email: "george@foreman.net"})

var getCache = function(x){ return Maybe(localStorage[x]); }.toIO();


var getStringEmail = compose( _.get('email'), JSON.parse)
var ex6 = compose(map(map(getEmail)), getCache) // 使用第一个map转换io内的数据，在使用第二个map转换Maybe内的数据

assertDeepEqual(Maybe("george@foreman.net"), runIO(ex6('user')))
console.log("exercise 6...ok!")


// TEST HELPERS
// =====================
function inspectIt(x){
  return (x.inspect && x.inspect()) || (x.toString && x.toString()) || x.valueOf(); //hacky for teachy.
}

function assertEqual(x,y){
  if(x !== y){ throw("expected "+x+" to equal "+y); }
}
function assertDeepEqual(x,y){
  if(x.val !== y.val) throw("expected "+inspectIt(x)+" to equal "+inspectIt(y));
}
```

### Other Functors

#### EventStream

+ An infinite list of results
+ Dual of array
+ Its map is sometimes lazy
+ Calls the mapped function each time an event happens

```js
const id_s = map(e => `#${e.id}`, Bacon.fromEventTarget(document, 'click'))
// => EventStream(String)

id_s.onValue(id => {alert(`you clicked ${id}`)})
```

```js
const id_s = map(e => `#${e.id}`, Bacon.fromEventTarget(document, 'click'))
const elements_s = map(doucment.querySelector, id_s)
// EventStream(Elements)

elements_s.onValue(e => {alert(`The inner html is ${e.innerHTML}`)})
```

```js
var hover_s = Bacon.fromEventTarget(document, "hover")
var element_s = map(compose(document.querySelector, get('id')), hover_s)
var postid_s = map(function(el) { return el.data('post-id') }, element_s)
var future_post_s = map(Api.getProductById, postid_s)
//=> EventStream(Future(Post))

future_post_s.onValue(alert)
```

#### Future

+ Has an eventual value
+ Similar to a promise, but it's "lazy"
+ You must fork it to kick it off
+ It takes a function as it's value
+ Calls the function with it's result once it's there

```js
const makeHtml = post => `<div>${post.title}</div>`,
  page_f = map(makeHtml, http.get('/posts/2'))

page_f.fork(
  err => {throw(err)},
  page => {$('#container').html(page)}
)
```

```js
const makeHtml = title => `<div>${title}</div>`,
  createPage = compose(makeHtml, get('title')),
  page_f = compose(map(createPage), http.get('/posts/2'))

page_f.fork(
  err => { throw(err) },
  page => { $('#container').html(page) }
)
```

```js
const lineCount = compose(length, split(/n\n/)),
  fileLineCount = compose(map(lineCount), readFile)

fileLineCount('mydoc.txt').fork(log, log) // => 34
```

### Other Functors: Exercise

```js
console.clear();
var _ = ramda;
var P = PointFree;
var map = P.fmap;
var compose = P.compose;
var Maybe = P.Maybe;
var Identity = P.Id;
var runIO = P.IO.runIO;
P.IO.extendFn();

// Exercise 1
// ==========
// Use getPost(id) to return a Future of the title of the post ({id: i, title: 'Love them futures'})
console.log("--------Start exercise 1--------")

var ex1 = compose(map(_get('title')), getPost)

ex1(3).fork(log, function(title){
  assertEqual('Love them futures', title)
  console.log("exercise 1..ok!")
})


// Exercise 2
// ==========
// Use ex1 to extend the computation and render the title in a div
console.log("--------Start exercise 2--------")

var render = function(x){ return "<div>"+x+"</div>"; }
var ex2 = compose(map(render), ex1)

ex2(3).fork(log, function(html){
  assertEqual('<div>Love them futures</div>', html)
  console.log("exercise 2...ok!")
})


// Exercise 3
// ==========
// In JSBin, click the "Output" tab to see a div. Click this div to run the test.
// Turn the clicks into a stream of the div's innerHTML
console.log("--------Start exercise 3--------")

var clicks = Bacon.fromEventTarget(document.querySelector("#box"), "click")

//Todo: turn clicks into a stream of the e.target.innerHTML
var htmlClicks = clicks.map(function(e){ return e.target.innerHTML; })

htmlClicks.onValue(function(html){
  assertEqual('<span>CLICK ME</span>', trim(html))
  console.log("exercise 3...ok!")
})


// Exercise 4
// ==========
// Keep the Output tab open. Type into the input to run the test.
// Transform the keydowns into a stream of the input's value
// Then use pureLog() to log it to the console
console.log("--------Start exercise 4--------")

var pureLog = function(x){ console.log(x); return x; }.toIO();
var search_input = document.querySelector("#search")
var keydowns = Bacon.fromEventTarget(search_input, "keydown")

//Todo: turn keydowns into a stream of the logged input's value
var logs = keydowns;

logs.onValue(function(io){
  assertEqual(search_input.value, runIO(io))
  console.log("exercise 4...ok!")
})

// Exercise 5*
// ==========
// Use only safeGet() to safely return the street name

console.log("--------Start exercise 5--------")

var safeGet = _.curry(function(x,o){ return Maybe(o[x]) })
var user = {id: 2, name: "Albert", address: { street: {number: 22, name: 'Walnut St'} } }
var ex5 = compose(map(map(safeGet('name'))), map(safeGet('street')), safeGet('address'));

assertDeepEqual(Maybe(Maybe(Maybe('Walnut St'))), ex5(user))
console.log("exercise 5...ok!")


// TEST HELPERS
// =====================
function inspectIt(x){
  return (x.inspect && x.inspect()) || (x.toString && x.toString()) || x.valueOf(); //hacky for teachy.
}

function assertEqual(x,y){
  if(x !== y){ throw("expected "+x+" to equal "+y); }
}
function assertDeepEqual(x,y){
  if(x.val !== y.val) throw("expected "+inspectIt(x)+" to equal "+inspectIt(y));
}

function log(x){ console.log(x); return x; }

function getPost(i) {
  return new Future(function(rej, res) {
    setTimeout(function(){
      res({id: i, title: 'Love them futures'})  
    }, 300)
  })
}

function getComments(i) {
  return new Future(function(rej, res) {
    setTimeout(function(){
      res(["This class should be illegal", "Monads are like space burritos"])
    }, 300)
  })
}

function trim(x){ return x.replace('/\S{0,}/g', ''); }
```

### Functor Laws & Properties

Recognize map

| Custom names                                       | We see it is map                            |
| -------------------------------------------------- | ------------------------------------------- |
| `[x].map(f) // [f(x)]`                             | `map(f, [x]) //[f(x)]`                      |
| `Maybe(x).attempt(f) // Maybe(f(x))`               | `map(f, Maybe(x)) // Maybe(f(x))`           |
| `Promise(x).then(f) // Promise(f(x))`              | `map(f, Promise(x)) // Promise(f(x))`       |
| `EventStream(x).subscribe(f) // EventStream(f(x))` | map(f, EventStream(x)) // EventStream(f(x)) |

Laws & Properties are useful

```js
// identity
map(id) == id

// composition
compose(map(f), map(g)) == map(compose(f, g))
```

reverse :: String -> String
toArray :: a -> Array a

```js
var toArray = function (x) { return [x] }

compose(toArray, reverse)("bingo")
//=> [ognib]

compose(map(reverse), toArray)("bingo")
//=> [ognib]
```

```js
compose(toArray, compose(toUpper, reverse))("bingo")
//=> [OGNIB]

compose(map(toUpper), map(reverse), toArray)("bingo")
//=> [OGNIB]

compose(map(compose(toUpper, reverse)), toArray)("bingo")
//=> [OGNIB]
```

#### Natural Transformations

nt:: F a -> T a

"Take one functor to another without knowing anything about the values"

maybeToArray :: Maybe a -> Array a

```js
maybeToArray(Maybe(2)) // => [2]

maybeToArray(Maybe(null)) // => []
```

```js
compose(nt, map(f)) == compose(map(f), nt)

compose(maybeToArray, map(add(1)))(Maybe(5)) // [6]

compose(map(add(1)), maybeToArray)(Maybe(5)) // [6]
```

#### Card Game #1

"Make an api call with an id and possibly retrieve a post"

```js
Future(Maybe(Post))
```

#### Card Game #2

"Click a navigation link and insert the corresponding html on the page"

```js
EventStream(IO(Dom))
```

#### Card Game #3

"Submit a signup form & return errors or make an API call that will create a user”

```js
EventStream(Either(Future(User)))
```

### Monads

#### Pointed Functors

of :: a -> F a

aka: pure, return, unit, point

```js
Container.of(split) // Container(split)

Future.of(match(/dubstep/)) // Future(match(/dubstep))

Maybe.of(reverse) // Maybe(reverse)

EventStream.of(replace(/debstep/, 'shoegaze')) // EventStream(replace(/debstep/, 'shoegaze'))
```

#### monads

A monad is a pointed functor with an extra function called mjoin and/or chain

"Nested computations"

functions: mjoin, chain

mjion :: M M a -> M a

chain :: (a -> M b) -> M a -> M b

Pointed Functor + mjoin|chain = Monad

aka: pure, return, unit, point

```js
mjoin(Container(Container(2))) // Container(2)
```

```js
const getTrackingId = compose(Maybe, get('tracking_id')),
  findOrder = compose(Maybe, Api.findOrder),
  getOrderTracking = compose(map(getTrackingId), findOrder)

const renderPage = compose(map(map(renderTemplete)), getOrderTracking)
renderPage(379) // => Maybe(Maybe(Html))
```

```js
const getTrackingId = compose(Maybe, get('tracking_id')),
  findOrder = compose(Maybe, Api.findOrder),
  getOrderTracking = compose(mjoin, map(getTrackingId), findOrder) // 使用mjoin压平Maybe为一层

const renderPage = compose(map(renderTemplete), getOrderTracking)
renderPage(379) // => Maybe(Html)
```

```js
const setSearchInput = (x => ('#input').val(x)).toIO(),
  getSearchTerm = (() => getParam('term', location.search)).toIO()
  initSearchForm = compose(map(setSearchInput), getSearchTerm)

initSearchForm() // => IO(IO(Dom))

map(runIO, initSearchForm())
```

```js
const setSearchInput = (x => ('#input').val(x)).toIO(),
  getSearchTerm = (() => getParam('term', location.search)).toIO()
  initSearchForm = compose(mjoin, map(setSearchInput), getSearchTerm)

initSearchForm() // => IO(Dom)

runIO(initSearchForm())
```

```js
const sendToServer = httpGet('/upload'),
  upLoadFromFile = compose(mjoin, map(sendToServer), readFile)

upLoadFromFile('/tmp/my_file.txt').fork(logErr, alertSuccess)
```

```js
const sendToServer = httpGet('/upload'),
  upLoadFromFile = compose(mjoin, map(sendToServer), mjoin, map(readFlie), askUser)

upLoadFile('What file?').fork(logErr, alertSuccess)
```

```js
const chain = function(f) {
  return compose(mjion, map(f))
}

aka: flatMap, bind
```

```js
const sendToServer = httpGet('/upload'),
  upLoadFromFlie = compose(chain(sendToServer), chain(readFile), askUser)

upLoadFromFile('What file?').fork(logErr, alertSuccess)
```

```js
const sendToServer = httpGet('/upload')

const upLoadFromFile = what => askUser(what).chain(readFile).chain(sendToServer)

upLoadFromFile('What file?').fork(logErr, alertSuccess)
```

```js
const chain = f => compose(mjoin, map(f))

const mjoin = chain(id)
```

### Monads Exercises

```js
console.clear();
var _ = R;
var P = PointFree;
var map = P.fmap;
var mjoin = P.mjoin;
var chain = P.flatMap;
var compose = P.compose;
var Maybe = P.Maybe;
var Identity = P.Id;
var runIO = P.IO.runIO;
P.IO.extendFn();

// Exercise 1
// ==========
// Use safeGet and mjoin or chain to safetly get the street name
console.log("--------Start exercise 1--------")

var safeGet = _.curry(function(x,o){ return Maybe(o[x]) })
var user = {id: 2, name: "Albert", address: { street: {number: 22, name: 'Walnut St'} } }

var ex1 = compose(chain(safeGet('name')), chain(safeGet('street')),safeGet('address'))

assertDeepEqual(Maybe('Walnut St'), ex1(user))
console.log("exercise 1...ok!")


// Exercise 2
// ==========
// Use monads to get the href, then purely log it.

console.log("--------Start exercise 2--------")

var getHref = function(){ return location.href }.toIO();
var pureLog = function(x){ console.log(x); return x; }.toIO();

var ex2 = compose(chain(pureLog),getHref)

assertEqual("https://null.jsbin.com/runner", runIO(ex2(null)))
console.log("exercise 2...ok!")


// Exercise 3
// ==========
// Use monads to first get the Post with getPost(), then pass it's id in to getComments().
console.log("--------Start exercise 3--------")

var ex3 = compose(chain(getComments, _.get('id')), getPost)

ex3(13).fork(log, function(res){
  assertEqual(2, res.length)
  console.log("exercise 3...ok!")
})


// TEST HELPERS
// =====================
function inspectIt(x){
  return (x.inspect && x.inspect()) || (x.toString && x.toString()) || x.valueOf(); //hacky for teachy.
}

function assertEqual(x,y){
  if(x !== y){ throw("expected "+x+" to equal "+y); }
}
function assertDeepEqual(x,y){
  if(x.val !== y.val) throw("expected "+inspectIt(x)+" to equal "+inspectIt(y));
}

function log(x){ console.log(x); return x; }

function getPost(i) {
  return new Future(function(rej, res) {
    setTimeout(function(){
      res({id: i, title: 'Love them futures'})  
    }, 300)
  })
}

function getComments(i) {
  return new Future(function(rej, res) {
    setTimeout(function(){
      res(["This class should be illegal", "Monads are like space burritos"])
    }, 300)
  })
}

function trim(x){ return x.replace('/\S{0,}/g', ''); }
```

#### Monad Laws

```js
compose(mjoin, fmap(g), mjoin, fmap(f))

mcompose(g, f)
```

```js
// left identity
mcompose(M, f)  == f

// right identity
mcompose(f, M)  == f

// associativity
mcompose(mcompose(f, g), h)  == mcompose(f, mcompose(g, h))
```

## The Demo

### Project Setup

Libraries are Evolving

We'll combine the best ones

+ CrossEye / ramda
+ baconjs / bacon.js
+ fantasyland / fantasy-io
+ DrBoolean / pointfree-fantasy
+ folktale / data.either

[github.com/begriffs/immutube](https://github.com/begriffs/immutube)

### Demo

```js
// HELPERS ///////////////////////////////////////////
  var compose = P.compose;
  var map = P.map;
  var log = function(x) { console.log(x); return x; }
  var fork = _.curry(function(f, future) { return future.fork(log, f); })
  var setHtml = _.curry(function(sel, x) { return $(sel).html(x); });
  var listen = _.curry(function (event, target) {
    return bacon.fromEventTarget(target, event);
  });
  var getData = _.curry(function(name, elt) { return $(elt).data(name); });
  var last = function(ar) { return ar[ar.length - 1]; };

  // PURE //////////////////////////////////////////////////
  
  //  api_key :: String
  var api_key = 'AIzaSyAWoa7aqds2Cx_drrrb5FPsRObFa7Dxkfg';

  //+ eventValue :: DomEvent -> String
  var eventValue = compose(_.get('value'), _.get('target'));

  //+ valueStream :: DomEvent -> EventStream String
  var valueStream = compose(map(eventValue), listen('keyup'));

  //+ termToUrl :: String -> URL
  var termToUrl = function(term) {
    return 'https://www.googleapis.com/youtube/v3/search?' +
      $.param({part: 'snippet', q: term, key: api_key});
  };

  //+ urlStream :: DomEvent -> EventStream String
  var urlStream = compose(map(termToUrl), valueStream);

  //+ getInputStream :: Selector -> IO EventStream String
  var getInputStream = compose(map(urlStream), $.toIO());

  //+ render :: Entry -> Dom
  var render = function(e) {
    return $('<li/>', {text: e.snippet.title, 'data-youtubeid': e.id.videoId});
  };

  //+ videoEntries :: YoutubeResponse -> [Dom]
  var videoEntries = compose(map(render), _.get('items'));

  //+ search :: URL -> Future [Dom]
  var search = compose(map(videoEntries), http.getJSON);

  //+ DomElement -> EventStream DomElement
  var clickStream = compose(map(_.get('target')), listen('click'));

  //+ URL -> String
  var idInUrl = compose(last, _.split('/'));

  //+ youtubeLink :: DomElement -> Maybe ID
  var youtubeId = compose(map(idInUrl), Maybe, getData('youtubeid'));

  // IMPURE /////////////////////////////////////////////////////

  getInputStream('#search').runIO().onValue(
    compose(fork(setHtml('#results')), search)
  );

  clickStream(document).onValue(
    compose(map(compose(setHtml('#player'), Player.create)), youtubeId)
  );

});
```
