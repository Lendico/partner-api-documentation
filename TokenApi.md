# Partner API
# Token API
The Token API aims to provide an endpoint for retrieve token for using partner api(Authorization Header).

# Operations
## Token
```POST /oauth2/token```

### Headers
Headers annotated with \* are mandatory
* `Content-Type: application/x-www-form-urlencoded`.

## Schema
For the request you need to send in the body the paramenters
for example :
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
See more [here](Tutorials.md)