# Mapping of POLi to Open Banking UK error codes

## Sources of data

There are two sources of data for this mapping:

1. The POLi error codes as listed on the [website](http://www.polipaymentdeveloper.com/errorcodes)
2. The Open Banking UK website, specifically [here](https://openbanking.atlassian.net/wiki/spaces/DZ/pages/5786479/Payment+Initiation+API+Specification+-+v1.1.0#PaymentInitiationAPISpecification-v1.1.0-Return&ErrorCodes) and [here](https://openbanking.atlassian.net/wiki/spaces/DZ/pages/5786479/Payment+Initiation+API+Specification+-+v1.1.0#PaymentInitiationAPISpecification-v1.1.0-TransactionStatus)

## Option for conveying additional information

Since the upstream specification is somewhat limited in its ability to return a wide variety of resposne codes, there exists an option to use the JSON API-compliant "Links" section to return links with additional error code information.  This conforms to the `HATEOAS` (Hypermedia As The Engine Of Application State) part of the [Uniform Interface](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm#sec_5_1_5) constraint of REST (and the Payments NZ work is following REST for its API specification).

## Mapping table

The following table shows the source POLi code table, extended with 3 additional columns:

* The HTTP code from Open Banking UK document
* Transaction status (where applicable)
* Comments on the mapping

Poli Error Code|Description|Condition|Recommendation|HTTP code|Transaction Status|Comments
---|---|---|---|---|---|---
1001|Invalid Token|The token specified in the request corresponds to a POLi ID that does not exist in the database.|Please contact POLi Support.|201|Rejected|No merchant token included in specification currently. Payment resource created, but unable to process further (submission will be rejected).
1002|Illegal Token|The token specified in the request corresponds to a POLi ID that does not belong to the specified merchant.|Please confirm that the specified merchant code is correct. If the issue persists please contact POLi Support.|201|Rejected|Payment resource created, but unable to process further (submission will be rejected).
1003|Invalid Merchant Code|The merchant code specified in the request does not exist in the database.|Please check that you have input the given merchant code correctly. If you are sure you have, please contact POLi Support.|201|Rejected|No merchant token included in specification currently. Incorrect IDs are generally considered a client error (400) but in this case the server resource is created but payment rejected
1004|Inactive Merchant|The merchant code specified in the request corresponds to a merchant that is inactive.|The merchant code you are using has been set to inactive. If you think this is an error, please contact POLi Support.|201|Rejected|No merchant token included in specification currently. Incorrect IDs are generally considered a client error (400) but in this case the server resource is created but payment rejected
1005|Merchant Not Authenticated|The merchant authentication code supplied is not correct or the authentication type has not been specified in the POLi  system.|Please check you have put the correct authentication code in for your merchant code. If you are certain you have, please contact POLi Support.|401|n/a|Authentication errors should not result in the creation of resource
1006|Deleted Merchant|The merchant code specified in the request corresponds to a merchant that has been deleted.|The merchant code you are using has been deleted. If you think this is an error, please contact POLi Support.|201|Rejected|
1007|Invalid Currency Code|The specified currency code does not exist in the database.|The currency code you are attempting to use is invalid. Please note that the only acceptable options are AUD and NZD.|201|Rejected|
1008|Invalid Merchant Currency|The specified currency code does not correspond to an active currency for the specified merchant.|The merchant you are using is set up for a different currency code than the one you provided.|201|Rejected|
1009|Currency System Limit Exceeded|The payment amount in the specified currency has exceeded the system limit for that currency.|Inform the customer that POLi  applies transaction limits for security reasons and to try another payment limit. Do not specify the limit. If you would like to change your limit, please contact POLi Support.|201|Rejected|
1010|Currency VAR Limit Exceeded|The payment amount in the specified currency has exceeded the VAR limit for that currency.|Inform the customer that POLi  applies transaction limits for security reasons and to try another payment limit. Do not specify the limit. If you would like to change your limit, please contact POLi Support.|201|Rejected|
1011|Currency Merchant Single Transaction Limit Exceeded|The payment amount in the specified currency has exceeded the merchant s single transaction limit for that currency.|Inform the customer that POLi  applies transaction limits for security reasons and to try another payment limit. Do not specify the limit. If you would like to change your limit, please contact POLi Support.|201|Rejected|
1012|Currency Merchant Daily Cumulative Limit Exceeded|The payment amount in the specified currency has exceeded the merchant s daily cumulative limit for that currency.|Inform the customer that POLi  applies transaction limits for security reasons and to try another payment limit. Do not specify the limit. If you would like to change your limit, please contact POLi Support.|201|Rejected|
1013|Invalid Merchant Established Date Time|The difference between the specified merchant established time and the system time is more than 24 hours.|Check your date and time settings.|201|Rejected|
1014|Invalid URL Format|The format of the specified URL is invalid.|Please check the URL fields in your initiate data and ensure they are correct.|400|n/a|Client bad request
1015|Invalid timeout value|The specified timeout value is less than the system minimum timeout value.|Please check the Timeout field you provided in your initiate data.|400|n/a|
1016|The transaction has expired|The transaction being enquired upon has lapsed past the 15min enquiry window|Use the Merchant Console to attain the outcome of this transaction|400|Rejected|Only on POST /payment-submissions, once the POST /payment has expired
1017|Blocked User IP address|The IP address of the user is blocked, restricted or otherwise from a list of known suspect IP addresses|Do not try to initiate a transaction again. NOTE: Strongly recommend that the user not be allowed to complete the transaction using another payment option at the Merchant s discretion.|403|n/a|
1018|Invalid IP address format|The IP address is in an invalid format|Try again passing in the correct data|400|n/a|
1019|Invalid IP address|The IP address is invalid|Try again passing in the correct data|400|n/a|
1020|No merchant primary account|The merchant has not set up a primary account to be used|Set up a primary account for the merchant through the console and try initiating the transaction again after it has been successfully added.|201|Rejected|Invalid account
1021|Invalid Field Characters|The specified field contains invalid characters.|Make sure there are no special characters in invalid fields.|400|n/a|Client bad request
1022|Mandatory Field|No value is supplied for a mandatory field.|A field considered mandatory has no data in it.|400|n/a|Client bad request
1023|Invalid Field Length|The specified field has an invalid length.|The data you have specified for a field is either too long or too short. Please verify the requirements for the field and retry.|400|n/a|Client bad request
1024|Invalid Currency Amount In Field|The specified field contains invalid currency amount.|The amount format is incorrect. Please ensure that no fewer or greater than two decimal places are specified.|400|n/a|Client bad request
1025|Invalid Field Range|The value in the field is out of the allowable range.|Please check the values and make sure they are within acceptable ranges.|400|n/a|Client bad request
1026|Invalid Transaction Status|The transaction has not followed the anticipated transaction status path|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API Provider error
1027|Invalid Merchant Financial Institution|The Financial Institution Code passed in is not allowed for this merchant|This merchant does not allow the bank you attempted to select.|400|n/a|Client bad request
1028|Invalid Financial Institution Code|The Financial Institution Code passed in is not valid|POLi does not support the bank you attempted to select.|400|n/a|Client bad request
1029|Inactive Financial Institution|The Financial Institution Code passed in is not currently active|The financial institution you selected is currently inactive.|400|n/a|Client bad request
1030|Deleted Financial Institution|The Financial Institution Code passed in has been deleted|The financial institution you selected has been deleted.|400|n/a|Client bad request
1031|Invalid Financial Institution Vector|The vector for the passed in Financial Institution Code is not available or nonexistent|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|Client bad request
1032|Invalid Transaction Status Code|The Transaction Status Code passed in is not valid|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API Provider error
1033|Invalid Transaction Status Codes|The Transaction Status Codes passed in are not valid|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API Provider error
1034|Invalid entity code|The merchant code is not valid.|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API Provider error - no merchant code
1035|Inactive entity code|The entity code is not active.|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API Provider error
1036|Deleted entity code|The entity code has been deleted.|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support..|500|n/a|API Provider error
1037|Either the TransactionRefNo or the MerchantReference must be supplied|The TransactionRefNo or MerchantReference passed in incorrect.|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API Provider error
2001|Invalid Token|The token is invalid.|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API Provider error
2002|Expired Token|The current token has timed out.|The transaction has expired. Please retry your payment.|201|Rejected|Submission not received within payment timeout value
2003|Illegal Token|The token is illegal.|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API Provider error
2004|Invalid merchant code|The merchant code passed is invalid|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API Provider error
2005|Inactive merchant code|The merchant code passed is marked as inactive|The merchant code has been set to inactive. Please contact POLi Support if you think this is an error.|201|Rejected|
2006|Deleted merchant code|The merchant code passed has been deleted|The merchant code passed has been deleted. Please contact POLi Support if you think this is an error.|202|Rejected|
2007|Invalid transaction status|The transaction status was not as expected.|This may point to a problem with the bank site. Contact POLi support for more details.|500|n/a|API Provider error
2008|Invalid payment amount|The payment amount was invalid|The amount paid for the transaction was not what was expected. Contact POLi support for more details.|?|?|Unclear if this situation can occur with the APIs planned
2009|Invalid merchant name|The merchant name is invalid|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API Provider error
2010|Invalid merchant sort code format|The sort code format is invalid|The BSB or sort code was not what was expected. Contact POLi support for more details.|201|Rejected|Syntax of request was correct but data validation failed
2011|Invalid merchant account number format|The merchant account number format is invalid|The account number was not what was expected. Contact POLi support for more details.|201|Rejected|Syntax of request was correct but data validation failed
2012|Invalid language code|The language code is invalid|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API Provider error
2013|Invalid Financial Institution code|The financial institution code is invalid|The FI code attempted was not found. Contact POLi support for more details.|201|Rejected|Syntax of request was correct but data validation failed
2014|Invalid merchant financial institution|The merchant financial institution is invalid|The FI code was not valid. Contact POLi support for more details.|201|Rejected|Syntax of request was correct but data validation failed
2015|Inactive financial institution|The financial institution passed is inactive|The FI code attempted was unavailable. Contact POLi support for more details.|201|Rejected|Syntax of request was correct but data validation failed on FI data
2016|Non-existent financial institution vector|The vector for the given financial institution does not exist|The FI code attempted was unavailable. Contact POLi support for more details.|201|Rejected|Syntax of request was correct but data validation failed
2017|Invalid transaction reference format|The transaction reference format was unexpected|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|400|n/a|Bad request format, client error
2018|Invalid Eula language|The Eula language was invalid|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
2019|Deleted Financial Institution|The financial institution was deleted|The FI code attempted was unavailable. Contact POLi support for more details.|201|Rejected|Syntax of request was correct but data validation failed on FI data
2020|Invalid transaction ref no|The transaction ref no is invalid|The transaction reference number was invalid. Contact POLi support for more details.|400|n/a|Client bad request
2021|Invalid Payment Amount|The amount being paid is not equal to the transaction amount|The user s locale can affect the way that currency amounts are displayed on the screen. For example, a German locale may display the amount with a comma rather than a decimal point. This affects the way that POLi  interprets the amount and may result in a failure. Setting the locale to En-AU, En-US, or En-GB will rectify this issue.|201|Rejected|Syntax of request was correct but data validation failed on payment amount
2022|Invalid merchant sort code|The merchant sort code does not equal the {4} sort code.|The sort code or BSB was invalid. Contact POLi support for more details.|201|Rejected|Syntax of request was correct but data validation failed on account validation
2023|Invalid merchant account number|The merchant account number does not equal the {4} account number|The account number was invalid. Contact POLi support for more details.|201|Rejected|Syntax of request was correct but data validation failed on account validation
2024|Invalid URL format|Invalid URL format|The URL format for one of the URL fields provided in the initiate data was invalid. Check your web services.|404|n/a|
2025|Invalid step sequence|Invalid step sequence|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|n/a|n/a|
2026|Invalid step type code|Invalid step type code|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|n/a|n/a|
2027|Invalid POLi ID|Invalid POLi ID|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|n/a|n/a|
2028|Invalid Certificate|Invalid Certificate|The certificate supplied by the bank is invalid. Please note this error only applies to the downloadable POLi Browser client|401|n/a|MA-SSL handshake failed
2029|No start up details found|No start up details found|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|n/a|n/a|
2030|No time remaining found|No time remaining found|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|n/a|n/a|
2031|Invalid IP address format|The IP address format was incorrect|Please ensure the IP address has been passed in correctly.|400|n/a|Client bad request
2032|Invalid IP address|The IP address has changed during the payment|Retry the transaction. This may be caused by a momentary loss of connection, such as a mobile device changing from WiFi to 3G or 4G.|401|n/a|Client bad request
2033|Invalid vector signature|Invalid vector signature|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|n/a|n/a|
2034|Invalid merchant suffix format|Invalid merchant suffix format|The suffix provided was in an incorrect format. Contact POLi support for more details.|201|Rejected|
2035|The merchant account suffix does not equal the account suffix|The bank account suffix being paid to does not equal to the account suffix set up by the merchant.|NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|201|Rejected|
2036|Unsupported wininet version|POLi was unable to access the bank security information|The certificate supplied by the bank cannot be accessed. Please note this error only applies to the downloadable POLi Browser client|n/a|n/a|
2037|Missing transaction data signing certificate||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|n/a|n/a|Signatures not used currently
2038|Error signing transaction data||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|n/a|n/a|Signatures not used currently
2039|Second POLi Browser detected|POLi has detected 'POLiBrowser.exe' is already running|Reboot your computer to fix this issue|n/a|n/a|Not applicable
2040|Invalid payee reference|The Payee Reference was different to the one expected.|This can be caused by a customer changing the reference, or an error. Contact POLi support for more details.|201|Rejected|
2041|Invalid date time|The date and time POLi collected from the bank page is not what was expected|Retry your transaction, or contact POLi support for more details.|201|Rejected|
3001|Invalid token||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|403|n/a|Token errors result in 403
3002|Expired token||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|403|n/a|Token errors result in 403
3003|Illegal Token||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|403|n/a|Token errors result in 403
3004|Invalid merchant|The merchant code was invalid.|Contact POLi support for more details.|201|Rejected|Syntax of request was correct but data validation failed - duplicate of 2022?
3005|Inactive merchant|The merchant code has been set to Inactive.|Contact POLi support for more details.|201|Rejected|Syntax of request was correct but data validation failed
3006|Deleted merchant|The merchant code has been deleted.|Contact POLi support for more details.|201|Rejected|Syntax of request was correct but data validation failed
3007|Invalid transaction status||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3008|Invalid financial institution code||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3009|Invalid merchant financial institution||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3010|Inactive financial institution||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3011|Invalid financial instituiton vector||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3012|Invalid language code||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3013|Invalid country code||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3014|Invalid currency code||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3015|Invalid eula language||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3016|Deleted financial institution||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3017|Invalid merchant terms and conditions language||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3018|Invalid URL format||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3019|Invalid step type code||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3020|Invalid eula version||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3021|Invalid FAQ language||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3022|The financial institution||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3023|The transaction has expired||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3024|No helper pane details found||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3025|No failure details found||NOTE: This error should never be returned to a Merchant. If it does then please inform POLi Support.|500|n/a|API provider error
3026|User time out of sync|The user's local time and timezone is out of sync with the POLi Server.|Please check that the time, date and time zone are correct on your computer|n/a|n/a|
3027|Invalid IP Address format|||400|n/a|Client bad request - duplicate?
3028|Invalid IP Address|The IP address has changed during the payment|Retry the transaction. This may be caused by a momentary loss of connection|400|n/a|Client bad request
3029|No merchant checkout URL|The IP address has changed during the payment|Retry the transaction. This may be caused by a momentary loss of connection|n/a|n/a|
3030|The transaction was terminated in an unknown state|The transaction passed its timeout value while in Unknown status|This may point to a problem with the customer's bank. Please contact POLi Support for more details.|201|Rejected|
3031|Payment URL for merchant has expired|Payment url has expired|There may be an error in the implementation of POLi . Check your web services.|n/a|n/a|
3033|Payment URL is not valid|Payment URL is invalid|An invalid POLiLinkURL has been attempted|n/a|n/a|
3034|Failed to update customer id||Non Fatal Error|n/a|n/a|
3035|Failed to update transactions top up indicator||Non Fatal Error|n/a|n/a|
4001|Unknown Error|An internal error has occurred|Please retry your POLi payment. If the error persists, please contact the merchant, quoting the POLi ID.|500|n/a|
4005|Null Result Error|Bank page is not recognised by POLi.|POLi has encountered an unexpected bank page. This may be due to a new or changed bank page. Our support team has been advised of the error and will address this error as soon as possible.|n/a|n/a|
4007|Invalid Transaction Status|The transaction status was not as expected.|This may point to a problem with the bank site. Contact POLi support for more details.|201|Rejected|
4030|ThreatMetrix rejected this user||An error has occurred. Please contact the merchant for more information.|n/a|n/a|
4031|Payment Data Not Matched|The payment information is missing or has changed|This may point to a problem with the bank site. Contact POLi support for more details.|201|Rejected|
4032|Unhandled Exception|An exception has occurred during payment|This may point to a problem with the bank site. Contact POLi support for more details.|500|n/a|
4033|Unhandled HttpRequestException|An HTTP Request exception has occurred during payment|This may point to a problem with the bank site. Contact POLi support for more details.|501|n/a|
4034|JavaScript execution failed|An error has occurred executing Javascript during the transaction.|This may point to a problem with the bank site. Contact POLi Support for more details.|n/a|n/a|
4040|Blacklisted Account||Please contact POLi Support for further information.|201|Rejected|
4050|Merchant account details Invalid|The merchant's account is not valid|Please contact POLi Support for more information or update your merchant bank account.|201|Rejected|
4051|Internet banking locked||Your internet banking service is locked. Please contact your bank for further details.|n/a|n/a|
4052|Third-party payments not supported||Your account does not support third-party payments. Please contact your bank for further details.|201|Rejected|
5001|An unknown error as occured||Please contact POLi Support for more details.|500|n/a|Unknown API provider error
5002|No transaction token||Please contact POLi Support for more details.|403|n/a|No token supplied
5003|Client certificate error||Please contact POLi Support for more details.|401|n/a|MA-SSL handshake failed
5004|Unable to execute|The transaction cannot proceed as it is not in an active state|Retry the payment. Contact your merchant if the issue persists.|201|Rejected|
5005|Unexpected page|POLi has encountered an unexpected bank page.|This may be due to a new or changed bank page. Our support team has been advised of the error and will address this error as soon as possible.|n/a|n/a|
5006|Null reference error|POLi has encountered an error with a bank page.|This may be due to an changed bank page, your payment timing out, or as a result of multiple errors.|n/a|n/a|
5007|Null or empty string|POLi has encountered an error with a bank page or field.|This may be due to an changed bank page, your payment timing out, or as a result of multiple errors.|n/a|n/a|
5008|IE Invalid SSL Error||POLi is unable to verify the bank's security certificate.|401|n/a|MA-SSL handshake failed
5009|Host invalid SSL error||POLi is unable to verify the bank's security certificate. The bank may have recently updated their security certificate, or the bank website is currently unavailable.|401|n/a|MA-SSL handshake failed
5010|Disabled JavaScript|JavaScript may not be enabled or is blocked by a browser extension.|Please enable javascript in your browser and try again|n/a|n/a|
5011|Unable to obtain vector|POLi cannot proceed due to an error.|An internal error has occurred.|n/a|n/a|
5012|Vector error||An internal error has occurred.|n/a|n/a|
5013|Vector processing error||An internal error has occurred.|n/a|n/a|
5014|Communication error||There is an error communicating with POLi server. This may be caused by a connection dropout or firewall settings on your computer.|n/a|n/a|
5015|Host error||The POLi server has returned an error when processing the transaction. Please retry your POLi payment.|500|n/a|API provider error
5016|Action Initiated Abort||An internal error has occurred.|n/a|n/a|
5017|Vector XML Reader error||An internal error has occurred.|n/a|n/a|
5018|Vector XML Deserialise Error||POLi is unable to continue due to an internal error.|n/a|n/a|
5019|Unsupported Wininet version|Error no longer in use.|An internal error has occurred.|n/a|n/a|
5020|Vector Signature Mismatch||An internal error has occurred.|n/a|n/a|
5021|Page Data Mismatch|Non-fatal error|Logging information only.|n/a|n/a|
5022|Null Vector||An internal error has occurred.|n/a|n/a|
5023|No financial institution details in vector||An internal error has occurred.|n/a|n/a|
5024|No financial institution start URL in vector||An internal error has occurred.|n/a|n/a|
5025|No trace dump filter in vector||An internal error has occurred.|n/a|n/a|
5026|Invalid Wininet offset|POLi is unable to verify the bank's security certificate. The bank website may be currently unavailable.|Retry payment at a later time.|n/a|n/a|
5027|Platform not supported||Your computer does not support the POLi Payment System.|n/a|n/a|
5028|Potential Westpac session corruption||An internal error has occurred.|n/a|n/a|
5029|User is attempting to pay an unsaved payee using a mobile vector which does not support unsaved payees|The user's bank requires a saved payee.|Retry the payment using a desktop operating system.|n/a|n/a|
5030|ThreatMetrix rejected this user||An error has occurred. Please contact the merchant for more information.|n/a|n/a|
5040|Blacklisted Account||An error has occurred. Please contact the merchant for further information.|201|Rejected|Syntax of request was correct but data validation failed - duplicate of 2011?
8001|Operational Error Without Trace Information|An operational error occurs but there is no trace information available.|Perform the web service again.|500|n/a|API provider error
8002|Operational Error With Trace Information|An operational error occurs and trace information is available.|Perform the web service again.|500|n/a|API provider error
8003|Invalid Field Characters|The specified field contains invalid characters.|There may be an error in the implementation of POLi . Check your web services.|201|Rejected|Syntax of request was correct but data validation failed
8004|Mandatory Field|No value is supplied for a mandatory field.|There may be an error in the implementation of POLi . Check your web services.|201|Rejected|Syntax of request was correct but data validation failed
8005|Invalid Field Length|The specified field has an invalid length.|There may be an error in the implementation of POLi . Check your web services.|201|Rejected|Syntax of request was correct but data validation failed
8006|Invalid Currency Amount In Field|The specified field contains invalid currency amount.|There may be an error in the implementation of POLi . Check your web services.|201|Rejected|Syntax of request was correct but data validation failed
8007|Invalid Field Range|The value in the field is out of the allowable range.|There may be an error in the implementation of POLi . Check your web services. Please contact POLi Support if the issue persists.|201|Rejected|Syntax of request was correct but data validation failed
11002|Unable to Send Nudge|A nudge sent to the merchant has failed.|The Nudge Notification URL may be publicly inaccessible, misconfigured, or the destination is down or taking too long to respond.|n/a|n/a|
12001|Merchant Code Required|Merchant code is empty|Provide a non-empty merchant code|201|Rejected|Syntax of request was correct but data validation failed
12002|Merchant Code Length|Merchant code length exceeds maximum number of characters allowed.|Provide a Merchant Code with valid length.|201|Rejected|Syntax of request was correct but data validation failed
12003 or 1003|Invalid Merchant Code|Merchant Code doesn t exist or inactive|Provide a valid Merchant Code.|201|Rejected|Syntax of request was correct but data validation failed
12004|Authentication Code Required|Authentication Code is empty|Provide a non-empty Authentication Code|401|n/a|
12005|Authentication Code Length|Authentication Code length exceeds maximum number of characters allowed.|Provide an Authentication Code with valid length.|401|n/a|
12006 or 1005|Invalid Authentication Code|Authentication Code is not valid|Provide a valid Authentication Code.|401|n/a|
12010|Request Type is Required|Request Type is empty|Provide a non-empty Request Type|400|n/a|Client bad request format
12011|Request Type Length|Request Type length exceeds maximum number of characters allowed.|Provide a Request Type with a valid length|400|n/a|Client bad request format
12012|Invalid Request Type|Request Type is not valid. It must be either Manual or Email.|Provide a Request Type set to either Manual or Email.|400|n/a|Client bad request format
12013|Invalid Payment Amount|Payment Amount is empty. Either it is empty or not a decimal number or decimal precision exceeds 2 or not in the range of 1.00 and 10000.00|Provide a valid Payment Amount.|201|Rejected|
12014|Payment Reference is required|Payment reference is empty.|Provide a non-empty Payment Reference.|201|Rejected|
12015|Invalid Payment Reference|Payment reference is not valid.||201|Rejected|
12016|Invalid Confirmation Email|Confirmation Email is either empty or not one of the values Yes, No|Provide Confirmation Email with a valid value.|n/a|n/a|Email format not included
12017|Invalid Customer Reference|Customer Reference is either empty or not one of the values Yes, No|Provide Customer Reference with a valid value.|201|Rejected|
12018|Recipient Name is required|Recipient Name is empty.|Provide a non-empty Recipient Name.|201|Rejected|
12019|Recipient Name Length|Recipient Name length exceeds maximum number of characters allowed|Provide a Recipient Name with a valid length.|201|Rejected|
12020|Invalid Recipient Name|Recipient Name is not valid.|Check invalid characters in the Recipient Name.|201|Rejected|
12021|Recipient Email is required|Recipient Email is empty.|Provide a non-empty recipient email.|n/a|n/a|Email format not included
12022|Recipient Email Length|Recipient Email length exceeds maximum number of characters allowed.|Provide a Recipient Email with a valid length.|n/a|n/a|Email format not included
12023|Invalid Recipient Email|Recipient Email is not valid.|Check Recipient Email follows email address rules.|n/a|n/a|Email format not included
12024|Email Delivery Failed|Email Delivery to Payer failed.|Check payer email address is valid and active.|n/a|n/a|Email format not included
14050|A transaction-specific error has occurred|System Error|Contact POLi support.|500|n/a|API provider error
14053|The amount specified exceeds the individual transaction limit set by the merchant|Validation issue|Either raise the limit or lower the amount for the transaction.|201|Rejected|
14054|The amount specified will cause the daily transaction limit to be exceeded|Validation issue|Either raise the limit or lower the amount for the transaction.|201|Rejected|
14055|General failure to initiate a transaction|System Error|Contact POLi support.|?|?|
14056|Error in merchant-defined data|Validation issue|Check the length of the MerchantData field.|201|Rejected|
14057|One or more values specified have failed a validation check|Validation issue|Check that you have met the validation rules.|201|Rejected|
14058|The monetary amount specified is invalid|Validation issue|Check that the amount is in the correct format.|201|Rejected|
14059|A URL provided for one or more fields was not formatted correctly|Validation issue|Check the URL formats.|n/a|n/a|
14060|The currency code supplied is not supported by POLi or the specific merchant|Validation issue|Please provide only AUD or NZD.|201|Rejected|
14061|The MerchantReference field contains invalid characters|Validation issue|MercantReference can only contain alphanumeric and these symbols:`@-_=:?./`|201|Rejected|
14062|One or more fields that are mandatory did not have values specified|Validation issue|Please provide all mandatory fields.|201|Rejected|
14099|An unexpected error has occurred within transaction functionality|System Error|Contact POLi support.|500|n/a|API provider error
