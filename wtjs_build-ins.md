# Value Properties

### infinity

```js
Infinity === Number.POSITIVE_INFINITY
> true

Infinity === Number.NEGATIVE_INFINITY
> false
```

### NaN

```js
NaN === Number.NaN
> false

NaN === NaN
> false
```
The only value in javascript that does not equal to itself

### undefined
one of the six primitives

```js
undefined === globalThis.undefined
> true
```

|                          | var x; | var x=null; | undeclared ||
| --- | --- | --- | --- | --- |
| x === undefined          | true   | false|Reference Error||
| x == undefined            | true   | true |Reference Error||
| typeof x === "undefined" | true   | false|true| preferred way|
| "x" in globalThis          | true   | true|false|when x is bound to global object|
| x === void 0             | true   | false|Reference Error|void evaluate expression and return undefined|


### globalThis
a cross environment (node.js, browser, web worker...) way of accessing the global context. 
**NOT supported by IE**


# Function properties

### eval()
**eval() is dangerous and slow - it invokes JS interpreter, DO NOT USE**
instead create a Function.

```js
let val="{c:new Date()}"
eval(val)
> {c: Fri May 22 2020 18:09:55 GMT-0400 (Eastern Daylight Time)}
Function("return "+val)()
> {c: Fri May 22 2020 18:09:55 GMT-0400 (Eastern Daylight Time)}
```

### uneval()

### isFinite() & Number.isFinite()
#ES2015

```js
Number.isFinite === globalThis.isFinite
> false

Number.isFinite('0')
> false

isFinite('0')
> true
```
Number.isFinite to returns false for all the typeof input !== 'Number'


### isNaN() & Number.isNaN()
#ES2015

```js
isNaN(Number('one'))
> true

Number.isNaN(Number('one'))
> true

isNaN('one')
> true

Number.isNaN('one')
> false

isNaN(undefined)
> true

Number.isNaN(undefined)
> false
```
Number.isNaN returns false for all the typeof input !== 'Number'

### parseFloat()

```js
Number.parseFloat === parseFloat
> true

parseFloat("3.14andmore")
> 3.14

parseFloat(3.3333333333333333333333333333333)
> 3.3333333333333335

parseFloat("infinity")
> NaN

parseFloat("Infinity")
> Infinity

parseFloat("-0")
> -0

typeof parseFloat("9999n")
> "number"
```

non-primitive objects can be parsed as well as long as Symbol.toPrimitive, toString, valueOf (in this order) is defined to return parsable String or Number. 

```js
parseFloat({ toString: function() { return "3.14" } })
> 3.14

let o={toString: function() { return "3.14" } }
o[Symbol.toPrimitive]=function(){return 10}
parseFloat(o)
> 10

parseFloat({valueOf:function(){return 10}})
> NaN

parseFloat({valueOf:function(){return 10}, toString:undefined})
> 10

parseFloat(Symbol(3.14))
> VM1177:1 Uncaught TypeError: Cannot convert a Symbol value to a string

parseFloat({valueOf:undefined, toString:undefined})
VM4867:1 Uncaught TypeError: Cannot convert object to primitive value
```

### parseInt()

```js
parseInt === Number.parseInt
> true

parseInt("-0X10")
> -16

parseInt(1,36)
> 1

parseInt(1,37)
> NaN

parseInt(3,2)
> NaN

parseInt(Number.MAX_SAFE_INTEGER)
> 9007199254740991

parseInt(Number.MAX_VALUE)
> 1

```
when used on objects, same Symbol.toPrimitive, toString(), valueOf() rules apply (see parseFloat)

### encodeURI()

UTF-8 encoding

```js
encodeURI("A-Za-z0-9;,/?:@&=+$-_.!~*()#'")
> "A-Za-z0-9;,/?:@&=+$-_.!~*()#'"
```
when used on objects, same Symbol.toPrimitive, toString(), valueOf() rules apply (see parseFloat)

### encodeURIComponent()

UTF-8 encoding

```js
encodeURIComponent("A-Za-z0-9;,/?:@&=+$-_.!~*()#'")
> "A-Za-z0-9%3B%2C%2F%3F%3A%40%26%3D%2B%24-_.!~*()%23'"
```
when used on objects, same Symbol.toPrimitive, toString(), valueOf() rules apply (see parseFloat)

### decodeURI()

```js
decodeURI("%A4")
> VM2183:1 Uncaught URIError: URI malformed

decodeURI(encodeURIComponent("?"))
> "%3F"
```
when used on objects, same Symbol.toPrimitive, toString(), valueOf() rules apply (see parseFloat)

