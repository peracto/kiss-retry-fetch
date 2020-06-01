# KISS Auth Fetch   
A Simple NodeJs module to retry a fetch request if it fails.

NB ... This module will only retry simple request bodies. It will not manage the retrying of Streams.

##Usage

The module returns a factory function. 

```js
const retryFetchFactory = require('kiss-retry-fetch')

const retryFetch = retryFetchFactory(
)

// use retryFetch as you would FetchAPI
```

By default, the module assumes that the Fetch API is exposed on the global context. If this is not the case or want to pipeline an alternate to the Fetch API, then define this in the 'fetch' property ...

```js
const kissAuthFetch = require('kiss-auth-fetch')
const nodeFetch = require('none-fetch')

const authFetch = authFetchFactory({
        fetch: nodeFetch,
        auth:  Promise.resolve('Bearer xxxxxx/yyyyyy')
    })

```

##Advanced Usage

```js
const fetch = require('node-fetch')
const retryFetchFactory = require('./peracto-retry-fetch')

module.exports = retryFetchFactory({
    maxRetries: 5,
    waitPeriod: [1000,3000,5000], // 1st retry 1 second, 2nd is 3 secords, 3rd and subseqent is 5seconds
    shouldRetry(response) {
        // Only retry if response is oneof these
        return [408, 429, 500, 502, 503].indexOf(response.status) === -1
    },
    getWaitTimeout(value, resource, response, retry) {
        // value is the default timeout period.
        // we can override this if nessesary 
        return value
    },
    fetch: fetch
})
// use authFetch as FetchAPI
```

| Property | Type|Description |
|:---------| :----: | :-----------
| maxRetries | integer | The number of retries before returing a failed Response. Default is 3 retries.
| waitPeriods | integer[] | And array of timeouts (milliseconds) to wait in a specified retry cycle 
| shouldRetry | function | A function - when returns true, then signals that a retry should occur. By default the retry will be triggered on status of 100-199, 499, 500-599.
| fetch | FetchAPI | By default we will us the 'fetch' API the is contained on the global context. In some javascript/nodejs configurations this may onot be available, so we can use dependency such as 'node-fetch'
| getWaitTimeout | function | By default we will us the waitPeriods defined in the waitPeriod property. We can choose to overide this default using this function. A use-case may be when the failure response message includes an embedded timeout period before retry.




