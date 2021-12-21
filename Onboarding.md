# Partner API
# Partner Onboarding
# 1. Business Onboarding
If you are here most likely you have already been through the business onboarding process.
But in case you have not, you need to request onboarding as a Lendico partner through [Partner Werden Page](https://www.lendico.de/partner-werden).
The Development team will be in contact for signing contracts, terms and conditions, privacy agreements etc.
In case you are eligible for using the API, you will receive the information with your credentials.

# 2. Study this Guide
Read this guide to understand how to use the API.
In case any questions reach out to the [Partner API Tech Team or Partner API Business Team](Contacts.md).
# Request access to test environment
## 3. Create a Partner API Account
For sending requests through Partner API you will need an account for the OAuth2 server.
[see](Security.md).
***To check, the account will be a partner of the onboarding or we will be requested later to the partner**

## 4. Request whitelisting
For sending requests to partner API your IP needs to be whitelisted, you can request a whitelisting of your IP here [Partner APIM Tech Team](Contacts.md).
We will send you then a subscription key that should be added in the header
`Subscription-Key`. See more about the subscription key [here](Security.md).

## 5. Test connection and access
For testing connectivity and access you do not need to POST any real data, you can call `GET /ping` you will get a string response `"You are connected to partner API!"` and HTTP status code 200, in case everything is working fine, in case not please contact the [Partner APIM Tech Team](Contacts.md).
See more about Test API [here](TestApi.md).

# Request access to production environment
Once you have tested out and is ready for production, request access to production to the [Partner API Tech Team](Contacts.md). 