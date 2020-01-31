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

#### ** Node.js **

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
