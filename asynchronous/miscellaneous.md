# Miscellaneous Asynchronous Topics

## Contents
- [Problem 1 - `Promise.all()` Without Rejects](#section1)
- [Problem 2 - Calling and Assembling Paged Results](#section2)


<div id = "section1"/>

## Problem 1 - `Promise.all()` Without Rejects

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

<div id = "section2"/>

## Problem 2 - Calling and Assembling Paged Results:

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

