---
title: LossExpress xPayoff API

includes:
  - lenders
  - payoff_request
  - errors

search: true

code_clipboard: true
---

# Introduction

Welcome to the LossExpress xPayoff API! You can utilize this API to get pertinent information regarding a consumer's vehicle loan.

# Testing

Depending on the configuration of your instance, please use one of the following as your base URL:

Production URL | Development URL                   | 
----------|---------------------------------|
`https://xpayoff-api.lossexpress.com/` | `https://xpayoff-api-dev.lossexpress.com/` 

The following Test VINs can be used on both instances when making a payoff request. 
See [Loan Payoff Request](#loan-payoff-request) and [Webhook Body](##webhook-body) for more details.

Test VIN | Outcome                                          | 
----------|--------------------------------------------|
`1FTMF1CB6JKE12626` | Automated Payoff Quote 
`1N4AA5AP8EC474073` | Automated Payoff Quote 
`5FNYF4H46CB077722` | Automated Payoff Quote 
`3LNHL2JC5CR800827` | Automated Payoff Quote 
`ZA9RU31B9XLA12448` | Automated Payoff Quote 

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
  "packetId": "f94bbd89-e9e0-45ac-a02b-e4d4afad6a8f",
  "payoffId": "f94bbd89-e9e0-45ac-a02b-e4d4afad6a8f",
  "orderId": "61951010-d4a0-48b3-86b6-3908da2b0800",
  "orderType": "Payoff Request",
  "payoffAmount": 10222.33,
  "perDiem": 2.33,
  "validThroughDate": "2020-08-22T00:00:00.000Z",
  "lenderPhoneNumber": "+12223334444",
  "checkPayableTo": "Jane Doe",
  "standardMailingAddress": {
    "attn": "John Doe",
    "streetAddress": "1000 Main Street",
    "streetAddress2": "Suite 900",
    "city": "Dallas",
    "state": "TX",
    "zipCode": "75204"
  },
  "success": true,
  "fulfilledBy": "integration",
  "customerAuthorizationAttempted": true
}
```

The body of the request sent to the webhook will be a JSON object with the following possible keys:

| Key                            | Description                                                                                                                                                | Example                                |
|--------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------|
| packetId                       | The LossExpress UUID                                                                                                                                       | `f94bbd89-e9e0-45ac-a02b-e4d4afad6a8f` |
| payoffId                       | The LossExpress UUID (Same as above - will be deprecated in future)                                                                                        | `f94bbd89-e9e0-45ac-a02b-e4d4afad6a8f` |
| orderId                        | The LossExpress UUID for the specific order                                                                                                                | `61951010-d4a0-48b3-86b6-3908da2b0800` |
| orderType                      | The type of order that has been requested                                                                                                                  | `Payoff Request`                             |
| payoffAmount                   | A number containing the amount remaining on the loan                                                                                                       | `10222.33`                             |
| perDiem                        | The per diem for the particular loan                                                                                                                       | `2.33`                                 |
| validThroughDate               | A JSON timestamp for the valid through date                                                                                                                | `2020-08-22T00:00:00.000Z`             |
| lenderPhoneNumber              | A valid phone number for the lender                                                                                                                        | `+12223334444`                         |
| checkPayableTo                 | To whom the check is payable to                                                                                                                            | `+12223334444`                         |
| standardMailingAddress         | Contains a standard mailing address for the lender                                                                                                         |
| overnightMailingAddress        | Contains an overnight mailing address for the lender                                                                                                       |
| accountNumber                  | Contains the account number for the account as provided by the lender                                                                                      |
| eftAccountNumber               | An account number for EFT                                                                                                                                  |
| eftRoutingNumber               | A routing number for EFT                                                                                                                                   |
| eftState                       | The state, as it relates to EFT                                                                                                                            | `TX`                                   |
| eftCity                        | The city, as it relates to EFT                                                                                                                             | `DALLAS`                               |
| eftZipCode                     | The ZIP code, as it relates to EFT                                                                                                                         | `75204`                                |
| success                        | A boolean, stating whether we were able to retrieve the payoff or not                                                                                      | `true`                                 |
| errorMessage                   | If success is false, we will attempt to provide an error message                                                                                           |
| fulfilledBy                    | Can be `"integration"` or `"fulfillment center"`, depending on how the payoff was fulfilled                                                                | `integration`                          |
| customerAuthorizationAttempted | Can be `true` or `false`, depending on whether verbal customer authorization was utilized to retrieve the payoff. Disabled at the company level by default. | `false`                                |
  
<aside class="notice">
  Only `payoffId` and `success` are guaranteed to be in any given webhook body sent.
</aside>

### Error Messages (Fulfillment Center)

Below are the current list of messages that could be returned when we run into an issue gathering a payoff via our fulfillment center:

- Lender would not release payoff information because:
  - Lender would not release information over phone
  - Need Full Member number
  - Need Full Social Security Number
  - Lender would not release to a third party
  - Request needs to be sent by fax 
- Loan has been paid off
- Loan could not be located with information given
- Customer authorization required
- Lender would not release payoff information
- Maximum call attempts reached