### decodeURIComponent()

utility function for decoding query param

```js
function decodeQueryParam(p) {
  return decodeURIComponent(p.replace(/\+/g, ' '));
}

decodeURIComponent('search+query%20%28correct%29')
> "search+query (correct)"

decodeQueryParam('search+query%20%28correct%29');

> "search query (correct)"
```

when used on objects, same Symbol.toPrimitive, toString(), valueOf() rules apply (see parseFloat)


# Fundamental objects

### Object

#### Object Creation

Object constructor rules
1. new Object(null|undefined) => empty object
2. return object of a given type
3. if the value is an object, it will return the value

```js
let x = {random: true}
let y = new Object(x)
y.random = false;
x.random
> false

let oempty = Object.create({});
let onull = Object.create(null);
let oundefined = Object.create(undefined);
> VM56748:1 Uncaught TypeError: Object prototype may only be an Object or null: undefined
oempty.toString()
> "[object Object]"
onull.toString()
> VM56604:1 Uncaught TypeError: y.toString is not a function
Object.setPrototypeOf(onull, Object.prototype); 
onull.toString()
> "[object Object]"

const source = { a: 1, b: 2, c: 3 };
Object.fromEntries(Object.entries(source).map(([ key, val ]) => [ key, val * 2 ]))
> {a: 2, b: 4, c: 6}
```

#### Static Methods

- Property definition

|| optional keys | default |  |
| --- | --- | --- | --- |
||enurable| false | appear in Object.keys() when true and property name is String|  |
||configurable| false | if property can be deleted or options can be modified |  | 
|data descriptor| writable | false | if value is modifiable after creation | 
|data descriptor| value | undefined |  | 
|accessor descriptor| get | undefined |  | 
|accessor descriptor| set | undefined |  | 

```js
let o = {};
Object.defineProperty(o,"tobedel",{value:null, configurable: false, enumerable: true});
delete o.tobedel;
o.tobedel;
> null
"use strict"; delete o.tobedel
> VM1126:1 Uncaught TypeError: Cannot delete property

Object.defineProperty(o, Symbol.for("strange"),{value: true, enumerable: true});
Object.keys(o);
> ["tobedel"]


function memory() {
  let l = null;
  let r = [];

  Object.defineProperty(this, 'val', {
    get() {
      return r;
    },
    set(value) {
      l = value;
      r.push({ val: l });
    }
  });
}
let m = new memory();
m.val = 'first';
m.val = 'second';
m.val
> 0: {val: "first"}
> 1: {val: "second"}

function o() {}
Object.defineProperty(o.prototype, "protoprop", {
  get() {
    return this.prop;
  },
  set(x) {
    this.prop = x;
  }
});
var a = new o();
a.protoprop = 1;
a
> o {prop: 1}
> prop: 1
> __proto__:
>   protoprop: 1

```

```js
let o = { [Symbol.for('prime')]: 13,  "even":2}
Object.getOwnPropertyDescriptor(o,Symbol.for('prime')); 
> {value: 13, writable: true, enumerable: true, configurable: true}
Object.getOwnPropertyDescriptor(o,"even"); 
> {value: 2, writable: true, enumerable: true, configurable: true}
Object.getOwnPropertyDescriptor(o,"notthere"); 
> undefined
Object.getOwnPropertyDescriptors(o); 
> even: {value: 2, writable: true, enumerable: true, configurable: true}
> Symbol(prime): {value: 13, writable: true, enumerable: true, configurable: true}

//#ES2015, a non-object  will be coerced to an object.
Object.getOwnPropertyDescriptor("noError",2)
> {value: "E", writable: false, enumerable: true, configurable: false}
Object.getOwnPropertyDescriptor(Symbol.for('noError'),0)
> undefined
```

*Shallow* copy of all *enumerable data properties* can be done using Object.assign().
```js
let s1 = {[Symbol.for("s")]:"special","s":"normal","one":true}
let s2 = {[Symbol.for("s")]:"special2","s":"normal2","two":true}
let t = {"three":true}
Object.assign(t, s1, s2)
{three: true, s: "normal2", one: true, two: true, Symbol(s): "special2"}

Object.assign({},"string",true,10,10n,true,undefined,null,Infinity,NaN)
> {0: "s", 1: "t", 2: "r", 3: "i", 4: "n", 5: "g"}

let b = Object.defineProperty({},"bomb",{value:"hidden",writable: false});
Object.assign(b, { a: 0 }, { b: 1, bomb: "kaboom", c: 2 }, { d: 3 });
> VM1165:1 Uncaught TypeError: Cannot assign to read only property 
b
{a: 0, b: 1, bomb: "hidden"}
```

