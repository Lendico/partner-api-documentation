
# Partner API
# Application API
The Application API is where an Application can be submitted through ```POST``` request or retrieved through ```GET``` request.
# Operations
## Submit Application
```POST /applications```
### Headers
Headers annotated with \* are mandatory

* `Idempotency-Key: <key>`\*  see [idempotency page](Idempotency.md).
* `Content-Type: application/json`.
  
## Retrieve Application
```GET /applications/{applicationId}```

*Note: Due to GDPR compliance, we must delete personal data from applications that were
rejected and were not converted to a loan, meaning that if you retrieve an application that 
falls in the GDPRs rules for data deletion the application might have anonymized data.*
## Application Schema

```
{
   "schemas":{
      "AddressDto":{
         "required":[
            "city",
            "country_code",
            "line_1",
            "type",
            "zip"
         ],
         "type":"object",
         "properties":{
            "city":{
               "maxLength":100,
               "minLength":0,
               "pattern":"[0-9a-zA-Z\\u00C0-\\u00D6\\u00D8-\\u00F6\\u00F8-\\u024F\\u1E00-\\u1EFF-/\\.,\\s\\(\\)\\$&\\+!\"#%'\\*:;<=>\\?@\\[\\]\\^_`\\{|\\}~§\\u005c\\u0081\\u0084\\u0092\\u0093\\u0094\\u0096\\u009a\\u00a0\\u00a1\\u00a6\\u00a9\\u00ab\\u00ac\\u00ad\\u00ae\\u00b0\\u00b2\\u00b3\\u00b4\\u00b5\\u00b6\\u00b7\\u00b9\\u00ba\\u00bb\\u00bc\\u00bf\\u00d7\\u0259\\u0301\\u0302\\u0308\\u0399\\u03a9\\u03bf\\u03c0\\u0430\\u043e\\u04eb\\u0627\\u062e\\u0646\\u064a\\u0686\\u06a4\\u2002\\u2003\\u2005\\u2009\\u200b\\u200e\\u2010\\u2013\\u2014\\u2018\\u2019\\u201a\\u201c\\u201d\\u201e\\u2022\\u2026\\u2027\\u2028\\u202a\\u202c\\u202f\\u2032\\u2033\\u2070\\u2074\\u2075\\u2077\\u2082\\u2083\\u20ac\\u2116\\u2122\\u221e\\u2502\\u25aa\\u25cf\\u30c4\\uf05a\\ufb01\\ufeff]+",
               "type":"string"
            },
            "zip":{
               "maxLength":6,
               "minLength":0,
               "pattern":"\\d{5}",
               "type":"string"
            },
            "type":{
               "type":"string",
               "description":"The type of address that is being provided, e.g PRIMARY",
               "enum":[
                  "PRIMARY",
                  "SECONDARY",
                  "MAILING"
               ]
            },
            "line_1":{
               "maxLength":255,
               "minLength":0,
               "pattern":"[0-9a-zA-Z\\u00C0-\\u00D6\\u00D8-\\u00F6\\u00F8-\\u024F\\u1E00-\\u1EFF-/\\.,\\s\\(\\)\\$&\\+!\"#%'\\*:;<=>\\?@\\[\\]\\^_`\\{|\\}~§\\u005c\\u0081\\u0084\\u0092\\u0093\\u0094\\u0096\\u009a\\u00a0\\u00a1\\u00a6\\u00a9\\u00ab\\u00ac\\u00ad\\u00ae\\u00b0\\u00b2\\u00b3\\u00b4\\u00b5\\u00b6\\u00b7\\u00b9\\u00ba\\u00bb\\u00bc\\u00bf\\u00d7\\u0259\\u0301\\u0302\\u0308\\u0399\\u03a9\\u03bf\\u03c0\\u0430\\u043e\\u04eb\\u0627\\u062e\\u0646\\u064a\\u0686\\u06a4\\u2002\\u2003\\u2005\\u2009\\u200b\\u200e\\u2010\\u2013\\u2014\\u2018\\u2019\\u201a\\u201c\\u201d\\u201e\\u2022\\u2026\\u2027\\u2028\\u202a\\u202c\\u202f\\u2032\\u2033\\u2070\\u2074\\u2075\\u2077\\u2082\\u2083\\u20ac\\u2116\\u2122\\u221e\\u2502\\u25aa\\u25cf\\u30c4\\uf05a\\ufb01\\ufeff]+",
               "type":"string",
               "description":"Street and house number",
               "example":"any street 2"
            },
            "line_2":{
               "maxLength":255,
               "minLength":0,
               "pattern":"[0-9a-zA-Z\\u00C0-\\u00D6\\u00D8-\\u00F6\\u00F8-\\u024F\\u1E00-\\u1EFF-/\\.,\\s\\(\\)\\$&\\+!\"#%'\\*:;<=>\\?@\\[\\]\\^_`\\{|\\}~§\\u005c\\u0081\\u0084\\u0092\\u0093\\u0094\\u0096\\u009a\\u00a0\\u00a1\\u00a6\\u00a9\\u00ab\\u00ac\\u00ad\\u00ae\\u00b0\\u00b2\\u00b3\\u00b4\\u00b5\\u00b6\\u00b7\\u00b9\\u00ba\\u00bb\\u00bc\\u00bf\\u00d7\\u0259\\u0301\\u0302\\u0308\\u0399\\u03a9\\u03bf\\u03c0\\u0430\\u043e\\u04eb\\u0627\\u062e\\u0646\\u064a\\u0686\\u06a4\\u2002\\u2003\\u2005\\u2009\\u200b\\u200e\\u2010\\u2013\\u2014\\u2018\\u2019\\u201a\\u201c\\u201d\\u201e\\u2022\\u2026\\u2027\\u2028\\u202a\\u202c\\u202f\\u2032\\u2033\\u2070\\u2074\\u2075\\u2077\\u2082\\u2083\\u20ac\\u2116\\u2122\\u221e\\u2502\\u25aa\\u25cf\\u30c4\\uf05a\\ufb01\\ufeff]+",
               "type":"string",
               "description":"Additional information eg. post box, floor"
            },
            "country_code":{
               "type":"string",
               "description":"ISO country code(ISO 3166-1 alpha-2) see https://www.iso.org/iso-3166-country-codes.html.(Note that it is only accepted Germany[DE] for the moment)",
               "example":"DE"
            }
         },
         "description":"List of addresses for the representative"
      },
      "ApplicationDetailsDto":{
         "required":[
            "product_code",
            "purpose"
         ],
         "type":"object",
         "properties":{
            "purpose":{
               "maxLength":255,
               "minLength":0,
               "pattern":"[0-9a-zA-Z\\u00C0-\\u00D6\\u00D8-\\u00F6\\u00F8-\\u024F\\u1E00-\\u1EFF-/\\.,\\s\\(\\)\\$&\\+!\"#%'\\*:;<=>\\?@\\[\\]\\^_`\\{|\\}~§\\u005c\\u0081\\u0084\\u0092\\u0093\\u0094\\u0096\\u009a\\u00a0\\u00a1\\u00a6\\u00a9\\u00ab\\u00ac\\u00ad\\u00ae\\u00b0\\u00b2\\u00b3\\u00b4\\u00b5\\u00b6\\u00b7\\u00b9\\u00ba\\u00bb\\u00bc\\u00bf\\u00d7\\u0259\\u0301\\u0302\\u0308\\u0399\\u03a9\\u03bf\\u03c0\\u0430\\u043e\\u04eb\\u0627\\u062e\\u0646\\u064a\\u0686\\u06a4\\u2002\\u2003\\u2005\\u2009\\u200b\\u200e\\u2010\\u2013\\u2014\\u2018\\u2019\\u201a\\u201c\\u201d\\u201e\\u2022\\u2026\\u2027\\u2028\\u202a\\u202c\\u202f\\u2032\\u2033\\u2070\\u2074\\u2075\\u2077\\u2082\\u2083\\u20ac\\u2116\\u2122\\u221e\\u2502\\u25aa\\u25cf\\u30c4\\uf05a\\ufb01\\ufeff]+",
               "type":"string"
            },
            "product_code":{
               "type":"string",
               "enum":[
                  "TERM_LOAN_EQUAL_INSTALLMENT"
               ]
            }
         },
         "description":"Specific fields for each product code",
         "discriminator":{
            "propertyName":"product_code"
         }
      },
      "ApplicationRequestDto":{
         "required":[
            "company",
            "details",
            "partner_account_manager"
         ],
         "type":"object",
         "properties":{
            "company":{
               "$ref":"#/components/schemas/CompanyDto"
            },
            "details":{
               "oneOf":[
                  {
                     "$ref":"#/components/schemas/LoanApplicationDetailsDto"
                  }
               ]
            },
            "partner_account_manager":{
               "$ref":"#/components/schemas/PartnerAccountManagerDto"
            }
         }
      },
      "CompanyDto":{
         "required":[
            "address",
            "bookkeeping_standard",
            "credit_scores",
            "financials",
            "foundation_date",
            "legal_form",
            "name",
            "representatives"
         ],
         "type":"object",
         "properties":{
            "fax":{
               "maxLength":30,
               "minLength":0,
               "type":"string"
            },
            "iban":{
               "type":"string"
            },
            "bic":{
               "maxLength":30,
               "minLength":0,
               "type":"string"
            },
            "address":{
               "$ref":"#/components/schemas/AddressDto"
            },
            "financials":{
               "maxItems":2147483647,
               "minItems":1,
               "uniqueItems":true,
               "type":"array",
               "description":"Yearly financial data, it should have at least the latest revenue",
               "items":{
                  "$ref":"#/components/schemas/FinancialDataDto"
               }
            },
            "representatives":{
               "maxItems":2147483647,
               "minItems":1,
               "uniqueItems":true,
               "type":"array",
               "description":"Representative, at least one(with at least applicant role) must be provided",
               "items":{
                  "$ref":"#/components/schemas/CompanyRepresentativeDto"
               }
            },
            "name":{
               "maxLength":400,
               "minLength":1,
               "pattern":"[0-9a-zA-Z\\u00C0-\\u00D6\\u00D8-\\u00F6\\u00F8-\\u024F\\u1E00-\\u1EFF-/\\.,\\s\\(\\)\\$&\\+!\"#%'\\*:;<=>\\?@\\[\\]\\^_`\\{|\\}~§\\u005c\\u0081\\u0084\\u0092\\u0093\\u0094\\u0096\\u009a\\u00a0\\u00a1\\u00a6\\u00a9\\u00ab\\u00ac\\u00ad\\u00ae\\u00b0\\u00b2\\u00b3\\u00b4\\u00b5\\u00b6\\u00b7\\u00b9\\u00ba\\u00bb\\u00bc\\u00bf\\u00d7\\u0259\\u0301\\u0302\\u0308\\u0399\\u03a9\\u03bf\\u03c0\\u0430\\u043e\\u04eb\\u0627\\u062e\\u0646\\u064a\\u0686\\u06a4\\u2002\\u2003\\u2005\\u2009\\u200b\\u200e\\u2010\\u2013\\u2014\\u2018\\u2019\\u201a\\u201c\\u201d\\u201e\\u2022\\u2026\\u2027\\u2028\\u202a\\u202c\\u202f\\u2032\\u2033\\u2070\\u2074\\u2075\\u2077\\u2082\\u2083\\u20ac\\u2116\\u2122\\u221e\\u2502\\u25aa\\u25cf\\u30c4\\uf05a\\ufb01\\ufeff]+",
               "type":"string"
            },
            "legal_form":{
               "type":"string",
               "enum":[
                  "PARTG",
                  "PARTG_MBB",
                  "GMBH",
                  "GGMBH",
                  "UG",
                  "GMBH_CO_KG",
                  "UG_CO_KG",
                  "KG",
                  "EG",
                  "AG",
                  "KG_CO_KG",
                  "OHG",
                  "KGAA",
                  "OHG_CO_KG",
                  "EK",
                  "NEK",
                  "FREIBERUFLER",
                  "SONSTIGE_A",
                  "SONSTIGE_B",
                  "ANDERE",
                  "EINZELFIRMA",
                  "FREIE_BERUFE",
                  "GBR",
                  "GBR_ARGE",
                  "GEWERBEBETRIEB",
                  "KORPERSCHAFT_OFFENTLICHEN_RECHTS",
                  "LANDWIRTSCHAFTLICHER_BETRIEB",
                  "LIMITED_CO_KG",
                  "SONSTIGE_RECHTSFORM",
                  "VEREIN",
                  "WIRTSCHAFTLICHE_ZWECKGEMEINSCHAFT"
               ]
            },
            "foundation_date":{
               "type":"string",
               "description":"Company foundation date",
               "format":"date",
               "example":"1977-01-12"
            },
            "bookkeeping_standard":{
               "type":"string",
               "description":"Bookkeeping standard of financial documents",
               "enum":[
                  "HGB_ESTR",
                  "ESTG",
                  "UNKNOWN"
               ]
            },
            "credit_scores":{
               "maxItems":2147483647,
               "minItems":1,
               "uniqueItems":true,
               "type":"array",
               "items":{
                  "$ref":"#/components/schemas/CreditScoreDto"
               }
            }
         },
         "description":"The company that is applying"
      },
      "CompanyRepresentativeDto":{
         "required":[
            "addresses",
            "birthday",
            "email",
            "first_name",
            "last_name",
            "nationality",
            "phone_number",
            "roles",
            "salutation"
         ],
         "type":"object",
         "properties":{
            "roles":{
               "maxItems":2147483647,
               "minItems":1,
               "uniqueItems":true,
               "type":"array",
               "description":"Roles of the representative",
               "items":{
                  "type":"string",
                  "description":"Roles of the representative",
                  "enum":[
                     "MD",
                     "UBO",
                     "GUARANTOR",
                     "COMPLEMENTARY",
                     "SME",
                     "BORROWER",
                     "APPLICANT",
                     "INVESTOR"
                  ]
               }
            },
            "title":{
               "maxLength":100,
               "minLength":0,
               "type":"string"
            },
            "salutation":{
               "type":"string",
               "enum":[
                  "MR",
                  "MRS",
                  "UNKNOWN"
               ]
            },
            "email":{
               "type":"string",
               "description":"Email of representative.",
               "example":"someemail@someprovider.com"
            },
            "birthday":{
               "type":"string",
               "description":"Date of birth of the representative.",
               "format":"date",
               "example":"1939-06-09"
            },
            "addresses":{
               "maxItems":2147483647,
               "minItems":1,
               "uniqueItems":true,
               "type":"array",
               "description":"List of addresses for the representative",
               "items":{
                  "$ref":"#/components/schemas/AddressDto"
               }
            },
            "nationality":{
               "pattern":"[a-zA-Z\\u00C0-\\u00D6\\u00D8-\\u00F6\\u00F8-\\u024F\\u1E00-\\u1EFF-\\s\\.]+",
               "type":"string",
               "description":"Nationality, ISO country code(ISO 3166-1 alpha-2) see https://www.iso.org/iso-3166-country-codes.html.",
               "example":"DE"
            },
            "first_name":{
               "maxLength":100,
               "minLength":0,
               "pattern":"[a-zA-Z\\u00C0-\\u00D6\\u00D8-\\u00F6\\u00F8-\\u024F\\u1E00-\\u1EFF-\\s\\.]+",
               "type":"string"
            },
            "last_name":{
               "maxLength":100,
               "minLength":0,
               "pattern":"[a-zA-Z\\u00C0-\\u00D6\\u00D8-\\u00F6\\u00F8-\\u024F\\u1E00-\\u1EFF-\\s\\.]+",
               "type":"string"
            },
            "phone_number":{
               "type":"string",
               "description":"Phone number of the representative.",
               "example":"+490000000000"
            }
         },
         "description":"Representative, at least one(with at least applicant role) must be provided"
      },
      "CreditScoreDto":{
         "required":[
            "id",
            "type"
         ],
         "type":"object",
         "properties":{
            "history":{
               "uniqueItems":true,
               "type":"array",
               "items":{
                  "$ref":"#/components/schemas/ScoreDto"
               }
            },
            "id":{
               "maxLength":100,
               "minLength":0,
               "type":"string"
            },
            "type":{
               "type":"string",
               "enum":[
                  "CREFO"
               ]
            }
         }
      },
      "FinancialDataDto":{
         "required":[
            "currency",
            "revenue",
            "year"
         ],
         "type":"object",
         "properties":{
            "year":{
               "type":"integer",
               "description":"Year of the provided financial data",
               "format":"int32",
               "example":2022
            },
            "revenue":{
               "type":"number",
               "description":"Value of the revenue for the provided financial data",
               "example":200000
            },
            "currency":{
               "type":"string",
               "description":"Currency code for the provided financial data. See https://www.iso.org/iso-4217-currency-codes.html",
               "example":"EUR"
            }
         },
         "description":"Yearly financial data, it should have at least the latest revenue"
      },
      "LoanApplicationDetailsDto":{
         "required":[
            "currency",
            "product_code",
            "purpose",
            "request_amount",
            "request_term",
            "request_term_unit"
         ],
         "type":"object",
         "allOf":[
            {
               "$ref":"#/components/schemas/ApplicationDetailsDto"
            },
            {
               "type":"object",
               "properties":{
                  "currency":{
                     "type":"string",
                     "description":"Currency code for the loan request. See https://www.iso.org/iso-4217-currency-codes.html",
                     "example":"EUR"
                  },
                  "request_amount":{
                     "minimum":0,
                     "exclusiveMinimum":true,
                     "type":"number",
                     "description":"Amount request for loan",
                     "example":200000
                  },
                  "request_term":{
                     "minimum":1,
                     "type":"integer",
                     "description":"Duration of the loan request",
                     "format":"int32",
                     "example":12
                  },
                  "request_term_unit":{
                     "type":"string",
                     "description":"Unit used for the duration",
                     "example":"MONTH",
                     "enum":[
                        "DAY",
                        "MONTH",
                        "YEAR",
                        "WEEK"
                     ]
                  }
               }
            }
         ]
      },
      "PartnerAccountManagerDto":{
         "required":[
            "email",
            "first_name",
            "last_name",
            "phone_number",
            "salutation"
         ],
         "type":"object",
         "properties":{
            "salutation":{
               "type":"string",
               "enum":[
                  "MR",
                  "MRS",
                  "UNKNOWN"
               ]
            },
            "email":{
               "type":"string",
               "description":"Email of partner account manager.",
               "example":"someemail@someprovider.com"
            },
            "first_name":{
               "maxLength":100,
               "minLength":0,
               "pattern":"[a-zA-Z\\u00C0-\\u00D6\\u00D8-\\u00F6\\u00F8-\\u024F\\u1E00-\\u1EFF-\\s\\.]+",
               "type":"string"
            },
            "last_name":{
               "maxLength":100,
               "minLength":0,
               "pattern":"[a-zA-Z\\u00C0-\\u00D6\\u00D8-\\u00F6\\u00F8-\\u024F\\u1E00-\\u1EFF-\\s\\.]+",
               "type":"string"
            },
            "phone_number":{
               "type":"string",
               "description":"Phone number of the partner account manager.",
               "example":"+490000000000"
            }
         },
         "description":"The partner account manager applying on behalf of the customer"
      },
      "ScoreDto":{
         "required":[
            "date",
            "score_value"
         ],
         "type":"object",
         "properties":{
            "date":{
               "type":"string",
               "description":"The date of the presented score",
               "format":"date",
               "example":"2021-11-24"
            },
            "score_value":{
               "maxLength":10,
               "minLength":0,
               "type":"string"
            }
         }
      }
   }
}
```
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
        "id":"201000454565",
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

| Field                    | Type   | Description                                                                                                                | Example                                                      |
|--------------------------|--------|----------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------|
| company*                 | object | The prospect's information that is applying,  <br /> see [company](ApplicationApi.md#company)                            | See [Json sytanx](ApplicationApi.md#-json-syntax-response) |
| details*                 | object | Details of the payload, the fields vary by product, <br /> see [details](ApplicationApi.md#details)                      | See [Json sytanx](ApplicationApi.md#-json-syntax-response) |
| partner_account_manager* | object | The partner account manager information, <br /> see [partner_account_manager](ApplicationApi.md#partner-account-manager) | See [Json sytanx](ApplicationApi.md#-json-syntax-response) |

## Company

| Field                 | Type     | Description                                                                                                                                                        | Example                                                          |
|-----------------------|----------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|
| name*                 | string   | Company's name                                                                                                                                                     | Company name                                                     |
| fax                   | string   | Company's fax number                                                                                                                                               | +49 (0) XXXX XXXX-39                                             |
| iban                  | string   | Company's IBAN                                                                                                                                                     | DEXXXXXXXXXXXXXXXXXXXX                                           |
| bic                   | string   | Company's BIC                                                                                                                                                      | INGDDEFFXXX                                                      |
| address*              | object   | Company's address, <br> see [address](ApplicationApi.md#address)                                                                                                 | See <br>[Json sytanx](ApplicationApi.md#-json-syntax-response) |
| financials*           | object[] | Yearly financial data, it should have <br>at least the latest revenue<br>see [financial_data](#tb-fd-fields)<br>**Should have at least the latest financial data** | See <br>[Json sytanx](ApplicationApi.md#-json-syntax-response) |
| representatives*      | object[] | List of company's representative,<br>see [representative](ApplicationApi.md#representative)<br>                                                                  | See <br>[Json sytanx](ApplicationApi.md#-json-syntax-response) |
| legal_form*           | enum     | Company's legal form<br>see [legal_form](Enums.md#--legal-forms)                                                                                                 | `GmBH`                                                           |
| foundation_date*      | date     | Foundation date of the company                                                                                                                                     | `1977-01-12`                                                     |
| bookkeeping_standard* | enum     | Bookkeeping standard of financial documents see [bookkeeping_standard](Enums.md#--bookkeeping-standard---annual-financial-statement)                             | `ESTG`                                                           |
| credit_scores*        | object[] | Credit score information, right now we accept <br>only crefo.<br>see [credit_score](Enums.md#--credit-score-type)                                                | See <br>[Json sytanx](ApplicationApi.md#-json-syntax-response) |

## Address

| Field         | Type   | Description                                                                                   | Example     |
|---------------|--------|-----------------------------------------------------------------------------------------------|-------------|
| city*         | string | Address city                                                                                  | Berlin      |
| type*         | enum   | Type of address<br>see [bookkeeping_standard](Enums.md#--address-types)                     | `PRIMARY`   |
| line_1*       | string | Street and number                                                                             | Nice str 1. |
| line_2        | string | Complement for address                                                                        | `4o Flor`   |
| country_code* | string | ISO country code(ISO 3166-1 alpha-2) <br>see https://www.iso.org/iso-3166-country-codes.html. | `DE`        |

## Representative
Representative of the company

| Field         | Type     | Description                                                                                                | Example                                                      |
|---------------|----------|------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------|
| roles*        | enum[]   | List of roles of the representative see [roles](Enums.md#--representative-roles)                         | `[APPLICANT, MD]`                                            |
| title         | string   | Title of the representative                                                                                | `DR`                                                         |
| salutation*   | enum     | Representative salutation<br>see [salutation](Enums.md#-salutation)                                      | `MRS`                                                        |
| email*        | string   | Email of the representative                                                                                | `4o Flor`                                                    |
| birthday*     | date     | Date of birth of the representative                                                                        | `DE`                                                         |
| addresses*    | object[] | List of addresses of the representative                                                                    | See [Json sytanx](ApplicationApi.md#-json-syntax-response) |
| nationality*  | string   | Nationality, ISO country code(ISO 3166-1 alpha-2) <br>see https://www.iso.org/iso-3166-country-codes.html. | `DE`                                                         |
| first_name*   | string   | First name of the representative                                                                           | `Thomas`                                                     |
| last_name*    | string   | Last name of the representative                                                                            | `Muller`                                                     |
| phone_number* | string   | Phone number of the representative(For the moment we only accept Geman phone numbers)                      | `+490000000000`                                              |

## Credit Scores
Credit score information

| Field   | Type     | Description                                                                               | Example      |
|---------|----------|-------------------------------------------------------------------------------------------|--------------|
| id*     | string   | The id of the credit provided                                                             | `2000000021` |
| type*   | enum     | The type of credit provided<br>see [credit_type](Enums.md#--credit-score-type)          | `CREFO`      |
| history | object[] | List of the credit score history <br>see [credit-score](ApplicationApi.md#credit-score) | `CREFO`      |

## Credit score

| Field       | Type   | Description                    | Example      |
|-------------|--------|--------------------------------|--------------|
| date        | date   | The date of the score provided | `2021-05-21` |
| score_value | string | Credit score                   | `300`        |

## Financial Data

| Field     | Type   | Description                                                                                                                                 | Example      |
|-----------|--------|---------------------------------------------------------------------------------------------------------------------------------------------|--------------|
| year*     | number | The year of the provided financial data                                                                                                     | `2021`       |
| revenue*  | number | The revenue of the provided financial data                                                                                                  | `1000000.00` |
| currency* | string | Currency code for the provided financial data<br>see https://www.iso.org/iso-4217-currency-codes.html(<br> for now it is only accepted EUR) | `EUR`        |

## Details
### Team Loan Equal Installment

| Field              | Type   | Description                                                                                                                    | Example                       |
|--------------------|--------|--------------------------------------------------------------------------------------------------------------------------------|-------------------------------|
| purpose*           | string | The purpose of the application                                                                                                 | `Wachstumskapital`            |
| product_code*      | enum   | Code of the product <br>see [product_code](Enums.md#-product-code)                                                           | `TERM_LOAN_EQUAL_INSTALLMENT` |
| currency*          | string | Currency of the requested amount<br> se https://www.iso.org/iso-4217-currency-codes.html(<br> for now it is only accepted EUR) | `EUR`                         |
| request_amount*    | number | Amount of the request                                                                                                          | `20000.00`                    |
| request_term*      | number | Duration of the request                                                                                                        | `12`                          |
| request_term_unit* | enum   | Duration unit of the request <br>see [term_unit](Enums.md#--term-units)                                                      | `MONTH`                       |

## Partner Account Manager

| Field         | Type   | Description                                                                        | Example         |
|---------------|--------|------------------------------------------------------------------------------------|-----------------|
| salutation*   | enum   | Partner account manager salutation<br>see <br>[salutation](Enums.md#-salutation) | `MRS`           |
| email*        | string | Email of the partner account manager                                               | `4o Flor`       |
| first_name*   | string | First name of the partner account manager                                          | `Thomas`        |
| last_name*    | string | Last name of the partner account manager                                           | `Muller`        |
| phone_number* | string | Phone number of the partner account manager                                        | `+490000000000` |

## Response


| Field             | Type      | Description                                                                         | Example                                                            |
|-------------------|-----------|-------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| version           | number    | The version of the resource                                                         | `1`                                                                |
| rejection_reasons | string[]  | List of rejections <br>see [rejection reasons](CommonErrors.md#rejection-reasons) | `[COMPANY_TOO_YOUNG]`                                              |
| application_id    | string    | Id of the application                                                               | `de.ing.business.application.eb237cea-1f7d-4e87-9a45-53ee3665b5fa` |
| last_updated_at   | date-time | The last time that the application was modified                                     | `2021-12-06T08:26:38.458Z`                                         |
| last_updated_by   | string    | Last actor that modified the application                                            | `PARTNER`                                                          |
| status            | enum      | The status of the application<br>see [status](Enums.md#-application-statuses)     | `REJECTED`                                                         |

Note: The difference between a rejected application response and a non-rejected one is the `status` (`REJECTED`) and the addition of the `rejection_reasons` data point to the response DTO.
