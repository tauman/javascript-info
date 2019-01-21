# Function Syntax

## Contents
- [Declarations and Expressions](#section1)
- [Arrow Functions](#section2)
- [Immediately Invoked Function Expression](#section3)

<div id='section1'/>

## Declarations, Expressions

There are two main ways of defining a function:
1. Declarative Syntax
2. Expression Syntax

Here is a sample function defined using declarative syntax:
```
function foo() {
    console.log('foo()');
}

console.log(foo.name); // prints "foo"
```

If the function is asynchronous:
```
async function foo() {
    return 5;
}
```

This function is defined using expression syntax:
```
const bar = function() {
    console.log('bar()');
};

console.log(bar.name); // prints "bar"
```

`async` can be also be used for expression syntax:
```
const bar = async function() {
    return 5;
};
```

This function is defined using expression syntax, but it defines the name of the function explicitly:
```
const quux = function baz() {
    console.log('baz()');
};

console.log(quux.name); // prints "baz"
```

<div id="section2"/>

## Arrow Functions

Arrow functions can only be defined with expression syntax, but there are a few options depending upon the number of parameters and the number of statements that make up the body.
```
const foo = (x, y) => {
	return x + y;
}
```

If the arrow function does not take any parameters, you must use the empty parenthesis:
```
const quux = () => {
    console.log('quux()');
};
```

However, the parenthesis are optional for arrow functions with only one parameter:
```
const baz = param => {
    return param;
};
```

Note that if the body only contains one statement, no curly braces are required and the return value will be the result of the expression. So if we consider this `double()` function:
```
const double = num => {
	return 2 * num;
}
```

It can be defined in this abbreviated syntax:
```
const double = num => 2 * num;
```

If the value returned is an object literal, then that value must be placed in parenthesis:
```
const thud = (param1, param2) => ({ param1, param2 });
```

`async` can also be used with arrow functions:
```
const foo = async (x, y) => {
	return x + y;
}

const baz = async param => {
    return param;
};

const double = async num => 2 * num;
```

<div id='section3'/>

## Immediately Invoked Function Expression

You can define a function and immediately invoke it through a syntax called **Immediately Invoked Function Expression** or **IIFE**:
```
(function foo() {
    console.log('foo()');
})();
```

Note that the function name is optional:
```
(function() {
    console.log('foo()');
})();
```

**IIFE** also works with arrow functions:
```
// with a function body
(() => {
    console.log('quux()');
})()

// single statement body form:
(() => 5 + 6)();
```

You can also preserve the return value:
```
const value = (() => 5 + 6)();
```