- Object Immutability

|                   | Object.freeze | Object.seal | Object.preventExtention|
| ---               | --- | --- | --- |
| add properties    | NO            | NO          | NO                     |
| delete property   | NO            | NO          | YES                    |
| writable          | false         | unchanged   | unchanged              |
| configurable.     | false         | false       | unchanged              | 
| checking method   | isFrozen()    | isSealed()  | isExtensible()         | 

All three state are **irriversible** and set **shallowly**

```js
function deepFreeze(object) {
  Object.getOwnPropertyNames(object).forEach(name => {
    var prop = object[name];
    
    if (prop && typeof prop === 'object')
      deepFreeze(prop);
  });
  
  return Object.freeze(object);
}
```

- Inspection

```js
let x = {}
Object.defineProperties(x,{
  visible:{value:true, enumerable: true}, 
  hidden:{value: false, enurable: false}});
Object.defineProperty(x, Symbol.for('special'), {
  value: Infinity, enumerable: true});

Object.keys(x)
> ["visible"]
Object.values(x)
> [true]
Object.entries(x)
> [Array(2)]
>  0: (2) ["visible", true]

Object.getOwnPropertyNames(x)
> (2) ["visible", "hidden"]
Object.getOwnPropertySymbols(x)
> [Symbol(special)]
```
- value-comparison
 #ES2015
```js
0 === -0
> true
Object.is(0,-0)
> false

Number.Nan === Number.NaN
> false
Object.is(Number.NaN, Number.NaN)
> true

```

- Prototype manipulations

```js
let o = Object.create(null)
Object.getPrototypeOf(o)
> null

let proto = {random:true};
let o = Object.create(proto);
console.log(Object.getPrototypeOf(o) === proto);
> true

// #ES2015 Non-object types get coerced into object
Object.getPrototypeOf(Symbol.for("special"));
> Symbol {Symbol(Symbol.toStringTag): "Symbol", constructor: ƒ, toString: ƒ, valueOf: ƒ, …}
Object.getPrototypeOf(3);
> Number {0, constructor: ƒ, toExponential: ƒ, toFixed: ƒ, toPrecision: ƒ, …}
Object.getPrototypeOf(undefined);
> VM6961:1 Uncaught TypeError: Cannot convert undefined or null to object

```


```js
Object.appendChain = function(oChain, oProto) {
  if (arguments.length < 2) { 
    throw new TypeError('Object.appendChain - Not enough arguments');
  }
  if (typeof oProto !== 'object' && typeof oProto !== 'string') {
    throw new TypeError('second argument to Object.appendChain must be an object or a string');
  }

  let oNewProto = oProto,
      oReturn = o2nd = oLast = oChain instanceof this ? oChain : new oChain.constructor(oChain);

  for (let o1st = this.getPrototypeOf(o2nd);
    o1st !== Object.prototype && o1st !== Function.prototype;
    o1st = this.getPrototypeOf(o2nd)
  ) {
    o2nd = o1st;
  }

  if (oProto.constructor === String) {
    oNewProto = Function.prototype;
    oReturn = Function.apply(null, Array.prototype.slice.call(arguments, 1));
    this.setPrototypeOf(oReturn, oLast);
  }

  this.setPrototypeOf(o2nd, oNewProto);
  return oReturn;
}

let p = 17;
let np =Object.appendChain(p, MySymbol={
  isPrime:true
});
[typeof p, typeof np]
> (2) ["number", "object"]
```

#### Instance properties

| properties | |
| --- | --- |
| constructor | reference to the function itself. read-only for primitive values |
| __ __proto__ __ | recommended to use Object.getPrototypeOf() instead|
| __ __noSuchMethod__ __ | non-standard & deprecated. Proxy instead | 

```js
(new Array).constructor === Array
> true

```

#### Instance methods (Object.prototype)

| methods | |
| --- | --- |
| __ __defineGetter__ __() | deprecated, Object.defineProperty() | 
| __ __defineSetter__ __() | deprecated, Object.defineProperty() |
| __ __lookupGetter__ __() | to be deprecated. returns getter of property |
| __ __lookupSetter__ __() | to be deprecated. returns setter of property |
| hasOwnProperty() | true if the specified property is a direct property | 
| isPrototypeOf() | check if argument object is descends from caller prototype |
| propertyIsEnumerable() | check if argument property is enumerable and is the caller's own property. |
| toLocaleString() | toString() by default, meant to be overriden |
| toString() | string representing the object, default "[object type]"|
| unwatch() | deprecated, use setter/getter or proxy |
| valueOf() | returns the primitive value of the specified object |
| watch() |  deprecated, use setter/getter or proxy |

