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

#### Creation

Object constructor rules:
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
> [10, -10]


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

Symbol('unique') === Symbol('unique')
> false
Symbol.for('common') === Symbol.for('common')
> true

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

### BigInt
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
- converted to a Boolean: via the Boolean function;
- logical operators ||, &&, and !; or
- conditional test ie. if

```js
JSON.stringify(BigInt(1)) 
> Uncaught TypeError: Do not know how to serialize a BigInt
BigInt.prototype.toJSON = function() { return this.toString()  }
JSON.stringify(BigInt(1)) 
> ""1""
```

### Math

*Math works with the Number type. It doesn't work with BigInt*
All properties and methods of Math are static!


| |  -5.05| 0.59 | null | undefined| |
| --- | --- | --- | --- | --- | --- |
|Math.floor(x) | -6 | 0 | 0 | NaN | |
|Math.ceil(x)  | -5 | 1 | 0 | NaN | |
|Math.round(x) | -5 | 1 | 0 | NaN | rounded to the nearest int |
|Math.trunc(x) | -5 | 0| 0 | NaN | integer part by removing decimals |
|Math.fround(x) | -5.050..| 0.589.. | 0 | NaN | nearest 32-bit single precision float |

- Math.random()
- Math.abs(x)
- Math.max([x[, y[, …]]]), Math.min([x[, y[, …]]])
- Math.pow(x, y)
- Math.sign(x)
- Math.sqrt(x), Math.cbrt(x)
- Math.clz32(x)
- Math.exp(x), Math.expm1(x)
- Math.imul(x, y)
- Math.log(x), Math.log1p(x), Math.log10(x), Math.log2(x)
- Math.hypot([x[, y[, …]]])

| Trignometric | hyperbolic #ES2015 |
| --- | --- |
| Math.cos(x)| Math.cosh(x) |
| Math.sin(x)| Math.sinh(x)    |
| Math.tan(x)| Math.tanh(x) |
| Math.asin(x)| Math.asinh(x) |
| Math.acos(x)| Math.acosh(x) |
| Math.atan(x)| Math.atanh(x) |
| Math.atan2(y, x)|     |


### Date

*epoch: midnight on January 1, 1970, UTC*

```js
Date.now()  //milliseconds since epoch
1591470914447
```

#### Instance Methods 
```js
let d = new Date(Date.UTC(2020, 5, 8, 19, 10, 0));  // month is 0 indexed
> Mon Jun 08 2020 15:10:00 GMT-0400 (Eastern Daylight Time)

let options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
d.toLocaleString('de-DE', options)
"Montag, 8. Juni 2020"
```
#### Date Parts

| getter | setter | UTC getter | UTC setter |
| --- | --- | --- | --- |
|getFullYear() |setFullYear() |getUTCFullYear() |setUTCFullYear()  |
|getYear()| setYear() | | |
|getMonth() |setMonth() |getUTCMonth() | setUTCMonth() |
|getDate()| setDate()|getUTCDate() |setUTCDate()|
|getDay() | |getUTCDay()  |  |
|getTime() |setTime() | |  |
|getHours() |setHours() |getUTCHours() | setUTCHours() |
|getMinutes() |setMinutes() |getUTCMinutes() | setUTCMinutes() |
|getSeconds() |setSeconds() |getUTCSeconds() | setUTCSeconds() |
|getMilliseconds() |setMilliseconds() |getUTCMilliseconds() | setUTCMilliseconds() |
|getTimezoneOffset() | | |  |

#### Printing Dates

