
# Partner API
# Application API
The Application API is where an Application can be submitted through ```POST``` request or retrieved through ```GET``` request.
# Operations
## Submit Application
```POST /applications```
### Headers
Headers annotated with \* are mandatory

* ```Subscription-Key: <key>```\*  for more details see [security page](Security.md) and [tutorials page](Tutorials.md)
* ```Idempotency-Key: <key>```\*  see [idempotency page](Idempotency.md).
  
## Retrieve Application
```GET /applications/{applicationId}```
### Headers
Headers anotated with \* are mandatory

```Subscription-key: <key>```\* , for more details see [security page](Security.md) and [tutorials page](Tutorials.md)


## Endpoint
### Development Endpoint
To be added

### Production Endpoint
To be added


## Application Schema

### <a name="application-json-sintax-pl"></a> Json Syntax Payload
```
{
  "company":{
    "name":"Wonderland company",
    "fax":"",
    "iban":"DEXXXXXXXXXXXXXXXXXXXX",
    "bic":"INGDDEFFXXX",
    "address":{
      "line_1":"Some street. 81",
      "line_2":"4 floor",
      "city":"Berlin",
      "zip":"10000",
      "country_code":"DE",
      "type": "PRIMARY"
    },
    "financials":[
      {
        "year":2021,
        "revenue":"2000000.00",
        "currency":"EUR"
      }
    ],
    "representatives":[
      {
        "roles":[
          "APPLICANT"
        ],
        "title":"title",
        "salutation":"MR",
        "email":"lendico@lendico.de",
        "birthday":"1939-06-09",
        "addresses":[
          {
            "line1":"Some Street 7",
            "line2":"4 floor",
            "city":"Berlin",
            "zip":"10000",
            "country_code":"DE",
            "type": "PRIMARY"
          }
        ],
        "nationality":"DE",
        "first_name":"Firstname",
        "last_name":"Lastname",
        "phone_number":"+490000000000"
      }
    ],
    "legal_form":"GMBH",
    "foundation_date":"1977-01-12",
    "bookkeeping_standard":"ESTG",
    "credit_scores":[
      {
        "history":[
          {
            "date":"2021-11-24",
            "score_value":"200"
          }
        ],
        "id":"2010000001",
        "type":"CREFO"
      }
    ]
  },
  "details":{
    "purpose":"Algum",
    "request_amount":"200000.00",
    "currency":"EUR",
    "request_term":30,
    "product_code":"TERM_LOAN_EQUAL_INSTALLMENT",
    "request_term_unit":"MONTH"
  },
  "partner_account_manager":{
    "salutation":"MR",
    "email":"partner@lendico.de",
    "first_name":"Firstname",
    "last_name":"Lastname",
    "phone_number":"+490000000000"
  }
}
```


### <a name="application-json-sintax-resp"></a> Json Syntax Response

```
{
    "version": 0,
    "rejectionReasons": [
        "BAD_INDUSTRY_CODE"
    ],
    "application_id": "de.ing.business.application.eb237cea-1f7d-4e87-9a45-53ee3665b5fa",
    "last_updated_at": "2021-12-06T08:26:38.458Z",
    "last_updated_by": "PARTNER",
    "status": "REJECTED"
}
```

## Fields
  Fields annotated with \* are mandatory.
## Application

