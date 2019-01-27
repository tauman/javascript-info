# Destructuring Arrays

```js
const ary = [1, 2, 3];

const [x, y] = [1, 2, 3];

console.log('\ndestructureArray1()');
console.log(`x: ${x} y: ${y}`);
console.log();
```

```js
const [x, , y] = [1, 2, 3];

console.log('\ndestructureArray2()');
console.log(`x: ${x} y: ${y}`);
console.log();
```

```js
const [first, ...rest] = ary;

console.log('\ndestructureArray3()');
console.log(`first: ${first}`);
console.log(`rest: ${rest}`);
console.log();
```

