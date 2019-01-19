# Callbacks

## Implementing Callbacks
A callback is a function which is invoked upon the completion or failure of an asynchronous action, such as an HTTP request or I/O. Although Promises and `async` functions have mostly replaced callbacks in JavaScript development, there are still older libraries that use the callback paradigm.

A function that takes a callback will typically be of the form:

```
function foo(param1...paramN, callback)
```
That is, the asynchronous function will take zero or more execution parameters and a final callback parameter, which will usually be a function of the form:
```
function callback(error, result, optionalResult..)
```
This callback function will be invoked upon the completion or the failure of the asynchronous task. In the event of an error, the `error` parameter will typically be populated with a string, object, or Error instance to indicate that an error has occurred along with typical error information. If the asynchronous action is successful, the `error` parameter will be `undefined` or `null` and the `result` parameter will be populated with the result of the action. Note that an asynchronous action might not return a result, in which case, the callback might only take the `error` parameter.

Here is a simple implementation of a callback:
```
function callback(error, result) {
    if (error) {
        // Do error handling stuff here
        return; // Do not forget this
    }

    // Do success stuff here, using the value of 'results'
    return;
}
```
As an example, let's consider how we would use the **Node.js** `fs` module to read a text file. This module includes a `readFile()` function which takes a path, an optional second parameter with option information, and a callback:
```
fs.readFile(path[, options], callback)
```
We can ignore the `options` parameter as the defaults will work okay for our purposes. A simple program to read the **AWS** credentials file on a Mac OS X machine might start like this:
```
const fs = require('fs');
const credFile = `${process.env.HOME}/.aws/credentials`;

fs.readFile(credFile, callback);
```
Now we need to implement the `callback` function:
```
/**
 * The function checks to see if the file was read successfully.
 * If there was an error, print the error and return. If the read
 * was successful, print the contents of the file.
 */
function callback(error, result) {
    if (error) {
        return console.log(error);
    }

    // The result is a Buffer, so we need to convert it to a String
    const credentials = result.toString('utf8');

    console.log(credentials);
}
```
Now we can put this together into a utility program. If you save this program in a node project, you should be able to run it at the command line.
```
const fs = require('fs');
const credFile = `${process.env.HOME}/.aws/credentials`;

/**
 * The function checks to see if the file was read successfully.
 * If there was an error, print the error and return. If the read
 * was successful, print the contents of the file.
 */
function callback(error, result) {
    if (error) {
        return console.log(error);
    }

    // The result is a Buffer, so we need to convert it to a String
    const credentials = result.toString('utf8');

    console.log(credentials);
}

fs.readFile(credFile, callback);
```
Often, when the callback implementation is trivial, the callback function will be passed as an inline definition:
```
const fs = require('fs');
const credFile = `${process.env.HOME}/.aws/credentials`;

fs.readFile(credFile, function(error, result) {
    if (error) {
        return console.log(error);
    }

    // The result is a Buffer, so we need to convert it to a String
    const credentials = result.toString('utf8');

    console.log(credentials);
}
```
Or even better, using an arrow function:
```
const fs = require('fs');
const credFile = `${process.env.HOME}/.aws/credentials`;

fs.readFile(credFile, (error, result) => {
    if (error) {
        return console.log(error);
    }

    // The result is a Buffer, so we need to convert it to a String
    const credentials = result.toString('utf8');

    console.log(credentials);
}
```

## Implementing a Function that Takes a Callback
If you are working within legacy code that uses callbacks, you should probably stay within that paradigm when adding or updating functionality rather than mixing in the Promise and/or async-await paradigm for asynchronous code. As an example, we will implement a simple function that adds 5 to a number and returns the result. If the value passed to the function is not a number, then the function will throw an `Error()`.

Our initial implementation will be synchronous:
```
function add5(num) {
    if (typeof num !== 'number') {
        throw new Error(`Value passed was not a number`);
    }


    return num + 5;
}
```
Now, we will make this an asynchronous call, by adding a `callback` parameter. and through the use of `setImmediate()`. Remember that callback functions should have the signature: `function(error, result)`:
```
function add5(num, callback) {
    // these are the return values for the callback function:
    let error = null;
    let result = null;

    if (typeof num !== 'number') {
        error = new Error(`Value passed was not a number`);
    } else P
        result = num + 5;
    }


    // Now we have to make this asynchronous, so we can't invoke the
    // callback directly. So instead, we use setTimeout() or setImmediate()
    setImmediate(callback, error, result);
}
```
The above example was somewhat nonsensical. There was no real reason to implment that as an asynchronous function. Now we will create one that is somewhat more realistic. Our next function will take an `id` and call a REST endpoint with that value using the `request` module and return either the result or the error if that fails. Since making an HTTP call is asynchronous, our function will have to be asynchronous as well. Here is an initial implementation:
```
// you will need to "npm install" this module
const request = require('request');

// This is an enpoint available online for testing
const restUrl = 'https://jsonplaceholder.typicode.com/users';

function fetchUser(id, callback) {
    const url = `${restUrl}/${id}`;

    // Make an HTTP GET call to 'url'
    request.get(url, (error, result, body) => {
        if (error) {
            // An error occurred, so we will invoke the callback
            // with the error value is its first parameter:
            return callback(error);
        }

        // Since the call was successful, we want to invoke the
        // callback with the return value. In this case, it is
        // the valu of the "body" parameter.
        return callback(null, body);
    });
}

// now we'll export our function
module.exports = fetchUser;
```
Finally, for the sake of completeness, we will add some data validation so that even if the `request` call is successful, our function will return an error unless the HTTP status code was 200 and the result was valid JSON:
```
const request = require('request');

const restUrl = 'https://jsonplaceholder.typicode.com/users';

/**
 * The function takes an id and a callback and calls the user endpoint.
 * If the call is successful, it parses the response using JSON.parse()
 * and calls the callback with the result. If there is an error, the HTTP
 * status code of the response is not 200, or the result cannot be parsed
 * as JSON, then the callback is called with an error.
 *
 * param id The id of the user to fetch
 * callback a callback function with the signature: function(error, result);
 */
function fetchUser(id, callback) {
    const url = `${restUrl}/${id}`;

    // Make an HTTP GET call to 'url'
    request.get(url, (error, result, body) => {
        if (error) {
            return callback(error);
        }

        if(result.statusCode !== 200) {
            return callback(new Error(`Expected a status code of 200, it was ${result.statusCode}`));
        }

        try {
            const user = JSON.parse(body);
            return callback(null, user);
        } catch(error) {
            return callback(new Error(`Error parsing response: ${error.message}));
        }
    });
}

module.exports = fetchUser;
```


