# Pure Functions

## Contents
- [Side Effects](#section1)
- [Immutability](#section2)
- [Deterministic Function](#section3)
- [Definition of Pure Functions](#section4)

<div id="section1"/>

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

<div id="section2"/>

## Immutability



<div id="section3"/>

## Deterministic Function

<div id="section4"/>

## Definition of Pure Functions

