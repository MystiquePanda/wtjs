## Value Properties

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


## Function properties

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


## Fundamental objects

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

const b = Object.defineProperty({},"bomb",{value:"hidden",writable: false});
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
Object.defineProperty(o, Symbol.for('special'), {
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

const proto = {random:true};
const o = Object.create(proto);
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

| term | | 
|--- | --- |
| Function Constructor | new Function('content');
| Function Expression  | let x = function(){}
| Function Declaration | function hoisted() {} |

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
| apply(thisArg [, argsArray]) | |
| bind(thisArg[, arg1[, arg2[, ...argN]]]) |  | 
| call(thisArg[, arg1, arg2, ...argN]) | | 
| toString() | |



### Symbol

### Boolean
