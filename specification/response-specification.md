---
description: Specification for redirection response from the AA back to FIU
---

# Response Specification

## URL Parameters

Following URL parameters would need to be accepted on the requestor side.

> All URL query parameters below are **required**.

| Parameter Name | Type   | Description                                                                                   |
| -------------- | ------ | --------------------------------------------------------------------------------------------- |
| fi             | String | Unique AA identifier. This will be encrypted using Base64/XOR along with resdate field        |
| resdate        | String | format will be `ddmmyyyyhh24misss` UTC                                                        |
| ecres          | String | Encrypted parameters ([see below](response-specification.md#ecres-encrypted-path-parameters)) |

## ecres (encrypted path parameters)

Below are the parameters that will be encrypted using AES256 encryption algorithm

> The parameters must be in application/x-www-form-urlencoded format

| **Parameter name** | **Parameter type** | **Parameter description**                                                                                    |
| ------------------ | ------------------ | ------------------------------------------------------------------------------------------------------------ |
| txnid              | String             | UUID txnid ( To be sent back from the request )                                                              |
| sessionid          | String             | Value of sessionid received in the ‘ecreq’ field in the request.                                             |
| userid             | String             | The AA user id                                                                                               |
| status             | String             | The status ‘S’ for success ( atleast one consent is approved ) and ‘F’ for failure ( all consents declined ) |
| errorcode          | String             | The response code : 0 if status is ‘S’ and others for failure (Refer to Error Codes table below)             |
| srcref             | Array              | Array of accepted consent handle identifiers only in case of LSP                                             |

## Error codes

The following errorcodes are returned by the AA to FIU when the user is redirected back to the FIU application.

| **Error code** | **Message**                | **Status Parameter** | **Mandatory** | **When is this returned**                     |
| -------------- | -------------------------- | -------------------- | ------------- | --------------------------------------------- |
| 0              | Success                    | S                    | Y             | When the user has accepted the consent        |
| 1              | Consent is rejected        | F                    | Y             | When the user rejects the consent             |
| 2              | Consent not available      | F                    | Y             | Consent request not found with the AA         |
| 3              | Invalid request            | F                    | Y             | The redirection request has invalid data      |
| 4              | User authentication failed | F                    | N             | User is not able to authenticate self         |
| 6              | Session timeout            | F                    | N             | On login page, user doesnt act |
| 7              | Account not discovered     | F                    | N             | FIP Id is passed but the accounts were not discovered in the FIP (error 404) and user cancels and goes back from AA page |
| 8              | Account not discovered     | F                    | N             | FIP Id is passed but the accounts were not discovered due to error in FIP (other than 404) and user cancels and goes back from AA page |
| 9              | Account not discovered     | F                    | N             | Multiple FIP Id were passed or FIP Id was not passed and no account discovered for the user in any FIP (error 404 or any other error) |
| 10             | Account not linked         | F                    | N             | Accounts discovered and user does nothing (did not click to link accounts) and session timed-out |
| 11             | Account not linked         | F                    | N             | Accounts discovered (did not click to link accounts) and user clicks cancelled |
| 12             | Account link OTP not attempted | F                    | N             | Accounts discovered, user clicked on link CTA, but OTP not entered; and user cancels |
| 13             | Account link OTP not attempted | F                    | N             | Accounts discovered, user clicked on link CTA, but OTP not entered; and user does nothing (timeout) |
| 14             | Account link OTP authentication failed   | F                    | N             | All accounts link failed due to wrong OTP (wrong OTPs entered in single or multiple attempts) and finally cancels to go back to FIU |
| 15             | Account link OTP authentication failed   | F                    | N             | All accounts link failed due to wrong OTP (single or multiple attempts) and does nothing (timeout) |
| 16             | Account link failed   | F                    | N             | User enters valid OTP but all account link fails at FIP with some error |
| 17             | Account link OTP authentication failed for some accounts   | F                    | N             | In case multiple FIP account linking, not all accounts linked successfully due to wrong OTP and user ends the AA journey by clicking cancel. |
| 18             | Account link OTP authentication failed for some accounts   | F                    | N             | In case multiple FIP account linking, not all accounts linked successfully due to wrong OTP and user does nothing (timeout) |
| 19             | Account link failed for some accounts   | F                    | N             | In case multiple FIP account linking, not all accounts linked successfully and user ends the AA journey |
| 20             | Unknown error   | F                    | N             | In case AA is not able to send any valid error code |
|21| Specific Account not present|F|N|Accounts value is passed but the accounts were not present and user cancels and goes back from AA page|
|22|Specific Account not present|F|N|Accounts value is passed but the accounts were not present and user stays idle and session times out|
|23|Accounts present but session timeout|F|N|Accounts value is passed and the account is present. But customer does not do anything and drops off in the consent screen|
