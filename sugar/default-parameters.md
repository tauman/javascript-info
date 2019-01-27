# Default Parameters

```js
function defaults1(x, y = 2) {
    console.log('\ndefaults1()');
    console.log(`x: ${x}, y: ${y}`);
    console.log();
}

defaults1(1);
defaults1(1, null);
```

```js
function defaults2({ x, y = 2 }) {
    console.log('\ndefaults1()');
    console.log(`x: ${x}, y: ${y}`);
    console.log();
}

defaults2({ x: 1 });
defaults2(); // now fix the function
```

```js
function defaults3({ m: x, n: y = 2} = {}) {
    console.log('\ndefaults3()');
    console.log(`x: ${x}, y: ${y}`);
    console.log();
}

defaults3({ m: 1, n: 3});
defaults3({ m: 1});
defaults3();
```

```js
// default values can be the result of function calls
function defaults4(time = new Date()) {
    console.log('\ndefaults4()');
    console.log(`time: ${time}`);
    console.log();
}

defaults4();
```

