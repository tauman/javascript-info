# Async-Await


## Syntax

The `async-await` construct is syntactical sugar for promises; in fact, they are really different ways of expressing the exact same thing. Because of this, you can freely intermix Promises and async-await to use whatever you feel is cleaner and easier to understand.

The syntax is very simple. Functions making asynchronous calls use the `async` keyword in their declaration; when making an asynchronous call, the call is preceded by the `await` keyword.

Here is a simple example:
```
async function foo() {
    // ... async stuff here
}

async function bar() {
    const result = await foo();

    // do some results processing here...

    return result;
}
```

If we want to include error-handling, then we could rewrite `bar()`:
```
async function foo() {
    // ... async stuff here
}

async function bar() {
    try {
        const result = await foo();

        // do some results processing here...

        return result;
    } catch(error) {
        // do error handling...
    }
}
```

Remember that promises and `async-await` are interchangeable. This means that we can utilize `async-await` with function calls that return promises. For example, we could make an `axios` call as if it was an `async` function.
```
const axios = require('axios');

const userUrl = 'https://jsonplaceholder.typicode.com/users';

async function fetchUser(id) {
    const url = `${userUrl}/${id}`;

    let result;
    try {
        result = await axios.get(url);
    } catch(error) {
        return {
            error: true,
            result: error.message
        }
    }

    try {
        result = JSON.parse(result);
    } catch(error) {
        return {
            error: true,
            result: `Unable to parse result: ${error.message}`
        }
    }

    return {
        error: false,
        result
    };
}
```

Now let's examine why we can mix promises and `async-await`. Run node at the terminal to enter the REPL and enter the following:
`Promise.resolve(5)`
The output from that should look something like:
```
Promise {
  5,
  domain:
   Domain {
     domain: null,
     _events:
      { removeListener: [Function: updateExceptionCapture],
        newListener: [Function: updateExceptionCapture],
        error: [Function: debugDomainError] },
     _eventsCount: 3,
     _maxListeners: undefined,
     members: [] } }
```
Now enter the following IIFE call on an async function:
`(async function() { return 5; })()`
Once again, you should see something like:
```
Promise {
  5,
  domain:
   Domain {
     domain: null,
     _events:
      { removeListener: [Function: updateExceptionCapture],
        newListener: [Function: updateExceptionCapture],
        error: [Function: debugDomainError] },
     _eventsCount: 3,
     _maxListeners: undefined,
     members: [] } }
```

They are **exactly the same**. Now it should be obvious why promises and `async-await` can be intermixed: the underlying implementation is identical. This means that you can also use `then()` and `catch()` from the `Promise` API on `async` functions:
```
async function foo() {
    return 5; // Remember that this is the same as Promise.resolve(5)
}

foo().then(r => console.log(r)).catch(e => console.log(e));

async function bar() {
    throw 'ERROR'; // This is the same as Promise.reject('ERROR');
}

bar().then(r => console.log(r)).catch(e => console.log(e));
```

The important points to remember here are:
1. An `async` function returns an instance of `Promise`.
2. The `await` keyword can only be used from within an `async` function.
3. Although `async` and `await` make code appear straightforward, remember that `await` is waiting for a code to run on the event loop.

