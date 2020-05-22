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
