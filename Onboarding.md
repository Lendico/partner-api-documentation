# Partner API
# Partner Onboarding

## 1 Onboarding
### 1.1 Business Onboarding
If you are here most likely you have already been through the business onboarding process.
But in case you have not, you need to request onboarding as a Lendico partner through [Partner Werden Page](https://openbusiness.ing.de/ing-partner-werden).
The Partner Management Team will be in touch for signing contracts, terms and conditions, privacy agreements etc.
In case you are eligible for using the API, we will start the API Onboarding.

### 1.2 API Onboarding

Before you can access Partner API, you first need to authenticate your application and retrieve an access token.
Partner API authenticates clients with the use of public key cryptography and uses OAuth 2.0 [RFC6749](https://datatracker.ietf.org/doc/html/rfc6749) as industry-standard protocol for authorization.

So some tasks need to be done before we can go ahead and try out any requests to the Partner API.
You need the following steps:
* First, you should already have a Microsoft AD tenant.
* Then we will request: 
  * List of IP addresses from where the Partner API will get the calls, for whitelisting them on our API.
  * List of IP addresses from where you will access the API Portal.
  * We need two different PKI key pairs. You can use official CA signed certificates, or use so called 'Self signed' certificates. See also the certificate specification in [the Authentication section of the security page](Security.md).
* Next, we will inform you to create an account in the [API Portal](https://api-portal.openbusiness.ing.de/), see how to sign up [here](ApiPortal.md#sign-up).
* After that we will send to the email account created in the first step, the credentials to access the API, the subscription key will be visible in the portal, [see API Portal Page](ApiPortal.md#exploring-apis-and-profile). 

## 2 Study this Guide
Read this guide to understand how to use the API.
In case any questions reach out to the [Partner API Tech Team or Partner API Business Team](Contacts.md).
### Request access to test environment

## 3 Request whitelisting
For sending requests to partner API your IP needs to be whitelisted, you can request a whitelisting of your IP here [Partner APIM Tech Team](Contacts.md).
We will send you then a subscription key that should be added in the header
`Subscription-Key`. See more about the subscription key [here](Security.md#subscription-key).

## 4 Test connection and access
For testing connectivity and access you do not need to POST any real data, you can call `GET /ping` you will get a string response `"You are connected to partner API!"` and HTTP status code 200, in case everything is working fine, in case not please contact the [Partner APIM Tech Team](Contacts.md).
See more about Test API [here](TestApi.md).

## Request access to production environment
Once you have tested out and is ready for production, request access to production to the [Partner API Tech Team](Contacts.md). 
