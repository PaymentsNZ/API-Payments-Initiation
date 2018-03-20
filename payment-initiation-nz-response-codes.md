# Payment API response codes

## HTTP response codes

The following are generic API response codes and do not necessarily reflect the status of payment processing.  API consumers **must** check the relevant fields of the payments and payment-submissions representations as returned for actual processing status.

### Return & Error codes

The following are the HTTP response codes for the different HTTP methods - across all Payment API endpoints.

|Situation|HTTP Status|Notes|Returned by POST|Returned by GET|
|:---|:---|:---|:---|:---|
|Query completed successfully|200 OK||No|Yes|
|Normal execution. The request has succeeded.|201 Created|The operation results in the creation of a new resource.|Yes|No|
|Delete operation completed successfully|204 No Content||No|No|
|Request has malformed, missing or non-compliant JSON body or URL parameters|400 Bad Request|The requested operation will not be carried out.|Yes|No|
|Authorization header missing or invalid token|401 Unauthorized|The operation was refused access. Re-authenticating the PSU may result in an appropriate token that can be used.|Yes|Yes|
|Token has incorrect scope or a security policy was violated.|403 Forbidden|The operation was refused access. Re-authenticating the PSU is unlikely to remediate the situation.|Yes|Yes|
|The TPP tried to access the resource with a method that is not supported.|405 Method Not Allowed||Yes|Yes|
|The request contained an accept header that requested a content-type other than application/json and a character set other than UTF-8|406 Not Acceptable||Yes|Yes|
|The operation was refused as too many requests have been made within a certain timeframe.|429 Too Many Requests|Throttling is a NFR. The ASPSP should include a Retry-After header in the response indicating how long the TPP must wait before retrying the operation.|Yes|Yes|
|Something went wrong on the API gateway or micro-service|500 Internal Server Error|The operation failed.|Yes|Yes|
An ASPSP **MAY** return other standard HTTP status codes (e.g. from gateways and other edge devices) as described in [RFC 7231 - Section 6.](https://tools.ietf.org/html/rfc7231#section-6)

#### 400 (Bad Request) v/s 404 (Not Found)

When a TPP tries to request a resource URL with an resource Id that does not exist, the ASPSP **must** respond with a 400 (Bad Request) rather than a 404 (Not Found).

E.g., if a TPP tries to GET /payments/22289 where 22289 is not a valid PaymentId, the ASPSP must respond with a 400.

If the TPP tries to access a URL for a resource that is not defined by these specifications (e.g. GET /card-accounts), the ASPSP **may** choose to respond with a 404 (Not Found).

If an ASPSP has not implemented an optional API, it **must** respond with a 404 (Not Found) for requests to that URL.

The table below illustrates some examples of expected behaviour:

|Situation|Request|Response|
|:---|:---|:---|
|TPP attempts to retrieve a resource that is in the specification, but not implemented by the ASPSP.  E.g., an ASPSP has chosen not to implement the status API endpoint for payment-submissions|GET /payment-submissions/1002|404 (Not Found)|
|TPP attempts to retrieve a resource that is not defined|GET /bulk|404 (Not Found)|
|TPP attempts to retrieve an account with a PaymentId that does not exist|GET /payments/1001|400 (Bad Request)|

#### 403 (Forbidden)

When a TPP tries to access a resource that it does not have permission to access, the ASPSP **must** return a 403 (Forbidden).

The situation could arise when:

* The TPP uses an access token that does not have the approporiate scope to access the requested resource.
* The TPP attempted to access a resource with an Id that it does not have access to. E.g., an attempt to access GET /payments/1001 where a payment resource with id 1001 belongs to another TPP.

When the TPP uses an access token that is no longer valid, the situation could potentially be remedied by asking the PSU to re-authenticate. This should be indicated by a 401 (Unauthorized) status code.

#### 429 (Too Many Requests)

When a TPP tries to access a resource too frequently the ASPSP **may** return a 429 (Too Many Requests).  This is a Non Functional Requirement and is down to individual ASPSPs to decide throttling limits.

This situation could arise when:

* A TPP decides to implement "Real Time Payment Status" functionality for its users and implements this badly by polling a GET endpoint or an Idempotent POST endpoint **once-per-second** constantly to provide pseudo "real-time" Status updates to the user.
* A TPP decides to use the Single Immediate Payment endpoint as if it were a BATCH payment facility and sends 1,000 payment requests in a very short space of time.

## Payment status by resource

The following list of ISO Transaction Codes is the complete set of payment processing statuses that the API returns.  Note that not all apply to all resources, and following sections will list code and and interpretation:

* Pending
* Rejected
* AcceptedTechnicalValidation
* AcceptedCustomerProfile
* AcceptedSettlementInProcess
* AcceptedSettlementCompleted

Not all method/resource combinations will return all codes. The following sub-sections list by method/resource the possible codes and their interpretation:

### POST /payments

Single Payment Setup Endpoint
`POST /payments`

The API allows the PISP to ask an ASPSP to create a new **payment** resource.

* This indicates to the ASPSP that a payment should be initiated. At this stage, the PSU may not have been identified by the ASPSP, and the request payload may not contain any information of the account that should be debited.
* This API effectively allows the PISP to send a copy of the consent to the ASPSP to authorise for this payment.

The ASPSP creates the payments resource and responds with a unique PaymentId to refer to the resource.

#### Payment Status

The Status field for the Payment API follows the behaviour and definitions for the ISO 20022 PaymentStatusCode code-set.

The payment resource that is created successfully must have one of the following PaymentStatusCode code-set:

||Status|Payment Status Description|
|:---|:---|:---|
|1|Pending|Payment initiation or individual transaction included in the payment initiation is pending.  Further checks and status update will be performed.|
|2|Rejected|Payment initiation or individual transaction included in the payment initiation has been rejected.|
|3|AcceptedTechnicalValidation|Authentication and syntactical and semantic validation are successful.|

### GET /payments/\{PaymentId\}

Single Payment Status Endpoint
`GET /payments/{PaymentId}`

A PISP can optionally retrieve a **payment** resource that they have created to check its status.

#### Payment Status

Once the PSU authorises the payment resource - the Status of the payment resource will be updated with AcceptedCustomerProfile.

The available PaymentStatusCode code-set enumerations for the payment resource are:

||Status|Payment Status Description|
|:---|:---|:---|
|1|Pending|Payment initiation or individual transaction included in the payment initiation is pending.  Further checks and status update will be performed.|
|2|Rejected|Payment initiation or individual transaction included in the payment initiation has been rejected.|
|3|AcceptedTechnicalValidation|Authentication and syntactical and semantic validation are successful.|
|4|AcceptedCustomerProfile|Preceding check of technical validation was successful. Customer profile check was also successful.|

### POST /payment-submissions

Single Payment Submission Endpoint
`POST /payment-submissions/`

Once the payment has been authorised by the PSU, the PISP can proceed to submitting the payment for processing:

* This is done by making a POST request to the **payment-submissions** resource.
* This request is an instruction to the ASPSP to begin the single immediate payment journey. The payment will be submitted immediately, however, there are some scenarios where the payment may not happen immediately (e.g. busy periods at the ASPSP).
* The PISP **must** ensure that the Initiation and Risk sections of the payment submission match the corresponding Initiation and Risk sections of the original payment resource. If the two do not match, the ASPSP **must not** process the request and **must** respond with a 400 (Bad Request).
* Any operations on the payment-submission resource will not result in a Status change for the payment resource.

#### Payment Submission Status

A payment-submission can only be created if its corresponding `/payments/{PaymentId}` resource has the status of 'AcceptedCustomerProfile'.

The payment-submission resource that is created successfully must have one of the following PaymentStatusCode code-set enumerations:

||Status|Payment Status Code|
|:---|:---|:---|
|1|Pending|Payment initiation or individual transaction included in the payment initiation is pending.  Further checks and status update will be performed.|
|2|Rejected|Payment initiation or individual transaction included in the payment initiation has been rejected.|
|3|AcceptedSettlementInProcess|All preceding checks such as technical validation and customer profile were successful and therefore the payment initiation has been accepted for execution.|

### GET /payment-submissions/\{PaymentSubmissionId\}

Single Payment Submission Status Endpoint
`GET/payment-submissions/{PaymentSubmissionId}`

A PISP can retrieve the payment-submission to check its status.

#### Payment Submission Status

The payment-submission resource must have one of the following PaymentStatusCode code-set enumerations:

||Status|Payment Status Description|
|:---|:---|:---|
|1|Pending|Payment initiation or individual transaction included in the payment initiation is pending.  Further checks and status update will be performed.|
|2|Rejected|Payment initiation or individual transaction included in the payment initiation has been rejected.|
|3|AcceptedSettlementInProcess|All preceding checks such as technical validation and customer profile were successful and therefore the payment initiation has been accepted for execution.|
|4|AcceptedSettlementCompleted|Settlement on the debtor's account has been completed.|
