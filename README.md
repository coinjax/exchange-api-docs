# CoinJax Exchange Api Documentation

## Base URL

Demo: https://api.demo.coinjax.com/v1

## Code Example

```javascript
const axios = require('axios');
const crypto = require('crypto');
 
const API_KEY = 'XXXXXXXX';
const API_SECRET = 'YYYYYY';
 
const instance = axios.create({
    baseURL: 'https://api.demo.coinjax.com',
    timeout: 2000
});
 
const privateCall = (path, body) => {
 
    body.nonce = Date.now().toString();
    body.request = path;
 
    let payload = new Buffer(JSON.stringify(body))
        .toString('base64');
 
    let signature = crypto
        .createHmac('sha384', API_SECRET)
        .update(payload)
        .digest('hex');
 
    return instance.post(path, body, {
        headers: {
            'X-CJX-APIKEY': API_KEY,
            'X-CJX-PAYLOAD': payload,
            'X-CJX-SIGNATURE': signature,
        }
    });
};
 
// Public API call
instance.get('/v1/symbols')
    .then(res => console.log(res.data));
    .catch(err => console.log(err));
 
// Private API call
privateCall('/v1/balances', {})
    .then(res => console.log(res.data))
    .catch(err => console.log(err));
```

## Public API

### Endpoint Quick Reference

| Title                        | Method | URI               | Description |
|------------------------------|--------|-------------------|-------------|
| List of market symbols       | GET    | symbols           | Returns an array of market symbols |
| Get details of single market | GET    | pubticker/:symbol | Returns market data such as last, ask, bid etc. |
| Get order book               | GET    | book/:symbol      | Returns the order book containing a list of bids and a list of asks. |
| Get trade feed               | GET    | trades/:symbol    | Returns an array of trade objects containing price, amount and timestamp |

## Private API

### Endpoint Quick Reference

| Title                        | Method | URI               | Description |
|------------------------------|--------|-------------------|-------------|
| Place order | POST   | order/new | |
| Cancel order | POST   | order/cancel | |
| Get order status | POST   | order/status | |
| Get open orders | POST   | orders | Returns a list of active orders of the specified market |
| Get finished orders | POST   | orders/hist | Returns a list of filled/cancelled orders of the specified market |
| Get transactions | POST   | transactions | Returns a list of transactions for a specified currency |
| Get balances | POST   | balances | Returns a list of balances for all assets |

## Authentication

Following Gemini/BFX APIs. All authenticated endpoint requests are POST with empty body – the payload is transmitted via header X-CJX-PAYLOAD

Refer to code example above.

Note: Public APIs still contain GET methods.

### Required in header

| Header | Value |
|--------|-------|
| Content-Length | `0` |
| Content-Type | `text/plain` |
| X-CJX-APIKEY | Your CoinJax API key |
| X-CJX-PAYLOAD | The base64-encoded JSON payload |
| X-CJX-SIGNATURE | `hex(HMAC_SHA384(base64(payload), key=api_secret))` |
| Cache-Control | `no-cache` |

### Required in payload

| Key | Value |
|--------|-------|
| nonce | A integer that is incremented in each request without repetition. Eg. a millisecond timestamp. |
| request | The URI string. eg. /v1/balances |

## Websocket

Testing. Will be available soon.