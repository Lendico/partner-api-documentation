# Partner API

# HTTP Status Codes

Possible HTTP status code returned by partner API

| Status Code | Description                                                                           |
|-------------|---------------------------------------------------------------------------------------|
| 200         | Response OK                                                                           |
| 201         | Resource created                                                                      |
| 202         | Request accepted for processing                                                       |
| 400         | Bad request parameters or fields, see [Common Errors](CommonErros.md)                 |
| 401         | Client authentication failed and  missing or invalid subscription key                 |
| 403         | Resource authorization failed                                                         |
| 404         | Resource not found                                                                    |
| 412         | Precondition failed                                                                   |
| 409         | Resource duplicated                                                                   |
| 413         | Request too large                                                                     |
| 422         | Request throttled                                                                     |
| 429         | Request throttled                                                                     |
| 500         | Internal error with details in the response body, see [Common Errors](CommonErros.md) | 
| 503         | Service unavailable                                                                   |
