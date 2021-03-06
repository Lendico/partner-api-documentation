# Partner API

## <a name="tb-address-type"></a>  Address Types
The type of address that is being provide

| Value     | Description               |
|-----------|---------------------------|
| PRIMARY   | In case main address      |
| SECONDARY | In case secondary address |
| MAILING   | In case mailing address   |

## <a name="tb-roles"></a>  Representative Roles
The roles of the representative

| Value               | Description                                                                      |
|---------------------|----------------------------------------------------------------------------------|
| MD                  | Company's Manager Director                                                       |
| APPLICANT           | Company's Applicant( of the company()one representative must have this role)     |
| UBO                 | Company's Ultimate Beneficial Owner                                              |
| GUARANTOR           | Company's Guarantor                                                              |
| COMPLEMENTARY       | Company's Complementary                                                          |
| SME                 | Company's  Subject Matter Expert                                                 |
| BORROWER            | Company's Borrower                                                               |
| INVESTOR            | Company's Investor                                                               |


## <a name="tb-salutation"></a> Salutation

| Value   | Description |
|---------|-------------|
| MR      |             |
| MRS     |             |
| UNKNOWN |             |


## <a name="tb-legal-form"></a>  Legal Forms

German legal forms

| Value                                 | Description                                                                                    |
|---------------------------------------|------------------------------------------------------------------------------------------------|
| PARTG                                 | Partnerschaftsgesellschaft                                                                     | 
| PARTG_MBB                             | Partnerschaftsgesellschaft mit beschr??nkter Berufshaftung                                      | 
| GMBH                                  | Gesellschaft mit beschr??nkter Haftung                                                          | 
| GGMBH                                 | Gemeinn??tzige Gesellschaft mit beschr??nkter Haftung                                            | 
| UG                                    | Haftungsbeschr??nkt                                                                             | 
| GMBH_CO_KG                            | Gesellschaft mit beschr??nkter Haftung & Compagnie Kommanditgesellschaft                        | 
| UG_CO_KG                              | Haftungsbeschr??nkt & Compagnie Kommanditgesellschaft                                           | 
| KG                                    | Compagnie Kommanditgesellschaft                                                                | 
| EG                                    | Eingetragene Genossenschaft                                                                    | 
| AG                                    | Aktiengesellschaft                                                                             | 
| KG_CO_KG                              | Kommanditgesellschaft & Compagnie Kommanditgesellschaft                                        | 
| OHG                                   | Offene Handelsgesellschaft                                                                     | 
| KGAA                                  | Kommanditgesellschaft auf Aktien                                                               | 
| OHG_CO_KG                             | Offene Handelsgesellschaft & Compagnie Kommanditgesellschaft                                   | 
| EK                                    | Eingetragener Kaufmann                                                                         | 
| NEK                                   |                                                                                                | 
| FREIBERUFLER                          | Freiberufler                                                                                   | 
| SONSTIGE_A                            |                                                                                                | 
| SONSTIGE_B                            |                                                                                                | 
| ANDERE                                |                                                                                                | 
| EINZELFIRMA                           |                                                                                                | 
| FREIE_BERUFE                          | Gesellschaft b??rgerlichen                                                                      | 
| GBR                                   | Gesellschaft b??rgerlichen Arbeitsgemeinschaft                                                  |
| GBR_ARGE                              |                                                                                                | 
| GEWERBEBETRIEB                        |                                                                                                | 
| KORPERSCHAFT_OFFENTLICHEN_RECHTS      |                                                                                                | 
| LANDWIRTSCHAFTLICHER_BETRIEB          |                                                                                                | 
| LIMITED_CO_KG                         |                                                                                                | 
| SONSTIGE_RECHTSFORM                   |                                                                                                | 
| VEREIN                                |                                                                                                | 
| WIRTSCHAFTLICHE_ZWECKGEMEINSCHAFT     |                                                                                                | 

## <a name="tb-bookkeeping"></a>  Bookkeeping Standard
German bookkeeping standard of financial documents

| Value      | Description |
|------------|-------------|
| HGB_ESTR   |             |
| ESTG       |             |
| UNKNOWN    |             |

## <a name="tb-credit-score-type"></a>  Credit Score Type
Types of credit score accepted

| Value      | Description           |
|------------|-----------------------|
| CREFO      | Crefo credit score    |

## <a name="tb-product-code"></a> Product code
Accepted product code

| Value                         | Description                              |
|-------------------------------|------------------------------------------|
| TERM_LOAN_EQUAL_INSTALLMENT   | Product code for term loan installment   |

## <a name="tb-status"></a> Application Status
Application Status for the application response

| Value    | Description                                                                |
|----------|----------------------------------------------------------------------------|
| RECEIVED | The application passed in the hard criteria and was received by sales team |
| REJECTED | The application was rejected due to not passing in the hard criteria       |