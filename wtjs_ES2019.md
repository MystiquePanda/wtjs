
## Class

### private and public property 
Private fields can only be declared up-front in a field declaration.

```js
class classObj {
  #privateProp = 0;
  publicProp=1;
  constructor(pri, pub) {    
    this.#privateProp = pri;
    this.publicProp = pub;
    this.undecPub = pub;
    this.#undecPri = pri;
  }
}
> Uncaught SyntaxError: Private field '#undecPri' must be declared in an enclosing class

let o = new classObj(-10,10);
o.publicProp
> 10
o.privateProp
> undefined
o.undecPub
> 10
```
