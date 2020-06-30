- [var vs. let](#var-vs-let)
- [Destructuring](#destructuring)
- [(Tagged) String Literals](#-tagged--string-literals)
- [String features](#string-features)
- [Generators](#generators)
- [Iterators](#iterators)
- [Math and Number feature](#math-and-number-feature)
    + [Trigonometric Methods](#trigonometric-methods)
- [Arrow function](#arrow-function)
- [Class and Inheritance](#class-and-inheritance)
  * [Object member](#object-member)
  * [This and class declaration](#this-and-class-declaration)
  * [Inheritance](#inheritance)
  * [Mix-ins](#mix-ins)
- [new data structures](#new-data-structures)
  * [Set](#set)
  * [Map](#map)
  * [Weakset](#weakset)
  * [Weakmap](#weakmap)
- [Async and Promises](#async-and-promises)
  * [Promise](#promise)
    + [Error](#error)
    + [Static methods](#static-methods)
  * [AsyncFunction](#asyncfunction)
- [Proxy and Reflect](#proxy-and-reflect)
  * [Reflect](#reflect)
  * [Proxy](#proxy)
  * [Static Method](#static-method)
- [Modules](#modules)
  * [Illegal Imports & Exports](#illegal-imports---exports)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>




## var vs. let

| var | let |
| --- | --- |
| function-scoped | block-scoped |
| hoisted | temporal deadzoned |
| create global object | don't create global object|
| allow redeclaration in strict | syntaxError in strict mode redeclaration | 


```javascript
function f(){
  console.log("function scope, pre-def: ",vrb);
  var vrb=100000;
  console.log("function scope, post-def: ",vrb);
  if (true){
    console.log("block scope, pre-def: ",vrb);
    var vrb = -100;
    console.log("block scope, post-def: ",vrb);
  }
console.log("function scope, post block: ",vrb);
}

console.log("global scope, pre-def: ",vrb);
var vrb=10;
console.log("global scope, post-def: ",vrb," globalThis.vrb? ",globalThis.vrb);
f();
console.log("global scope, post-function-call: ",vrb);
```

```javascript
var funcs = [];
for (var i = 0; i < 3; i++) {
  funcs[i] = function() {
    console.log("My value: " + i);
  };
}
for (var j = 0; j < 3; j++) {
  funcs[j]();
}
```

## Destructuring

```js
let [a,,[[b,,c],...d]]=[1,[[3,4],[5,6]]]
> Uncaught TypeError: undefined is not iterable (cannot read property Symbol(Symbol.iterator))

let [a,oa,[[b,,c],...d]]=[1,{oa:"one"},[[3,4],[5,6]]];
oa
> {oa: "one"}

let [a,{oa:na="uno",ob:nb="dos"},[[b,,c],...d]]=[1,{oa:"one"},[[3,4],[5,6]]];
oa
> VM3539:1 Uncaught ReferenceError: oa is not defined
[na,nb]
>["one";"dos"]

let o = {random:true};
Object.setPrototypeOf(o,{hidden:":)"});
let {random:r,hidden,missing} = o;
missing
> undefined
hidden
> ":)"
```

for object destructuring, the right hand need to be coercible to an Object.
for array destructuring, the right hand need to be iterable.
```js
let { blowUp } = null
> VM385:1 Uncaught TypeError: Cannot destructure property 'blowUp' of 'null' as it is null.

let { blowUp } = undefined
> VM518:1 Uncaught TypeError: Cannot destructure property 'blowUp' of 'undefined' as it is undefined.

let [a,b] = Number(10)
> Uncaught TypeError: Number is not a function or its return value is not iterable

let { blowUp } = Number;
blowUp
> undefined
```

destructuring and Functions
```js
let f=({a=1,b=2,c})=>{console.log(`c=${c}`); return{a,b}};

let {a=100,b=200,c=300} = f();
> VM4947:1 Uncaught TypeError: Cannot read property 'a' of undefined

let {a=100,b=200,c=300} = f(2);
> c=undefined
[a,b,c]
> [1, 2, 300]

let {a=100,b=200,c=300} = f({b:"neg 2",});
> c=undefined
[a,b,c]
> [1, "neg 2", 300]
```

```js
let [x,y]=["x","y"];
[y,x]=[x,y];
[x,y]
> {"y","x"]


const arr = [0,1,2,3];
const [...clone] = arr;
clone[0] = Infinity;
clone
> [Infinity, 1, 2, 3]
arr
> [0, 1, 2, 3]
```

## (Tagged) String Literals

```js
`\`` === '`'
> true
`${{valueOf:()=>(3),toString:()=>(2)}[Symbol.toPrimitive]=1}`
> "1"

String.raw(`print\nraw`)
> VM9745:1 Uncaught TypeError: Cannot convert undefined or null to object
String.raw`print\nraw\n${new Date()}`
> "print\nraw\nMon May 25 2020 18:15:01 GMT-0400 (Eastern Daylight Time)"
String.raw({raw: [1, 2, Number(3)]}, ' one ', {toString:()=>(' two ')},' three ')
"1 one 2 two 3"

let tagged=(str, ...keys)=>{
console.log("str: ",str);
console.log("keys: ",keys); 
console.log("raw: ",String.raw(str))}
tagged`tagged template ${{complex:true}} \n \u00A9${Number(4)}` //#ES2016
> str:  ["tagged template ", " ↵ ©", "", raw: Array(3)]
> keys: [{complex:true}, 4]
> raw:  tagged template  \n \u00A9
```

Usecase for tagged string literal
```js
function highlight(strings, ...values) {
     // here i is the iterator for the strings array
     return strings.reduce((result, string, i) => {
      return `${result}${string} <cite>${values[i] || ''}</cite>`;
    }, '');
}

const author = 'Thomas A. Edison';
const statement = `I have not failed. I've just found 10,000 ways that won't work.`;
highlight`${statement} by ${author}`;
> " <cite>I have not failed. I've just found 10,000 ways that won't work.</cite> by  <cite>Thomas A. Edison</cite> <cite></cite>"
```
[https://codeburst.io/javascript-es6-tagged-template-literals-a45c26e54761]


## String features

```js
"Some New methods".startsWith("Methods", 9)
> false

"Some New methods".startsWith("Methods".toLowerCase(), 9)
> true

"Some New methods".endsWith("ew ", 9)
> true
"Some New methods".includes("Some", 5)
> false

"Loop".repeat(-2)
> VM119261:1 Uncaught RangeError: Invalid count value
"Loop".repeat(0)
> ""


String.fromCodePoint(9731 /*HTML Entity*/, 0x0001D49F /*UTF-32*/)
> "☃𝒟"

```
## Generators

```js

let generator = function* () { 
  yield 1;
  yield "two";
  yield 3n;
  return false;
}
let g = generator();
[g.next(),g.next(),g.next()]
> [{value: 1, done: false};{value: "two", done: false};{value: 3n, done: false};{value: undefined, done: true}]
g.return()
> {value: false, done: true}

function* fibonacci() {
  const f = [];
  const base = function*() {
    yield f.push(0)-1;  
    yield f.push(1)-1;
  };
      
  yield* base();
  while (true) {
    const {length:l} = f;
    f.push(f[l-2]+f[l-1])
    yield f[l];
  }
}
let f = fibonacci();
for (let i =0; i < 5; i++) {console.log(f.next().value)}
> {value: 3, done: false}
g.return()
> {value: undefined, done: true}

function* fibonacci() {
  let f = [0,1];
  yield* f;
  while (true) {
    const {length:l} = f;
    f.push(f[l-2]+f[l-1]);
    yield f[l];
  }
}

function* gen(){
  let inner = function*() {
    yield* [1,2];
    return "inner done";
  }
  yield* (yield* inner());
  return "done";
}
let g = gen();
for (let i =0; i < 20; i++) {console.log(g.next())}


function withConstructor(){
  const arr = [1,2];
  const con = Object.getPrototypeOf(function*(){}).constructor;
  const g = new con('yield* arr');
  const iterator = g();
  let i;
  do{
    i = iterator.next(); 
    console.log(`from generator ${i.value}`)
  }while(!i.done); 
}
withConstructor()
> Uncaught ReferenceError: arr is not defined

function asGenerator(){
const arr = [1,2];
const Generator = function*(){yield* arr}
const iterator = Generator();
let i = true;
  do{
    i = iterator.next(); 
    console.log(`from generator ${i.value}`)
  }while(!i.done); 
}
asGenerator()
> from generator 1
> from generator 2
> from generator undefined


function* gen(i){
  console.log(`print ${i}`);
  const j = 5*(yield(i*10));
  console.log(`print ${j}`);
  const k = yield(2*j/4);
  console.log(`print ${k}`);
  return(i+j+k);
}
let g = gen(10);
g.next(20);
> print 10
> {value: 100, done: false}
g.next(10);
> print 50
> {value: 25, done: false}
g.next(5);
> print 5
> {value: 65, done: true}
```

## Iterators
| protocol | property | |
| --- | --- | --- |
| Iterable | [Symbol.iterator] | A zero-argument function that returns an 'Iterator' object|
| Iterator | next() | a zero-argument function that return {value:,done:boolean}|

A generator object is both *iterator* and *iterable*

```js
let weekdays = new String("monday to friday");
weekdays[Symbol.iterator] = function* () {
  yield* ['m','t','w','th','f'];
};
[...weekdays]
> (5) ["m", "t", "w", "th", "f"]
weekdays+""
> "monday to friday"

weekdays[Symbol.iterator] = function () {
  return {
    next: function () {
      return this._first ? {
        value: 'no weekend',
        done: (this._first = false)
      } : {
        done: true
      }
    },
    _first: true
  };
};
[...weekdays]
> ["no weekend"]

/#ES2015 class syntax
class WeekClass {
  *[Symbol.iterator]() {
    yield* ['m','t','w','th','f'] 
  }
}
[...new WeekClass()]
> (5) ["m", "t", "w", "th", "f"]
```

## Math and Number feature

```js
0xF     //#ES5
> 15
0b1111  //#ES2015
> 15
0o17    //#ES2015
> 15
> Number('0b1111')
> 15

Number.parseInt('111', 2)
> 7
Number.parseInt('111', 8)
> 73
Number.parseFloat(

Number.isFinite('123')
> false
Number.isNaN('???')
> false
Number.isInteger('33')


0.1 + 0.2 === 0.3
> false
Math.abs(0.1 + 0.2- 0.3)<Number.EPSILON
> true

//−2^53^ < i < 2^53^
Number.MAX_SAFE_INTEGER
> 9007199254740991
Number.MIN_SAFE_INTEGER
> -9007199254740991
Number.isSafeInteger(Number.MIN_SAFE_INTEGER-1)
> false

Math.sign(0)
> 0
Math.sign(NaN)
> NaN
Math.trunc(3.1)
> 3
Math.cbrt(8)
> 2
> Math.expm1(1e-10) // more precise than Math.exp
1.00000000005e-10
Math.log1p(0)       // more precise than Math.log
> 0
Math.log2(8)
> 3
Math.log10(8)
> 0.9030899869919435
Math.fround(1.3)    //32 bit floating point
> 1.2999999523162842
//multiply two 32 bit int and returns the lower 32 bits 
Math.imul(Number.MAX_SAFE_INTEGER,Number.MAX_SAFE_INTEGER)
> 1
//count leading 0 in 32 bit Int
Math.clz32(0b01000000000000000000000000000000)
> 1
```
#### Trigonometric Methods 
- Math.sinh(x)
- Math.cosh(x)
- Math.tanh(x)
- Math.asinh(x)
- Math.acosh(x)
- Math.atanh(x)
- Math.hypot(...values)

## Arrow function

```js
[(x=>1)(),(x=>(1))(),(()=>1)(),(x=>{1})()]
> (4) [1, 1, 1, undefined]

let f = ([a, b] = [1, 2], {x: c} = {x: a + b}) => {a:a,b:b,c:c};
f()
> {a: 1, b: 2, c: 3}

let f = (a, b, c)
  => 1;
> Uncaught SyntaxError: Unexpected token '=>'

//short circuit
let callback=()=>{};
callback = callback || function() {};
callback = callback || () => {};
> Uncaught SyntaxError: Malformed arrow function parameter list
callback = callback || (() => {});
```

difference between Arrow and function
- doesn't have this (lexically bind their context)
- doesn't have argument
- doesn't have prototype

for function method, good general rule:
- Use non-arrow functions for methods that will be called using the object.method() syntax. Those are the functions that will receive a meaningful this value from their caller.
- Use arrow functions for everything else.

```js
(function(){return `arguments ${[...arguments]}`})(1,2,3,4)
> "arguments 1,2,3,4"
(()=>`argument ${[...arguments]}`)(1,2,3,4)
> Uncaught ReferenceError: arguments is not defined


let o = {
  helper:function(c){console.log("Helper called from "+c)},
  func:function(){
    let innerfunc= function(){this.helper("func")}.bind(this);
    innerfunc();
  },
  arrow:function(){
    let innerfunc=()=>this.helper("arrow");
    innerfunc();
  }
}
o.func()
> Helper called from func
undefined
o.arrow()
> Helper called from arrow

console.log((() => {}).prototype);
> undefined
console.log((function(){}).prototype); 
> {constructor: ƒ}
```

## Class and Inheritance

*a class is actually a function under the hood in JavaScript*
*...and functions are objects*

| | function | class |
| --- | --- | --- |
| hoisted | declaration hoisted | declaration NOT hoisted |
| strict mode | need to specify into strict mode | strict mode is enforced within the class body's syntactic|
| inheritance | [parent].call() | extends / super() | 

### Object member
four types of property/method: 
- defined inside a constructor function that are given to object instances
- defined directly on the constructor themselves, that are available only on the constructor *static properties/methods*
- Defined on a constructor's prototype: inherited by all instances and inheriting object classes
- available on an object instance


### This and class declaration

Syntax notes:
- must call super() if there's a constructor
- when there's a setter, the data property need to be a different name to distinguish from the setter.

```js
class SomeClass extends ParentClass {
  constructor(myArgs, parentArgs) { 
    super(parentArgs); 
    this._myArgs = myArgs;   
  }

  get myArgs(){return this._myArgs}
  set myArgs(arg){this._myArgs = args}
}
```

```js
class ClassObj { 
  m() {return this;}
  static sm() {return this;}
}

// Autoboxing: if this=undefined, this = globalThis, 
// Autoboxing disabled when in strict mode
let o = new ClassObj();
let objm = o.m;
let staticm = ClassObj.sm;
[o.m(),objm(),ClassObj.sm(),staticm()]
> [ClassObj, undefined, ƒ, undefined]

function FuncObj() { }
FuncObj.prototype.m = function() { return this;}
FuncObj.sm = function() { return this;}

let o = new FuncObj();
let objm = o.m;
let staticm = FuncObj.sm;
[o.m(),objm(),FuncObj.sm(),staticm()]
> [FuncObj, Window, ƒ, Window]
```

### Inheritance

- super(), can only be used in constructors, and must be called before the this keyword can be used.

```js
function Animal (name) {
  this.name = name;  
}

Animal.prototype.speak = function () {
  console.log(`${this.name} is an Animal.`);
}

class Mammal extends Animal {
  speak() {
    console.log(`${this.name} is a Mammal .`);
  }
}

let p = new Animal('Dinosaur');
p.speak(); 
> Dinosaur is an Animal.
let c = new Mammal('Yoda');
c.speak();
> Yoda is a Mammal.

let Dinosaur = {
  roar() {
    console.log(`${this.name} is a Dinosaur.`);
  }
};

Object.setPrototypeOf(Animal.prototype, Dinosaur);

let h = new Animal('Chicken');
h.roar();
> Chicken is a Dinosaur.

[c instanceof Mammal;c instanceof Animal]
> [true;true]
[p.constructor === Mammal;p.constructor === Animal]
> [true;false]


class MyArray extends Array {
  # override default constructors
  static get [Symbol.species]() { return Array; }
}
let a = new MyArray(1,2,3);
let mapped = a.map(x => x * x);
[mapped instanceof MyArray,mapped instanceof Array]
> [false,true]

```

### Mix-ins

```js
function Factory(type) {
  return class {
    printType() { return type }
  }
}
class Robot extends Factory("Robo") {
  speak(){console.log(`I'm a ${this.printType()}`);}
}
let r = new Robot();
r.speak()
> I'm a Robo


class SportsEquipment {
    type(){
      return "SportsEquipment";
    }
}

let redMixin = Base => class extends Base {
  color() { return "red" }
  print() { return "redMixin" }
};

let ballMixin = Base => class extends Base {
  shape() { return "ball" }
  print() { return "ballMixin" }
};

class Basketball extends ballMixin(redMixin(SportsEquipment)) { }
let b = new Basketball()
[b.color(),b.shape(),b.print()]
> ["red", "ball", "ballMixin"]
```

## new data structures

### Set

```js
let s = new Set()
s.add({})
s.add({})
s.add(null)
s.add(undefined)

for (let [key, value] of s.entries()) console.log(key,value)
> {} {}
> {} {}

let s1 = new Set([1, 2, 3, 4]);
let s2 = new Set([2, 4, 6, 8, 9]);
let intersection = new Set([...s1].filter(x => s2.has(x)));
intersection
> Set(2) {2, 4}
let difference = new Set([...s1].filter(x => !s2.has(x)));
difference
> Set(2) {1, 3}

(new Set([NaN,undefined,null,,NaN])).has(NaN)
> true
```

### Map
insertion order are perserved when iterating

```js
let m = new Map();
m['random'] = false;
m.set('random',true);
m.set(NaN,"notANumber")
m.set(undefined,"undef")
m.set(null,"hasValue?")
m.set(Symbol.for("special"),true)
[m.get(NaN),m.get(undefined),m.get(null),m.get(Symbol.for("special"))]
>  ["notANumber", "undef", "hasValue?", true]
m.get(Number("broken"))
> "notANumber"

m.size=1;
m.size
> 5

let original = new Map([[1, {id:'one'}]])
let clone = new Map(original)
original === clone
> flase
original.get(1)["name"]='one'
clone.get(1)
> {id: "one", name: "one"}


let first = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
])
let second = new Map([
  [1, 'uno'],
  [2, 'dos']
])
new Map([...first, ...second, [1, 'eins']])
> Map(3) {1 => "eins", 2 => "dos", 3 => "three"}

```

### Weakset
```js
let ws = new WeakSet();
ws.add(1);
> Uncaught TypeError: Invalid value used in weak set
ws.add({})
> WeakSet {{}}
```

*Use case*: Detecting circular references
```js
function execRecursively(fn, subject, _refs = null){
  if(!_refs)
    _refs = new WeakSet();
	
  // Avoid infinite recursion
  if(_refs.has(subject))
    return;

  fn(subject);
  if("object" === typeof subject){
    _refs.add(subject);
    for(let key in subject)
      execRecursively(fn, subject[key], _refs);
  }
}
```

### Weakmap

```js
let wm = new WeakMap()
wm.set(1,"one")
> Uncaught TypeError: Invalid value used as weak map key
wm.set(globalThis, 1)
> WeakMap {Window => 1}
```
*Use case*: Hide Implementation
```js
const privates = new WeakMap();

function Public() {
  const me = {
    // Private data goes here
  };
  privates.set(this, me);
}

Public.prototype.method = function () {
  const me = privates.get(this);
  // Do stuff with private data in `me`...
};

module.exports = Public;
```


## Async and Promises

### Promise

Callbacks inside-out

```js
let pp = new Promise(
 (fulfilledCB,rejectedCB)=>{
    setTimeout(()=>{ rejectedCB();console.log("done")}, 3000);})
/*depends on which callback is called, the state of promise will change.*/
```
State of Promise:
- pending: initial state
- fulfilled: operation completed successfully -  "resolved"
- rejected: operation failed.

| Method | Description |
| --- | --- |
| .then(fulfilledCB[, rejectedCB]) | fulfilledCB(value), rejectedCB(reason) called asynchronously| 
| .catch(error) | same as .then(null,fail) |
| .finally() | |

```js
Promise.reject()
  .then(() => 100, () => -100)
  .then(solution => console.log('Resolved with ' + solution))
> Resolved with -100

// doesn't error even when CB aren't valid
Promise.resolve().then({jibberish:true})
> Promise {<resolved>: undefined}

// async execution
let resolvedProm = Promise.resolve(33);
let thenProm = resolvedProm.then(value => {
    console.log("called after the end of the main stack. the value received and returned is: " + value);
    return value;
});

// postpone the execution to when the stack is empty
let t = setTimeout(() => {
    console.log("postponed",thenProm);
});
console.log("instant",thenProm);
> instant Promise {<pending>}
> called at the end of the main stack. the value received and returned is: 33
> postponed Promise {<resolved>: 33}

// async execution
Promise.resolve('dinosaurs')
  .then(s=>{
    return new Promise(function(res, rej) {
      setTimeout(function() {
        s += ' rule';
        res(s);
      }, 1);
    });
  })
  .then(s=>{
    setTimeout(function() {
      s += 'd';
      console.log("misconception: ",s);
    }, 1)
    return s;
  }) 
  .then(s=>{
    console.log("Listen! ");
    console.log("fact:",s);
  });
Promise {<pending>}
> Listen! 
> fact: dinosaurs rule
> misconception:  dinosaurs ruled

//order matters with exception handling
Promise.resolve()
  .then(() => {
    throw new Error('Kaboom!');
  })
  .catch(e => {
    console.error('Fire!Fire!  ' + e.message);
  })
  .then(() => {
    console.log("Looks like all good now");
  });
> Fire!Fire!  Kaboom!
> Looks like all good now


/* returns another pending promise object, the resolution/rejection of the promise returned by then will be subsequent to the resolution/rejection of the promise returned by the handler */
let rej = Promise.reject();
let p = new Promise((s,r)=>{setInterval(s,1000)}).then(s=>{return rej},r=>{})
p
> Promise {<rejected>: undefined}
```

#### Error

When a Promise fails one of the two events (type: PromiseRejectionEvent) are sent to global scope
- rejectionhandled
- unhandledrejection

```js
window.onunhandledrejection = e => console.log("globally heard a unhandled rejection from promise ",e)
window.onrejectionhandled = e => console.log("globally heard a rejection from promise ",e)
let p = new Promise((s,r)=>{r("no good reason")}).then(()=>{console.log("this won't print")})
> globally heard a unhandled rejection from promise  PromiseRejectionEvent {isTrusted: true, promise: Promise, reason: "no good reason", type: "unhandledrejection", target: Window, …}

/*TODO can't seem to generate rejectionhandled*/
let p = new Promise((s,r)=>{r("no good reason")}).catch((e)=>{console.log("this will print")})
> this will print
```

#### Static methods

```js
let p1 = Promise.resolve("uno");
let p2 = "dos";
let p3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log("done with p3")
    resolve("tres");
  }, 3000);
}); 
setTimeout(
()=>{
  console.log("stack is empty")
  Promise.all([p1,p2,p3]).then(res =>console.log("async ", res))
  Promise.all([]).then(res => console.log("empty iterator is sync"))
  })
> stack is empty
> empty iterator is sync
> done with p3
> async ["uno", "dos", "tres"]

// Fast fail
let p1 = new Promise((resolve, reject) => { 
  setTimeout(() => resolve('ten sec'), 10000); 
}); 
let p2 = new Promise((resolve, reject) => { 
  setTimeout(() => resolve('one sec'), 1000); 
});
let pf = new Promise((resolve, reject) => {
  setTimeout(() => reject(new Error('reject, one.five sec')),1500);
});
Promise.all([p1, p2, pf])
.then(v => console.log(v))
.catch(e => console.error(e.message));
> reject, one.five sec

// catching fails 
let p1 = new Promise((resolve, reject) => { 
  setTimeout(() => resolve('ten sec'), 10000); 
}); 
let p2 = new Promise((resolve, reject) => { 
  setTimeout(() => resolve('one sec'), 1000); 
});
let pf = new Promise((resolve, reject) => {
  setTimeout(() => reject(new Error('reject, one.five sec')),1500);
});
Promise.all([p1,p2,pf.catch(e => e),]).then(values => { 
  console.log(values[0]) // "p1_delayed_resolution"
  console.log(values[1])
  console.error(values[2]) // "Error: p2_immediate_rejection"
})
> ten sec
> one sec
> Error: reject, one.five sec
    
    
let p1 = Promise.resolve("uno");
let p2 = "dos";
let p3 = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log("done with p3")
    resolve("tres");
  }, 3000);
}); 
setTimeout(
()=>{
  console.log("stack is empty")
  Promise.allSettled([p1,p2,p3]).then(res =>console.log("async ", res))
  })
> stack is empty
> done with p3
> 0: {status: "fulfilled", value: "uno"}
> 1: {status: "fulfilled", value: "dos"}
> 2: {status: "fulfilled", value: "tres"}

let p1 = new Promise((s,r) => setTimeout(s, 500, 'one'))
let p2 = new Promise((s,r) => setTimeout(s, 1000, 'two'))
Promise.race([p1, p2]).then(v => console.log(v));
> one

let p1 = new Promise((s,r) => s("one"))
let p2 = new Promise((s,r) => setTimeout(s, 1000, 'two'))
Promise.race([p1, p2]).then(v => console.log(v));
> one

Promise.race([]).then(v => console.log(v));
> Promise {<pending>}

let forever = Promise.race([]);
let fulfilled = Promise.resolve(100);

let p = Promise.race([forever, fulfilled, "non-Promise value"]);
let p2 = Promise.race([forever, "non-Promise value", fulfilled]);
console.log([p,p2]);
setTimeout(function(){
    console.log('the stack is now empty');
    console.log("p: ", p);
    console.log("p2: ", p2);
});
> [Promise {<pending>},Promise {<pending>}]
> the stack is now empty
> p:  Promise {<resolved>: 100}
> p2:  Promise {<resolved>: "non-Promise value"}
```

### AsyncFunction

```js
Object.getPrototypeOf(async function(){})
> AsyncFunction {Symbol(Symbol.toStringTag): "AsyncFunction", constructor: ƒ}

function after2Seconds() {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('resolved');
    }, 2000);
  });
}

async function asyncCall() {
  console.log('calling:');
  const result = await after2Seconds();
  console.log(result);
}

asyncCall();
> calling:
> resolved


let f = (async () => (1))()
f
> Promise {<resolved>: 1}


async function f() {
 const result1 = await new Promise((resolve) => setTimeout(() => resolve('1'),3000))
 console.log("result1: ",result1); 
 const result2 = await new Promise((resolve) => setTimeout(() => resolve('2')))
  console.log("result2: ",result2);
}
f()
console.log("this is the end")

> this is the end
> result1:  1
> result2:  2

//use Promise.all or Promise.allSettled when dealing with multiple promise
let f = async () => {
   const p1 = new Promise((s) => setTimeout(() => s('1'), 2000))
   const p2 = new Promise((_,r) => setTimeout(() => r('2'), 500))   
   const results = [await p1, await p2]  
}
f().catch(() => {}) 
> Promise {<pending>}
// error will show up after p2 is resolved, before p1 is resolved

// parallel execution
let slow = () => {
  console.log("start slow..");
  return new Promise(s=>{setTimeout(()=>s("slow done"),3000)});
}
let fast = () => {
  console.log("start fast..");
  return new Promise(s=>{setTimeout(()=>s("fast done"),5000)});
}
let parallel = async () => {
  await Promise.all([
      (async()=>console.log(await slow()))(),
      (async()=>console.log(await fast()))()
  ])
  console.log("all done")
}
```

## Proxy and Reflect

### Reflect

```js
//ES5
Function.prototype.apply.call(Math.floor, undefined, [1.75]);
Reflect.apply(Math.floor, undefined, [1.75]);
> 1
```

| Method Name | Object | Reflect|
| --- | --- | --- | 
| defineProperty() | returns the object that was passed to the function if successful, raise TypeError if failed to create property. | returns true if successful and false otherwise.|
| defineProperties() | same as above | N/A |
| set() | N/A | returns boolean based on if operation was successful. *Throws a TypeError* if the target was not an Object. |
| get() | N/A | returns boolean based on if operation was successful. *Throws a TypeError* if the target was not an |
| deleteProperty() | N/A | returns boolean based on if operation was successful | 
| getOwnPropertyDescriptor() |returns a property descriptor of the given property if it exists on the object argument passed in, and returns undefined if it does not exist. However, if an object is not passed in as the first argument, it will be coerced into an object. |  returns a property descriptor of the given property if it exists on the object and undefined if it does not. *Throw a TypeError* if anything other than an object (a primitive) is passed in as the first argument.|
|getOwnPropertyDescriptors() | returns an object containing a property descriptor of each passed-in object. Returns an empty object if the passed-in object has no owned property descriptors. | N/A | 
| getPrototypeOf() | returns the prototype of the given object. Returns null if there are no inherited properties.  coerces non-objects | returns the prototype of the given object. Returns null if there are no inherited properties, and *throws a TypeError* for non-objects. | 
|setPrototypeOf()| returns the object itself if its prototype was set successfully. Throws a TypeError if the prototype being set was anything other than an Object or null, or if the prototype for the object being modified is non-extensible.| returns true if the prototype was successfully set on the object and false otherwise. *Throws a TypeError* if the target passed in was not an Object, or if the prototype was anything other than an Object or null.|
|isExtensible()|returns true if the object is extensible, and false otherwise. If the first argument is not an object (a primitive), it will be coerced into a non-extensible, ordinary object and return false.|returns true if the object is extensible, and false if it is not. *Throws a TypeError* if the first argument is not an object (a primitive).|
| preventExtensions()	| returns the object that is being made non-extensible. If the argument is not an object (a primitive) treats the argument as a non-extensible, ordinary object and returns the object itself.|returns true if the object has been made non-extensible, false otherwise. *Throws a TypeError* if the argument is not an object (a primitive).|
|keys() | returns an Array of strings that map to the target object's own (enumerable) property keys. If the target is not an object, coerces into objects| N/A|
|ownKeys()| N/A | returns an Array of property names that map to the target object's own property keys. *Throws a TypeError* if the target is not an Object.|
	
	
### Proxy
```js
let target = {
  k: "basic",
  tbr: "special"
};
let handler = {
  get: function(target, prop) {
    return prop in target? target[prop] : "magic"
  }
};

let p = new Proxy(target, handler);
console.log([p.k,p.new]);
> ["basic", "magic"]

let refHandler = {
  get: function(target, prop, receiver){ 
       return prop === "tbr" ? "replaced value": Reflect.get(...arguments)
    }
}
let p = new Proxy(target, refHandler);
console.log([p.k,p.tbr])
> ["basic","replaced value"]
```

| Handler / trap | Interceptions | Invariants |
| --- | --- | --- |
| getPrototypeOf() | Object.getPrototypeOf(), Reflect.getPrototypeOf(),__ proto __ ,Object.prototype.isPrototypeOf(),instanceof | If target is not extensible, Object.getPrototypeOf(proxy) method must return the same value as Object.getPrototypeOf(target). | 
| setPrototypeOf() | Object.setPrototypeOf(), Reflect.setPrototypeOf() |  |
| isExtensible()| Object.isExtensible(), Reflect.isExtensible()	| must return the same value as Object.isExtensible(target).
| preventExtensions() | Object.preventExtensions(), Reflect.preventExtensions() | returns true if Object.isExtensible(proxy) is false. | 
| getOwnPropertyDescriptor() | Object.getOwnPropertyDescriptor(),Reflect.getOwnPropertyDescriptor() | must return an object or undefined | 
| defineProperty() | Object.defineProperty(), Reflect.defineProperty() | must adhere to property's configurable policy |
|has()	| Reflect.has() | | 
| get()	 | Reflect.get() | behavior need to adhere to property property |
| set()	 | | | 
| deleteProperty() | delete proxy[prop], delete proxy.prop, Reflect.deleteProperty()| cannot delete non-configurable property | 
| enumerate() | Reflect.enumerate() | | 
| ownKeys() | Object.getOwnPropertyNames(), Object.getOwnPropertySymbols(), Object.keys(), Reflect.ownKeys() | | 
| apply()|proxy(..args), Function.prototype.apply() and Function.prototype.call(), Reflect.apply() | |
| construct() | new proxy(...args), Reflect.construct()| | 
		
### Static Method

```js
let revocable = Proxy.revocable(target, {
  get: function(target, prop) {
    return prop in target? "[[" + Reflect.get(...arguments) + "]]"  : "proxy magic" ;
  }
});
console.log(revocable.proxy.nonexisting);
> proxy magic

revocable.revoke();
console.log(revocable.proxy.nonexisting);
> Uncaught TypeError: Cannot perform 'get' on a proxy that has been revoked
proxy.foo = 1
> Uncaught TypeError: Cannot perform 'set' on a proxy that has been revoked
delete proxy.foo;
> Uncaught TypeError: Cannot perform 'deleteProperty' on a proxy that has been revoked
typeof proxy
> "object"
```

**Use Case: Validation**

```js
let validator = {
  set: function(obj, prop, value) {
    if (prop === 'age') {
      if (!Number.isInteger(value)) {
        throw new TypeError('The age is not an integer');
      }
      if (value > 200) {
        throw new RangeError('The age seems invalid');
      }
    }
    obj[prop] = value;
    return true;
  }
};

let person = new Proxy({}, validator);
person.age = 'young';
> Uncaught TypeError: The age is not an integer
person.age = 300;
> Uncaught TypeError: The age seems invalid
person.age = 100
```
other use cases:
- Extending constructor
- Manipulating DOM nodes
- Value correction and an extra property


## Modules

- All exported identifiers must be explicitly exported by name, can’t programmatically loop through an array and export names in a data-driven way.
	- Named Exports (Zero or more exports per module)
	- Default Exports (One per module)
- Modules are singletons. Even if a module is imported multiple times, only a single “instance” of it exists.
- Imports and exports must be at the top level. structure of ES6 modules is static, can't conditionally import different modules
- Module imports are hoisted 
- imports of a module are read-only views on the exported entities
- default exports and unqualified named imports works in #ES2016 modules, unlike its predecessors 
- All of a module’s dependencies are loaded, parsed, and linked eagerly, before any module code runs.

[HTML and Java script loading](https://flaviocopes.com/javascript-async-defer/)

|        | Script | Modules |
|   ---    | --- |---- |
|HTML element|<script>|<script type="module">|
|Default mode|non-strict|strict|
|Top-level variables are|global|local to module|
|Value of this at top level|window|undefined
|Executed|synchronously|asynchronously|
|Declarative imports (import statement)|no|yes|
|Programmatic imports (Promise-based API)|yes|yes|
|File extension|.js|.js|

```js
//export any top-level function, class, var, let, or const
export function f() {}
export class c {}
export let v1 = …, v2 = …
export const v1 = …, v2 = …
export var v1 = …, v2 = …

export { name1, name2, …, nameN };

// Renaming exports
export { variable1 as name1, variable2 as name2, …, nameN };

// Exporting destructured assignments with renaming
export const { name1, name2: bar } = o;

// Default exports - default export is just another named export
export default expression;
export default function (…) { … } // also class, function*
export default function name1(…) { … } // also class, function*
export { name1 as default, … };

// Aggregating modules
export * from …; // does not set the default export
export * as name1 from …; // Draft ECMAScript® 2O21
export { name1, name2, …, nameN } from …;
export { import1 as name1, import2 as name2, …, nameN } from …;
export { default } from …;

// Re-exporting 
export { name1, name2 as myName2 } from 'src/other_module';
```

```js
// Default import
  import localName from 'src/my_lib';
  
// Namespace import: imports the module as an object (with one property per named export).
  import * as my_lib from 'src/my_lib';
  
// Named imports:
  import { name1, name2 } from 'src/my_lib';

// Rename imports:
  import { name1 as localName1, name2 } from 'src/my_lib';
  import { default as foo } from 'src/my_lib';

// Empty import: only loads the module, doesn’t import anything. 
// The first import executes the body of the module.
  import 'src/my_lib';
 
```

### Illegal Imports & Exports

```js
import ... from getModuleName(); // Error, only from "string" is allowed
export default const variable1 = 1, variable1 = 2, variable1 = 3;

if(...) {
  import ...; // Error, not allowed!
}
```
