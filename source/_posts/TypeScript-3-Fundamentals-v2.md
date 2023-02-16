---
title: TypeScript 3 Fundamentals v2
date: 2019-08-05 23:20:36
categories: 学习笔记
tags: [FrontEndMasters, TypeScript]
---
## Introduction

[repo](https://github.com/mike-works/typescript-fundamentals/tree/master)

[slide](https://drive.google.com/file/d/170oHzpLNeprUa-TMmOAnSU4caEFDSb3e/view)

### What's TypeScript

[typescript](https://www.typescriptlang.org/)

+ An open-source **typed, syntactic superset** of JavaScript, developed by **Microsoft**
+ Compiles to [**readable JavaScript**](https://www.typescriptlang.org/play/index.html#src=class%20OtherContact%20%7B%0D%0A%20%20public%20age:%20number%20=%200;%0D%0A%20%20//%20private%20password:%20string;%0D%0A%20%20constructor(%0D%0A%20%20%20%20public%20name:%20string,%0D%0A%20%20%20%20public%20email:%20string,%0D%0A%20%20%20%20%20%20public%20phone:%20number)%20%7B%0D%0A%20%20%20%20%20%20%20%20console.log('my%20constructor')%0D%0A%20%20%20%20//%20()%20password%20must%20either%20be%20initialized%20like%20this,%20or%20have%20a%20default%20value%0D%0A%20%20%20%20//%20this.password%20=%20Math.round(Math.random()%20*%201e14).toString(32);%0D%0A%20%20%7D%0D%0A%7D)
+ Comes in three parts: **Language, Language Server** and **Compiler**
+ Works seamlessly with [**Babel 7**](https://devblogs.microsoft.com/typescript/typescript-and-babel-7/)
<!-- more -->
### Rationale

#### but why add types

+ Encode **constraints and assumptions**, as part of **developer intent**
+ **Catch common mistakes** (i.e. incomplete refactors)
+ Move some **runtime errors to compile time**
+ Provide your consumers (including you) with **a great DX**

### Course Overview

#### In this class, we'll learn about

+ Adding type information to variables, functions and classes
+ Configuring the compiler
+ A practical strategy for incrementally converting JS to TS
+ Parameterizing interfaces and type aliases with generics
+ Conditional, mapped and branded types
+ TS Compiler API basics

### Flags

[Project setup](https://github.com/mike-works/typescript-fundamentals/tree/v2#dependencies)

```bash
git clone https://github.com/mike-works/typescript-fundamentals -b v2 tscript
cd tscript
```

#### Compiling

`cd ./examples/hello-ts/`

`tsc ./src/index.ts` 转译为es3语法js文件

`tsc ./src/index.ts --target ES2015` 转译为es2015语法文件（含有promise和generator语法，没有async await）

`tsc ./src/index.ts --target ES2017` 转译为es2017语法文件（含有async await语法）

此时使用`node ./src/index.js`会报错，因为导出为ESM规范的模块，node使用commonJS规范

`tsc ./src/index.ts --target ES2017 --module commonjs`

可以看到使用`node ./src/index.js`文件正常运行

还可以加`--watch`命令进入观察模式

### Configuring TypeScript

`touch ./tsconfig.json`

```json
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "es2019",
    "outDir": "lib"
  },
  "include": ["src"]
}
```

`tsc`可以看到生成了lib文件夹，并含有对应js文件

```json
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "es2019",
    "outDir": "lib",
    "declaration": true,
    "sourceMap": true
  },
  "include": ["src"]
}
```

在`compilerOptions`中添加`"declaration": true,"sourceMap": true`，重新使用`tsc`进行编译，会看到生成了`index.d.ts`和`index.js.map`两个文件

## TypeScript Basics

### Variables

`open ./notes/1-basics.ts`

可以看到使用let赋值时，ts会根据初始值的类型约定变量类型。使用const时，使用初始值来标识变量。

```js
//== BASICS ==//

/**
 * (1) x is a string, b/c we’ve initialized it
 */
let x = "hello world";

/**
 * (2) reassignment is fine
 */
x = "hello mars";

/**
 * (3) but if we try to change type
 */
x = 42; // 🚨 ERROR

/**
 * (4) let's look at const. The type is literally 'hello world'
 */
const y = "hello world";
```

### Variable Declarations

有时我们会声明一个变量，但并不立即赋值，此时ts就会将变量标识为any，这并不是什么好事，为了避免此类情况，需要在声明变量时指定变量类型。

```js
/**
 * (5) sometimes we need to declare a variable w/o initializing it
 */
let z;
z = 41;
z = "abc"; // (6) oh no! This isn't good

/**
 * If we look at the type of z, it's `any`. This is the most flexible type
 * in TypeScript (think of it like a JavaScript `let`)
 */

/**
 * (7) we could improve this situation by providing a type annotation
 * when we declare our variable
 */
let zz: number;
zz = 41;
zz = "abc"; // 🚨 ERROR Type '"abc"' is not assignable to type 'number'.
```

### Arrays & Tuples

可以在初始化数组时指定类型，也可以直接给数组赋值为对应类型数据，ts会自动识别类型，但是注意，在静态赋值时ts可以识别元素是否为对应类型，但是使用push等方法时，无法确认每个元素对应的具体类型。

```ts
//== SIMPLE ARRAYS ==//

/**
 * (8) simple array types can be expressed using []
 */
let aa: number[] = []; // 标识类型为数组，数组每个元素为数字类型
aa.push(33);
aa.push("abc"); // 🚨 ERROR: Argument of type '"abc"' is not assignable to parameter of type 'number'.

/**
 * (9) we can even define a tuple, which has a fixed length
 */
let bb: [number, string, string, number] = [
  123,
  "Fake Street",
  "Nowhere, USA",
  10110
];

bb = [1, 2, 3]; // 🚨 ERROR: Type 'number' is not assignable to type 'string'.

/**
 * (10) Tuple values often require type annotations (  : [number, number] )
 */
const xx = [32, 31]; // number[];
const yy: [number, number] = [32, 31];
```

### Object Types & Interfaces

```ts
/**
 * (11) object types can be expressed using {} and property names
 */
let cc: { houseNumber: number; streetName: string };
cc = {
  streetName: "Fake Street",
  houseNumber: 123
};

cc = {
  houseNumber: 33
};
/**
 * 🚨 Property 'streetName'
 * 🚨   is missing in type   '{ houseNumber: number; }'
 * 🚨   but required in type '{ houseNumber: number; streetName: string; }'.
 */

/**
 * (12) You can use the optional operator (?) to
 * indicate that something may or may not be there
 */
let dd: { houseNumber: number; streetName?: string };
dd = {
  houseNumber: 33
};

// (13) if we want to re-use this type, we can create an interface
interface Address {
  houseNumber: number;
  streetName?: string;
}
// and refer to it by name
let ee: Address = { houseNumber: 33 };
```

### Intersection & Union Types

```ts
//== UNION & INTERSECTION ==//

/**
 * (14) Intersection types
 * Sometimes we have a type that can be one of several things
 */

export interface HasPhoneNumber {
  name: string;
  phone: number;
}

export interface HasEmail {
  name: string;
  email: string;
}

let contactInfo: HasEmail | HasPhoneNumber =
  Math.random() > 0.5
    ? {
        // we can assign it to a HasPhoneNumber
        name: "Mike",
        phone: 3215551212
      }
    : {
        // or a HasEmail
        name: "Mike",
        email: "mike@example.com"
      };

contactInfo.name; // NOTE: we can only access the .name property  (the stuff HasPhoneNumber and HasEmail have in common)

/**
 * (15) Union types
 */
let otherContactInfo: HasEmail & HasPhoneNumber = {
  // we _must_ initialize it to a shape that's asssignable to HasEmail _and_ HasPhoneNumber
  name: "Mike",
  email: "mike@example.com",
  phone: 3215551212
};

otherContactInfo.name; // NOTE: we can access anything on _either_ type
otherContactInfo.email;
otherContactInfo.phone;
```

### Type Systems & Object Shapes

#### Type Systems & Type Equivalence

```ts
function validateInputField(input: HTMLInputElement) {
  /*...*/
}
validateInputField(x);
// can we regard x as an HTMLInputElement?
```

+ **Nominal Type Systems** answer this question based on whether x is an instance of a class/type named HTMLInputElement
+ **Structural Type Systems** only care about the *shape* of an object. This is how typescript works!

#### Object Shapes

+ When we talk about the shape of an object, we're referring to the names of properties and types of their values

```ts
let car:{make: string; model: string; year: number}
```

#### Wider vs. Narrower

+ Describes: relative differences in range of a type’s allowable values

最顶层类型是any，最底层是never，可以在上层类型中使用下层，反之则不允许。既类型只能越来越具体。

### Functions

`open ./notes/2-function-basics.ts`

```ts
// (1) function arguments and return values can have type annotations
function sendEmail(to: HasEmail): { recipient: string; body: string } {
  return {
    recipient: `${to.name} <${to.email}>`, // Mike <mike@example.com>
    body: "You're pre-qualified for a loan!"
  };
}

// (2) or the arrow-function variant
const sendTextMessage = (
  to: HasPhoneNumber
): { recipient: string; body: string } => {
  return {
    recipient: `${to.name} <${to.phone}>`,
    body: "You're pre-qualified for a loan!"
  };
};

// (3) return types can almost always be inferred
function getNameParts(contact: { name: string }) {
  const parts = contact.name.split(/\s/g); // split @ whitespace
  if (parts.length < 2) {
    throw new Error(`Can't calculate name parts from name "${contact.name}"`);
  }
  return {
    first: parts[0],
    middle:
      parts.length === 2
        ? undefined
        : // everything except first and last
          parts.slice(1, parts.length - 2).join(" "),
    last: parts[parts.length - 1]
  };
}

// (4) rest params work just as you'd think. Type must be array-ish
const sum = (...vals: number[]) => vals.reduce((sum, x) => sum + x, 0);
console.log(sum(3, 4, 6)); // 13
```

### Function Signature Overloading

```js
// (5) we can even provide multiple function signatures
// "overload signatures"
function contactPeople(method: "email", ...people: HasEmail[]): void;
function contactPeople(method: "phone", ...people: HasPhoneNumber[]): void;

// "function implementation"
function contactPeople(
  method: "email" | "phone",
  ...people: (HasEmail | HasPhoneNumber)[]
): void {
  if (method === "email") {
    (people as HasEmail[]).forEach(sendEmail);
  } else {
    (people as HasPhoneNumber[]).forEach(sendTextMessage);
  }
}

// ✅ email works
contactPeople("email", { name: "foo", email: "" });

// ✅ phone works
contactPeople("phone", { name: "foo", phone: 12345678 });

// 🚨 mixing does not work
contactPeople("email", { name: "foo", phone: 12345678 });
```

### Lexical Scope

```js
// (6) the lexical scope (this) of a function is part of its signature

function sendMessage(
  this: HasEmail & HasPhoneNumber,
  preferredMethod: "phone" | "email"
) {
  if (preferredMethod === "email") {
    console.log("sendEmail");
    sendEmail(this);
  } else {
    console.log("sendTextMessage");
    sendTextMessage(this);
  }
}
const c = { name: "Mike", phone: 3215551212, email: "mike@example.com" };

function invokeSoon(cb: () => any, timeout: number) {
  setTimeout(() => cb.call(null), timeout);
}

// 🚨 this is not satisfied
invokeSoon(() => sendMessage("email"), 500);

// ✅ creating a bound function is one solution
const bound = sendMessage.bind(c, "email");
invokeSoon(() => bound(), 500);

// ✅ call/apply works as well
invokeSoon(() => sendMessage.apply(c, ["phone"]), 500);
```

## Interface & Type Aliases

### Type Aliases & extends

`open ./notes/3-interface-type-basics.ts`

#### Type Alias

```ts
//== TYPE ALIAS ==//
/**
 * (1) Type aliases allow us to give a type a name
 */
type StringOrNumber = string | number;

// this is the ONLY time you'll see a type on the RHS of assignment
type HasName = { name: string };

// 🚨 self-referencing types don't work! (we'll get there!)
type NumVal = 1 | 2 | 3 | NumArr;
type NumArr = NumVal[];
```

#### extends

```ts
// == INTERFACE == //
/**
 * (2) Interfaces can extend from other interfaces
 */

export interface HasInternationalPhoneNumber extends HasPhoneNumber {
  countryCode: string;
}
```

### Call & Construct Signatures

#### Call Signatures

```ts
/**
 * (3) they can also be used to describe call signatures
 */

interface ContactMessenger1 {
  (contact: HasEmail | HasPhoneNumber, message: string): void;
}

type ContactMessenger2 = (
  contact: HasEmail | HasPhoneNumber,
  message: string
) => void;

// NOTE: we don't need type annotations for contact or message
const emailer: ContactMessenger1 = (_contact, _message) => {
  /** ... */
};
```

#### Construct Signature

```ts
/**
 * (4) construct signatures can be described as well
 */

// interface ContactConstructor {
  new (...args: any[]): HasEmail | HasPhoneNumber;
}
```

### Dictionary Objects & Index Signatures

```ts
/**
 * (5) index signatures describe how a type will respond to property access
 */

/**
 * @example
 * {
 *    iPhone: { areaCode: 123, num: 4567890 },
 *    home:   { areaCode: 123, num: 8904567 },
 * }
 */

interface PhoneNumberDict {
  // arr[0],  foo['myProp']
  [numberName: string]:
    | undefined
    | {
        areaCode: number;
        num: number;
      };
}

const phoneDict: PhoneNumberDict = {
  office: { areaCode: 321, num: 5551212 },
  home: { areaCode: 321, num: 5550010 } // try editing me
};

// at most, a type may have one string and one number index signature
```

### Combining Interfaces

```ts
/**
 * (6) they may be used in combination with other types
 */

// augment the existing PhoneNumberDict
// i.e., imported it from a library, adding stuff to it
interface PhoneNumberDict {
  home: {
    /**
     * (7) interfaces are "open", meaning any declarations of the
     * -   same name are merged
     */
    areaCode: number;
    num: number;
  };
  office: {
    areaCode: number;
    num: number;
  };
}

phoneDict.home;   // definitely present
phoneDict.office; // definitely present
phoneDict.mobile; // MAYBE present

/**
 * (8) Interfaces are initialized lazily, so combining it
 * -   w/ a type alias allows for recursive types!
 */

type StringVal = "a" | "b" | "c" | StringArr;

// type StringArr = StringVal[];
interface StringArr {
  // arr[0]
  [k: number]: "a" | "b" | "c" | StringVal[];
}

const x: StringVal = Math.random() > 0.5 ? "b" : ["a"]; // ✅ ok!
```

## Classes

`open ./notes/4-class-basics.ts`

```ts
// == CLASSES == //

/**
 * (1) Classes work similarly to what you're used to seeing in JS
 * -   They can "implement" interfaces
 */

export class Contact implements HasEmail {
  email: string;
  name: string;
  constructor(name: string, email: string) {
    this.email = email;
    this.name = name;
  }
}
```

### Access Modifiers & Initialization

```ts
/**
 * (3) Access modifier keywords - "who can access this thing"
 *
 * - public - everyone
 * - protected - me and subclasses
 * - private - only me
 */

class ParamPropContact implements HasEmail {
  constructor(
    public name: string,
    public email: string = "no email") {
    // nothing needed
  }
}
```

```ts
/**
 * (4) Class fields can have initializers (defaults)
 */
class OtherContact implements HasEmail, HasPhoneNumber {
  protected age: number = 0;
  // private password: string;
  constructor(
    public name: string,
    public email: string,
    public phone: number) {
    // () password must either be initialized like this, or have a default value
    this.password = Math.round(Math.random() * 1e14).toString(32);
  }
}
```

### Definite Assignment & Lazy Initalization

```ts
/**
 * (4) Class fields can have initializers (defaults)
 */
class OtherContact implements HasEmail, HasPhoneNumber {
  protected age: number = 0;
  private password: string;
  constructor(public name: string, public email: string, public phone: number) {
    // () password must either be initialized like this, or have a default value
    this.password = Math.round(Math.random() * 1e14).toString(32);
  }
}
```

### Abstract Classes

```ts
/**
 * (5) TypeScript even allows for abstract classes, which have a partial implementation
 */

abstract class AbstractContact implements HasEmail, HasPhoneNumber {
  public abstract phone: number; // must be implemented by non-abstract subclasses

  constructor(
    public name: string,
    public email: string // must be public to satisfy HasEmail
  ) {}

  abstract sendEmail(): void; // must be implemented by non-abstract subclasses
}
```

```ts
/**
 * (6) implementors must "fill in" any abstract methods or properties
 */
class ConcreteContact extends AbstractContact {
  constructor(
    public phone: number, // must happen before non property-parameter arguments
    name: string,
    email: string
  ) {
    super(name, email);
  }
  sendEmail() {
    // mandatory!
    console.log("sending an email");
  }
}
```

## Converting to TypeScript

### What not to do

+ Functional changes at the same time
+ Attempt this with low test coverage
+ Let the perfect be the enemy of the good
+ Forget to add tests for your types
+ Publish types for consumer use while they're in a "weak" state

### Compliling in "loose mode"

+ Start with tests passing
+ Rename all .js to .ts, allowing implicit any
+ Fix only things that are not type-checking, or causing compile errors
+ **Be careful to avoid changing behavior**
+ Get tests passing again

### Making Anys Explicit

+ Start with tests passing
+ Ban implicit any(`"noImplicitAny: true,`)
+ Where possible, provide a specific and appropriate type
  + Import types for dependencies from DefinitelyTyped
  + Otherwise explicit any
+ Get tests passing again

### Strict Mode

+ Incrementally, in small chunks...
+ Enable strict mode

```json
"strictNullChecks": true,
"strict": true,
"strictFunctionTypes": true,
"strictBindCallApply": true
```

+ Replace explicit anys with more appropriate types
+ Try really haed to avoid unsafe casts

### Exercise: Address Book

+ We have a address book program that we want to convert form js to ts using our 3-step approach.

`cd ././challenges/address-book/`

## Generics

Generics parameterize types in the same way that functions parameterize values.

+ When to use them
+ Type parameters
+ Constraints

`./notes/5-generics-basics.ts`

```ts
/**
 * (1) Generics allow us to parameterize types in the same way that
 * -   functions parameterize values
 */

// param determines the value of x
function wrappedValue(x: any) {
  return {
    value: x
  };
}

// type param determines the type of x
interface WrappedValue<X> {
  value: X;
}

let val: WrappedValue<string[]> = { value: [] };
val.value;

/**
 * we can name these params whatever we want, but a common convention
 * is to use capital letters starting with `T` (a C++ convention from "templates")
 */

/**
 * (2) Type parameters can have default types
 * -   just like function parameters can have default values
 */

// for Array.prototype.filter
interface FilterFunction<T = any> {
  (val: T): boolean;
}

const stringFilter: FilterFunction<string> = val => typeof val === "string";
stringFilter(0); // 🚨 ERROR
stringFilter("abc"); // ✅ OK

// can be used with any value
const truthyFilter: FilterFunction = val => val;
truthyFilter(0); // false
truthyFilter(1); // true
truthyFilter(""); // false
truthyFilter(["abc"]); // true
```

### Type Parameters

```ts
/**
 * (3) You don't have to use exactly your type parameter as an arg
 * -   things that are based on your type parameter are fine too
 */

function resolveOrTimeout<T>(promise: Promise<T>, timeout: number): Promise<T> {
  return new Promise<T>((resolve, reject) => {
    // start the timeout, reject when it triggers
    const task = setTimeout(() => reject("time up!"), timeout);

    promise.then(val => {
      // cancel the timeout
      clearTimeout(task);

      // resolve with the value
      resolve(val);
    });
  });
}
resolveOrTimeout(fetch(""), 3000);
```

### Constraints & Scope

```ts
/**
 * (4) Type parameters can have constraints
 */

function arrayToDict<T extends { id: string }>(array: T[]): { [k: string]: T } {
  const out: { [k: string]: T } = {};
  array.forEach(val => {
    out[val.id] = val;
  });
  return out;
}

const myDict = arrayToDict([
  { id: "a", value: "first", lisa: "Huang" },
  { id: "b", value: "second" }
]);

/**
 * (5) Type parameters are associated with scopes, just like function arguments
 */

function startTuple<T>(a: T) {
  return function finishTuple<U>(b: U) {
    return [a, b] as [T, U];
  };
}
const myTuple = startTuple(["first"])(42);
```

### Use Cases

```ts
/**
 * (6) When to use generics
 *
 * - Generics are necessary when we want to describe a relationship between
 * - two or more types (i.e., a function argument and return type).
 *
 * - aside from interfaces and type aliases, If a type parameter is used only once
 * - it can probably be eliminated
 */

interface Shape {
  draw();
}
interface Circle extends Shape {
  radius: number;
}

function drawShapes1<S extends Shape>(shapes: S[]) { // use Generics
  shapes.forEach(s => s.draw());
}

function drawShapes2(shapes: Shape[]) { // use basic types
  // this is simpler. Above type param is not necessary
  shapes.forEach(s => s.draw());
}
```

### Exercise: Dictionary

+ `Dict<T>` and Friends
  + A Dictionary (a.k.a. Associative Array) is a collection of key-value pairs, that ensures key uniqueness.
  + Build a Dict that's generic over its value type
  + Higher-order functions on JS Arrays are awesome! Let's also make a mapDict and reduceDict.

## Top & Bottom Types

+ Passing private values through typed code
+ Exhaustive Conditionals
+ Type Guards
+ Branded Types

`open ./notes/6-guards-and-extreme-types.ts`

### Top Types

```ts
//== TOP TYPES ==//

/**
 * (1) "Top types" are types that can hold any value. Typescript has two of them
 */

let myAny: any = 32
let myUnknown: unknown = 'hello, unknown'

// Note that we can do whatever we want with an any, but nothing with an unknown

myAny.foo.bar.baz
myUnknown.foo

/**
 * (2) When to use `any`
 * Anys are good for areas of our programs where we want maximum flexibility
 * Example: sometimes a Promise<any> is fine when we don't care at all about the resolved value
 */
async function logWhenResolved(p: Promise<any>) {
  const val = await p
  console.log('Resolved to: ', val)
}

/**
 * (3) When to use `unknown`
 * Unknowns are good for "private" values that we don't want to expose through a public API.
 * They can still hold any value, we just must narrow the type before we're able to use it.
 *
 * We'll do htis with a type guard.
 */

myUnknown.split(", "); // 🚨 ERROR
```

### Type Guards

```ts
/**
 * (4) Built-in type guards
 */
if (typeof myUnknown === "string") {
  // in here, myUnknown is of type string
  myUnknown.split(", "); // ✅ OK
}
if (myUnknown instanceof Promise) {
  // in here, myUnknown is of type Promise<any>
  myUnknown.then(x => console.log(x));
}

/**
 * (5) User-defined type guards
 * We can also create our own type guards, using functions that return booleans
 */

// 💡 Note return type
function isHasEmail(x: any): x is HasEmail {
  return typeof x.name === "string" && typeof x.email === "string";
}

if (isHasEmail(myUnknown)) {
  // In here, myUnknown is of type HasEmail
  console.log(myUnknown.name, myUnknown.email);
}

// my most common guard
function isDefined<T>(arg: T | undefined): arg is T {
  return typeof arg !== "undefined";
}
```

### Unknow & Branded Types

```ts
/**
 * (6) Dealing with multiple unknowns
 * -   We kind of lose some of the benefits of structural typing when using `unknown`.
 * -   Look how we can get mixed up below
 */

let aa: unknown = 41;
let bb: unknown = ["a", "string", "array"];
bb = aa; // 🚨 yikes

/**
 * (7) Alternative to unknowns - branded types
 * -   One thing we can do to avoid this is to create types with structures that
 * -   are difficult to accidentally match. This involves unsafe casting, but it's ok
 * -   if we do things carefully
 */

/* two branded types, each with "brand" and "unbrand" functions */
interface BrandedA {
  __this_is_branded_with_a: "a";
}
function brandA(value: string): BrandedA {
  return (value as unknown) as BrandedA;
}
function unbrandA(value: BrandedA): string {
  return (value as unknown) as string;
}

interface BrandedB {
  __this_is_branded_with_b: "b";
}
function brandB(value: { abc: string }): BrandedB {
  return (value as unknown) as BrandedB;
}
function unbrandB(value: BrandedB): { abc: string } {
  return (value as unknown) as { abc: string };
}

let secretA = brandA("This is a secret value");
let secretB = brandB({ abc: "This is a different secret value" });

secretA = secretB; // ✅ No chance of getting these mixed up
unbrandB(secretA);
unbrandA(secretB);

// back to our original values
let revealedA = unbrandA(secretA);
let revealedB = unbrandB(secretB);

// 💡 PROTIP - always brand/unbrand casting in exactly one place.
```

### Bottom Types

```ts
//== BOTTOM TYPE: never ==//

/**
 * (8) Bottom types can hold no values. TypeScript has one of these: `never`
 */
let n: never = 4;

/**
 * A common place where you'll end up with a never
 * is through narrowing exhaustively
 */

let x = "abc" as string | number;

if (typeof x === "string") {
  // x is a string here
  x.split(", ");
} else if (typeof x === "number") {
  // x is a number here
  x.toFixed(2);
} else {
  // x is a never here
}

/**
 * (9) We can use this to our advantage to create exhaustive conditionals and switches
 */

class UnreachableError extends Error {
  constructor(val: never, message: string) {
    super(`TypeScript thought we could never end up here\n${message}`);
  }
}

let y = 4 as string | number;

if (typeof y === "string") {
  // y is a string here
  y.split(", ");
} else if (typeof y === "number") {
  // y is a number here
  y.toFixed(2);
} else {
  throw new UnreachableError(y, "y should be a string or number");
}
```

## Advanced Types

### Mapped & Conditional Types, & Type Queries

`open ./notes/7-advanced-types.ts`

```ts
/**
 * (1) MAPPED TYPES allow the use of an interface to transform keys into values
 */

interface CommunicationMethods {
  email: HasEmail
  phone: HasPhoneNumber
  fax: { fax: number }
}

function contact<K extends keyof CommunicationMethods>(
  method: K,
  contact: CommunicationMethods[K] // 💡turning key into value -- a *mapped type*
) {
  //...
}
contact('email', { name: 'foo', email: 'mike@example.com' })
contact('phone', { name: 'foo', phone: 3213332222 })
contact('fax', { fax: 1231 })

// we can get all values by mapping through all keys
type AllCommKeys = keyof CommunicationMethods
type AllCommValues = CommunicationMethods[keyof CommunicationMethods]

/**
 * (2) Type queries allow us to obtain the type from a value using typeof
 */

const alreadyResolvedNum = Promise.resolve(4);

type ResolveType = typeof Promise.resolve;

const x: ResolveType = Promise.resolve;
x(42).then(y => y.toPrecision(2));

/**
 * (3) Conditional types allow for the use of a ternary operator w/ types
 * We can also extract type parameters using the _infer_ keyword
 */

type EventualType<T> = T extends Promise<infer S> // if T extends Promise<any>
  ? S // extract the type the promise resolves to
  : T; // otherwise just let T pass through

let a: EventualType<Promise<number>>;
let b: EventualType<number[]>;
```

### Built-in Utility Types

```ts
//== Built-in Utility Types ==//

/**
 * (4) Partial allows us to make all properties on an object optional
 */
type MayHaveEmail = Partial<HasEmail>;
const me: MayHaveEmail = {}; // everything is optional

/**
 * (5) Pick allows us to select one or more properties from an object type
 */

type HasThen<T> = Pick<Promise<T>, "then" | "catch">;

let hasThen: HasThen<number> = Promise.resolve(4);
hasThen.then;

/**
 * (6) Extract lets us obtain a subset of types that are assignable to something
 */

type OnlyStrings = Extract<"a" | "b" | 1 | 2, number>;

/**
 * (7) Exclude lets us obtain a subset of types that are NOT assignable to something
 */
type NotStrings = Exclude<"a" | "b" | 1 | 2, string>;

/**
 * (8) Record helps us create a type with specified property keys and the same value type
 */
type ABCPromises = Record<"a" | "b" | "c", Promise<any>>;
```

## Declaration Merging

+ How typescript understands your code
+ Stacking values, types and namespaces

`open ./notes/8-declaration-merging.ts`

```ts
/**
 * (1) "identifiers" (i.e., a variable, class, function, interface)
 * -   can be associated with three things: value, type and namespace
 */

function foo() {}
interface bar {}
namespace baz {
  export const biz = 'hello'
}

// how to test for a value
const x = foo; // foo is in the value position (RHS).

// how to test for a type
const y: bar = {}; // bar is in the type position (LHS).

// how to test for a namespace (hover over baz symbol)
baz;

export { foo, bar, baz }; // all are importable/exportable

/**
 * (2) Functions and variables are purely values.
 * -   Their types may only be extracted using type queries
 */
const xx = 4;
const yy: typeof xx = 4;

/**
 * (3) Interfaces are purely types
 */
interface Address {
  street: string;
}

const z = Address; // 🚨 ERROR (fails value test)

/**
 * (4) Classes are both types _and_ values
 */

class Contact {
  name: string;
}

// passes both the value and type tests

const contactClass = Contact; // value relates to the factory for creating instances
const contactInstance: Contact = new Contact(); // interface relates to instances

/**
 * (5) declarations with the same name can be merged, to occupy the same identifier
 */

class Album {
  label: Album.AlbumLabel = new Album.AlbumLabel();
}
namespace Album {
  export class AlbumLabel {}
}
interface Album {
  artist: string;
}

let al: Album; // type test
let alValue = Album; // value test

export { Album }; // 👈 hover over the "Album" -- all three slots filled

/**
 * (6) Namespaces have their own slot, and are also values
 */

// 💡 they can be merged with classes

class AddressBook {
  contacts!: Contact[];
}
namespace AddressBook {
  export class ABContact extends Contact {} // inner class
}

const ab = new AddressBook();
ab.contacts.push(new AddressBook.ABContact());

// 💡 or functions

function format(amt: number) {
  return `${format.currency}${amt.toFixed(2)}`;
}
namespace format {
  export const currency: string = "$ ";
}

format(2.314); // $ 2.31
format.currency; // $
```

## Compiler API

`open ./notes/9-compiler-api.ts`

```ts
import * as path from 'path'
import * as ts from 'typescript'

function isDefined<T>(x: T | undefined): x is T {
  return typeof x !== 'undefined'
}

// (1) Create the program
const program = ts.createProgram({
  options: {
    target: ts.ScriptTarget.ESNext
  },
  rootNames: [
    // path to ../examples/hello-ts/src/index.ts
    path.join(__dirname, '..', 'examples', 'hello-ts', 'src', 'index.ts')
  ]
})

// (2) Get the non-declaration (.d.ts) source files (.ts)
const nonDeclFiles = program
  .getSourceFiles()
  .filter(sf => !sf.isDeclarationFile)

// (3) get the type-checker
const checker = program.getTypeChecker()

/**
 * (4) use the type checker to obtain the
 * -   appropriate ts.Symbol for each SourceFile
 */
const sfSymbols = nonDeclFiles
  .map(f => checker.getSymbolAtLocation(f))
  .filter(isDefined) // here's the type guard to filter out undefined

// (5) for each SourceFile Symbol
sfSymbols.forEach(sfSymbol => {
  const { exports: fileExports } = sfSymbol
  console.log(sfSymbol.name)
  if (fileExports) {
    // - if there are exports
    console.log('== Exports ==')
    fileExports.forEach((value, key) => {
      // - for each export
      console.log(
        key, // - log its name

        // - and its type (stringified)
        checker.typeToString(checker.getTypeAtLocation(value.valueDeclaration))
      )
      const jsDocTags = value.getJsDocTags()
      if (jsDocTags.length > 0) {
        // - if there are JSDoc comment tags
        console.log(
          // - log them out as key-value pairs
          jsDocTags.map(tag => `\t${tag.name}: ${tag.text}`).join('\n')
        )
      }
    })
  }
})
```