| Field                    | Type   | Description                                                                                   | Example                                     |
|--------------------------|--------|-----------------------------------------------------------------------------------------------|---------------------------------------------|
| company*                 | object | The prospect's information that is applying,  <br /> see [company](#tb-company-fields)        | See [Json sytanx](#application-json-sintax) |
| details*                 | object | Details of the payload, the fields vary by product, <br /> see [details](#tb-details-fields)  | See [Json sytanx](#application-json-sintax) |
| partner_account_manager* | object | The partner account manager information, <br /> see [partner_account_manager](#tb-pam-fields) | See [Json sytanx](#application-json-sintax) |

## Company

| Field                 | Type     | Description                                                                                                                                                        | Example                                         |
|-----------------------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------|
| name                  | string   | Company's name                                                                                                                                                     | A very nice company name                        |
| fax                   | string   | Company's fax number                                                                                                                                               | +49 (0) XXXX XXXX-39                            |
| iban                  | string   | Company's IBAN                                                                                                                                                     | DEXXXXXXXXXXXXXXXXXXXX                          |
| bic                   | string   | Company's BIC                                                                                                                                                      | INGDDEFFXXX                                     |
| address*              | object   | Company's address, <br> see [address](#tb-address-fields)                                                                                                          | See <br>[Json sytanx](#application-json-sintax) |
| financials*           | object[] | Yearly financial data, it should have <br>at least the latest revenue<br>see [financial_data](#tb-fd-fields)<br>**Should have at least the latest financial data** | See <br>[Json sytanx](#application-json-sintax) |
| representatives*      | object[] | List of company's representative,<br>see [representative](#tb-rep-fields)<br>                                                                                      | See <br>[Json sytanx](#application-json-sintax) |
| legal_form*           | enum     | Company's legal form<br>see [legal_form](Enums.md#tb-lf-fields)                                                                                                    | `GmBH`                                          |
| foundation_date*      | date     | Foundation date of the company                                                                                                                                     | `1977-01-12`                                    |
| bookkeeping_standard* | enum     | Bookkeeping standard of financial documents see [bookkeeping_standard](Enums.md#tb-bk-fields)                                                                      | `ESTG`                                          |
| credit_scores*        | object[] | Credit score information, right now we accept <br>only crefo.<br>see [credit_score](#tb-cs-fields)                                                                 | See <br>[Json sytanx](#application-json-sintax) |

## Address

| Field          | Type   | Description                                                                                                | Example     |
|----------------|--------|------------------------------------------------------------------------------------------------------------|-------------|
| city*          | string | Address city                                                                                               | Berlin      |
| type*          | enum   | Type of address<br>see [bookkeeping_standard](Enums.md#tb-bk-fields)                                       | `PRIMARY`   |
| line_1*        | string | Street and number                                                                                          | Nice str 1. |
| line_2         | string | Complement for address                                                                                     | `4o Flor`   |
| country_code*  | string | ISO country code(ISO 3166-1 alpha-2) <br>see https://www.iso.org/iso-3166-country-codes.html.              | `DE`        |

## Representative
Representative of the company

| Field         | Type     | Description                                                                                                | Example                                     |
|---------------|----------|------------------------------------------------------------------------------------------------------------|---------------------------------------------|
| roles*        | enum[]   | List of roles of the representative see [roles](Enums.md#tb-roles)                                         | `[APPLICANT, MD]`                           |
| title         | string   | Title of the representative                                                                                | `DR`                                        |
| salutation*   | enum     | Representative salutation<br>see [salutation](Enums.md#tb-salutation)                                      | `MRS`                                       |
| email*        | string   | Email of the representative                                                                                | `4o Flor`                                   |
| birthday*     | date     | Date of birth of the representative                                                                        | `DE`                                        |
| addresses*    | object[] | List of addresses of the representative                                                                    | See [Json sytanx](#application-json-sintax) |
| nationality*  | string   | Nationality, ISO country code(ISO 3166-1 alpha-2) <br>see https://www.iso.org/iso-3166-country-codes.html. | `DE`                                        |
| first_name*   | string   | First name of the representative                                                                           | `Thomas`                                    |
| last_name*    | string   | Last name of the representative                                                                            | `Muller`                                    |
| phone_number* | string   | Phone number of the representative                                                                         | `+490000000000`                             |

## Credit Scores
Credit score information

| Field   | Type     | Description                                                               | Example      |
|---------|----------|---------------------------------------------------------------------------|--------------|
| id*     | string   | The id of the credit provided                                             | `2000000021` |
| type*   | enum     | The type of credit provided<br>see [credit_type](Enums.md#tb-credit-type) | `CREFO`      |
| history | object[] | List of the credit score history <br>see [credit-score](#tb-credit-score  | `CREFO`      |

## Credit store

| Field        | Type   | Description                    | Example      |
|--------------|--------|--------------------------------|--------------|
| date*        | date   | The date of the score provided | `2021-05-21` |
| score_value* | string | Credit score                   | `300`        |

## Financial Data

| Field     | Type   | Description                                                                                           | Example      |
|-----------|--------|-------------------------------------------------------------------------------------------------------|--------------|
| year*     | number | The year of the provided financial data                                                               | `2021`       |
| revenue*  | number | The revenue of the provided financial data                                                            | `1000000.00` |
| currency* | string | Currency code for the provided financial data<br>see https://www.iso.org/iso-4217-currency-codes.html | `EUR`        |

## Details
### Team Loan Equal Installment

| Field              | Type   | Description                                                                               | Example                       |
|--------------------|--------|-------------------------------------------------------------------------------------------|-------------------------------|
| purpose*           | string | The purpose of the application                                                            | `Wachstumskapital`            |
| product_code*      | enum   | Code of the product <br>see [product_code](Enums.md#tb-product-code)                      | `TERM_LOAN_EQUAL_INSTALLMENT` |
| currency*          | string | Currency of the requested amount<br> se https://www.iso.org/iso-4217-currency-codes.html  | `EUR`                         |
| request_amount*    | number | Amount of the request                                                                     | `20000.00`                    |
| request_term*      | number | Duration of the request                                                                   | `12`                          |
| request_term_unit* | enum   | Duration unit of the request <br>see [term_unit](Enums.md#tb-term-unit)                   | `MONTH`                       |

## Partner Account Manager

| Field         | Type   | Description                                                                        | Example         |
|---------------|--------|------------------------------------------------------------------------------------|-----------------|
| salutation*   | enum   | Partner account manager salutation<br>see <br>[salutation](Enums.md#tb-salutation) | `MRS`           |
| email*        | string | Email of the partner account manager                                               | `4o Flor`       |
| first_name*   | string | First name of the partner account manager                                          | `Thomas`        |
| last_name*    | string | Last name of the partner account manager                                           | `Muller`        |
| phone_number* | string | Phone number of the partner account manager                                        | `+490000000000` |

## Response


| Field             | Type      | Description                                                                 | Example                                                            |
|-------------------|-----------|-----------------------------------------------------------------------------|--------------------------------------------------------------------|
| version           | number    | The version of the resource                                                 | `1`                                                                |
| rejection_reasons | string[]  | List of rejections <br>see [rejection reasons](CommonErros.md#tb-rejection) | `[COMPANY_TOO_YOUNG]`                                              |
| application_id    | string    | Id of the application                                                       | `de.ing.business.application.eb237cea-1f7d-4e87-9a45-53ee3665b5fa` |
| last_updated_at   | date-time | The last time that the application was modified                             | `2021-12-06T08:26:38.458Z`                                         |
| last_updated_by   | string    | Last actor that modified the application                                    | `PARTNER`                                                          |
| status            | enum      | The status of the application<br>see [status](Enums.md#tb-status)           | `REJECTED`                                                         |

Note that the difference between a rejected application response and a non rejected one is the `status` (`REJECTED`) and the addition of the `rejection_reasons` data point to the response DTO.
