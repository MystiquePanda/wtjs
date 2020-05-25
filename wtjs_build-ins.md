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
| x == undfined            | true   | true |Reference Error||
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
```



### Function

### Symbol

### Boolean
