# Partner API
# Token API
The Token API aims to provide an endpoint for retrieving tokens to use with the Partner API (as Authorization Header).

# Operations
## Token URLs
### Development
```POST https://api.accp.openbusiness.ing.de/token-api/oauth2/token```
### Production
```POST https://api.openbusiness.ing.de/token-api/oauth2/token```

### Headers
Headers annotated with \* are mandatory
* `Content-Type: application/x-www-form-urlencoded`.
* `Subscription-Key: your-api-portal-subscription-key`

## Schema
Include in the request body these parameters:
`client_id={client_id}&client_secret={client_secret}`

### Response 
```
{
    "token_type": "Bearer",
    "expires_in": 3599,
    "ext_expires_in": 3599,
    "access_token": "{token}"
}
```
## Implementation
See more [here](Tutorials.md#java-apache-httpclient-full-implementation-of-a-client)
