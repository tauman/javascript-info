# Callbacks

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
```const fs = require('fs');
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

