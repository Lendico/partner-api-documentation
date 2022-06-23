# Partner API
# Security
For keeping our process safe we have several layers of security, which are depicted on this page.

## IP whitelisting
To access the API, it is necessary to provide the list of IPs of the Client Application that will be calling the Partner API, so we can whitelist them, and only after that, the client application will be able to call the API.
A call from a non-whitelisted IP will return HTTP status code `403` and response :
```
{
    "title": "Forbidden",
    "status": 403,
    "detail": "Caller IP address is not allowed. Access denied."
}
```

## Throttling
To secure the API against some attacks, for instance, denial of service (DoS) attack, and to avoid misuse of the API, we limit the number of requests by 30 request per second by IP.
Requests that exceed the limit per second will be blocked with HTTP status code `429`
and response:
```
{
    "title": "Rate limit is exceeded.",
    "status": "429",
    "detail": "Rate limit is exceeded. Try again in 46 seconds."
}
```
The Partner API provides some headers to help to deal with the rate limit.

 * RateLimit-Limit: containing the amount of requests allowed in a time window of 60 seconds;
 * RateLimit-Remaining: containing the remaining requests amount in the current window;
 * RateLimit-Reset: containing the time remaining in the current window for refreshing the rate limit, specified in seconds, you should try again only after those seconds.
See more in this [draft]( https://tools.ietf.org/id/draft-polli-ratelimit-headers-00.html).

Another Throttling mechanism we use is by crefo id within a time window.
You cannot apply using the same crefo id within the first 5 minutes from the first try, it will lead to HTTP status code `429` and the following error message:
```
{
    "title": "Limit of requests by crefo Id exceeded.",
    "status": 429,
    "detail": "Limit of requests, in a given amount of time, by crefo Id exceeded, try again later."
}
```
  
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
Requests with invalid Subscription key will be rejected with HTTP status code `401`.
and response:
```
{
  "title": "Invalid subscription key",
  "status": "401",
  "detail": "Access denied due to invalid subscription key. Make sure to provide a valid key for an active subscription."
}
```

## OAuth2
Partner API is protected by OAuth2, for sending request you need to be authorized, for doing so, as part of the API onboarding, 
you will get the credentials for the OAuth2. 
With the credentials, you can make a `POST` request to obtain the token, see more information in the [Token API page](TokenApi.md).
See an example of OAuth2 implementation client in the [Tutorials Page](Tutorials.md).

## mTLS
We use another layer of security for ensuring the integrity of the data, encrypting and validating the communication both ways, server and client side with mTLS.
Requests without or with invalid certificates will be rejected with HTTP status code `401`

```
{
    "title": "Invalid client certificate",
    "status": "401",
    "detail": "Invalid certificate provided, please try again with a valid certificate"
}
```

We use mTLS meaning that for calling the API you need to provide a valid certificate, this certificate must have been provided beforehand(see [Onboarding Page](Onboarding.md)).
You need to add the certificate to the request. See an example of mTLS implementation java client on the [Tutorials Page](Tutorials.md).

### Certificate Requirements.
Requirements for the TLS and HTTP Signature certificates

* The certificate lifespan must not exceed 39 months.
* Public key algorithm must be RSA-2048 bits or higher.
* Signature algorithm: SHA-256 bits or higher.

See how to generate a self-signed certificate on the [Tutorials Page](Tutorials.md)
or you can use official signed by a commercial CA in case you have.

