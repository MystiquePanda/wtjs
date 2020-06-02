- [var vs. let](#var-vs-let)
- [Destructuring](#destructuring)
- [(Tagged) String Literals](#-tagged--string-literals)
- [String features](#string-features)
- [Generators](#generators)
- [Iterators](#iterators)
- [Math & Number feature](#math---number-feature)
    + [Trigonometric Methods](#trigonometric-methods)
- [Arrow function](#arrow-function)
- [new data structures](#new-data-structures)
- [Class & Inheritance](#class---inheritance)
- [Async and Promises](#async-and-promises)

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

## Math & Number feature

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
(()=>(1))()
> 1

let f = ([a, b] = [1, 2], {x: c} = {x: a + b}) => ({a:a,b:b,c:c});
f()
> {a: 1, b: 2, c: 3}

let f = (a, b, c)
  => 1;
> Uncaught SyntaxError: Unexpected token '=>'

let callback=()=>{};
callback = callback || function() {};
callback = callback || () => {};
> Uncaught SyntaxError: Malformed arrow function parameter list
callback = callback || (() => {});
```

difference between Arrow and function
- doesn't have this
- doesn't have argument
- doesn't have prototype

for function method, good general rule:
- Use non-arrow functions for methods that will be called using the object.method() syntax. Those are the functions that will receive a meaningful this value from their caller.
- Use arrow functions for everything else.
```js
let f = function(){return `arguments ${[...arguments]}`}
f(1,2,3,4)
> "arguments 1,2,3,4"
let f = ()=>`argument ${[...arguments]}`
f(1,2,3,4)
> Uncaught ReferenceError: arguments is not defined


et o = {
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

let f = () => {};
console.log(f.prototype); 
```

## Class & Inheritance

*In fact, a class is actually a function under the hood in JavaScript*

| | function | class |
| | --- | --- |
| hoisted | declaration hoisted | declaration NOT hoisted |
| strict mode | need to specify into strict mode | within the class body's syntactic is always executed in strict mode |
| inheritance | [parent].call() | extends / super() | 

### Object member
four types of property/method: 
- defined inside a constructor function that are given to object instances
- defined directly on the constructor themselves, that are available only on the constructor *static properties/methods*
- Defined on a constructor's prototype: inherited by all instances and inheriting object classes
- those available on an object instance


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

let o = new ClassObj();
let objm = o.m;
let staticm = Obj.sm;
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
function Human (name) {
  this.name = name;  
}

Human.prototype.speak = function () {
  console.log(`${this.name} is Human.`);
}

class Child extends Human {
  speak() {
    console.log(`${this.name} is a child.`);
  }
}

let c = new Child('Yoda');
c.speak(); 
> Yoda is the child.
let p = new Parent('Yoda');
p.speak()
> Yoda is the parent.

let Animal = {
  roar() {
    console.log(`${this.name} is an Animal.`);
  }
};

Object.setPrototypeOf(Human.prototype, Animal);

let h = new Human('Adam');
h.roar();
> Adam is an Animal.

c instanceof Child
> true

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


## Async and Promises



