# Promise
---

![Promise Image](https://promisesaplus.com/assets/logo-small.png)

## [Promises/A+](https://promisesaplus.com/) definition of `promise`


>A `promise` represents the eventual results of an asynchronous operation. The primary way of interacting with a promise is through its `then` method, which registers callbacks to receive either a promise's eventual value or the reason why promise cannot be fulfilled.

## Libraries

### Some implementations of Promise/A+:

* [`promise`](https://www.npmjs.com/package/Promise): Basic JS Promise Implementation for funzzies Open to suggestions for improvements
* [`es6-promise`](https://www.npmjs.com/package/es6-promise): A lightweight library that provides tools for organizing asynchronous code
* [`bluebird`](https://www.npmjs.com/package/bluebird): Full featured Promises/A+ implementation with exceptionally good performance
* [`rsvp`](https://www.npmjs.com/package/rsvp): A lightweight library that provides tools for organizing asynchronous code
* [`q`](https://www.npmjs.com/package/q): Q A library for promises (CommonJS/Promises/A,B,D)

### Some helpful libraries using Promise to handle asynchronous operations

* [`request-promise`](https://www.npmjs.com/package/request-promise): The simplified HTTP request client 'request' with Promise support. Powered by Bluebird.
* [`node-fetch`](https://www.npmjs.com/package/node-fetch): A light-weight module that brings window.fetch to node.js and io.js
* [`axios`](https://www.npmjs.com/package/axios): Promise based HTTP client for the browser and node.js
* [`q-io`](https://www.npmjs.com/package/q-io): IO using Q promises


## Create a Promise

There are many ways to create a promise:

* Using Promise constructor:

```javascript
var Promise = require('es6-promise')

var promise1 = new Promise(function(fulfill, reject) {
    fs.readFile('test.json', function(err, res) {
        if (err) {
            reject(new Error(err))
        }
        fulfill(res)
    })
})
```

* Using Promise shortcuts:

```javascript
// To create a fulfilled promise
Promise.resolve(success_value).then(function(value) {
    console.log(value)
})

// To create a rejected promise
Promise.reject(failed_value).catch(function(err) {
    console.log(err)
})
```

## Chain promises

Create a promise and then chain it with `then` function.

```javascript
promise1.then(function onFulfilled1(res) {
    return res
}, function onRejected1(err) {
    return err
}).then(function onFulfilled2(res) {
    console.log(res)
}, function onRejected2(err) {
    console.log(err.message)
})
```

### Process multiple promises

To process a list of promises, using `Promise.all` function whose argument is a list of promise objects, if all the promises if fulfilled, `onFulfilled` will be called with a list of resolved objects accordingly to promises list and `onRejected` functions will be called if any of promises throws an error.

```javascript
Promise.all([promise1, promise2]).then(function(list_of_results) {
    // Do some stuffs with received results
}).catch(function(list_of_errors) {
    // Do some stuffs with thrown errors
})
```

**That's it! Good luck!**
