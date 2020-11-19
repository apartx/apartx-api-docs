# ApartX API Documentation

# Playground

https://codesandbox.io/s/apartx-api-playground-jmrc8?file=/api.js

# HTTP API

HTTP API endpoint available on https://apartx.co/api/v1/.

Some methods requires authorization read below.

> We have limit in 2 calls per second from single account to authorization required methods and 100 calls per second from single IP address to public methods.

All HTTP methods accept JSON formats of requests and responses if it not specified by headers.

## Authorization

In order for access to private API methods, generate authorization keys in profile settings.

All request to these methods must contain the following headers:

* **X-KEY** - your key.
* **X-SIGNATURE** - query’s POST data, sorted by keys and signed by your key’s **“secret”** according to the HMAC-SHA256 method.
* **X-NONCE** - integer value, must be greater then nonce in previous api call.

#### Node.js

```js
var crypto = require('crypto')
var _ = require('underscore')

var createSignature = function (options, secret) {
  options = _.omit(options, ['__proto__'])
  payload = Object
  .keys(options) // get keys of payload object
  .sort() // sort keys
  .map((key) => key + "=" + encodeURIComponent(options[key])) // each value should be url encoded. the most sensitive part for sign checking
  .join('&'); // to sting, separate with ampersand
  hmac = crypto.createHmac('sha256', secret)
  hmac.update(payload)
  hmac.digest('hex')
}
```

# Locks

## List all locks

!> This method requires authorization.

Returns information about all available locks.

**HTTP Request**

`GET https://apartx.co/api/v1/locks`

**Query parameters**

| Parameter | Required | Description |
| --------- | ------- | ----------- |
| limit | No | Limit |
| page | No | Page number |

#### ** Node.js **

```js
let axios = require('axios');

let params = {
  limit: 1
}

axios.get('https://apartx.co/api/v1/locks', {
  params
  headers: getAuthHeaders(params)
}).then((result) => {
  // console.log(result)
  // {
  //   "locks": [
  //     {
  //       "_id": "FiTLosPF2BHMZLxTB",
  //       "name": "2017MM_6dd5b5",
  //       "subscription": {
  //         "type": "monthly",
  //         "endedAt": 1607883916
  //       }
  //     }
  //   ],
  //   "total": 3
  // }
});
```

> Response schema

```ts
{
  locks: Array<Lock> [{
    "_id": String,
    "name": String,
    "userId": String,
    "subscription": Subscription {
      "type": "montly" | "pay_per_key",
      "endedAt": Number | null // Unix timestamp in seconds. Present only if type is 'monthly'
    }
  }],
  total: Number
}
```

## List passcodes of lock

!> This method requires authorization.

Returns information about all generated passcodes for given lock.

**HTTP Request**

`GET https://apartx.co/api/v1/locks/:lockId/passcodes`

**Url parameters**

| Parameter | Description |
| --------- | ----------- |
| lockId | ID of lock |

**Query parameters**

| Parameter | Required | Description |
| --------- | ------- | ----------- |
| limit | No | Limit |
| page | No | Page number |

#### ** Node.js **

```js
const axios = require('axios');

let lockId = 'lockId';
let params = {
  limit: 1
};

axios.get(`https://apartx.co/api/v1/locks/${lockId}/passcodes`, {
  params,
  headers: getAuthHeaders(params)
}).then((result) => {
  // result.passcodes -> Array of passcodes
  // result.total -> Total count
  // console.log(result)
  // {
  //   "passcodes": [
  //     {
  //       "_id": "kW4YAsFZNKHKwqLCR",
  //       "lockId": "PApu2XWr7DmEQ6aRC",
  //       "passcode": "10249815",
  //       "startTime": 1605693575,
  //       "endTime": 1605697167,
  //       "createdAt": 1605695582
  //     }
  //   ],
  //   "total": 12
  // }
});
```

> Response schema

```ts
{
  passcodes: Array<Passcode> [{
    "_id": String,
    "lockId": String,
    "passcode": String,
    "startTime": Number,
    "endTime": Number,
    "createdAt": Number
  }],
  total: Number
}
```


## Create Passcode

!> This method requires authorization.

Create new passcode for lock.

**HTTP Request**

`POST https://apartx.co/api/v1/locks/:lockId/passcodes`

**Url parameters**

| Parameter | Description |
| --------- | ----------- |
| lockId | ID of lock |

**Post parameters**

| Parameter | Description |
| --------- | ----------- |
| startTime | Passcode start time (Date format **Unix timestamp in seconds**) |
| endTime | Passcode end date (Date format **Unix timestamp in seconds**) |

#### ** Node.js **

```js
const axios = require('axios');

let lockId = 'p6ta3scW2L8qaL83z';
let params = {
    startTime: 1605722400,
    endTime: 1605726000
};

axios.post(`https://apartx.co/api/v1/locks/${lockId}/passcodes`, {
  data: params,
  headers: getAuthHeaders(params)
}).then((result) => {
  // console.log(result);
  // {
  //   _id: 'bES5Wewa4FcwZZTv3',
  //   passcode: '3334584630',
  //   startTime: 1605722400,
  //   endTime: 1605726000
  // }
});
```

> Response schema

```ts
{
  "_id": String,
  "passcode": String,
  "startTime": Number,
  "endTime": Number
}
```
