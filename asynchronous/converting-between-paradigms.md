# Converting Between Paradigms

## Contents
- [Callback Paradigm](#section1)
    - [Mixed Implementation](#section2)
    - [Pure Callback Implementation](#section3)
- [Promise Paradigm](#section4)
    - [Mixed Implementation](#section5)
    - [Pure Promise Implementation](#section6)
- [`async` Paradigm](#section7)

In general, it is advisable not to directly mix asynchronous paradigms, as this code can be confusing or difficult to analyze. For example, consider if you were writing a two functions: one makes an HTTP GET and saves the content in a file, and a second that loads a file and then POSTs it to an endpoint. If we are using the Node.js `fs` module for file I/O and the `axios` module for HTTP calls, we end up mixing callbacks and promises.

<div id="section1"/>
## Callback Paradigm

<div id="section2"/>
### Mixed Implementation

An implementation of our two functions in the callback paradigm might look like:
```
const fs = require('fs');
const axios = require('axios');

function saveFromUrl(url, file, callback) {
    axios.get(url)
        .then(result => {
            const content = result.data;
            fs.writeFile(file, content, (error) => {
                if (error) {
                    return callback(error);
                }

                return callback();
            });
        })
        .catch(error => {
            callback(error);
        });
}

function loadAndPost(file, url, callback) {
    fs.readFile(file, (error, content) => {
        if (error) {
            return callback(error);
        }

        axios.post(url, content)
            .then(resolve => callback())
            .catch(e => callback(e));
    });
}
```

<div id="section3"/>
### Pure Callback Implementation

Since our operations are simple, the two functions, `saveFromUrl()` and `loadAndPost()` are not too difficult to follow. However, we can make them easier to understand if we wrap our calls to `axios.get()` and `axios.post()` in functions that take a callback:
```
const axios = require('axios');

function get(url, callback) {
    axios.get(url)
        .then(response => callback(null, response)
        .catch(error => callback(error));
}

function post(url, data, callback) {
    axios.post(url)
        .then(response => callback(null, response)
        .catch(error => callback(error));
}
```

Now we can implement `saveFromUrl()` and `loadAndPost()` as purely callback based calls:

```
const fs = require('fs');
const axios = require('axios');

function get(url, callback) {
    axios.get(url)
        .then(response => callback(null, response)
        .catch(error => callback(error));
}

function post(url, data, callback) {
    axios.post(url)
        .then(response => callback(null, response)
        .catch(error => callback(error));
}

function saveFromUrl(url, file, callback) {
    get(url, (error, result) => {
        if (error) {
            return callback(error);
        }

        const content = result.data;
        fs.writeFile(file, content, (error) => {
            if (error) {
                return callback(error);
            }

            return callback();
        });
    });
}

function loadAndPost(file, url, callback) {
    fs.readFile(file, (error, content) => {
        if (error) {
            return callback(error);
        }

        post(url, content, error => {
            if (error) {
                return callback(error);
            }

            return callback();
        });
    });
}
```

While having the nested "stepped" callbacks can get messy with more than two steps, it is consistent and usually easier to follow that a mix of callbacks and promises.

<div id="section4"/>
### Promise Paradigm

<div id="section5"/>
### Mixed Implementation

An implementation of our two functions in the promise paradigm might look like:
```
const fs = require('fs');
const axios = require('axios');

function saveFromUrl(url, file) {
    return new Promise((resolve, reject) => {
        axios.get(url)
            .then(result => {
                const content = result.data;
                fs.writeFile(file, content, (error) => {
                    if (error) {
                        return reject(error);
                    }

                    return resolve(null);
                });
            })
            .catch(error => {
                reject(error);
            });
    });
}

function loadAndPost(file, url) {
    return new Promise((resolve, reject) => {
        fs.readFile(file, (error, content) => {
            if (error) {
                reject(error);
            }

            axios.post(url, content)
                .then(resolve)
                .catch(e => reject(e));
        });
    });
}
```

<div id="section6">
## Pure Promise Implementation

Again, our operations are simple, the two functions, `saveFromUrl()` and `loadAndPost()` are not too difficult to follow. However, we can make them easier to understand if we wrap our calls to `fs.readFile()` and `fs.writeFile()` in functions that return a promise:

```
const fs = require('fs');

function readFile(file) {
    new Promise((resolve, reject) => {
        fs.readFile(file, (error, data) => {
            if (error) {
                return reject(error);
            }

            return resolve(data);
        });
    });
}

function writeFile(file) {
    new Promise((resolve, reject) => {
        fs.writeFile(file, (error) => {
            if (error) {
                return reject(error);
            }

            return resolve();
        });
    });
}
```

Now we can implement `saveFromUrl()` and `loadAndPost()` as purely promise based calls:
```
const fs = require('fs');
const axios = require('axios');

function readFile(file) {
    new Promise((resolve, reject) => {
        fs.readFile(file, (error, data) => {
            if (error) {
                return reject(error);
            }

            return resolve(data);
        });
    });
}

function writeFile(file) {
    new Promise((resolve, reject) => {
        fs.writeFile(file, (error) => {
            if (error) {
                return reject(error);
            }

            return resolve();
        });
    });
}

function saveFromUrl(url, file) {
    return axios.get(url).then(result => writeFile(file, result.data));
}

function loadAndPost(file, url) {
    return readFile(file).then(content => axios.post(url, content));
}
```

<div id='section7'/>
## `async` Paradigm

Since `async-await` and promises are interchangeable, the pure `async` implementation should be trivial. Note that `async-await` does not provide a way to directly wrap the callback code, however we can use a promise implementation and then just treat it as an `async` function:
```
const fs = require('fs');
const axios = require('axios');

function readFile(file) {
    new Promise((resolve, reject) => {
        fs.readFile(file, (error, data) => {
            if (error) {
                return reject(error);
            }

            return resolve(data);
        });
    });
}

function writeFile(file) {
    new Promise((resolve, reject) => {
        fs.writeFile(file, (error) => {
            if (error) {
                return reject(error);
            }

            return resolve();
        });
    });
}

async function saveFromUrl(url, file) {
    const result = await axios.get(url);
    await writeFile(file, result.data));
}

async function loadAndPost(file, url) {
    const content = await readFile(file);
    await axios.post(url, content);
}
```

