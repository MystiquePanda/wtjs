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
o.__proto__.hidden=":)";
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

### Class & Inheritance

### Template string

### String feature

### Math & Number feature

### new data structures

### Generators

### Iterators

### Async and Promises

### Arrow function

### 