| method | description | output |
| --- | --- | --- | 
| toDateString() | "date" portion of the Date | "Mon Jun 08 2020" | 
| toISOString() | ISO 8601 Extended Format | "2020-06-08T19:10:00.000Z" |
| toJSON() | used by JSON.stringify() | "2020-06-08T19:10:00.000Z" |
| toGMTString() | deprecated. Use toUTCString() instead |  |
| toUTCString() | string using the UTC timezone | "Mon, 08 Jun 2020 19:10:00 GMT" |
| toLocaleDateString() | based on system settings | "6/8/2020" |
| toLocaleFormat() | deprecated |  |
| toLocaleString() | Overrides Object.prototype.toLocaleString() | "6/8/2020, 3:10:00 PM" |
| toLocaleTimeString() | locality-sensitive representation of the time | "3:10:00 PM" |
| toTimeString() | "time" portion in system locale | "15:10:00 GMT-0400 (Eastern Daylight Time)" |
| toString() | | "Mon Jun 08 2020 15:10:00 GMT-0400 (Eastern Daylight Time)" |
| valueOf() | | 1591643400000 |


# Text Processing

### String

```js
[typeof "wtjs", typeof new String("wtjs")]
> (2) ["string", "object"]

"wtjs".padEnd(10,"*")
> "wtjs******"
```

### RegExp

```js
let r = /ab+c/i;
let rr = new RegExp(/ab+c/, 'i') // literal notation
let rrr = new RegExp('ab+c', 'i') // constructor
[typeof r,Object.getPrototypeOf(r).constructor.name]
> ["object", "RegExp"]

"John Smith".replace(/(\w+)\s(\w+)/, "$2, $1")
>"Smith, John"

let text = 'Some text\nAnd some more\r\nAnd yet\rThis is the end'
text.split(/\r\n|\r|\n/)
> (4) ["Some text", "And some more", "And yet", "This is the end"]

let url = 'http://mysub.domain.com'
/[^.]+/.exec(url)[0].substr(7)
> "mysub"

// special array as output
// RegExp.exec(), String.match(), and String.replace()
let r = /d(b+)(d)/i;
let res = r.exec('cdbBdbsbz')
> (3) ["dbBd", "bB", "d", index: 1, input: "cdbBdbsbz", groups: undefined]
```

# Collections

### Array

#### Static methods

if it quacks (a value) looks like a duck (an array) then it is a duck (an array)

```js
Array.isArray(new Int8Array())
> false

// isArray Polyfill
if (!Array.isArray) {
  Array.isArray = function(arg) {
    return Object.prototype.toString.call(arg) === '[object Array]';
  };
}

let arrLike = {v:[1,2,3],[Symbol.iterator]:function*(){yield*this.v}
Array.from(arrLike,x=>x+x)
> [2, 4, 6]

Array.of(1, 2, 3, undefined);
> [1, 2, 3, undefined]
```

#### Instance Method and properties 

```js
let arr = [...Array(5).keys()]
arr.length=10
> (10) [0, 1, 2, 3, 4, empty × 5]
arr.length = 3
> (3) [0, 1, 2]

let holey = ['Hello']
holey[99] = 'world';
holey.length
> 100
```


| instance method | output | note | 
| --- | --- | --- |
| .map((item, index, array)=>{}),this) | Array |  | 
| .every((item, index, array)=>{})) | boolean | |
| .some((item, index, array)=>{},this) | boolean | |
| .filter((item, index, array)=>{},this) | Array | |
| .forEach(item, index, array)=>{},this) | void |  | 
| .find(item, index, array)=>{},this) | item |  | 
| .findIndex(item, index, array)=>{},this) | void |  | 
| .reduce((accu, item, index, array)=>{},initAccu)| accu | Array[0] = initAccu if not provided, when initAccu isn't provided, iteration is called one less time than otherwise,excluding holes | 
| .reduceRight((accu, item, index, array)=>{},initAccu) | accu | excluding holes| 


