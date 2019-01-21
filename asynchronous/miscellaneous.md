# Miscellaneous Asynchronous Topics

## Contents
- [Infinite Recursion](#section1)
- [Problem - `Promise.all()` Without Rejects](#section2)
- [Problem - Calling and Assembling Paged Results](#section3)

<div id = "section1"/>

## Infinite Recursion

JavaScript ES6 does not use lazy evaluation, and it does not have tail optimized recursion. As a result, when writing recursive synchronized functions, you must always be aware of the possibility of exceeding the maximum call stack size. For example, the following program will not run forever:
```
function recurse() {
    console.log('recurse()');
    recurse();
}
```

However, remember that calls to asynchronous functions continue execution once the asynchronous call is made. Therefore, stack size is not a consideration when making recursive asynchronous calls, and the following program will run forever:
```
function asyncRecurse() {
    console.log('asyncRecurse()');
    setTimeout(asyncRecurse);
}
```
In the example `asyncRecurse()` above, the first statement in the function will print **asyncRecurse()**, then with the call to `setTimeout()`, the call to `asyncRecurse()` will be put on the event loop and the initial call to `asyncRecurse()` will exit. At that point, Node.js will execute the next asynchronous call as determined by the event loop heuristics. The following program illustrates this:
```
function sayHello() {
    console.log('Hello');
}

console.log('START...');
setTimeout(sayHello);
console.log('...END');
```
Note the order in which the `console.log()` statements are printed. This order will be the same if `setImmediate()` or `nextTick()` are used in place of `setTimeout()`. For an in-depth examination of how the event loop works (and how the various types of the asynchronous calls and actions are handled), read the excellent five-part article about the Node.js event loop linked on the [README](../README.md) file.

<div id = "section2"/>

## Problem - `Promise.all()` Without Rejects

You have a collection of functions that return promises that you want to invoke using `Promise.all()`, but if one or more of the promises reject, instead of `Promise.all()` rejecting, you want the promise to resolve with an error flag and error message.

_Hint: An example of possible output if you have three functions and one of them rejects could be:_
```
[{
    error: false,
    result: 'Some data'
}, {
    error: true,
    result: 'The Error Message'
}, {
    error: false,
    result: 'Some other data'
}]
```

Here is one solution:
```
/**
 * fnList: a list of functions that take no arguments
 * returns: a Promise that resolves to an array of results
 */
function exec(fnList) {
    const list = fnList.map(fn => {
        return new Promise((resolve, reject) => {
            fn()
                .then(r => resolve({ error: false, value: r }))
                .catch(e => resolve({ error: true, value: e }));
        });
    });

    return Promise.all(list);
}
```

<div id = "section3"/>

## Problem - Calling and Assembling Paged Results:

You are using the `request` module with callbacks to call an endpoint that returns one or more pages of JSON results. Each page of results has a 'nextPage' attribute which will contain the url to call for the next page or 'null' if there are no more results. Without using any async libraries, write a function that gets all of the results and assembles them before returning the results. The function should be:

`fetchAndAssemble(url, callback)`

callback will be invoked with the assembled results, or the error, if any of the fetches results in an error.

A page of results will look like:
```
{
    items: [{
        item: 'value'
    }, {
        item: 'value'
    }],
    nextPage: url|null
}
```

Here is one solution (note the asynchronous recursion):
```
const request = require('request');

function fetch(url, callback) {
    const fetchWithResults = (url, results, cb) => {
        request.get(url, (error, result, body) => {
            if (error) {
                return cb(error);
            }

            try {
                body = JSON.parse(body);
            } catch (error) {
                return cb('Unable to parse results');
            }

            results = [...results, ...body.items];

            if (body.nextPage === null) {
                return cb(null, results);
            }

            return fetchWithResults(body.nextPage, results, cb);
        });
    };

    fetchWithResults(url, [], callback);
}
```

Now we can test the solution to verify that it works.
```
const request = require('request');
const nock = require('nock');

const URL = 'http://localhost:8080';

// Set up mocked urls with three pages of results
nock(URL)
    .get('/1')
    .reply(200, {
        items: [{ item: 'value 1' }, { item: 'value 2' }, { item: 'value 3' }, { item: 'value 4' }, { item: 'value 5' }],
        nextPage: `${URL}/2`
    })
    .get('/2')
    .reply(200, {
        items: [{ item: 'value 6' }, { item: 'value 7' }, { item: 'value 8' }, { item: 'value 8' }, { item: 'value 10' }],
        nextPage: `${URL}/3`
    })
    .get('/3')
    .reply(200, {
        items: [{ item: 'value 11' }, { item: 'value 12' }, { item: 'value 13' }, { item: 'value 14' }, { item: 'value 15' }],
        nextPage: null
    });


function fetch(url, callback) {
    const fetchWithResults = (url, results, cb) => {
        request.get(url, (error, result, body) => {
            if (error) {
                return cb(error);
            }

            try {
                body = JSON.parse(body);
            } catch (error) {
                return cb('Unable to parse results');
            }

            results = [...results, ...body.items];

            if (body.nextPage === null) {
                return cb(null, results);
            }

            return fetchWithResults(body.nextPage, results, cb);
        });
    };

    fetchWithResults(url, [], callback);
}


// Call and print the results or the error
fetch(`${URL}/1`, (error, result) => {
    if (error) {
        return console.log(`ERROR: ${error.message}`);
    }

    console.log('RESULTS:');
    console.log(result);
});
```
