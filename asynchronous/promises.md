# Promises

## Contents
- [Using Promises](#section1)
- [Promise Chaining](#section2)
- [Promises in Parallel](section3)
- [Implementing Promises](#section4)

<div id='section1'/>

## Using Promises

Promises provide a way to invoke an asynchronous function and specify what code to run in the event that the function call succeeds or fails. The `Promise` class is a fundamental part of **ES6** JavaScript and it provides most of the functionality you will need for responding to promise lifecycle events (i.e. when a promise resolves or rejects), invoking asynchronous functions in parallel), explicitly resolving or rejecting a promise, or even implementing your own promises.

### Responding to Resolution and Rejection

Using the `then()` and `catch()` functions, we respond to the success or failure of a promise. Both `then()` and `catch()` a function with a single parameter:
```
successPromise.then(value => {
    // success processing code
});

failPromise.catch(value => {
    // failure processing code
});
```

Additionally, we can chain those together to respond to success and failure:
```
aPromise
    .then(success => {
        // success processing code
    })
    .catch(failure => {
        // failure processing code
    })
```

Alternatively, we can include success and error handling code with `then()` call:
```
// This identical in effect to using then() and catch()
aPromise.then(
    success => {
        // success processing code
    },
    (failure => {
        // failure processing code
    })
```

Now we can try some specific code, but first we'll need to understand two functions: `Promise.resolve()` and `Promise.reject()`.

- `Promise.resolve(value)` creates a promise that will resolve with the value passed in.
- `Promise.reject(value)` creates a promise that will reject with the value passed in.

Now, using those functions, we can show how to handle promise resolution.

This code will print: `Resolved with 5`:

```
Promise.resolve(5)
    .then(r => console.log(`Resolved with ${r}`))
    .catch(e => console.log(`Rejected with ${e}));
