# Destructuring Objects

```js
const object = {
    a: 'value a',
    b: 'value b'
};

const { a, b } = object;

console.log('\ndestructureObject1()');
console.log(`"a: "${a}" b: "${b}"`);
console.log();
```

```js
const object = {
    a: 'value a',
    b: 'value b'
};

const { a: m, b: n } = object;

console.log('\ndestructureObject2()');
console.log(`m: "${m}" n: "${n}"`);
console.log();
```

```js
const object = {
    a: 'value a',
    b: 'value b'
};

const { a, b, c = 'value c'} = object;

console.log('\ndestructureObject1WithDefaults()');
console.log(`a: "${a}" b: "${b}" c: "${c}"`);
console.log();
```

```js
const object = {
    a: 'value a',
    b: 'value b'
};

const { a: m, b: n, c: s = 'value c'} = object;

console.log('\ndestructureObject2WithDefaults()');
console.log(`m: "${m}" n: "${n}" s: "${s}"`);
console.log();
```

```js
const obj1 = {
    x: 'value x',
    y: 'value y',
    z: {
        a: 'value a',
        b: 'value b'
    }
};

const {
    x: m,
    y: n,
    z: {
        a: s,
        b: t
    }
} = obj1;

console.log('\ndestructureObject2WithDefaults()');
console.log(`m: "${m}" n: "${n}" s: "${s}" t: "${t}"`);
console.log();
```
