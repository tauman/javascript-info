# Pure Functions

## Contents
- [Definition of Pure Function](#section1)
- [Side Effects](#section2)
- [Deterministic Function](#section3)
- [Immutability](#section4)


<div id="section1"/>

## Definition of Pure Function

A **Pure Function** is a function in which the return value is determined only by the input values without and side-effects and such that the function is completely deterministic. This means that the function does not depend upon any external (to the function) state and it does not change any external state.

<div id="section2"/>

## Side Effects

In programming, a *side effect* is a change in the surrounding state of a block of code. For example, if a function changes some part of the state outside of its block during its execution it has a side effect. Note that that can include changing an object passed to the function as a parameter. The potential problem with side-effects is the fact that the state of your program can be changed unexpected and unpredictably. This is even more of a problem in multi-threaded and asynchronous programming, with the potential to create race and deadlock conditions.

Here is a function that creates a side-effect by changing the object passed to it:
```js
let object = {
	x: 1,
	y: 2
}

function foo(obj) {
	obj.z = 3;
}

console.log(object); // this will print: { x: 1, y: 2}

foo(object);

console.log(object); // this will print: { x: 1, y: 2, z: 3}
```

Here is a more direct example of a function that changes the value of an outside variable:
```js
let object = {
	x: 1,
	y: 2
}

function foo() {
	object.z = 3;
}

console.log(object); // this will print: { x: 1, y: 2}

foo();

console.log(object); // this will print: { x: 1, y: 2, z: 3}
```

Both of the above functions have side-effects. Additionally, functions that interact with external devices (i.e. the file system or a database) also have side-effects. Even logging calls are side-effect, so technically, no function with logging calls is free of side-effects. We can ignore logging calls 99% of the time when considering whether or not our function has side-effects, but you should keep in mind that a logging call is a side effect in case you run into that remaining 1% of the time when it matters (e.g. you have mis-configured your logger so that your `async` function is throwing an exception).


<div id="section3"/>

## Deterministic Function

A deterministic function is one in which the output can be predicted with 100% certainty based upon the input parameters.

Here are a few examples of deterministic functions:
```js
function foo() {
    return 5;
}

function bar(x) {
    return x;
}

function quux(x, y) {
    return x**y;
}

function baz(x) {
    return Math.sin(x);
}
```

Here are a few examples of non-deterministic functions:
```js
// This function is dependant upon the system time
function now() {
    return Date.now();
}


let x = 5;

// This function depends upon the value of a variable `x` that is defined and visible outside the scope of the function
function foo(y) {
    return x + y
}
```

<div id="section4"/>

## Immutability

An *immutable* object or variable is one whose state cannot be modified after it is created. Immutability is a standard feature of functional programming, with many function programming languages disallowing the value of a variable to be changed once the initial value is bound (i.e. assigned). Immutability is a simple way to prevent unintended side-effects or potential problems in asynchronous programming, since once a value is set, it cannot be inadvertently changed.

Note that JavaScript does not easily support complete immutability, even with the `const` keyword, so immutability in JavaScript is more by convention:
```js
const x = 9;

const obj = { x: 1, y: 2 };
const ary = [1, 2, 3];

x = 10; // this will result in an error

obj = { a: 10, b: 11 }; // as will this
ary = [10, 11, 12]; // and this

// However, this is completely valid
obj.x = 4;

// As is this
ary[1] = 4;
```



