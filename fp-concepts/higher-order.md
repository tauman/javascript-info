# Higher Order Functions

## Contents
- [Definition of Pure Function](#section1)
- [Functions that Take a Function as a Parameter](#section2)
- [Functions that Return a Function](#section3)
- [Functions that Take a Function as a Parameter and Return a Function](#section4)

<div id="section1"/>

## Definition of a Higher Order Function

A higher order function is a function that takes a function as an argument and/or returns a function.

<div id="section2"/>

## Functions that Take a Function as a Parameter

The first example takes a function as an argument and applies it to the numeric value `5`, returning the result.
```js
// Take a function 'applier' and apply it to 5, returning the result
function doIt(applier) {
    return applier(5);
}

const add20 = value => value + 20;

console.log(add20); // <- this is a function
console.log(doIt(add20)); // <- take function and apply it
```

Perhaps the most common uses of higher order functions in JavaScript are passing a function to the various collection methods of `Array`, registering an event listener, and passing a callback to an asynchronous function. In the example below, we pass a function to the `map()` method of `Array`. Then map applies that function to each item in the array and returns a new array as the result:
```js
const ary = [1, 2, 3, 4, 5];

const doubler = value => value * 2;

const doubledAry = ary.map(doubler);

console.log(doubler); // <- this is a function
console.log(doubledAry); // <- this is the result of application of that function
```

He is an example of registering a listener:
```js
const { EventEmitter } = require('events');

// create an instance of EventEmitter
const myEmitter = new EventEmitter();

// create the listener
const listener = event => console.log(event)

// register the listener
myEmitter.on('event', listener);


// Wait 2000 milliseconds, then emit an event.
// Note that the bind() call is necessary because of the way JavaScript handles 'this'
setTimeout(emitter.emit.bind(emitter), 2000, 'event');
```

```js
const fs = require('fs');

// Create the callback
const callback = (error, files) => {
    if(error) {
        console.log(`ERROR: ${error.message}`)
        return;
    }

    console.log(files);
};

// List the files in the current directory using our callback
fs.readdir('.', callback);
```

<div id="section3"/>

## Functions that Return a Function

```js
// Take a value 'addValue' and return a function that adds that
// value to another value.
//
// Note how this uses a closure
function addGenerator(addValue) {
    return value => value + addValue;
}

const add5 = addGenerator(5);

// console.log(add5);  // <- this is a function
// console.log(add5(7));  // <- this is the result of the function
```

<div id="section4"/>

## Functions that Take a Function as a Parameter and Return a Function

```js
// take a function 'applier' and return a new function that applies
// the original function 'applier' in a value.
function applyGenerator(applier) {
    return value => applier(value);
}

const doubleApply = applyGenerator(num => 2 * num);

console.log(doubleApply);  // <- this is a function
console.log(doubleApply(5));  // <- this is the result of the function
```


