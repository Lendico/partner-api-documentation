# Partner API
# Security
For keeping our process safe we have several layers of security, which are depicted on this page

## IP whitelisting
To be added after implementation in AAPIM

## Throttling
To be added after implementation in AAPIM
## Subscription Key
A subscription key is a key generated to the user in the onboarding process, this key is unique for each partner and must be provided in a header  ```Subscription-Key: <key>```\* for every request to the server.
Requests without a Subscription key will be rejected with HTTP status code `401`
and response:
```
{
  "title": "Missing subscription key",
  "status": "401",
  "detail": "Access denied due to missing subscription key. Make sure to include subscription key when making requests to an API."
}
```
Requests with invalid Subscription key will be rejected with HTTP status code `401`
and response:
```
{
  "title": "Invalid subscription key",
  "status": "401",
  "detail": "Access denied due to invalid subscription key. Make sure to provide a valid key for an active subscription."
}
```

## OAuth2
To be added after implementation in AAPIM
## mTLS
To be added after implementation in AAPIM
