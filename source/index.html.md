---
title: LossExpress xPayoff API

includes:
  - lenders
  - payoff_request

search: true

code_clipboard: true
---

# Introduction

Welcome to the LossExpress xPayoff API! You can utilize this API to get pertinent information regarding a consumer's vehicle loan.

# Authentication

Our xPayoff API utilizes a simple API key-based authentication, where the key (generated from our developer interface) must be passed in the Authorization header like so:

`Authorization: Bearer exampletokenbutreplacewithyourown`

# Receiving Information (Webhooks)

We send any payoff information we receive for any requests you make as a POST call to any HTTPS endpoint you specify within our system. This section details what you can expect with webhooks within LossExpress xPayoff API.

Please note that you'll be able to manage your webhooks within the developer interface, and that you _must_ have a webhook enabled in order to make payoff requests.

## General Information

Whenever we receive payoff information (almost always within seconds of an initial request), we send a POST request to a URL that has been designated by you within our system.

Your URL must be publicly accessible (or at least accessible by our servers), and must accept the request, responding with a 200 HTTP status code.

We will deactivate any webhooks (as well as corresponding payoff requests) for any account that sends non-200 HTTP status codes in response to our requests to the URL five times in a row, or ten times in a 15 minute period of time.

## Signed Request

Every request to a webhook will be signed by LossExpress, with the request containing a `X-LossExpress-Signature` header that will contain an SHA256 HMAC that is generated using the following string:

`STRINGIFIED_REQUEST_BODY:PAYOFF_ID`

Where STRINGIFIED_REQUEST is the JSON of the request body (with no padding/whitespace) and PAYOFF_ID is the payoff ID for the particular request. This signature is signed with your API key and can be used to validate that the request came from LossExpress.

## Webhook Body

> Example Body:

```json
{
  "payoffId": "f94bbd89-e9e0-45ac-a02b-e4d4afad6a8f",
  "payoffAmount": 10222.33,
  "perDiem": 2.33,
  "validThroughDate": "2020-08-22T00:00:00.000Z",
  "lenderPhoneNumber": "+12223334444",
  "standardMailingAddress": {
    "streetAddress": "1000 Main Street",
    "streetAddress2": "Suite 900",
    "city": "Dallas",
    "state": "TX",
    "zipCode": "75204"
  }
  "success": true,
  "fulfilledBy": "integration"
}
```

The body of the request sent to the webhook will be a JSON object with the following possible keys:

Key  | Description | Example
---- | ----------- | -------
payoffId | The LossExpress UUID | `f94bbd89-e9e0-45ac-a02b-e4d4afad6a8f`
payoffAmount | A number containing the amount remaining on the loan | `10222.33`
perDiem | The per diem for the particular loan | `2.33`
validThroughDate | A JSON timestamp for the valid through date | `2020-08-22T00:00:00.000Z`
lenderPhoneNumber | A valid phone number for the lender | `+12223334444`
standardMailingAddress | Contains a standard mailing address for the lender |
overnightMailingAddress | Contains an overnight mailing address for the lender |
accountNumber | Contains the account number for the account as provided by the lender |
eftAccountNumber | An account number for EFT |
eftRoutingNumber | A routing number for EFT |
eftState | The state, as it relates to EFT | `TX`
eftCity | The city, as it relates to EFT | `DALLAS`
eftZipCode | The ZIP code, as it relates to EFT | `75204`
success | A boolean, stating whether we were able to retrieve the payoff or not | `true`
errorMessage | If success is false, we will attempt to provide an error message
fulfilledBy | Can be `"integration"` or `"fulfillment center"`, depending on how the payoff was fulfilled | `integration`

<aside class="notice">
  Only `payoffId` and `success` are guaranteed to be in any given webhook body sent.
</aside>