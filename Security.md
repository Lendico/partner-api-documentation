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
See more in this [draft]( https://datatracker.ietf.org/doc/draft-ietf-httpapi-ratelimit-headers/00/).

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
A subscription key is a unique key that is used to authenticate and authorize access to Partner API.
It is generated in the API Portal in the onboarding process and must be provided in a header  ```Subscription-Key: <key>```\* for every request to the server.
Requests without a Subscription key in the header will be rejected with HTTP status code `401`
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
Partner API is protected by OAuth2. As part of the API onboarding, 
you will get the credentials for the OAuth2. 
With the credentials, you can make a `POST` request to obtain the token, see more information in the [Token API page](TokenApi.md).
See an example of OAuth2 implementation client in the [Tutorials Page](Tutorials.md#java-apache-httpclient-full-implementation-of-a-client).

## Authentication

The Authentication part is composed of Mutual TLS (typical end-point authentication and encryption) and 
HTTP Request signing (application level authentication and integrity) which is described in [HTTP Signatures Draft RFC version 10](https://datatracker.ietf.org/doc/html/draft-cavage-http-signatures-10).

To secure the connection to our APIs, we require TLS Client Authentication and HTTP message signing for both authentication and end-to-end data integrity. For TLS Client Authentication, use your TLS client certificate. For HTTP message signing, use your HTTP Signing certificate.

Two X.509 public key certificates are required for authentication:

* A TLS connection certificate used for setting up a mutual TLS connection
* A HTTP Signature certificate used for signing the requests

Those two certificates must have been provided before calling the API otherwise your call will be rejected(see [Onboarding Page](Onboarding.md)).

Requests without or with invalid certificates will be rejected with HTTP status code `401`

```
{
    "title": "Invalid client certificate",
    "status": "401",
    "detail": "Invalid certificate provided, please try again with a valid certificate"
}
```

You need to add the certificate to the request and sign the message. See an example for both using java client on the [Tutorials Page](Tutorials.md).

### Certificate Requirements.
Requirements for the TLS and HTTP Signature certificates

* Public key algorithm: RSA-2048 bits or higher
* Signature algorithm: SHA-256 bits or higher
* Valid upon receipt
* Certificate lifespan must not exceed 39 months
* **TLS and HTTP Signature certificates are different**

See how to generate a self-signed certificate on the [Tutorials Page](Tutorials.md#generating-self-signed-certificate-with-openssl)
or you can use official signed by a commercial CA in case you have.

### HTTP Request signing

To secure integrity of the request in transit, your application needs to sign every API request using the [HTTP Signatures Draft RFC version 10](https://datatracker.ietf.org/doc/html/draft-cavage-http-signatures-10). Below we will explain the steps to create a valid signature.

### 1. Calculate the digest of the body of your request

The digest is the SHA-256 hash value of the requests body encoded in Base64. If the body is empty, it should be the SHA-256 value of the empty string.

Example digest header for an empty body:

```
Digest: SHA-256=47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=
```
You will receive error in case invalid digest
```
{
    "type": "https://lendico.github.io/partner-api-documentation/",
    "title": "Unauthorized",
    "status": 400,
    "detail": "Request was malformed or otherwise invalid - [Provided payload digest diverge of provided digest]."
}
```

```
{
    "type": "https://lendico.github.io/partner-api-documentation/",
    "title": "Unauthorized",
    "status": 400,
    "detail": "Request was malformed or otherwise invalid - [Digest Header]."
}
```
### 2. Set the date header to the current date and time

The Date header requires the current date in the HTTP standard Date header format [(see RFC7231)](https://datatracker.ietf.org/doc/html/rfc7231#section-7.1.1.2).

**Important: We enforce a strict time frame for the request. If the Date header's time component diverges by more than 3 minutes, the request will be considered invalid and discarded.**

Example Date header:

```
Date: Wed, 03 Jul 2019 08:28:28 GMT
```
You will get an error in case providing invalid date header
e.g
```
{
    "type": "https://lendico.github.io/partner-api-documentation/",
    "title": "Unauthorized",
    "status": 401,
    "detail": "Difference between current GMT time and the Date header is more than 3 minutes allowed]."
}
```

```
{
    "type": "https://lendico.github.io/partner-api-documentation/",
    "title": "401",
    "status": 400,
    "detail": "Request was malformed or otherwise invalid - [Date Header]."
}
```
### 3. Create and sign the signing string

For creating the signing string, follow the guide in [Section 2.3](https://datatracker.ietf.org/doc/html/draft-cavage-http-signatures-10#section-2.3) of the HTTP Signatures Draft RFC. So for the HTTP Signing flow you need:
* (request-target)
* Digest
* Date
The request target is a combination of the HTTP action verb and the request path. These are taken from the HTTP request, no additional header is needed.

Example signing string:

```
(request-target): get /greetings/single\n
date: Wed, 03 Jul 2019 08:28:28 GMT\n
digest: SHA-256=47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=

```

**Note: The '\n' symbols above are included to demonstrate where the new line character should be inserted. There is no new line on the final line of the signing string.**

To create the signature value, the signing string needs to be signed with your signing key using the RSA-SHA256 algorithm and Base64 encoded.

### 4. Set the signature header in the request

The HTTP signature header can now be constructed. The headers parameter is used to specify the ordered list of lowercased HTTP headers included when generating the signature for the request.

Example Signature header:

```
Signature: keyId="[CLIENT_ID]",algorithm="rsa-sha256",headers="(request-target) date digest",signature="[SIGNATURE_VALUE]"
```

For the initial authentication request to get an application access token (Client credentials OAuth 2.0 flow), We use the 'Authorization header' with the signature. 
So Instead the above header you need to use :

```
Authorization: Signature keyId="[CLIENT_ID]",algorithm="rsa-sha256", headers="(request-target) date digest", signature="[SIGNATURE_VALUE]"
```

You will get an error in case the signature is invalid, signed with different certificate from the one provided, or has different structure than required:
```
{
    "type": "https://lendico.github.io/partner-api-documentation/",
    "title": "Signature could not be successfully verified.",
    "status": 401,
    "detail": "Either the signature is malformed or the information required for constructing that signature is invalid or erroneous, please check the documentation."
}
```

Tips and common mistakes

* Since the generation of the signature input has to be an exact match, please read and follow the draft specification closely [HTTP Signatures Draft RFC version 10](https://datatracker.ietf.org/doc/html/draft-cavage-http-signatures-10)
* The header identifiers in the headers parameter must be in lower-case
* During the signature string construction, For (request-target) the value must be carefully created ( lowercase only the :method pseudo-header and not the :path pseudo-header; include also the query parameters into the :path pseudo-header). For more details check [Section 2.3](https://datatracker.ietf.org/doc/html/draft-cavage-http-signatures-10#section-2.3) from the draft specification.
* When constructing the signing string, the \n character is a LF (line-feed) character not a LF CR (line-feed carriage-return, like on Windows)
* Strip any leading or trailing white space from the headers before you use them

