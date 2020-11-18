# ApartX API Documentation

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
var request = require('request');

var params = {

}

request.get({
    url: 'https://apartx.co/api/v1/locks',
    headers: getAuthHeaders(params)
  }, function (error, response, body) {
    // process response
  }
);
```

> Example output

```ts
{
  locks: [{
    "_id": String,
    "name": String,
    "userId": String,
    "subscription": Object
  }],
  total: Number
}
```

> Subscription Schema

```ts
{
  type: 'montly' | 'pay_per_key',
  endedAt: Number
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
var request = require('request');

var params = {

}
var lockId = 'lockId'

request.get({
    url: `https://apartx.co/api/v1/locks/${lockId}/passcodes`,
    headers: getAuthHeaders(params)
  }, function (error, response, body) {
    // process response
  }
);
```

> Example output

```ts
{
  passcodes: [{
    "_id": String,
    "lockId": String,
    "createdBy": String,
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
| startTime | Passcode start time (Date format YYYY-MM-DD HH:mm) |
| endTime | Passcode end date (Date format YYYY-MM-DD HH:mm) |

#### ** Node.js **

```js
var request = require('request');

var params = {
    lockId: 'lockId',
    startTime: '2020-01-01 15:00'
    endTime: '2020-01-01 16:00'
};
var lockId = 'lockId'

request.post({
    url: `https://apartx.co/api/v1/locks/${lockId}/passcodes`,
    form: params,
    headers: getAuthHeaders(params)
  }, function (error, response, body) {
    // process response
  }
);
```

> Example output

```json
{
  "_id": String,
  "passcode": String,
  "startTime": Number,
  "endTime": Number
}
```