```js
// skip exclude holes(empty) during iteration 
[1,3,,'last',undefined].forEach((e) => console.log(e))
> 1
> 3
> last
> undefined

// only sync. callbacks
let arr = [5, 4, 100];
let sum = 0;
let sumFunction = async (a, b) => a+b
arr.forEach(async (r) => sum = await sumFunction(sum, r))
sum
> 100

// iteration scope, defined on first iteration
let arr = [1,2,3];
arr.map((c,i,a)=>{
a.push(101);
return c>100})
> (3) [false, false, false]

let arr = [5, 4, 7, 11, 2, 6]
arr.filter( (e, index, arr) => {
  //console.log(arr)
  arr.pop()
  return e < 6
})
> [5,4]

let arr = [5, 4, 7, 11, 2, 6]
arr.filter( (e, i, arr) => {
  arr[Math.min(i+1,arr.length-1)]+=10
  //console.log(arr)
  return e < 6
})
> [5]

[{x:22},{x:42},{x:-100}].map( e => e.x )
.reduce(( max,cur) => Math.max( max, cur), -Infinity );
> 42

[100,"abc",new  Boolean(false)].some(x=>!x)
> false

```

- **back**: .pop() , .push()
- **front**: .shift(), .unshift()
- **by index**: .splice(pos, n)
- **shallow copy**: .slice()
- **search**: .indexOf(), lastIndexOf()

```js
['a', 'b', 'c'].concat(1, [2, 3]);
> ["a", "b", "c", 1, 2, 3]

//works like memmove
[1, 2, 3, 4, 5].copyWithin(-4)
> [1, 2, 3, 1, 2]
[1, 2, 3, 4, 5].copyWithin(0, 3)
> [4, 5, 3, 4, 5]
[1, 2, 3, 4, 5].copyWithin(0, 3, 4)
> [4, 2, 3, 4, 5]
[1, 2, 3, 4, 5].copyWithin(-2, -3
> [1, 2, 3, 3, 4]
[].copyWithin.call({length: 5, 3: 1}, 0, 3)
> {0: 1, 3: 1, length: 5}


[1, 2, 3, 4, 5].fill(4, -3, -1)
> [1, 2, 4, 4, 5]
[1, 2, 3, 4, 5].fill(4, 3, 6)
> [1, 2, 3, 4, 4]
Array(5).fill(4)
> [4, 4, 4, 4, 4]
[].fill.call({ length: 3 }, 4)
> {0: 4, 1: 4, 2: 4, length: 3}
let arr = Array(3).fill({})
arr[0].clone = "hi"
> [{ clone: "hi" }, { clone: "hi" }, { clone: "hi" }]

[1, 2, NaN].includes(NaN) 
> true
```

#### Uniform Typed Array

- Int8Array, Uint8Array, Uint8ClampedArray
- Int16Array, Uint16Array
- Int32Array, Uint32Array
- Float32Array, Float64Array
- BigInt64Array, BigUint64Array

### Map
*see [wtjs_ES2015](https://github.com/MystiquePanda/wtjs/blob/master/wtjs_ES2015.md#map)*

### Set
*see [wtjs_ES2015](https://github.com/MystiquePanda/wtjs/blob/master/wtjs_ES2015.md#set)*

### WeakMap 
*see [wtjs_ES2015](https://github.com/MystiquePanda/wtjs/blob/master/wtjs_ES2015.md#weakmap)*

### WeakSet
*see [wtjs_ES2015](https://github.com/MystiquePanda/wtjs/blob/master/wtjs_ES2015.md#weakset)*



# Control abstraction objects
### Promise & Async
*see [wtjs_ES2015](https://github.com/MystiquePanda/wtjs/blob/master/wtjs_ES2015.md#async-and-promises)*

### Generator
*see [wtjs_ES2015](https://github.com/MystiquePanda/wtjs/blob/master/wtjs_ES2015.md#generators)*

# Reflection
*see [wtjs_ES2015](https://github.com/MystiquePanda/wtjs/blob/master/wtjs_ES2015.md#proxy-and-reflect)*



# Structured Data

### ArrayBuffer
### SharedArrayBuffer 
### Atomics 
### DataView
### JSON

# Internationlization
# WebAssembly

