---
title: Deep JavaScript Foundations
date: 2019-04-29 11:42:32
categories: 学习笔记
tags: [FrontEndMasters, JavaScript, 闭包, 高阶函数, 面相对象, 原型机制]
---

## 介绍(Introduction)

我们为什么要深入了解JavaScript？我们现在有了babel，我们有typescript，问什么还要学习JavaScript？

我们为什么要深入了解JavaScript？我们现在有了babel，我们有typescript，问什么还要学习JavaScript？
无论是vue、react、angular，都是建立在js之上的，并不能脱离js的范畴。js有的特性他们也有，js会产生的问题他们也会产生。

我们作为开发人员是有着有倾向性的，假设好的心理模型，当bug出现时，我们有时会责怪编程语言，但这并不是js的问题。js的设计有着复杂的历史原因，显然，更主要的原因使我们没有阅读规范。

[ECMA规范](https://www.ecma-international.org/ecma-262/9.0/index.html#Title)

问题出现时，你首先要找到那么权威的说明在哪里，然后阅读他。

首先应该问自己，规范是这样规定的吗？我所做的是否符合规范？

如果你想要代码按照你所设想的执行，那么唯一的方式就是了解你的代码时怎样执行的。
<!--more-->
这节课的根本目的是了解js的底层原理（算法设计）。这样才能使我们成为一个更加高效的开发人员。如果我们不知道我们所编写的代码的作用，并且只是单纯的希望不要有bug，显然是个不切实际的想法。

这里举了一个例子：你能说出一个职业，一个完全建立在猜测之上的职业吗？

如果我们不了解js的运行机制，代码是如何工作的，而只是看到代码跑起来了，产生了一些结果，那么，我们是不是一个合格的开发人员呢？有的时候，我们会建立一个符合我们预期的心理模型，并且希望js能按照我们的心理模型计算出我们预期的结果。但显然，我们所建立的心理模型很有可能与js的运行模型并不一致，那么，bug的产生也就理所当然了。就像你不能指望一个建筑设计师说我认为这个墙有用，这个房子很结实，让大家住进去吧，看看是不是这样。

一般我们发现问题时，我们一般会在MDN上搜索查看资料，但是MDN并不是规范。所以当MDN无法解释时，我们要阅读规范。我们通过不断阅读规范修正我们的心理模型使其与js工作的模型逐渐一致，这样，我们也就能越来越接近bug free了。

作者认为js可以分为三个核心部分，不论是VUE、ANGULAR、REACT甚至是JQuery，都是js，都建立在以下三个支柱之上：

1. 类型(type)
   + 原始类型(Primitive Type)
   + 抽象操作(Abstract Operations)
   + 强制转换(Coercion)
   + 等于(Equality)
   + 其他js语言(TypeScript,Flow,etc)
2. 作用域(Scope)
   + 作用域范围(Nested Scope)
   + 变量提升(Hoisting)
   + 闭包(Closure)
   + 模块化(Modules)
3. 面相对象(Objects(Oriented))
   + this
   + class{}
   + Prototypes
   + OO vs. OLOO

## 类型(Type)

### 原始类型(Primitive Types)

在js中，一切都是对象。
> "In JavaScript, everything is an object."

这是不正确的说法。

这个说法的来源是js中的绝大部分值可以表现为对象，但他们并不是对象。

也许有许多人都是这样认为的，但是规范上并不是这样。

规范是这样说的：
ECMAScript语言类型对应于由使用ECMAScript语言的ECMAScript程序员直接操作的值。ECMAScript语言类型有Undefined、Null、Boolean、String、Symbol、Number和Object。ECMAScript语言值是以ECMAScript语言类型为特征的值。

> An ECMAScript language type corresponds to values that are directly manipulated by an ECMAScript programmer using the ECMAScript language. The ECMAScript language types are Undefined, Null, Boolean, String, Symbol, Number, and Object. An ECMAScript language value is a value that is characterized by an ECMAScript language type.

就像规范所规定一样，js中有七种原始类型

1. undefined
2. string
3. number
4. boolean
5. object
6. symbol

注意，在这里作者并没有将null放在原始类型中，作者认为null来源于js的历史错误之一，并在后面会解释他这样认为的原因。

在这里作者还举出了一些平时经常被我们误认为原始类型的“类型”

+ undeclared(未声明的变量)
+ null(同上所述，被认为是历史错误)
+ function(不是原始类型，只是Object的“子对象”)
+ array(不是原始类型，只是Object的“子对象”)
+ bigint(即将加入到最新的规范中，已经在chrome v8引擎中实现。[MDN文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/BigInt))

所以除了Object、function以及array，其他并不是“对象”。

在js中，变量没有类型，但是变量的值有类型。
> In JavaScript, variables don't have types, values do.

### typeof操作符

> typeof操作符返回一个字符串，表示未经计算的操作数的类型。

首先来看一段代码

```javascript
var v
typeof v    // "undefined"
v = '1'
typeof v    // "string"
v = 2
typeof v    // "number"
v = true
typeof v    // "boolean"
v = {}
typeof v    // "object"
v = Symbol()
typeof v    // "symbol"
```

当我们使用typeof后加上变量v时，我们判断的不是变量v的类型，而是变量v当前值得类型。通过代码我们可以发现这一点，在我们给变量v重新赋值之后，typeof返回的结果也做出了变化，返回了我们所赋给变量v的值得类型的字符串。

在第二行，我们看到返回了一个undefined，这说明在我们声明一个变量时，会自动给变量赋值为undefined。我们理解undefined一般理解为变量还没有赋值，但我们可以理解为当前还没有值但可能会有。

这里注意，typeof操作符返回的是字符串，所以比较时应该也用字符串。

```javascript
typeof doesnotExist   // "undefined"

var v = null
typeof v              // "object" 注意，这里返回的预期是null，但是是object

v = function(){}
typeof v              // “function” 这里很奇怪，因为预期返回object

v = [1, 2, 3]
typeof v              // "object" 因为数组也是object类型，所以返回object
```

作者在这里认为使用typeof判断null时返回"object"是因为ES1中的历史问题。当然，typeof判断函数时返回"function"可以帮助我们判断是不是函数，但是在判断数组时又失去了作用（现在使用Array.isArray()判断）。作者认为这也是历史原因，并且我们无法进行修复，因为这会导致许多建立与此的代码发生错误。

### Bigint

bigint是规范中即将可能推出的新的原始类型,给一个变量赋值为42n时，代表了不仅仅是数字42，还代表了数字可以无限大，直到填满内存空间。

```javascript
// 即将推出
var v = 42n  //or BigInt(42)
typeof v     // "bigint"
```

### Kind of Emptiness

这里对未声明变量和变量的undefined进行了说明。在js中，变量未声明和变量声明后未赋值完全是两个概念。这也是因为历史原因，可能是不希望因为没有声明变量而阻断程序的运行。

在这里，为了解决这个问题，es6提出了uninitialized(也成为TDZ-the temporal dead zone-暂时性死区)。这个想法的初衷在于变量，例如块级作用域变量，不会进行初始化。一个未声明的变量不会被自动赋值为undefined。在一个未被初始化的作用域中，会被报错。

### NaN & isNaN

NaN是not a number的缩写，顾名思义，应该代表了变量的值不是number类型。但实际上NaN代表了无效数字的值。

```javascript
var myAge = Number('0o46')        // 38
var myNextAge = Number('39')      // 39
var myCatsAge = Number('n/a')     // NaN
myAge - "my son's age"            // NaN

myCatsAge === myCatsAge           // false  注意此处!

isNaN(myAge)                      // false
isNaN(myCatsAge)                  // true
isNaN("my son's age")             // true   注意此处！

Number.isNaN(myCatsAge)           // true
Number.isNaN("my son's age")      // false
```

在数学中，0是一个有意义的数字，但是当我们想给一个数字类型变量赋值，表示这不在是一个数字该怎么办（作者举了猫的例子，因为作者不再养猫，所以作者的猫的年龄不是数字）。在js中，减号表示数学相减运算，js会将符号两边的变量的值强制转换为数字，所以数字减去字符串，得到的是NaN。

在一般情况下，我们不希望使用==，以防止js自动为我们进行类型转换。而是使用===来判断两个变量代表的值是否相等。但是在上述代码中，因为ieee规定NaN不等于NaN自身，所以使用===判断时，即使两边变量代表的值是一样的，还是不相等。所以除了ieee规定的NaN不等于NaN，其他变量都全等于自身。

为了分清楚变量的值是否是NaN，我们有了isNaN运算符。同时要注意的是，在上述代码中我们给isNaN传入了一个字符串，理论上应该返回false，但是在isNaN进行判断前js认为我们传入的是一个数字，会进行强制的类型转换，所以最后判断为true。为了避免这种现象，我们有设计出了Number.isNaN()来避免强制类型转换，所以在最后一行我们传入string类型的值，得到了false。

NaN：无效的数字
> NaN: Invalid Number

### 负零(Negative Zero)

如果你问一个数学家，数学家会告诉你不存在负零，但是在js中是存在的，是存在于ieee 754规范中的。

```javascript
var trendRate = -0
trendRate === -0            // true

trendRate.toString()        // “0”   注意！
trendRate === 0             // true  注意！
trendRate < 0               // false
trendRate > 0               // false

Object.is(trendRate, -0)    // true
Object.is(trendRate, 0)     // false
```

可以看到，当我们把-0转化为字符串是，得到的是"0"，并且-0全等于（===）0。在这里作者也介绍了Object.is(),并将其比喻为====。顺便，Object.is()也可以用来进行比较NaN。

在这里作者也举例了-0的应用实例：如果设计了一款游戏，想要让速度代表汽车行进方向，那么当汽车停止时，我们就需要0和-0来判断汽车停止时的行进方向。

```javascript
Math.sign(-3)       // -1
Math.sign(3)        // 1
Math.sign(-0)       // -0   注意这里！
Math.sign(0)        // 0   注意这里！

// "fix" Math.sign(...)
function sign(v) {
  return v !== 0 ? Math.sign(v) : Object.is(v, 0) ? -1 : 1
}

sign(-3)            // -1
sign(3)             // 1
sign(-0)            // -1
sign(0)             // 1
```

### 基本对象(Fundamental Objects)

基本对象(Fundamental Objects)也称作内置对象(Built-In Objects)或者原生功能(Native Functions)。

在以下功能中，作者建议一定要使用new关键字调用(可以看出，首字母均为大写，说明是一种构造函数)

+ Object()
+ Array()
+ Function()
+ Date()
+ RegExp()
+ Error()

以下是作者不建议使用new关键字的

+ String()
+ Number()
+ Boolean()

## 强制类型转换(Coercion)

### 抽象运算(Abstract Operations)

> 这些操作不是ECMAScript语言的一部分;在这里定义它们仅仅是为了帮助ECMAScript语言的语义规范。在整个规范中定义了其他更专门化的抽象操作。
> These operations are not a part of the ECMAScript language; they are defined here to solely to aid the specification of the semantics of the ECMAScript language. Other, more specialized abstract operations are defined throughout this specification.

这段讲的就是js中的一些列抽象操作，主要就是类型转换(Type Conversion)，我们一般把js的类型转换称为强制类型转换(Coercion)，或隐式类型转换，目前看来，这几个术语表达的应该是同一件事，及js自动进行的一系列操作。

#### ToPrimitive(hint) (7.1.1)

> 抽象操作ToPrimitive接受输入参数和可选参数PreferredType。抽象操作ToPrimitive将其输入参数转换为非Object类型。如果一个对象能够转换为多个基本类型，它可以使用可选提示PreferredType来支持该类型。
> The abstract operation ToPrimitive takes an input argument and an optional argument PreferredType. The abstract operation ToPrimitive converts its input argument to a non-Object type. If an object is capable of converting to more than one primitive type, it may use the optional hint PreferredType to favour that type.

注意，`ToPrimitive`并不是js引擎所实现的一个功能，我们并不能在js中进行调用。这是一个概念性的操作，是一个功能需求的描述，是一个算法描述。这个需求要求了在js中要把一些不是原始类型的值转化为原始类型。

`ToPrimitive`描述了如果你有一个非原始类型的值，你希望他转化成哪种基本类型。就像进行数学运算，会自动调用`ToPrimitive`将运算符两边的值转化为数字类型，但是注意，这并不能保证这个值一定能转化为`Nubmer`类型。

在js的算法设计中，递归是很主要的一部分。例如调用`ToPrimitive`后返回的仍不是一个属于原始类型的值，就会不断调用`ToPrimitive`知道返回一个可以操作的值。

基本上`ToPrimitive`会传入两种参数`number`和`string`，如果`number`，首先会调用`valueOf()`方法进行转换，如果转换失败，会调用`toString()`方法。传入`string`时正好相反，会首先调用`toString()`，然后调用`valueOf()`方法。

#### ToString (7.1.12)

> 抽象操作ToString:根据参数将参数转换为String类型的值
> The abstract operation ToString converts argument to a value of type String

`null      "null"`
`undefined "undefined"`
`true      "true"`
`false     "false"`
`3.14159   "3.14159"`
`0         "0"`
`-0        "0" // 注意，这里返回的与预期有一些出入`

以上是对一些值调用`toString()`方法后的返回结果，在大多数情况下，返回的结果与我们的预期也相符

当接收数组作为参数时，会有如下返回结果

`[]               ""`
`[1,2,3]          "1,2,3"`
`[null,undefined] ","`
`[[[],[],[]],[]]  ",,,"`
`[,,,,]           ",,,"`

可以发现，`toString()`方法传入数组时，会将数组的[]去掉并返回值，当值为null或undefined时，会保留位置但是并没有值，所以在传入数组时返回的结果与我们的预期很不相符。

当接收对象作为参数时，有如下表现：

`{}                             "[object Object]"`
`{a:2}                          "[object Object]"`
`{ toString(){ return "X"; } }  "X"`

#### ToNumber (7.1.3)

> 抽象操作ToNumber将参数转换为类型为Number的值
> The abstract operation ToNumber converts argument to a value of type Number

上面也提到过，当我们想做一件事，这件事需要操作数字时，就会自动调用`ToNumber()`将值转为Number类型。

`""         0`
`"0"        0`
`"-0"      -0`
`"009"      9`
`"3.14159"  3.14159`
`"0."       0`
`".0"       0`
`"."        NaN`
`"0xaf"     175`

通过上面的调用结果，比较值得注意的就是空字符会转为0。

`false      0`
`true       1`
`null       0`
`undefined  NaN`

上面比较值得注意的是null会转化为0，而不是像undefined一样转化为NaN。

在传入数组时，会有以下表现（会先将数组调用toString，所以空数组返回0）

`[""]          0`
`["0"]         0`
`["-0"]       -0`
`[null]        0`
`[undefined]   0`
`[1,2,3]       NaN`
`[[[[]]]]      0`

传入对象时，有如下表现：

`{ .. }                        NaN`
`{ valueOf() { return 3; } }   3`

#### ToBoolean (7.1.2)

> 抽象操作ToBoolean将参数转换为Boolean类型的值[7.1.2ToBoolean ( argument )](https://www.ecma-international.org/ecma-262/9.0/index.html#sec-toboolean)
> The abstract operation ToBoolean converts argument to a value of type Boolean

`Falsy             Truthy`
`“”                “foo”`
`0,-0              23`
`null              { a:1 }`
`NaN               [1,3]`
`false             true`
`undefined         function(){..}  ...`

一般我们只需记住Falsy部分即可，其他不属于Falsy的均转换为true。当我们仅仅只调用`ToBoolean`时并没与其他强制转换发生，所以将一个空数组输入会返回true。

#### 强制类型转换Coercion

在我们学习js时，我们都知道js是一门动态类型语言，会进行类型转换，我们也在极力避免类型转换的发生

> You claim to avoid coercion because it's evil, but...

```js
var numStudents = 16
console.log(`there are ${numStudents} students`) // there are 16 students
```

在上述代码中，我们就进行了类型转换。在这方面，类型转换是的输出结果符合了我们的心理预期。在这里我们使用了类型转换，我们就要了解类型转换帮我们做了什么。

这里还举例了许多其他案例，不一一叙述。

#### Boxing

我们知道我们可以通过.value.length访问一个原始类型值得值得长度。这是因为原始类型值是对象吗？根据规范可知并不是，这是应为在我们通过点方法访问一个非对象的属性时，js使用类型转换帮我们转换为了对象，以防报错阻止程序运行。

## Philosophy of Coercion

### Intentional Coercion

您不需要通过避免强制转换来处理这些类型转换的特殊情况。
> You don't deal with these type conversion corner cases by avoiding coercions.

相反，您必须采用一种使值类型简单明了的编码风格。
> Instead, you have to adopt a coding style that makes value types plain and obvious.

一个高质量的JS程序应该积极拥抱强制类型转换，确保每个操作涉及的类型都是清晰的。因此，边缘案例是处于安全管理下的。
> A quality JS program embraces coercions, making sure the types involved in every operation are clear. Thus, corner cases are safely managed.

JavaScript的动态类型不是缺点，而是它的一个优点
> JavaScript's dynamic typing is not a weakness, it's one of its strong qualities

### Culture of Learning

这里作者对于初级开发者的学习也提出了一些建议。初级开发者会在一些基本问题上犯错误，这很正常。如果一个初级开发者碰到了边缘案例没有发现错误，应该与其讨论边缘案例所发生的情况。一个比较懒惰的观点就是指望团队的每一个人都一直学习，一直进步。但这也是不现实的，我们首先应该确保自身在不断学习进步，更加了解我们使用的工具。代码是一种交流方式。

### Implicit Coercion

隐式类型转换不是魔术，也不是一件坏事，是一个抽象的方法。

我们会隐藏不必要的细节，避免读者注意力的分散，并使读者更清楚所做的事。
> Hiding unnecessary details, re-focusing the reader and increasing clarity

js更好入门的原因之一就是不会强迫开发人员处理各种复杂的类型转换。

这里作者认为问题的关键还是对开发人员的思维负担到底是增加了还是减少了。
> Is showing the reader the extra type details helpful or distracting?

### Understanding Feature

以下是道格拉斯克劳柯福德在js语言精粹中的观点：
> "If a feature is sometimes **useful** and sometimes **dangerous** and if there is a better option then always use the **better** option."
> -- "The Good Parts", Crockford

在这里，作者提出了他的观点：
> Useful: when the reader is focused on what's important(读者能够关注到重点部分)
> Dangerous: when the reader can't tell what will happen(读者不知道发生了什么)
> Better: when the reader understands the code(读者完全理解代码的意图)
作者还认为：
> 故意避免使用可以提高代码可读性的特性是不负责任的
> It is irresponsible to knowingly avoid usage of a feature that can improve code readability

## Equality

### Double & Triple Equals

一般我们把==成为松散比较或者不严格比较，===成为严格比较。
> == checks value (loose)
> === checks value and type (strict)

但事实上可能并不如此。

> If you're trying to understand your code, it's critical you learn to think like JS

这里作者深入的讲述了==和===所表现出的一些行为及其导致的问题。

这是关于==及===的规范链接[ECMA](https://www.ecma-international.org/ecma-262/9.0/index.html#sec-abstract-equality-comparison)。

以下是规范中关于==部分：

> Abstract Equality Comparison
> The comparison x == y, where x and y are values, produces true or false. Such a comparison is performed as follows:
> 1.If Type(x) is the same as Type(y), then
> 1.1.Return the result of performing Strict Equality Comparison x === y.
> 2.If x is null and y is undefined, return true.
> 3.If x is undefined and y is null, return true.
> 4.If Type(x) is Number and Type(y) is String, return the result of the comparison x == ! ToNumber(y).
> 5.If Type(x) is String and Type(y) is Number, return the result of the comparison ! ToNumber(x) == y.
> 6.If Type(x) is Boolean, return the result of the comparison ! ToNumber(x) == y.
> 7.If Type(y) is Boolean, return the result of the comparison x == ! ToNumber(y).
> 8.If Type(x) is either String, Number, or Symbol and Type(y) is Object, return the result of the comparison x == ToPrimitive(y).
> 9.If Type(x) is Object and Type(y) is either String, Number, or Symbol, return the result of the comparison ToPrimitive(x) == y.
Return false.

以下是关于===部分：

> Strict Equality Comparison
> The comparison x === y, where x and y are values, produces true or false. Such a comparison is performed as follows:
> 1.If Type(x) is different from Type(y), return false.
> 2.If Type(x) is Number, then
> 2.1.If x is NaN, return false.
> 2.2.If y is NaN, return false.
> 2.3.If x is the same Number value as y, return true.
> 2.4.If x is +0 and y is -0, return true.
> 2.5.If x is -0 and y is +0, return true.
> 2.6.Return false.
> 3.Return SameValueNonNumber(x, y).

关键注意关于==的第一条，第一条说明了即使使用==号，也会先进行===比较，如果类型一致，直接返回===比较后的结果。之前说过，规范是js代码实现的算法描述，在实现==的算法描述第一步，就进行了类型比较。所以==和===都进行了类型比较，只不过==要在===比较失败时进行一些其他情况的判定并返回比较结果。所以当两个值得类型相同时，==与===没有任何区别。

所以真正重要的是作为开发人员要清楚地知道自己要比较的两个值是什么类型。

所以==与===的区别就在于我们是否允许两个比较值进行类型转换。因为===是短路操作，如果类型不同直接返回false。但是==允许在类型不同时进行类型转换后递归比较。

在这里注意，比较两个对象时，应为变量保存的是对象的引用地址，所以即使两个对象的结构及字面量一致，返回的也是false。

> == allows coercion (types different)
> === disallows coercion (types same)

### Coercive Equality

和其他操作一样，强制类型转换是否有助于等于比较?
> Like every other operation, is coercion helpful in an equality comparison or not?

你可以问自己，如果我很明确要比较的数据类型，强制类型转换是否对我有帮助？

使用==并不意味着坏的，而是意味着我是否允许比较的数据进行类型转换。如果当前情况下进行类型转换对我是否更有帮助，是更安全还是更危险。如果你不确定要比较的数据的类型，那么想办法在其他部分是数据类型更加明确是不是更好的选择？

使用==的优势之一可以参考规范的第2条及第3条，既免于我们判断数据类型为空时，到底是`===null`还是`===undefined`。

这里作者也提出了lint工具只是一个参考，lint工具通过预先制定好的规则来判断代码是否符合规范，但是规范只是参考，并不能保证代码就一定不出错。关于代码规范，每个人也有不同的看法。我们应该让团队自己决定团队的代码规范。

### Double Equals Algorithm

可以从上面的规范看出，==号的算法设计倾向于把两个值转化为数字类型进行比较，当比较值不是原始类型值（数组等），会转化为原始类型值进行比较。

### Double Equals Summary

> If the types are the same: ===
> If null or undefined: equal
> If non-primitives: ToPrimitive
> Prefer: ToNumber

==的算法摘要：如果类型一致就是用===判断，如果是null或者undefined就返回true，如果不是原始类型就转化为原始类型进行比较，最后比较时倾向于把值转化为number类型进行比较

### Corner Cases: Summary

我们应在使用==判断时避开边缘案例的几个主要场景

1. == with 0 or "" (or even " ")
2. == with non-primitives
3. == true or == false : allow ToBoolean or use ===

不要使用==比较0，空字符串，或包含空格的空字符串。
不要用==比较非原始类型的值。
不要判断一个值是否==true或者==false，如果不得不比较，使用===。

### The case for preferring ==

了解类型总是比不了解它们好
> Knowing types is always better than not knowing them
静态类型不是了解类型的唯一(甚至不一定是最好的)方法
> Static Types is not the only (or even necessarily best) way to know your types
如果你不了解你要比较的数据类型，不要使用==
> == is not about comparisons with unknown types
==是关于与已知类型的比较，在类型转换非常有帮助的情况下可以选择
> == is about comparisons with known type(s), optionally where conversions are helpful

## Static Typing

### TypeScript & Flow

优点：
> Benefits:

1. 捕获类型相关的错误
   > Catch type-related mistakes
2. 交流类型的意图（直接在代码上表示出类型而不是注释，使得代码更加易读）
   > Communicate type intent
3. 编辑器的提示支持
   > Provide IDE feedback

警告：
> Caveats:

1. 推断是最好的猜测，而不是保证
   > Inferencing is best-guess, not a guarantee
2. 注释是可选的
   > Annotations are optional
3. 应用程序中任何没有类型化的部分都会带来不确定性
   > Any part of the application that isn't typed introduces uncertainty

### Inferencing

有些人认为动态类型的最大的问题之一是会意外的导致变量赋值为我们预期之外的类型，对于持有这个观点的人来说，静态类型检查是一个很好的解决方案。

### Custom Types & Signatures

函数的参数类似一个变量，如果你在ts中说我希望只传递数字，也就是再说我希望该变量只保存数字。

### Validating Operand Types

作者认为在typescript中有一件事被低估了，除了静态类型检查，它还可以告诉我们有些操作是无效的，例如你无法用一个数字减去一个字符串，也就是说不要使用强制类型转换，这在有时对我们很有帮助。

### TypeScript vs. Flow

嗯。。。似乎flow已经凉了。。。vue的作者已经全面转向了TypeScript，所以。。。

### TypeScript & Flow: Pros and Cons

下面是作者认为的js的静态类型语言一些优缺点：

让代码的类型更为明显。
> They make types more obvious in code
与其他语言的相似性很高，减少了学习成本及思维负担。
> Familiarity: they look like other language's type systems
流行程度高，社区活跃。
> Extremely popular these days
对于语言特性支持的比较好。
> They're very sophisticated and good at what they do
它们使用“非js标准”语法(或代码注释)。
> They use "non-JS-standard" syntax (or code comments)
它们需要一个构建过程，这增加了进入的障碍。
> They require* a build process, which raises the barrier to entry
对于那些没有正式类型经验的人来说，它们的复杂性可能令人生畏。
> Their sophistication can be intimidating to those without prior formal types experience
它们更关注“静态类型”(变量、参数、返回、属性等)，而不是值类型。
> They focus more on "static types" (variables, parameters, returns, properties, etc) than value types
对运行时行为有信心的唯一方法是限制/消除动态类型。
> The only way to have confidence over the runtime behavior is to limit/eliminate dynamic typing

### Understanding Your Types

JavaScript有一个(动态)类型系统，它使用各种形式的强制类型转换进行值的数据类型转换，包括相等比较
> JavaScript has a (dynamic) type system, which uses various forms of coercion for value type conversion, including equality comparisons

js的值是有类型的，变量没有。

然而，流行的响应似乎是:尽可能避免使用这个系统，并使用===“保护”自己，免于担心类型转换。
> However, the prevailing response seems to be: avoid as much of this system as possible, and use === to "protect" from needing to worry about types

避免使用所有类型的JS(比如假装===)的部分问题在于，它可能会在系统上延续bug
> Part of the problem with avoidance of whole swaths of JS, like pretending === saves you from needing to know types, is that it tends to systemically perpetuate bugs

如果不知道操作中涉及的类型，就无法编写高质量的JS程序。
> You simply cannot write quality JS programs without knowing the types involved in your operations.

另外，许多人选择采用不同的“静态类型”系统
虽然在某些方面确实有帮助，但这是另一种“回避”
> Alternately, many choose to adopt a different "static types" system layered on top
> While certainly helpful in some respects, this is "avoidance" of a different sort

显然，JS的类型系统较差，所以必须替换它，而不是学习和利用
许多人声称JS的类型系统对于新开发人员来说太难学了，而且静态类型(以某种方式)更容易学
我的主张:更好的方法是接受并学习JS的类型系统，并采用一种使类型尽可能明显的编码风格
通过这样做，您将使您的代码对有经验的开发人员和新开发人员都更具可读性和健壮性
> Apparently, JS's type system is inferior so it must be replaced, rather than learned and leveraged
> Many claim that JS's type system is too difficult for newer devs to learn, and that static types are (somehow) more learnable
> My claim: the better approach is to embrace and learn JS's type system, and to adopt a coding style which makes types as obvious as possible
> By doing so, you will make your code more readable and more robust, for experienced and new developers alike

## Scope(作用域)

### Scope

+ Nested Scope • Hoisting(嵌套范围•提升)
+ Closure(闭包)
+ Modules(模块化)

之前作者提出了他认为的js的三大支柱，分别为类型系统(Types)，作用域(Scope)和面向对象(Object(Oriented))。

首先我们要理解词法作用域(lexical scope)，词法作用域时js的运行机制之一。理解词法作用域之后我们就可以理解闭包，理解闭包之后我们就能理解模块化。

> Scope: where to look for things
作用域使我们寻找数据的地方。为了理解作用域，首先要明确几个问题。

第一，我们在寻找什么？一般而言，我们寻找的是标识符。所以当我们处理代码时，当js引擎处理作用域时，js引擎基本上是在处理这个问题：当我看到这个变量时，这个变量处于什么位置？处于哪个作用域？作者做了一个比喻，就像有几个不同颜色的桶，里面装了对应颜色的石头。当js想要找绿色石头时，显然会去绿色桶中去找。

在这里，作者认为js不是一个脚本语言，而是一个编译语言。js在执行前会进行编译，或者说解析。作者提出了证据，就是假设在代码第100行有一个语法错误，js会在执行一开始就报错，而不是执行完前99行后进行报错。这说明了js会在执行开始前对代码进行解析。那么，js会进行哪些解析处理？

在编辑器理论中，有着四个编译阶段（有时可能有的阶段合并，有的时候分开）：

1. lexing(词法分析)
2. tokenization(标记)
3. parsing(将token转化为抽象语法树)
4. code generation(代码生成)

这是文本代码转化为可执行机械码的一般过程。

一般认为js是脚本语言的原因之一是没有运行编译器进行编译，这是许多人区别脚本语言和编译语言的心理模型。在这里，作者认为正确的分析方式应该是代码在执行前究竟有没有进行解析执行。作者在这里提出js是编译语言的原因是引发js对作用域建立时机的思考（js是什么时候把不同颜色的石头装到不同颜色的桶之中的）。如果我们认为js是边执行边分类的话那么显然会导致一些错误。这就产生了我们预期心理模型与js执行模型的分歧。所以我们在这里所做的就是使我们的大脑的思维方式尽量与编译器一致，我们在这里希望我们能像编译器一样思考。好消息是我们可以根据二八原则，只学习百分之二十的编译器思想就可以避免百分之八十的问题，剩下的百分之二十的问题暂时与我们无关。在这里，把不同石头提前装到不同颜色的桶中就类似js处理作用域并把对应标识符放置在对应作用域。

所以js在执行前，会有一个编译阶段，并在编译阶段中生成抽象语法树的同时也生成了词法作用域，然后交于js引擎解析执行代码。所以js会先处理代码，生成词法作用域，然后解析执行。

既然在上面的例子中不同颜色的石头代表了标识符，那么水桶代表了什么？

函数作用域和块级作用域(ES6中新增)
> JavaScript organizes scopes with functions and blocks

### Compilation & Scope(编译和作用域)

代码分析：

```js
var teacher = 'Kyle'

function otherClass() {
  var teacher = 'Suzy'
  console.log('welcome!')
}

function ask() {
  var question = 'why?'
  console.log(question)
}

otherClass()
ask()
```

在分析时强调了js的所有词法作用域在js执行前的编译阶段就已经创建，这样在运行时才可以顺利的访问不同词法作用域的不同变量。

### Executing Code

在js中，变量拥有两个角色，一个是存储数据，一个是在需要获取数据时代表数据。这样我们就可以把数据和变量关联起来。变量(variable)和值(value)的关系更像是源(source)和目标(target)。

如果试图给一个未声明的变量赋值，js会自动将该变量添加到全局作用域中。

### Strict Mode

把`"use strict"`关键字放在作用域顶部，即可切换为严格模式。在严格模式下，给未声明的变量赋值会导致引用错误(ReferenceError)。

### Nested Scope(作用域套嵌)

作用域套嵌还可以用水桶比喻，套嵌可以类比为水桶里面装水桶，里面的水桶没有就到外面的水桶中找，所以闭包也就是直接返回小水桶，但是小水桶带着外面大水桶的石头（引用了外部函数执行上下文中的变量，并且函数的参数可以视作在函数内部声明的新变量，其值与传入的实参的值相同），在内部的水桶可以找到外部水桶中的石头，但是外部的无法找到内部水桶中的石头。

### Undefined vs Undeclared

变量未声明与变量未赋值到底有什么区别？undefined表示变量存在但是目前没有任何值（有可能之前有，也有可能之后有但是目前没有）；未声明表示在我们的所有作用域中不存在这个变量。

### Lexical Scope Elevator

在这里作者把词法作用域比作一个有电梯的高层建筑物，如果你想在这样一个大厦找东西，首先在第一层找（当前执行上下文），没有就坐电梯（作用域链）到第二层找（上级作用域），最终到顶楼找（全局作用域）。

## Scope & Function Expressions(作用域和函数表达式)

### Function Expressions(函数表达式)

在之前，我们在编译阶段分析函数时，将函数比喻为特殊颜色的水桶，放在全局作用域这个水桶中，并将函数内声明的变量标记为与函数代表的水桶颜色相同的石头放入当前水桶。

下面看一段代码

```js
function teacher() { /*...*/ }  // 函数声明

var otherTeacher = function() { /* otherTeacher */ }

var myTeacher = function anotherTeacher() { // 函数表达式
  console.log(anotherTeacher)
}

console.log(teacher) // ƒ teacher() { /*...*/ }
console.log(otherTeacher) // ƒ () { /* otherTeacher */ }
console.log(myTeacher) // ƒ anotherTeacher() { console.log(anotherTeacher) }
console.log(anotherTeacher) // ReferenceError: anotherTeacher is not defined
```

但是函数声明和函数表达式是有区别的，函数表达式会将函数名作为变量注册到局部作用域中，所以在全局作用域中无法通过函数名访问通过函数表达式定义的函数，可以通过将函数表达式赋值给变量，通过变量访问函数。

#### Named Function Expressions

在上述代码中，`otherTeacher`与`anotherTeacher`均为函数表达式，但是可以发现，`otherTeacher`变量赋予了一个匿名函数，`anotherTeacher`后跟了一个命名函数，我们称`anotherTeacher`为命名的函数表达式。

### Naming Function Expressions

一般我们使用函数表达式都是把匿名函数赋值给变量，但是把命名函数赋值给变量有如下好处(上面代码中`otherTeacher`与`anotherTeacher`通过控制台输出也可发现)

1. Reliable function self-reference (recursion, etc)(可靠的函数自引用(递归等))
2. More debuggable stack traces(调试时更多可堆栈跟踪)
3. More self-documenting code(更多的自我记录的代码)

在函数自引用时，我们知道arguments.callee已经不推荐使用，那么我们如何在函数内部引用函数自身（递归等情况）？直接使用保存着函数指针的外部变量？还是使用只存在函数作用域内部且具有只读属性的函数名？显然后者可能是更好的选择。首先函数名不会想外部变量一样被重新赋值，所以一直指向函数本身，其次函数名只存在函数作用域内部，只要函数存在就可以调用。

其次在我们进行调试时，如果使用匿名函数，那么显然调用栈中是不容易看出到底执行的是哪个匿名函数。但是使用命名函数表达式，就可以在调用栈中显示出函数名称，方便调试。

第三，可以使得代码的可读性提高，我们可以根据函数的作用给函数命名，并且赋值给变量。

代码的作用是更清晰的展现编程者的意图。所以匿名函数除了减少键盘输入次数可能并没有其他好处，我们应该了解我们所写的每一个函数的功能，并且根据函数的功能给其命名，如果功能过于复杂导致名称过于抽象，那么应该把函数拆分，然后重新给每个函数命名。

### Arrow Functions(箭头函数)

下面还是看一段代码；

```js
var ids = people.map(person => person.id) // 常见用法，但作者不建议

var ids = people.map(function getId(person) { // 建议使用命名回调函数，可读性更强
  return person.id
})

/****************************************************************************/

getPerson()
  .then(person => getData(person.id)) // 一般用法，作者不建议
  .then(renderData)

getPerson()
  .then(function getDataFrom(person){ // 建议使用命名回调函数，可读性更强
    return getData(person.id)
  })
  .then(renderData)

/****************************************************************************/

var getId = person => person.id
var ids = people.map(getId)

var getDataFrom = person => getData(person.id)
getPerson()
  .then(getDataFrom)
  .then(renderData)
```

### Function Type Hierachy(函数类型层次)

在这里作者认为

命名的函数声明 > 命名函数表达式 > 匿名函数表达式
> (Named) Function Declaration > Named Function Expression > Anonymous Function Expression

## Advanced Scope

### Lexical & Dynamic Scope(词法和动态作用域)

词法作用域在编译时已经创建好，不受代码执行影响。动态作用域边执行边创建，代码会影响作用域。

### Lexical Scope

词法作用域的设计初衷就是在编译时创建好代码中已有的作用域，这样在解析代码时就不懂在创建一遍作用域，加快代码的解析速度。

### Dynamic Scope(动态作用域)

```js
var teacher = 'Kyle'

function ask(question) {
  console.log(teacher, question)
}

function otherClass() {
  var teacher = 'Suzy'
  ask('why')
}

otherClass()
```

这里的代码中，控制台输出的是`Kyle why`，因为函数的作用域取决于定义是的作用域，也就是在编译时函数的作用域就已经决定。虽然`ask`在`otherClass`内调用，但就像之前所说，js是词法作用域，执行时不影响编译时创建好的作用域，所以输出的是`Kyle why`而不是`Suzy why`。

对应的，动态作用域语言输出`Suzy why`，因为其作用域取决于函数执行时的作用域。

动态作用域在运行时的条件确定作用域，词法作用域在编译时已确定，执行时不会更改已经确定的作用域。

### Function Scoping

```js
var teacher = 'Kyle'

function anotherTeacher() {
  var teacher = 'Suzy'
  console.log(teacher) // Suzy
}

console.log(teacher) // Kyle
```

可以看到，上述代码中，`teacher`分别被声明在两个作用域中，这就一定程度上解决了变量命名冲突问题。

在程序设计中，有一项原则，称为最小暴露原则或者最小特权原则：你应该保持一切都隐藏起来，只暴露必要的部分。这是一种防御姿态，保持我们程序的独立性。这事实上也解决了三个问题，首先减少了命名冲突，其次减少了意外使用或修改的风险（不暴露在外部，在外部无法访问修改私有属性方法），第三，可以帮助以后的重构（不依赖外部环境，可以放心修改）。

### IIFE Pattern(立即执行函数模式)

立即执行函数来源于一个想法，既然我们可以把一段代码放在一个()内，使其成为一个代码段执行，那么为什么要把保存函数指针的变量后加()执行而不是函数本身放在()内，在后面加()调用呢？

这应该就是立即执行函数的来源。既把一个函数的声明放在一个()内，使其解析为一个代码段，并在后加()立即调用，这样就把定义函数和调用函数放在了一起。而不是在变量中保存函数指针然后使用变量调用函数。

在这里作者还是建议即使使用立即执行函数，也最好给函数命名。如果实在不知道叫啥，就叫IIFE。这样才方便在Chrome开发者工具中追踪调用栈目前执行的函数。

### Block Scoping

块级作用域的原则与函数作用域一致：我们把数据和函数放在块级作用域内隐藏起来，减少命名冲突的可能性，保护私有变量及方法，方便未来进行重构。

```js
var teacher = 'Kyle'

{ // 块级作用域开始
  let teacher = 'Suzy' // 私有变量
  console.log(teacher) // ‘Suzy’
} // 块级作用域结束

console.log(teacher)
```

注意，因为在es5中只有函数作用域，所以即使在{}内使用var来声明变量，变量属于其所在的函数或全局作用域内，所以在{}内不能使用var来定义私有变量。**通过使用ES6的`let`及`const`定义变量，我们可以在一个{}定义私有变量及方法，既`let`和`const`会将所在的{}变成一个块级作用域，也就是说如果一个{}内没有`let`和`const`关键字，{}就不是一个块级作用域**。

```js
function diff(x, y){
  if(x > y) {
    var tmp = x // 这里使用var声明了一个if语句内的临时变量，不建议使用，因为var在整个函数的作用域中
    let tmp2 = x // 这里使用了let关键字声明变量，是的if后的{}成为一个块级作用域，tmp2成为一个私有变量
    x = y
    y = tmp
  }
  return y - x
}
```

在这里作者还建议了谨慎使用`let`及`const`，因为这两个关键字会把当前{}变为新的作用域，但是使用`var`并不会导致作用域的增加，所以仍有使用`var`的原因。例如如下代码：

```js
function repeat(fn, n) {
  var result

  for(var i = 0; i < n; i++) {
    result = fn( result, i )
  }

  return result
}
```

### Choosing let or var

作者认为上述代码使用es6的`let`关键字后可以修改如下

```js
function repeat(fn, n) {
  var result

  for(let i = 0; i < n; i++) {
    result = fn( result, i )
  }

  return result
}
```

注意在上述代码中，for循环内的关键字修改为`let`，但是函数体内第一行仍使用`var`。作者认为，只在一些需要私有变量的时候，例如for循环体内使用`let`，但是在定义一些作用域内均可使用的变量时，仍使用`var`关键字，这样可以使代码更加语义化，既使用`let`声明明确的私有变量，`var`声明作用域内的公共变量。

```js
function lookupRecord() {
  try {
    var id = getRecord( searchStr )
  }
  catch (err) {
    var id = -1
  }
  return id // 如果把上面的try catch语句中的var替换为let，这里会报错，因为id在此作用域内未声明
}
```

因为`var`关键字可以在同一作用域内多次声明同一变量，所以有时使用var重复声明变量可以使代码更加语义化，此时可以使用`var`关键字。

### Explicit let Block

```js
function formatStr(str) {
  { let prefix, rest
      prefix = str.slice(0, 3)
      rest = str.slice(3)
      str = prefix.toUpperCase() + rest
  }

  if (/^FOO:/.test(str)) {
    return str
  }

  return str.slice(4)
}
```

在上述代码中，即使在函数作用域内，作者还是给临时变量添加了{}，使用了块级作用域来包裹声明的临时变量。这也是其他使用块级作用域语言的常用作法。

### Const

`const`不是意味着一个不变的变量，而是意味着无法重新分配的变量。在js中，基本类型传递的是基本类型的值，把基本类型值赋值给`const`定义的变量，那个就无法重新给此变量赋值。但是因为引用类型传递的是指针，所以即使改变了指针所指向的数据结构，但是只要指针本身不改变，就不会报错。所以使用`const`定义变量并把引用类型的指针赋值给变量时，仍可更改引用类型的值。

### Hoisting(变量提升)

js引擎其实并没有变量提升这个执行过程。变量提升是一种语言惯例，其实并不存在与js中。

```js
student // undefined
teacher // undefined
var student = 'you'
var teacher = 'Kyle'
```

在过去，我们把先使用变量后声明并赋值变量，但是并不报错并返回undefined的现象解释为变量提升。我们对变量提升的解释模型如下

```js
var student
var teacher
student = 'you'
teacher = 'Kyle'
```

人们认为代码在执行时的结果如上，并将变量提升解释为js在执行时会将所有变量的声明提升到作用域顶端，但是还在代码的同样位置赋值。但事实上js的运行机制并不是这样。

就像我们之前所讲的一样，js在执行前的编译阶段就已经创建好了所有的作用域，并明确了作用域内声明的所有变量。所以js执行时的代码更类似如下这样：

```js
student
teacher
student = 'you'
teacher = 'Kyle'
```

js在解析编译阶段就已经将所有作用域及各个作用域的变量都创建好，只是在执行阶段进行赋值并操作修改。所以变量提升的说法并不严谨，这只是为了简化js的运行机制删除了一些细节的粗略模型。

```js
teacher() // Kyle
otherTeacher() // TypeError: otherTeacher is not a function

function teacher() {
  return 'Kyle'
}

var otherTeacher = function () {
  return 'Suzy'
}
```

使用变量提升模型，我们将上述代码转换如下：

```js
function teacher() {
  return 'Kyle'
}
var otherTeacher

teacher() // Kyle
otherTeacher() // TypeError: otherTeacher is not a function

otherTeacher = function () {
  return 'Suzy'
}
```

### let Dosen't Hoist

使用let关键字不会导致变量提升？

我们来看一段代码

```js
{
  teacher = 'Kyle' // TDZ error 暂时性死区(temporal dead zone)错误
  let teacher
}
```

这句话的出发点很好，但不准确。

```js
var teacher = 'Kyle'
{
  console.log(teacher) // TDZ error 暂时性死区(temporal dead zone)错误
  let teacher = 'Suzy'
}
```

在上述代码中，如果用let不会变量提升来解释，显然控制台应该打印”Kyle“。但是代码仍然报错暂时性死区错误。

这说明`let`也会有变量提升，但是提升的方式与`var`不同。首先`let`提升到块级作用域顶部，而`var`提升高函数作用域顶部。其次在使用`var`时，会将变量的值初始化为`undefined`，但是用`let`时，只会在块级作用域顶部声明变量，但是不会初始化变量，所以在初始化变量的值之前都无法使用变量，这就是暂时性死区。

作者在这里认为TDZ概念最早提出是基于`const`，因为`const`代表了一个不可以重新赋值的变量，如果我们把`const`定义的变量提升到作用域顶部并在赋值之前初始化为`undefined`，那么显然`const`就会在赋值时重新赋值，这显然违背了`const`设计的初衷。所以在使用`const`定义变量时，仍存在变量提升，但不会初始化，此时`const`所定义的变量也无法使用，这就是TDZ的由来。既然`const`如此，那么`let`为什么不也这样呢，所以es6中`let`和`const`都有TDZ。

在问答中，作者也提出了**其实js在编译时也对函数进行了处理，创建了函数作用域并声明了作用域的变量，但是并没有对变量进行具体操作，这些操作都会在js执行阶段调用函数时执行**。

## Closure(闭包)

### Origin of Closure(闭包的起源)

闭包的思想在计算机科学存在之前就已经存在，来源于Lambda演算。

### What is Closure

这里是作者认为最为准确的闭包的定义。

**闭包是即使函数是在词法作用域之外执行，函数仍然可以“记住”它的词法作用域**。
> Closure is when a function “remembers” its lexical scope even when the function is executed outside that lexical scope.

当把一个函数作为回调函数传给另一个函数作为参数时，或者把一个函数从另一个函数内部返回时，很明显函数定义时所在的作用域与执行时并不一致，此时函数的作用域在概念上应该被垃圾回收掉了。但是我们发现此时仍可以通过函数访问已经’消失‘的变量，这并不是偶然的，这就是闭包的设计初衷。

```js
function ask(question) {
  setTimeout(function waitASec() {
    console.log(question)
  }, 100)
}
ask('What is Closure')
// What is Closure
```

在上述代码中可以看到，`setTimeout`内的回调函数`waitASec`访问到了其外部`ask`的实参，理论上`ask`已在100毫秒前执行完毕，其执行上下文被推出调用栈，并被垃圾回收机制回收。但是因为`setTimeout`内的回调函数`waitASec`对其实参进行了引用，所以在内存中的数据并没有消失，而是保留了下来，以供`waitASec`进行调用，这就是闭包。

在这里作者提出据她了解，js引擎会将作用域的全部执行上下文保留，而不仅仅是被引用的变量，这在作用域内数据量特别大的时候会有性能上的风险。

### Closing Over Variables

闭包并不是把变量保存下来，而是与变量建立连接。所以如果内部函数引用了外部变量，外部变量改变，内部函数引用的外部变量也会改变。

```js
var teacher = 'Kyle'

var myTeacher = function myTeacherIs() {
  console.log(teacher)
}

teacher = 'Suzy'

myTeacher() // Suzy
```

可以看到上述代码输出了Suzy，证明了外部变量改变后，函数内部引用的teacher的值也进行了改变。

```js
for(var i = 1; i <= 3; i++){
  setTimeout(function numberIs(){
    console.log(`i: ${i}`)
  }, i * 1000)
} // 每隔1s输出4，共三次
```

如果使用es6语法，将上述代码的输出结果为1，2，3。代码如下

```js
for(var i = 1; i <= 3; i++){
  let j = i
  setTimeout(function numberIs(){
    console.log(`j: ${j}`)
  }, j * 1000)
}
```

上述代码中，我们在for循环内使用了`let`关键字，使得for循环的{}成为了一个块级作用域。`setTimeout`的回调函数引用的时在作用域内新建的变量，每次执行for循环，就新建一个`j`，所以可以输出1，2，3。

```js
for(let i = 1; i <= 3; i++){
  setTimeout(function numberIs(){
    console.log(`i: ${i}`)
  }, i * 1000)
}
```

或者我们可以更加简化一下，直接在for循环的()内使用`let`关键字，使for循环的{}成为一个块级作用域。

### Module Pattern

```js
var workshop = {
  teacher: 'Kyle',
  ask(question) {
    console.log(this.teacher, question)
  }
}

workshop.ask('Is this a module?') // Kyle Is this a module?
```

上述代码把方法和方法需要的数据都添加到了一个对象中，是**命名空间模式**。这是一种组织方法与数据的模式，但这并不是模块。命名空间模式并不是模块的原因是模块模式需要封装的概念，封装意味着要隐藏数据与行为。

**模块将数据和行为(方法)封装在一起。模块的状态(数据)由它的方法通过闭包保存**。
> Modules encapsulate data and behavior(methods) together. The state (data) of a module is held by its methods via closure.

```js
var workshop = (function Module(teacher){
  var publicAPI = { ask, }
  return publicAPI

  //*******************

  function ask(question) {
    console.log(teacher, question)
  }
})('Kyle')

workshop.ask("It's a Module, right?") // Kyle It's a Module, right?
```

可以看到，在上述代码中，我们把需要的变量变为了函数的参数传入，并且返回了引用了参数的方法，在外部是无法访问方法所引用的数据的。通过闭包，我们可以保存私有变量及方法，所以没有闭包就没有模块。

```js
function WorkshopModule(teacher) {
  var publicAPI = { ask, }
  return publicAPI

  //***************

  function ask(question) {
    console.log(teacher,question)
  }
}

var workshop = WorkshopModule('Kyle')

workshop.ask("It's a Module, right?") // Kyle It's a Module, right?
```

在上面的代码中，我们没有使用IIFE(立即执行函数)，而是定义了一个普通函数作为模块，在需要时创建一个实例，通过实例的方法可以访问实例的私有属性。这样只用定义一次，每创建一个实例就有了一个新的模块。我们把`WorkshopModule`称为工厂函数。

### ES6 Modules & Node.js

ES6的模块基于文件，所以一个文件只能有一个模块。

### ES6 Module Syntax

```js
var teacher = 'Kyle'

export default function ask(question) {
  console.log(teacher, question)
}
```

```js
import ask from 'workshop.mjs'

ask("It's a default import, right?")
// Kyle It's a default import, right?

import * as workshop from 'workshop.mjs'

workshop.ask("It's a namespace import, right?")
// Kyle It's a namespace import, right?
```

## Objects

### Objects Overview

Objects (Oriented)

+ this
+ class { }
+ Prototypes
+ “Inheritance” vs. “Behavior Delegation” (OO vs. OLOO)

### The this Keyword

函数的`this`引用调用该函数的执行上下文，完全由函数的调用方式决定（函数定义时的执行上下文与`this`无关）。
> A function's this references the execution context for that call, determined entirely by how the function was called.

```js
function ask(question) {
  console.log(this.teacher, question)
}

function otherClass() {
  var myContext = {
    teacher: 'Suzy'
  }
  ask.call(myContext, 'Why?') // Suzy Why?
}

otherClass()
```

因此，每次调用此感知函数时，它可以具有不同的上下文，这使得它更加灵活和可重用。
> A this-aware function can thus have a different context each time it's called, which makes it more flexible & reusable.

在之前我们了解到，js使用词法作用域，也就是函数的作用域完全取决于函数定义时的作用域，但是如果我们想要获得一些动态作用域的特性呢？既当前作用域不取决于定义时的作用域，而是函数调用时的作用域，所以设计出了this关键字。

为了区分使用时`this`的指向，js设计出了四种调用函数的方式：

1. this: implicit binding(隐式绑定)
2. this: explicit binding(显示绑定)
3. this: hard binding(硬绑定，创建了一个新的函数，并不算函数调用)
4. this: new binding(new关键字绑定)
5. default binding

### Implicit & Explicit Binding

#### implicit binding(隐式绑定)

```js
var workshop = {
  teacher: 'Kyle',
  ask(question) {
    console.log(this.teacher, question)
  }
}

workshop.ask('What is implicit binding?') // Kyle What is implicit binding?
```

隐式绑定时this指向调用函数的对象的执行上下文(如果直接调用函数，指向widows对象的执行上下文，既全局作用域)。

通过隐式绑定，我们可以模拟出动态作用域的效果:

```js
function ask(question) {
  console.log(this.teacher, question)
}

var workshop1 = {
  teacher: 'Kyle',
  ask: ask
}

var workshop2 = {
  teacher: 'Suzy',
  ask: ask
}

workshop1.ask('How do I share a method?') // Kyle How do I share a method?
workshop2.ask('How do I share a method?') // Suzy How do I share a method?
```

#### explicit binding(显示绑定，使用call或apply方法)

```js
function ask(question) {
  console.log(this.teacher, question)
}

var workshop1 = {
  teacher: 'Kyle'
}

var workshop2 = {
  teacher: 'Suzy'
}

ask.call(workshop1, 'Can I explicitly set context?') // Kyle Can I explicitly set context?
ask.call(workshop2, 'Can I explicitly set context?') // Suzy Can I explicitly set context?
```

上述代码可以看到，我们直接调用了函数，但是使用了`Function.prototype.call()`方法调用，手动的将`ask`内的`this`指向了`workshop1`和`workshop2`，而不是`windows`对象，输出了预期的结果

#### hard binding

```js
var teacher = 'Suzy'
var workshop = {
  teacher: 'Kyle',
  ask(question) {
    console.log(this.teacher, question)
  }
}

setTimeout(workshop.ask, 1000, 'Lost this?') // Suzy Lost this?

setTimeout(workshop.ask.bind(workshop), 1000, 'Hard bound this?') // Kyle Hard bound this?
```

可以看到，在上述代码中将`work.ask`作为回调函数传入其他函数作为参数时，`this`的指向进行了改变，指向了`windows`对象的执行上下文，这是因为传递的只是函数的指针，在调用时函数指向了windows。此时我们要使用`bind`方法将`workshop`的执行上下文绑定在方法内的`this`上，这样只要调用，`this`就指向了`workshop`的执行上下文，而不是当前调用的作用域。所以`bind()`方法不是调用函数，而是创建一个新的函数，在调用时设置this关键字为提供的值。

### The new Keyword

使用`new`关键字，是我们第三种可以调用函数的方式（`bind`是创建了新函数，并不算调用函数）。

```js
function ask(question) {
  console.log(this.teacher, question)
}

var newEmptyObject = new ask("What is 'new' doing here?")
// undefined "What is 'new' doing here?"
```

当我们使用new关键字调用函数时，会有如下的过程

> js高级程序设计

1. 创建一个新对象，
2. 将构造函数的作用域赋给新对象（此时构造函数的this指向了新对象，新对象的原型也指向了构造函数的原型对象）
3. 执行构造函数中的代码
4. 返回新对象

> 此教程

1. Create a brand new empty object
2. Link that object to another object
3. Call function with this set to the new object
4. If function does not return an object, assume return of this

在JavaScript: The Hard Part中，我们是通过以下代码实现`new`关键字的

```js
// 构造器
function userCreator(name, score) {
  const newUser = Object.create(userFunctionStore)
  newUser.name = name
  newUser.score = score
  return newUser
}
// 原型对象
let userFunctionStore = {
  increment() { this.score++ },
  login() { console.log("you are loggedin") }
}
// 实例化构造器
let user1 = userCreator("Will", 3)
let user2 = userCreator("Tim", 5)
user1.increment()
```

### default binding

```js
var teacher = 'Kyle'

function ask(question){
  console.log(this.teacher, question)
}

function askAgain(question) {
  "use strict"
  console.log(this.teacher, question)
}

ask("What's the none-strict-mode default?") // Kyle What's the none-strict-mode default?
askAgain("What's the strict-mode default") //  TypeError
```

### Binding Precedence

```js
var workshop = {
  teacher: 'Kyle',
  ask: function ask(question) {
    console.log(this.teacher, question)
  }
}

new (workshop.ask.bind(workshop))('What does this do') // undefined "What does this do"
```

在上面的代码中，我们首先为在`workshop`对象中调用`ask`，同时也将`ask`中的`this`手动绑定到了`workshop`上，最后使用`new`调用函数并传入参数，最后`this.teacher`输出为`undefined`。

以下是判断this指向的顺序：

1. Is the function called by new?
2. Is the function called by call() or apply()? Note: bind() effectively uses apply()
3. Is the function called on a context object?
4. DEFAULT: global object (except strict mode)

### Arrow Functions & Lexical this

```js
var workshop = {
  teacher: 'Kyle',
  ask(question) {
    setTimeout(() => {
      console.log(this.teacher, question)
    }, 100)
  }
}

workshop.ask("Is this lexical 'this'?") // Kyle Is this lexical 'this'?
```

可以看到，在上述代码中回调函数使用了箭头函数，此时没有手动绑定`this`，但是仍输出了`workshop`的执行上下文内的变量。在这里我们把箭头函数中的`this`称为`lexical this`。在箭头函数中，并不存在`this`，在箭头函数中使用`this`就跟其他变量一样在定义时已经确定作用域，而不是执行时，并且绑定的是箭头函数的上一级作用域。

箭头函数是`this`-绑定(即`.bind()`)到它的父级函数。
> An arrow function is this-bound (aka .bind()) to its parent function.

上述是绝大部分人对于箭头函数中`this`的认知，实际上，根据规范，箭头函数中并不存在`this`关键字。所以在箭头函数中使用`this`，就会像使用其他变量一样，根据作用域链指向其父级作用域的`this`。

> 箭头函数并没有定义this，所以箭头函数中的this像其他普通变量一样，是按词法解析的(也就是“词法this”)。
> An arrow function doesn't define a this, so it's like any normal variable, and resolves lexically (aka "lexical this").

以下是规范中关于箭头函数中this的说明：

> ArrowFunction不为arguments、super、this或new.target定义本地绑定。ArrowFunction中任何对arguments、super、this或new.target的引用必须解析为词法封闭环境中的绑定。通常，这将是立即封闭函数的函数环境。尽管ArrowFunction可能包含对super的引用，但是在步骤4中创建的函数对象并不是通过执行MakeMethod而变成方法的。引用super的ArrowFunction总是包含在非ArrowFunction中，并且可以通过ArrowFunction的函数对象捕获的范围访问实现super所需的状态。
> An ArrowFunction does not define local bindings for arguments, super, this, or  new.target. Any reference to arguments, super, this, or new.target within an ArrowFunction must resolve to a binding in a lexically enclosing environment. Typically this will be the Function Environment of an immediately enclosing function. Even though an ArrowFunction may contain references to super, the function object created in step 4 is not made into a method by performing MakeMethod. An ArrowFunction that references super is always contained within a non-ArrowFunction and the necessary state to implement super is accessible via the scope that is captured by the function object of the ArrowFunction.

### Resolving this in Arrow Functions

```js
var workshop = {
  teacher: 'Kyle',
  ask: (question) => {
    console.log(this.teacher, question)
  }
}

workshop.ask("What happend to 'this'?") // undefined "What happend to 'this'?"
workshop.ask.call(workshop,"Still no 'this'?") // undefined "Still no 'this'?"
```

在上述代码中可以看到，即使显示的调用`call()`方法将箭头函数的`this`绑定在`workshop`上，仍然输出`undefined`，因为箭头函数中并没有定义`this`。

### ES6 class Keyword

`class`关键字是一个基于js原型系统的语法糖。

```js
class Workshop {
  constructor(teacher) {
    this.teacher = teacher
  }
  ask(question) {
    console.log(this.teacher, question)
  }
}

var deepJS = new Workshop('Kyle')
var reactJS = new Workshop('Suzy')

deepJS.ask("Is 'class' a class?") // Kyle Is 'class' a class?
reactJS.ask("Is this class ok?")  // Suzy Is this class ok?
```

以上是`class`的基本用法，如果想继承一个已经定义好的类，可以像如下代码

```js
class Workshop { // 定义一个基类
  constructor(teacher) {
    this.teacher = teacher
  }
  ask(question) {
    console.log(this.teacher, question)
  }
}

class AnotherWorkshop extends Workshop { // 继承基类
  speakUp(msg) {
    this.ask(msg)
  }
}

var JSRecentParts = new AnotherWorkshop('Kyle')

JSRecentParts.speakUp('Are classes getting better?') // Kyle Are classes getting better?
```

在上述代码中，我们使用`extends`关键字使`AnotherWorkshop`继承了`Workshop`的属性和方法。

```js
class Workshop {
  constructor(teacher) {
    this.teacher = teacher
  }
  ask(question) {
    console.log(this.teacher, question)
  }
}

class AnotherWorkshop extends Workshop {
  ask(msg) {
    super.ask(msg.toUpperCase())
  }
}

var JSRecentParts = new AnotherWorkshop('Kyle')
JSRecentParts.ask('Are classes super?') // Kyle ARE CLASSES SUPER?
```

在ES6中，我们使用了`class`解决面相对象中的封装问题，使用`extends`解决继承问题，那么如何解决多态问题呢？

所以设计出了`super`关键字，在上述代码中，我们在`AnotherWorkshop`中使用了`super`重新定义了继承与`Workshop`的`ask`方法。

```js
class Workshop {
  constructor(teacher) {
    this.teacher = teacher
  }
  ask(question) {
    console.log(this.teacher, question)
  }
}

var deepJS = new Workshop('Kyle')
setTimeout(deepJS.ask, 100, "Still losing 'this'?") // undefined "Still losing 'this'?"
```

可以看到，使用`class`定义的方法与普通函数一样，并不会绑定`this`。

### Fixing this in Classes

[作者的解决方案](https://gist.github.com/getify/86bed0bb78ccb517c84a6e61ec16adca)

```js
var method = (function defineMethod(){
   var instances = new WeakMap();

   return function method(obj,methodName,fn) {
      Object.defineProperty(obj,methodName,{
         get() {
            if (!instances.has(this)) {
               instances.set(this,{});
            }
            var methods = instances.get(this);
            if (!(methodName in methods)) {
               methods[methodName] = fn.bind(this);
            }
            return methods[methodName];
         }
      });
   }
})();

function bindMethods(obj) {
   for (let ownProp of Object.getOwnPropertyNames(obj)) {
      if (typeof obj[ownProp] == "function") {
         method(obj,ownProp,obj[ownProp]);
      }
   }
}
```

## Prototypes

### Prototypes(原型)

对象是通过“调用构造函数”创建的（使用new关键字）。
> Objects are built by "constructor calls" (via new)

“调用构造函数”会创建一个“**链接上**”构造函数**原型**的对象。
> A "constructor call" makes an object “**linked to**” its own **prototype**

我们都知道js是一门基于对象的语言。在一些面相对象的语言中，我们会创建一个类，在使用时创造一个类的实例来进行使用。类就类似图纸，实例就类似根据图纸造出来的建筑。我们没法直接使用图纸，但是可以使用建筑，同时，我们的建筑时根据图纸制造的，可以造的不同，但是一些图纸上的特性，建筑上也存在。

js原型系统的关键在于**实例并没有复制构造函数的原型对象，而是将实例的原型与构造函数的原型对象链接到了一起**。

### Prototypal Class

```js
function Workshop(teacher) {
  this.teacher = teacher
}
Workshop.prototype.ask = function(question) {
  console.log(this.teacher, question)
}

var deepJS = new Workshop('Kyle')
var reactJS = new Workshop('Suzy')

deepJS.ask("Is 'prototype' a class?") // Kyle Is 'prototype' a class?
reactJS.ask("Isn't 'prototype' ugly?") // Suzy Isn't 'prototype' ugly?
```

### The Prototype Chain

> 几乎所有的 JavaScript 对象都是 Object 的实例；一个典型的对象继承了Object.prototype的属性（包括方法），尽管这些属性可能被遮蔽（亦称为覆盖）。但是有时候可能故意创建不具有典型原型链继承的对象，比如通过Object.create(null)创建的对象，或者通过Object.setPrototypeOf方法改变原型链。
> 改变Object原型，会通过原型链改变所有对象；除非在原型链中进一步覆盖受这些变化影响的属性和方法。这提供了一个非常强大的、但有潜在危险的机制来覆盖或扩展对象行为。

### Dunder Prototypes(__proto__)

```js
function Workshop(teacher) {
  this.teacher = teacher
}
Workshop.prototype.ask = function(question) {
  console.log(this.teacher, question)
}

var deepJS = new Workshop('Kyle')

deepJS.constructor === Workshop // true

deepJS.__proto__ === Workshop.prototype // true
Object.getPrototypeOf(deepJS) === Workshop.prototype // true
```

> Object.prototype 的 `__proto__`  属性是一个访问器属性（一个getter函数和一个setter函数）, 暴露了通过它访问的对象的内部[[Prototype]] (一个对象或 `null`)。
> 使用__proto__是有争议的，也不鼓励使用它。因为它从来没有被包括在EcmaScript语言规范中，但是现代浏览器都实现了它。__proto__属性已在ECMAScript 6语言规范中标准化，用于确保Web浏览器的兼容性，因此它未来将被支持。它已被不推荐使用, 现在更推荐使用`Object.getPrototypeOf`/`Reflect.getPrototypeOf` 和`Object.setPrototypeOf`/`Reflect.setPrototypeOf`（尽管如此，设置对象的`[[Prototype]]`是一个缓慢的操作，如果性能是一个问题，应该避免）。
> `__proto__`属性也可以在对象文字定义中使用对象`[[Prototype]]`来创建，作为`Object.create()`的一个替代。 请参阅： object initializer / literal syntax.

通过上述mdn对于`__proto__`的说明，其实所有对象的`__proto__`指向了`Object.prototype.__proto__`，这是一个访问器属性（函数），函数通过实例对象调用，就指向了实例对象的执行上下文，也就是操作了实例对象的prototype属性。

注意，箭头函数没有prototype属性。

### Shadowing Prototype

```js
function Workshop(teacher) {
  this.teacher = teacher
}
Workshop.prototype.ask = function(question) {
  console.log(this.teacher, question)
}

var deepJS = new Workshop('Kyle')
deepJS.ask = function(question) {
  this.ask(question.toUpperCase())
}

deepJS.ask('Oops,is this infinite recursion?') // Maximum call stack size exceeded
```

### Prototypal Inheritance

```js
function Workshop(teacher) {
  this.teacher = teacher
}
Workshop.prototype.ask = function(question) {
  console.log(this.teacher, question)
}

function AnotherWorkshop(teacher) {
  Workshop.call(this, teacher) // 使用call绑定this指向AnotherWorkshop
}

AnotherWorkshop.prototype = Object.create(Workshop.prototype) // 使用Object.create继承Workshop.prototype
AnotherWorkshop.prototype.speakUp = function(msg) {
  this.ask(msg.toUpperCase())
}

var JSRecentParts = new AnotherWorkshop('Kyle')

JSRecentParts.speakUp('Is this actually inheritance?') // Kyle IS THIS ACTUALLY INHERITANCE?
```

### Classical vs Prototypal Inheritance

在传统的面向对象语言中，实例复制了公共的属性方法。在js中，实例连接到了存储着公共属性及方法的对象。所以js的设计模式与传统的面相对象语言的设计模式并不相同。

### Inheritance is Delegation

在js的设计模式并不是“继承”，更像是“委托”。js使用原型系统或者说委托系统，而不是类系统或者说继承系统。

### OLOO(Objects Linked to Other Objects) Pattern

```js
var Workshop = {
  setTeacher(teacher) {
    this.teacher = teacher
  },
  ask(question) {
    console.log(this.teacher, question)
  }
}

var AnotherWorkshop = Object.assign(
  Object.create(Workshop),
  {
    speakUp(msg) {
      this.ask(msg.toUpperCase())
    }
  }
)

var deepJS = Object.create(AnotherWorkshop)
deepJS.setTeacher('Kyle')
deepJS.speakUp('But is not this cleaner') // Kyle BUT IS NOT THIS CLEANER
```

`Object.create()`的polyfill:

```js
if(!Object.create) {
  Object.create = function(object) {
    function F() {}
    F.prototype = object
    return new F()
  }
}
```

### Delegation-Orienten Design

设计模式并不仅仅影响我们组织代码的方式，也指导着我们编写代码的思维模式。