### Function

| term | | hoisted? |
|--- | --- | --- |
| Function Constructor | new Function('content') | NO | 
| Function Expression  | let x = function(){} | NO | 
| Function Declaration | function myFunc(){} | YES | 

Function constructor do not create closures to their creation contexts - created in the global scope.
```js
let x = 10;
function funcConstruction() {
    let x = -10;
    return new Function('return x;');
}
function funcDeclaration() {
    let x = -10;
    return function f() {return x;};
}

[funcConstruction()(),funcDeclaration()()]
> [10, 20]


var hoisted = "func" in this;
console.log(`'func' ${hoisted ? "is" : "is not"} hoisted. typeof func is ${typeof func}`);
if (false) {
  function func(){ return 1; }
}
> 'func' is hoisted. typeof func is undefined

```


#### Instance properties
| properties | | 
| --- | --- |
| function.arguments | Deprecated. use arguments object instead | 
| function.caller |  Deprecated |
| function.displayName | non-standard |
| function.name| don't use when code are being minified | 
| function.length| return expected argument number | 

```js
// Array-like but not an Array
(function(){console.log(arguments)})()
> callee: ƒ ()
> length: 0
> Symbol(Symbol.iterator): ƒ values()
> __proto__: Object

function longestString() {
  var longest = '';
  for (var i=0; i < arguments.length; i++) {
    if (arguments[i].length > longest.length) {
      longest = arguments[i];
    }
  }
  return longest;
}

(function func(a = 55) {
  console.log(arguments[0]);
  arguments[0] = 99;
  console.log(a);
})(-10)
> -10
> -10

(function func(a = 55) {
  console.log(arguments[0]);
})()
> undefined


(function(){}).length
> 0 
(function(...args) {}).length
> 0
(function(a = 1, b, c) {}).length
> 0
```

#### Instance methods (Function.prototype)
| methods | |
| --- | --- | 
| bind(thisArg[, arg1[, arg2[, ...argN]]]) | creates a new bound function|  
| call(thisArg[, arg1, arg2, ...argN]) | accepts an argument list | 
| apply(thisArg [, argsArray]) | accepts a single array of arguments. use null or undefined when no args needed|
| toString() | |

```js
let base = [0, 1, 2];
base.push.apply(base, ['three', 'four', 'five'])
base
> (5) [0, 1, 2, "three", "four", "five"]

// argument limit of 65536!
Math.max.apply(null, [5, 6, 2, 3, 7]);

// create a new object with array rather than lots of arguments
Function.prototype.construct = function(aArgs) {
  let oNew = Object.create(this.prototype);
  this.apply(oNew, aArgs);
  return oNew;
};

let o = {random: true}
let f = function(){return this.random}
[f(),f.bind(o)()]
> (2) [undefined, true]
[Object.getOwnPropertyDescriptors(f.bind(o)),Object.getOwnPropertyDescriptors(f)]
> ["bound f","f"]
```

### Symbol
"primitive data type"

```js
let u = new Symbol('unique')
> VM422:1 Uncaught TypeError: Symbol is not a constructor

JSON.stringify({[Symbol('foo')]: 'foo'})
> "{}"

let sym = Symbol('random')
let obj = {[sym]: false}
obj[sym]
obj[Object(sym)]
```

#### Static Properties & Methods

| Property | Return Type | Description | Usage |
| --- | --- | --- | --- | 
| Symbol.asyncIterator | method | object's default AsyncIterator | await...of | 
| Symbol.hasInstance | method | if a constructor recognizes the argument as its instance | instanceof | 
| Symbol.isConcatSpreadable | Boolean | if an object should be flattened to its array elements | Array.prototype.concat() | 
| Symbol.iterator | method | default iterator for an object | for...of | 
| Symbol.match | method | matches against a string, also to determine if an object may be used as a regex | String.prototype.match() | 
| Symbol.matchAll | method | returns an iterator, that yields matches of the regex against a string | String.prototype.matchAll() | 
| Symbol.replace | method | replaces matched substrings of a string | String.prototype.replace() | 
| Symbol.search | method | the index within a string that matches the regex | String.prototype.search() | 
| Symbol.species | constructor function | used to create derived objects | |
| Symbol.toPrimitive | method | converting an object to a primitive value | |
| Symbol.toStringTag | string | the default description of an object | Object.prototype.toString() | 
| Symbol.unscopables | object | property names that are excluded from the environment bindings of the associated object | |


