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
Number('one')
> NaN

isNaN(NaN)
> true

isNaN(Number.NaN)
> true

NaN === Number.NaN
> false

isNaN(Number('one'))
> true

Number.isNaN(Number('one'))
> true

isNaN('one')
> true

Number.isNaN('one')
> false
```
in order for Number.isNaN to return true, 

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
** NOT supported by IE **


## Function properties

### eval()

### uneval()

### isFinite()

```js
Number.isFinite === globalThis.isFinite
> false
Number.isFinite
```
### isNaN()
see 
[NaN]:( /#nan)

### parseFloat()

### parseInt()

### decodeURI()

### decodeURIComponent()

### encodeURI()

### encodeURIComponent()


## Fundamental objects
