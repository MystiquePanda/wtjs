### var vs. let

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

### Destructuring

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

### (Tagged) String Literals

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


### String features

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

### Class & Inheritance

### Math & Number feature

### new data structures

### Generators

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

```

### Iterators
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


### Async and Promises

### Arrow function

