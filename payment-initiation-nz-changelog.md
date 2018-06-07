# Payments API changelog

---
## V0.1.2 - 6/06/2018
Changing URL to open-banking-nz
Add 501 status for optional APIs

## v0.1.1 - 04/05/2018

* Added account number format description of 2-4-7-2 hyphen-delimited as recommended by Payments NZ working group and accepted by standards sub-group on 4/05/2018.  This has been added to both Debtor and Creditor account identifiers in OpenAPI specification.

## v0.1.0 - 23/03/2018

* Changed base path to /open-banking/v0.1
* Version bump specification to v0.1.0 to reflect MINOR change to stabilise for pilot

## v0.0.5 - 12/03/2018

* Update BECSRemittance object to include DebtorName in reference information.  This is particularly to support the merchant payment scenario, where no debtor account informaiton is supplied in the API request (account information)[] (only creditor/merchant information is supplied in the API request).  Field is optional, and at the same object tree depth as CreditorName.

## V0.0.4 - 18/01/2018

* Minor update to BECSRemittance description.  Advised characters was a-zA-Z, changed to a-z, A-Z, - (dash) and 0-9

## V0.0.3 - 19/12/2017

* Note the previously removed 'additionalProperties: false' from Links object, so that additional links can be used
* PaymentID changed to PaymentId in PaymentSubmission (typo)
* Updated info section (contact, licence info, terms of service)

## V0.0.2 - 11/12/2017

* Added description to BECSRemittance describing acceptable characters for Particulars, Code and Reference

## v0.0.1 - 30/11/2017

* Changed URLs and Emails in info section to use 'openbanking.org.nz' domain (was openbanking.org.uk)
  * Include reference to upstream project
* Created definitions section
* Added Meta, Risk, Links, PaymentInitiation models in definitions section
* Set specification version number to 0.0.1 and /open-banking/v1 as base path
* Refactored payments and payments-submission requests to use common PaymentInitiation and Risk model definitions
* Refactored payments and payments-submission responses to use PaymentResponse and PaymentSubmissionReponse, Risk, Links and Meta model definitions
* Added notes prefixed with OBNZ (Open Banking New Zealand) to document changes to existing data items
* Added RemittanceInformation (in PaymentInformation) required, with only CreditorName as mandatory
* Added Payments Scheme "BECS Electronic Credit" to PaymentInitiation DebtorAccount and CreditorAccount
  * Added a default to 'BECS Electronic Credit' to (hopefully) make it easier to identify
* New "BECSRemittance" object included in PaymentInitiation model
  * Particulars, Code, Reference (Debtor & Creditor)
  * Creditor Name (decision)
* InstructionIdentification and EndtoEndIdentification updated to 36 characters
* Risk object definition changed - MVP is blank object (object is mandatory but no fields are mandatory)
* Creditor and Debtor Agent - clarified that this is the BICFI ISO20022 and added link:
  [https://www.iso20022.org/standardsrepository/public/wqt/Description/mx/dico/datatypes/_YWZBNtp-Ed-ak6NoX_4Aeg_-1295138508](https://www.iso20022.org/standardsrepository/public/wqt/Description/mx/dico/datatypes/_YWZBNtp-Ed-ak6NoX_4Aeg_-1295138508)

* HTTP Headers changes:
  * x-idempotency-key is required and will be used (mandatory)
  * x-fapi-financial-id ignored (optional)
  * x-fapi-last-logged-in - ignored (optional)
  * x-fapi-customer-ip-address - ignored (optional)
  * x-fapi-interaction-id, will be echoed if received (optional)
  * x-jws-signature - ignored initially if supplied (optional)