```

This code will print: `Rejected with ERROR`:

```
Promise.reject('ERROR')
    .then(r => console.log(`Resolved with ${r}`))
    .catch(e => console.log(`Rejected with ${e}));
```

<div id='section2'/>

## Promise Chaining

If the function you pass to `then()` or `catch()` returns a value, that value will be returned by `then()` or `catch()` wrapped in a promise, unless the value itself is a promise, in which case it will be returned directly. This allows you to chain promises together:
```
Promise.resolve(5)
    .then(r => {
        console.log(r);
        return r + 1;
    })
    .then(r => {
        console.log(r)
    });

// prints "5", then prints "6"
```

If any promise in this chain rejects, the rejection will be handled by the first `catch()` in the chain:
```
Promise.resolve(5)
    .then(r => {
        console.log(r);
        return r + 1;
    })
    .then(r => {
        throw r;
    })
    .catch(e => {
        console.log(`ERROR: ${e}`);
    });

// prints "5", then prints "ERROR: 6"
```

Here's a complex example:
```
Promise.resolve(5)
    .then(r => {        // 1
        console.log(r);
        return r + 1;
    })
    .then(r => {        // 2
        console.log(r);
        return Promise.resolve(r);
    })
    .then(r => {        // 3
        console.log(r);
        return r + 1;
    })
    .catch(e => {       // 4
        console.log(`ERROR: ${e}`);
    });

// prints "5", prints "6", and prints "6" again
```

Now add another `then()` after the `catch()` and note how the `catch()` is skipped
```
Promise.resolve(5)
    .then(r => {        // 1
        console.log(r);
        return r + 1;
    })
    .then(r => {        // 2
        console.log(r);
        return Promise.resolve(r);
    })
    .then(r => {        // 3
        console.log(r);
        return r + 1;
    })
    .catch(e => {       // 4
        console.log(`ERROR: ${e}`);
    })
    .then(r => {        // 5
        console.log(r);
    })

// prints "5", prints "6", prints "6" again, and finally prints "7"
```

Now try changing step 2 in the above example to return Promise.reject(r) to see what happens:
```
Promise.resolve(5)
    .then(r => {        // 1
        console.log(r);
        return r + 1;
    })
    .then(r => {        // 2
        console.log(r);
        return Promise.reject(r);
    })
    .then(r => {        // 3
        console.log(r);
        return r + 1;
    })
    .catch(e => {       // 4
        console.log(`ERROR: ${e}`);
        return 10;
    })
    .then(r => {        // 5
        console.log(r);
    });

// prints "5", prints "6", prints "ERROR: 6", and finally prints "10"
```

Notice how a rejection causes the chain to jump to the first `catch()`. Also note that the catch function returns another promise. The outcome is also the same if, instead of returning a Promise that rejects in step 2, the function throws:
```
Promise.resolve(5)
    .then(r => {        // 1
        console.log(r);
        return r + 1;
    })
    .then(r => {        // 2
        console.log(r);
        throw r;
    })
    .then(r => {        // 3
        console.log(r);
        return r + 1;
    })
    .catch(e => {       // 4
        console.log(`ERROR: ${e}`);
        return 10;
    })
    .then(r => {        // 5
        console.log(r);
    })

// prints "5", prints "6", prints "ERROR: 6", and finally prints "10"
```

Promise chaining allows you to write straightforward code to chain together a series of asynchronous actions.


<div id='section3'/>

## Promises in Parallel

The `Promise` class provides two methods for running promises in parallel: `Promise.all()` and `Promise.race()`; both methods take an array of promises.

`Promise.all()` will run all promises in parallel, returning a promise that resolves to an array of values from all of the promise resolutions. An example should clarify that:
```
Promise.all([Promise.resolve(4), Promise.resolve(5), Promise.resolve(6)])
    .then(r => console.log(r))
    .catch(e => console.log(`ERROR: ${e}`));

// prints the array [4, 5, 6]
```

However, if any of the promises in the array reject, then the promise returned by `all()` rejects with the value of the first rejection:
```
Promise.all([Promise.resolve(4), Promise.reject(5), Promise.resolve(6)])
    .then(r => console.log(r))
    .catch(e => console.log(`ERROR: ${e}`));

// prints "ERROR: 5"
```

`Promise.race()` will run all promises in parallel, returning a promise that will resolve on the first promise to resolve, or reject if one of the promises rejects before any other promises have resolved. Note that in the example below, we can reliably predict the order that each promise resolves or rejects because we are using `Promise.resolve()` and `Promise.reject()`. However, most asynchronous actions are not so predictable.
```
Promise.race([Promise.resolve(4), Promise.resolve(5), Promise.resolve(6)])
    .then(r => console.log(r))
    .catch(e => console.log(`ERROR: ${e}`));

// prints "4"

Promise.race([Promise.resolve(4), Promise.reject(5), Promise.resolve(6)])
    .then(r => console.log(r))
    .catch(e => console.log(`ERROR: ${e}`));

// prints "4"

Promise.race([Promise.reject(4), Promise.resolve(5), Promise.resolve(6)])
    .then(r => console.log(r))
    .catch(e => console.log(`ERROR: ${e}`));

// prints "ERROR: 4"

Promise.race([Promise.reject(4), Promise.resolve(5), Promise.reject(6)])
    .then(r => console.log(r))
    .catch(e => console.log(`ERROR: ${e}`));

// prints "ERROR: 4"
```

<div id='section4'/>

## Implementing Promises

Creating your own promise implementations is relatively easy. You just create an instance of `Promise` and pass a function that takes two parameters: `resolve` and `reject`. Then you invoke the `resolve` or `reject` parameter when your promise resolves or rejects. We'll demonstrate this with a function that takes a parameter and resolves with that parameter if it is a number and rejects with that parameter if it is not a number:
```
function asyncNumberTest(value) {
    return new Promise((resolve, reject) => {
        if (typeof value === 'number') {
            return resolve(value);
        }

        return reject(`Error: '${value}' is not a numeric value`);
    })
}

asyncNumberTest(5)
    .then(r => console.log(r))
    .catch(e => console.log(e));

// prints "5"

asyncNumberTest('bogus')
    .then(r => console.log(r))
    .catch(e => console.log(e));

// prints "Error: 'bogus' is not a numeric value"
```

Implementing your own promises allows you to wrap an asynchronous function that takes a callback in a promise. You can find an example of this in [Converting Between Paradigms](./asynchronous/converting-between-paradigms.md).

