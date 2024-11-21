# Partner API
# APIs
## API Portal
### Development

```https://api-portal.accp.openbusiness.ing.de/```

### Production
```https://api-portal.openbusiness.ing.de/```
access available only upon request

## Endpoints
### Development
```https://api.accp.openbusiness.ing.de/partner-api/{resource}```

### Production
```https://api.openbusiness.ing.de/partner-api/{resource}```

### Headers
For all the APIs operations you must provide the headers:
* ```Subscription-Key: <key>```.
* `Authorization : Bearer <token>`\*

```
Note: for the token endpoint you do not need to provide the Authorization header since this endpoint is used for obtaining the token.
```
For more details see [security page](Security.md) and [tutorials page](Tutorials.md)
### Security
All the APIs are protected for security layers, such as, mTLS, Whitelisting, Throttling, etc.
For more details see [security page](Security.md) and [tutorials page](Tutorials.md)

## Application API
See [Application API Page](ApplicationApi.md)
## Test API
See [Test API Page](TestApi.md)
## Token API
See [Token API Page](TokenApi.md)

# Postman collection
you can find the postman collection [here](https://www.getpostman.com/collections/45d35533417147f71979).