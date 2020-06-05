
```js
[1] + [1] - [1]
> 10
```
'+' acts as both addition and concatenation depends on argument type
'-' will force argument into number

```js
('b'+'a'+ + 'a' +'a').toLowerCase()
> "banana"
```
'+' acts as both addition and concatenation depends on argument type


```js
['1','7','11'].map(parseInt)
> [1, NaN, 3]
```
map passes (current_value, current_index, array) and parseInt takes (number,radix)