```js
Symbol('unique') === Symbol('unique')
> false
Symbol.for('common') === Symbol.for('common')
> true

Symbol.keyFor(Symbol.for('common'))
> "common"
```

#### Instance Properties & Methods

```js
Symbol.for('unique').description
> "unique"

Object.getPrototypeOf(Symbol.for('unique'))
> Symbol {Symbol(Symbol.toStringTag): "Symbol", 
> constructor: ƒ, toString: ƒ, valueOf: ƒ, …}
> constructor: ƒ Symbol()
> description: (...)
> toString: ƒ toString()
> valueOf: ƒ valueOf()
> Symbol(Symbol.toPrimitive): ƒ [Symbol.toPrimitive]()
> Symbol(Symbol.toStringTag): "Symbol"
> get description: ƒ description()
> __proto__: Object
```

### Boolean

primitive Boolean true & false !== true & false of Boolean object

```js
// Any object that is not undefined or null
let x = new Boolean(false);
if (x) {
  console.log("fake truth");
}
> fake truth

[Boolean(0), Boolean(-0), Boolean(null), Boolean(false), Boolean(NaN), Boolean(undefined), Boolean('')]
> (7) [false, false, false, false, false, false, false]
[Boolean(true), Boolean('true'), Boolean('false'), Boolean('random'), Boolean([]), Boolean({})]
> (6) [true, true, true, true, true, true]
```

### Error objects

- Error
- AggregateError 
- EvalError
- InternalError 
- RangeError
- ReferenceError
- SyntaxError
- TypeError
- URIError


# Numbers and dates

### Number

```js
123 === 123.0 
> true

Number(254).toString(16)
>"fe"
Number(-0xff).toString(2)
> "-11111111"

// locale and the fallback language
Number(12345.6789).toLocaleString(['ban', 'id'])
"12.345,679"

// locale and number type
Number(12345.6789).toLocaleString('de-DE', { style: 'currency', currency: 'EUR' })
> "12.345,68 €"

// local locale and config
Number(12345.6789).toLocaleString(undefined, {minimumFractionDigits: 2, maximumFractionDigits: 2})
> "12,345.68"

```
  
| caller| args | toFixed() | toPrecision() |
| --- | --- | --- | --- |
|Number(3.14159)| none | "3" | "3.14159" |
|Number(3.14159)| 0 | "3" | RangeError |
|Number(3.14159)| 4 | "3.1416" | "3.142" |
|Number(3.14159)| 7 | "3.1415900" | "3.141590" |
|Number(3.14159)| 101 | RangeError | RangeError |
|Number(100) | 1 | "100.0" | "1e+2" |
|Number(100) | 3 | "100.000"| "100" |
|Number(2.35) | 1 | "2.4" | "2" |
|*Number(2.55)* | 1 | "2.5" | "3" |
|Number(-2.35) | 1 |"-2.4"| "-2" |
|-2.35 | 1| -2.4 | -2 | 

*Floating point precision issue*

## BigInt
2^53 - 1

```js
1n+1n*10
> Uncaught TypeError: Cannot mix BigInt and other types, use explicit conversions

5n / 2n
> 2n

1n+2n*10n%7n-5n**2n
> -18n
5n&13n|5n
>5n

let mixed = [4n, 6, -12n, 10, 4, 0, 0n]
mixed.sort()
> [-12n, 0, 0n, 10, 4n, 4, 6]
mixed.sort((a, b) => a - b) 
> Uncaught TypeError: Cannot mix BigInt and other types, use explicit conversions
mixed.sort((a, b) => (a < b) ? -1 : ((a > b) ? 1 : 0))
> [-12n, 0, 0n, 4n, 4, 6, 10]

let max = 2n ** (10n - 1n) - 1n;
BigInt.asIntN(10, max);
> 511n
BigInt.asIntN(10, max + 1n);
> -512n

let max = 2n ** 64n - 1n;
BigInt.asUintN(64, max)
>18446744073709551615n
BigInt.asUintN(64, max+1n) 
> 0n //overflow
```

A BigInt behaves like a Number in cases where:
-converted to a Boolean: via the Boolean function;
-logical operators ||, &&, and !; or
-conditional test ie. if

```js
JSON.stringify(BigInt(1)) 
> Uncaught TypeError: Do not know how to serialize a BigInt
BigInt.prototype.toJSON = function() { return this.toString()  }
JSON.stringify(BigInt(1)) 
> ""1""
```

# Text Processing
# Collections
# Structured Data
# Reflection
# Control abstraction objects
# Internationlization
# WebAssembly

```

