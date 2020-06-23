## Dynamic Imports

```js
// import looks like a function but it's a special syntax that just happen to use ()

let modulePath = prompt("Which module to load?");
import(modulePath)
  .then(obj => <module object>)
  .catch(err => <loading error, e.g. if no such module>)

// Programmatic aka Dynamic Imports inside Async func
module = await import(modulePath)
let { default:newFuncName } = await import(modulePath);
```

## GlobalThis

a cross platform/evironment way to access the top-most parent object


## import.meta

Object with null prototype that contains meta information on the module. The object is extensible, and its properties are writable, configurable, and enumerable.
