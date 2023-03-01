
# Partner API
# Commons Errors and Rejection Reasons
## Commons Errors 
Partner API uses the standard [Problem Details for HTTP APIs(RFC-7807)](https://datatracker.ietf.org/doc/html/rfc7807) for returning erros.
### Response Body Syntax Example
```
{
    "type": "https://zalando.github.io/problem/constraint-violation",
    "status": 400,
    "violations": [
        {
            "field": "partnerAccountManager.email",
            "message": "value must not be empty or null."
        }
    ],
    "title": "Constraint Violation"
}
```
or
```
{
    "title": "Unprocessable Entity",
    "status": 422,
    "detail": "currency [USD] is not supported yet, please contact the system admin."
}
```

| Error Type                                         | HTTP Status Code | Title                                        | Detail                                                                                                                                                                                          | Description  |
|----------------------------------------------------|------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| [https://www.lendico.de/](https://www.lendico.de/) | 400              | Constraint Violation                         | violations:\["", ""\]                                                                                                                                                                           | Client error |
| [https://www.lendico.de/](https://www.lendico.de/) | 401              | Missing subscription key                     | see [Security Page](Security.html#subscription-key)                                                                                                                                             | Client error |
| [https://www.lendico.de/](https://www.lendico.de/) | 401              | Invalid subscription key                     | see [Security Page](Security.html#subscription-key)                                                                                                                                             | Client error |
| [https://www.lendico.de/](https://www.lendico.de/) | 401              | Partner not matched                          | Unable to find a partner with the provided key/id, please contact the system admin                                                                                                              | Client error |
| [https://www.lendico.de/](https://www.lendico.de/) | 401              | Invalid client certificate                   | Invalid certificate provided, please try again with a valid certificate                                                                                                                         | Client error |
| [https://www.lendico.de/](https://www.lendico.de/) | 401              | Unauthorized                                 | In case invalid or missing authorization header. See [Security Page](Security.html#oauth2)                                                                                                      | Client error |
| [https://www.lendico.de/](https://www.lendico.de/) | 401              | Unauthorized                                 | Unauthorized access to the resource, please contact the system admin.                                                                                                                           | Client error |
| [https://www.lendico.de/](https://www.lendico.de/) | 401              | Signature could not be successfully verified | In case signature is invalid, e.g message signed with different no or different certificate from the one provided, or message build using different structure. See [Security Page](Security.md) | Client error |
| [https://www.lendico.de/](https://www.lendico.de/) | 403              | Forbidden                                    | see [Security Page](Security.md)                                                                                                                                                                | Client error |
| [https://www.lendico.de/](https://www.lendico.de/) | 404              | Not Found                                    |                                                                                                                                                                                                 | Client error |
| [https://www.lendico.de/](https://www.lendico.de/) | 429              | Limit of request by crefo Id exceeded.       | Limit of request, in a given amount of time, by crefo Id exceeded, try again later.                                                                                                             | Client error |
| [https://www.lendico.de/](https://www.lendico.de/) | 429              | Rate limit is exceeded.                      | see [Security Page](Security.html#throttling)                                                                                                                                                   | Client error |
| [https://www.lendico.de/](https://www.lendico.de/) | 500              | Internal Server Error                        | The internal exception                                                                                                                                                                          | Server error |
| [https://www.lendico.de/](https://www.lendico.de/) | 503              | Service Unavailable                          |                                                                                                                                                                                                 | Server error |

## Rejection Reasons

When an application is submitted we perform synchronized pre-validation, in case the company data is compliant with those basics checks the application move forward in our approval flow.
In case the prospect does not fulfil the basic validation criteria it will be automatically rejected with one or more rejection reasons.

### Rejection Response Body Syntax Example

```
{
    "version": 1,
    "rejection_reasons": [
        "BAD_INDUSTRY_CODE"
    ],
    "application_id": "de.ing.business.application.d2ab8ff8-c8bd-43fd-8f64-0150dc822099",
    "last_updated_at": "2021-12-20T16:54:23.359Z",
    "last_updated_by": "PARTNER",
    "status": "REJECTED"
}
```
or
```
{
    "version": 1,
    "rejection_reasons": [
        "BAD_INDUSTRY_CODE",
        "COMPANY_REVENUE_TOO_LOW"
    ],
    "application_id": "de.ing.business.application.d2ab8ff8-c8bd-43fd-8f64-0150dc822099",
    "last_updated_at": "2021-12-20T16:54:23.359Z",
    "last_updated_by": "PARTNER",
    "status": "REJECTED"
}
```

Note that the difference between a rejected application response and a non-rejected one is the `status` (`REJECTED`) and the addition of the `rejection_reasons` data point to the response DTO.

                                                                         
| Rejection reason           | Description                                                                                   |
|----------------------------|-----------------------------------------------------------------------------------------------|
| COMPANY_REVENUE_TOO_LOW    | The company must have at least 50k € revenue.                                                 |
| COMPANY_TOO_YOUNG          | The company must be at least two years old.                                                   |
| POOR_COMPANY_CREDIT_SCORE  | The company has a poor credit score.                                                          |
| BAD_LEGAL_FORM             | The company’s legal form is not accepted.                                                     |
| BAD_INDUSTRY_CODE          | At least one of the company’s industry codes are not acceptable.                              |
| COMPANY_LOCATION           | The company must be located in Germany.                                                       |
| COMPANY_CREFO_ID_NOT_FOUND | The company was not found in crefo.                                                           |
| INTERNAL_REASON            | The company was rejected in one of the screening phases due a internal reason(e.g risk check) |
