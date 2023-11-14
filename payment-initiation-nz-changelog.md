# Payments API change log

---

## v2.1.2, v2.2.2, v2.3.2 - 25/10/2023

- Patch release to update all HTTP error responses to include standard error body.  This aligns with the documented requirement and Technical Working Group decision 029.

## v2.0.2, v2.1.1, v2.2.1, v2.3.1 - 4/11/2022

- Patch release to add `additionalProperties: false` to BECSRemittance DebtorReference and CreditorReference sub-schemas.  This prevents the possibility of additional JSON member values being supplied

## v2.3.0 - 19/06/2022

- No new changes to Swagger for v2.3.0 - as changes for the version are on scoping mandatory endpoint functionality

## v2.2.0 - 19/06/2022

- No new changes to Swagger for v2.2.0

## v2.1.0 - 01/12/2020

- New error code QueryParam.Invalid

## v2.0.1 - 17/07/2020

- Patch update to Swagger specification to include documented query parameters.

## v2.0.0 - 20/03/2020

Incorporate changes in preparation for v2.0.0 release, including:

- Version bump to v2.0.0 and URL to v2.0
- x-fapi-customer-last-logged-time updated to x-fapi-auth-date (per latest FAPI spec). Aligned definition of date type (per OBIE v3.1.2 spec)
- Content-Type aligned with Accept header rewording; updated Content-Type to Mandatory (align with OBIE v3.1.2)
- Added x-customer-user-agent (agreed for NZ v1.0 spec, but not documented)
- Added x-merchant-ip-address (agreed for NZ v1.0 spec, but not documented)
- 415 (Unsupported Media Type) for unsupported request payloads (per OBIE v3.1.2) - was not documented in v1.0 spec
- 503 (Service Unavailable) for deprecated resources (per OBIE v3.1.2)
- ErrorResponseStructure (align with v3.1.2 OBIE but reworded)
- Updated DeliveryAddress class in NZRisk1 to OBPostalAddress8 to align with Technical Decision - 009 - Standardising Address Objects
- Agreed error codes as per Technical Decision - 003 - Error Codes
- Updated third_party_client_credential scope to payments based on Technical Decision - 021 - OAuth
- Aligned naming of consents and payments endpoints with OBIE
- Added enduring-payment-consents endpoint
- Added domestic-payments/{DomesticPaymentId}/debtor-account endpoint

## V1.0.0 26/02/2019

Minor updates to prep for v1.0.0 release:

- Bumped version to v1.0.0 and URL to v1.0
- Changed `Status` in `PaymentResponse` and `PaymentSubmissionResponse` to required
- Removed `501 Not Implemented` on `GET` methods (mandatory to provide)
- Removed `DebtorAgent` object
- Added new Risk headers - `x-merchant-ip-address` and `x-customer-user-agent`
- Added new `Risk` object items per Risk discussion page
- Updated actor names to be consistent with NZ usage (PSU, ASPSP, TPP)
- Removed `OB:` references

## V0.3.1 19/02/2019

Refactored parameters and HTTP responses, no functional or interface change.

## V0.3.0 - 16/11/2018

Add full payment data to `payment-submissions` response (`GET` and `POST`). **Note:** this is different to upstream, but supports identification of debtor account for refund scenarios.

Version bump in swagger specification to v0.3.0 and URL to v0.3.

## V0.2.1 - 26/07/2018

Add Risk section in, same as upstream standard.
Remove IBAN and SortCodeAccountNumber from Debtor and Creditor AccountScheme enums. Leaving as `enum` for now, so values can be added later.

## V0.2.0 - 15/06/2018

- Version bump to v0.2.0 indicating re-inclusion of OpenID Connect into authorisation flow
- Basepath changed to /open-banking-nz/v0.2
- Minor correction to enums where 'BECSElectronicCredit' was erroneously 'BECS Electronic Credit' (enum values should not have spaces)

## V0.1.2 - 6/06/2018

- Changing URL to open-banking-nz
- Add 501 status for optional APIs

## v0.1.1 - 04/05/2018

- Added account number format description of 2-4-7-2 hyphen-delimited as recommended by Payments NZ working group and accepted by standards sub-group on 4/05/2018. This has been added to both Debtor and Creditor account identifiers in OpenAPI specification.

## v0.1.0 - 23/03/2018

- Changed base path to /open-banking/v0.1
- Version bump specification to v0.1.0 to reflect MINOR change to stabilise for pilot

## v0.0.5 - 12/03/2018

- Update BECSRemittance object to include DebtorName in reference information. This is particularly to support the merchant payment scenario, where no debtor account informaiton is supplied in the API request (account information)[] (only creditor/merchant information is supplied in the API request). Field is optional, and at the same object tree depth as CreditorName.

## V0.0.4 - 18/01/2018

- Minor update to BECSRemittance description. Advised characters was a-zA-Z, changed to a-z, A-Z, - (dash) and 0-9

## V0.0.3 - 19/12/2017

- Note the previously removed 'additionalProperties: false' from Links object, so that additional links can be used
- PaymentID changed to PaymentId in PaymentSubmission (typo)
- Updated info section (contact, licence info, terms of service)

## V0.0.2 - 11/12/2017

- Added description to BECSRemittance describing acceptable characters for Particulars, Code and Reference

## v0.0.1 - 30/11/2017

- Changed URLs and Emails in info section to use 'openbanking.org.nz' domain (was openbanking.org.uk)
  - Include reference to upstream project
- Created definitions section
- Added Meta, Risk, Links, PaymentInitiation models in definitions section
- Set specification version number to 0.0.1 and /open-banking/v1 as base path
- Refactored payments and payments-submission requests to use common PaymentInitiation and Risk model definitions
- Refactored payments and payments-submission responses to use PaymentResponse and PaymentSubmissionReponse, Risk, Links and Meta model definitions
- Added notes prefixed with OBNZ (Open Banking New Zealand) to document changes to existing data items
- Added RemittanceInformation (in PaymentInformation) required, with only CreditorName as mandatory
- Added Payments Scheme "BECS Electronic Credit" to PaymentInitiation DebtorAccount and CreditorAccount
  - Added a default to 'BECS Electronic Credit' to (hopefully) make it easier to identify
- New "BECSRemittance" object included in PaymentInitiation model
  - Particulars, Code, Reference (Debtor & Creditor)
  - Creditor Name (decision)
- InstructionIdentification and EndtoEndIdentification updated to 36 characters
- Risk object definition changed - MVP is blank object (object is mandatory but no fields are mandatory)
- Creditor and Debtor Agent - clarified that this is the BICFI ISO20022 and added link:
  [https://www.iso20022.org/standardsrepository/public/wqt/Description/mx/dico/datatypes/_YWZBNtp-Ed-ak6NoX_4Aeg_-1295138508](https://www.iso20022.org/standardsrepository/public/wqt/Description/mx/dico/datatypes/_YWZBNtp-Ed-ak6NoX_4Aeg_-1295138508)

- HTTP Headers changes:
  - x-idempotency-key is required and will be used (mandatory)
  - x-fapi-financial-id ignored (optional)
  - x-fapi-last-logged-in - ignored (optional)
  - x-fapi-customer-ip-address - ignored (optional)
  - x-fapi-interaction-id, will be echoed if received (optional)
  - x-jws-signature - ignored initially if supplied (optional)
